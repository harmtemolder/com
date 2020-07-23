---
layout: post
title: Are you ready to analyze your Oura data in a Jupyter Notebook?
date: 2020-07-23 09:46:00 -0400
description: In case the Oura app does not give you the insights you are looking for, Jupyter will.
img: oura-screenshot.png
img-caption: Screenshot of my Oura app
tags: [Oura, Sleep, Jupyter]
categories: [Sleep]
---
Don't get me wrong, the Oura app is really well-built. It provides a great way to see how you slept last night or even last week. And to discover trends in all measured variables over even longer periods of time. It also allows you to add tags and comments to a day, so that, looking back, you can see what might have caused a great or poor night's sleep. What it doesn't let you do is deep-dive into those tags. So I've stopped using them, in favor of the following workflow:

## 1. I wear my Oura ring every night to measure my sleep and readiness

That's what it's designed for, and it does a great job at these things. I guess you can guess what "measuring sleep" means, but in case you're wondering about "readiness", [Oura explain it in detail on their blog](https://ouraring.com/readiness-score). On top of that, I have been providing [the UCSF TemPredict Study](https://ouraring.com/ucsf-tempredict-study) with my Oura data and a daily 1-minute survey to help them find correlations between sleeping patterns and COVID-19. But that is where my interaction with the Oura app ends.

## 2. On waking up, I log my sleep in [the open-source Personal Log Android app](https://f-droid.org/packages/com.tiwa.pl/)

So instead of adding tags in the Oura app—which does not let me export them—I have this template greeting me every morning when I boot my phone:

```
# sleep
airbnb
keto
naked
reading
```

These four tags apply to almost every day, so it's faster to remove them when they don't than type them out when they do. I then add whatever applied to last night's sleep, e.g.:

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

## And that's it, there's nothing more to it. But this gives me a lot more insights from my input than the Oura app

Because now I can get both those tags and my Oura measurements into a single Jupyter Notebook through a combination of Oura's API and a little script I wrote. Let's dive in:

### Clone my repository

Don't expect a polished product, but it gets the job done:

```shell
git clone https://github.com/harmtemolder/analyze-oura.git
```

### Getting data out of the Oura API

![The Oura API in a Jupyter Notebook]({{ site.images }}/oura-api-jupyter-notebook.png)

[The API](https://cloud.ouraring.com/docs/) is not that complicated when using a personal access token, so I use Python's `requests` library to retrieve the data. I then store it in a Pickle file, so I don't have to bother the API again until I want fresh data:

1. Set up a conda environment according to this project's `environment.yml` and activate it:
  ```shell
  conda env create -f environment.yml -p env
  conda activate ./env
  ```
1. Start Jupyter with:
  ```shell
  jupyter notebook
  ```
1. Open `oura-api.ipynb`.
1. Read through each cell and run them. Some pointers:
  * On any future runs you only have to run the "Load Data" part, since your personal access token will have been saved in `config.yml`.
  * You can change the `start_date` and `end_date` variables to your liking.

### Getting data out of your Personal Log

The Personal Log app saves logs as individual TXT files with the timestamp as filename.

![Personal Log TXT files in Finder]({{ site.images }}/personal-log-txt-files.png)

I have written `personal-log.py` to convert those TXT files into a single Excel sheet. Having an Excel sheet means I was able to manually copy my old tags from the Oura app into Personal Log. This script let's you easily go from TXT files to an Excel file and back:

![Personal Log TXT files converted to an Excel file]({{ site.images }}/personal-log-to-excel.png)

1. Make sure your conda environment is still active.
1. Run:
  ```shell
  python personal-log.py
  ```
1. Follow the prompts. This will write an Excel file next to your Personal Log TXT files.

### Combining it all into insights

The `personal-log-vs-oura-sleep.ipynb` Jupyter Notebook is where the magic happens. Make sure your conda environment is still active and start Jupyter Notebook again. Then [Shift + Enter] through the first couple of cells—to load all data—until you reach "3. Analyze Tags".

I've picked a handful of Oura measurements to be considered as dependent (or "y") variables, but feel free to edit this list. Then run the cell.
* `score`
* `rem_pct`
* `deep_pct`
* `restless`
* `hr_lowest`
* `hr_average`
* `rmssd`
* `breath_average`

Then follow two function definitions to easily draw boxplots and run [*t*-tests](https://en.wikipedia.org/wiki/Student%27s_t-test) for a bunch of variables at a time. Run these cells to have them available.

#### `t_test`

Running the `t_test` function without any parameters will combine all tags from your personal logs with the previously defined `y_vars`. It then runs a *t*-test for every combination, testing the hypothesis that the mean from all cases with a tag significantly differ from all cases without that tag. I consider a *p* < 0.05 significant and mark those combinations with asterisks. For example:

```
  sleeping mask     on score         :  0.161 (p = 0.872)  
  sleeping mask     on rem_pct       : -1.821 (p = 0.075)  
  sleeping mask     on deep_pct      :  0.650 (p = 0.518)  
  sleeping mask     on restless      : -1.629 (p = 0.110)  
**sleeping mask     on hr_lowest     : -2.729 (p = 0.010)**
**sleeping mask     on hr_average    : -2.488 (p = 0.017)**
**sleeping mask     on rmssd         :  2.693 (p = 0.010)**
  sleeping mask     on breath_average: -1.727 (p = 0.090)  
```

So on the nights I wore a sleeping mask my lowest and average heart rate were significantly lower compared to the nights I did not wear one, and my HRV was higher. Conclusion? Wear a sleeping mask more often.

You're able to slice the data so that you test a smaller period of time. For example, I only logged `coffee` when I lived in Llanquihue:

```
t_test(df=data.loc[data['llanquihue'] == 1], x_vars=['coffee'])
```

And I started logging `analog` on the 14th of June:

```
t_test(df=data['2020-06-14':], x_vars=['analog'])
```

#### `draw_boxplots`

This function let's you visualize a combination of x and y variables—defaulting to all tags and the previously defined `y_vars`—in boxplots, with or without an overlaid scatter plot (`swarm=True`). X variables are laid out horizontally, y variables vertically.

A word of warning: Running it without any parameters will draw a lot of plots:

![Boxplots of my Personal Log tags and Oura data]({{ site.images }}/personal-log-boxplots.png)

## Conclusion

Now that you've seen everything, it's up to you to optimize your sleep based on your insights. If you're curious to my insights, feel free to ask me through whichever medium you prefer.
