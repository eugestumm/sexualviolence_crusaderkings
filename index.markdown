---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---



# Sexual violence in video game social media: a sentiment analysis of rape speech in a subreddit of Crusader Kings
This is my project on Crusader Kings and sexual violence. My goal is to analyze how the community of players of the video game series Crusader Kings dealed with rape speech in their biggest Reddit community. I used the data collected from PRAW Reddit API with sentiment analysis using VADER sentiment analysis tool and descriptive and exploratory statistics to visualize and interpret the data. 

It is my final project for the Digital Humanities Practicum course, which I worked under the guidance of Professor Susanna Allés-Torrent at the University of Miami. The original source code of this project is in a private repository containing all the data and code used to scrap data. Since it contains my private API keys for Reddit, I cannot make it public. Here you can see a version without my Reddit API keys. 

## Main findings

Although most of the posts had a highly negative score, many posts had a positive score due to celebratory speech about rape. Below, see an example:

```html
Hello guys!

No need to talk around the bush: In games like these that give me so many options I want to go completely nuts in terms of morality!

So, what mods are there out there that you can recommend which feature all these things that would induce my employer to fire me and my family to disown me should they ever find out I played it?

Edit: Thanks a lot for all the help! I think I’m gonna stick with the GoT mod for now since the Dark World reborn seems to have some balancing issues and I’m really not a fan of animals.
```



## How to use this project
I diveded this code into 5 main steps, and one additional step for including quick forms of visualizations. Each folder contains a number in the beginning of its name, which indicates the order in which the code should be read and executed.

If you wanna experiment with different data samples, please make sure to manually copy the data you scraped from Reddit to one folder to the other.


- [Scraping data from Reddit using PRAW library](/tutorial/2023-04-25-1_praw_scrap_ck_reddit_rape)
- [(Optional) Preparing the data to be used with other NLP techniques!](/tutorial/2023-04-26-2_merge_posts_comments)
- [Introduction to VADER (Valence Aware Dictionary and sEntiment Reasoner) Sentiment Analysis tool](/tutorial/2023-04-27-3_introduction_to_vader)
- [Using VADER to generate a sentiment analysis of the reddit posts, titles, and comments](/tutorial/2023-04-28-4_sentiment_analysis)
- [Visualizing Sentiment Analysis data](/tutorial/2023-05-04-5_visualization_sentiment_analysis)
- [(Optional) Pandas Profiling quick visualization](/tutorial/2023-05-05-6_pandas_profiling_visualization)



## Acknowledgements
To supervise and improve the quality of my programming, I also used GitHub's Copilot and OpenAi's ChatGPT. Since Artificial Intelligences are becoming a increasingly important part of programming communities and practices, I wanted to experiment with them as part of my development as a programmer. Their assistance was very enlightening, and for this reason I include them in the acknowledgements of this project.

## License
This code is licensed under the GNU GPL3 license.