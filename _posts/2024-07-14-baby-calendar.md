---
layout: post
title:  "Calendars for babies, or, project management with AIs"
---

This write up happens in two parts -- a basic use case write up, and then a meandering set of conclusions and ideas at the end.

## Basic instructions

### Use case
Any new parent will understand the sheer amount of time one spends learning and thinking about baby schedules. The corpus of concepts is enormous: "wake windows", "feed schedules", and sleep techniques quickly permeate everything you do. Imagine dreaming about sleep schedules...

Anyway, as our baby's gotten older, we spend less time diligently tracking everything he does in a vain attempt to understand him, and a bit more time steering him (and us) into target schedules. We found that the baby, when given structures he likes, responds well. 

In particular, I like to track these in Google Calendar, but I spend an inordinate amount of time making and managing calendar invites. 

### Prompting

I asked Claude to help with the following:

> You are an expert executive assistant engineer. You are going to help me manage my baby's schedule. To start with, can you help with the following: 1. Make a CSV of the schedule that I can upload to Google Sheets, 2. Given a Google sheet ID, write code that can create calendar invites from the schedule that is written in the format you select
Assume I'm using Google Calendar. I want the calendar events to recur daily for a week.
>

I followed up to add length as a parameter, add in a notes, and create another script to batch delete events.

## Baseline Experience
I'm starting to evaluate use cases on the same basis as other tech:
1. **Faster than manual**: Normally I'd include savings from future runs, but in this case I can say that Claude got the job done in parity or faster even on the first run.
2. **Silent errors**: Zero. Once again, I spent my time iterating, not debugging code. And I found that Claude did what I told it to -- I didn't find any secret errors that meant I had to rethink the entire approach.


## Meandering
This experience has prompted me to think a little about these types of exercises, as they're pretty common in the workplace. I think there's a lot of untapped potential in using these AIs as project management tools -- discovering, and in some cases, automating away risks. I think there's probably a few techniques on the horizon that can make this possible:

### 1. Asking for upfront risks and ambiguities [today]

Much like scoping any project, it's sensible to think through edge cases, sources of error etc in order to make this load bearing. I asked Claude __"what are the missing requirements and ambiguous statements in this request that might cause an issue?"__ and it gave me a suprisingly good list. Baking in this in as a first step in any request would be quite useful!

> The request lacks specificity in several key areas: the time range and recurrence pattern for events, handling of conflicts and time zone changes, error management, user feedback mechanisms, event update processes, privacy settings, and notifications. It doesn't address data validation, scalability for large numbers of events, long-term authentication management, or comprehensive logging and backup strategies. The absence of a defined testing approach is also notable. These ambiguities and missing requirements could lead to implementation challenges and potential reliability issues in real-world application of the script.

### 2. Learned tasks [soon]

Clearly Claude is good enough to operate my calendar, and it's getting the code correct enough on first try that I believe it'll do a good job. I'm going to use the word __agentic__ here just for SEO. 

That said, I'd love for it to be able to learn and save tasks for the future if it does a good job on the first ask. For example, after a successful run, I'd want it to ask "should I plan to do this in the future?" and be able to remember any modifications that it learned in the first go around. This behavior happens automatically at jobs (e.g. [see one, do one, teach one](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9258902/)) -- I think it starts to feel like magic though when you don't have to be very explicit about how to combine previously learned subtasks to get a bigger task done. 

I also think tasks will need to be learned at the user (or at least group) level rather than globally -- if there's one thing I've learned from corporations, it's that even simple processes are rarely consistent. For example, even if you scan inventory using the same tracking system in two different grocers, there's almost always a different set of scanners, and different process for logging and checking that inventory, and differently configured software storing things.

### 3. Automated change management

In a past life I spent a load of time managing operational changes -- for example, introducing a new software system for managing calendars. I'd always go in thinking it would be a simple port job to switch from one provider to the next, and obviously it never was. [Arguably, if it were, there would be little value in switching if service provided was a complete commodity and at price parity]. 

Most of the hard work was in figuring out what the fundamental differences in operations were so you could plan for and manage them -- these were almost always double-edged swords where the operational reason the new software would be painful for the organisation to use was also the business reason that you were switching to it. 

Ideally, you'd want to ask questions like "which tasks won't succeed", "simulate where a given set of test cases fail and suggest what to do about it" -- these typically involved painstaking test case development. Often, the work involved in setting up the test cases meant the tests got done at the very end, and resulted in timelines getting pushed back.

Building on #1 and #2 though, two sufficiently smart AIs, trained in how their companies operate, could run one-off simulations of the complex systems of those companies to find the failure points. Maybe they could write the integration themselves, but I'm more interested in the fact that they could also "manage the program", find the risks, and write the comms. 

In modern tech cos, there's often an army of smart folks deployed to do these functions who could immediately go help make changes somewhere else. While changing processes constantly is in itself a defect (how many times have you changed the to do list software you use, only to get the same amount done), but working groups even as few as ~10 people seem to be able to create enough friction and push back that it takes a lot of work to suss out and manage new changes. 