# 103.7. Search text files using regular expressions

**Weight:** 2

**Description:** Candidates should be able to manipulate files and text data using regular expressions. This objective includes creating simple regular expressions containing several notational elements. It also includes using regular expression tools to perform searches through a filesystem or file content.

**Key Knowledge Areas:**

* Create simple regular expressions containing several notational elements
* Use regular expression tools to perform searches through a filesystem or file content

**Terms and Utilities:**

* grep
* egrep
* fgrep
* sed
* regex\(7\)

While we are working with text files, often it will happen that we are looking for a text in  text file.We might be looking for something that is not that specific. For example we are looking for "linux" or "Linux" or what ever, that is where regular expression come in handy.

Regular Expression

> There's a differene between basic and extended regular expressions.Some utilities is written to support only basic regular expressions and other utilities are written to support extended regular expressions as well.

grep

grep is what we use to filter texts.grep is a utility that can benifit alot from regular expressions.

Bracket Expressions

Range Expressions

Any character

line positioning 

repetition operators

`*`

`+`

`?`

multiple strings grep cat\|dog \*

Parentheses: to define subexpressions



