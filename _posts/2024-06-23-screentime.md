---
layout: post
title:  "Tracking screen time goals with Claude"
---

After seeing rave reviews for Claude 3.5 Sonnet and the artifacts UI, I wanted to give it a try myself. 

**TL;DR: Outputs are reliable, right, fast, and auditable. It's not a good designer yet, but it helped me make something I wanted**

## Prior art ##
I've been using AI assistance to program side projects for about 2 years (since Copilot came out). 
* I found that Copilot was a good line-level autocomplete, but not a very good problem solver. I'd try to write out problem statements in the comments but I often found myself rewriting things. Mostly, it helped me stay somewhat idiomatic in whatever language I was working in.
* When GPT-4 came out, I was able to start getting chunks of code written, and edited, that would often be close to working. I really struggled with memory though -- often responses would have more and more hallucinations as I asked for revisions. Unfortunately, I was asking for a lot of revisions.

## What I wanted ##
I've been wanting to reduce my screen time because my 9-month-old has already realised iPhones are the world's best toy (re: drug). Apple's Screen Time feature is nifty, but I struggle with how it categorises usage - I don't really count screen time for Apple Maps when I'm traveling somewhere, but I don't want to accidently spend a lot of time on social media. And because I moonlight as a product manager, I love a good metric.

So I decided to make a screen time dashboard to track my usage of only apps I deemed "unnecessary" and bake in some goal tracking that Screentime won't support. I felt this was a good project for Claude. I did some research and found that Apple syncs data to Macs in the knowledgeC.db. I decided to used Streamlit for the interface, and query data from knowledgeC.db. I chose to set up a virtualenv in Python to keep things tidy. I chose all of these ahead of time, so I don't know if Claude would have helped
make a good choice here.

I found an [article](https://felixkohlhas.com/projects/screentime/) and copied out the query as source material (Thanks Felix!). I then asked Claude help. In ~2 hours, I made [this dashboard](https://www.loom.com/share/9e10d9107c204419be00d6c90b217060) and produced this [repo](https://github.com/hareeshganesan/screentime-goal-tracker). Here's a Claude-generated summary of what it does:
- Daily screen time and pickup trends visualization
- Unnecessary screen time tracking
- Goal achievement display
- Detailed app usage breakdown
- Suggestions for alternative activities when screen time goals are exceeded

[[https://cdn.loom.com/sessions/thumbnails/9979506e076a44f8ad371f89e6e160de-with-play.gif|Demo]]

## What I learned ##
Claude 3.5 is a clear step up from anything I've used to date. I used Claude exclusively to get what I wanted - I wasn't trying to trip it up so take my postive experiences with a grain of salt. Here's what I liked:
* **Fewer run-time failure change the experience**: Run-time failures are an exception not a rule. Only 17% of the generations (4 out of 23) ended in errors. In contrast, I'd guesstimate that 40-50% of what I used to get from an LLM I'd need to fix or pass back to the model for error handling. 
* **Feels like a (mediocre) product engineer**: I was very rarely let down by the metrics and visualisations it produced. It felt like working with a competent analyst. I didn't have to ask twice for a given metric, which meant I spent most of the time iterating to the dashboard and metrics __that I found valuable__ instead of spelling things out in more and more detail for a model.
* **Requirements, not code**: I didn't feel the need to rewrite the code in order to get functionality I wanted. In the end, I write ~550 words of requirements through back and forth discussion, with lots of copying and pasting. Claude produced ~310 lines of code that made those requirements real.
* **Better long-term memory**: I'm guessing, but Artifacts helped me figure out where the code was, what the context was, and what the latest changes were. I imagine that helped the model avoid hallucinations far down the context chain too. 

Claude did get tripped up in a few areas:
* **Unterminated lines when the chat gets long**: The generations would just fail a few times near the end of the chat, resulting in incomplete lines. I solved this by starting a new chat with the current artifacts.
* **Very bad designer**: I asked Claude to make the whole thing look like Apple. It put gray text on a white background. That's a shame. Maybe one day that won't be the case, I'm sure there's a way to train it to be sensible.

I'll avoid going into prediction-land, but suffice to say I found it much easier to homebrew software that I really wanted. Overall, a fun side project, and a dashboard that I'm actually checking (for now).

## Appendix 1: The prompts I used

The messages I used
1. I'd like to get access to a spreadsheet of Apple screentime data every week, how do I do that?
2. What is streamlit?
3. Please make a streamlit app that allows me to provide times as input, and visualise a graph of sums of screentimes. To access screentimes, you can use the python script attached.
4. Here's some sample data btw.
5. Can you do the following:
   1. I want to see everything in hours instead of seconds
   2. I want to see data labels on all charts
   3. I want to see a sorted table of top apps
   4. I want to see a chart of total screen time over the last 4 weeks
6. Can you please write the code so I don't have to use sample data and I use the script I had?
7. Please update screentime.py to accept filters on devices. And please add a device filter to the screentime streamlit app.
8. Perfect. Okay let's work on the views a bit. My goal is to reduce screen time on my iPhone down to an hour per day. To do this, here's what I want to view:
* big text at the top that sets the goals: 1. Unnecessary screen time < 1h 2. Pickups < 50
* a big number that is the "Unnecessary screen time" for the day. if it is <45 it should be green. if it is between 45-60 it should be yellow, if it is more than 60 it should be red. 1. unnecessary screen time is screen time on the following apps (search within the app string to match): ios, Netflix, Tweetie2) 2. if red show some suggestions for what else to do. you can recommend these. but include: 1. Write in your notebook 2. Take a walk 3. Message a friend 4. Go to the gym 5. Play with the baby
* a big number that is "Number of accesses": that should be green if less than 50, yellow for 50-100, and red if 100+. 1. you can calculate pickups by the number of times that the apps above have been accessed (e.g. the rows in the db)
* a daily screen time trend for the last week with data labels
* total screen time trend for the last week with data labels
9. Perfect. A few notes:
   1. For the last dashboard, can you switch to total screen time trend for the past 4 weeks
   2. Can you make the first and second (screen time and accesses) numbers side by side instead?
   3. Could you add one more chart in. I would like it to show each day in the last week as a box, and have that day be green if I met both goals, and red if I did not
10. Can you also show a table at the bottom of screentimes for all apps over the last week. I'd like this to be a paginated and sortable table.
11. Can you make the daily goal achievement more like commit graphs (with more transparency with lower screentime) instead?
12. Instead of total cumulative screen time on 3, can I do daily screen time. And can I also see one for pickups?
13. The database query in screentime.py needs to respect the start and end times in the app. Please make this happen.
14. Can you please add the most recent date of data to the top of the app?

The first file I attached:
```

import sqlite3
from os.path import expanduser

def query_database():
    # Connect to the SQLite database
    knowledge_db = expanduser("~/Library/Application Support/Knowledge/knowledgeC.db")
    with sqlite3.connect(knowledge_db) as con:
        cur = con.cursor()
        
        # Execute the SQL query to fetch data
        # Modified from https://rud.is/b/2019/10/28/spelunking-macos-screentime-app-usage-with-r/
        query = """
        SELECT
            ZOBJECT.ZVALUESTRING AS "app", 
            (ZOBJECT.ZENDDATE - ZOBJECT.ZSTARTDATE) AS "usage",
            (ZOBJECT.ZSTARTDATE + 978307200) as "start_time", 
            (ZOBJECT.ZENDDATE + 978307200) as "end_time",
            (ZOBJECT.ZCREATIONDATE + 978307200) as "created_at", 
            ZOBJECT.ZSECONDSFROMGMT AS "tz",
            ZSOURCE.ZDEVICEID AS "device_id",
            ZMODEL AS "device_model"
        FROM
            ZOBJECT 
            LEFT JOIN
            ZSTRUCTUREDMETADATA 
            ON ZOBJECT.ZSTRUCTUREDMETADATA = ZSTRUCTUREDMETADATA.Z_PK 
            LEFT JOIN
            ZSOURCE 
            ON ZOBJECT.ZSOURCE = ZSOURCE.Z_PK 
            LEFT JOIN
            ZSYNCPEER
            ON ZSOURCE.ZDEVICEID = ZSYNCPEER.ZDEVICEID
        WHERE
            ZSTREAMNAME = "/app/usage"
        ORDER BY
            ZSTARTDATE DESC
        """
        cur.execute(query)
        
        # Fetch all rows from the result set
        return cur.fetchall()


response = query_database()

# The tuple contains the following fields:

# Field	Note	Example
# App Name	Bundle identifier1	'org.chromium.Chromium', 'md.obsidian'
# Usage	App usage in seconds	
# Start Time	Unix timestamp (UTC)	
# End Time	Unix timestamp (UTC)	
# Creation Time	Unix timestamp (UTC)	
# Time Zone	Offset from UTC in seconds	3600, 7200
# Device ID	Device UUID	'9B392A24-F0A5-5BF9-AC20-0CC38404D6CE', 'B9C0C352-91F5-5940-AC26-64BE69B4EC75'
# Device Model	Device Model	'iPhone14,2', 'iPad8,5'
```


