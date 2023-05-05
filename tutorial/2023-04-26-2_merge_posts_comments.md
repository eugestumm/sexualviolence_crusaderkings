---
layout: default
title: (Optional) Preparing the data to be used with other NLP techniques!
nav_order: 2
---

# (Optional) Preparing the data to be used with other NLP techniques!

## Introduction

In this Python Notebook file, I will be formatting the data so one could use it in many suitable ways. In particular, I am interested in using this data for sentiment analysis. However, other Natural Language Processing techniques could be used to look at this data and might benefit from different forms of designing the data. 

For this reason, I will use this program for two main purposes. The first, it to double check if there is any duplicated post from our initial data scrapping. If the data scrapping filter that we set up for preventing duplicated posts, we should not see any error message accusing a duplicate. The second goal of this programming piece is to create a text-only file containing the title of the posts, the body of the posts, and all the comments. The goal here is to recreate each webpage including only the human-generated textual production, as one would find it if accessed Reddit on the browser. 

The identity of the users that commented or posted on the scrapped resources were not collected, so they should not appear on the newly generated files. 

## Setting up the code!

I started importing the two libraries we will use in this project, the os library to use the folders of the system and the csv library to use the csv format. 

The first step is to create a folder to store all the files. I also make sure to check if this folder already exist to prevent errors. 




```python
import os
import csv

# Create the output directory if it does not exist
output_directory = 'full_posts_comments'
if not os.path.exists(output_directory):
    os.makedirs(output_directory)
```

Since the folder set up was successful, I will read the posts.csv file and store it in a variable called posts. I will also read the comments.csv file and store it in a variable called comments. 

Then, I will create a function to merge the posts and comments. This function will be used to create a new file containing the title of the posts, the body of the posts, and all the comments. The goal here is to recreate each webpage including only the human-generated textual production, as one would find it if accessed Reddit on the browser.

To make sure there is no duplicated posts, I will create a function to check if there is any duplicated post. This function will be used to check if the data scrapping was successful. If there is any duplicated post, the function will return an error message. If there is no duplicated post, the function will return a message saying that the data scrapping was successful.

This way, one could use the comments separatedly or together with the posts.

#### Observation!

Make sure to use utf-8 encoding to avoid errors with the text.


```python

# Read the posts.csv file and create a dictionary with post_id as key
# and the post's title, body as value
posts = {}
added_post_ids = set()
with open('posts.csv', 'r', encoding="utf-8") as posts_file:
    reader = csv.reader(posts_file)
    next(reader)  # Skip header row
    for row in reader:
        post_id, title, body, score, ups, downs = row
        if post_id in added_post_ids:
            print(f'Error: Post {post_id} already added.')
            continue
        posts[post_id] = {'title': title, 'body': body, 'comments': []}
        added_post_ids.add(post_id)

# Read the comments.csv file and add each comment's body to the respective post's dictionary
with open('comments.csv', 'r', encoding="utf-8") as comments_file:
    reader = csv.reader(comments_file)
    next(reader)  # Skip header row
    for row in reader:
        post_id, comment_id, comment, score, ups, downs = row
        if post_id in posts:
            posts[post_id]['comments'].append(comment)

# Create a text file for each post with its title, body, and comments' body
for post_id, post_data in posts.items():
    filename = f'{output_directory}/{post_id}.txt'
    with open(filename, 'w', encoding="utf-8") as output_file:
        output_file.write(f'{post_data["title"]}\n\n')
        output_file.write(f'{post_data["body"]}\n\n')
        for comment in post_data['comments']:
            output_file.write(f'{comment}\n\n')

```

### Checking if everything went well!

If the data cleaning was successful, the function should return no message. If there is any duplicated post, the function should return an error message.
