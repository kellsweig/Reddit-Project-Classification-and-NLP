# ***Reddit-Project***

# The Challenge
A large outdoor sports retailer is looking to improve their online marketing strategy. They wish to create better targeted advertisements for gear in their camping and backpacking categories. The marketing team has asked me to create a model that can determine from online text whether the potential customer is more likely/ primarily an ultralight backpacker or a camper. The text the model will be deployed on will be social media posts and comments across different platforms as well as comments on the sports retailer's website and blogs. 

# Background
### Differences between camping and ultralight backpacking
**Backpacking** is a type of camping where all camping gear is carried by an individual or group as they hike. **Ultralight backpacking** is a subcategory of backpacking which focuses on minimizing the amount of weight carried. Some people categorize ultralight backpacking by base weight (the weight of the pack without consumables such as food and water) with ultralight backpacking defined as having a base weight under 10 pounds. For others this is a loose definition or arbitrary cutoff.


Like car campers, ultralight backpackers are camping and sleeping outdoors. However the majority of individuals who go camping are not ultra-light backpackers and most are car campers who are able to transport their gear directly to their campsite with the use of a vehicle. Therefore, the 'average camper' is not limited by weight and can bring much heavier gear that may be more durable, more comfortable, and more luxurious. Ultralight backpackers track the weight of their gear by the ounce and will purchase very different tents, sleeping bags, sleeping pads, and cooking equipment compared with car campers. My goal is to build a model that can distinguish between someone who primarily car camps and someone who is an ultralight bakcpacker.

### Why does this matter?

Ultralight backpackers and car campers purchase very different gear. A tent that is marketed towards an ultralight backpacker may not appeal to a car camper and vice versa.

![A large camping stove for car camping](https://m.media-amazon.com/images/I/91WICqfRWWL._AC_UF1000,1000_QL80_.jpg)
This stove would be a great purchase for a car camper but would not be appropriate for an ultralight backpacker to lug around. 

![A backpacking stove](https://images.squarespace-cdn.com/content/v1/5b4544e485ede17941bc95fc/1629408597455-ZMKU30RDLUK53R3AJ28W/msr-pocketrocket-deluxe-camping-stove-colorado.jpg)
This is one of the most common stove setups for ultralight backpackers

The outdoors sports retailer would like to optimize their marketing by targeting specific products to specific customers.


# Data Collection
This retailer explained that they will have posts, comments and other text data from potential customers that can be used to classify what type of camper the customer is. Therefore, I need to collect text data in order to build a Natural Language Processing (NLP) classification model. To train this model, I needed to collect large amounts of text data written by ultralight backpackers and campers.

I found two communities on Reddit, r/ultralight and r/camping, and pulled posts from these two communitites or subreddits. This process took place in the Data Collection notebook.

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
I built and compared the metrics of the following models:
- Multinomial Naive Bayes
- Bagging Classifier
- Ada Boost Classifier
- Random Forest Classifier
- Extra Trees Classifier
- SVM Classifier
- Voting Classifier

I used the TF-IDF vectorizer for pre-processing and used a grid-search to tune the parameters of multiple models. I found that the Random Forest Classifier and the AdaBoost Classifier were able to best predict the subreddit a post came from. The accuracy of the Random Forest Classifier was .993 and the accuracy of the AdaBoost was .994 when run on the testing data. To help determine which model performed better, I created confusion matrixes to look at what types of mistakes were made. I then queried the misclassified posts and read them to determine if a human would make the same error or if the models could be further improved. I found that while the AdaBoost Classifier misclassified less posts, the mistakes of the RandomForest model were more similar to the mistakes a human could make. Both models misclassified different sets of posts.

Since the models made different types of mistakes, I decided to try to combine the models with a Voting Classifier which would use the probabilities of the predictions weighted with the predictions to determine a final prediction. This improved the accuracy (testing data) slightly bringing it to .9968. More importantly, after examining the set of misclassified posts, I determined that the mistakes the model made were much more reasonable. 3 of the posts were 'mistakes' a human would have made, 3 were mistakes a human would probably not have made, and the final 3 posts were not directly related to backpacking or camping at all. I decided that this was a reasonable error rate and to move forward with this final model. 


# Findings/ Conclusion
My final model, a voting classifier containing an AdaBoost classifier and an Extra Trees Model, has an accuracy of .9968. While this model is considered a black box model (meaning it is not human-interpretable), I was able to make some inferences about how the model was working by obtaining feature importances from the Extra Trees model. 
- that while some words such as campsite, camping, camp, and gear are common across both subreddits, the frequency with which these words are used differs.
- The decision not to use lemmatization or stemming was likely a good choice. Camp, camping, campground, campers, and campsite all have the same base, however these words have very specific uses in these communities. Ultralight backpackers may be much more likely to say camp than campers, and reducing model complexity in this way would likely have resulted in a less successful model. 
- The wrods/ characted groups 'com, 'https', and 'lighterpack' were very high on this list. It is common in the r/ultralight subreddit (and in the ultralight backpacking community in general) to share links to a personal gearlist hosted on the site 'lighterpack'. The presence of these words or characters likely increased the likelihood a post came from r/ultralight.

Finally, the model was tested once again on a new dataset 'final_test.csv' which was collected, cleaned, and transformed the same way- but for one exception. Short posts (with less than 10 words) were retained. The model was able to correctly classify the subreddits with 99.4% accuracy- even on shorter posts. 

# Limitations & Next Steps
This model has only been trained on language used on subreddit. This may not translate to the type of language used on other social media platforms or on comments on gear comapnies' websites. Ultralight backpackers who post on reddit may not think the same or communicate the same way that other ultralight backpackers do and the same goes for car campers. The high accuracy of the model may actually indicate that it is trained on the very specific tendencies of posters on these subreddits and may also lead the model to perform worse on data pulled from other sources. 

As new gear develops and different topics and challenges become relevant to the ultralight backpacking and camping communities, the model should be continually retrained on new data. This will maintain the current high performance and may even improve it over time. If the model does not perform well on text from other platforms, data from sources outside of reddit can be fed in.


## Sources & Citations:
Coding & Technical Assitance:
- Kiersten South: assisted with adding images to markdown
- Tim Book: troubleshooting error messages related to data-size
- Rowan Schaefer: Project direction, troubleshooting with git.ignore, theoretical issues with model deployment
- Emily Rogalski: Data pulls questions

Coding Sources:
- The framework for each model I used was based off of the following lessons: 505 (Naive Bayes), 602 (Bagging), 603 (Random Forest and Extra Trees), 604 (Boosting- AdaBoost), and 605 (SVM)

Image Sources:
- first image: https://m.media-amazon.com/images/I/91WICqfRWWL._AC_UF1000,1000_QL80_.jpg
- second image: https://images.squarespace-cdn.com/content/v1/5b4544e485ede17941bc95fc/1629408597455-ZMKU30RDLUK53R3AJ28W/msr-pocketrocket-deluxe-camping-stove-colorado.jpg