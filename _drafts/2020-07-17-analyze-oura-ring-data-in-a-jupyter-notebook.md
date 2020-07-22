---
layout: post
title: Are you ready to analyze your Oura data in a Jupyter Notebook?
date: 2020-07-17 16:03:00 -0400
description: In case the Oura app does not give you the insights you are looking for, Jupyter will.
img: oura-screenshot.png
img-caption: Screenshot of my Oura app
tags: [Oura, Sleep, Jupyter]
categories: [Sleep]
---
The Oura app is really well-built. It provides a great way to see how I slept last night or even last week. And to discover trends in all measured variables over even longer periods of time. It also allows you to add tags and comments to a day, so that, looking back, you can see what might have caused a great or poor night's sleep. What it doesn't let you do is deep-dive into those tags. So I've stopped using them, in favor of the following workflow:

## 1. I wear my Oura ring every night to measure my sleep and readiness
That's what it's designed for, and it does a great job at these things. I guess you can guess what "measuring sleep" means, but in case you're wondering about "readiness", [Oura explain it in detail on their blog](https://ouraring.com/readiness-score). On top of that I have been providing [the UCSF TemPredict Study](https://ouraring.com/ucsf-tempredict-study) with my Oura data and a daily survey to help them find correlations between sleeping patterns and COVID-19. But that is where my interaction with the Oura app ends.

## 2. On waking up, I log my sleep in [the open-source Personal Log Android app](https://f-droid.org/packages/com.tiwa.pl/)
So instead adding tags in the Oura app, which does not let me export them, I have this template greeting me every morning when I boot my phone:

```
# sleep
airbnb
keto
naked
reading
```

These four tags apply to almost every day, so it's faster to remove them when they don't than type them out every day. I then add whatever applied to last night's sleep:

```
# sleep
airbnb
keto
naked
reading
no coffee
fish
cheese
```

## 3. And that's it, there's nothing more to it. But it gives me a lot more insights from my input than the Oura app
Because now I can get both those tags and my Oura measurements into a single Jupyter Notebook through a combination of Oura's API and a little script I wrote.

### Getting data out of the Oura API
[The API](https://cloud.ouraring.com/docs/) is not that complicated when using a personal access token, so I use Python's `requests` library to retrieve the data. I then store it in a Pickle file, so I don't have to request it again today.

![The Oura API in a Jupyter Notebook]({{ site.images }}/oura-api-jupyter-notebook.png)

### Getting data out of your Personal Log
The Personal Log app saves logs as individual TXT files with the timestamp as filename.

![Personal Log TXT files in Finder]({{ site.images }}/personal-log-txt-files.png)

I have written `personal-log.py` to convert those TXT files into a single Excel sheet, mostly because I wanted to manually copy my old tags from the Oura app into Personal Log. This script let's you easily go from TXT files to an Excel file and back:

![Personal Log TXT files converted to an Excel file]({{ site.images }}/personal-log-to-excel.png)

### Combining it all into insights
The `personal-log-vs-oura-sleep.ipynb` Jupyter Notebook is where the magic happens.
