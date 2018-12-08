---
layout: page
title: Detecting Bias in Amazon reviews
subtitle: By ADA-LYN
published: true
---

# Introduction

### Abstract

In the past, when buying an item, one had to trust reviews in newspapers or from friends. In todays age, with online shopping, we have to tap into the minds of thousands of people who have purchased the product we are thinking about. With the help of Amazon reviews and their star-system, we can easily analyse how well to product likely is. But while a newspaper or professional reviewer is generally working hard for consistency and unbiasedness, these facts are not given for a general public reviewer writing a comment. With help of the Amazon dataset, we will try to find bias in the reviews, in order to possibly give an idea on whether or not, and if so how, to correct a bias. We will be especially interested in the influence of the following factors on the number of stars given:

- The date the review was written on ?
- The category of the product the review was written for ?
- The country of residence of the buyer (What the product bough on Amazon US or Amazon UK ?)
- Past ratings from the reviewer

### Dataset

We will use an [Amazon Dataset](http://jmcauley.ucsd.edu/data/amazon/) that consist multiple millions of Amazon reviews. More precisely, it contains (amongst other informations that will not be used) the following informations for each review : 

- `marketplace` : The "country of amazon". Data is available for the United Stated, United Kingdom, France, Germany and Japan.
- `customer_id` : A unique id representing the user that wrote the review
- `product_id` : A unique id representing the product reviews
- `product_title` : The name of the product
- `product_category` : The category of the product. The following categories are available : *Wireless, Watches, Video Games, Video DVD, Video, Toys, Tools, Sports, Software, Shoes, Pet Products, Personal Care Appliances, PC, Outdoors, Office Products, Musical Instruments, Music, Mobile_Electronics, Mobile Apps, Major Appliances, Luggage, Lawn_and_Garden, Kitchen, Jewelry, Home Improvement, Home Entertainment, Home, Health Personal Care, Grocery, Gift Card, Furniture, Electronics, Digital Video_Games, Digital Video Download, Digital Software, Digital_Music_Purchase, Digital Ebook Purchase, Camera, Books, Beauty, Baby, Automotive and Apparel*
- `star_rating` : The number of stars given by the reviewer, between 1 and 5
- `helpful_votes` : Other users can vote on the usefulness of a review. This represent the total number of positive votes
- `total_votes` : This is the number of positive and negative votes combined
- `verified_purchase` : If Amazon is able to check that you indeed bought this exact product, with the same price, your review will be markerd as "verified"
- `review_headline` : Short summary of the review
- `review_body` : Full content of the review
- `review_date` : Date the review was posted on

Since we have a lot of data (Amounting to ~20GB), our main focus will be on the reviews related to Books. We will do an in-depth analysis of these reviews, and then rapidly compare it to the other categories.

### Research Questions

We have two main questions we will try to answer during this project 

- What factors influence the amount of stars given in an amazon review (other than product quality) ? 
- Is there a way to correct this bias, should we correct this bias ?

### Motivation

**TODO** Why we want to do this

### General Description of Data

**TODO** count / mean / median / std

# Effects on Rating

Lorem ipsum

### By Product Category

Lorem ipsum

####  Analysis of some features

Lorem ipsum

**Analysis of the `verified_purchase` feature**

This might have an influence, since non-verified purchases won't be as reliable since poeple might not have actually bought the product. ... **explain by date**

![alt text](/img/other/verified_by_year.png)

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

### Further Possible Work

### Usefulness

(ethics, applicable, for whom, ...)

### Comparaison with Other Studies
