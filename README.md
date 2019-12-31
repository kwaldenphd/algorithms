# Introduction to algorithms lab

<a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license"><img style="border-width: 0;" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" alt="Creative Commons License" /></a>
This tutorial is licensed under a <a href="http://creativecommons.org/licenses/by-nc/4.0/" rel="license">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

This lab builds on previous work with sorting and searching algorithms. In this lab, we will explore how an algorithm script built in Python can automate the process of stripping HTML tags from downloaded web content. 

Instead of executing Python code in blocks or sections, we will write an entire `.py` file that we can execute to automate all the functions or steps outlined in the file. More on this later.

## Lab Goals

- Introduce Uniform Resource Locators (URLs)
- Explain how to use Python to download and save the contents of a web page to your local hard drive
- Learn how to remove the HTML markup from a webpage
- Introduce looping and branching to repeat tasks and test for certain conditions
- Convert a long string to a list of words that canbe sorted, indexed, and counted

## Acknowledgements

This lab is based on and uses content from the following Programming Historian tutorials:
- William J. Turkel and Adam Crymble, "Downloading Web Pages with Python," The Programming Historian 1 (2012), https://programminghistorian.org/en/lessons/working-with-web-pages.
- William J. Turkel and Adam Crymble, "From HTML to List of Words (part 1)," The Programming Historian 1 (2012), https://programminghistorian.org/en/lessons/from-html-to-list-of-words-1.
- William J. Turkel and Adam Crymble, "From HTML to List of Words (part 2)," The Programming Historian 1 (2012), https://programminghistorian.org/en/lessons/from-html-to-list-of-words-2.
- William J. Turkel and Adam Crymble, "Code Reuse and Modularity in Python," The Programming Historian 1 (2012), https://programminghistorian.org/en/lessons/code-reuse-and-modularity.

#  Table of Contents
- [Using Python to Download Web Pages](#using-python-to-download-web-pages)
  * [What is a URL](#what-is-a-url)
  * [Using Python to Open URLs](#using-python-to-open-urls)
  * [Saving a Web Page](#saving-a-web-page)
- [Devising an Algorithm](#devising-an-algorithm)
  * [Explore the HTML & Identify Patterns](#explore-the-html--identify-patterns)
  * [Outline the Algorithm](#outline-the-algorithm)
- [Building the Algorithm in Python](#building-the-algorithm-in-python)
  * [Review of Looping and Branching](#review-of-looping-and-branching)
  * [Building the `stripTags` routine](#building-the-striptags-routine)
  * [`continue` and `return`](#continue-and-return)
- [Putting it all together](#putting-it-all-together)
- [Next steps](#next-steps)
  * [Writing to a Text File](#writing-to-a-text-file)
  * [Creating a list of words](#creating-a-list-of-words)
- [Why did we do this?](#why-did-we-do-this)
- [Oh the Places You Could Go](#oh-the-places-you-could-go).
- [Lab Notebook Questions](#lab-notebook-questions)

# Using Python to Download Web Pages

## What is a URL

1. Uniform Resource Locators (URLs) tell your browser where to find an online resource by specifying the server, directory and file name. The URL also specifies the protocol the server and your browser use to exchange information.

2. The basic structure of a URL is: `protocol://host:port/path?query`

3. Look at a few of the Wikisource URLs for [State of the Union Speeches by United States Presidents](https://en.wikisource.org/wiki/Portal:State_of_the_Union_Speeches_by_United_States_Presidents). 

<blockquote>Q1: What pattern do you see emerging in these URLs? Use this pattern to predict the URL for a specific speech.</blockquote>

## Using Python to Open URLs

4. Spend a few minutes looking at the structure of a page for a specific speech. Here we are not so much interested in what the transcript says, but what features the page has. 

<blockquote>Q2: What elements do you see on the page, in addition to the text of the speech?</blockquote>

5. Now let’s try opening the page using Python. 

6. Open a new Python file in Geany and save it as `open-webpage.py`.

7. Add the following lines to the blank file. Replace the Wikisource URL with a link to a speech of your choosing:
```Python
# open-webpage.py

import urllib

url = 'https://en.wikisource.org/wiki/John_F._Kennedy%27s_Third_State_of_the_Union_Address'

response = urllib.urlopen(url)
webContent = response.read()

print(webContent[0:300])
```

<blockquote>Q3: Based on our previous work with Python, what do you think is happening in these lines of code?</blockquote>
 
8. A few things happen with this code:
  a. it opens the speech URL
  b. it reads the contents of the URL into the `webContent` string
  c. it prints the first 300 characters of the string

9. `url`, `response`, and `pageContents` are all variables named in this code.
  a. `url` holds the URL of the web page we are going to download
  b. `urlopen` is a function from Python's `urllib2.py` module
  c. `response` saves the result of the URL we opened using `urlopen`
  d. `read` copies the contents of `response` into the `webContent` variable

<blockquote>Consult the Python documentation to learn more about the urllib library: https://docs.python.org/3/library/urllib.html</blockquote>

10. This block of code includes 3 variables, 1 module, 2 methods, and 1 parameter.

<blockquote>Q4: What are the variables, module, methods, and parameter in this block of code?</blockquote>

11. You will see some HTML markup in the resulting output. The text of the speech is much further down the page, past the first 300 characters.

12. Our goal for this lab is to build an algorithm that removes the HTML markup to leave you with the plain text of the speech.

## Saving a Web page

13. We want to modify the `open-webpage.py` program to save a copy of the `webContent` variable to an HTML file on our computer.

14. Use Geany's Save As function to save the `open-webpage.py` program as `save-webpage.py`. Replace the URL and HTML file name with your own content.
```Python
# save-webpage.py

import urllib

url = 'https://en.wikisource.org/wiki/John_F._Kennedy%27s_Third_State_of_the_Union_Address'

response = urllib.urlopen(url)
webContent = response.read()

f = open('Kennedy_Third_SOTU.html', 'w')
f.write(webContent)
f.close
```

<blockquote>Q5: What do you think is happening in the last three lines of our new program? Consult (and cite) additional Python documentation as needed. What do you think will be the output of this program?</blockquote>

15. Execute the program and open the saved `.HTML` file in a text editor.

# Devising an algorithm

16. In this section of the lab, we will build an algorithm in Python that uses a variety of string operators and string methods, as well as looping and branching, to separate content from HTML tags.

## Explore the HTML & Identify Patterns

17. Open your `.HTML` file and scroll through the source code.

<blockquote>Q6: What kinds of HTML markup do you see in the file? Where is the markup located in relation to the content text?</blockquote>

<blockquote>Q7: What patterns do you see in the HTML? How could these patterns help you write a program that removes unnecessary markup?</blockquote>
 
## Outline the algorithm

18. Based on the patterns you identified the previous step, outline the steps your algorithm will take to remove unnecessary markup.  At this point, we are not thinking in terms of specific Python commands or functions. 

19. Think about how you would describe the algorithm to someone who has little background in Python.

<blockquote>Q8: What are the steps for your algorithm?</blockquote>

20. For example, when looking at the HTML for President Kennedy's third inaugural address, I notice that `<div></div>` tags are used for metadata or menu items, and `<p></p>` tags surround the content text. I also notice that `<div></div>` tags are used for content that comes before and after the content text.

21. I might want to build an algorithm that works through the following steps:
  a. downloads the web page as an `.HTML` file
  b. searches the HTML for the first `<p>` tag
  c. stores the location of the first `<p>` tag
  d. searches the HTML for the last `</p>` tag
  e. saves the location of the last `</p>` tag
  f. saves everything after the first `<p>` tag and before the last `</p>` tag to a string

22. Now I also need to instruct Python how to handle the `<p></p>` tags located within the content text. 

23. Once the algorithm has isolated the content text, the following steps will remove the remaining markup:
  g. look at every character in the string
  h. ignore characters that follow a left angle bracket `<`
  i. look at and save characters that follow a right angle bracket `>`
  j. append the characters not in a tag to a new variable

<blockquote>Q9: Do you want or need to revise the steps for your algorithm? How and why?</blockquote>

# Building the Algorithm in Python

## Review of Looping and Branching

24. Like many programming languages, Python includes a number of looping mechanisms. 

25. In this lab, we'll use the `for` loop to instruct the program to do something for each character in a string. 

26. We also need a way to test the contents of a string, and choose a course of action based on that test. `if` statements in Python are a branching mechanism we can use for this purpose.

<blockquote>Python uses a single equals sign (=) for assignment, to set one thing equal to something else. In order to test for equality, use double equals signs (==) instead.</blockquote>

## Building the `stripTags` routine

27. Let's build a routine called `stripTags` that removes the `<div></div>` tag sections before and after the content text.

```Python
# stripTags.py
def stripTags(pageContents):
    startLoc = pageContents.find("<p>")
    endLoc = pageContents.rfind("</p>")

    pageContents = pageContents[startLoc:endLoc]
    return pageContents
```

28. The `stripTags` routine uses Python `find` and `rfind` to look for the first instance of the `<p>` tag and the last instance of the `</p>` tag.

<blockquote>Q10: Search available Python documentation to describe what <code>find</code> and <code>rfind</code> are doing in this program. Be sure to cite relevant sources consulted.</blockquote>

29. Now we have a routine that will remove the `<div></div>` tags markup before and after our content text.

30. We still need to instruct Python how to handle the `<p></p>` tags that remain in `pageContents`.

```Python
# stripTags.py
def stripTags(pageContents):
    startLoc = pageContents.find("<p>")
    endLoc = pageContents.rfind("</p>")

    pageContents = pageContents[startLoc:endLoc]

    inside = 0
    text = ''

    for char in pageContents:
        if char == '<':
            inside = 1
        elif (inside == 1 and char == '>'):
            inside = 0
        elif inside == 1:
            continue
        else:
            text += char

    return text
```
### `continue` and `return`

31. There are two new Python concepts in this program: `continue` and `return`

32. The Python `continue` statement tells the interpreter to jump back to the top of the enclosing loop. 

33. So if we are processing characters inside of a pair of angle brackets, we want to go get the next character in the `pageContents` string without adding anything to our `text` variable.

34. `print` outputs the result of our program for the user to read. 

35. However, in this case we want one part of a program to send information to another part. When a function finishes executing, it can `return` a value to the code which called it. 

<blockquote>Q11: Explain what the <code>stripTags</code> routine is doing in your own words. What alternatives to the <code>for-if</code> and <code>elif</code> statements could we use to remove the remaining <code><p></p></code> tags?</blockquote>

## Putting it all together (so far)

36. At this point, we have `save-webpage.py` and `stripTags.py`.

```Python
# save-webpage.py

import urllib

url = 'https://en.wikisource.org/wiki/John_F._Kennedy%27s_Third_State_of_the_Union_Address'

response = urllib.urlopen(url)
webContent = response.read()

f = open('Kennedy_Third_SOTU.html', 'w')
f.write(webContent)
f.close
```

```Python
# stripTags.py
def stripTags(pageContents):
    startLoc = pageContents.find("<p>")
    endLoc = pageContents.rfind("</p>")

    pageContents = pageContents[startLoc:endLoc]

    inside = 0
    text = ''

    for char in pageContents:
        if char == '<':
            inside = 1
        elif (inside == 1 and char == '>'):
            inside = 0
        elif inside == 1:
            continue
        else:
            text += char

    return text
```
37. We need to think about how best to bring those two programs together, using the concepts of code reuse and modularity.

38. Our `save-webpage.py` file includes a program that downloads and saves a web page as an `HTML` file. 

39. To run `stripTags.py`, we would need to reopen or reload that `HTML` file in Python as a string in order to execute the `stripTags` routine.

40. What if we wanted to combine those two steps to be part of the same algorithm? This is where code reuse and modularity come in.

41. Modularity allows you to work on sections of a larger program. You can refine and de-bug each section before putting them all together. This makes it easier to reuse individual modules in other programs and identify and fix programs in your code.

42. Breaking a program into modules hides the details for how something is done within the module that does it. Other modules don’t need to know how something is accomplished if they are not responsible for doing it. This need-to-know principle is called “encapsulation."

43. We are going to save the `stripTags` routine in its own file, as a module we can import into another program. In this case, we will import `stripTags` into a new `HTML-to-text` program.

44. We can think of the entire `stripTags` routine as its own program. 

45. Our updated `save-webpage` program that calls `stripTags` as a module might look like this:
```Python
#HTML-to-text.py
import urllib, stripTags

url = 'https://en.wikisource.org/wiki/John_F._Kennedy%27s_Third_State_of_the_Union_Address'

response = urllib.urlopen(url)
HTML = response.read()

print((stripTags.stripTags(HTML)))
```

46. Running `HTML-to-text.py` goes through the following steps:
  a. gets the web page for the Wikisource URL
  b. looks in the `stripTags.py` module for the `stripTags` function
  c. uses that function to remove the `<div></div>` tag content AS WELL AS the `<p></p>` tags that remain in the content text
  d. prints the output of the `stripTags` function

47. To recap:

  * The `stripTags` function stored in `stripTags.py` requires one argument. In other words, to run properly it needs one piece of information to be supplied. 

  * The `stripTags` function in `stripTags.py` needs one thing: a string called `pageContents`. But you’ll notice that when we call `stripTags` in the final program (`HTML-to-text.py`), there’s no mention of `pageContents`. 

  * Instead, the function is given `HTML` as an argument. This is confusing. Once a function has been declared, we no longer need to use the same variable name when we call the function. As long as we provide the right type of argument, everything should work fine, no matter what we call it. 

  * In this case we want `pageContents` to use the contents of our `HTML` variable. 

<blockquote>Q12: Describe how code reuse and modularity works in your own words. How would you explain what is happening when Python calls as module from another program?</blockquote>

## Next steps

48. Now that we have the ability to extract raw text from web pages, we want to get the text in a form that is easy to process. 

### Writing to a Text File

49. One option is to save the markup-free text as a `.txt` file. 

50. In a previous step for this lab, we used Python's `read` and `write` functions to create a new file and write a variable to that file.

51. Example from the original `save-webpage.py`:
```Python
f = open('Kennedy_Third_SOTU.html', 'w')
f.write(webContent)
f.close
```
<blockquote>Q13: How would you change the end of <code>HTML-to-text.py</code> to save the output as a <code>.txt</code> file?</blockquote>

### Creating a list of words

52. Another option would be to convert the markup-free text into a list of words in Python. Remember a list is an ordered collection of objects.

53. We need to convert the string output from `stripTags` into list of words.

54. A sample program for that conversion:
```Python
#html-to-list1.py
import urllib, stripTags

url = 'https://en.wikisource.org/wiki/John_F._Kennedy%27s_Third_State_of_the_Union_Address'

response = urllib.urlopen(url)
html = response.read()
text = stripTags.stripTags(html)
wordlist = text.split()

print((wordlist[0:120]))
```
<blockquote>Q14: Explain what is happening in <code>html-to-list1.py</code> in your own words.</blockquote>

<blockquote>Q15: What is the output when you execute <code>html-to-list1.py</code>?</blockquote>

# Why did we do this?

At this point, your brain probably hurts. Mine does.

Why go to all the trouble of building an algorithm in Python when we could just copy and paste the text from Wikisource into a `.txt` file? Why build an algorithm when we could just remove the markup ourselves through manual data manipulation?

That's a fair question.

Scientific disciplines value research that can be reproduced and replicated. A 2013 editorial in *PLOS Computational Biology* outlines the value of reproducible computational research:

- "The importance of replication and reproducibility has recently been exemplified through studies showing that scientific papers commonly leave out experimental details essential for reproduction, studies showing difficulties with replicating published experimental results, an increase in retracted papers, and through a high number of failing clinical trials.

- "We want to emphasize that reproducibility is not only a moral responsibility with respect to the scientific field, but that a lack of reproducibility can also be a burden for you as an individual researcher. As an example, a good practice of reproducibility is necessary in order to allow previously developed methodology to be effectively applied on new data, or to allow reuse of code and results for new projects. In other words, good habits of reproducibility may actually turn out to be a time-saver in the longer run.

- "We further note that reproducibility is just as much about the habits that ensure reproducible research as the technologies that can make these processes efficient and realistic."

<em>Sandve GK, Nekrutenko A, Taylor J, Hovig E (2013) Ten Simple Rules for Reproducible Computational Research. PLOS Computational Biology 9(10): e1003285. https://doi.org/10.1371/journal.pcbi.1003285.</em>

Manually wrangling or manipulating data makes it difficult for colleagues (and future you) to understand how you got from point A to point B with your data. It also increases the likelihood of human error. In the *PLOS Computational Biology* article, one of the ten rules the authors outline is "Avoid Manual Data Manipulation Steps."

The workflow we built in Python automates the data manipulation process and creates a template others could implement or adapt in their own work. 

# Oh, the Places You Could Go

The list of words includes data that is still very messy, even with the HTML tags removed.

Other Programming Historian tutorials walk through how to use Python to normalize textual data.
- William J. Turkel and Adam Crymble, "Working with Text Files in Python," The Programming Historian 1 (2012), https://programminghistorian.org/en/lessons/working-with-text-files.
- William J. Turkel and Adam Crymble, "Normalizing Textual Data with Python," The Programming Historian 1 (2012), https://programminghistorian.org/en/lessons/normalizing-data.

With normalized textual data, you could use Python to do things like:
- count word frequency
  * William J. Turkel and Adam Crymble, "Counting Word Frequencies with Python," The Programming Historian 1 (2012), https://programminghistorian.org/en/lessons/counting-frequencies.
- analyze keywords in contact using n-grams
  * William J. Turkel and Adam Crymble, "Keywords in Context (Using n-grams) with Python," The Programming Historian 1 (2012), https://programminghistorian.org/en/lessons/keywords-in-context-using-n-grams.

Why go to all this  trouble? 

Computational text analysis or other kinds of natural language processing allow us to see patterns and detect change over time in large bodies of textual materials.

A few projects that do this with speeches given by U.S. presidents:
- [Google News Lab's Inaugurate project](http://inauguratespeeches.com/) that looks at the subjects of inauguration speeches
- ["The Language of the State of the Union,"](https://www.theatlantic.com/politics/archive/2015/01/the-language-of-the-state-of-the-union/384575/) an interactive project from the *Atlantic*'s Ben Schmidt and Mitch Fraas that "reveals how the words presidents use reflect the twists and turns of American history"
- ["Mapping the State of the Union,"](https://www.theatlantic.com/politics/archive/2015/01/mapping-the-state-of-the-union/384576/), also from Schmidt and Fraas, that "shows the 1,410 different spots on the globe presidents have referenced in 224 speeches"

# Lab Notebook Questions

In addition to the lab notebook questions, include final versions of the following `.py` files. 
- `save-webpage.py`
- `stripTags.py`
- `HTML-to-text.py`
- `HTML-to-list.py`

Q1: What pattern do you see emerging in these URLs? Use this pattern to predict the URL for a specific speech.

Q2: What elements do you see on the page, in addition to the text of the speech?

Q3: Based on our previous work with Python, what do you think is happening in these lines of code?
 
Q4: What are the variables, module, methods, and parameter in this block of code?

Q5: What do you think is happening in the last three lines of our new program? Consult (and cite) additional Python documentation as needed. What do you think will be the output of this program?

Q6: What kinds of HTML markup do you see in the file? Where is the markup located in relation to the content text?

Q7: What patterns do you see in the HTML? How could these patterns help you write a program that removes unnecessary markup?

Q8: What are the steps for your algorithm?

Q9: Do you want or need to revise the steps for your algorithm? How and why?

Q10: Search available Python documentation to describe what `find` and `rfind` are doing in this program. Be sure to cite relevant sources consulted.

Q11: Explain what the `stripTags` routine is doing in your own words. What alternatives to the `for-if` and `elif` statements could we use to remove the remaining `<p></p>` tags?

Q12: Describe how code reuse and modularity works in your own words. How would you explain what is happening when Python calls as module from another program?

Q13: How would you change the end of `HTML-to-text.py` to save the output as a `.txt` file?

Q14: Explain what is happening in `html-to-list1.py` in your own words.

Q15: What is the output when you execute `html-to-list1.py`?
