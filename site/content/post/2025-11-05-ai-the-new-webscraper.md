---
title: AI, the new webscraper
date: 2025-11-05T12:04:00.000-08:00
draft: false
description: How to use AI to do research at scale saving time and money.
image: /img/uploads/blog_back.webp
tags:
  - ai
  - llm
  - claude
  - gemini
  - google
---
Over the last few years there's been alot of examples of using Large Language Models (LLMs) for pulling web pages and then converting them to structured data. Usually with some mix of Python, requests, beautiful soup and then scrapy. More recently the use of AI and even image processing has taken strides. The state of the art in 2024 was to use JinaAI et al, so much so that the first comment on the youtube video is “Dammit stop telling everybody about Jina “!!! [source](https://www.youtube.com/watch?v=QxHE4af5BQE)



There is a lot of talk about AI web scrapers coming to destroy the internet with crazy traffic spikes and not being good netizens [ref](https://www.theregister.com/2025/08/29/ai_web_crawlers_are_destroying/). Then theres also the general enshifitication and growth of AI slop [ref](https://www.youtube.com/watch?v=ZRQEq4HChEY). But there is one counter argument. It turns out that in these training data are old websites that are now dead (you can of course browse wayback). But these don’t turn up in search. Turns out this even includes data and technical content. Here lies the potential.



Then, biggest problem here is that you need to know where to start and what you want.

What if there was a way to use natural language to find and parse 1000s of urls both dead and alive and then return structured data?

\## AI research and Search Tools

In his recent post Simon Willison goes through how to use the OpenAI research model [ref](https://til.simonwillison.net/llms/o4-mini-deep-research), and I’ve also played with Claude research, one thing to note is that you can parse 500+ web urls, but it’s hard to specify which ones get selected and crawled into model context. Enter Gemini and the Google gen ai sdk [ref](https://colab.research.google.com/github/google-gemini/cookbook/blob/main/quickstarts/Search_Grounding.ipynb), this lets you use the Grounding capability to launch searches.

Using this in Colab is quite nice as you can generate a Vertex AI API key and have it in the environment variables. The tie in is OK but not seamless. Too many variations on the same product... but it will do for now. 

So what do we need? 

Set the grounding tool as Google Search


```python
grounding_tool = types.Tool(

   google_search=types.GoogleSearch()

)
```




Then, set the params, note there are a few versions for different models so it’s worth checking those. I’d definitely recommend using a thinking model.


```python
config = types.GenerateContentConfig(

   tools=\[grounding_tool],

   temperature=0.5,

   thinking_config=types.ThinkingConfig(includeThoughts=True, thinkingBudget=10_000),

   seed=2048,

   candidate_count=1,

\#frequency_penalty=1.0,

)
```




From here we have to start on prompt design. Still somewhat of a dark art, interestingly. I put my prompt into Claude as part of some larger debugging and it gave it a very different structure. More on that later.


```python
response = client.models.generate_content(

   model="gemini-2.5-flash",

   contents="---start new session---\n\

\---init---\n\

\---you are an experienced senior broadband analyst with more than 10 years of knowledge working for goldmansachs---\n\

   I want to find out which towns in the following list \

   have fiber to the home. You should look for each one and try to find \

   city ordinances from the city or county session/meeting/q&a notes or rfps or franchise agreements (these will be .pdf files), \

   consulting pdf reports from ctc, magellan or another, \

   permits for public works, \

   press releases, social media/reddit, news articles, and check ISP websites/blogs. \

   Make sure you don't use any/n\

\-site:fcc.gov/n\

\-site:highspeedinternet.com/n\

\-site:inmyarea.com/n\

\-site:allconnect.com/n\

\-site:broadbandsearch.net/n\

\-site:https://bestneighborhood.org/n\

\-site:broadbandnow.com/n\

\-site:https://broadbandmap.com/n\

\-site:rsinc.com/n\

\-site:https://ispreports.org/n\

   websites. \n\

   download and review the websites, documents and/or articles from the search results for each city, state.\n\

   Ignore non fiber operators in a city that have generic fiber offerings nationally e.g. in Frequently asked questions.\n\

   summarize the content to build an informed summary.\n\

   write out the format as a csv. city,state,isp,100_word_summary,annouce_date,dd-mm-yyyy,link,url_type,source_url\

   its ok to have more than one row per city, state./n\

   san francisco, ca /n\

   san jose, ca /n\

   morgan hill, ca /n\

   sacramento, ca /n\

   watsonville, ca /n\

   santa cruz, ca/n\

   make sure you write a valid csv with quotes around content with commas , and urls./n\

   dont duplicate the 100_word_summary, it should be a summary for the specific result\

   ",

   config=config,

)


print(response.text)
```


So here are the first two gaps in the SDK/API, what you really want is structured csv return data and a way to exclude websites (I didn’t want to go down a JSON spec rabbit hole, which you can [ref](https://ai.google.dev/gemini-api/docs/structured-output)). The above worked OK but broke later when using batch mode. Specifically the hack to use Google Search exclude syntax “-site:”. 



## Scaling Using Batch

OK this works, but it’s going to be not so great to run this on the whole US or world... Enter the batch mode [ref](https://ai.google.dev/gemini-api/docs/batch-api). 



Step 1: find some data to loop over, luckily there is a list of cities with population on [public.opendatasoft.com](public.opendatasoft.com)



Let’s download the data, get it into a Dataframe and setup the process.


```bash
!wget "https://public.opendatasoft.com/api/explore/v2.1/catalog/datasets/geonames-all-cities-with-a-population-1000/exports/parquet/?lang=en&timezone=America%2FLos_Angeles"
```

```python
import pandas as pd

df = pd.read_parquet('/content/index.html?lang=en&timezone=America%2FLos_Angeles')

city_dfs = df\[(df['country_code'] == 'US') & (df\['population'] > 10_000)].sort_values(by=\['admin1_code', 'name'], ascending=True)

import json

from google import genai

from google.genai import types

client = genai.Client()

\# List of domains to exclude

excluded_domains = [

   "fcc.gov",

   "highspeedinternet.com",

   "inmyarea.com",

   "allconnect.com",

   "broadbandsearch.net",

   "bestneighborhood.org",

   "broadbandnow.com",

   "broadbandmap.com",

   "rsinc.com",

   "ispreports.org",

   "broadbandsearch.com",

   "reviews.org"

]

\# Create the exclusion list as a formatted string

exclusion_list = "\n".join(\[f"- {domain}" for domain in excluded_domains])

\# Define the generation configuration for the individual requests

request_generation_config = {

   'temperature': 0.5,

   'candidate_count': 3,

   'thinking_config': {'include_thoughts': True, 'thinking_budget': 12000},

   'seed': 2048,

}

\# Define the tools for the individual requests

request_tools = \[{'google_search': {}}]

\# Create a sample JSONL file with the improved promptfor s in states:

s_c_df = city_dfs

\# Split into groups of 5 rows

for i in range(0, len(s_c_df), 5):

   group_df = s_c_df\[i:i+5]

\# You can now process each group_df here

   print(f"Processing group {i//5 + 1} for state {states}:")

   city_names = \[f"{row['name']}, {row\['admin1\_code']}" for \_, row in group_df.iterrows()]

\# The improved prompt with proper formatting and explicit instructions

   user_prompt = f"""---start new session---

\---init---

\---you are an experienced senior broadband analyst with more than 10 years of knowledge working for Goldman Sachs---

   I want to find out which towns in the following list have fiber to the home (FTTH).

   For each city, you should search for and analyze:

   1. City ordinances from city or county session/meeting/Q&A notes

   2. RFPs or franchise agreements (typically .pdf files)

   3. Consulting PDF reports from CTC, Magellan, or other consultants

   4. Permits for public works

   5. Press releases from local government or ISPs

   6. Social media posts (especially Reddit discussions)

   7. Local news articles

   8. ISP websites and blogs announcing service

   IMPORTANT EXCLUSION RULES:

   When you receive search results, you MUST ignore and not use any information from these domains:

   {exclusion_list}

   These are aggregator sites that don't provide original source information. If you encounter results from these domains, skip them and look for other sources.

   SEARCH STRATEGY:

   For each city, use search queries like:

\- "\[city name] fiber to the home announcement"

\- "\[city name] FTTH franchise agreement pdf"

\- "\[city name] fiber optic permits"

\- "\[city name] city council fiber broadband"

\- "\[city name] \[ISP name] fiber service"

   ANALYSIS INSTRUCTIONS:

   1. Download and thoroughly review the websites, documents and articles from the search results

   2. Focus on LOCAL sources like city websites, local news, and ISP announcements

   3. Ignore generic national fiber offerings that appear in FAQ sections

   4. Only include fiber providers that specifically serve that city

   5. Build an informed summary based on the actual content reviewed

   OUTPUT FORMAT:

   Create a CSV with the following columns:

   city,state,isp,100_word_summary,announce_date,link,url_type,source_url

   Format requirements:

\- Use quotes around any field containing commas or URLs

\- Date format: DD-MM-YYYY

\- Multiple rows per city are acceptable if multiple ISPs or announcements exist

\- Each 100_word_summary should be unique and specific to that result

\- url_type should indicate: news_article, city_document, isp_announcement, etc.

   CITIES TO RESEARCH:

   {city_names}

   Remember: Focus on finding PRIMARY SOURCES and LOCAL INFORMATION. Avoid using any results from the excluded domains listed above."""

   with open("my-batch-requests.jsonl", "a") as f:

\    requests = [

\    {"key": f"fiber-request-{i}",

\    "request": {

\    "contents": \[{"parts": [{"text": user_prompt}]}],

\    "generation_config": request_generation_config,

\    "tools": request_tools

\    }}

\    ]

\    for req in requests:

\    f.write(json.dumps(req) + "\n")

print("Created my-batch-requests.jsonl with improved prompts")
```

You’ll see here how the prompt changed. I found it quite interesting that the results differed and I needed to create another way to exclude the other sites. This seems to work. The iteration time for small batches was in the order of minutes and it was nice to try it out before launching 1000+.

Checking batch jobs and downloading data was all pretty straight forward.

But the responses did vary quite a lot. Parsing out the response took some key wrangling but eventually it made some human readable csv’s. 

## How much and Why?

All up this cost about $30 from PoC to then run the whole list of US cities.

This saved me a few hours of using regular search and the results were promising. One thing to note is as I mentioned some of the urls were dead. But after looking them up in Wayback there is some good content there. 

Overall, it’s a useful way to get a bunch of data. Is it clean, no but hopefully future features will improve that. I did put the dirty csv into Gemini and it managed to improve things abit but there are still challenges making it machine readable in a consistent column structure. 

Give it a try for yourself here - [BroadbandResearch.ipynb](https://colab.research.google.com/drive/1vhqY9dMvp_CjakAZfL_6AMe20FQLdT7j#scrollTo=-FZM9mOUZYNM)
