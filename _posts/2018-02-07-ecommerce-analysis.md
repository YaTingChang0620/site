---
layout: post
title:  "Customer Segmentation and Repurchase Prediction"
date:   2018-02-07
excerpt: "A case study of online retailer in UK"
image: "/images/ecom/cover.png"
---

This dataset contains actual transactions from 2010 and 2011 for a UK-based online retailer. There are a few challenges in this dataset:
<ol>
<li>Too few features, eight features only:<br>
&emsp;&emsp;InvoiceNo, Stockcode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country</li>
<li>There is no product category attribute: <br>
&emsp;&emsp;all product description is unstructured data, nearly 4000 unique product descriptions</li>
</ol>

<h2>Objectives</h2>
<div class="box">
<h3> 1. Customer Segmentation </h3>
<p>Cluster more than 4000 customers into subgroups and analyze purchase behavior for each subgroup</p>
<h3>2. Repurchase Prediction</h3>
<p>Use binary classification to predict whether or not a customer will purchase product in the next month</p>
</div>

<h2>I. Exploratory Data Analysis</h2>

<p>The number of features in our raw data is not enough for in-depth analysis. For example, we want to know how many items customers tend to buy in each transaction, or the interval between visits.<br>
These features are all lack in our raw data. Therefore, we generated additional 17 features into 25 features in total such as (per customer):
<ul>
<li>total spending</li>
<li>unit price per transaction</li>
<li>item per transaction</li>
<li>time interval between visits</li>
</ul>
</p>

<h3>Some descriptive summaries:</h3>
<ol>
<li value="1">Number of transactions significantly increased in November due to Thanksgiving. In order to analyze purchase behavior by season, we add four indicator variables into our dataset.</li>
<li value="2">Most customers are one-time buyer. Few customers tend to purchase more than 4 times.</li>
<div style="display: inline-block; width:100%;">
    <div class="" style="max-width: 32%;max-height: 32%;display: inline-block;">
        <img src="{{"/images/ecom/InvoiceMon.png"| absolute_url }}"  alt="" style="width:100%"/>
        <center><sub><figcaption>(1) high peak during October to December.</figcaption></sub></center>
    </div>
    <div class="" style="max-width: 64%;max-height: 64%;display: inline-block;">
        <img src="{{"/images/ecom/hist_transaction.png"| absolute_url }}"  alt="" style="width:100%"/>
        <center><sub><figcaption>(2) mostly, one-time buyer</figcaption></sub></center>
    </div>
</div>
</ol>

<ol>
<li value="3">Most transactions occur in the noon.</li>
<li value="4">The unit item per transaction falls between 2 to 3.</li>


<div style="display: inline-block; width:100%;">
	<div class="" style="max-width: 64%;max-height: 64%;display: inline-block;">
        <img src="{{"/images/ecom/InvoicePeriod.png"| absolute_url }}"  alt="" style="width:49%" />
        <img src="{{"/images/ecom/InvoiceHour.png"| absolute_url }}" alt="" style="width:49%" />
        <center><sub><figcaption>(3) The distribution of transaction over various time period in a day</figcaption></sub></center>
    </div>
    <div class="" style="max-width: 32%;max-height: 32%;display: inline-block;">
        <img src="{{"/images/ecom/hist_quantity_level.png"| absolute_url }}"  alt="" style="width:100%"/>
        <center><sub><figcaption>(4) The distribution of quantity level</figcaption></sub></center>
    </div>
</div>
</ol>
<br>

<h2>II. Product Categorization</h2>
<p> As mentioned above, it's difficult to do product-related analysis without knowing the category 
each product belongs to. To cluster the products, we first discretize products into different levles of
price and quantity and use one-hot encoding to represent these features:</p>
<div class="table-wrapper">
<table>
<tbody>
<tr>
<th>Product</th>
<th>PL1</th>
<th>PL2</th>
<th>...</th>
<th>PL7</th>
<th>QL1</th>
<th>QL2</th>
<th>...</th>
<th>QL7</th>
</tr>
<tr>
<td>CHRISTMAS TREE HEART DECORATION</td>
<td>1</td>
<td>0</td>
<td>...</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>...</td>
<td>1</td>
</tr>
<tr>
<td>MINI PAINT SET VINTAGE</td>
<td>0</td>
<td>1</td>
<td>...</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>...</td>
<td>0</td>
</tr>
</tbody>
</table> 
</div>

<p>To deal with the complicated product descriptions, we tokenized descriptions and kept all the nouns as keywork by Python nltk package. We narrowed over 4000 unique product description down to the 193 keywords.<br>
And again add these keywords into features through ont-hot encoding.</p>
<div class="table-wrapper">
<table>
<tbody>
<tr>
<th>Product</th>
<th>heart</th>
<th>vintage</th>
<th>set</th>
<th>...</th>
<th>christmas</th>
<th>...</th>
<th>birthday</th>
<th>tree</th>
</tr>
<tr>
<td>CHRISTMAS TREE HEART DECORATION</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>...</td>
<td>1</td>
<td>...</td>
<td>0</td>
<td>1</td>
</tr>
<tr>
<td>MINI PAINT SET VINTAGE</td>
<td>0</td>
<td>1</td>
<td>1</td>
<td>...</td>
<td>0</td>
<td>...</td>
<td>0</td>
<td>0</td>
</tr>
</tbody>
</table> 
</div>

<p>We then applied k-means clustering with silloutte methods to categorize the products.</p>

<div style="display: inline-block; width:100%;">
<div class="" style="max-width: 95%;max-height: 95%;display: inline-block;">
<img src="{{"/images/ecom/stat_cluster1.png"| absolute_url }}"  alt="" style="width:100%"/>
<center><figcaption>Cluster 1: lower price and higher demanded products(e.g. bag, card, Christmas, Vintage)</figcaption></center>
</div>
</div>
<br>
<div style="display: inline-block; width:100%;">
<div class="" style="max-width: 95%;max-height: 95%;display: inline-block;">
<img src="{{"/images/ecom/stat_cluster3.png"| absolute_url }}"  alt="" style="width:100%"/>
<center><figcaption>Cluster 3: high price level and less demanded products(e.g. necklace,lace)</figcaption></center>
</div>
</div>
<br>

<h2>III. Customer Segmentation</h2>
<p>To reduce our model's complexity and tackle 'curse of dimensionality', we used tSNE instead of PCA to perform the dimension reduction. With tSNE, we transform our features into 2 dimensional representation. <br>
Then, applied k-means clustering to segment cutomers. </p>

<p><center><div style="display: inline-block; width:100%;">
	<div class="" style="max-width: 45%;max-height: 45%;display: inline-block;">
        <img src="{{"/images/ecom/customer_silhouette_kmeans.png"| absolute_url }}"  style="width:100%"/>
     	<center><sub><figcaption>Optimal number of customer clusters by sillouette analysis</figcaption></sub></center>
    </div>
    <div class="" style="max-width: 45%;max-height: 45%;display: inline-block;">
        <img src="{{"images/ecom/customer_tsne_kmeans.png"| absolute_url }}"  style="width:100%"/>
        <center><sub><figcaption>Visualization of customer clusters based on t-SNE features</figcaption></sub></center>
    </div>
</div></center></p>
<br>

<h3>Customer analysis by cluster:</h3>

<ol>
<li>Customers in cluster 1 have the largest amount of transactions, and also contributed the most revenue for the company.</li>
<li>Customers in cluster 3 have nearly the same average spending as the customers in cluster 1, indicatig that they tend to purchase high-price product in each transaction.</li>
</ol>

<div style="display: inline-block; width:100%;">
	<div class="" style="max-width: 32%;max-height: 32%;display: inline-block;">
        <img src="{{"images/ecom/byCluster_transaction.png"| absolute_url }}"  style="width:100%">
     	<center><sub><figcaption>Average number of transaction</figcaption></sub></center>
    </div>
    <div class="" style="max-width: 32%;max-height: 32%;display: inline-block;">
        <img src="{{"images/ecom/byCluster_aveSpend.png"| absolute_url }}" style="width:100%">
        <center><sub><figcaption>Average expense</figcaption></sub></center>
    </div>
    <div class="" style="max-width: 32%;max-height: 32%;display: inline-block;">
        <img src="{{"images/ecom/byCluster_aveUnitPrice.png"| absolute_url }}"  style="width:100%">
        <center><sub><figcaption>Average product unit price</figcaption></sub></center>
    </div>
</div>

<br>

<h2>IV. Customer Repurchase Prediction</h2>
<p>Since the transaction is from year 2010 to year 2011, we treat the transactions from 2010/12 to 2011/11 as our training data and 2011/12 as our test data. In our training data, we split 20% of data into validation set and the remaining into training set. <br>
Our model achieved 78% accuracy rate and the performance below is conducted via 5-fold cross validation:</p>

<div class="table-wrapper">
<table>
<tbody>
<tr>
<th>Fold</th>
<th>1</th>
<th>2</th>
<th>3</th>
<th>4</th>
<th>5</th>
<th>Average</th>
<th>SD</th>
</tr>
<tr>
<td>Accuracy</td>
<td>0.790</td>
<td>0.771</td>
<td>0.783</td>
<td>0.753</td>
<td>0.804</td>
<td>0.780</td>
<td>0.019</td>
</tr>
<tr>
<td>AUC</td>
<td>0.774</td>
<td>0.752</td>
<td>0.739</td>
<td>0.748</td>
<td>0.777</td>
<td>0.758</td>
<td>0.017</td>
</tr>
</tbody>
</table>
</div>

<br>
<h2>V. Summary</h2>
<h3>What we've done:</h3>
<ol>
<li>Characterized products using text analysis and provided insight on product categories by K-means clustering </li>
<li>Analyzed customer behaviors and conducted customer segmentations for personalized recommendations and data-driven marketing </li>
<li> Built a logistic regression model to predict whether customers return in the next month, achieving 78% accuracy and 75% AUC in 5-fold cross-validation </li>
</ol>

<h3>Next steps:</h3>
<ol>
<li>Build a specific LR models for each cluster. Every time when a customer purchased the products,
we could first determine which cluster he/she belongs and then use that cluster's model to 
predict his/her repurchase rate. Based on the prediction outcome, we could better optimize our marketing 
budget allocation. </li>
<li>Further analyze the most popular product in each customer cluster and provide personalized recommendation for each cluster!</li>
</ol>