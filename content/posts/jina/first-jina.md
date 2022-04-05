---
author: Kyle McLester
date: 2022-04-05
format: hugo
title: Getting Started with Neural Search
categories:
    - Jina
    - Search
tags:
    - jina
    - neural search
    - docArray
    - flow
    - executor
    - document
summary: 🚧 UNDER CONSTRUCTION - Using Jina.ai to create a contexualized search system for YouTube subtitles and transcripts
---

## What is Neural Search?

In the greater context of search systems, "neural search" is the practice of retrieving information using a pre-trained neural network. This method of search differs from traditional implementations in that it does not require precise keyword matching or regular expressions. The use of pre-trained language models enables semantic similarity calculations to be performed that return relevant results -- even if their title, index, etc. do not explicitly match the input query.

For example, if we had a dataset consisting of the values listed below, a query of "sharing your story" might not return the desired results if we use keyword matching. However, if we create an embedding of our query and compute the semantic similarity to the text entries in our dataset, we will likely see the expected result (the last entry in dataset).

```json
    {
        "text": "Hello, everyone.",
        "start": 4.958,
        "duration": 1.417
    },
    {
        "text": "It is a tremendous pleasure to be here",
        "start": 6.708,
        "duration": 3.709
    },
    {
        "text": "to share with you my story.",
        "start": 10.458,
        "duration": 1.792
    },
```

## What (and Who) is Jina AI?

Jina AI is a group of brilliant developers, mathematicians, and scientists that came together to create a unified system that implements neural search. The key differentiator between Jina AI and competitors is that Jina allows us to search across various modalities -- or types of data. Jina is also "cloud-native," meaning it is designed from the ground up to be deployed to containers, kubernetes clusters, etc allowing for rapid scalability.

Okay, enough of the flattery and (non-sponsored) advertising, how do we use it?

## Setting Up Your Environment

Before we begin, it always good practice to create a new virtual environment. To do so, enter the following in your preferred terminal:

```bash
# Linux/MacOS/WSL2
python3 -m venv .venv

# Windows w/anaconda
conda create --name myenv
```

Now activate your new virtual environment:

```bash
# Linux/MacOS/WSL2
source .venv/bin/activate

# Windows w/anaconda
conda activate myenv
```

## Install Project Dependencies

```bash
python3 -m pip install pandas tqdm jina youtube_transcript_api
```

You may be wondering what this "YoutubeTranscriptApi" library is for -- for this project, the data we will be using are YouTube subtitles. This library enables us to retrieve subtitles and timestamps for YouTube videos, as long as you know the videos id.

## Getting Started

First, create a fresh jupyter notebook in your project directory. You can either do this in your IDE or by using the touch terminal command (ex: `touch jina-search.ipynb`). It's just creating a file...you know how to do this.

Before we import our main dependencies, let's enable jupyter widgets and hide the Deprecation Warnings that will surely appear later on.

```python
!jupyter nbextension enable --py widgetsnbextension

import warnings
warnings.filterwarnings('ignore', category=DeprecationWarning)
```

### Import Dependencies

```python
import os
import json
from shutil import rmtree

import numpy as np
import pandas as pd
import requests
from tqdm import tqdm

from youtube_transcript_api import YouTubeTranscriptApi
from jina import Document, DocumentArray, Flow, Executor, requests
```

### Create Cleanup Function

This is not necessary, however; if you run the project subsequent times you can run into issues with conflicting index files.

```python
def cleanup():
    rmtree("workspace", ignore_errors=True)
```

### Select YouTube Videos to Search

You can use any video on YouTube that has subtitles (created manually or auto-generated) -- you just need to know the video id. To find a videos' id, go to the video you want to search and check the url. YouTube urls tend to follow the format: https://www.youtube.com/watch?v={VIDEO_ID} or https://www.youtube.com/watch?v={VIDEO_ID}&ab_channel={CHANNEL_NAME}. Now create a list of your video id's:

```python
# You can use any video(s).
# If you would like to follow exactly as I have done, use the following
video_ids = ["n2aJYtuGu54", "XDoAglqd7ss"]
```

## Retrieving YouTube Transcripts

We will use the YouTubeTranscriptApi to return the transcripts for each video in our `video_ids` list. I create a dictionary of transcripts that can later be called using the video id key.

```python
# Dictionary of extracted transcripts, using video id as the key
transcripts = {}

for id in tqdm(video_ids):
  transcript_list = YouTubeTranscriptApi.list_transcripts(id)
  transcript = transcript_list.find_transcript(["en"])
  transcripts[id] = transcript.fetch()
```

### Create Dataframe

To temporarily store our data, let's create a pandas dataframe.

```python
df = pd.DataFrame(columns=["video_id","json_transcript","transcript"])

df["video_id"] = video_ids
df["json_transcript"] = [transcripts[key] for key in transcripts.keys()]

filtered_text = []
for script in df["json_transcript"]:
  lines = ''
  for line in script:
    try:
      lines+=line['text']+' '
    except:
      continue
  filtered_text.append(lines)

df['transcript'] = filtered_text
```

Now you should have a dataframe that looks something like this:

| video_id | json_transcript | transcript |
| -------- | --------------- | ---------- |
| n2aJYtuGu54 | [{'text': 'science is a profession', 'start': ... | science is a profession and like all professio... |
| XDoAglqd7ss | [{'text': 'if you've come this far in the cour... | if you've come this far in the course then you... |

### Create Links to Videos w/Timestamps

YouTube allows you to share videos that start at a specified time. The format for this is: https://youtu.be/{VIDEO_ID}?t={TIME_IN_SECONDS} . We are going to leverage this to return links that jump straight to the desired content:

```python
for idx, id in enumerate(df['video_id']):
  for x, video in enumerate(df.loc[idx]["json_transcript"]):
      try:
        if id == 'n2aJYtuGu54':
          video['link'] = f"https://youtu.be/n2aJYtuGu54?t={np.round(video['start']).astype(int)}"
        if id == 'XDoAglqd7ss':
          video['link'] = f"https://youtu.be/XDoAglqd7ss?t={np.round(video['start']).astype(int)}"
      except:
        continue
```

### Write to JSON

At this point, our dataset is complete. You are free to add more features or metadata but this is all I will be doing in this demo. To use this dataset in the future, let's go ahead and save it as a JSON file:

```python
output = {}
output["n2aJYtuGu54"] = df.loc[0]["json_transcript"]
output["XDoAglqd7ss"] = df.loc[1]["json_transcript"]

with open("transcripts.json", "w") as f:
  json.dump(output, f, indent=4)
```

## Finally Using Jina (and DocArray)

Before we begin searching our data, we have to load it into a format that Jina understands and recognizes. For this, we will use Jina's "DocArray" library. DocArray allows us to load multi-modal data into a specialized numpy-array of sorts. Since DocArray accepts various modalities, we have a choice to make. Either searching the JSON file we created in the above step, or creating smaller documents of pure text. The JSON approach allows the custom links to be returned, but the text approach provides more context for queries to match with. I'm sure there is a way to create a pipeline that does both of these...but I haven't gotten that far. Below are both approaches - choose whichever works best for your needs. For the sake of this demo, I will use the JSON method.

```python
# Method 1: Using JSON
docs = DocumentArray()

for i, transcript in enumerate(df["json_transcript"]):
  sub_doc = DocumentArray()
  for line in transcript:
    doctext = line["text"]
    doc = Document(text=doctext)
    doc.tags = line
    sub_doc.extend([doc])
  docs.tags = df.loc[i]["video_id"]
  docs.extend(sub_doc)
```

```python
# Method 2: Using Text
docs = DocumentArray()

for transcript in df["transcript"]:
  text_to_split = df.loc[0]["transcript"]
  text_to_split = text_to_split.split()
  # create documents with length of 40 words
  n = 40
  split_text = [' '.join(text_to_split[i:i+n]) for i in range(0, len(text_to_split), n)]
  for x in split_text:
    doctext = x
    doc = Document(text=doctext)
    docs.extend([doc])
```

## Create Jina Flow

Jina operates using 3 primary functions - documents (seen above), executors, and flows. You have already seen documents so we can skip those. Executors are individual functions performed on documents (text encoding, indexing, etc). Flows are the functions that orchestrate executors into a pipeline. The flow that we are going to create is shown below. It is a very basic flow but it will first encode our documents, then pass the encoded documents to the indexer.

```python
flow = (
    Flow()
    .add(
        name="text_encoder",
        uses="jinahub://CLIPTextEncoder",
        uses_with={"device":"cpu"},
        install_requirements=True
    )
    .add(
        name="text_indexer",
        uses="jinahub://SimpleIndexer"
    )
)
```

You can view what your flow looks like by simply printing it with `flow`:

![Flow](/posts/jina/flow.png)

## Running Jina Flow

Now we must run the flow we have created, passing in our `docs` and setting the request size. Jina recommends to have a low request size if you are able to - else it will take a very long time to index. I also run the cleanup function just in case we have erroneous files lingering behind.

```python
cleanup()

with flow:
  flow.index(
      inputs=docs,
      request_size=10
  )
```

This will take a few minutes the first time. Once indexing is complete, you can begin querying your documents.

## Query the Index

To start searching, we must first create a query document:

```python
query_doc = Document(text="It takes a long time to become a scientific professional")
```

Once the query document is created, we can pass it to our flow and retrieve a response:

```python
with flow:
  response = flow.search(inputs=query_doc, return_results=True)
```

To print all of the matches Jina has found, use `response[0]`:

![Response](/posts/jina/response.png)

Notice in our response object that the text **and** custom links are being returned. You could then use this to search for a specific location in a video, and use the link to jump to the correct point in time.

{{<collapse summary="You can view the complete Google Colab [here](https://drive.google.com/file/d/1exM354PBXj3ezw4aSl3UjN9XcrAXoCMB/view?usp=sharing), or expand this drop-down to view code">}}

```python
!python3 -m pip install youtube_transcript_api jina spacy -qq
!python3 -m spacy download en_core_web_md -qq

# Enable jupyter widgets so we can see images*
!jupyter nbextension enable --py widgetsnbextension

# Disable warnings
import warnings
warnings.filterwarnings('ignore', category=DeprecationWarning)

import os
import json
from shutil import rmtree

import numpy as np
import pandas as pd
import requests
from tqdm import tqdm

from youtube_transcript_api import YouTubeTranscriptApi
from jina import Document, DocumentArray, Flow, Executor, requests

def cleanup():
    rmtree("workspace", ignore_errors=True)

video_ids = ["n2aJYtuGu54", "XDoAglqd7ss"]

# Dictionary of extracted transcripts, using video id as the key
transcripts = {}

for id in tqdm(video_ids):
  transcript_list = YouTubeTranscriptApi.list_transcripts(id)
  transcript = transcript_list.find_transcript(["en"])
  transcripts[id] = transcript.fetch()

df = pd.DataFrame(columns=["video_id","json_transcript","transcript"])

df["video_id"] = video_ids
df["json_transcript"] = [transcripts[key] for key in transcripts.keys()]

filtered_text = []
for x in df["json_transcript"]:
  lines = ''
  for y in x:
    try:
      lines+=y['text']+' '
    except:
      continue
  filtered_text.append(lines) 

df['transcript'] = filtered_text

for idx, id in enumerate(df['video_id']):
  for x, video in enumerate(df.loc[idx]["json_transcript"]):
      try:
        if id == 'n2aJYtuGu54':
          video['link'] = f"https://youtu.be/n2aJYtuGu54?t={np.round(video['start']).astype(int)}"
        if id == 'XDoAglqd7ss':
          video['link'] = f"https://youtu.be/XDoAglqd7ss?t={np.round(video['start']).astype(int)}"
      except:
        continue

output = {}
output["n2aJYtuGu54"] = df.loc[0]["json_transcript"]
output["XDoAglqd7ss"] = df.loc[1]["json_transcript"]

with open("transcripts.json", "w") as f:
  json.dump(output, f, indent=4)

# docs = DocumentArray()

# for transcript in df["transcript"]:
#   text_to_split = df.loc[0]["transcript"]
#   text_to_split = text_to_split.split()
#   n = 40
#   split_text = [' '.join(text_to_split[i:i+n]) for i in range(0, len(text_to_split), n)]
#   for x in split_text:
#     doctext = x
#     doc = Document(text=doctext)
#     docs.extend([doc])

docs = DocumentArray()

for i, transcript in enumerate(df["json_transcript"]):
  sub_doc = DocumentArray()
  for line in transcript:
    doctext = line["text"]
    doc = Document(text=doctext)
    doc.tags = line
    sub_doc.extend([doc])
  docs.tags = df.loc[i]["video_id"]
  docs.extend(sub_doc)

flow = (
    Flow()
    .add(
        name="text_encoder",
        uses="jinahub://CLIPTextEncoder",
        uses_with={"device":"cpu"},
        install_requirements=True
    )
    .add(
        name="text_indexer",
        uses="jinahub://SimpleIndexer"
    )
)

cleanup()
with flow:
  flow.index(
      inputs=docs,
      request_size=10
  )

query_doc = Document(text="It takes a long time to become a scientific professional")

with flow:
  response = flow.search(inputs=query_doc, return_results=True)

response[0]
```

{{</collapse>}}

## Congratulations

If you have made it this far, congrats. If you have any questions or concerns please let us know. Also, share with anyone that might be interested! Thanks