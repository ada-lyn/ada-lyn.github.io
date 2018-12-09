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

First, we will analyse some of the features (`verified_purchase`, `helpful_votes`, `total_votes`, `review_date`, numbe of reviews per product, `review_headline` and `review_body`), then we are going to discuss their potential influence in the bias, and if they shopuld be considered, and finally we will compare the results with the other categories.

####  Analysis of some features

**Analysis of the `verified_purchase` feature**

This might have an influence, since non-verified purchases won't be as reliable since poeple might not have actually bought the product. We have splited the dataset into verified and non verified purchases, and for each category we plot the average rating per year, and the number of reviews per year.

![Number of verified reviews for each year](/img/other/verified_by_year.png)

We can clearly see, with a 95% confidence interval, that the average rating is higher for verified reviews in recent years. This is difficult to guess what could have caused it to change along the years, possibly one or more modifications in the condition to be a verified purchase. It would be good to ignore all unverified reviews, to have more reliable ratings.

There is however a problem with that. By looking at the evolution of the number of reviews over the years (graph on the right just above), we realized that in the earliest years there are almost no verified reviews. This is probably due to the fact that this functionality was added quite late, or that maybe it used to be harder to be a verified review. Hence, we decided, despite the difference of rating, to keep all reviews, in order to avoid losing so much data for the earliest years.
It is clear that a lot of data is missing in the 2000's if we limit ourselves to the verified reviews. From now on, we will use all the data.

**Analysis of the `helpful_votes` and `total_votes` features**

Lorem ipsum

**Analysis of the `review_date` feature**

Lorem ipsum

**Analysis of the number of reviews per product**

Lorem ipsum

**Analysis of the `review_headline` and `review_body` features**

Lorem ipsum

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
