# **Introduction to ElasticSearch and Python**

ElasticSearch is a highly scalable open-source full-text search and analytics engine. It allows you to store, search, and analyze big volumes of data quickly and in near real time.

Some use-cases of ElasticSearch are:

 * Full Text Search
 * Auto Completer
 * Analytics

In this tutorial we'll cover of How to connect to ElasticSearch using Python, inserting Data and Making query to ElasticSearch.

##**Installation**

Install ElasticSearch :

```sh
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.5.3.tar.gz
sha1sum elasticsearch-5.5.3.tar.gz 
tar -xzf elasticsearch-5.5.3.tar.gz
cd elasticsearch-5.5.3/ 
```
> **NOTE**:  Make sure you have Java already installed

Install ElasticSearch python client :

```sh
$ pip install elasticsearch
```


##**Let's Start**

### **Intro to ElasticSearch**

**Index**
An index is a collection of documents that have somewhat similar characteristics. For example, you can have an index for customer data, another index for a product catalog, and yet another index for order data. 
You can define as many indexes as you want.

**Type**
Within an index, you can define one or more types. A type is a logical category/partition of your index whose semantics is completely up to you.

**Document**
A document is a basic unit of information that can be indexed. For example, you can have a document for a single customer, another document for a single product, and yet another for a single order. This document is expressed in JSON format.

You can (very roughly) think of an index like a database.

MySQL => Databases => Tables => Columns/Rows
Elasticsearch => Indices => Types => Documents with Properties

###**Connecting to ElasticSearch**

Start ElasticSearch Server
```sh
./bin/elasticsearch
```
Connect to server from python code
```python
from elasticsearch import Elasticsearch
es = Elasticsearch()
```

###**Creating Index**
Just like we define schema for a Database, in ElasticSearch we need to define a mapping.

```python
request_body = {
            "mappings": {
                "blog": {
                    "properties": {
                        "author": {
                            "type": "text"
                        },
                        "title": {
                            "type": "text"
                        }
                    }
                }
            }
        }

res = es.indices.create(index = test_index, body = request_body)
```
*Mapping for type = Blog is defined above*

###**Adding data to Index**

Indexing a single document
```python
doc = {
    'author': 'RealPython',
    'text': 'Elasticsearch is cool'
}
res = es.index(index="test-index", doc_type='blog', id=1, body=doc)
```

Multiple document can be indexed using bulk function
```python
from elasticsearch import helpers

data = []
#Generating sample data
for x in range(0,1000):
	y = "realpython_" + str(x)
	z = "hello_" + str(x)
	row = {
	    "_index" : "test-index",
	    "_type" : "blog",
	    "_id" : x,
	    "_source" : {
	      "author": y,
	      "title": z
	    }
	}
	data.append(row)
	
helpers.bulk(es, data)
```

###**Time to Query!**

```python
res = es.search(index="test-index", body={"query": {"match_all": {}}})
for hit in res['hits']['hits']:
    print("%(author)s: %(title)s" % hit["_source"])
``` 


*Body contains the query to searched.* 

###**Conclusion**
With Python you can easily connect with ElasticSearch and insert Data, execute queries and Search data at almost real time. You can also see other types of queries in ElasticSearch [here.](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)
