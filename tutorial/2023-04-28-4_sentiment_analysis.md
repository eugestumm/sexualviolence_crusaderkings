---
layout: default
title: Using VADER to generate a sentiment analysis of the reddit posts, titles, and comments
nav_order: 4
---

# Using VADER to generate a sentiment analysis of the reddit posts, titles, and comments

## Introduction

Now that we are already familiar with the data, we can start to analyze it. In this notebook, we will use the VADER (Valence Aware Dictionary and sEntiment Reasoner) library to analyze the sentiment of the reddit posts, titles, and comments. VADER is a lexicon and rule-based sentiment analysis tool that is specifically attuned to sentiments expressed in social media. It is a good choice for this project because it is designed to analyze sentiments in social media posts, which is exactly what we are doing.

## Importing the Libraries

We will start by importing the libraries that we will use in this notebook. We will use the csv file for manipulating the data and the vaderSentiment library for analyzing the sentiment of the reddit posts, titles, and comments.

## Importing the Data

We will start by importing the data that we cleaned in the previous Python Notebook.

## Analyzing the Sentiment of the Reddit posts, titles, and comments 

We will start by analyzing the sentiment of the reddit posts. We will use the vaderSentiment library to analyze the sentiment of the reddit posts. We will use the polarity_scores() function to analyze the sentiment of the reddit posts. The polarity_scores() function returns a dictionary of scores of the sentiment of the reddit posts, titles, and comments. This function will return a neg (negative), a neu (neutral), and a pos (positive) score. They represent the percentage of the post that correspond to each sentiment. The compound score is a metric that calculates the sum of all the lexicon ratings which have been normalized between -1 (most extreme negative) and +1 (most extreme positive). We will add the scores to the dataframe.

After this process is done for each of the reddit posts, titles, and comments, we will save the dataframe to a csv file. One for the comments and other for the posts and titles.


```python
import csv
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer

# Define the file names
posts_file = 'posts.csv'
comments_file = 'comments.csv'
posts_sentiment_file = 'posts_sentiment.csv'
comments_sentiment_file = 'comments_sentiment.csv'

# Create an instance of SentimentIntensityAnalyzer
analyzer = SentimentIntensityAnalyzer()

# Open the input files and the output files
with open(posts_file, 'r', newline='', encoding='utf-8') as f_posts, \
     open(comments_file, 'r', newline='', encoding='utf-8') as f_comments, \
     open(posts_sentiment_file, 'w', newline='', encoding='utf-8') as f_posts_sentiment, \
     open(comments_sentiment_file, 'w', newline='', encoding='utf-8') as f_comments_sentiment:

    # Create the CSV reader and writer objects
    posts_reader = csv.reader(f_posts)
    comments_reader = csv.reader(f_comments)
    posts_sentiment_writer = csv.writer(f_posts_sentiment)
    comments_sentiment_writer = csv.writer(f_comments_sentiment)

    # Write the header row for the output files
    posts_sentiment_writer.writerow(['post_id', 'title', 'body', 'score', 'ups', 'downs', 'title_compound', 'title_pos', 'title_neu', 'title_neg', 'body_compound', 'body_pos', 'body_neu', 'body_neg'])
    comments_sentiment_writer.writerow(['post_id', 'comment_id', 'comment', 'score', 'ups', 'downs', 'compound', 'pos', 'neu', 'neg'])

    # Skip the header row of the input files
    next(posts_reader)
    next(comments_reader)

    # Loop over the posts and analyze their sentiment
    for post_row in posts_reader:
        post_id, title, body, score, ups, downs = post_row
        title_scores = analyzer.polarity_scores(title)
        title_compound = title_scores['compound']
        title_pos = title_scores['pos']
        title_neu = title_scores['neu']
        title_neg = title_scores['neg']
        body_scores = analyzer.polarity_scores(body)
        body_compound = body_scores['compound']
        body_pos = body_scores['pos']
        body_neu = body_scores['neu']
        body_neg = body_scores['neg']
        posts_sentiment_writer.writerow([post_id, title, body, score, ups, downs, title_compound, title_pos, title_neu, title_neg, body_compound, body_pos, body_neu, body_neg])

    # Loop over the comments and analyze their sentiment
    for comment_row in comments_reader:
        post_id, comment_id, comment, score, ups, downs = comment_row
        scores = analyzer.polarity_scores(comment)
        compound = scores['compound']
        pos = scores['pos']
        neu = scores['neu']
        neg = scores['neg']
        comments_sentiment_writer.writerow([post_id, comment_id, comment, score, ups, downs, compound, pos, neu, neg])

```
