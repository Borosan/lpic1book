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

### Regular Expression

Regular expressions are used when we want to search for specify lines of text containing a particular pattern.Regex can be used in a variety of programs like grep, sed, vi, bash, rename and many more. Here we will use regex with grep command.

> There's a differene between basic and extended regular expressions.Some utilities is written to support only basic regular expressions and other utilities are written to support extended regular expressions as well.With GNU grep, there is no difference in functionality.

#### What makes up regular expressions

There are two types of characters to be found in regular expressions:

* literal characters
* metacharacters

Literal characters are standard characters that make up our strings. Every character in this sentence is a literal character. You could use a regular expression to search for each literal character in that string.

Metacharacters are a different beast altogether; they are what give regular expressions their power. With metacharacters, we can do much more than searching for a single character. Metacharacters allow us to search for combinations of strings and much more. 

 **Concatenation**

 Concatenating two regular expressions creates a longer expression. For example, the regular expression **a** will match the string **abcdcba** twice \(the first and last **a**\) and so will the regular expression **b**. However, **ab** will match only **ab**cdcba, while **ba** will match only abcdc**ba**.

 **Repetition**

* The **\***   means preceding item will be matched **0** **or more** times.
* The **+** means preceding item will be matched **1 or more** times.
* The **?** means preceding item will be matched, **0 or 1** time.
* **{N}** meanspreceding item is matched exactly N times.
* **{N,}** means preceding item is matched N or more times.
* The **{N,M}** means preceding item is matched at least **N** times, but not more than **M** times.

 **Any Characters**

The .\(dot\) is a metacharacter that stands for any character.

* The  **.**   Matches **any single character**. 

> One of the most commonly used patterns is .**‘\*’**, which matches an arbitrary length string containing any characters \(or no characters at all\)

**line positioning** 

* **^**  ****matches the beginning of the line.
* **$**  matches the end of the line.

**Bracket expression**

*  **\[set\_of\_characters\]**  Matches any single character in set\_of\_characters. By default, the match is case-sensitive.
*  **\[^set\_of\_characters\]**  _Negation:_ Matches any single character that is not in set\_of\_characters. By default, the match is case sensitive.

**Alternation** 

The altern ation operator \(\|\) matches either the preceding or following expression. for example :

* **cat\|dog** it will match **cat or dog**.

 **Range expression**

A range expression is two characters separated by a **– \(hyphen\)**, such as 0-9 for digits. Note that ranges are locale dependent.

 **Named classes**

Several named classes provide a convenient shorthand for commonly used classes. Named classes open with \[: and close with :\] and may be used within bracket expressions. Some examples:

* **\[:alnum:\]**  Alphanumeric characters.
* **\[:blank:\]**  Space and tab characters.

  **\[:digit:\]**  The digits 0 through 9 \(equivalent to 0-9\).

* **\[:upper:\] and \[:lower:\]**  Upper and lower case letters, respectively.
* **^\(negation\)**  When used as the first character after \[ in a character class, the ^ \(caret\) negates the sense of the remaining characters so the match occurs only if a character \(except the leading ^\) is **not in the class**.

> Character classes are one area where regular expressions and globbing **are** similar, although the negation differs \(^ vs. !\).

\*\*\*\*



grep

grep is what we use to filter texts.grep is a utility that can ben**e**fit a lot from regular expressions.

Bracket Expressions

Range Expressions

**any charachter**

line positioning 

repetition operators

`*`

`+`

`?`

multiple strings grep cat\|dog \*

Parentheses: to define subexpressions

.

.

.

[http://www.grymoire.com/Unix/Regular.html](http://www.grymoire.com/Unix/Regular.html)

[https://www.linux.com/tutorials/introduction-regular-expressions-new-linux-users/](https://www.linux.com/tutorials/introduction-regular-expressions-new-linux-users/)

[https://linux.die.net/Bash-Beginners-Guide/sect\_04\_01.html](https://linux.die.net/Bash-Beginners-Guide/sect_04_01.html)

.

