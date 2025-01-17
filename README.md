# Crypto Forecast with Twitter Data

## Project Goal and Problem Statement
With the recent collapse of the FTX crypto scam, I wonder if data from Twitter related to the top cryptocurrencies can be collected and used to correlate and forecast volatility.
This project aims to demonstrate how to build a system that collects real-time tweets of the existing cryptocurrencies -using keywords. This data can be indexed by its exchange symbols and fed into Elastic Cloud for graphical presentation and analysis.

YouTube Video URL
https://youtu.be/uV1kpTkHq7U

## Big Data Source
The data source is Twitter streaming data, filtered to receive tweets related to the top 20 cryptocurrencies. (https://coinmarketcap.com/coins/)

To avoid the incompatibility of other data-collecting tools due to the recent update on the Twitter API and to achieve a high streaming speed, I developed my collector in Python using API calls based on the Twitter API V2 stream specification.

## Data Format
The Twitter data is streamed as events per tweet. The volume depends on the current number of active users, and the speed of the data can be fast, and volume can grow very fast. During my testing, for the filtered 20 cryptocurrencies, the data can grow from a few hundred to the order of 8k-10k/minute.

## Events Enriched with Sentiment Analysis
RoBERTa is an NLP model, from Facebook, based on the BERT language.
The University of Cardiff RoBERTa model was pretrained in sentiment analysis of Tweet texts and was added to the python code of the collector to enrich the crypto tweet events by adding sentiment analysts. The model result was qualified as POSITIVE, NEGATIVE, or NEUTRAL applying a softmax function, also a corresponding direction of 1, 0, or -1 was added to facilitate calculations on the event counts.
(https://huggingface.co/cardiffnlp/twitter-roberta-base-sentiment)

## Expected results
Collecting the real-time Twitter data using this processing pipeline allows us to identify which cryptocurrencies become more volatile -based on the trending number of tweets.
Sentiment analysis of the tweet’s text was added to the pipeline to enrich the data allowing us to distinguish the direction of the trend besides the sheer volume of tweets.

## Processing Pipeline Diagram
<img width="468" alt="image" src="https://user-images.githubusercontent.com/35944732/206832853-2cc2738d-97fe-41e4-8bfb-f9cc2714af02.png">


## Processing Pipeline
Collection tier: Ad-Hoc collector made in python

- The collector ingests the data from Twitter -filtered by the cryptocurrencies’ symbols, tagged with keywords related to the matches found within the text
- At the time of collection, the data gets enriched with sentiment analysis of the text

### Messaging tier: Kafka

- The collector acts as a Kafka producer pushing events into the defined crypto Kafka topic for further processing 
- Kafka produces the topic to send the events to the Kafka listeners

### Stream Processing Tier: Logstash 

- Logstash listens to the Kafka topic crypto
- Adds a timestamp, converts the id to a string, and delivers de data as the index crypto for Elasticsearch and Kibana

### Visualization Tier: Kibana with Elasticsearch

- Kibana with Elasticsearch is used to visualize the received data and graphically analyze the cryptocurrencies tweet trends
