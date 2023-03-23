# Predicting Game Outcomes for Tier 1 Professional League of Legends Teams

by James Thai (jqthai@ucsd.edu)

---

## Framing the Problem

The problem at hand regards the outcome of League of Legends games, specifically Tier 1 Professional League of Legends games. We want to predict whether or not a tier 1 professional League of Legends team will win a given game. 

The prediction problem at hand will be done through classification. More specifically, through the use of a binary classifier, since the response variable we are predicting is one of two outcomes: a win, or a loss.

As an avid League of Legends player and watcher, I wanted to predict whether or not a team would lose to see how different statistics and occurrences during the game affect a team's outcome. Professional League of Legends is extremely different than playing casually or even competitively by yourself online - professional teams study the game and understand the implications of acquiring certain objectives at certain times during games. Creating a model to predict this would be a great classification problem, and the process of building, testing, and improving the model would provide insight to what factors affect winning the most.

The metric I chose to evaluate my model was accuracy, which is simply (# of accurate predictions/# of predictions made). Other suitable metrics, such as recall (# of true positive predictions/# of actual positive instances), or precision (# of true positive predictions/# predicted positive), were also potential candidates for the metric that would be used to be evaluated. I decided to go with arguably the most simple metric, accuracy, because in the case of predicting a win or a loss in a League of Legends game, recall or precision does not seem to be more important or weigh more heavily.

For instance, if our model was instead a COVID test predicting whether or not the user has COVID-19 or not, recall might be prioritized because correctly identifying someone has COVID (true positive) is arguably more important than correctly identifying someone does not (true negative) because we want the positive person to take the proper precautions to maintain their health and not jeopardize the health of others around them.

In the case of precision, there is no tangible difference or worse false prediction (false positive or false negative) when it comes to wins and losses, so just using accuracy would suffice. Additionally, there are an equal amount of wins and losses in the dataset that I built the model on, so there isn't any class imbalance or skew towards wins or losses that we would have to worry about. Overall, accuracy seems to be a perfectly viable metric to use over precision, recall, or other potential metrics.

The dataset that we explore and use to create our binary classifier has *a lot* of data summarizing every game. There are statistics that summarize certain aspects of each game after it has finished, such as total kills, game length, the total number of certain objectives obtained by either team, and much more. Obviously, in the case of our classification problem - we cannot use this data. We want to predict the outcome of a game, and obviously doing so means that we would not have access to the data I just described above - it wouldn't exist while a game is occurring! 

Luckily, our dataset also provides *a lot* of that insightful data that we need! Information on gold difference, xp difference, and other statistics measuring the difference between two teams at the 10 and 15 minute mark into the game is usable for our prediction problem. Other additional information, such as 'firsts', are also extremely insightful. Our dataset provides information on which team accomplished the first of a certain objective. For example, which team secured the first blood/first kill is one of the 'first' statistics. Information that is not aggregate of the entire game and instead indicative of certain timestamps and certain moments as the game occurs is data that we can use to model and predict our outcomes.

---

## Baseline Model

I started off creating a baseline model for the prediction problem. I decided to approach the binary classification problem by using a Decision Tree - a tree that asks and answers questions in regards to the data until it is 'confident' in predicting an outcome.

My baseline model was quite simple - it only included four features. These four features were the gold difference between the team and the opposing team at 15 minutes ('golddiffat15'), the experience difference between the team and the opposing team at 15 minutes, whether or not the team achieved first blood in the game ('firstblood'), and the league that the game was played in.

Two of the features in my baseline model were quantitative - the gold difference and experience difference features. One of my features was categorical - the 'league' column in my dataset. To render this information useful, I had to quantify it. I One Hot Encoded the 'league' column, which essentially determined whether or not the game took place in a specific league for all unique leagues in question by applying 1 for yes and 0 for no. My last feature, 'firstblood', was a binary feature, so it was nominal.

After using training data to train my model, I determined the performance of my model on my testing data. The accuracy my model achieved on the unseen testing data was 0.599, or around 60%. I do not think this current model is necessarily 'good' - it can only predict a little over half of the games correctly consistently. It seems that there is a lot of room for improvement in increasing the accuracy of our model.

---

## Final Model

I added a multitude of features to my model to improve its accuracy. After combing through the data and seeing what information our dataset provided, I decided to use the following features:

#### The original quantitative features, but modified -

'golddiffat15', 'xpdiffat15' transformed into quantiles.

I decided to transform these two columns into percentiles because I thought that seeing the proportion of games the difference in these two metrics (gold, experience) compared to other games would help our model make predictions more than just leaving these two columns of data as is. Every League of Legends game is different, and the gold difference and experience difference at 15 minutes varies a lot. Transforming them into quantiles to compare how big the difference is to a typical League of Legends game could be more useful for our model to measure the impact these two metrics had on a certain game.

#### The rest of the original features in our base model, as is - 

Column names in dataset = 'league', 'firstblood'

These two features seemed to work well in our base model - the one hot encoding for league allows the model to differentiate between leagues and the potential differences in playstyle in different regions. First blood is always an important statistic - it indicates which team dealt the first blow in a given game. Getting the upper hand early in a game is integral to increasing your chances of winning a game, so I decided to keep it as is.

#### More 'firsts' - 

Column names in dataset = 'firstdragon', 'firstbaron', 'firstherald', 'firsttothreetowers'

As with 'firstblood', all other 'firsts' are extremely important as well. Acquiring the first of a certain objective in a given game gives you numerous advantages over the other team, which sets you up for success. In many games, accomplishing these 'firsts' can allow you to 'snowball' a game, which means that your newfound strength over the other team continues to grow at a faster and faster rate because you are continually getting stronger than the other team because of the fact that you are already stronger than them.

In simpler terms - I decided to go with these four columns specifically because they provide different advantages.

Acquiring the 'firstdragon' of a game gives every player on the team bonus stats - whether that be damage, speed, defense, or other more intricate benefits. This gives the team that acquires this an edge over the enemy, increasing their chances of winning.

The same goes for the 'firstbaron' and 'firstherald'. Acquiring the 'firstbaron' means that you get a temporary buff that gives you a great amount of firepower to siege the enemy base, getting closer to victory, and the herald gives the team a strong ally that does the same, but on a smaller scale.

The feature 'firsttothreetowers' is a great indicator for the team that has acquired map control. Having control of the map means having control of the objectives and resources on the map, which greatly contributes to success and victory on the rift.

#### 'side' -

In League of Legends, there are two sides of the map: the Blue and Red Side. Each team starts on one side and tries to siege the side of the enemy team. The map is not symmetrical - certain objectives are catered towards being on a certain side, which means that each side has its own advantages and disadvantages. The blue side is commonly considered the better side - many believe the geometry of the map aids the blue team in securing certain objectives, such as the ones I talked about in the 'firsts' section, like dragons.

#### Standardized difference in kills at 15 minutes - 

This data was not immediately available in the dataset, and I had to do some programming to be able to compute it and use it in my model! I thought that this would be a great feature that would help my model because as tactical as League of Legends is with objectives and map control, getting kills is also extremely important and plays a role in attaining said goals. Killing enemies on the opposing team means more gold, more map control, and more time on the map to secure objectives - all of which are ultimately stepping stones towards victory (or defeat if the team is on the other side of the fighting). I wanted to standardize the data because different games have different amounts of kills, and standardizing it helps mitigate the variance that might occur in the difference of kills at 15 minutes in different games.

I believe all these features helped improve the model of my performance because they provide more insight to what the teams are accomplishing during the game. My base model had features that focused on the laning phase and overall early stages of a game, and the one I improved and just described goes beyond that and includes features that are focused on later stages of the game, which may be more impactful than the beginning of a game.

### Model and Hyperparameters

Using a Decision Tree for my model opens up to more decisions I had to make (that was a great pun) - I had to choose the hyperparameters for which my Decision Tree would use, which are parameters that we tune to improve the accuracy and effectiveness of our model. The hyperparameters in question were 'max_depth', 'min_samples_split', and 'criterion'.

I wanted to find the best hyperparameters for these three because I felt that they would contribute the most to improving my model and making it the best it could possibly be. 

'max_depth' refers to the maximum amount of 'questions' the Decision Tree can ask before having to come to a conclusion. By finding the best number 'max_depth' can be, it allows my model to not overfit to the training data and be able to generalize well to unseen data. It allows my model to find a balance between fitting to the training data but also being able to make accurate predictions on unseen data.

As for 'min_samples_split' and 'criterion' - these two help determine the best way to split the tree into smaller questions. Finding the best value for 'min_samples_split' and 'criterion' would be another great way to improve my model.

The method I used to find the best hyperparameters was by using a GridSearchCV object. What this does is it takes in a list of hyperparameters, and fits itself to a model and training data and computes the accuracy for every single combination of every hyperparameter a certain number of times (can be determined by us). After applying this to my training data and giving my searcher different hyperparameters, the best hyperparameters for my model ended up being:

'max_depth': 5, 'min_samples_split': 100, and 'criterion': 'entropy'

After improving upon my model with many new features and hyperparameters, I put it to the test! The performance my new final model had on the unseen training data had an accuracy of 79% - essentially a 20% improvement in the model compared to the base model!

## Fairness Analysis

If we look back at the model, we can see that it places a lot of emphasis on the 'first' objectives. This is because teams who acquire a majority of the 'first' objectives tend to capitalize on their lead and win the game before the enemy team has a chance to recoup and rally a comeback. 

But is our model as accurate on longer games? The longer a game goes on, the more stable a game is, and more often the two teams are in a stalemate. This means the impact of these 'firsts' diminishes as a game goes on - the opposing team has likely caught up to the team that acquired those 'firsts' by then and negated those advantages. Naturally, one can infer that these 'firsts' are not as important for games that are last longer than average.
In situations where games lasted longer, would our model still be as accurate? 

To answer the question, I did a permutation test with a significance level (alpha) of 0.05 to come to a conclusion.

#### Grouping games into 'long' and 'non-long' (regular) games

I decided to test my model's accuracy on both long games (call this Group X) and non-long (regular games) (call this Group Y). After computing the mean game length and seeing that it was around 1930 seconds (32 minutes), I decided to classify a game as long if it was longer than 2160 seconds (36) minutes.

With this, I decided to evaluate whether or not my model was fair with the metric of accuracy and the test statistic (accuracy of long games - accuracy of non-long (regular) games), which I would expect to be negative.

#### Null Hypothesis

Our model is fair. Its accuracy for games that are long and games that aren't long (regular length) are roughly the same, and any differences are due to random chance.

#### Alternative Hypothesis

Our model is not fair. Its accuracy for games that are longer is lower than its accuracy for games that are not long.

#### Permutation Test Conclusion

After conducting the permutation test, I came up with a p-value of 0.0. This means that none of my simulated differences in accuracy were as low or lower than my observed statistic.
