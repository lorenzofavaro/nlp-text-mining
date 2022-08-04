# nlp-text-mining
This project has been created for the exam of Tecnologie del Linguaggio Naturale (NLP) of the Master's Degree course at the University of Turin. This is the third part of it.

## Setup
- Clone repository
- Create a virtual environment and activate it
    - To create it, just open a console in the project root folder and execute `py -m venv venv`
    - Next, to activate it, type `call ./venv/Scripts/activate.bat` in the same console
- Install all the required libraries through `pip install -r requirements.txt`
- Download the small spacy model (needed for the hanks notebook): `py -m spacy download en_core_web_sm`
- Open and launch any notebook

The project focuses on the implementation of some notebooks each concerning a different NLP task. Each notebook is documented internally but for clarity and completeness the documentation of each will be reported below.

## Notebooks

### Defs
Here we computed the similarity between the definitions given by us (unito students) for 4 terms. The choice of terms was made during a lesson:
<p align="center">
  <img src="https://github.com/lorenzofavaro/nlp-text-mining/blob/main/docs/word_categories.png"/>
</p>

The goal of the exercise was to show evidence of how difficult it was to write the definition of a concept and how difficult it was to agree on a single definition.
Through the [cosine similarity](https://en.wikipedia.org/wiki/Cosine_similarity), we see that the similarity between the definitions is higher for "Person" that is our most generic concepts.
Through the most frequent words similarity (we compute mean score for the most frequent words), instead we got that the similarity is highest for the definition of "Brick", followed by "Person" that we got before.

As we can see in the notebook, both the scores method return low scores, therefore we can conclude that (as we expected) giving definitions to a concept is really a hard task!
Furthermore, we observe that (as we have seen during the lessons) the similarity is higher for the concrete concepts.

### Content2Form
The Content2Form exercise tackled the problem of onomasiological research. This task consists in identifying a concept starting from its definition. Like writing definitions, onomasiological research is a difficult problem to tackle. Since the goal pursued by this task is substantially the opposite of the writing of definitions, we have studied onomasiological research by introducing the mechanism of the Genus - differentia, with which we want to represent a concept starting from the genus which is usually a hyperonym of the concept, and identifying a series of distinctive characteristics of the concept (differentia) such as to differentiate it from any other in the hierarchy in which it is framed. The mechanism just introduced can be used on the contrary by looking in the definition for some element that can act as a genus and that refers us to a hierarchy of concepts in which, hopefully, we can place the concept to be identified.

The approach we adopted is:
 1. Find the genus candidates (typically the most frequent terms in the definitions)
 2. Collect the wordnet synsets for:
    - each genus candidate
    - each hyponym of each genus candidate
    - each hypernym of each genus candidate
 3. Get the wordnet signature (definition + examples) of each collected synset
 4. Compare wordnet signature and the definitions through Lesk (overlap)
 5. Choose the synset that has the highest score

### Hanks
In this notebook we defined an algorithm that implements Patrick Hanks' [Valence Theory](http://clg.wlv.ac.uk/papers/hanks-2012a.pdf) relating to the construction of the meaning of complex expressions. According to Hanks, the essence of the meaning of a sentence lies in the verb and its valence. To understand the meaning of a sentence it is necessary to study the different semantic manifestations of the employees of a verb. A verb with valence "n" will have associated "n" slots, which include all the words that assume a certain syntactic role associated with the slot, so for a transitive verb with valence 2 we will all study fillers for the slots of "subject" and "direct object". The fillers are classified into generic semantic groups, given by the semantic types and the different combinations of these, for each slot, form the different meanings of the sentence in which the given verb is needed.

To accomplish this task we had to:
 - Choose a transitive verb (minimum valence = 2) &rarr; **EAT**
 - Retrieve from a corpus n (> 1000) instances in which it is used &rarr; I composed a corpus merging two pre-processed corpora: [corpora_1](https://sentence.yourdictionary.com/eat) and [corpora_2](https://wortschatz.uni-leipzig.de/en/download/English)
 - Perform parsing and disambiguation
 - Use wordnet supersenses on the arguments (subj and obj in the case of 2 arguments) of the chosen verb
 - Aggregate the results, compute the frequencies, print semantic clusters obtained
    - A semantic cluster is intended as a combination of semantic types (i.e. pairs of sem_types if valence = 2)

Finally we plotted the semantic clusters:
<p align="center">
  <img src="https://github.com/lorenzofavaro/nlp-text-mining/blob/main/docs/hanks.png"/>
</p>

### Segmentation
The fourth exercise saw the definition of a Document Segmentation algorithm. Document segmentation is one of the tasks that we have seen placed in the context of text mining. The goal is, starting from a text, to identify the points of change of speech, then, in the direction of these points, segment the document and organize its paragraphs taking into account the change of context. The heuristics used to correctly identify the cutting points for the purposes of this exercise was the following: "we are in correspondence with a change of speech when the lexicon used changes considerably from one paragraph to another in the document".

So we had to implement a simple algorithm of Text Segmentation. Basically, given a corpus that contains different themes, our algorithm must be able to find the cuts that best distinguish them.
To do that, we defined a score metric based on the Co Occurrence. Co-occurrence of a segment is measured as the sum of all the occurrences of the k most common words.

Then, we divided our algorithm in two phases:
 - Bruteforce: choose the cuts randomly maximizing the score
 - Refine: refine the previously calculated cuts by testing whether small shifts in the cuts improve the total score

For the bruteforce phase we established a max of 1000 iterations. It seemed like a good compromise between speed and effectiveness.
In conclusion, overall we got pretty good results.

As a dataset, we manually composed it by sequencing four wikipedia articles:
- Computer Science
- Football
- Cinematography
- Music

### Topic Modeling
In this exercise we had to implement an algorithm of Topic Modeling, another NLP task that we have addressed in the context of document semantics. With Topic Modeling we refer, in fact, to a specific NLP (Natural Language Processing) task that allows automatically to identify the main topics covered in a certain document corpus.

It is a task that follows an unsupervised approach, and as such does not require a tagged dataset. The dataset used in the correspondent notebook is a csv file that contains around 200k news headlines from the year 2012 to 2018 obtained from HuffPost obtained from Kaggle.

The two main methods for implementing Topic Modeling approaches are:
  - Latent Semantic Analysis (LSA)
  - Latent Dirichlet Allocation (LDA)

In the case study, an LDA approach was considered, made simpler thanks to the use of the Gensim library which, in fact, provides a ready-made implementation. LDA considers each document as a collection of topics under a certain proportion, and each topic is a set of keywords that, under a probability distribution, refer to a topic. LDA approaches the problem with a probabilistic approach. It consists of a generative model that receives as input the number of topics to search for.

The basic idea of ​​LDA is that documents can be seen as a mix of topics and there are some summary words of these topics that are returned in output to form the topic. Since the approach implemented by LDA is probabilistic, initially, given as input k topic and a collection of documents, LDA randomly assigns each word in the texts to any of the topics, in this way the probability distribution of the topic and that of the words in the topics. This means that each topic k has its own distribution over the entire vocabulary of terms, so if I know that a certain document talks about science, the probability of the word "argon" will be higher than the probability of "dog". Later these probabilities are recalculated, then the assignment is updated with a new probability distribution. After repeating these steps for a certain number of times we arrive at a situation in which the probabilities change little and such results the final probabilities of each topic.

The basic steps for topic modeling are:
 1. Preprocessing of data
 2. Creation of the dictionary and corpus necessary for topic modeling
 3. Topic construction

For this task, we managed to get good results and we used pyLDAvis library to generate an interactive panel that displays a representation in the topic space. We also used WordCloud library to visualize a representation of the top N words in each topic with the size of the words proportional to the weight.
<p align="center">
  <img src="https://github.com/lorenzofavaro/nlp-text-mining/blob/main/docs/topic_modeling.png"/>
</p>

### Guillotine
In this notebook is treated the game of the Guillotine which is broadcast every evening on Rai Uno on Italian television.
The competitor is presented with five pairs of words, of which he must choose one and of which one is the right clue and the other is an intruder; if he chooses the right one, the prize money remains intact, otherwise it is halved.

Once all five clues have been found, the competitor has a minute to think about what the word that binds to each of them may be. If he guesses the word he wins the prize pool, otherwise he wins nothing. The champion returns by right in the next episode.

So, we needed to implement an algorithm that, given 5 words, return the 6th. The sixth word has to be strongly related to the other five.

Our algorithm searches within a dataset (composed by us) all the sentences in which at least one word of the 5 dates appears. These sentences are saved and pre-processed by tokenizing them (and removing stop words). Then the word that occurs most in all the selected sentences is selected. We weight more the words that appears in sentences for different test words.

The dataset was composed by combining:
  - Titles of movies
  - Titles of italian songs
  - Common saying sentences

We tested our algorithm on 5 lists of test words and we obtained an accuracy of 3/5 = 60%. So, not bad for this task!

### False Friends
In this exercise we saw the definition of a false friends word detection algorithm. The general definition of a false friend is that of two almost homonymous words that share many characters in common but which differ greatly in meaning. An example of false friends are: `lost` & `most`. 
For this exercise we decided to work on a single language, English, using the lexical resource of WordNet to access the different meanings of the terms.
To understand whether or not two words are false friends, we looked at their [Edit Distance](https://en.wikipedia.org/wiki/Edit_distance), that is the minimum number of operations of insertion, removal, modification, to transform one string into another.
After that, to check if two words are False Friends we checked their Wu & Palmer similarity making sure it is less than a certain threshold.
This way, terms with *high lexical similarity* and *low semantic similarity* are good candidates to be False Friends.

Having already dealt with the SemCor corpus in the second part of the course, we decided to exploit this resource again, used in this context only to access a list of English language content words and to check for the presence of false friends. SemCor is an English language corpus consisting of 352 semantically annotated texts, with a total of 37176 sentences, which come from the Brown corpus. Currently, Semcor represents the largest hand annotated dataset with wordnet synsets.

We extracted 20 random sentences from the corpus and searched for false friends using 2 as edit distance threshold and 0.3 wu&palmer similarity. These parameters values were empirically set.

### FCA (Formal Concept Analysis)
The last exercise concerned the Formal Concept Analysis as a methodology for doing ontology learning.
Ontology learning represents the process of automatically creating an ontology to suffer from the texts and documents that are available. It can also be seen how the transition from an unstructured data, such as a text document, to a structured data in an automatic way. One of the different methodologies we have studied to do ontology learning is the Formal Concept Analysis. The latter represents a method that comes from the mathematical field, which allows you to automatically induce taxonomies starting from unstructured data. Fundamental to understand the FCA mechanism is the adjacency matrix, a matrix whose rows are represented by concepts and columns by features.
By analyzing a specific domain of interest, for example that of fruit, concepts and features are obtained, such as: “apple” and “has peel”. The characteristics are modeled as Boolean functions that identify the presence or absence of a property in each concept. From this matrix the formal context is constructed, a taxonomic representation of the concepts and features in the form of a graph, whose nodes are the latent concepts and the arcs are hyperonimic relations. The concepts of the matrix are merged into these new latent concepts and a taxonomic structure is derived.

## Contributing
Libraries used:
- [nltk](https://www.nltk.org/)
- [numpy](https://numpy.org/)
- [concepts](https://pypi.org/project/concepts/)
- [spacy](https://spacy.io/)
- [matplotlib](https://matplotlib.org/)
- [gensim](https://radimrehurek.com/gensim/)
- [pandas](https://pandas.pydata.org/)
- [pyLDAvis](https://pyldavis.readthedocs.io/en/latest/readme.html)

## Authors
- [Lorenzo Favaro](https://github.com/lorenzofavaro)
- [Andrea Senese](https://github.com/AndreaSenese)
