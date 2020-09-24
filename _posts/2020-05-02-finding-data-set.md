---
layout: post
title: Finding a Data Set
tags: data-science career capstone-project data-set machine-learning
---

Once you have picked a couple of topics based on the guidelines provided in my [previous post]({{site.baseurl}}{% link _posts/2020-04-19-capstone-importance.md %}), you need to find data sets to go with these topics. In this post, we will discuss what makes a data set good for data science projects. Then, we will list a couple of ways  to find them.

## Picking the Data Set

1. Narrow, a.k.a. Tall and Skinny, Data

    Of the many components that make up data science, employers tend to focus on machine learning more than other disciplines. Aside from being relevant to the topic of choice, the data set should showcase your machine learning skills. To facilitate machine learning, a data set with many records and relatively few attributes, i.e., <a href="https://en.wikipedia.org/wiki/Wide_and_narrow_data#Narrow">a narrow data set</a> is preferred. If you need some examples on what a narrow data set should be like, I highly recommend downloading some of the data sets in [Kaggle]({http://www.kaggle.com}). The main data set in each competition is usally in the right form. 

2. Right Size
 
    While a larger data set is preferred in machine learning in general, data set can be too large and impede progress. Available computational power wwould become an issue and it would be difficult to iterate in the project. Until you are comfortable using some advanced tools, such as <a href="https://spark.apache.org/">Apache Spark</a>, you should avoid data set that is too large to fit into memory. Obvioulsy, if you find a great data set, but it is too large, you can always use only a fraction of the data. When down sampling the data, you should be mindful to not alter the distribution in the original data set.

## Finding Data Set on the Internet

There are so much data available on the web. I am going to list some standard ways for data scientists to find, scrape, or curate the right data set for their projects. 

1. Search Engines

    i. Try Google and Google Data Search

      [Google](http://www.google.com) should be the obvious first tool for finding a data set. It is the most popular search engine in the world because it works well. In fact, Google has a decidated service called [Dataset Search](https://datasetsearch.research.google.com/) for data sets. 

    ii. Go beyond the first couple of pages of search results
       
      Google and Google Dataset Search should be suffice for many data sets, but what if you did not find the data you are looking for? We know search engines are very good for common queries as the top results tend to satisfy.  However, a data set search is not a typical query so the result may not appear readily. In the past, I have found the results that are relevant to me ten, maybe twenty, pages down. 
        
    iii. Try other search engines
   
      While Google is the dominant search engine in the world, there are [other](http://www.bing.com) [search](http://www.duckduckgo.com) [engines](http://www.yandex.com) out in the wild. I often find useful information from the alternatives. 
        
2. Scraping

    Sometimes, data might be formated for viewing on the web, but not necessarily readily available for download. While cutting and pasting is a solution, it would likely be too laborious for any data set suitable for machine learning. In some cases, you might be able to scrape it off the pages directly. There are many great tutorials on webscrapping so I am not going to repeat them here. 

3. Finding and Calling Public APIs

    Aside from downloading data sets whole or scraping them from website, you can also find public APIs that would allow you to find the right data. Calling APIs directly is a great way to practice your programming skills as well. You can find [many public APIs avaiable using a simple search](https://www.google.com/search?q=public+apis&oq=public+apis).

## Final Thoughts

I have listed here a couple of ways to find a data set for data science projects. There are many great resources on finding data sets. My hope is that this post will provide you with some ideas to get started. 
