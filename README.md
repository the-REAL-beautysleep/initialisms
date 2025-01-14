How to add more mnemonics

Pull down latest branch. 
Append to the words.txt file
Run a test locally   
Deploy to eb         `eb deploy`



- [x] set up on https
- [ ] There should be a test suite
- [x] words.txt should be far far larger
- [ ] refactor code to be shorter and easier to ready
- [ ] prepare this code base to be used as an off site calculations facilaty
- [ ] set up so that only mym website can be the origin
- [x] tidyResultsList should prevent duplicates in return
- [x] drop elements not the same length as the initials string in tidyList
- [x] order is not important option which loops over the regex in different orders
- [ ] consider ways to speed up code

- [ ] return [ "no results found, try a different phrase."]

# how to set up eb env for https
https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/configuring-https.html


`eb init` Sets default values for Elastic Beanstalk applications created with EB CLI by prompting you with a series of questions.
`eb create` Creates a new environment and deploys an application version to it.

to deploy to mym's aws profile use ` --profile beautysleep@mym` at the end of  the `eb` or `aws` command, otherwise susbuddys account will be used
















# initialisms

![Code card](https://raw.github.com/wiseman/initialisms/master/code-card.jpg "code card")

From http://ask.metafilter.com/255675/Decoding-cancer-addled-ramblings:

> In my grandmother's final days battling brain cancer, she became
> unable to speak and she filled dozens of index cards with random
> letters of the alphabet. I'm beginning to think that they are the
> first letters in the words of song lyrics, and would love to know
> what song this was. This is a crazy long shot, but I've seen Mefites
> pull off some pretty impressive code-breaking before!

This program guesses sentences from initial letters of each word using
the unreasonable effectiveness of data.  For example, if given the
right seed texts, it can decode the input
"OFWAIHHBTNTKCTWBDOEAIIIHFUTDODBAFUOT" into "our father who ascended
into heaven hallowed be thy name thy kingdom come thy will be done on
earth as it is in heaven for us this day our daily bread and forgive
us of the"

Inspired by http://norvig.com/ngrams/ch14.pdf.


## Getting ready to use it

It's probably easiest to use virtualenv:

```
$ virtualenv env
```

Then install nltk and python-gflags:

```
$ env/bin/pip install nltk python-gflags
```


## Example use

Start the program, feeding it some prayer texts:

```
$ env/bin/python decode.py apostles-creed.txt athanasian-creed.txt nicene-creed.txt order-of-morning.txt
```

Once it says "Enter initials:", type "OFWAIHHBTN" and press Enter.  It
will output something like this:

```
5.99453913576e-12 our father who ascended into heaven hallowed be thy name
```

This means that its best guess for "OFWAIHHBTN" is "our father who
ascended into heaven hallowed be thy name", with a probability of some
small number.

It is case-insenstive: OFWAIHHBTN is treated the same as "ofwaihhbtn".

You can use "$" to indicate the start of a sentence, for example
"$OFWAIHHBTN".


### Corpora

The program makes its guesses based on text you feed it.  I've
included 8 pieces of text, all in the `corpora` subdirectory:

|Filename                           |Description|
|-----------------------------------|-----------|
|apostles-creed.txt                 |[The Apostles Creed](https://www.ccel.org/creeds/apostles.creed.html)|
|athanasian-creed.txt               |[The Athanasian Creed](https://en.wikipedia.org/wiki/Athanasian_Creed)|
|bible-kjv.txt                      |[The King James Bible](https://en.wikipedia.org/wiki/King_James_Version)|
|hymnprayerbo00kunz_djvu.txt        |[<em>Hymn and prayer book: for the use of such Lutheran churches as use the English language</em> (1795)](https://archive.org/details/hymnprayerbo00kunz)|
|nicene-creed.txt                   |[The Nicene Creed](https://en.wikipedia.org/wiki/Nicene_Creed)|
|order-of-morning.txt               |[The Order of Morning Service](http://www.lutheran-hymnal.com/online/page5.html)
|prayerbookreligi00lasauoft_djvu.txt|[<em>Prayer-book for religious: a complete manual of prayers and devotions for the use of the members of all religious communities : a practical guide to the particular examen and to the methods of meditation</em> (1914, c1904)](https://archive.org/details/prayerbookreligi00lasauoft)|
|tlh.txt                            |[The Lutheran Hymnal](http://www.projectwittenberg.org/etext/hymnals/tlh/)|

To use corpora, supply them as arguments on the command line.  For example:

```
$ env/bin/python decode.py bible-kjv.txt nicene-creed.txt
```

If you want to use other texts, put them in the `corpora`
subdirectory.  Then you can specify their filenames on the command
line.

More and larger corpora slow the program down tremendously.  For
example, using just the King James Bible, trying to decode just three
letters, like "ofw", can take 10-20 seconds.  Trying to decode 4 or 5
or more can take minutes--or hours.


### Command line flags

The program uses Viterbi decoding and assumes a "noisy
channel"--meaning that it assumes there's a chance the letters you
give it as input are wrong.  By default it assumes there's a 0.1%
chance of an error.  If you want to change that, use the
`--error_prob` flag.  For example, this tells it there's a 50% chance
of an error per letter:

```
$ env/bin/python decode.py --error_prob=0.5 bible-kjv.txt
```

### Final notes

Of course there's no reason this code is limited to interpreting
religious codes.  It is limited only by its corpora (and its bigram
model, and its slowness, and...).
