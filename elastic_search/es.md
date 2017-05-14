# Elastic Search

## Start up command
```
./elasticsearch -Ecluster.name=my_cluster_name -Enode.name=my_node_name
```

## Exact Values VS. Full Text

Exact values are exactly what they sound like.  
Full text refers to textual data -- usually witten in some human language -- like the text of a tweet or the body of an email.

## Analysis and Analyzers

### Analysis is a process that consists of the following:
- First, tokenizing a block of text into individual terms suitalbe for use in an inverted index.
- Then normalizing these terms into a standard form to improve their "searchability", or recall.

This job is performed by analyzers.

### An analyzer is really just a wrapper taht combines three functions into a single package:
#### Character filters
- First, the string is passed through any character filters in turn. Their job is to tide up the string before tokenization. A character filter could be used to strip out HTML, or to convert & characters to the work _and_.

#### Tokenizer
- Next, the string is tokenized into individual terms by a tokenizer. A simple tokenizer might split the text into terms whenever it encounters whitespace or punctuation.
#### Token filters
- Last, each term is passed through any toek filters in turn, which can change terms, remove terms, or add terms.

### Built-in Analyzers
Sample String
````
"Set the shape to semi-transparent by calling set_trans(5)"
````
#### Standard analyzer
- Default analyzer Elasticsearch uses.
Sample output
````
set, the, shape, to, semi, transparent, by, calling, set_trans, 5
````
#### Simple analyzer
- splits the text on anything that isn't a letter 
- loercases the terms.
Sample output
````
set, the, shape, to, semi, transparent, by, calling, set, trans
````

#### Whitespace analyzer
- split the text on whitespace
- does not lowercase
Sample output
````
Set, the, shape, to, semi-transparent, by, calling, set_trans(5)
````

#### Language analyzers
- able to take the peculiarities of the specified language into account
- able to stem English words
Sample output
````
set, shape, semi, transpar, call, set_tran, 5
````

### When Analyzers Are Used
- When querying a full-text field, the query will apply the same analyzer to the query string to produce the correct list of terms to search for.
- When querying an exact-value field, the query will not analyze the query string, but instead search for the exact value that you have specified.

### Testing Analuzers
````
curl -XGET 'localhost:9200/_analyze?pretty' -H 'Content-Type: application/json' -d'
{
  "analyzer": "standard",
  "text": "Text to analyze"
}'
````

## Mapping
### Core Simple Field Types
Supported simple field types:
- String: string
- Whole number: byte, short, integer, long
- Floating-point: float, double
- Boolean: boolean
- Date: date
#### The string field datatype has been replaced by the text field for full text analyzed content, and the keyword field for not-analyzed exact string values. 

### View the mapping
````
curl -XGET 'localhost:9200/gb/_mapping/tweet?pretty'
````

### Customizing Field Mappings

- Distinguish between full-text string fields and exact value string fields
- Use language-specific analyzers
- Optimize a field for partial matching
- specify custom date formats
- And much more

#### index
The _index_ attribute control how the string will be indexed. Avaliable values:
- analyzed
    - First analyze the string and then index it. In other words, index this field as full text.
- not_analyzed
    - Index this field, so it is searchable, but the index the value exactly as specified. Do not analyze it.
- no
    - Don't index this field at all. This field will not be searchable.

### Updateing a Mapping
New fields can be added, but existing field cannot be changed.

#### Delete the index
````
curl -XDELETE 'localhost:9200/gb?pretty'
````
#### Create a new index, and specify the tweet field to use the _english_ analyzer:
````
curl -XPUT 'localhost:9200/gb?pretty' -H 'Content-Type: application/json' -d'
{
  "mappings": {
    "tweet" : {
      "properties" : {
        "tweet" : {
          "type" :    "string",
          "analyzer": "english"
        },
        "date" : {
          "type" :   "date"
        },
        "name" : {
          "type" :   "string"
        },
        "user_id" : {
          "type" :   "long"
        }
      }
    }
  }
}
'
````
#### Add a new field
````
curl -XPUT 'localhost:9200/gb/_mapping/tweet?pretty' -H 'Content-Type: application/json' -d'
{
  "properties" : {
    "tag" : {
      "type" :    "string",
      "index":    "not_analyzed"
    }
  }
}
'
````

## Queries

### match_all Query
It simply matches all documents. It is the default query that is used if no query has been specified.

### match Query
- If against a full-text field, it will analyze the query string by using the correct analyzer for that field before executing the search.
- If on a field containing an exact value, such as a number, a date, a Boolean, or a not_analyzed string field, then it will search for that exact value.

### multi_match Query
- It allows to run the same match query on multiple fields.

### range Query
- It allows to find numbers or dates that fall into a specified range.
