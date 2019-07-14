# Scroll Search

- [Introduction](#introduction)
- [Usage](#usage)

<a name="introduction"></a>
## Introduction

While a search request returns a single “page” of results, the scroll API can be used to retrieve large numbers of results (or even all results) from a single search request, in much the same way as you would use a cursor on a traditional database.

Scrolling is not intended for real time user requests, but rather for processing large amounts of data, e.g. in order to reindex the contents of one index into a new index with a different configuration.


<a name="usage"></a>
## Usage

    $search = Article::scrollSearch()
        ->query(function (\Draotix\LaravelModelElasticsearching\QueryBuilder $builder) {
            $builder->match("title", "foo");
            return $builder;
        });
    
        
    $result = [];
    while ($search->next()) {
        $response = $search->getResponse();

        $result[] = $response->getData();    
    }