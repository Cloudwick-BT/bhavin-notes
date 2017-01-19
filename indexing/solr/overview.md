## Introduction

It is open source search platform based on Apache Lucene project. ts major features include powerful full-text search, hit highlighting, faceted search, dynamic clustering, database integration, rich document (e.g., Word, PDF) handling, and geospatial search. It is written in java and provides RESTful service for powerful integration.

## Indexing

  * It does **inverted index**, which is keyword-centric data structure. It is similar to keyword index present at the end of the book.
  * Document is the unit of search.
  * Index consists of one or more Documents and a document consists of one or more Fields. _In database terminology, a Document corresponds to a table row, and a Field corresponds to a table column._
  * One need to define schema before adding documents to Solr.
  * When data is added, it goes through lot of transformations called as **Analysis**. The end result are series **Tokens** which are then added to index.


## Solr sharding concepts
 
  * **Shard group** is the collection of documents, composed of one or more shards.
  * Index is split into chucks called **Shard**.
  * When you create an index, you define the number of shards that you want. Each shard is in itself a fully-functional and independent Solr index that can be hosted on any index server.
  * A Shard is a logical partition of the collection, containing a subset of documents from the collection.


## Solr distributed approach issues 

There were, however, several problems with the distributed approach that necessitated improvement with SolrCloud:
  * Splitting an index into shards was somewhat manual.
  * There was no support for distributed indexing, which meant that you needed to explicitly send documents to a specific shard; Solr couldn't figure out on its own what shards to send documents to.
  * There was no load balancing or failover, so if you got a high number of queries, you needed to figure out where to send them and if one shard died it was just gone.

** SolrCloud fix these issues **


## SolrCloud

  * It fixes all the above issues, No masters or slaves. 
  * Every shard consist of exactly one physical replica, exactly one of which is **leader**.
  * Document send to solr for indexing -> system determine which shard it belongs to -> which node host leader of that shard -> document forwarded to leader and then updates are sent to replicas.
