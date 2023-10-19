# ***Reddit-Project***

# Summary



# Problem Statement
A large outdoor sports retailer is looking to improve their online marketing strategy. They wish to create better targeted advertisements for gear in their camping and backpacking categories. The marketing team has asked me to create a model that can determine from online text whether the potential customer is more likely/ primarily an ultralight backpacker or a camper. The text the model will be deployed on will be social media posts and comments across different platforms as well as comments on the sports retailer's website and blogs. 

# Background
### Differences between camping and ultralight backpacking
**Backpacking** is a type of camping where all camping gear is carried by an individual or group as they hike. **Ultralight backpacking** is a subcategory of backpacking which focuses on minimizing the amount of weight carried. As such, ultralight backpackers are also camping, while the majority of individuals who camp are not ultra-light backpackers. Most campers are able to transport their gear directly to their campsite with the use of a vehicle. Therefore, the 'average camper' is not limited by weight and can bring much heavier gear that may be more durable, more comfortable, and more luxurious. Ultralight backpackers track the weight of their gear by the ounce and will purchase very different tents, sleeping bags, sleeping pads, and cooking equipment compared with car campers. 

For example, a car camper may purchase and use a large 4 person tent, multiple large sleeping pads, heavy sleeping bags, camping chairs, a multi-burner stove, and other large camping items. Comparatively, an ultralight backpacker will likely purchase a smaller, lighter tent, a lightweight tent or quilt, and the smallest pot and stove they can find. 

The outdoors sports retailer would like to optimize their marketing by targeting specific products to specific customers and advertising those products with the features that matter most to each group. 


# Data Collection
This retailer explained that they will have posts, comments and other text data from potential customers that can be used to classify what type of camper the customer is. Therefore, I need to collect text data in order to build a Natural Language Processing (NLP) classification model. To train this model, I needed to collect large amounts of text data written by ultralight backpackers and campers.

I found two communities on Reddit and pulled posts from these two communitites or subreddits.

### Subreddits
**Ultralight Subreddit self-description:** "r/Ultralight is the largest online Ultralight Backcountry Backpacking community! This sub is about overnight backcountry backpacking, with a focus on moving efficiently, packing light, generally aiming at a sub 10 pound base weight, and following LNT principles. Join us and ask yourself the question: Do I really need that?"
https://www.reddit.com/r/Ultralight/

**Camping Subreddit self-description:** "A subreddit for campers concerned more about the act of camping and less concerned about hiking long distances or light gear. Primarily for tent/hammock camping. No RV camping here."
https://www.reddit.com/r/camping/

#### PRAW
Reddit has its own API. I used PRAW, the Python Reddit API Wrapper to obtain posts from each subreddit. Reddit limits the number of posts that can be collected per request and per day. PRAW enables multiple parameters for requests such as 'new' which requests the newest posts and 'top' which requests posts with the highest vote counts. I used PRAW to pull posts over the course of multiple days to collect a total of 4,005 posts from the camping subreddit and 8,979 posts from the ultralight subreddit. 


# EDA & Data Cleaning
Data cleaning consisted of the following steps:
- Concatenating all of the posts pulled on different days into 1 dataframe
- Dropping all duplicates (since multiple types of requests were used, some duplicate posts were pulled)
- Dropping any rows without any text in the post
- Dropping any rows with very little text (less than 10 words)

Key findings from inital Exploratory Data Analysis:
- The average length of a post in r/ultralight (281 words) was significantly longer than the vareage length of a post in r/camping (92 words)
- Posts in r/ultralight were slightly more positive than posts in r/camping (vader score of 7.98 vs 6.86)
- Some of the most commonly used words from each subreddit were the same: tent, gear, trip, and time

#### Possible Challenges for Machine Learning:

1. There are more posts in r/ultralight and the posts are significantly longer resulting in very unbalanced classes
2. The model will be trained on a larger quantity of 'ultralight' vs 'camping' language which could lead to bias and problems with misclassification
3. The culture of the subreddits may be different and not indicative of the larger populations- everyone who is an ultralight backpacker and everyone who camps. The significant difference in sentiment can be due to differences in moderation between the subreddits, rules for posting etc. 


# Model Construction & Analysis


# Findings/ Conclusion

# Limitations