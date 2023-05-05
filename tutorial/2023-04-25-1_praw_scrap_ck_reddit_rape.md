---
layout: default
title: Scraping data with PRAW library
nav_order: 1
---

# Scrapping data with PRAW library

## Introduction

In this Python Notebook file, I am going to set up everything to scrap data from Reddit. Reddit has its own API to access their data. PRAW is my choice because it respects the terms of use stated by Reddit, is open-source and has the support of a big community and the very Reddit. For scrapping data from Reddit, my first choice and recommendation is to prefer PRAW over other data scrapping libraries. 

This is also the moment to design what database models will best suit your interests. In this project, I am separating comments and posts in two different .csv files. I added a row called post_id, so I can use it to track the origin of each comment as well as the unique id of each post. I have chosen this database model because it would allow further researchers to more easily work with the textual productions from comments. 

Another option is using an hierarchical database, like a JSON file, and nest the comments inside each post. While it is also a valuable form of organizing data, it would require more computer processing power when analyzing only the comments, since further analysis algorithms would need to read all the posts just to find the nested comments. 

### Importing libraries

In this initial part of the setup, I am just importing the needed libraries for this program. I am using the PRAW library to access the Reddit API to scrap data, the pandas library for database usage, the csv library to work with csv databases and the os library to use the operational system functions. 



```python
import praw
import pandas as pd
import csv
import os

```

### Setting up the credentials to use the PRAW API

To use the PRAW API, you need to set up a Reddit account and create an app. You can do this here: https://www.reddit.com/prefs/apps/

This information is private and you should never share your keys with untrusted parties. 


```python
reddit = praw.Reddit(client_id='xxx', #left top part
                     client_secret='yyy',
                     user_agent='yyy')

```

So far everything working! Below, I will test if the connection is okay. You can see the message bellow to make sure if the API is working.


```python
try:
    subreddit = reddit.subreddit("test")
    for submission in subreddit.new(limit=1):
        print(submission.title)
except Exception as e:
    print(f"Connection failed: {e}")
else:
    print("Connection successful")
```

    test3
    Connection successful
    

### Hands on! Performing the actual data scrapping using PRAW and saving it in a CSV database

In this code, I start setting up the PRAW library to conduct the search for the keywords "rape" and "raping" in the CrusaderKings subreddit. I created two lists, one called "posts" and the other one called "processed posts IDs".

This way, I can keep track of the post IDs that I already processed and prevent the creation of duplicated posts when using a similar keyword for my search, as it is the case of "rape" and "raping."

The first part of this data scraping is conducting a loop search that will retrieve all the posts with the keyword "rape" in the title of the posts or the content of the posts. Then, I repeat the process using a similar keyword (i.e., "raping"), since I could expand the variety of posts that talk about this same subject.

I store the post IDs, the titles, the body, the general score, the upvotes, and the downvotes from each Reddit post that was retrieved from this keyword search.

In some circumstances, the comments might not have a general score, nor a score of upvotes nor downvotes. To prevent errors, I test if these categories are empty and I add a null value to them.

After each loop, I save all the posts and comments that I scraped from Reddit in two different files, one file for the posts and another file for the comments. So, I print a message saying that every post and comment was saved in its respective CSV files successfully.

To help you visualizing each post being scrapped, I also added printing lines to say the title of each post, followed by the keyword that was used for it to be retrieved. 


```python
ck_subreddit = reddit.subreddit('CrusaderKings')

# CSV file for posts
posts = []
processed_post_ids = set()  # set to keep track of already processed post IDs

for post in ck_subreddit.search('rape', limit=1000):
    if 'rape' in post.title or 'rape' in post.selftext:
        if post.id not in processed_post_ids:
            posts.append([post.id, post.title, post.selftext, post.score, post.ups, post.downs])
            processed_post_ids.add(post.id)
            print(post.title, 'rape')

for post in ck_subreddit.search('raping', limit=1000):
    if 'raping' in post.title or 'raping' in post.selftext:
        if post.id not in processed_post_ids:
            posts.append([post.id, post.title, post.selftext, post.score, post.ups, post.downs])
            processed_post_ids.add(post.id)
            print(post.title, 'raping')

posts_df = pd.DataFrame(posts, columns=['post_id', 'title', 'body', 'score', 'ups', 'downs'])
posts_df.to_csv('posts.csv', index=False)

# CSV file for comments
comments = []
for post in posts:
    post_id = post[0]
    for comment in reddit.submission(post_id).comments:
        if comment.score == None:
            comments.append([post_id, comment.id, comment.body, None, None, None])
        else:
            comments.append([post_id, comment.id, comment.body, comment.score, comment.ups, comment.downs])
            print('comment added')

comments_df = pd.DataFrame(comments, columns=['post_id', 'comment_id', 'comment', 'score', 'ups', 'downs'])
comments_df.to_csv('comments.csv', index=False)

print("CSV files successfully saved.")

```

    Feudal vs Tribal rape
    It pains me how unpolished this game is rape
    Holy hell guys the crusades in this game actually happened we shouldn't joke about this stuff it was horrifying rape
    good lord do the devs know the limits of compassion rape
    Got my first immortal character (650 hours) during an Aztec rape of my empire! rape
    I really don’t want to rape my husband. rape
    What's the most depraved mod? I want rape, sexual slavery, sadism and everything else you have to offer! rape
    All I did was raze their cities and rape their women. I don't get why they are so mad at me rape
    Can you still start a war, capture the other king's wife, rape her, impregnate her, cut off her hands, tongue, eyes, then ransom her back to give birth to your spawn? rape
    How can I ensure my heir doesn’t get raped by war each time I die? rape
    The King of Scotland's only daughter claimed I raped her rape
    Could I be the victim of Satanic rape? rape
    A conversation with my marshal (2) rape
    When the king of Denmark pisses you off so much that you decapitate his 1-year-old girl, hang his vassals, and rape his wife rape
    How to not get raped as Harold Godwinson? rape
    You raped her. You murdered her. You killed her children. (Game of Thrones) rape
    No game accomplishes to stress me out like CK3 does rape
    I think I just got the option to rape my husband. rape
    Queen of Hungary won't stay in my Viking rape dungeon? rape
    "Exile" characters rape
    A Yygling count of the Faeroe Islands, the child of rape, is set to inherit a whopping nine counties and the kingdom of Navarre. rape
    not enough portraits to rape for in india rape
    I feel abused... rape
    Need some help kidnapping and raping the pope rape
    I know I'm being gang raped, but I'm still concerned about your political affiliation... rape
    This is why I always jail rape and genocide rather than being a family man. rape
    When you see this guy's boats approaching..."I never wanted to see someone raped...until now" rape
    Is it possible that someone raped your wife? rape
    What would the Visigoths call the Kingdom of Maghreb rape
    Help me I am Imprisoned for rape!! rape
    Paying a fine because I (allegedly) raped the daughter of my LIege. rape
    Given my current position, am I at risk of getting raped by the mongols? First time getting this far and they just started spawning rape
    ck3 criticism rape
    While everyone is complaining about coalitions, here is my being raped by raiders rape
    Tyrannical to revoke title of a duchy I just conquered??? rape
    First Norse reformation run, let's see if we can pull it off rape
    Is there any point of playing as the Zoroastrian duke, or do you just get raped by the Ilkhan/Timurds? rape
    My liege (and ally) went to war and I got pillaged rape
    Anyone else become extremely good at history and geography due to CK2? rape
    A little CK3 rage for you all, enjoy the schadenfreude rape
    Here is what I *love* about CK2 rape
    The single coolest dynasty in the game: The House of Ceuta (Qutids) rape
    Why is having affairs as norse criminal? rape
    Einarr Vagnsson's Saga rape
    I Want To TOTALLY Destroy This One Annoying Ass Duchy rape
    Thoughts and observations 215 hours in. rape
    What was your most horrible thought when playing ck2? rape
    Mod to put Mecca to the torch rape
    Bruh how the fuck do you even defend against a Crusade? Just got hit with a 60K-strong army. Seems like the best way to keep your lands is to simply surrender, save up your money, and just declare war on the beneficiary ruler after the truce is over... rape
    Screw Catholicism rape
    AAR: Tywin Lannister Did Nothing Wrong or: How I Learned to Stop Worrying and Rationalize Genocide rape
    Strange observation or is it just me? rape
    I’m going through religious rebellion hell with no end in sight. How do I convert to hellenic if I don’t convert immediately after forming Rome? rape
    The story of Emperor John "The Avatar of Jesus"/Imparat Ioan "Avatarul lui Iisus" rape
    How the hell do you raise large armies? rape
    War/battle tips rape
    Annoying looting mechanic rape
    How I created Israel rape
    MONEY MONEY MONEY rape
    How to impregnate my celibate wife? rape
    [ACCIDENTALLY TURNED RANT HALFWAY THROUGH] My only wish for this game: replace Hungarian cultural units with something else rape
    When have you got lucky in the game? rape
    just a canaries playthrough (charlemagne start( rape
    Questions about a jewish play through rape
    [Succession Game #2] Round 9 - King Oswulf d'Isigny rape
    I hate the Aztecs rape
    Need help making my culture better than other cultures rape
    From Norway to Venice. The Mercantile Vikings rape
    [Game #6, Round 7] - King Belasko II rape
    Venice has frustrating design rape
    [Succession Game #2] Round 4 - King Henry I d'Isigny rape
    [Game #5, Round #6] - King Belasko rape
    Dealing with Jihad madness rape
    How to play as a Tribal rape
    Small rant about religious conversion and heresies rape
    This game is crazy! rape
    This game have changed me. rape
    Adapting Feudalism as a tribal superpower. rape
    A tale of treachery, bugs and general bullshittery. rape
    Will vassal holy orders join factions against you? rape
    Some advices for Sword of Islam? rape
    [Help] Venice 769 Playthrough rape
    Mod idea: Casualties rape
    Question on Adventurers rape
    Is raiding OP? rape
    Wife does of explosive vomiting ! *GONE WRONG* rape
    Recommendation for a Viking Playthrough rape
    Some memories from my first campaign rape
    Frisia nerf? When did this happen? rape
    I can't arrange any marriages or betrothals suddenly. Help? rape
    How the fuck do you even live as Karen. rape
    Shower Thought I Had rape
    Zunist Run Update 2: Elective Bugaloo rape
    [Problem] My viking ships are heaving with gold but are stuck in the Rhine. Help me please. rape
    Trying to take over Sweden as the Republic of Gotland, not going so well. rape
    Torture mods? rape
    The Rise of Sweden - The Reign Of King Alfr rape
    Regarding shattered world, how can I speed up other characters to do better regarding expansion? rape
    Ruthenia vs Rus rape
    So confused, need clarification. rape
    TIP: How to adopt feodalism or make a republic without reformation. rape
    The new combat system does not work... rape
    Can anyone give me some advice about my Ironman Khazar playthrough? rape
    Can we please ban Anime Portraits from this subreddit? Every time someone posts it I throw up a little in my mouth. rape
    Oi me bum rape
    Seriously they should balance the chance of imprisonment. Currently its ridiculous!!! rape
    Tips for playing Saxony? rape
    Glorious Basilius....not. rape
    Did they break the mongols? rape
    How the hell do I stop my vassals from raping me during succession (CK3) raping
    Why is my wife okay with me raping women but raises a stink if I seduce them first? raping
    help how do I stop the mongols from raping europe raping
    My Marshal is demanding to be imprisoned for raping his newborn daughter... who apparently could be lying raping
    That bastard is raping all my family raping
    When I'm not pillaging and raping, I like to engage in childish fights with little girls raping
    My 3 year old daughter hates me for raping her mother (she doesn't even know her) raping
    Considering going back to CK2 raping
    The rebirth of Egypt raping
    A comprehensive and summarical list of all Crusader Kings Two DLCs. raping
    Bloodlines: The Legacy of Charlemagne (Part 1) raping
    How do I get Sicily if I started in 1066? raping
    Wow i put the game down for a year or 2 and its a whole new game! raping
    [Game 3, Round 1] - Duke Ishanadl Abdeddit raping
    I always play Norse pagans. Is it possible to play a game and successfully be friends with the HRE or Catholics in general so they dont call a death crusade against my Odin worshipping kingdom? raping
    I wish I could quit my blobbing addiction . raping
    Is there an Achievement for Halting the Mongols in their tracks? raping
    [Succession Game #2] Round 8 - King Henry III d'Isigny raping
    [Game 3, Round 8] - Sultan Saruca Abdeddit raping
    Game difficulty levels raping
    About revolts happening when you're a horselord. raping
    Please recommend a good noob coop vanilla ck2 please! raping
    How To Keep Vikings Away raping
    Scandinavian Empire help raping
    Some tips and random thoughts for a Rurik ToG start and non-Norse pagans in general (long) raping
    £1.59 for some faces raping
    Annexed nomad lands not letting me (a feudal ruler) install any vassals without first building castles? raping
    Liege took ALL of my money.... raping
    The Paradoxical Millennium Needs You! raping
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    comment added
    CSV files successfully saved.
    

### Last comments

As you can see, this code will output many lines of text with the titles of the posts and when each comment is being saved. In my test, it retrieved 138 posts and hundreds of comments from these posts. 

The next step is preparing the data to use it!
