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

 A regex pattern uses a regular expression engine which translates those patterns.

Linux has two regular expression engines:

* The **Basic Regular Expression \(BRE\)** engine.
* The **Extended Regular Expression \(ERE\)** engine.

> There's a differene between basic and extended regular expressions.Some utilities is written to support only basic regular expressions \(BRE\)and other utilities are written to support extended regular expressions\(ERE\) as well.Most Linux programs work well with BRE engine specifications, With GNU grep, there is no difference in functionality.

## grep

grep stands for **g**eneral **r**egular **e**xpression **p**arser**.** The grep filter searches a file for a particular pattern of characters, and displays all lines that contain that pattern.grep is a utility that can benefit a lot from regular expressions.

```text
grep [options] pattern [files]
```

```text
Options Description
-c : This prints only a count of the lines that match a pattern
-h : Display the matched lines, but do not display the filenames.
-i : Ignores, case for matching
-l : Displays list of a filenames only.
-n : Display the matched lines and their line numbers.
-v : This prints out all the lines that do not matches the pattern
-e exp : Specifies expression with this option. Can use multiple times.
-f file : Takes patterns from file, one per line.
-E : Treats pattern as an extended regular expression (ERE)
-w : Match whole word
-o : Print only the matched parts of a matching line,
 with each such part on a separate output line.
```

## What makes up regular expressions

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

Curly braces enable you to specify the number of existence for a pattern, it has three formats:

* **{N}** meanspreceding item is matched exactly N times.
* **{N,}** means preceding item is matched N or more times.
* The **{N,M}** means preceding item is matched at least **N** times, but not more than **M** times.

**Brace expansion**

 The syntax for brace expansion is either a sequence or a comma separated list of items inside curly braces "{}". The starting and ending items in a sequence are separated by two periods "..".

**Any Characters**

The .\(dot\) is a metacharacter that stands for any character\(except newline `\n` \)

* The  **.**   Matches **any single character**. 

> One of the most commonly used patterns is .**‘\*’**, which matches an arbitrary length string containing any characters \(or no characters at all\)

**Anchor Characters**

Anchor Characters or easily Line positioning Characters are used To locate the beginning or ending of a line in a text:

* **^**  ****matches the beginning of the line.
* **$**  matches the end of the line.

**Alternation \(multiple strings\)**

The alternation operator \(\|\) matches either the preceding or following expression. for example :

* **cat\|dog** it will match **cat or dog**.

### Character Classes

We can match any character with the dot special character, but what if you match a set of characters only, you can use a character class.The character class matches a set of characters if any of them found, the pattern matches.The character class is defined using square brackets \[\]

**Bracket expression**

By placing a group of characters within brackets \("\[" and "\]"\), we can specify that the character at that position can be any one character found within the bracket group.

*  **\[set\_of\_characters\]**  Matches any single character in set\_of\_characters. By default, the match is case-sensitive.

### Negating Character Classes

What about searching for a character that is not in the character class?To achieve that, precede the character class range with a caret ^

*  **\[^set\_of\_characters\]**  _Negation:_ Matches any single character that is not in set\_of\_characters. By default, the match is case sensitive.

> Character classes are one area where regular expressions and globbing **are** similar, although the negation differs \(^ vs. !\).

 **Range expression**

To specify a range of characters, we ****can use the \(-\) **hyphen** symbol, such as 0-9 for digits. Note that ranges are locale dependent.

###  Special Character Classes \(**Named classes\)**

Several named classes provide a convenient shorthand for commonly used classes. Named classes open with \[: and close with :\] and may be used within bracket expressions. Some examples:

* **\[:alnum:\]**  Alphanumeric characters.
* **\[:alpha:\]**  Alphabetic characters.
* **\[:blank:\]**  Space and tab characters.
* **\[:digit:\]**  The digits 0 through 9 \(equivalent to 0-9\).
* **\[:upper:\] and \[:lower:\]**  Upper and lower case letters, respectively.

**Group expressions**

By placing part of a regular expression inside parentheses, we can group that part of the regular expression together.

* **\(\) Groups regular expressions**

**Special Characters**

As you have seen regex patterns use some special characters. And we can’t include them in your patterns and if we do so, we won’t get the expected result.

These special characters are recognized by regex:

**.\*\[\]^${}\+?\|\(\)**

We need to escape these special characters using the backslash character \(\\).

### grep

grep stands for **g**eneral **r**egular **e**xpression **p**arser**.** The grep filter searches a file for a particular pattern of characters, and displays all lines that contain that pattern.grep is a utility that can benefit a lot from regular expressions.

```text
grep [options] pattern [files]
```

```text
Options Description
-c : This prints only a count of the lines that match a pattern
-h : Display the matched lines, but do not display the filenames.
-i : Ignores, case for matching
-l : Displays list of a filenames only.
-n : Display the matched lines and their line numbers.
-v : This prints out all the lines that do not matches the pattern
-e exp : Specifies expression with this option. Can use multiple times.
-f file : Takes patterns from file, one per line.
-E : Treats pattern as an extended regular expression (ERE)
-w : Match whole word
-o : Print only the matched parts of a matching line,
 with each such part on a separate output line.
```



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

\*\*\*\*[https://dzone.com/articles/35-examples-of-regex-patterns-using-sed-and-awk-in](https://dzone.com/articles/35-examples-of-regex-patterns-using-sed-and-awk-in)

[https://www.digitalocean.com/community/tutorials/using-grep-regular-expressions-to-search-for-text-patterns-in-linux\#regular-expressions](https://www.digitalocean.com/community/tutorials/using-grep-regular-expressions-to-search-for-text-patterns-in-linux#regular-expressions)

.

