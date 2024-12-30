java cAssessment 4 MATH70094: Programming for Data Science Autumn 2024
Assessment 4
This assessment contains two questions that will test your ability to work with files and data in
R and Python, as well as how to create and package your code in these two languages. Question
1 is on R, while Question 2 is on Python. The available marks are indicated in brackets for each
question. Note that this assessment will count 50% towards the final grade for this module. This
assessment will be marked, and feedback will be provided.
Make sure that you carefully read the following sections on Background and Submission Instruc tions.
Background
In this assessment, we want to build a spam classifier to decide for a given message string if it is a
genuine message (ham) that we want to keep, or if it is not a genuine message (spam) that should be
filtered out. A message can be thought of as a random sequence of words, but since we hardly ever
see the same message twice it is common to ignore the word order, and to simply record how many
times each word appears. We therefore represent a message by a random vector X ∈ {0, 1, 2, . . . , }
p
with counts for a vocabulary of p words. The vocabulary stays fixed for all the messages.
Denote by P r(X) the probability of a specific message, let S be the event that X corresponds to
spam and let H be the complementary event that X is ham. From Bayes’ theorem the probability of
X being spam is
P r(S|X) = P r(X|S)P r(S)
P r(X)
.
Here, P r(S) is the prior probability of an arbitrary message being spam, and P r(X|S) is the
probability to see message X given that we know it is spam. Similarly, the probability of X being
ham is
P r(H|X) = P r(X|H)P r(H)
P r(X)
.
If P r(S|X) > P r(H|X), we classify the message X as spam, otherwise as ham.
To simplify the estimation of the probabilities P r(X|S) and P r(X|H) from training data we make
a second simplifying assumption, namely we assume that the probability of any word appearing in a
message is independent of any other word appearing or not. This means
P r(X|S) =
p
Y
j=1
P r(Xj |S), P r(X|H) =
p
Y
j=1
P r(Xj |H),
where Xj is the count of the jth word in the vocabulary. With these assumptions, the classifier is
called Naive Bayes classifier. Despite its simplicity, it works surprisingly well in practice.
Suppose now we have training data represented by a matrix M ∈ {0, 1, 2, . . . }
n×p
containing words
counts for n messages and a vector spam_type ∈ {ham, spam}
n assigning each message to a label
ham or spam. For example, Mij is the number of times word j appears in message i and spam_typei
is its label. By combining the information in M and spam_type we can compute nS and nH, the
1
Assessment 4 MATH70094: Programming for Data Science Autumn 2024
total number of spam and ham messages, nS,j and nH,j the number of times the jth word appears in
spam and ham messages, as well as NS and NH the total number of words in spam and ham messages.
With this we form the estimates
P r(S) ≈
nS
nS + nH
,
P r(H) ≈
nH
nS + nH
,
P r(Xj |S) ≈
nS,j + α
NS + α × (NS + NH)
,
P r(Xj |H) ≈
nH,j + α
NH + α × (NS + NH)
.
The scalar α ∈ (0, 1] helps preventing zero estimates. Note that by applying the logarithm,
P r(S|X) > P r(H|X) is equivalent to
p
X
j=1
log P r(Xj |S) + log P r(S) >
p
X
j=1
log P r(Xj |H) + log P r(H).
To avoid numerical errors when multiplying many near zero numbers in the approximation of the
products Q p
j=1 P r(Xj |S) and Q p
j=1 P r(Xj |H) from the estimates above, it is therefore better to
base the classification on the logarithms of the estimates.
Submission Instructions
Along with this PDF, you are provided with two folders files_train and files_test which
contain within subfolders messages (formed of strings), and two files train.csv and test.csv.
Create files according to the two questions below, and then create one zip file (https://docs.filefor
mat.com/compression/zip/) named CID_PDS_Assessment4.zip with:
• the files train.csv and test.csv,
• a folder corpus, containing your R package files,
• a folder spamfilter, containing your Python package files,
• the corpus_0.0.1.tar.gz file created in Question 1,
• the file process_corpus.R created in Question 1,
• the file filter.py created in Question 2.
This can be visualised as follows:
CID_PDS_Assessment4.zip
|-- train.csv
|-- test.csv
|-- corpus folder
|-- spamfilter folder
|-- corpus_0.0.1.tar.gz
|-- process_corpus.R
|-- filter.py
2
Assessment 4 MATH70094: Programming for Data Science Autumn 2024
Note the following before submitting:
• Do not add the folders files_train and files_test to your zip file.
• Replace CID in CID_PDS_Assessment4.zip by your own college ID number. For
example, if your college ID number is 12345678, then the zip file should be named
12345678_PDS_Assessment4.zip.
• The only external Python and R libraries allowed in this assessment are:
– Python: NumPy, Pandas, unittest,
– R: testthat, R6, stringr, stopwords
You should not load additional (non-base) libraries.
• For Python, provide doc string comments, and for R roxygen2 style comments (as described
in the Blackboard videos of week 9) for every attribute and method you define. You also
should add code comments as usual.
• Please answer in each cell/code block only the corresponding subpart (e.g., only answer Part
D(i) in the cell below the heading Part D(i)). The markers will try, where possible, not to
penalize answers to parts for errors in previous parts. For example, if you cannot do Part D(i),
leave the corresponding cell blank and do Part D(ii) assuming Part D(i) is working.
• You may use code and variables from previous subparts in your answers of a particular part.
• Marks may be deducted if these layout and format instructions are not followed.
Submit the zip file on Blackboard in the Assessment 4 submission tab in the module page. The
deadline is Monday 06 January 2025 at 09:00am, UK time.
Please note Imperial College’s policy on the late submission of assessments. This assessment must
be attempted individually. Your submission must be your own, unaided work. Candidates are
prohibited from discussing assessed coursework, and must abide by Imperial College’s rules. Enabling
other candidates to plagiarise your work constitutes an examination offence. To ensure quality
assurance is maintained, departments may choose to invite a random selection of students to an
‘authenticity interview’ on their submitted assessments.
Question 1 - R (60 marks)
The aim of this question is to build a package for loading and cleaning messages from data.
Some functions that may be useful in this question are:
• gsub, sapply, readLines, Filter,
• str_split from the stringr package,
• stopwords("en") from the stopwords package.
3
Assessment 4 MATH70094: Programming for Data Science Autumn 2024
Code clarity (5 marks)
There is a famous saying among software developers that code is read more often than it is written.
Five marks will be awarded (or not awarded) based on the clarity of the code and appropriate use
of comments.
Part A (25 marks)
Create a script file corpus.R with a R6 class CorpusR6 containing
• private attributes: ham_strings (vector of strings), spam_strings (vector of strings),
• public methods:
– initialize: a function that takes the string name of a source folder as input, reads for
each message file in this folder (and also within subfolders) the contents of the file line
by line, and adds the message text (without the message head) either to spam_strings
or ham_strings depending on if the file name contains the substring "spam" or not.
– clean_messages: a function that modifies all the messages stored in the two private
variables; it proceeds for each message string as follows:
∗ transforms the message string to lower case,
∗ splits the string into words (tokens) separated by arbitrary long whitespace and
creates with these words a vector of strings,
∗ removes from the end of each token any arbitrary sequence of punctuations,
∗ removes any token that belongs to the list of English stopwords obtained from calling
stopwords("en"),
∗ removes from each token any remaning punctuations,
∗ remove all tokens of length less than three,
∗ collapses the vector of tokens into one代 写MATH70094、Python
代做程序编程语言 string, with tokens separated by whitespace.
(We will not make more modifications to the tokens, even though we could.)
– print: a function that prints the CorpusR6 object. For example, when corpus is a
CorpusR6 object formed of 4345 ham messages and 673 spam messages, then we have as
output
> corpus
CorpusR6 object
Number of Ham files: 4345
Number of Spam files: 673
– save_to_csv: a function that takes the name of a target csv file as input and saves to
it a csv file that contains in each line either ham or spam and separated from this by a
comma a message string, either from ham_string or message_string corresponding to
the first column (the format should be as in the provided files train.csv and test.csv).
4
Assessment 4 MATH70094: Programming for Data Science Autumn 2024
In addition to providing these attributes and functions, include appropriate documentation, input
checks (for every argument!) and unit tests, which test all specifications listed above.
Part B (15 marks)
Create a package called corpus which contains the code from Part A and exposes the func tions in Part A to the user. Make sure that devtools::document(), devtools::test() and
devtools::check() do not produce any errors or warnings (notes are OK) when called from within
the folder corpus. The result should be the file corpus_0.0.1.tar.gz, if you have chosen version
number 0.0.1.
Part C (15 marks)
Make sure that the class from Part A is available. Create a script file process_corpus.R which cre ates two CorpusR6 objects corpus_train and corpus_test from the provided folders files_train
and files_test (you should set a path that works for you, the markers will set another one, make
sure these folders are not part of the final zip file!). For the two objects, clean all messages that
were contained in the folders using clean_messages and print the R6 objects to the screen. Finally,
use save_to_csv to save corpus_train to the file train.csv and corpus_test to test.csv.
The two csv files should contain the same entries as the provided files.
Question 2 - Python (90 marks)
The aim of this question is to build a package for spam classification with Naive Bayes using Test
driven development and Defensive Programming, along with an application to a real data set.
Code clarity (5 marks)
There is a famous saying among software developers that code is read more often than it is written.
Five marks will be awarded (or not awarded) based on the clarity of the code and appropriate use
of comments.
Part A (20 marks)
Create a script file utils.py with three functions:
• tokenize: A function that takes a message string as input, splits it into words (tokens) along
whitespace and returns a list with the token strings. There should be no whitespace left
anywhere within the token strings.
5
Assessment 4 MATH70094: Programming for Data Science Autumn 2024
• document_terms: A function that takes a list of word lists each created with tokenize as
input and returns a Document Term Dataframe. This Dataframe has as many rows as there
are word lists in the list, and has as many columns as there are unique words in the word lists.
Each column corresponds to one word, and each entry of the Dataframe counts how many
times a word appears in a document/message (the Dataframe is basically the matrix M from
the Background section).
• compute_word_counts: A function that takes a Document Term Dataframe (created with
document_terms) and a list spam_types of strings (with entries either ham or spam) as inputs,
and returns a 2 × p matrix with counts, where p is the length of the vocabulary, and where
the first row contains the overall counts for words in ham messages and the second row for
spam messages.
In addition to providing these functions, include appropriate documentation, input checks (for every
argument!) and unit tests, which test all specifications listed above.
For example, for the first function we expect the following output:
>>> tokenize(" properly separated text")
['properly', 'separated', 'text']
As an example for the latter two functions, suppose we have the following:
>>> doc1 = ["call", "here", "win", "prize", "money"]
>>> doc2 = ["call", "money", "call", "money", "bargain"]
>>> doc3 = ["call", "here", "information"]
>>> word_lists = [doc1, doc2, doc3]
>>> dtm = document_terms(word_lists)
>>> spam_types = ["spam", "spam", "ham"]
>>> word_counts = compute_word_counts(dtm,spam_types)
In this case, dtm should be
call here win prize money bargain information
1 1 1 1 1 1 0 0
2 2 0 0 0 2 1 0
3 1 1 0 0 0 0 1
while word_counts should be
call here win prize money bargain information
n_ham 1 1 0 0 0 0 1
n_spam 3 1 1 1 3 1 0
6
Assessment 4 MATH70094: Programming for Data Science Autumn 2024
Part B (25 marks)
Create a script file classifier.py with a class NaiveBayes containing
• private attributes: __word_counts (a matrix), __spam_types (a list),
• public attributes: log_probs_ham (a list containing the logs of the approximated P r(Xj |H)
from the Background section), log_probs_spam (a list containing the logs of the approximated
P r(Xj |S)), log_prior_ham (the log of the approximated P r(H)), log_prior_spam (the log
of the approximated P r(S)),
• public methods:
– __init__: a function that takes word_counts and spam_types as arguments, and sets
the two corresponding private attributes;
– get_spam_types: a function that returns the private attribute spam_types;
– get_word_counts: a function that returns the private attribute word_counts;
– fit: a function that takes as argument an α value (with default α = 0.5), and sets the
values of the public attributes as described in the Background section;
– classify: a function that takes a message string, tokenizes it using the method tokenize
from Part A and returns a classification ham or spam, as explained in the Background
section; words in the message that were not seen in the training data are ignored;
– print: a print method that prints the object as specified in the example below.
In addition to providing this class, include appropriate documentation and unit tests, which test all
specifications listed above.
Continuing the example from Part A we should obtain
>>> nb >> nb.fit(1)
>>> nb
NaiveBayes object
vocabulary size: 7
top 5 ham words: call,here,information,win,prize
top 5 spam words: call,money,here,win,prize
(prior_ham,prior_spam): (0.3333333,0.6666667)
Here, top 5 ham words corresponds to the five largest values in log_probs_ham, and top 5 spam
words corresponds to the five largest values in log_probs_spam.
7
Assessment 4 MATH70094: Programming for Data Science Autumn 2024
Part C (15 marks)
Create a folder containing the data for a Python package called spamfilter with the code from
Parts A and B, and exposes the functions in Part A and the class in Part B to the user. Make sure
that running python -m pytest and python -m build from within the folder spamfilter in the
command line does not produce any errors or failures. The result of python -m build should be the
file spamfilter-0.0.1.tar.gz and/or spamfilter-0.0.1*******.whl in the folder spamfilter/dist,
if you have chosen version number 0.0.1 (and ******* are optional other characters).
Part D (25 marks)
Make sure that the functions and class from Parts A and B are available. Create a script file
filter.py with code as described below.
D(i)
Load the file train.csv and create with this an object from the NaiveBayes class. Fit the object
with α = 1 and print it to the console.
D(ii)
Load also the file test.csv and classify the messages in both train.csv and test.csv using the
classifier from Part D(i). Print the confusion tables (comparing true classifications to actual ones)
for both cases, and print the accuracy (diagonal of confusion matrix divided by total number of
messages in the respective set of messages) to the console. Comment briefly on the difference
between the accuracies for both cases.
D(iii)
Continuing on from D(ii) consider α now a tuning parameter. Fit NaiveBayes messages in train.csv
for 10 evenly spaced values of α in the interval [0,1]. Determine the best such α in terms of achieving
the highest accuracy when testing on the messages in test.csv.
Discuss briefly if choosing the tuning parameter α in this way is reasonable.
8

         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
