# Information Operations: A Comparative Analysis of Congressional and State-Backed Influence Tweets

By: Patrick Aquino, Adam Imran, Thomas P. Malejko, and Douglas Post <br> <br>

Georgetown University <br>
Analytics 502: Massive Data <br>
Dr. Marck Vaisman <br>
5 May 2020 <br>




## Introduction, Background, and Motivation


 Much of what western observers consider warfare is based upon the philosophies of Carl von Clausewitz—a 20th Century military theorist—who believed that war was the extension of politics by other means; a contest between two sovereign states seeking political gain. The ultimate objective of this warfare was to secure a physical victory on the battlefield such that the enemy was forced to capitulate. The victor&#39;s political demands forever enshrined in the end-of-war treaty—much like the Treaty of Versailles captured the demands of the Allied Powers at the conclusion of World War I. However, the post-World War II security construct largely made this form of warfare either illegal or too costly to pursue. As a result, warfare underwent a slow mutation, becoming less kinetic in the process. The first mutation—guerilla conflicts like the Vietnam and Nicaraguan Contra Wars. The next, proxy-backed insurgencies like the recent conflicts in Iraq and Syria. Finally, information warfare. While the desire to misinform, deceive, and otherwise non-kinetically engage an enemy force is as old as war itself—the modern manifestation is vastly different from the fabled Trojan Horse (a prominent example of information warfare). Whereas historical information operations sought to enhance military operations on the physical battlefield, modern incarnations of these operations are now the principal form of warfare. Largely fueled by the internet&#39;s rapid ascendancy, individuals and states seeking to deceive a population now have the unprecedented ability to do so with just a few clicks (Patrikarakos, 2017). Russian interference in the 2016 United States Presidential Election and foreign influence campaigns during BREXIT are just two contemporary examples where foreign actors desired to influence the political affairs of another, sovereign state. As a result, politicians have an increasing responsibility to communicate with their constituents to provide a source of truth amid a glut of disinformation and fake news.

 Twitter, a microblogging and social networking service, is one of the principal tools leveraged by those engaged in information warfare due to its popularity and simplicity. Both, those seeking to inform and deceive, have the ability to share 280-character &#39;tweets&#39; with billions of people—spread across the world—with just a few clicks. Therefore, the need to separate reality from fiction, truth from falsehood, and facts from lies on this platform is a non-trivial pursuit. According to the Rand Corporation, &quot;the United States has a pressing capability gap in its ability to detect malign or subversive information campaigns…before these efforts substantially influence the attitudes and behaviors of large audiences&quot; (Marcellino et al., 2020, p. ix). This challenge is so perverse, that the United States Congress has implored social media companies—including Twitter—to disrupt these foreign information operations directly (_Senate: Foreign Influence Operations_, 2018, pp. 2–3). One such effort to detect information operations on social media involves the use of community lexical analysis, which combines network and textual analyses to understand social network structures (Marcellino et al., 2020, p. 9). Building upon this methodology, we sought to identify any textual commonalities or differences that exist between the tweets of congressional leaders and those who attempt to deceive the American public for their own benefit.

## The Data

**The Raw Data**

 To conduct this analysis, two massive datasets were used—Tweets of Congress and Twitter&#39;s Information Operations. The Tweets of Congress dataset is the front-end of a much larger project that collects the daily tweets from more than 1,000 office, committee, and party Twitter accounts for both houses of the United States Congress. The dataset is broken down into daily JSON files that begin in June 2017 and conclude in December 2019—the end-date for this study (Litel, 2017/2020). Twitter&#39;s Information Operations dataset was originally released in October 2018 at the behest of the United States Congress to provide regular updates about the company&#39;s efforts to combat foreign-influence campaigns. The dataset consists of numerous comma-separated value files, broken down by date, country of origin, and type (account information, tweet information, and media). The data extracted for this research spans from June 2015 to March 2020 and contains tweet information about suspected state-backed information operations from numerous countries, including Russia, Bangladesh, Iran, Saudi Arabia, and Venezuela.

**Data Wrangling and Munging**

 Once the data was loaded into a public Amazon S3 Bucket, the first step was to align the timeframes of the two data sets. The Information Operations dataset was aggregated and filtered to only tweets from June 01, 2017 to December 31, 2019. This made the dataset substantially smaller and a somewhat easier to work worth; however, still well over 25 gigabytes.

 Since the overall plan was to do textual analysis with this data, we had to decide how to handle suspected information operations tweets in foreign languages because we sought to compare the two datasets side-by-side. A large number of the accounts in the suspected information operations database were from other countries and, subsequently, many of those tweets were in other languages. The congressional tweets dataset was, on the other hand, entirely in English. Thus, the information operations dataset was filtered again for only observations having a _tweet\_language_ of English. This trimmed that dataset to approximately 10 gigabytes in size, which was much more manageable size for training a topic model, when combined with the 1.2 gigabytes from the congressional tweets dataset. Additionally, any observations with null values in the _tweet\_text_ column were dropped as well as observations with the same _screen\_name_ and _tweet\_text_. While the number of times an account tweets the same information is definitely a relevant piece of information, duplicates would only serve to skew the results for topic modeling. After all wrangling, cleaning, and merging of the datasets was complete, the final dataset was roughly 10 gigabytes in size.

## Exploratory Data Analysis

 **General Analysis**

 As part of the initial analysis, preliminary statistics and plots were generated to gain a better understanding of any trends or themes worth further investigation. Since the data was largely text-based, traditional summary statistics would not provide appropriate insights. As a result, the data was aggregated and plotted to achieve this effect. The first attempt to understand the data was to plot the daily tweet frequency for each dataset which revealed some interesting trends. The plots are below:

![Figure 1](https://github.com/dyp6/502FinalProjScripts/blob/master/sn8iApZ_kyJFi83rBYoEPBA.png)
_Figure 1. Daily Tweet Frequency for the Congressional (left) and Information Operations (right) Datasets with notable events highlighted on each plot._

 First, congressional tweeting habits tend to follow a steady rhythm throughout the year, which includes a dramatic spike in activity around the president&#39;s annual state of the union address and an expected holiday lull (with the exception of the 2019, which was dominated by tweets about the House Impeachment process). Attempts to maliciously influence the American public, however, appear to have undergone a steady decrease in frequency since the Unite the Right Rally in August 2017 (this could be due to increased scrutiny and twitter&#39;s efforts to remove such content from its site during this period as well). Another interesting observation in the information operations dataset was increased activity in the months leading up to President Trump&#39;s impeachment proceedings (beginning mid-2019). The next interesting trend identified during this analysis was the overwhelming prevalence of congressional tweets during the late afternoon and evening. Unfortunately, this unique finding could not be compared to the information operations dataset because it lacked a time zone element in _tweet\_datetime_ to allow for an appropriate comparison.

![](RackMultipart20200503-4-61xuvo_html_dbc0262aa03989b2.png)

_Figure 2. Frequency of congressional tweets by time of day—the daily sum is 100 percent_

Finally, we compared the frequency of tweeting between the two groups by time of day. Unsurprisingly, congressional tweets varied greatly depending on the day with a noticeable decrease in tweet frequency of weekends. Conversely, information operations tweets occurred with near-uniform frequency throughout the week. The resulting plot is below:

![](RackMultipart20200503-4-61xuvo_html_fc36f5bf49a85628.png)

Figure 3. _Frequency of tweets by day of the week—each category (congressional and information operations) sums is 100 percent for the entire week._ 

**Natural Language Processing**

 Based on the nature of these datasets, Natural Language Processing (NLP) is a natural method for exploration as it provides insights about common words, distributions, and differences between years. Through techniques learned in Analytics 502 (and independent research), this process ultimately set the stage for a more in-depth analysis. The first step in this process was to pass the tweets through a pipeline containing: normalization; lemmatization; tokenization; and stop word removal. Next, the text was exploded to see all the words from the lists created by the pipeline. After that, frequency counts were generated to see what words occurred most frequently and were the most informative. Finally, the word counts were visualized in a bar chart (containing the top 15 words) and a word cloud containing the top 100 words.

 Using the process detailed above, the congressional tweet NLP visualization is seen in _Figure 4_ . Even though the 2017, 2018, and 2019 visualizations came from the same dataset, it was useful to annualize the word counts to understand how congressional tweeting habits varied from year to year. These charts highlight that congressional tweets tend to focus on legislative matters like bill, health care, family, work, tax, house, and senate. That being said, the yearly word counts do highlight the notable events of the year. In March 2017, _The American Healthcare Act of 2017_ was passed, so it makes sense that health and care appeared often in congressional tweets during that period. In the 2019-word-cloud, &#39;impeachment&#39; is displayed, which also makes sense because of the large public discussion of that matter. Interestingly, &#39;Trump&#39; was in the five most frequently used words during each year of this study.

![](RackMultipart20200503-4-61xuvo_html_ba00fc977a6bdc66.gif)
![](RackMultipart20200503-4-61xuvo_html_4989f83b134bad98.gif)

_Figure 4. Frequency graphs and word clouds for congressional tweets from 2017 to 2019 (left to right)._

Now compare and contrast the analysis completed above with the analysis of Suspected Information Operation Tweets (below):

![](RackMultipart20200503-4-61xuvo_html_2757d78ff0d19ea.gif)
_Figure 5. Frequency graphs and word clouds for info Ops tweets_

The word cloud and bar chart of these potentially malign-intention tweets show references to many of the same words as the congressional tweets with some notable differences—emotion-filled words, contentious topics, and specific groups of people. From these visualizations we can gather that angry words are common—such as attack, kill, and leave—but on the other side positive-emotion words like happy and love are also frequent. Similarly, contentious words like Obama, Hillary, Trump and news also appear among the top words in this dataset. Finally, specific groups that cause emotional responses for certain segments of the American population (such as Muslims, police, and ISIS) are prevalent.

## Methodology

 The main method used in this project is Latent Dirichlet Allocation (LDA), however all of the preprocessing and preparation for LDA will be discussed in detail for the remainder of this section. LDA, as well as most natural language processing methods, often works best when the text is tailored to the actual process of the algorithm. LDA uses word frequencies with term-document frequencies to find documents similar to each other in the corpus and classify them into a pre-specified number of topics. Term-document frequencies represent the amount that a term appears in separate documents, while term frequencies alone represent the overall frequency that a specific term is used in the corpus. The process of LDA, that was just described, necessitates text modification so important terms are not drowned out by common words, which are especially prevalent in a corpus of tweets.

 There are a number of pre-made stop word sets, and we used the one from PySpark&#39;s NLTK package. Stop words sets remove some of the most common words—like a, the, it, and, so, but, etc.—as well as words that have no relevance to the overall topic of a document—like then, after, they, there, because, etc. No set of stop words is perfect, so throughout the monthly topic modeling process, which is described below, words that seemed to be overpowering with little or no meaning, and groupings of letters that do not make sense were added to the set of stop words. The final set of added stop words is pictured below.

![](RackMultipart20200503-4-61xuvo_html_7d1a96ad21aeeb0d.png)
_Figure 6. Terms added to the standard NLTK stop words set._ 

 Once the set of stop words was finalized, the final preprocessing pipeline was used to transform the text data into a more suitable form for LDA. This pipeline included tokenization of the words for each document, a normalizer that changes all words to lowercase, and a lemmatizer that takes each word and trims it down to only its root form. Once that is complete, and before the topic modeling begins, the Term Frequency (TF) and Inverse Document Frequency (IDF) were computed in a process often abbreviated to TF-IDF vectorization. These values are input to the LDA algorithm, and topics are assigned based on the probability that the words in a document are from each topic. Topic lists were computed for each month from June 2017 to December of 2019 for both the congressional tweets and the information operations tweets datasets. A number of trials were run on both datasets to try to find an optimal vocabulary size, number of iterations, and minimum number of appearances for terms. A cross validation with grid search would have been used but it was too computationally expensive to evaluate on the full data set. The results of this process will be discussed in the results and conclusions sections.

 The next objective evaluated for this project was to see how well this LDA model could distinguish between the two types of tweets—congressional or suspected information operations. For this task, the vocabulary size and minimum term frequency were both increased to account for the greater size of the corpus, but other parameters remained the same. Then the model was run to search for two topics, in hopes that it could effectively separate information operations and congressional tweets. The results of this process will also be discussed in the following sections.

## Results and Interpretations

**Monthly Topic Modelling**

 The results of the monthly topic model were compiled and inputted into a timeline in order to make examination easier. The timeline can be viewed in either two or three dimensions at this [1](#sdfootnote1anc)[link](https://www.tiki-toki.com/timeline/entry/1420338/Congressional-and-Information-Operations-Monthly-Tweet-Topics). In 3D, the topics are visible without the need to click and open them.

 The top ten words for each topic are displayed in this timeline. There are four topics for each tweet type, resulting in eight different topics per month studied in this analysis. The topics for the congressional tweets are often distinct and tend to highlight the popular political issues of the month or period, like health care legislation, immigration reform, or Unite-the-Right Rally in Charlottesville. For example, in September 2018 the topic modelling process effectively identified and separated the two most preeminent events of the month—the Justice Kavanagh confirmation hearing (and associated events) and general patriotism associated with the September 11, 2001 memorial ceremonies. Similarly, in September 2019, this process separated the three biggest topics of the month—calls for President Trump&#39;s Impeachment, student-led climate protests, and the United States–Mexico–Canada Agreement (or new NAFTA). Conversely, the topics generated from the information operations dataset had much less to do with current events and more to do with miscellaneous subjects of discontent. For the information operations topic, each month generally contained one current events topic like the Unite-the-Right Rally in August 2017, NBA Conference Finals in Mary 2019. Curiously, there are a number of topics constructed around gamer terminology (such as xboxsupport, gamertag, and dm) and porn material (money, sex, big, tasty, im, etc.). We suspect that these topics are derived from messages attempting to either recruit or target their message to potentially vulnerable populations—those who are typically isolated.

**Predictive LDA**

 The next process was the attempt to distinguish between the two types of tweets using the LDA model that was tuned during the monthly topic modelling process. This algorithm was given two topics to find, in hopes that it would offer some separation between the two types of tweets. The resulting model classified the tweets into two topics, and the full results are shown in _Figure 7_ (below).
![](RackMultipart20200503-4-61xuvo_html_58329320ecc7a89b.png)
![](RackMultipart20200503-4-61xuvo_html_29dddc060cd1c5ac.png)

_Figure 7. Detailed output for the predictive LDA process._

 As seen in the figures above, the LDA model did not effectively separate the two tweets into two distinct topics. A few key notes to note. First, congressional tweets were easier to categorize—about 78% in Topic 2 and 22% in Topic 1. This could be due to the largely legislative focus of that group&#39;s tweet corpus. Conversely, the information operations tweets were uniformly distributed across the two topics, which highlights the challenges of identifying attempts by bad actors who desire to influence the American public for their own benefit. As was identified in the NLP EDA, &#39;Trump&#39; was a common term in both search topics.

## Conclusions

 As detailed above, Latent Dirichlet Allocation (alone) is not sufficient to predict whether a tweet originated from a congressional leader or a foreign actor attempting to maliciously influence the American population. Therefore, only limited conclusions can be drawn from this study. First, tweets originating from foreign actors engaged in information operations tend to use evocative, emotionally charged language targeting vulnerable populations (gamers, porn viewers, and other isolated groups). Additionally, these foreign influence tweets tend to profuse extremely conservative ideologies. These observations differ greatly from the majority of congressional tweets, which tend to have more moderate content and focus on their activities and current legislation proposals. Even the time of tweeting varies greatly between the two groups, with congressional tweets typically originating on workdays while foreign influence tweets are uniformly distributed across the week. Second, President Trump is clearly an important figure as his name appears frequently and is among the most common words in both datasets. Finally, despite the LDA inability to fully differentiate between tweeting groups, it was very effective at separating political topics with limited overlap. Therefore, LDA holds some potential for identifying foreign influence operations, however it needs to be combined with other techniques (such as network analysis—as was used in _Detecting Malign or Subversive Information Efforts over Social Media_) before it can be used for more definitive purposes.

**Future Research**

Future research into this topic could include:

1. Compare President Trump&#39;s tweeting habits to both datasets to identify any commonalities or differences. Note: On 1 May 2020 (as this paper was being finalized), the Washington Post published an article about a Democratic group using a technology designed to identify ISIS propaganda to counter some of President Trump&#39;s coronavirus messaging (Stanley-Becker, 2020). This article could serve as an interesting starting point for this type of future research, but at a minimum highlights the relevance of this, and similar types, of research.

2. Further evaluate the extent to which foreign influence operations use of gamer and porn language, tags, and links to attract followers.

## References

Litel, A. (2020). _Alexlitel/congresstweets_ [CSS]. https://github.com/alexlitel/congresstweets (Original work published 2017)

Marcellino, W., Marcinek, K., Pezard, S., &amp; Matthews, M. (2020). _Detecting Malign or Subversive Information Efforts over Social Media_ (p. 67). Rand Corporation. https://www.rand.org/pubs/research\_reports/RR4192.html

_Open Hearing on Foreign Influence Operations&#39; Use of Social Media Platforms_, United States Senate, 2nd Session, 115th Congress, 171 (2018). https://www.govinfo.gov/content/pkg/CHRG-115shrg31350/pdf/CHRG-115shrg31350.pdf

Patrikarakos, D. (2017). _War in 140 Characters: How Social Media is Reshaping Conflict in the Twenty-First Century_ (First Edition). Basic Books.

Stanley-Becker, I. (2020, May 1). _Technology once used to combat ISIS propaganda is enlisted by Democratic group to counter Trump&#39;s coronavirus messaging_ [Newspaper]. Washington Post; Washington Post. https://www.washingtonpost.com/politics/technology-once-used-to-combat-isis-propaganda-is-enlisted-by-democratic-group-to-counter-trumps-coronavirus-messaging/2020/05/01/6bed5f70-8a5b-11ea-ac8a-fe9b8088e101\_story.html

## Appendix A


All code used in this project can be found in the following GitHub Repo:

[https://github.com/dyp6/502FinalProjScripts](https://github.com/dyp6/502FinalProjScripts)

## Appendix B

**Division of Labor**

Patrick Aquino: Exploratory (Congress Tweets) | Research | Paper Edits

Adam Imran: Natural Language Processing EDA | Natural Language Processing Visualizations (Frequency Charts and Word Clouds) | High level NLP Analysis | Storytelling

Thomas Malejko: Project Management | Data Sourcing | Nontechnical and Background Research | Preliminary (Traditional) EDA | LDA Analysis Support

Douglas Post: Data Sourcing | Data Ingestion, Cleaning, and Storing | Monthly Topic Modelling | Predictive LDA | Resident PySpark and Cloud Computing Guru


[1](#sdfootnote1anc) If this link is disabled by Canvas, the direct link is: https://www.tiki-toki.com/timeline/entry/1420338/Congressional-and-Information-Operations-Monthly-Tweet-Topics


