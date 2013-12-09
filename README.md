VCParser
========

A little combinatory parser in VDM-SL

Basics
-------

* To create a parser that accepts only "abc",
~~~~~
takeString("abc")
~~~~~
will take a string (seq of char, in VDM-SL) and returns a PARSED value in the form of either
~~~~~
mk_PARSED(mk_ERROR(message), rest)
~~~~~
where the "rest" component indicates where the parser failed, or
~~~~~
mk_PARSED(mk_TREE(label, components), rest)
~~~~~
where the mk_TREE is the parse tree and the "rest" is the remained substring of the source string after the parse.
For example, 
~~~~~
takeString("abc")("abcdefxyz")
~~~~~
will result in
~~~~~
mk_PARSED(mk_TREE(nil, "abc"), "defxyz")
~~~~~
and
~~~~~
takeString("abc")("defxyz")
~~~~~
will result in
~~~~~
mk_PARSED(mk_ERROR("Expected 'a'"), "defxyz")
~~~~~


* To create a parser that accepts "abc" trimming blanks around it,
~~~~~
trimBlanks(takeString("abc"))
~~~~~
is the function, for example,
~~~~~
trimBlanks(takeString("abc"))(" abc def xyz ")
~~~~~
will result in
~~~~~
mk_PARSED(mk_TREE(nil, "abc"), "def xyz ")
~~~~~

* To create a parser that accepts either "abc" and "def",
~~~~~
either([takeString("abc"), takeString("def")])
~~~~~
will be the one.
For example,
~~~~~
either([takeString("abc"), takeString("def")])("abcdefxyz")
~~~~~
will result in
~~~~~
mk_PARSED(mk_TREE(nil, "abc"), "defxyz")
~~~~~

* To create a parser that accepts "abc" and then "def" with trimming the surrounding blanks,
~~~~~
series([trimBlanks(takeString("abc")), trimBlanks(takeString("def"))])
~~~~~
is the function, for example,
~~~~~
series([trimBlanks(takeString("abc")), trimBlanks(takeString("def"))])(" abc  defxyz")
~~~~~
will result in
~~~~~
mk_PARSED(mk_TREE(nil, [mk_TREE(nil, "abc"), mk_TREE(nil, "def")]), "xyz")
~~~~~

* To create a parser that accepts any number of repeated "abc" (so-called "*"),
~~~~~
star(takeString("abc"))
~~~~~
is the function, for example,
~~~~~
star(takeString("abc"))("abcabcxyz")
~~~~~
will result in
~~~~~
mk_PARSED(mk_TREE(nil, [mk_TREE(nil, "abc"), mk_TREE(nil, "abc")]), "xyz")
~~~~~

* To create a parser that optionally accepts "abc" (so-called "[]"),
~~~~~
option(takeString("abc"))
~~~~~
is the function, for example,
~~~~~
option(takeString("abc"))("abcdefxyz")
~~~~~
will result in
~~~~~
mk_PARSED(mk_TREE(nil, "abc"), "defxyz")
~~~~~
and
~~~~~
option(takeString("abc"))("defxyz")
~~~~~
will result in
~~~~~
mk_PARSED(mk_TREE(nil, []), "defxyz")
~~~~~
Please note that the result above is not an error but it successfully accepted an empty substring from the given string.

* To change an error message of a parser, the "iferror" combinator is your friend.
~~~~~
iferror("I knew!", takeString("abc"))("xyz")
~~~~~
will result in
~~~~~
mk_PARSED(mk_ERROR("I knew!"), "xyz")
~~~~~

There are a bunch of other useful combinators and minimal parsers, such as any, digit, alphabet and so on.
Please read the VCParser.vdmsl for details.
