# Artisan Console

- [Introduction](#introduction)
    - [Usage](#usage)
- [Datatypes](#datatypes)
    - [Alias datatype](#alias-datatype)
    - [Binary datatype](#binary-datatype)
    - [Range datatypes](#range-datatypes)
        - [Integer range datatype](#integer-range-datatype)
        - [Float range datatype](#float-range-datatype)
        - [Long range datatype](#long-range-datatype)
        - [Double range datatype](#double-range-datatype)
        - [Date range datatype](#date-range-datatype)
        - [Ip range datatype](#ip-range-datatype)
    - [Boolean datatype](#boolean-datatype)
    - [Date datatype](#date-datatype)
    - [Geo point datatype](#geo-point-datatype)
    - [Geo shape datatype](#geo-shape-datatype)
    - [Ip datatype](#ip-datatype)
    - [Nested datatype](#nested-datatype)
    - [Numeric datatype](#numeric-datatype)
        - [Long](#long)
        - [Integer](#integer)
        - [Short](#short)
        - [Byte](#byte)
        - [Double](#double)
        - [Float](#float)
    - [Object datatype](#object-datatype)
    - [Text datatype](#text-datatype)
    - [Join datatype](#join-datatype)
    - [Custom](#custom)
- [Custom analyzer](#custom-analyzer)
    - [Custom tokenizer](#custom-tokenizer)



<a name="introduction"></a>
## Introduction
Mapping is the process of defining how a document, and the fields it contains, are stored and indexed. For instance, use mappings to define:

- which string fields should be treated as full text fields.
- which fields contain numbers, dates, or geolocations.
- the format of date values.
- custom rules to control the mapping for dynamically added fields.

<a name="usage"></a>
### Usage
To use the mapping in the model the `SearchableInterface` should be implemented and the `Searchable` trait should be called.
`SearchableInterface` will make you implement the `mapUp` and `mapDown` methods. 

The `mapUp` method is where the field mapping should be done. Call `php artisan elasticMap:up` on the terminal to do the mapping. Example: 

    public static function mapUp()
    {
        // TODO: Implement mapUp() method.
    }

The `mapDown` method is used to delete the mapping:

    public static function mapDown()
    {
        self::map()->delete();
    }


<a name="datatypes"></a>
## Datatypes

<a name="alias-datatype"></a>
### Alias datatype
An alias mapping defines an alternate name for a field in the index.
    
    public static function mapUp()
    {
        $map = self::map()
            ->alias("alias_items", "items");
    }

<a name="binary-datatype"></a>
### Binary datatype
The binary type accepts a binary value as a Base64 encoded string. The field is not stored by default and is not searchable
    
    public static function mapUp()
    {
        $map = self::map()
            ->binary("binary_test");
    }

<a name="range-datatypes"></a>
### Range datatypes
The following range types are supported:

<a name="integer-range-datatype"></a>
#### Integer range datatype
A range of signed 32-bit integers with a minimum value of -231 and maximum of 231-1.

    public static function mapUp()
    {
        $map = self::map()
            ->integerRange("integer_range_test");
    }
    
<a name="float-range-datatype"></a>
#### Float range datatype
A range of single-precision 32-bit IEEE 754 floating point values.

    public static function mapUp()
    {
        $map = self::map()
            ->floatRange("float_range_test");
    }

    
<a name="long-range-datatype"></a>
#### Long range datatype

    public static function mapUp()
    {
        $map = self::map()
            ->longRange("float_range_test");
    }
    
    
<a name="double-range-datatype"></a>
#### Double range datatype
A range of double-precision 64-bit IEEE 754 floating point values.

    public static function mapUp()
    {
        $map = self::map()
            ->longRange("longRange_test");
    }
    
    
<a name="date-range-datatype"></a>
#### Date range datatype
A range of date values represented as unsigned 64-bit integer milliseconds elapsed since system epoch.

    public static function mapUp()
    {
        $map = self::map()
            ->dateRange("dateRange_test");
    }
    
<a name="ip-range-datatype"></a>
#### Ip range datatype
A range of ip values supporting either IPv4 or IPv6 (or mixed) addresses.

    public static function mapUp()
    {
        $map = self::map()
            ->ipRange("ipRange_test");
    }
    
<a name="boolean-datatype"></a>
### Boolean datatype
Boolean fields accept JSON true and false values, but can also accept strings which are interpreted as either true or false.

    public static function mapUp()
    {
        $map = self::map()
            ->boolean("boolean_test");
    }
    
<a name="date-datatype"></a>
### Date datatype
JSON doesn’t have a date datatype, so dates in Elasticsearch can either be:

- strings containing formatted dates, e.g. "2015-01-01" or "2015/01/01 12:10:30".
- a long number representing milliseconds-since-the-epoch.
- an integer representing seconds-since-the-epoch.

Internally, dates are converted to UTC (if the time-zone is specified) and stored as a long number representing milliseconds-since-the-epoch.

    public static function mapUp()
    {
        $map = self::map()
            ->date("date_test", "yyyy-MM-dd HH:mm:ss");
    }
    
<a name="geo-point-datatype"></a>
### Geo point datatype

Fields of type geo_point accept latitude-longitude pairs, which can be used:

- to find geo-points within a bounding box, within a certain distance of a central point, or within a polygon.
- to aggregate documents geographically or by distance from a central point.
- to integrate distance into a document’s relevance score.
- to sort documents by distance.


    public static function mapUp()
    {
        $map = self::map()
            ->geo_point("geo_point_test");
    }
       
<a name="geo-shape-datatype"></a>
### Geo shape datatype

The geo_shape datatype facilitates the indexing of and searching with arbitrary geo shapes such as rectangles and polygons. It should be used when either the data being indexed or the queries being executed contain shapes other than just points.

    public static function mapUp()
    {
        $map = self::map()
            ->geo_shape("geo_shape_test");
    }
       
<a name="ip-datatype"></a>
### Ip datatype
An ip field can index/store either IPv4 or IPv6 addresses.

    public static function mapUp()
    {
        $map = self::map()
            ->ip("ip_test");
    }
    
<a name="keyword-datatype"></a>
### Keyword datatype
A field to index structured content such as email addresses, hostnames, status codes, zip codes or tags.

They are typically used for filtering (Find me all blog posts where status is published), for sorting, and for aggregations. Keyword fields are only searchable by their exact value.

    public static function mapUp()
    {
        $map = self::map()
            ->keyword("keyword_test");
    }
    
<a name="nested-datatype"></a>
### Nested datatype
The nested type is a specialised version of the object datatype that allows arrays of objects to be indexed in a way that they can be queried independently of each other.

    public static function mapUp()
    {
        $map = self::map()
            ->nested("nested_test", function (IndexBuilder $mapper) {
                return $mapper->integer("nested1")
                    ->integer("nested2")
                    ->integer("nested3");
        });
    }
    
<a name="numeric-datatype"></a>
### Numeric datatype
The following numeric types are supported:

<a name="long"></a>
#### Long
A signed 64-bit integer with a minimum value of -263 and a maximum value of 263-1.

    public static function mapUp()
    {
        $map = self::map()
            ->long("long_test");
    }
    
<a name="integer"></a>
#### Integer
A signed 32-bit integer with a minimum value of -231 and a maximum value of 231-1.

    public static function mapUp()
    {
        $map = self::map()
            ->long("long_test");
    }
    
<a name="short"></a>
#### Short
A signed 16-bit integer with a minimum value of -32,768 and a maximum value of 32,767.

    public static function mapUp()
    {
        $map = self::map()
            ->short("short_test");
    }
    
<a name="byte"></a>
#### Byte
A signed 8-bit integer with a minimum value of -128 and a maximum value of 127.

    public static function mapUp()
    {
        $map = self::map()
            ->short("short_test");
    }
    
<a name="double"></a>
#### Double
A double-precision 64-bit IEEE 754 floating point number, restricted to finite values.

    public static function mapUp()
    {
        $map = self::map()
            ->double("double_test");
    }
    
<a name="float"></a>
#### Float
A single-precision 32-bit IEEE 754 floating point number, restricted to finite values.

    public static function mapUp()
    {
        $map = self::map()
            ->float("float_test");
    }
    
<a name="object-datatype"></a>
### Object datatype
JSON documents are hierarchical in nature: the document may contain inner objects which, in turn, may contain inner objects themselves.

    public static function mapUp()
    {
        $map = self::map()
            ->object("object_test", function ($mapper) {
                return $mapper->integer("object1")
                    ->integer("object2")
                    ->integer("object3");
                }
            );
    }
    
<a name="text-datatype"></a>
### Text datatype
A field to index full-text values, such as the body of an email or the description of a product. These fields are analyzed, that is they are passed through an analyzer to convert the string into a list of individual terms before being indexed.

    public static function mapUp()
    {
        $map = self::map()
            ->text("title");
    }
    
Advanced usage of text method by using fields. Multi-fields allow the same string value to be indexed in multiple ways for different purposes, such as one field for search and a multi-field for sorting and aggregations, or the same string value analyzed by different analyzers.

    public static function mapUp()
    {
        $map = self::map()
            ->text("title_integer", function ($mapper) {
                return $mapper->integer("items1")
                    ->integer("items2")
                    ->integer("items3");
            });
    }
        
<a name="join-datatype"></a>
### Join datatype
The join datatype is a special field that creates parent/child relation within documents of the same index. The relations section defines a set of possible relations within the documents, each relation being a parent name and a child name.

    public static function mapUp()
    {
        $map = self::map()
            ->join("join_test", "questions", "answer");
    }
   
Or with multiple children:

    public static function mapUp()
    {
        $map = self::map()
            ->join("join_test", "questions", ["answer", "comment"]);
    } 
    
         
<a name="custom"></a>
### Custom
   
A custom field (every field with custom params) can be created by using the `custom` method:

    public static function mapUp()
    {
        $map = self::map()
            ->custom("text", "title", []);
    } 
    
<a name="analyzer"></a>
## Analyzer
Example to set a analyzer to a field:

    public static function mapUp()
    {
        $map = self::map()
            ->text("title")->analyzer("standard");
    }
    
Creating custom analyzer by using custom : `tokenizer`, `filter` or `char filter`.

<a name="custom-tokenizer"></a>
### Custom tokenizer

    $map = self::map()
        ->configTokenizer(
            "standard",
            "my_tokenizer",
                [
                    'max_token_length' => 5
                ]
            )
        ->customAnalyzer("custom_analyzer", function (AnalyzerBuilder $builder) {
            $builder = new AnalyzerBuilder();

            $builder->tokenizer("my_tokenizer");

            return $builder;
        })
        ->text("my_test_int")->analyzer("custom_analyzer");
        
<a name="custom-filter"></a>
### Custom filter

    $map = self::map()
        ->configFilter("lowercase", "greek_lowercase", ["language" => "greek"])
        ->customAnalyzer("custom_analyzer", function (AnalyzerBuilder $builder) {
            $builder = new AnalyzerBuilder();

            $builder->filter("greek_lowercase");

            return $builder;
        })
        ->text("my_test_int")->analyzer("custom_analyzer");
        
<a name="custom-char-filter"></a>
### Custom char filter

    $map = self::map()
        ->configCharFilter("html_strip", "my_char_filter", ["escaped_tags" => ["b"]])
        ->customAnalyzer("custom_analyzer", function (AnalyzerBuilder $builder) {
            $builder = new AnalyzerBuilder();

            $builder->charFilter("my_char_filter");

            return $builder;
        })
        ->text("my_test_int")->analyzer("custom_analyzer");
                
<a name="custom-anlyzer-all-together"></a>
### Custom analyzer all together

    $map = self::map()
        ->configTokenizer(
            "standard",
            "my_tokenizer",
                [
                    'max_token_length' => 5
                ]
        )
        ->configFilter("lowercase", "greek_lowercase", ["language" => "greek"])
        ->configCharFilter("html_strip", "my_char_filter", ["escaped_tags" => ["b"]])
        ->customAnalyzer("custom_analyzer", function ($builder) {
            $builder = new AnalyzerBuilder();

            $builder->tokenizer("my_tokenizer")
                ->filter("asciifolding", "greek_lowercase")
                ->charFilter("my_char_filter");

            return $builder;
        })
        ->text("my_test_int")->analyzer("custom_analyzer");