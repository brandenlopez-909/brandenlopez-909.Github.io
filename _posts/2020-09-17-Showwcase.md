# Showwcase Data Analysis: User Engagement

## I. Introduction and Motivation
Showwcase is a platform tailored to the tech community, in Showwcase a user can share projects and explore others. Showwcase provides many ways to interact and in the data set provided for October 2019, "user engagement" is tracked by logins. Events within each login duration are recorded. With the aim of understanding user engagement in Python, we must first define "user engagement". Deriving my definition of user engagement from [Mixpanel](https://mixpanel.com/topics/what-is-user-engagement/), user engagement is defined by interactions such as likes and comments, and time actively spent the platform.

User engagement is important for brand expansion and increasing revenues. It is because of this, metrics on how users are engaged are imperative to the success of platforms.  

## II. Data and Exploration 

### 1. Data Enhancement
Using libraries from Python, data integrity was verified and data enhancement began. The data set can be manipulated from existing variables, one thing that I noticed was multiple logins. Using user identification numbers and date of login, we are able to produce a new user engagement metric that tells how many logins a user had each day. This is useful because multiple logins could be used to indicate addictiveness. 

In addition to this, the descriptions of each variable led me to believe that a true active time on the platform could be found. To produce this new variable, we subtract  **inactive_duration** from **session_duration**, only to find many negative values in the new variable. This happens because the sum of **session_duration** and **inactive_duration** are most likely the total time. Thus, the definition of **session_duration** should be changed from, "number of seconds a user was logged for that session" to "number of seconds a user was actively logged for that session". It's important to know that this speculation and many other reasons could attribute to negative values. For instance, the algorithm for recording times could be broken. 

After implementing any controllable user enhancements, I began to think of variable categories and my time on Showwcase. The variable categories generalize into 4 things: unique identifiers, profile updates, user-to-user engagement, and time spent. 

The only metric to record a user updating their profile is **projects_added**. Despite this, Showwcase allows the user to update the profile by updating external links, profile pictures, badges, experiences, articles, and an about me section. In addition, the data set's user-to-user variables are limited to likes and comments and can be expanded to: followers added, visited user pages, and clicks on external links. Lastly, we don't know how long a user engages in profile updates, reads projects, articles, and explores other user profiles. By adding all the mentioned variables, user engagement can more be more efficiently tracked. 

### 2. Data Exploration 
Using Python, the data was visualized and descriptive statistics were explored. Since the data set is small, exploration for seasonality trends is ignored. Instead, I aimed to explore the relationship of each variable. The heat map in **Figure 1** shows that the correlation between variables is weak. 

![Figure 1](Pictures/HeatMap.png)


A weak correlation rules out modeling with linear regression. More so, since the sample size is small (N=299), modeling should be refrained from. In **Figure 2** the data is plotted against one another and we view distributions. 

![Figure 2](Pictures/Figure2.png)

It's clear why there is a weak correlation and in this figure, we are able to see possible distributions. Every metric with the exception of **session_duration** is grouped near one and appears to follow a logarithmic distribution, while **session_duration** is approaching normal distribution. These insights are valuable when N increases and modeling is then capable. 

### 3. Descriptive Statistics 
While visualization provides a quick way to process information, descriptive statistics are best at telling the story of the data. In the table below, we view the average amount of engagement each variable received.
|Variable               |Average amount|
|-----------------------|--------------|
|session_projects_added |      1.618729|
|session_likes_given    |     10.458194|
|session_comments_given |      2.408027|
|inactive_duration      |    731.638796|
|bugs_in_session        |      1.237458|
|session_duration       |   1190.414716|
|login_amount           |      1.280936|

In the above table, we see that the average user spends 20 minutes engaged, likes 10 items per session, and comments on a fifth of those. While there is a lot of good, two negative items are present. The average user is inactive for 12 minutes and encounters at least one bug. A reason for the high inactivity could be how **inactive_duration** is recorded. Technolgy reports are often dense in information and require more time to read than a Facebook post. If a user is a slower reader they might log as "inactive" for short periods, but this is speculation and is not bolstered with data. Instead, a quantitative reason could be bugs. 

**Figure 2** shows that the most inactive time occurs without a bug. In fact, the average user who does not encounter a bug will spend 10 minutes and 40 seconds inactive. This is only a minute and 20 seconds difference. 

Lastly, we explore the chance of events occurring in the table below. 
|Variable         |Chance of occurring|
|-----------------|-------------------|
|projects_added   | 0.7558528428093646|
|likes_given      | 0.7123745819397993|
|comments_given   | 0.7491638795986622|
|inactive_status  | 0.5719063545150501|
|bug_occured      | 0.3879598662207358|
|multiple_sessions| 0.2508361204013378|

There are many ways of interpreting the above table and many of those ways lead to similar results. Approximately 75.5\% of logins had users adding a project. While the table shows many goods of user engagement, two negative statistics stand out. **inactive_status** and **bug_occured** are at 57.1\% and 38.8\%. Having **inactive_status** at a majority of people only strengthens my assumption that inactive time is a common occurrence by how it's defined. What is extremely concerning is that almost 40\% of users encountered a bug. While it is not a majority, [88% of users reported they would abandon an app if they encountered bugs or glitches](https://www.applause.com/blog/app-abandonment-bug-testing#:~:text=A%20survey%20of%20over%201%2C000,least%20one%20bug%20every%20day.). To illustrate how impactful this I will put in the context of a statistics problem. 

![Equation](Pictures/Equation.PNG)

Thus bugs should be fixed as a substantial amount of Showwcase users might leave the platform. 

## III. Results. 

Since no data prior to October 2019 is provided, there is no benchmark to compare the results, but we found high rates of engagement. All positive user engagement occurs at least 70 percent of the time. More so, the average user spends 20 minutes engaged, likes 10 posts, and comments on 2 of them per session. With high engagement statistics, Showwcase is on its way to growing and increasing revenues. A few bad results stand out, there are high inactivity rates and times, which is possibly the result of how inactivity is measured, and bugs are frequent in the platform. If fixed, Showwcase can minimize inactive time and users leaving. 

## IV. Conclusion
Showwcase data for October 2019 shows that user engagement is can be measured through likes, comments, time, and profile updates. These are just a few metrics that can be used to better understand user engagement and the metrics should be expanded upon. Data exploration found that user engagement is good and opened us to areas of improvement. Bugs are ramped in Showwcase and the true reason inactive time is high needs identified. 

**Note**: The Python code to form this analysis is presented in a Juypter Notebook which is accessible at my Github. 
