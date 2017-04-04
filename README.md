# PyRATA
-------------

PyRATA is an acronym which stands both for "Python Rule-based feAture sTructure Analysis" and "Python Rule-bAsed Text Analysis". Indeed, PyRATA is not only dedicated to process textual data.

In short, PyRATA 
* provides regular expression matching operations over more complex structures than a list of characters (string), namely a sequence of features set (i.e. list of dict in python jargon).
* offers a similar API to the python re module,
* follows the Perl regexes de facto standard in terms of language syntax,
* is implemented in python 3,
* can be used for processing textual data but is not limited to (the only restriction is the respect of the data structure to explore)
* is released under the MIT Licence which is *a short and simple permissive license*
* fun and easy to use

## Description
-------------

Traditional regular expression (RE) engines handle character strings; In other words, lists of character tokens.
In Natural Language Processing, RE are used to define patterns which are used to recognized some phenomena in texts.
They are the essence of the rule-based approach for analysing the texts.

But character strings are a poor data structure. In the present work, we are dealing with lists of dict tokens. The dict python type is a data structure to represent a set of attribute name/value. Right now we only handle primitive types as allowed values.
The objective is to offer a language and an engine to define patterns aiming at matching (parts of) lists of featureset. 

In the most common use case, a featureset list is a data structure used to represent a sentence as sequence of words, each word token coming with a set of features. 
But it is not limited to the representation of sentences. It can also be used to represent a text, with the sentence as token unit. Each sentence with its own set of features.

### The API

The API is developed to be familiar for whom who develops with the python re module API. 

The module defines several known functions such as `search`, `findall`, or `finditer`. Some of the functions are also available for compiled regular expressions. The former take at least two arguments including the pattern to recognize and the data to explore (e.g. `re.search(pattern, data)`) while the latter take at least one, the data to explore (e.g. `compiledPattern.search(data)`).

More named arguments (`lexicons`, `verbosity`) allows to set lexicons which can be used to define set of accepted values for a specified feature or the level of verbosity.

A __pattern__ is made of one or several steps. A __step__ is, in its simplest form, the specification of a single constraint (*NAME OPERATOR VALUE*). For a given attribute name, you can specify its required exact value (with `=` *OPERATOR*), a regex definition of its value (`~` *OPERATOR*) or a list of possible values (`@` *OPERATOR*). A more complex step can be a __quantified step__ or a __class step__. The former allows to set *optional* step (`?`), steps which should occurs *at least one* (`+`), or *zero or more* (`*`). The latter aims at specifing more than one constraints and conditions on them with *parenthesis* (`()`) and logical connectors such as *and* (`&`), *or* (`|`) and *not* (`!`). A class step can be quantied.
The language offers the possibility to retrieve specific parts of a patterns by defining some *groups* with parenthesis (`()`) over the steps.

See the *Quick overview* section for more details and examples.

### References
  * https://docs.python.org/3/library/re.html
  * [nltk.RegexpParser](https://gist.github.com/alexbowe/879414) ; http://www.nltk.org/_modules/nltk/chunk/regexp.html#RegexpChunkParser ; http://nbviewer.jupyter.org/github/lukewrites/NP_chunking_with_nltk/blob/master/NP_chunking_with_the_NLTK.ipynb ; https://gist.github.com/alexbowe/879414
  * linguastream
  * pattern
  * ruta
  * xpath from me over graph of objects

### Limitations
* cannot handle overlapping annotations  

## Installation
-----------------

Right now pyrata is not published on PyPI, so the procedure to use it is the following:

### Download or clone pyrata
Download the latest PyRATA release
    
    wget https://github.com/nicolashernandez/PyRATA/archive/master.zip
    unzip master.zip -d .
    cd PyRATA-master/

or clone it 

    git clone https://github.com/nicolashernandez/PyRATA.git
    cd pyrata/

### Installation
Then install pyrata 

    sudo pip3 install . 

Of course, as any python module you can barely copy the pyrata sub dir in your project to make it available. This solution can be an alternative if you do not have root privileges or do not want to use a virtualenv.

### Requirement

PyRATA use the [PLY](http://www.dabeaz.com/ply/ply.html "PLY") implementation of lex and yacc parsing tools for Python (version 3.10).

You do not need to care about this stage if you performed the install procedure above.

If you do not properly install pyrata, you will have to manually install ply.

    sudo pip3 install ply

### Run tests (optional)

    python3 test_pyrata.py


## Quick overview (in console)
-----------------

First run python

    python3

Then import the main pyrata regular expression module:

    >>> import pyrata.re as pyrata_re

Let's say you have a sentence in the pyrata data structure format, __a list of dict__. A dict is a map i.e. a set of features, eachone with a name and value (in our case with primitive types).

    >>> data = [{'pos': 'PRP', 'raw': 'It'}, {'pos': 'VBZ', 'raw': 'is'}, {'pos': 'JJ', 'raw': 'fast'}, {'pos': 'JJ', 'raw': 'easy'}, {'pos': 'CC', 'raw': 'and'}, {'pos': 'JJ', 'raw': 'funny'}, {'pos': 'TO', 'raw': 'to'}, {'pos': 'VB', 'raw': 'write'}, {'pos': 'JJ', 'raw': 'regular'}, {'pos': 'NNS', 'raw': 'expressions'}, {'pos': 'IN', 'raw': 'with'},{'pos': 'NNP', 'raw': 'Pyrata'}]

There is __no requirement on the names of the features__.

By the way, you can also easily turn a sentence into the pyrata data structure, for example by doing:

    >>> import nltk
    >>> sentence = "It is fast easy and funny to write regular expressions with Pyrata"
    >>> data =  [{'raw':word, 'pos':pos} for (word, pos) in nltk.pos_tag(nltk.word_tokenize(sentence))]

In the previous code, you see that the names `raw` and `pos` have been arbitrary choosen to means respectively the surface form of a word and its part-of-speech.

At this point you can use the regular expression methods available to explore the data. Let's say you want to search the advectives. By chance there is a property which specifies the part of speech of tokens, *pos*, the value of *pos* which stands for adjectives is *JJ*.
To __search the first location__ where a given pattern (here `pos="JJ"`) produces a match:

    >>> pyrata_re.search('pos="JJ"', data)
    >>> <pyrata_re Match object; span=(2, 3), match="[{'pos': 'JJ', 'raw': 'fast'}]">

To get the __value of the match__:

    >>> pyrata_re.search('pos="JJ"', data).group()
    >>> [{'raw': 'fast', 'pos': 'JJ'}]
    
To get the __value of the start and the end__:

    >>> pyrata_re.search('pos="JJ"', data).start()
    >>> 2
    >>> pyrata_re.search('pos="JJ"', data).end()
    >>> 3

To __find all non-overlapping matches__ of pattern in data, as a list of datas:

    >>> pyrata_re.findall('pos="JJ"', data)
    >>> [[{'pos': 'JJ', 'raw': 'fast'}], [{'pos': 'JJ', 'raw': 'easy'}], [{'pos': 'JJ', 'raw': 'funny'}], [{'pos': 'JJ', 'raw': 'regular'}]]]

To __get an iterator yielding match objects__ over all non-overlapping matches for the RE pattern in data:

    >>> for m in pyrata_re.finditer('pos="JJ"', data): print (m)
    ... 
    <pyrata_re Match object; span=(2, 3), match="[{'pos': 'JJ', 'raw': 'fast'}]">
    <pyrata_re Match object; span=(3, 4), match="[{'pos': 'JJ', 'raw': 'easy'}]">
    <pyrata_re Match object; span=(5, 6), match="[{'pos': 'JJ', 'raw': 'funny'}]">
    <pyrata_re Match object; span=(8, 9), match="[{'pos': 'JJ', 'raw': 'regular'}]">

What about the expressivity of the pyrata grammar? A pattern is made of __steps__, eachone specifying the form of the element to match. 
You can search a __sequence of elements__, for example an adjective (tagged *JJ*) followed by a noun in plural form  (tagged *NNS*):

    >>> pyrata_re.search('pos="JJ" pos="NNS"', data).group()
    [{'pos': 'JJ', 'raw': 'regular'}, {'pos': 'NNS', 'raw': 'expressions'}]

You can specify a __class of elements__ by specifying constraints on the properties of the required element with logical operators like:

    >>> pyrata_re.findall('[(pos="NNS" | pos="NNP") & !raw="pattern"]', data)
    [[{'pos': 'NNS', 'raw': 'expressions'}], [{'pos': 'NNP', 'raw': 'Pyrata'}]]

You can quantify the repetition of a step: in other words specifying a __quantifier to match one or more times__ the same form of an element:

    >>> pyrata_re.findall('pos="JJ"+', data)
    [[{'raw': 'fast', 'pos': 'JJ'}, {'raw': 'easy', 'pos': 'JJ'}], [{'raw': 'funny', 'pos': 'JJ'}], [{'raw': 'regular', 'pos': 'JJ'}]

Or specifying a __quantifier to match zero or more times__ a certain form of an element:

    >>> pyrata_re.findall('pos="JJ"* [(pos="NNS" | pos="NNP")]', data)
    [[[{'raw': 'regular', 'pos': 'JJ'}, {'raw': 'expressions', 'pos': 'NNS'}], [{'raw': 'Pyrata', 'pos': 'NNP'}]]

Or specifying a __quantifier to match once or not at all__ the given form of an element:

    >>> pyrata_re.findall('pos="JJ"? [(pos="NNS" | pos="NNP")]', data)
    [[{'pos': 'JJ', 'raw': 'regular'}, {'pos': 'NNS', 'raw': 'expressions'}], [{'pos': 'NNP', 'raw': 'Pyrata'}]]

At the atomic level, there is not only the equal operator to set a constraint. You can also __set a regular expression as a value__. 
In that case, the operator will not be `=` but `~` 

    >>> pyrata_re.findall('pos~"NN."', data)
    [[{'raw': 'expressions', 'pos': 'NNS'}], [{'raw': 'Pyrata', 'pos': 'NNP'}]]

Consequently `[pos="NNS" | pos="NNP"]`, `pos~"NN[SP]"` and 'pos~"(NNS|NNP)"' are equivalent forms. They may not have the same processing time.

You can also __set a list of possible values (lexicon)__. In that case, the operator will be `@` in your pattern and the value will be the name of the lexicon. The lexicon is specified as a parameter of the pyrata_re methods (`lexicons` parameter). Indeed, multiple lexicons can be specified. The data structure for storing lexicons is a dict/map of lists. Each key of the dict is the name of a lexicon, and each corresponding value a list of elements making of the lexicon.

    >>> pyrata_re.findall('lem@"positiveLexicon"', data, lexicons = {'positiveLexicon':['easy', 'funny']})
    [[ {'pos': 'JJ', 'raw': 'easy'}], [{'pos': 'JJ', 'raw': 'funny'}]]

Currently no __wildcard character__ is implemented but you can easily simulate it with a non existing attribute or value:

    >>> pyrata_re.findall('pos~"VB." [!raw="to"]* raw="to"', data)
    [[{'raw': 'is', 'pos': 'VBZ'}, {'raw': 'fast', 'pos': 'JJ'}, {'raw': 'easy', 'pos': 'JJ'}, {'raw': 'and', 'pos': 'CC'}, {'raw': 'funny', 'pos': 'JJ'}, {'raw': 'to', 'pos': 'TO'}]]

To __understand the process of a pyrata_re method__, specify a __verbosity degree__ to it (*0 None, 1 +Parsing Warning and Error, 2 +syntactic and semantic parsing logs, 3 +More parsing informations*):

Here some syntactic problems: 

    >>> pyrata_re.findall('*pos="JJ" [(pos="NNS" | pos="NNP")]', data, verbosity=1)
    Error: syntactic parsing error - unexpected token type="ANY" with value="*" at position 1. Search an error before this point.

    >>> pyrata_re.findall('pos="JJ"* bla bla [(pos="NNS" | pos="NNP")]', data, verbosity=1)
    Error: syntactic parsing error - unexpected token type="NAME" with value="bla" at position 17. Search an error before this point.

Working with __chunks in IOB tagged format__. As mentioned in [nltk book](http://www.nltk.org/book/ch07.html), _The most widespread file representation of chunks uses IOB tags. In this scheme, each token is tagged with one of three special chunk tags, I (inside), O (outside), or B (begin). A token is tagged as B if it marks the beginning of a chunk. Subsequent tokens within the chunk are tagged I. All other tokens are tagged O. The B and I tags are suffixed with the chunk type, e.g. B-NP, I-NP. Of course, it is not necessary to specify a chunk type for tokens that appear outside a chunk, so these are just labeled O. An example of this scheme is shown below_  

    >>> data = [{'pos': 'NNP', 'chunk': 'B-PERSON', 'raw': 'Mark'}, {'pos': 'NNP', 'chunk': 'I-PERSON', 'raw': 'Zuckerberg'}, {'pos': 'VBZ', 'chunk': 'O', 'raw': 'is'}, {'pos': 'VBG', 'chunk': 'O', 'raw': 'working'}, {'pos': 'IN', 'chunk': 'O', 'raw': 'at'}, {'pos': 'NNP', 'chunk': 'B-ORGANIZATION', 'raw': 'Facebook'}, {'pos': 'NNP', 'chunk': 'I-ORGANIZATION', 'raw': 'Corp'}, {'pos': '.', 'chunk': 'O', 'raw': '.'}] 

    chunk."PERSON" [pos~"VB"]* FIXME
    pos="IN" chunk."ORGANIZATION" FIXME

    Before introducing the chunk operator: introduce the annotate methods

What can do the annotate method:
- each feature set of the matched sequences are updated with a given feature set
- each feature set of the matched sequences are updated with a given feature set ; some of them should follow the iob scheme.
- by default group 0 is updated or the given groups of the matched squences

    annotation = {'chunk':'PERSON'}
    new_data = annotate (pattern, data, annotation, iob=['chunk'], groups = ['1'])


### Compiled regular expression

__Compiled regular expression objects__ support the following methods `search`, `findall` and `finditer`. It follows the same API as [Python re](https://docs.python.org/3/library/re.html#re.regex.search) but uses a sequence of features set instead of a string.

Below an example of use for `findall`

    >>> data = [{'pos': 'PRP', 'raw': 'It'}, {'pos': 'VBZ', 'raw': 'is'}, {'pos': 'JJ', 'raw': 'fast'}, {'pos': 'JJ', 'raw': 'easy'}, {'pos': 'CC', 'raw': 'and'}, {'pos': 'JJ', 'raw': 'funny'}, {'pos': 'TO', 'raw': 'to'}, {'pos': 'VB', 'raw': 'write'}, {'pos': 'JJ', 'raw': 'regular'}, {'pos': 'NNS', 'raw': 'expressions'}, {'pos': 'IN', 'raw': 'with'},{'pos': 'NNP', 'raw': 'Pyrata'}]
    >>> compiled_re = pyrata_re.compile('pos~"JJ"* pos~"NN."')
    >>> compiled_re.findall(data)
    [[{'raw': 'regular', 'pos': 'JJ'}, {'raw': 'expressions', 'pos': 'NNS'}], [{'raw': 'Pyrata', 'pos': 'NNP'}]]


### Groups

In order to __retrieve the contents a specific part of a match, groups can be defined with parenthesis__ which indicate the start and end of a group.

    >>> import pyrata.re as pyrata_re
    >>> pyrata_re.search('raw="is" (!raw="to"+) raw="to"', [{'pos': 'PRP', 'raw': 'It'}, {'pos': 'VBZ', 'raw': 'is'}, {'pos': 'JJ', 'raw': 'fast'}, {'pos': 'JJ', 'raw': 'easy'}, {'pos': 'CC', 'raw': 'and'}, {'pos': 'JJ', 'raw': 'funny'}, {'pos': 'TO', 'raw': 'to'}, {'pos': 'VB', 'raw': 'write'}, {'pos': 'JJ', 'raw': 'regular'}, {'pos': 'NNS', 'raw': 'expressions'}, {'pos': 'IN', 'raw': 'with'},{'pos': 'NNP', 'raw': 'Pyrata'}]).group(1)
    [{'raw': 'fast', 'pos': 'JJ'}, {'raw': 'easy', 'pos': 'JJ'}, {'raw': 'and', 'pos': 'CC'}, {'raw': 'funny', 'pos': 'JJ'}]

Have a look at test_pyrata to see a more complex example of groups use.

### Data modification

#### Substitution

The `sub` method **substitutes the leftmost non-overlapping occurrences of pattern matches or a given group of matches by a dict or a sequence of dicts**. Returns a copy of the data obtained and by default the data unchanged.

    >>> import pyrata.re as pyrata_re
    >>> pyrata_re.sub('pos~"NN.?"', {'raw':'smurf', 'pos':'NN' }, [{'raw':'Over', 'pos':'IN'}, {'raw':'a', 'pos':'DT' }, {'raw':'cup', 'pos':'NN' }, {'raw':'of', 'pos':'IN'}, {'raw':'coffee', 'pos':'NN'}, {'raw':',', 'pos':','}, {'raw':'Mr.', 'pos':'NNP'}, {'raw':'Stone', 'pos':'NNP'}, {'raw':'told', 'pos':'VBD'}, {'raw':'his', 'pos':'PRP$'}, {'raw':'story', 'pos':'NN'}])
    [{'raw': 'Over', 'pos': 'IN'}, {'raw': 'a', 'pos': 'DT'}, {'raw': 'smurf', 'pos': 'NN'}, {'raw': 'of', 'pos': 'IN'}, {'raw': 'smurf', 'pos': 'NN'}, {'raw': ',', 'pos': ','}, {'raw': 'smurf', 'pos': 'NN'}, {'raw': 'smurf', 'pos': 'NN'}, {'raw': 'told', 'pos': 'VBD'}, {'raw': 'his', 'pos': 'PRP$'}, {'raw': 'smurf', 'pos': 'NN'}]

Here an example by modifying a group of a Match:

    >>> group = [1]
    >>> pyrata_re.sub('pos~"(DT|PRP\$)" (pos~"NN.?")', {'raw':'smurf', 'pos':'NN' }, [{'raw':'Over', 'pos':'IN'}, {'raw':'a', 'pos':'DT' }, {'raw':'cup', 'pos':'NN' }, {'raw':'of', 'pos':'IN'}, {'raw':'coffee', 'pos':'NN'}, {'raw':',', 'pos':','}, {'raw':'Mr.', 'pos':'NNP'}, {'raw':'Stone', 'pos':'NNP'}, {'raw':'told', 'pos':'VBD'}, {'raw':'his', 'pos':'PRP$'}, {'raw':'story', 'pos':'NN'}], group)
    [{'raw': 'Over', 'pos': 'IN'}, {'raw': 'a', 'pos': 'DT'}, {'raw': 'smurf', 'pos': 'NN'}, {'raw': 'of', 'pos': 'IN'}, {'raw': 'coffee', 'pos': 'NN'}, {'raw': ',', 'pos': ','}, {'raw': 'Mr.', 'pos': 'NNP'}, {'raw': 'Stone', 'pos': 'NNP'}, {'raw': 'told', 'pos': 'VBD'}, {'raw': 'his', 'pos': 'PRP$'}, {'raw': 'smurf', 'pos': 'NN'}]

To **update (and extends) the features of a match or a group of a match with the features of a dict or a sequence of dicts** (of the same size as the group/match).

    >>> pyrata_re.update('(raw="Mr.")', {'raw':'Mr.', 'pos':'TITLE' }, [{'raw':'Over', 'pos':'IN'}, {'raw':'a', 'pos':'DT' }, {'raw':'cup', 'pos':'NN' }, {'raw':'of', 'pos':'IN'}, {'raw':'coffee', 'pos':'NN'}, {'raw':',', 'pos':','}, {'raw':'Mr.', 'pos':'NNP'}, {'raw':'Stone', 'pos':'NNP'}, {'raw':'told', 'pos':'VBD'}, {'raw':'his', 'pos':'PRP$'}, {'raw':'story', 'pos':'NN'}])
    [{'raw': 'Over', 'pos': 'IN'}, {'raw': 'a', 'pos': 'DT'}, {'raw': 'cup', 'pos': 'NN'}, {'raw': 'of', 'pos': 'IN'}, {'raw': 'coffee', 'pos': 'NN'}, {'raw': ',', 'pos': ','}, {'raw': 'Mr.', 'pos': 'TITLE'}, {'raw': 'Stone', 'pos': 'NNP'}, {'raw': 'told', 'pos': 'VBD'}, {'raw': 'his', 'pos': 'PRP$'}, {'raw': 'story', 'pos': 'NN'}]

To **extends (i.e. if a feature exists then do not update) the features of a match or a group of a match with the features of a dict or a sequence of dicts** (of the same size as the group/match:
    >>> pattern = 'pos~"(DT|PRP\$|NNP)"? pos~"NN.?"'
    >>> annotation = {'chunk':'NP'}
    data = [ {'raw':'Over', 'pos':'IN'},  {'raw':'a', 'pos':'DT' },  {'raw':'cup', 'pos':'NN' }, {'raw':'of', 'pos':'IN'}, {'raw':'coffee', 'pos':'NN'}, {'raw':',', 'pos':','},  {'raw':'Mr.', 'pos':'NNP'},  {'raw':'Stone', 'pos':'NNP'}, {'raw':'told', 'pos':'VBD'}, {'raw':'his', 'pos':'PRP$'},  {'raw':'story', 'pos':'NN'} ]
    >>> pyrata_re.extend(pattern, annotation, data, [0])
    [{'pos': 'IN', 'raw': 'Over'}, {'pos': 'DT', 'raw': 'a', 'chunk': 'NP'}, {'pos': 'NN', 'raw': 'cup', 'chunk': 'NP'}, {'pos': 'IN', 'raw': 'of'}, {'pos': 'NN', 'raw': 'coffee', 'chunk': 'NP'}, {'pos': ',', 'raw': ','}, {'pos': 'NNP', 'raw': 'Mr.', 'chunk': 'NP'}, {'pos': 'NNP', 'raw': 'Stone', 'chunk': 'NP'}, {'pos': 'VBD', 'raw': 'told'}, {'pos': 'PRP$', 'raw': 'his', 'chunk': 'NP'}, {'pos': 'NN', 'raw': 'story', 'chunk': 'NP'}]

    >>> pyrata_re.extend(pattern, annotation, data, [0])

Both with update or extend, you can specify if the data obtained should be annotated with IOB tag prefix.
    >>> group = [0]
    >>> iob = True
    >>> pyrata_re.extend(pattern, annotation, data, group, iob)
    [{'raw': 'Over', 'pos': 'IN'}, 
     {'raw': 'a', 'chunk': 'B-NP', 'pos': 'DT'}, {'raw': 'cup', 'chunk': 'I-NP', 'pos': 'NN'}, 
     {'raw': 'of', 'pos': 'IN'}, {'raw': 'coffee', 'chunk': 'B-NP', 'pos': 'NN'}, 
     {'raw': ',', 'pos': ','}, 
     {'raw': 'Mr.', 'chunk': 'B-NP', 'pos': 'NNP'}, {'raw': 'Stone', 'chunk': 'I-NP', 'pos': 'NNP'}, 
     {'raw': 'told', 'pos': 'VBD'}, 
     {'raw': 'his', 'chunk': 'B-NP', 'pos': 'PRP$'}, {'raw': 'story', 'chunk': 'I-NP', 'pos': 'NN'}]


#### FIXME
  * 
  * 
  * updates|extends the features of a match or a group of a match with IOB values of the features of a dict or a sequence of dicts (of the same size as the group/match or kwargs ?
  Return the data obtained.  If the pattern isn't found, data is returned unchanged.



### Generating the pyrata data structure

Have a look at the `pyrata_nltk.py` script (run it). It shows __how to turn various nltk analysis results into the pyrata data structure__.
In practice two approaches are available: either by building the dict list of fly or by using the dedicated pyrata_nltk methods: `list2pyrata (**kwargs)` and `listList2pyrata (**kwargs)`. 

The former turns a list into a list of dict (e.g. a list of words into a list of dict) with a feature to represent the surface form of the word (default is `raw`). If parameter `name` is given then the dict feature name will be the one set by the first value of the passed list as parameter value of name. If parameter `dictList` is given then this list of dict will be extented with the value of the list (named or not). 

The latter turns a list of list `listList` into a list of dict with values being the elements of the second list; the value names are arbitrary choosen. If the parameter `names` is given then the dict feature names will be the ones set (the order matters) in the list passed as `names` parameter value. If parameter `dictList` is given then the list of dict will be extented with the values of the list (named or not).

Example for generating complex data on fly:

    >>> import nltk
    >>> from nltk import word_tokenize, pos_tag, ne_chunk
    >>> from nltk.chunk import tree2conlltags
    >>> sentence = "Mark is working at Facebook Corp." 
    >>> pyrata_data =  [{'raw':word, 'pos':pos, 'stem':nltk.stem.SnowballStemmer('english').stem(word), 'lem':nltk.WordNetLemmatizer().lemmatize(word.lower()), 'sw':(word in nltk.corpus.stopwords.words('english')), 'chunk':chunk} for (word, pos, chunk) in tree2conlltags(ne_chunk(pos_tag(word_tokenize(sentence))))]
    >>> pyrata_data
    [{'lem': 'mark', 'raw': 'Mark', 'sw': False, 'stem': 'mark', 'pos': 'NNP', 'chunk': 'B-PERSON'}, {'lem': 'is', 'raw': 'is', 'sw': True, 'stem': 'is', 'pos': 'VBZ', 'chunk': 'O'}, {'lem': 'working', 'raw': 'working', 'sw': False, 'stem': 'work', 'pos': 'VBG', 'chunk': 'O'}, {'lem': 'at', 'raw': 'at', 'sw': True, 'stem': 'at', 'pos': 'IN', 'chunk': 'O'}, {'lem': 'facebook', 'raw': 'Facebook', 'sw': False, 'stem': 'facebook', 'pos': 'NNP', 'chunk': 'B-ORGANIZATION'}, {'lem': 'corp', 'raw': 'Corp', 'sw': False, 'stem': 'corp', 'pos': 'NNP', 'chunk': 'I-ORGANIZATION'}, {'lem': '.', 'raw': '.', 'sw': False, 'stem': '.', 'pos': '.', 'chunk': 'O'}]

Example of uses of pyrata dedicated conversion methods: See the `pyrata_nltk.py` scripts

## Project development
---------


### Roadmap

#### Last report
Implementation of alternative sequence in groups requires first to modify the grammar. The actual branch named parsing-alternative-sequences faces some regression issues with test on groups.

Idea to handle IOB-chunk operator, is to generate a corresponding sub pattern e.g. 'chunk_"NN"' -> 'chunk="B-NN" chunk="I-NN"*'. The problem with this trick is how to handle quantifier e.g. 'chunk_"NN"*' would mean '(chunk="B-NN" chunk="I-NN"*)*'. 
In the parsing algorithm it already exists the mechanism for processing quantifiers. It would make sense to reuse it by passing in parameter the sub pattern. This echoes to the necessity to handle groups/sequences as a token. This is a common part with implementing the handling of alternative sequences in groups.

Right now (because of the issues in parsing-alternative-sequences), we will focus on modifying the syntactic parser to generate a sub list for sequence tokens, then we will modify the semantic parser to call itself on sequence tokens.

#### TODO list following a decreasing priority order.

* grammar implement IOB operator to handle sequence of tokens with a BIO value as a single token
* api/engine module re implement substitution sub/// 
* api/engine module re implement annotate/// ; the new feature is added to the current feature structure in a BIO style
* api/engine implement the position (from which word token position we start to search) and the search for annotate (not finditer) 
* grammar implement operator to search the pattern from the begining ^ and/or to the end $
* api/engine module re implement match
* quality implement logging facility
* communication packaging and distributing publish On PyPI
* communication user/developer reorganize README into specific docs : quick overview vs user guide, developer guide, roadmap pages
* quality evaluate performance http://www.marinamele.com/7-tips-to-time-python-scripts-and-control-memory-and-cpu-usage
* quality evaluate performance comparing to pattern and python 3 chunking (see the use example and show how to do similar)
* quality evaluate performance time `[pos="NNS" | pos="NNP"]`, `pos~"NN[SP]"` and 'pos~"(NNS|NNP)"' are equivalent forms. They may not have the same processing time.
* quality improve performance (memory and time) ; evaluate the possibility of doing the ply way to handle the debug/tracking mode
* grammar implement group alternative so they can be used to handle IOB-chunk operator
* grammar implement group reference so they can be matched later in the data with the \number special sequence
* grammar implement wildcards (so far handled by a `'!b*'` in `'!b* b'`
* grammar does class atomic with non atomic contraint should be prefered to not step to adapt one single way of doing stuff: partofclassconstraint -> NOT classconstraint more than step -> NOT step ; but the latter is simpler so check if it is working as expected wi quantifier +!pos:"EX" = +[!pos:"EX"])
* grammar allow grammar with multiple rules (each rule should have an identifier... and its own groupindex)
* grammar move the python methods as grammar components
* grammar think about the context notion 
* api/engine implement lex.lex(reflags=re.UNICODE)
* communication developer make diagrams to explain process and relations between files
* quality test complex regex as value
* quality code handle the test case of error in the patterns
* quality code test re methods on Compiled regular expression objects 
* quality code end location is stored several times with the expression rules ; have a look at len(l.lexer.groupstartindex): and len(l.lexer.groupendindex): after parsing in pyrata_re methods to compare 
* quality see the pattern search module and its facilities

### Achieved

Done...

#### Grammar

* implement sequence parsing
* implement CLASS OF tokens (parsing and semantic analysis with logical and/or/not operators and parenthesis)
* implement quantifier AT_LEAST_ONE
* implement quantifier OPTIONAL
* implement quantifier ANY
* implement surface EQ comparison operator for atomic constraint 
* implement list inclusion operator for atomic constraint 
* implement REGEX comparison operator for atomic constraint 
* implement groups

#### API and regex engine
* module re implement search
* module re implement findall
* module re implement finditer
* module re implement compile
* module re compiled re object implement
* module nltk implement methods to turn nltk structures (POS tagging, chunking Tree and IOB) into the pyrata data structure 
* make modular pyrata_re _syntactic_parser and semantic_parser : creation of syntactic_analysis, syntactic_pattern_parser, semantic_analysis, semantic_step_parser, 


#### Communication and code quality

* write README with short description, installation, quick overview sections
* home made debugging solution for users when writting patterns (e.g. using an attribute name not existing in the data) ; wirh verbosity levels
* a test file 
* packaging and distributing package the project (python module, structure, licence wi copyright notice, gitignore)
* packaging and distributing configure the project 


## Developpers tips
---------

* access to parsed lextoken from the grammar, the grammar/pattern step, and the data token with length, Line Number and Position based on http://www.dabeaz.com/ply/ply.html#ply_nn33
reporting-parse-errors-from-ply-to-caller-of-parser
* code handle errors wo fatal crash http://stackoverflow.com/questions/18046579/
* code fix use test_match_inside_sequence_at_least_one_including_negation_on_atomic_constraint and test_match_inside_sequence_at_least_one_including_negation_in_class_constraint
* grammar parsing solve the shift/reduce conflict with AND and OR  ; The parser does not know what to apply between Rule 10    classconstraint -> partofclassconstraint,  and   (Rule 11    classconstraint -> partofclassconstraint AND classconstraint and Rule 12  or  classconstraint -> partofclassconstraint OR classconstraint) ; sol1 : removing Rule 10 since classconstraint should only be used to combine atomic constraint (at least two); but consequently negation should be accepted wo class (i.e. bracket) and with quantifier if so ; the use of empty rule lead to Parsing error: found token type= RBRACKET  with value= ] but not expected ; sol2 : which solve the problem, inverse the order partofclassconstraint AND classconstraint  -> classconstraint AND partofclassconstraint
* Warning: code cannot rename tokens into lextokens in parser since it is Ply 
* Warning: ihm when copying the grammar in the console, do not insert whitespace ahead
* code separate lexer, syntactic parser and semantic parser in distinct files http://www.dabeaz.com/ply/ply.html#ply_nn34 
* fix parsing bug with pos~"VB." *[!raw="to"] raw="to", +[pos~"NN.*" | pos="JJ"] pos~"NN.*", *[pos~"NN.*" | pos="JJ"] pos~"NN.*", 
