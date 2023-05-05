---
layout: default
title: (Optional) Extra resource! Fast visualization of the data
nav_order: 6
---

# (Optional) Extra resource! Fast visualization of the data

## Introduction to Pandas Profiling

Pandas Profiling is a python library that creates HTML webpages with data visualizations from pandas data structures. Although it has several limitations, it is an alternatives for fast visualization of data sets and data exploration. When analyzing a database, one can rely on it to have a quick exploratory and descriptive analysis of the dataset. 

In the code below, I used pandas_profiling to create two HTML reports, one for the posts_sentiment.csv file and the other for the comments_sentiment.csv file. The result of the reports are available within this folder. 


```python
import pandas as pd
import pandas_profiling as pp

# Load the posts CSV file
posts = pd.read_csv('posts_sentiment.csv')

# Generate the profile report for the posts CSV file
profile_posts = pp.ProfileReport(posts, title='Posts', minimal=True)

# Save the report to an HTML file
profile_posts.to_file('posts_report.html')

# Load the comments CSV file
comments = pd.read_csv('comments_sentiment.csv')

# Generate the profile report for the comments CSV file
profile_comments = pp.ProfileReport(comments, title='Comments', explorative=True)

# Save the report to an HTML file
profile_comments.to_file('comments_report.html')

```

    Summarize dataset: 100%|██████████| 20/20 [00:00<00:00, 210.76it/s, Completed]                     
    Generate report structure: 100%|██████████| 1/1 [00:01<00:00,  1.94s/it]
    Render HTML: 100%|██████████| 1/1 [00:00<00:00,  6.31it/s]
    Export report to file: 100%|██████████| 1/1 [00:00<00:00, 333.65it/s]
    Summarize dataset: 100%|██████████| 55/55 [00:02<00:00, 18.77it/s, Completed]                  
    Generate report structure: 100%|██████████| 1/1 [00:01<00:00,  1.76s/it]
    Render HTML: 100%|██████████| 1/1 [00:00<00:00,  1.18it/s]
    Export report to file: 100%|██████████| 1/1 [00:00<00:00, 74.37it/s]
    
```

[You can check the quick visualization of the posts data here.](posts_report.html)

[You can check the quick visualization of the comments data here.](comments_report.html)
