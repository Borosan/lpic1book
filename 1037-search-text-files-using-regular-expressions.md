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
* regex(7)

While we are working with text files, often it will happen that we are looking for a text in  text file.We might be looking for something that is not that specific. For example we are looking for "linux" or "Linux" or what ever, that is where regular expression come in handy.(the most important light weight section!)

### Regular Expression

Regular expressions are used when we want to search for specify lines of text containing a particular pattern.Regex can be used in a variety of programs like grep, sed, vi, bash, rename and many more. Here we will use regex with grep command.

 A regex pattern uses a **regular expression engine** which translates those patterns.

Linux has two regular expression engines:

* The **Basic Regular Expression (BRE)** engine.
* The **Extended Regular Expression (ERE)** engine.

> There's a difference between basic and extended regular expressions.Some utilities is written to support only basic regular expressions (BRE)and other utilities are written to support extended regular expressions(ERE) as well.Most Linux programs work well with BRE engine specifications, With GNU grep, there is no difference in functionality.

## What makes up regular expressions

There are two types of characters to be found in regular expressions:

* literal characters
* metacharacters

**Literal characters** are standard characters that make up our strings. Every character in this sentence is a literal character. You could use a regular expression to search for each literal character in that string.

**Meta characters** are a different beast altogether; they are what give regular expressions their power. With meta characters, we can do much more than searching for a single character. Meta characters allow us to search for combinations of strings and much more. 

## grep

grep stands for **g**eneral **r**egular **e**xpression **p**arser**.** The grep filter searches a file for a particular pattern of characters, and displays all lines that contain that pattern.grep is a utility that can benefit a lot from regular expressions.

```
grep [options] pattern [files]
```

Lets see some examples:

| command                          | description            |
| -------------------------------- | ---------------------- |
| echo "linux is my os" \| grep l  | **l**inux is my os     |
| echo "linux is my os" \| grep i  | l**i**nux **i**s my os |

 **Concatenation**

 Concatenating two regular expressions creates a longer expression. 

| regex                                    | match                              |
| ---------------------------------------- | ---------------------------------- |
| echo "aa ab ba aaa bbb AB BA" \| grep a  | **aa a**b b**a** **aaa** bbb AB BA |
| echo "aa ab ba aaa bbb AB BA" \| grep ab | aa **ab** ba aaa bbb AB BA         |

```
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

### Extended Regular Expressions

 **Repetition**

* The **\*  ** means preceding item will be matched **0** **or more** times.
* The **+** means preceding item will be matched **1 or more** times.
* The **?** means preceding item will be matched, **0 or 1** time.

{% hint style="info" %}
#### Globbing and Regex: So Similar, So Different

 Beginners sometimes tend to confuse **wildcards**(globbing) with **regular expressions** when using grep but they are not the same.\
 **Wildcards** are a feature provided by the shell to expand file names whereas** regular expressions** are a text filtering mechanism intended for use with utilities like grep, sed and  awk.
{% endhint %}

| Special Character | Meaning in Globs                   | Meaning in Regex                                            |
| ----------------- | ---------------------------------- | ----------------------------------------------------------- |
| **\***            | zero or more characters            | zero or more of the character it follows                    |
| **?**             | single occurrence of any character | zero or one of the character it follows but not more than 1 |
| **.**             | literal "." character              | any single character                                        |

In order to avoid any mistake while using  extended regular expressions, use `grep` with` -E` option, `-E`  treats pattern as an extended regular expression(ERE).

{% hint style="info" %}
**double quotes " " : **Also we need to put our extended regex between  double quotes, other wise it might be interpreted by shell and gives us different results. 
{% endhint %}

| regex                                           | match                              |
| ----------------------------------------------- | ---------------------------------- |
| echo "aa ab ba aaa bbb AB BA" \| grep -E "a\*b" | aa **ab** **b**a aaa** bbb **AB BA |
| echo "aa ab ba aaa bbb AB BA" \| grep -E  "a+b" | aa **ab** ba aaa bbb AB BA         |
| echo "aa ab ba aaa bbb AB BA" \| grep -E "a?b"  | aa **ab** **b**a aaa **bbb** AB BA |

This is a point where egrep comes to play:

{% hint style="success" %}
## egrep

 **egrep** is a pattern searching command which belongs to the family of grep functions. It works the same way as **`grep -E`** does. It treats the pattern as an extended regular expression and prints out the lines that match the pattern. If there are several files with the matching pattern, it also displays the file names for each line.

```
egrep [ options ] 'PATTERN' files 
```

 **Options:** Most of the options for this command are same as grep.

So instead of using grep -E command in above we can use egrep easily.
{% endhint %}

#### Curly Braces

Curly braces enable us to specify the number of existence for a pattern, it has three formats:

* **{N} **meanspreceding item is matched_ exactly_ **N times**.
* **{N,} **means preceding item is matched **N or more times**.
* The **{N,M}** means preceding item is matched_ at least_ **N** _times, but not more than_ **M**_ times_.

| regex                                             | match                                   |
| ------------------------------------------------- | --------------------------------------- |
| echo "ab aab aaab aaaab ba Ab" \| egrep  "a{1}b"  | **ab** a**ab** aa**ab** aaa**ab** ba Ab |
| echo "ab aab aaab aaaab ba Ab" \| egrep "a{2,}b"  | ab **aab** **aaab** **aaaab** ba Abb    |
| echo "ab aab aaab aaaab ba Ab" \| egrep "a{1,3}b" | **ab aab aaab** a**aaab** ba Ab         |

**Any Characters**

The .(dot) is a meta character that stands for any character(except newline `\n` )

* The  **.  ** Matches **any single character**. 

> One of the most commonly used patterns is .**‘\*’**, which matches an arbitrary length string containing any characters (or no characters at all)

| regex                                         | match                     |
| --------------------------------------------- | ------------------------- |
| echo "ac abc aaabbbccc abcz" \| egrep "a.\*c" | **ac abc aaabbbccc abc**z |

**Anchor Characters**

Anchor Characters or easily Line positioning Characters are used To locate the beginning or ending of a line in a text:

* **^** ** **matches the beginning of the line.
* **$**  matches the end of the line.

```
root@ubuntu16-1:~# cat text
abc
bcd
efg
root@ubuntu16-1:~# egrep "^b" text
bcd
root@ubuntu16-1:~# egrep "g$" text
efg
```

**Alternation (multiple strings)**

The alternation operator (|) matches either the preceding or following expression. for example :

* **cat|dog** it will match** cat or dog**.

| regex                                            | match                     |
| ------------------------------------------------ | ------------------------- |
| echo "cat dog was a cartoon" \| egrep "cat\|dog" | **cat dog** was a cartoon |

### Character Classes

We can match any character with the dot special character, but what if you match a set of characters only? We can use a character class.The character class matches a set of characters if any of them found, the pattern matches.The character class is defined using square brackets \[ ]

**Bracket expression**

By placing a group of characters within brackets ("\[" and "]"), we can specify that the character at that position can be any one character found within the bracket group.

*  **\[set_of_characters]**  Matches any single character in _**set_of_characters**_. By default, the match is case-sensitive.

| regex                                              | match                     |
| -------------------------------------------------- | ------------------------- |
| echo "cat dog was a cartoon" \| egrep "cart\[o]\*" | cat dog was a **cartoo**n |

### Negating Character Classes

What about searching for a character that is not in the character class? To achieve that, precede the character class range with a caret **^.**

*  **\[^set_of_characters] ** _Negation:_ Matches any single character that is **not in** _**set_of_characters**_. By default, the match is case sensitive.

| regex                                                 | match                     |
| ----------------------------------------------------- | ------------------------- |
| echo "cat dog was a cartoon" \| grep ".\*\[^cartoon]" | **cat dog was a** cartoon |

**Range expression**

To specify a range of characters, we** **can use the **(-)** **hyphen **symbol, such as 0-9 for digits. Note that ranges are locale dependent.

| regex                                        | match                               |
| -------------------------------------------- | ----------------------------------- |
| echo "a12345a a54321a 123" \| egrep "\[a-z]" | **a**12345**a** **a**54321**a** 123 |

###  Special Character Classes (**Named classes)**

Several named classes provide a convenient shorthand for commonly used classes. Named classes open with \[: and close with :] and may be used within bracket expressions. Some examples:

* **\[\[:alnum:]] ** Alphanumeric characters.
* **\[\[:alpha:]]  **Alphabetic characters.
* **\[\[:blank:]] ** Space and tab characters.
* **\[\[:digit:]] ** The digits 0 through 9 (equivalent to 0-9).
* **\[\[:upper:]] **and** \[:lower:] ** Upper and lower case letters, respectively.

| **regex**                                                 | match                               |
| --------------------------------------------------------- | ----------------------------------- |
| echo "a12345a a54321a 123 678 9" \| egrep "\[\[:alpha:]]" | **a**12345**a** **a**54321**a** 123 |

**Group expressions**

By placing part of a regular expression inside parentheses, we can group that part of the regular expression together.

* **() Groups regular expressions**

| regex                                                    | match                         |
| -------------------------------------------------------- | ----------------------------- |
| echo "a12345a a54321a 123 678 9" \| egrep "(1a.\*)\|(9)" | a12345a a5432**1a 123 678 9** |

### **Special Characters**

regex patterns use some special characters. And we can’t include them in your patterns and if we do so, we won’t get the expected result.These special characters are recognized by regex:

**.\*\[]^${}\\+?|()**

So how we can search for some of them inside a text? That's where fgrep comes to play.

### fgrep

 If you don't want the power of regex, it can be very frustrating. This is especially true if you're actually looking for some of the special characters in a bunch of text. You can use the `fgrep` command (or `grep -F`, which is the same thing) in order to skip any regex substitutions. Using `fgrep`, you'll search for exactly what you type, even if they are special characters.

```
fgrep [options] [ -e pattern_list] [pattern] [file]
```

```
    -c : It is used to print only a count of the lines which contain the pattern.
    -h : Used to display the matched lines.
    -i : During comparisions, it will ignore upper/lower case distinction.
    -l : Used to print the names of files with matching lines once, separated by new-lines. It will not repeat the names of files when the pattern is found more than once.
    -n : It is used precede each line by its line number in the file (first line is 1).
    -s : It will only display the error messages.
    -v : Print all lines except those contain the pattern.
    -x : Print only lines matched entirely.
    -e pattern_list : Search for a string in pattern-list (useful when the string begins with a “-“).
    -f pattern-file : Take the list of patterns from pattern-file.
    pattern : Specify a pattern to be used during the search for input.
    file : A path name of a file to be searched for the patterns. If no file operands are specified, the standard input will be used.

```

as an example:

```
root@ubuntu16-1:~# cat text
^abc
bcd$
efg|
root@ubuntu16-1:~# fgrep "^" text
^abc
root@ubuntu16-1:~# fgrep "$" text
bcd$
```

### sed

 Sed command or **S**tream** Ed**itor is very powerful utility offered by Linux/Unix systems. It is mainly used for **text substitution** , **find & replace** but it can also perform other text manipulations like **insertion**, **deletion**, **search ,** etc. 

With **sed**, we can edit complete files without actually having to open it. Sed also supports the use of regular expressions, which makes sed an even more powerful **text manipulation tool**.

 We have previously used sed for text substitution, here we want to use regex with that.

```
sed OPTIONS... [SCRIPT] [INPUTFILE...]
```

 `-r, --regexp-extended ` tells sed that we are using **regular expressions** in the script.

```
root@ubuntu16-1:~# cat text
Comment this is a  comment:
comment pigs can not fly because the sky is high!

root@ubuntu16-1:~# sed -r "s/^(Comment|comment)/\#/" text
# this is a  comment:
# pigs can not fly because the sky is high!
```

By default, sed prints every line. If it makes a substitution, the new text is printed instead of the old one. If you use an optional argument to sed, "sed -n," it will not:

```
root@ubuntu16-1:~# sed -rn "s/^(Comment|comment)/\#/"  text
root@ubuntu16-1:~# 
```

When the "-n" option is used, the "p" flag will cause just the modified line to be printed:

```
root@ubuntu16-1:~# sed -rn "s/^(Comment|comment)/CHANGED/p"  text
CHANGED this is a  comment:
CHANGED pigs can not fly because the sky is high!
```

sed is very powerfull tools and that is  complicated, we have just take a quick lookat it! 

.

.

.

[http://www.grymoire.com/Unix/Regular.html](http://www.grymoire.com/Unix/Regular.html)

[https://www.linux.com/tutorials/introduction-regular-expressions-new-linux-users/](https://www.linux.com/tutorials/introduction-regular-expressions-new-linux-users/)

[https://linux.die.net/Bash-Beginners-Guide/sect\_04\_01.html](https://linux.die.net/Bash-Beginners-Guide/sect\_04\_01.html)

****[https://dzone.com/articles/35-examples-of-regex-patterns-using-sed-and-awk-in](https://dzone.com/articles/35-examples-of-regex-patterns-using-sed-and-awk-in)

[https://www.digitalocean.com/community/tutorials/using-grep-regular-expressions-to-search-for-text-patterns-in-linux#regular-expressions](https://www.digitalocean.com/community/tutorials/using-grep-regular-expressions-to-search-for-text-patterns-in-linux#regular-expressions)

[https://www.linuxnix.com/10-file-globbing-examples-linux-unix/](https://www.linuxnix.com/10-file-globbing-examples-linux-unix/)

[https://www.linuxjournal.com/content/globbing-and-regex-so-similar-so-different](https://www.linuxjournal.com/content/globbing-and-regex-so-similar-so-different)

[https://www.zyxware.com/articles/4627/difference-between-grep-and-egrep](https://www.zyxware.com/articles/4627/difference-between-grep-and-egrep)

[https://www.geeksforgeeks.org/fgrep-command-in-linux-with-examples/](https://www.geeksforgeeks.org/fgrep-command-in-linux-with-examples/)

[https://www.linuxtechi.com/20-sed-command-examples-linux-users/](https://www.linuxtechi.com/20-sed-command-examples-linux-users/)

[http://www.grymoire.com/Unix/Sed.html](http://www.grymoire.com/Unix/Sed.html)

[https://www.linuxtechi.com/20-sed-command-examples-linux-users/](https://www.linuxtechi.com/20-sed-command-examples-linux-users/)

.
