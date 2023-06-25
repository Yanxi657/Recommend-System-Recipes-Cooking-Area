# Recommend-System-Recipes-Cooking-Area
User behavior in dish preferences is categorized into 5 events: viewing recipes, short descriptions, steps, long descriptions, and comments. These events undergo data analysis and visualization, including weighted processing and normalization. User-based and event-based collaborative filtering methods are then utilized for recommendation.

## Dataset Description& Data Visualization
### 1.In the dataset,I have 13926rows, 4 columns.
Columns: Date, UserID, RecipeID, Event
Event Type: view_recipe, short_description, steps, long_descriptioin, view_comments
### 2.Data Visualization
In the stage of visual analysis of the original data, I visualized the scatter plot of Event Count by Hour for five events, and assigned the weights of the events according to this result, and the weight sizes from top to bottom were: View recipe; steps; short_description; long_descriptioin; View comments, supported by this data, makes my weight assignment reasonable.  

## User-based Algorithms
I used different matrices to calculate the similarity and found the most suitable matrix for the calculation.
1.User-item matrix with Iights: Iighting according to the conversion funne
2.User-item matrix using time-decay:assign a higher Iight to more recent events and a loIr Iight to older events
3.Normalize user-item matrix:Scale uiMatrix values betIen 0 and 10
4. Normalize user-item matrix (fill NaN with 0):Handling null values: Fill in NA with 0
5. Normalize user-item matrix (fill in NaN with mean):Handling null values: Fill in NA with mean
6. Decentralized matrix:Subtracting the mean of each column from the corresponding elements of uiMatrixNorm

User-based collaborative filtering
Step1. Find users who are similar to user 122
Step2. Summarize recipes with no implicit ratings from user 122
Step3. See implicit ratings for these recipes by similar users (19 records in total)
Step4. Recommend recipes to user 122 using top-5

The result of Pearson：the similarity calculated by Pearson is very strange, the values after descending sorting are 1 and many NA. while the results calculated by cosine are shown on the right, It goes from 1 to 0. So I choose cosine similarity. 


## Item-based algorithms
Project-based collaborative filtering is also a recommendation system method that predicts user ratings or preferences for items based on similarity between them. Compared with users who often change their preferences, the similarity between items is less likely to change, and is more stable and accurate.

### 3.1 Item-based collaborative filtering
Step1. Multiply item * item by using 'dot' function
Step2. Pick out recipes with higher implicit ratings from user 122
Step3. In the item-item similarity matrix, find recipes similar to these recipes
The first step is to perform Similarity Precomputation, Multiply item and item by using 'dot' function, and get the item-item similarity Matrix, so that I can know which recipes are similar.
The second step is to find the recipes that user 122 has a higher implicit score, so that I can know which recipes user 122 usually prefers to read.
At this time, it involves how to filter the recipes with higher scores. After trying, I choose to set the threshold value to 0.5, and the recipes greater than 0.5 are reserved for the next step.
In the third step, in the item-item similarity matrix, I find recipes similar to these recipes, and choose more similar ones to recommend to user 122.

At this time, the filter is involved again. The item coverage after setting the threshold is not good. I will explain it in detail later, so this time the filtering uses top-k, k is 5, and I will recommend 5 movies to the user.


### 3.2 Item-based collaborative filtering (SVD) 
Step1. Build SVD model
Step2. example prediction
Step3. Verify model accuracy using known examples

Now，the second solution, SVD algorithm.
Fisrt,I have completed the SVD model after simple processing and tuning parameters. Then I used model to predict NO.146 implicit rating by No. 122. 
Although I have not recorded the actual value, I can know this model works. It is possible to proceed further.
Then, I have used one known example to verify the accuracy of the model.As shown，The prediction accuracy of the model is not high!


### 3.3 Item-based collaborative filtering (Funk SVD) 
Step1. Build SVD model
Step2. certification accuracy
Step3. Predict implicit rating for 122 user
Step4. Recommended recipe predicts the higher implicit score to user 122

So I tried Funk SVD, and after modeling I also tested it with the same known examples and found that the prediction accuracy of this model was higher than SVD, so I decided to use it for the prediction of recommendation.
I used the model to predict the implicit ratings of user 122 for all recipes, and used the top-k method to filter out the top five recipes recommended to him.
The above is all I have for item-based collaborative filtering.


## Parameter tuning & Performance evaluation
### 4.1Initial Exploration of Data
1. Sparsity of user-item matrix
1. Sparsity of user-item matrix
First, I calculated sparsity of the scoring matrix to observe the data, and found that the sparsity is nearly 100%, which is different from the data of the teacher's class example and closer to the real data.
At the same time, I believe that users' interests will change over time, and what they like now may not necessarily be liked later, so I also consider the problem of time decay，I'll talk about it on the next page

### 4.2 Adjustment parameters
When I built the recommendation system, I adjusted different parameters many times, which are the following:
1.Threhold
In recommender systems I filter data by setting multiple thresholds. After many attempts, I found that the threshold was too high and the coverage was too low, so I adjusted the threshold to a relatively high coverage level.
2.Time Decay
I set the threshold of the decay factor to 30 days, because after a month, changes in climate and temperature may affect people's dietary preferences and change people's interest in a certain dish.
3.Top-K
I set K = 5  in the recommendation system, because I found that the first 5 recipes recommended are more suitable for mobile phone screens.
4.Iight
According to the scatter diagram of user behavior data, I determined the user's operation order of the recipe:  view_recipe, steps, short_description, long_description and view_comments, and assigned different Iights to different actions in this order to recommend related content more accurately

### 4.3 Coverage
User Coverage & Catalogue Coverage
After the recommendation system was built successfully, I used user coverage and catalog coverage to evaluate our recommendation system.The higher the user coverage, the more content the recommendation system recommends. Our user coverageis only 5.5%, while that based on item is 27%. Therefore, I believe that among the two methods I use, the item-based recommendation can tap user interests to a greater extent and recommend items to users.

## Findings & Challenges
### 5.1 Findings
5.1.1 
User-based：
Advantage：
It can provide personalized recommendations to users because it focuses on the similarity betIen users. 
Disadvantage：
Data sparsity will have a certain negative impact on this method. Since users can only rate/interact with a subset of items, it may be difficult to find other users similar to a user who have limited public ratings on a small number of items.
5.1.2 Item-Based：
Advantage：
Faster when dealing with a large number of items because it focuses on similarity betIen items rather than similarity betIen users.
Disadvantage：
More data is needed to get accurate recommendations, so it may be less effective when dealing with a small number of items.


### 5.2 Challenges
1.Cold start problem
Recommending new users and new items is a challenge because there is not enough data available to calculate similarity.
2.Matrix sparsity
The quality of items evaluated by users is too small compared to the total number of items, resulting in sparse data in the rating matrix. When calculating the nearest neighbors of users or items, the accuracy rate will be relatively low, which will lead to a sharp decline in the recommendation quality of system.
3.Data bias problem
The data used in recommender systems may be biased, e.g. users may tend to interact with certain kinds of items, so these items will be recommended more in the system, while other types of items may be ignored.
