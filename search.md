# Search

- [Introduction](#introduction)
- [Match All Query](#match-all-query)
- [Count Query](#count)
- [Full text queries](#full-text-queries)
    - [Match](#match)
    - [Match phrase](#match-phrase)
    - [Match Phrase Prefix Query](#match-phrase-prefix-query)
    - [Multi Match Query](#multi-match-query)
    - [Common](#common)
    - [Query String Query](#query-string-query)
    - [Simple Query String Query](#simple-query-string-query)
- [Term level queries](#term-level-queries)
    - [Term Query](#term-query)
    - [Terms Query](#terms-query)
    - [Terms Set Query](#terms-set-query)
    - [Range Query](#range-query)
    - [Prefix Query](#prefix-query)
    - [Wildcard Query](#wildcard-query)
    - [Regexp Query](#regexp-query)
    - [Fuzzy Query](#fuzzy-query)
    - [Ids Query](#ids-query)
- [Compound queries](#compound-queries)
    - [Constant Score Query](#constant-score-query)
    - [Bool Query](#bool-query)
    - [Function Score](#function-score)
    - [Nested Query](#nested-query)
    - [Has Child Query](#has-child-query)
    - [Has Parent Query](#has-parent-query)
- [Geo queries](#geo-queries)

<a name="introduction"></a>
## Introduction

<a name="match-all-query"></a>
## Match All Query
The most simple query, which matches all documents. 

    $response = Article::search()
        ->all();

Accepts `limit` parameter which restricts the number of documents to be returned. 

    $response = Article::search()
        ->all(50);



<a name="count"></a>
## Count

The count API allows to easily execute a query and get the number of matches for that query. It can be executed across one or more indices. 

    $response = Article::search()
        ->count();


<a name="full-text-queries"></a>
## Full text queries

<a name="match"></a>
### Match

Match queries accept text/numerics/dates, analyzes them, and constructs a query. For example:

    $response = Article::search()
        ->match("title", "a")
        ->get();

Parameter allowed `field`, `query`, `operator`, `fuzziness`, `zeroTermsQuery`, `cutoffFrequency `, `autoGenerateSynonymsPhraseQuery`.

Example of using operator and fuzziness :

    $response = Article::search()
        ->match("title", "foo bar", "and", 2)
        ->get(0, 100);
        

<a name="match-phrase"></a>
### Match phrase

The match_phrase query analyzes the text and creates a phrase query out of the analyzed text. For example:

    $response = Article::search()
        ->matchPhrase("title", "Mechanic Amigo")
        ->get();
        
Parameter allowed `field`, `query`, `analyzer`.

<a name="match-phrase-prefix-query"></a>
### Match Phrase Prefix Query

The match_phrase_prefix is the same as match_phrase, except that it allows for prefix matches on the last term in the text. For example:

    $response = Article::search()
        ->matchPhrasePrefix("title", "Mechanic Amigo")
        ->get();

<a name="multi-match-query"></a>
### Multi Match Query

The multi_match query builds on the match query to allow multi-field queries:

    $response = Article::search()
        ->multiMatch("foo", ["title", "slug"])
        ->get();
     

<a name="common"></a>
### Common

The common terms query is a modern alternative to stopwords which improves the precision and recall of search results 
(by taking stopwords into account), without sacrificing performance.

    $response = Article::search()
        ->common("title", "Mechanic")
        ->get();


<a name="query-string-query"></a>        
### Query String Query

A query that uses a query parser in order to parse its content. Here is an example:
    
    $response = Article::search()
        ->queryString("Mechanic AND Automatic OR Mechanic", "title")
        ->get();

Parameter allowed `query`, `defaultField`, `fields`, `params`.


<a name="simple-query-string-query"></a>        
### Simple Query String Query

A query that uses the SimpleQueryParser to parse its context. Unlike the regular query_string query, the 
simple_query_string query will never throw an exception, and discards invalid parts of the query.

    $data = Article::search()
        ->simpleQueryString("\"fried eggs\" +(eggplant | potato) -frittata", ["title^5", "body"])
        ->get();


<a name="term-level-queries"></a>
## Term level queries
While the full text queries will analyze the query string before executing, the term-level queries operate on the exact terms that are stored in the inverted index, and will normalize terms before executing only for keyword fields with normalizer property.

<a name="term-query"></a>
### Term Query
Returns documents that contain an exact term in a provided field.

    $data = Article::search()
        ->term("state", "Pa")
        ->get();
        

<a name="terms-query"></a>
### Terms Query
Returns documents that contain one or more exact terms in a provided field.

    $data = Article::search()
        ->terms("states", "articles", "2", "states")
        ->get();


<a name="terms-set-query"></a>
### Terms Set Query

Returns any documents that match with at least one or more of the provided terms. The terms are not analyzed and thus must match exactly. The number of terms that must match varies per document and is either controlled by a minimum should match field or computed per document in a minimum should match script.

The field that controls the number of required terms that must match must be a number field:
    
    $data = Job::search()
        ->termsSet("state", ["Pa", "Al"])
        ->get();


<a name="range-query"></a>
### Range Query
Matches documents with fields that have terms within a certain range. The type of the Lucene query depends on the field 
type, for string fields, the TermRangeQuery, while for number/date fields, the query is a NumericRangeQuery.

    $data = Article::search()
        ->range("age", ["gte" => 1, "lte" => 30])
        ->get();
            
            
<a name="prefix-query"></a>            
### Prefix Query

Matches documents that have fields containing terms with a specified prefix (not analyzed).

    $data = Article::search()
        ->prefix("state", "Pa")
        ->get();


<a name="wildcard-query"></a>            
### Wildcard Query

Returns documents that contain terms matching a wildcard pattern.

A wildcard operator is a placeholder that matches one or more characters. For example, the * wildcard operator matches zero or more characters. You can combine wildcard operators with other characters to create a wildcard pattern.
    
    $data = Article::search()
        ->wildcard("state", "P*")
        ->get();


<a name="regexp-query"></a>            
### Regexp Query
The regexp query allows you to use regular expression term queries. See Regular expression syntax for details of the supported regular expression language. The "term queries" in that first sentence means that Elasticsearch will apply the regexp to the terms produced by the tokenizer for that field, and not to the original text of the field.

    $data = Article::search()
        ->regexp("title", "Mech.*nic")
        ->get();

<a name="fuzzy-query"></a>            
### Fuzzy Query
The fuzzy query uses similarity based on Levenshtein edit distance.

    $data = Article::search()
        ->fuzzy("title", "Mechani")
        ->get();


<a name="ids-query"></a>            
### Ids Query
Returns documents based on their IDs.
    
    $data = Article::search()
        ->ids([1, 5])
        ->get();


<a name="compound-queries"></a>            
## Compound queries
Compound queries wrap other compound or leaf queries, either to combine their results and scores, to change their behaviour, or to switch from query to filter context.

<a name="constant-score-query"></a>
### Constant Score Query
A query that wraps another query and simply returns a constant score equal to the query boost for every document in the filter.

    $data = Article::search()
        ->constantScore([
            "filter" => [
                "term" => ["user" => "my_user"]
            ],
            "boost" => 1.2
        ])
        ->get();

<a name="bool-query"></a>
### Bool Query

A query that matches documents matching boolean combinations of other queries. The bool query maps to Lucene BooleanQuery. It is built using one or more boolean clauses, each clause with a typed occurrence.

    $data = Article::search()
        ->must(function (QueryBuilder $builder) {
            $builder->match("title", "Premier");
            $builder->match("title", "Advisor");
            return $builder;
        })
        ->filter(function (QueryBuilder $builder) {
            $builder->term("state", "Pa");
            $builder->term("organization", 59);
            return $builder;
        })
        ->get();


<a name="function-score"></a>
### Function Score

The function_score allows you to modify the score of documents that are retrieved by a query. This can be useful if, for example, a score function is computationally expensive and it is sufficient to compute the score on a filtered set of documents.

    $data = Article::search()
        ->functionScore(
            [
                "functions" => [
                    [
                        "filter" => [ "match" => [ "test" => "bar" ] ],
                        "weight" => 23
                    ]
                ]
            ],
        function (QueryBuilder $builder) {
            $builder->match("title", "Advisor");
                return $builder;
            }
        )
        ->get();
        

<a name="nested-query"></a>
### Nested Query

Nested query allows to query nested objects / docs (see nested mapping). The query is executed against the nested objects / docs as if they were indexed as separate docs (they are, internally) and resulting in the root parent doc (or parent nested mapping). Here is a sample mapping we will work with:

By using the query builder as callback:

    $data = Article::search()
        ->nested(
            "obj1",
            "avg",
            function (QueryBuilder $builder) {
                $builder->match("obj1.title", "Advisor");
                return $builder;
            }
        )
        ->get();

By using array: 


        $data = Article::search()
            ->nested(
                "obj1",
                "avg",
                [
                    "bool" => [
                        "must"  => [
                            [ "match"  => ["obj1.name"  => "blue"] ],
                            [ "range"  => ["obj1.count"  => ["gt"  => 5]] ]
                        ]
                    ]
                ]
            )
            ->get();
            
            
            
<a name="has-child-query"></a>
### Has Child Query

The has_child filter accepts a query and the child type to run against, and results in parent documents that have child docs matching the query. Here is an example:

    $data = Article::search()
        ->hasChild(
            "blog_tag",
            function (QueryBuilder $builder) {
                $builder->match("obj1.title", "Advisor");
                return $builder;
            }
        )
        ->get();
        
        
Or without using the query builder you can use your own parameters array:
    
    $data = Article::search()
        ->hasChild(
            "blog_tag",
            null,
            [...params]
        )
        ->get();

Has child function accepts other parameters like `$scoreMode`,  `$minChildren` and `$maxChildren`.

            
<a name="has-parent-query"></a>
### Has Parent Query

The has_parent query accepts a query and a parent type. The query is executed in the parent document space, which is specified by the parent type. This query returns child documents which associated parents have matched.

    $data = Article::search()
        ->hasParent(
            "blog",
            function (QueryBuilder $builder) {
                $builder->match("title", "Advisor");
                return $builder;
            }
        )
        ->get();
       
Or without using the query builder you can use your own parameters array:

    $data = Article::search()
            ->hasChild(
                "blog",
                null,
                [...params]
            )
            ->get();

<a name="geo-queries"></a>
### Geo queries

Refer to ElasticSearch documentation for more information how to use geo functions.
https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-queries.html

