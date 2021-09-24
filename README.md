# Module 16 Challenge - Amazon Vine Analysis - AWS and PySpark

## Overview

Project Origination Date: 2021-09-21

### Purpose

The purpose of this analysis is to analyze Amazon reviews written by members
of the paid Amazon Vine Program.

For this analysis, the `us_Tools_v1_00` Dataset will be used.

### Tasks

1. Choose a dataset from the collection of Amazon Example Product Reviews
	- For purposes of this analysis, the `us_Tools_v1_00` Dataset will be used.
2. Use PySpark to perform the ETL process to extract the dataset, transform the data,
connect to an AWS RDS instance, and load the transformed data into a PostgreSQL Database
3. Use PySpark, Pandas, or SQL to determine if there is any bias toward favorable reviews
from Vine members in the selected dataset.
4. Write a summary of the analysis (this README.md)

### Approach

1. Create a PostgreSQL Database Hosted on Amazon AWS.
2. Connect to this database via pgAdmin.
3. Within pgAdmin, create tables using client-provided pre-defined schema.
4. Import provided Starter Code to Google Colab Notebook.
5. Extract one of the review datasets, and create a new DataFrame containing the data.
6. Transform the dataset into four DataFrames that will match the schema in the pgAdmin tables.
7. Determine bias of Vine Reviews

### Deliverables

1. Perform ETL on Amazon Product Reviews
2. Determine Bias of Vine Reviews
3. Provide a Written Report on the Analysis (this README.md)

Jump to [Deliverables](#deliverables-1)

### Resources

- Software:
	- PostgreSQL Database, hosted on Amazon Web Services
	- pgAdmin 4, Version 5.2
	- Google Colab Notebooks (https://colab.research.google.com)
- Data:
	- `https://s3.amazonaws.com/amazon-reviews-pds/tsv/amazon_reviews_us_Tools_v1_00.tsv.gz`
		- Sample dataset of Amazon Product reviews, obtained from Amazon Web Services
		- Downloaded/Accessed: 2021-09-21
	- `Amazon_Reviews_ETL_starter_code.ipynb`
		- Client-provided Starter Code
		- Imported to Google Colab Notebook and renamed `Amazon_Reviews_ETL.ipynb`

Additional information about these resources is outlined below in Tables 1 & 2.

**Table 1: Source Data Description**
| File Name                               | Brief Description of Contents
|-----------------------------------------|------------------------------
| `amazon_reviews_us_Tools_v1_00.tsv` | Tab-Delimited Plaintext File without quote or escape characters, containing Amazon Product Reviews from the Tools Category. First line is Header, remainder of file 1 line corresponds to 1 record. 1,741,101 Lines Containing 1 Header Row and 1,741,100 Reviews with Metadata. Data is taken from United States Marketplace. All Metadata and most reviews are in English. Data is primarily represented using the Basic Latin ASCII Character Set, but there are some Latin-1 Supplement and Latin Extended ASCII and Non-ASCII Unicode Characters present, most frequently punctuation and symbols such as Registered Trademark, smart quotes, and en dashes. Appearing less frequently, but also present are non-Latin language characters from various world alphabets such as Cyrillic, Greek, Hebrew, Arabic, Korean, Chinese, Japanese, Emoji, Mathematical Symbols, and Diacritics. 15 Fields, described in greater detail below in Table 2.

**Table 2: `amazon_reviews_us_Tools_v1_00.tsv` Fields**
| Field Name                              | Amazon Original Description of Contents                                                                                                                                    | Additional Explanation of Contents
|-----------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------
| `marketplace`                           | 2 letter country code of the marketplace where the review was written.                                                                                                     | All entries are in `US` for 'United States'.
| `customer_id`                           | Random identifier that can be used to aggregate reviews written by a single author.                                                                                        | 1,053,515 Unique Customer ID Numbers. ID Numbers are purely numeric with 5-8 digits. Min: 10027, Max: 53096511.
| `review_id`                             | The unique ID of the review.                                                                                                                                               | Unique Alphanumeric Review ID. Ranging in length from 10-14 characters. All Review ID's Begin with 'R'.
| `product_id`                            | The unique Product ID the review pertains to. In the multilingual dataset the reviews for the same product in different countries can be grouped by the same `product_id`. | 183,810 Unique Alphanumeric Product ID's. All ID's have a length of 10 characters.
| `product_parent`                        | Random identifier that can be used to aggregate reviews for the same product.                                                                                              | 166,372 Unique Product Parent ID Numbers. ID Numbers are purely numeric with 3-9 digits. Min: 538, Max: 999989950.
| `product_title`                         | Title of the product.                                                                                                                                                      | 167,530 Free Text Product Titles. Ranging in length from 1-400 characters.
| `product_category`                      | Broad product category that can be used to group reviews (also used to group the dataset into coherent parts).                                                             | Free Text Product Title. All entries are in `Tools`.
| `star_rating`                           | The 1-5 star rating of the review.                                                                                                                                         | Numeric star rating of the review. Length of 1 digit. Min: 1, Max: 5.
| `helpful_votes`                         | Number of helpful votes.                                                                                                                                                   | Numeric count of helpful votes of the review. Ranging in length from 1-4 digits. Min: 0, Max: 4760.
| `total_votes`                           | Number of total votes the review received.                                                                                                                                 | Numeric count of total number of votes of the review. Ranging in length from 1-4 digits. Min: 0, Max: 4858.
| `vine`                                  | Review was written as part of the Vine program.                                                                                                                            | Alphabetic Pseudo-Boolean value. Length of 1 character. Possible values: `N` for 'No', review was not written as part of the Vine Program, `Y` for 'Yes', review was written as part of the Vine Program.
| `verified_purchase`                     | The review is on a verified purchase.                                                                                                                                      | Alphabetic Pseudo-Boolean value. Length of 1 character. Possible values: `N` for 'No', review was not written for a Verified Purchase, `Y` for 'Yes', review was written for a Verified Purchase.
| `review_headline`                       | The title of the review.                                                                                                                                                   | 932,228 Unique Free Text Review Headlines. Ranging in length from 1-128 characters.
| `review_body`                           | The review text.                                                                                                                                                           | 1,591,365 Unique Free Text Reviews. Ranging in length from 0-43,791 characters. All reviews with 0 characters in `review_body` do have non-NULL `review_headline`.
| `review_date`                           | The date the review was written.                                                                                                                                           | 5,770 Unique Gregorian Calendar Dates in the format of '%Y-%m-%d'. Least-Recent: 1999-11-09, Most-Recent: 2015-08-31.
                                            
#### Data Quality                           

With the exception of the presence of non-Latin characters and symbols in the Free Text fields (16,961 lines contain at least 1 non-ASCII Character), the quality of this dataset is overall very good and reliable.
[At least from the standpoint of Metadata, without discussing the veracity of the contents themselves.]

All fields contain consistent and expected values, few if any NULL values, and proper rules are being applied and adhered to for the allowed values within each given field.

A cursory review has revealed some reviews written in French.

#### Profanity Check

Out of curiosity, and as a way to test the hypothesis that strongly-worded reviews can be informative, the Review Headline and Review Body text were compared against George Carlin's Seven Words You Can't Say on TV.
As a first pass, the seven words were included with simple lowercase spellings, with one added variant known already to be present within the text ($#!t). Word boundaries were not enforced, because some reviewers
have some creative coinages and we did not want to ignore these creations (such as 'EHOLYSHITBALZE!!').

There are some false positives such as 'mishits', 'pushit', 'butits', French language reviews using the word 'petits', and Brand Names (either properly or improperly spelled) like 'Matsushita', or 'Bostitsch' (sic).
More work could be done to derive more variants of profane words made up of known common spelling variations and punctuation character substitution to find more profane reviews,
and false positives could be reduced by compiling a whitelist of brand names and benign words which themselves innocently contain spellings of profane words (e.g. ).

'Piss' is the most-frequently appearing profane word among the seven searched for, and most-often appears with its intended
pejorative sense and not as part of a false-positive making up some other word. In the absence of other more comprehensive means of analysis, a 'piss' check could be a way to quickly find
your most-dissatisfied customers!

A cursory review reveals that those reviewers who took the time to mention Jeff Bezos by name do so more often to criticize than to praise.

One final caution is to remember the flexibility and variability of profanity. 'Shit' is commonly mentioned in negative product reviews, but 'the shit' is high praise. Similarly, 'the tits' signifies a highly positive review,
while the mention of 'tits up' would likely be something to avoid.

## Deliverables

### Deliverable 1

See `Amazon_Reviews_ETL.ipynb`

### Deliverable 2



## Results

- Q: How many Vine reviews and non-Vine reviews were there?
    - A: Lorem ipsum.

- Q: How many Vine reviews were 5 stars? How many non-Vine reviews were 5 stars?
    - A: Lorem ipsum.

- Q: What percentage of Vine reviews were 5 stars? What percentage of non-Vine reviews were 5 stars?
    - A: Lorem ipsum.

## Summary

Is there any positivity bias for reviews in the Vine Program?

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Proin aliquet iaculis lorem non sollicitudin. Fusce elementum ac elit finibus auctor. Curabitur orci sem, accumsan a diam sit amet, efficitur tristique velit.

What additional analysis could be performed to support these conclusions?

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Proin aliquet iaculis lorem non sollicitudin. Fusce elementum ac elit finibus auctor. Curabitur orci sem, accumsan a diam sit amet, efficitur tristique velit.

-- END --
