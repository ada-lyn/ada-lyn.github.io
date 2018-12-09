---
layout: page
title: Detecting Bias in Amazon reviews
subtitle: By ADA-LYN
published: true
---

# Introduction

### Abstract

In the past, when buying an item, one had to trust reviews in newspapers or from friends. In today's age, with online shopping, we have to tap into the minds of thousands of people who have purchased the product we are thinking about. With the help of Amazon reviews and their star-system, we can easily analyse how good the product likely is. But while a newspaper or professional reviewer is generally working hard for consistency and unbiasedness, these facts are not given for a general public reviewer writing a comment. With help of the Amazon dataset, we will try to find bias in the reviews, in order to possibly give an idea on whether or not, and if so how, to correct a bias. We will be especially interested in the influence of the following factors on the number of stars given:

- The date the review was written on?
- The category of the product the review was written for?
- The country of residence of the buyer (for instance, was the product bought on Amazon US or Amazon UK?)
- Past ratings from the reviewer

### Dataset

We will use an [Amazon Dataset](http://jmcauley.ucsd.edu/data/amazon/) that consist multiple millions of Amazon reviews. More precisely, it contains (amongst other information that will not be used) the following information for each review: 

- `marketplace` : The "country of Amazon". Data is available for the United Stated, United Kingdom, France, Germany and Japan
- `customer_id` : A unique id representing the user who wrote the review
- `product_id` : A unique id representing the product reviews
- `product_title` : The name of the product
- `product_category` : The category of the product. The following categories are available: *Wireless, Watches, Video Games, Video DVD, Video, Toys, Tools, Sports, Software, Shoes, Pet Products, Personal Care Appliances, PC, Outdoors, Office Products, Musical Instruments, Music, Mobile_Electronics, Mobile Apps, Major Appliances, Luggage, Lawn_and_Garden, Kitchen, Jewelry, Home Improvement, Home Entertainment, Home, Health Personal Care, Grocery, Gift Card, Furniture, Electronics, Digital Video_Games, Digital Video Download, Digital Software, Digital_Music_Purchase, Digital Ebook Purchase, Camera, Books, Beauty, Baby, Automotive and Apparel*
- `star_rating` : The number of stars given by the reviewer, between 1 and 5
- `helpful_votes` : Other users can vote on the usefulness of a review (either positive or negative). This represents the total number of positive votes
- `total_votes` : This is the number of positive and negative votes combined
- `verified_purchase` : If Amazon is able to check that you indeed bought this exact product, with the same price, your review will be marked as "verified"
- `review_headline` : Short summary of the review
- `review_body` : Full content of the review
- `review_date` : Date the review was posted on

Since we have a lot of data (Amounting to ~20GB), our main focus will be on the reviews related to the *Books*. This is also by far the largest dataset and the one Amazon is most known for. We will do an in-depth analysis of these reviews, and then rapidly compare it to the other categories.

### Research Questions

There are two main questions we will try to answer during this project: 

- What factors influence the rating given in an Amazon review (other than product quality)? 
- Is there a way to correct this bias, should we correct this bias?

### Motivation

Our goal is mostly to provide useful information to Amazon itself and to the products sellers. So that they can have a better idea of what a given review value really should be.
For instance, they might receive a very bad review from someone and not understand why they had such a bad rating. But after bias correction it could be possible that the reviewer is just a "hater" (someone who always gives bad rating to every product) and that instead of a one-two stars it could be worth a 3-4 stars. There could be other factors that influenced the rating of that review and we will try to best correct them to valuable information.

We do not think it would be a good idea to correct every review and publicly change the displayed score. This could lead to many people being angry at Amazon for thinking their opinion is biased. Also once a metric is known, it becomes very easy for ill intentioned people to find a way to exploit it. They could then always increase/decrease as they want by carefully using the bias correction in the other way. 

### General Description of Data

Before focusing our study on *Books* only, we will take a quick look at the complete dataset, and see if we can see any interesting effect. We will, for each of the available category, look at the number of reviews available. Note however that this might not be entirely representative of the true repartition of the categories, since we do not know what process Amazon used to select those reviews. 

![Number of review for each category](/img/other/n_by_category.png)

Note that we used a logratihmic scale, so the proportions are distorded. Indeed, some of the smallest categories (*Digital Software*, *Major Appliance*, P3ersonnal Care Appliance* for instance) have so few reviews compared to the larger ones (*Books*, *Ebooks*, *Wireless*) that they would be invisible on a linear scale. The results are mostly expected, although some categories are ranked higher than one might expect them to be (e.g. *Video DVD*, when they mostly have been replaced by streaming services, or *Wireless* that is ranked way higher than *Electronics*, even if it's only a subcategory of **the other / NOT SURE ABOUT THE PHRASING**) 

We also took a quick look at the average rating (the number of stars given by the reviewers) of each category. 

![Average rating for each category](/img/other/avg_by_category.png)

As you can see, there is quite a non negligible difference between the highest and lowest rated category. The rating for the top three categories (*Gift Card*, *Digital Music* and *Music*) is expected, since it is quite hard to find negative points about a gift card (it cannot really differ from the promised product, unless there is some kind of error with the card, like having been sent a damaged one). And for the Music categories, people usually know what artist they like, and they probably won't be surprised by the quality of the product.

We can also find some logical explanations for the lowest ranked categories. *Software* and *Digital Software* are clearly more niche products, often with a higher price tag, intended for businesses or professional individuals. Because of the type of product, it is also way harder to know exactly what to expect (You will not be able to judge the quality of a software by looking at screenshots, notably you won't see any bugs that might affect your experience negatively). You will more frequently be disappointed when your expensive software does not work as expected, and you can not just use the warranty to exchange it with a better one. 

To answer to our main question: *Is there some bias and should we correct it?*, we think that indeed there is some (People are clearly biased because they buy music that they know they will probably like), but it also seems that some categories (mainly *Software*) are really of worse quality than the other. For this reason, when we will try to correct bias later, we will only do it for one category, independently from the other. 

We will now, for the *Books*, take a deeper look at some of the features available.

# Effects on Rating

This part will be separated in two. We will first do an analysis by category (as said before in depth for the *Books*, and then compared to other categories), and then by country. 

### By Product Category

First, we will analyse some of the features (`verified_purchase`, `helpful_votes`, `total_votes`, `review_date`, numbe of reviews per product, `review_headline` and `review_body`), then we are going to discuss their potential influence in the bias, and if they should be considered, and finally we will compare the results with the other categories.

####  Analysis of some features

**Analysis of the `verified_purchase` feature**

This might have an influence, since non-verified purchases won't be as reliable since poeple might not have actually bought the product. We have splited the dataset into verified and non verified purchases, and for each category we plot the average rating per year, and the number of reviews per year.

<p float="left">
  <img src="/img/other/verified_by_year.png" width="99%" />
</p>

We can clearly see, with a 95% confidence interval, that the average rating is higher for verified reviews in recent years. This is difficult to guess what could have caused it to change along the years, possibly one or more modifications in the condition to be a verified purchase. It would be good to ignore all unverified reviews, to have more reliable ratings.

There is however a problem with that. By looking at the evolution of the number of reviews over the years (graph on the right just above), we realized that in the earliest years there are almost no verified reviews. This is probably due to the fact that this functionality was added quite late, or that maybe it used to be harder to be a verified review. Hence, we decided, despite the difference of rating, to keep all reviews, in order to avoid losing so much data for the earliest years.
It is clear that a lot of data is missing in the 2000's if we limit ourselves to the verified reviews. From now on, we will use all the data.

**Analysis of the `helpful_votes` and `total_votes` features**

<p float="left">
  <img src="/img/products/helpful_vs_number_Books.png" width="49.5%" />
</p>

We can see that people tend to give more positive votes than negatives ones on reviews. Possibly as the users tend to give good reviews on *Books* (~4.3/5 on *Books* and overall good ratings on other categories as well), they will also tend to positively rate other things such as other people's reviews.

<p float="left">
  <img src="/img/products/helpful_vs_rating_Books.png" width="49.5%" />
</p>

This graph may seem a bit strange at first, wondering why people would mostly positively rate reviews with high ratings only. However it can easily be explained: on any product it makes sens to assume a user will usually give a positive vote to a review if it is somewhat close to that user opinion on the product. And since the average in *Books* is very high, most of the reviews with high ratings will be close to the product average on average, meaning the closer a review's rating is close to the general opinion (= product average), the more likely it is to get positive votes.
The same explanation can be applied in a reversed way for reviews with lows ratings that tend to have lower helpful ratio.

To try to correct bias later, we can use this helpful ratio to indicate how much a review should be trusted and less biased corrected. The higher helpful ratio it has, the more probable it gets to be what the average person thinks, hence not needing bias correction.

Hence the graphs of most of the categories look similar but the categories with a bad average rating (such as *Digital Software* with average ~3.55/5) will have a more flat or even reversed curve. It can be explained by the same kind of reasoning but inversed: reviews with high rating will be far from the average product rating, hence people will view the review as less accurate. Note that there probably are other unknown effects in place to explain such negativity in this case. See figure below for *Digital Software*:

<p float="left">
	<img src="/img/products/helpful_vs_rating_Digital_Software.png" width="49.5%" />
</p>

**Analysis of the `review_date` feature**

For this, we decided to split the data according to four different time metrics. By Year, by Month, by day of month and by day of week. 

<p float="left">
  <img src="/img/products/rating_by_month_evolution_Books.png" width="49.5%" />
  <img src="/img/products/number_by_month_evolution_Books.png" width="49.5%" /> 
</p>

We can observe that the confidence bands for the average rating by month are quite tight. This allows us to draw conclusions with confidence. For the total number of reviews, however, we can not draw confidence bands. The number of reviews seems to have a general correlation with holidays. There is roughly a peak in summer and one in winter. We attribute this to the fact that people have more time then, so they read more. Note however that the reviews we have range from may 1996 to july 2014. The lower values in the fall might also be partialy related to the fact the we have the data for fall 2014, but this alone shouldn't have that much influence over al the years.

We also see that the ratings are higher in hollyday season. This we attribute to the people being happier then. However, it has to be noted that the effect is very small (about 0.03), but statistically significant.

<p float="left">
  <img src="/img/products/rating_by_year_evolution_Books.png" width="49.5%" />
  <img src="/img/products/number_by_year_evolution_Books.png" width="49.5%" /> 
</p>

Again, the confidence bands are very tight for the rating, which is nice for drawing conclusions. We can see that the confidance band widens for early years, here there are less reviews. We clearly see a decrease in the years up untill ~2003 in the ratings. Afterwards the ratings increase. We will check if this effect is present in datasets across countries and product types in the next milestone. We will have to do research in order to find out wether amazon changed their rating system somehow or why this effect may occur. The number of reviews per year is clearly increasing, which is not surprising since amazon has been growing steadily. We observe a slight decrease after the year 2000, this may be attributed to a decrease in interest in internet companies after the dotcom bubble. The last drop around 2014-2015 is simply due to the missing data after july 2014.

<p float="left">
  <img src="/img/products/rating_by_dayofweek_evolution_Books.png" width="49.5%" />
  <img src="/img/products/number_by_dayofweek_evolution_Books.png" width="49.5%" /> 
</p>


We can see that here, the confidence bands are wider. Notably, we have a spike in ratings on monday, which is surprising as we would expect people to not be the happiest on monday. The number of ratings starts high at the beginning of the week, and then steadily decreases until the weekend where it is at its lowest point. We may assume that people use / test their new products during the weekend, and give it a rating the following week after, mainly on monday because they don't have the motivation to work. Here again, the effect on the average rating is significant, but very small (in the order of 0.01).

<p float="left">
  <img src="/img/products/rating_by_dayofmonth_evolution_Books.png" width="49.5%" />
  <img src="/img/products/number_by_dayofmonth_evolution_Books.png" width="49.5%" /> 
</p>

For the day of the month, the confidence bands are almost too big to draw significant conclusions. We could however argue that people usually receive their salary at the end of the month, therfore are more happy and give better ratings in the following days, with their happiness decreasing until the next payday. The Number of reviews is very low on the 31. This is not something worth interpreting, as almost all months have at least 30 days, but only a few have 31. The slight decrease in the number of reviews over the month might be connected to the previous argument of payday happiness, where people might spend more money to buy products on amazon at the beggining of the month, and hence give more reviews. The effect on the average rating we observe is about 0.02.

**Analysis of the number of reviews per product**

We want here to see if there is any trend going on between the total number of reviews on a product and the rating of that product. e decided to only keep products with more than 2 reviews, since it is reasonable to assume that the average rating of products with a small amount of reviews might not be that reliable as an indication of the true quality of the product.


<p float="left">
  <img src="/img/products/rating_vs_reviews_Books.png" width="49.5%" />
  <img src="/img/products/number_vs_reviews_Books.png" width="49.5%" /> 
</p>

There seems to be a trend between the two, but as the number of reviews per product increases, we have less and less data and the confidence interval becomes larger. This makes sense, as we can expect the number of reviews to be highly positively correlated with the number of sales. It is quite intuitive that only few products are very popular and many products are not so successful. (The number of reviews is intuitively highly correlated with the numbe of sales.)

So we can observe for sure a descending trend in the average rating between 3 and ~100 number of reviews per product (of about 0.05 stars, which is not negligable with so much data) but any value after that becomes too uncertain to conclude anything.


**Analysis of the `review_headline` and `review_body` features**


Another feature that may give us a hint on the rating is the review's text. A method that comes to mind would be to analyse the text with natural language processing models, especially sentiment analysis. But this analysis would not bring new insights, if we were to find a positive correlation. It would seem fairly intuitive that a more positive review will come with a higher rating. Additionnaly, these methods would be cumbersome to implement with spark and use too much processing power.

We decided to go with features that are easier to extract. We chose to use text length. The reasoning would be that there is not much to say if the product is perfect, but one can explain at length why the product is not good.

<p float="left">
  <img src="/img/products/rating_vs_title_length_Books.png" width="49.5%" />
  <img src="/img/products/number_vs_title_length_Books.png" width="49.5%" /> 
</p>

We have a huge peak for reviews with a title length of aproximatly 10 characters. This might be because a lot of people that liked their products put simple titles such as "very good", "I recommand", "best product", and so on. Reviews with very small titles have lower grades, maybe people that don't like product don't care about the title and just put the negative comment, but this there is a very low number of those reviews it is harder to tell with good confidence.

Overall however, we see that the average rating increases quite a lot for longer titles (more than 0.1 stars), probably because people that liked the product take some time to make good titles that might catch the attention of other people.

For the number of reviews, this result is expected. Most of the people make short title, whith very few making extremly short titles (hard to do a title in 3 letters), and few make very long title (Most people will write their opinion in the review body instead).

We will look deeper at reviews with a title of length 10, in particular the 6 most common titles.

| review_headline	| count   |
|-----------------|---------|
| Five Stars      |	1380977 |
|	Four Stars	    | 226379  |
|	Great book	    | 62192   |
|	Great Book	    | 53497   |
|	great book	    | 25645   |
|	Excellent!	    | 16354   |

This confirms our intuition. Most of the people that liked the product can put a simple adjective / the number of stars, to quickly say what they think. For negative reviews, this would be harder, at least if you want to give a constructive review.

Now, for the body length

<p float="left">
  <img src="/img/products/rating_vs_review_length_Books.png" width="49.5%" />
  <img src="/img/products/number_vs_review_length_Books.png" width="49.5%" /> 
</p>

For the body lenght however we see that the result is completly different. A short review body generaly corresponds to a higher rating, whereas a long review coresponds to a lower rating. It probably is because there is not always much to say for excellent products, whereas you can describe a lot of negative points for the less good products. That trend is very clear between 0 and 2000 characters, with a huge drop of more than 0.5 stars, but for longer reviews, it is harder to conclude anything since the confidence interval is much larger, and we can see a lot of noise.

#### Importance of the features for the bias correction

Lorem ipsum

#### Comparaison with other categories

Lorem ipsum

Lorem ipsum

### By Country

- Difference between countries
	- Herding

### By User

- by category 

# Bias Correction

### Decisions

### Correction by Date

and other factors seen in previous observations

### Correction by User History

(using the formula)

# Conclusion

### Summary

Wonderful summary

### Further Possible Work

### Usefulness

(ethics, applicable, for whom, ...)

### Comparaison with Other Studies
