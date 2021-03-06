---
title: Intro to OCTIS
summary: Optimizing and Comparing Topic Models is Simple!
date: "2022-02-09"
author: Kyle McLester
format: hugo
bibliography: /Users/kmclester/Documents/weekender-blog/content/posts/octis/octis.bib
jupyter: python3
categories: ["Topic Modeling"]
freeze: auto
---



**[OCTIS GitHub](https://github.com/MIND-Lab/OCTIS)**

![OCTIS logo](/posts/octis/octis-logo.png)

OCTIS or "Comparing and Optimizing Topic Models is Simple" was created
by a team in Italy to assist with training, analyzing, and comparing
topic models (Terragni et al. 2021). They intend for OCTIS to be used by
researchers to aid in comparing models of interest; which are scored
using benchmark datasets and well-known evalutation metrics. Assuming
wide adoption, it would make comparing research teams results' much
easier as everyone would be using the same testing methodology.

## What is Topic Modeling?

If you have never heard of or worked with topic modeling, have no fear;
the concept is pretty intuitive. The general premise behind topic models
are that they are statistical methods that aim to extract the hidden
topics underlying a collection of documents. Said differently, they
allow us to get a "birds eye view" of a documents content. For example,
if we were performing topic modeling on the linked research paper, the
model may return "octis framework optimize evaluation hyperparameter".
As I mentioned, this gives us an idea of what the document is about
without having to read the entire paper.

## A Brief History

An early version of topic modeling was originally described in 1998
(Papadimitriou et al. 1998). Another was created by Thomas Hofmann in
1999 called *probabilistic latent semantic analysis* or "PLSA" (Hofmann
1999). Shortly thereafter, Latent Dirichlet allocation (LDA) was created
as a generalization of PLSA - this is generally accepted as the most
common topic model currently in use (Blei, Ng, and Jordan 2003).

LDA is an unsupervised approach that allows us to find latent or
"hidden" themes in a collection of documents. This process assumes each
document is a "bag of words" and that each word/document falls into one
of **k** topics. We're able to use the following probabilities:
**P(topic *t* \| document *d*)** and **P(word *w* \| topic *t*)** to
surmise the appropriate topic associated with each document. LDA also
uses Bayesian principles in the form of generating prior predictive
probabilities and applying Bayesian updating to iteratively calculate
posterior probabilities - once some threshold is met, the algorithm
returns the list of topics and associated words.

As I mentioned, LDA is one of the most popular options and is great for
what it is but it experiences one major pitfall -- context. LDA and
topic models as a whole began to fall out of favor with scientists
around 2012 due to the introduction of neural based approaches (Ruder
2021). These approaches include word embeddings (word2vec, glove, etc),
sequence-to-sequence models, attention (transform) models, and
pre-trained language models (BERT, Huggingface, etc). A nueral based
approach allows models to account for the context in which a word is
spoken/written - they can alter their vector representation based on
surrounding words, part of speech, etc.

Fortunately, topic models have recently recieved a neural "facelift" as
well. Several new methods such as Neural LDA, Embedded Topic Models
(ETM), and Contextualized Topic Models (CTM) have all been developed
within the last two to five years (Terragni et al. 2021). The team
behind OCTIS hope to combine both modern and traditional techniques into
a unified framework - such that researchers can compare the accuracy of
models against a standardized baseline. The intention is not to
determine which model is the all-around king of models, rather, it is to
test which model would be the best for a given scenario. Neural
approaches are all the rave right now but occassionally it is wise to
brush off the traditional methods as they may be faster, more efficient,
or achieve 95% of the desired response with minimal effort. It is also a
good idea to understand the old way of doing things so you can better
appreciate what newer methods have to offer.

### tl;dr

LDA is great; people think neural nets are cool; new isn't always better
but sometimes it is; OCTIS wants to make comparing them easier; models
are just tools

## Learning is a Process

OCTIS follows the general framework shown below. These pipelines are
setup for ease of use but also repeatability. Understanding this
workflow is key to being successful with OCTIS.

![OCTIS workflow](/posts/octis/octis-workflow.png)

### Pre-processing

First, we import a raw dataset and pass it to the pre-processing
pipeline. This pipeline can convert all text to lowercase, remove
punctuation, lemmatization, remove stop words (a, and, the, etc.),
remove unfrequent and most frequent words based on a given threshold,
and remove documents with less words than some threshold value. It is
important to remember that just because a utility is included, does
**not** mean you have to use it. Not every document requires the
aforementioned operations and it's up to the researchers discression to
determine the best course of action.

OCTIS includes several tests datasets:

| Dataset        | Domain            | \# Docs | Avg \# words in docs | \# Unique words |
|----------------|-------------------|---------|----------------------|-----------------|
| 20 News-groups | Forum posts       | 16309   | 48                   | 1612            |
| BBC News       | News              | 2225    | 150                  | 3106            |
| M10            | Scientific Papers | 8355    | 6                    | 1696            |
| DBLP           | Scientific Papers | 54595   | 5                    | 1513            |

### Topic Modeling

OCTIS includes a variety of off-the-shelf topic models, including both
classical and neural approaches. The included models are:

-   Latent Dirichlet Allocation (LDA)
-   Non-negative Matrix Factorization (NMF)
-   Latent Semantic Analysis (LSI)
-   Hierarchical Dirichlet Process (HDP)
-   Neural LDA
-   Product-of-Experts LDA (ProdLDA)
-   Embedded Topic Models (ETM)
-   Contexualized Topic Models (CTM)

Topic models are effectively a black-box. They take a dataset as input
and a set of hyperparameters and return the top-t topic words, the
document-topic distributions, and the topic-word distribution in a
specified format.

### Evaluation metrics

The evaluation metrics can be used in two ways:

-   An objective which is targeted by the Bayesian Optimization strategy
-   A metric in which to monitor the behavior of a topic model while the
    model is optimized on a different objective

Performance of the topic model can be evaluated using the following
metrics:

-   Topic coherence metrics
-   Topic significance metrics
-   Diversity metrics
-   Classification metrics

OCTIS provides 10 evaluation metrics directly from their web dashboard
and then 13 additional metrics can be accessed through the python
library.

### Hyper-parameter Optimization

If any of the available hyper-parameters are selected to be optimized
(for a given evaluation metric), the Bayesian Optimization engine
explores the search space to determine the optimal settings. These
optimal settings are based on a desired threshold set for the selected
evaluation metric. The team behind OCTIS realized that the performance
estimated by these metrics can be subject to noise, so they decided the
objective function should be computed as the median of n-model runs,
using the same hyper-paramter configuration.

In this case, Bayesian Optimization is a sequential model-based
optimization strategy for black-box functions (topic models). The
general idea is to use all of the model's configurations evaluated so
far to best approximate the value of the selected performance metric and
then select a new promising configuration to evaluate (Terragni et al.
2021).

"The approximation is provided by a probabilistic surrogate model, which
describes the prior belief over the objective function using the
observed configurations. The next configuration to evaluate is selected
through the optimization of an acquisition function, which leverages the
uncertainty in the posterior to guide the exploration." (Terragni et al.
2021)

![Comparison between OCTIS and well known topic modeling
libraries](/posts/octis/octis-optimization.png)

------------------------------------------------------------------------

## OCTIS in Action

### [LDA Example](https://colab.research.google.com/github/MIND-Lab/OCTIS/blob/master/examples/OCTIS_LDA_training_only.ipynb)

``` python
# Import dependencies
from octis.models.LDA import LDA
from octis.dataset.dataset import Dataset
from octis.evaluation_metrics.diversity_metrics import TopicDiversity
from octis.evaluation_metrics.coherence_metrics import Coherence
```

``` python
# Define dataset
dataset = Dataset()
dataset.fetch_dataset("20NewsGroup")
```

``` python
# Create Model
model = LDA(num_topics=20, alpha=0.1)
```

``` python
# Train the model using default partition choice
output = model.train_model(dataset)

print(*list(output.keys()), sep="\n") # Print the output identifiers
```

    topic-word-matrix
    topics
    topic-document-matrix
    test-topic-document-matrix

``` python
# Return the generated topics
[' '.join(x) for x in output['topics']]
```

    ['file image program version system include software color display widget',
     'key government system law chip people security make clipper enforcement',
     'armenian turkish israeli village attack kill russian genocide people government',
     'gun weapon firearm fire tank cop control bill military state',
     'test make time pray people money give talk car power',
     'post mail send message group list address email newsgroup people',
     'sell good price sale offer make problem include condition mail',
     'bike buy ride good engine cost make tire motorcycle work',
     'disease system year research program rate report service datum high',
     'encryption phone chip government clipper technology device agency information time',
     'computer include information ground modem electronic price network correction software',
     'game team win year play good player time make season',
     'window problem work application run print program font error time',
     'people make time man year child law give day call',
     'water launch time year back body side day start leave',
     'space book design good system point year time plane interested',
     'card drive driver scsi system problem work speed bus bit',
     'evidence claim true good time science make question people objective',
     'car drug dealer criminal state day problem illegal property find',
     'belief church atheist faith question thing point make human exist']

``` python
# Initialize performance metric
npmi = Coherence(texts=dataset.get_corpus(), topk=10, measure='c_npmi')

# Initialize performance metric
topic_diversity = TopicDiversity(topk=10)
```

``` python
# Retrieve metric scores
topic_diversity_score = topic_diversity.score(output)
print(f'Topic diversity: {str(topic_diversity_score)}')

npmi_score = npmi.score(output)
print(f'Coherence: {str(npmi_score)}')
```

    Topic diversity: 0.72

    Coherence: 0.06585295311548674

**NOTE**: For a neural-based example, see the [Google
Colab](https://colab.research.google.com/github/MIND-Lab/OCTIS/blob/master/examples/OCTIS_Optimizing_CTM.ipynb)
notebook provided by OCTIS.

------------------------------------------------------------------------

## Sources

<div id="refs" class="references csl-bib-body hanging-indent">

<div id="ref-blei2003latent" class="csl-entry">

Blei, David M, Andrew Y Ng, and Michael I Jordan. 2003. "Latent
Dirichlet Allocation." *Journal of Machine Learning Research* 3 (Jan):
993--1022.

</div>

<div id="ref-10.1145/312624.312649" class="csl-entry">

Hofmann, Thomas. 1999. "Probabilistic Latent Semantic Indexing." In
*Proceedings of the 22nd Annual International ACM SIGIR Conference on
Research and Development in Information Retrieval*, 50--57. SIGIR '99.
New York, NY, USA: Association for Computing Machinery.
<https://doi.org/10.1145/312624.312649>.

</div>

<div id="ref-10.1145/275487.275505" class="csl-entry">

Papadimitriou, Christos H., Hisao Tamaki, Prabhakar Raghavan, and
Santosh Vempala. 1998. "Latent Semantic Indexing: A Probabilistic
Analysis." In *Proceedings of the Seventeenth ACM SIGACT-SIGMOD-SIGART
Symposium on Principles of Database Systems*, 159--68. PODS '98. New
York, NY, USA: Association for Computing Machinery.
<https://doi.org/10.1145/275487.275505>.

</div>

<div id="ref-ruder2021a" class="csl-entry">

Ruder, Sebastian. 2021. "A Review of the Recent History of Natural
Language Processing." Sebastian Ruder, Sebastian Ruder.

</div>

<div id="ref-terragni-etal-2021-octis" class="csl-entry">

Terragni, Silvia, Elisabetta Fersini, Bruno Giovanni Galuzzi, Pietro
Tropeano, and Antonio Candelieri. 2021. "OCTIS: Comparing and Optimizing
Topic Models Is Simple!" In *Proceedings of the 16th Conference of the
European Chapter of the Association for Computational Linguistics:
System Demonstrations*, 263--70. Online: Association for Computational
Linguistics. <https://doi.org/10.18653/v1/2021.eacl-demos.31>.

</div>

</div>
