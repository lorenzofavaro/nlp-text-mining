# nlp-text-mining
This project has been created for the exam of Tecnologie del Linguaggio Naturale (NLP) of the Master's Degree course at the University of Turin.

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

Through the [cosine similarity](https://en.wikipedia.org/wiki/Cosine_similarity), we see that the similarity between the definitions is higher for "Person" that is our most generic concepts.
Through the most frequent words similarity (we compute mean score for the most frequent words), instead we got that the similarity is highest for the definition of "Brick", followed by "Person" that we got before.

As we can see in the notebook, both the scores method return low scores, therefore we can conclude that (as we expected) giving definitions to a concept is really a hard task!
Furthermore, we observe that (as we have seen during the lessons) the similarity is higher for the concrete concepts.

### Content2Form
In this exercise we had to find the correct synset starting from the definitions given by us (unito students) for 4 terms. To direct our search we used the principle of "genus".

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
In Lexical Analysis, Patrick Hanks offers a wide-ranging empirical investigation of word use and meaning in language.

We had to:
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