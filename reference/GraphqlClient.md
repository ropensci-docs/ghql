# GraphqlClient

R6 class for constructing GraphQL queries

## Value

a \`GraphqlClient\` class (R6 class)

## Public fields

- `url`:

  (character) list of fragments

- `headers`:

  list of named headers

- `schema`:

  holds schema

- `result`:

  holds result from http request

- `fragments`:

  (list) list of fragments

## Methods

### Public methods

- [`GraphqlClient$new()`](#method-GraphqlClient-new)

- [`GraphqlClient$print()`](#method-GraphqlClient-print)

- [`GraphqlClient$ping()`](#method-GraphqlClient-ping)

- [`GraphqlClient$load_schema()`](#method-GraphqlClient-load_schema)

- [`GraphqlClient$dump_schema()`](#method-GraphqlClient-dump_schema)

- [`GraphqlClient$schema2json()`](#method-GraphqlClient-schema2json)

- [`GraphqlClient$fragment()`](#method-GraphqlClient-fragment)

- [`GraphqlClient$exec()`](#method-GraphqlClient-exec)

- [`GraphqlClient$prep_query()`](#method-GraphqlClient-prep_query)

------------------------------------------------------------------------

### Method [`new()`](https://rdrr.io/r/methods/new.html)

Create a new \`GraphqlClient\` object

#### Usage

    GraphqlClient$new(url, headers)

#### Arguments

- `url`:

  (character) URL for the GraphQL schema

- `headers`:

  Any acceptable headers, a named list. See examples

#### Returns

A new \`GraphqlClient\` object

------------------------------------------------------------------------

### Method [`print()`](https://rdrr.io/r/base/print.html)

print method for the \`GraphqlClient\` class

#### Usage

    GraphqlClient$print(x, ...)

#### Arguments

- `x`:

  self

- `...`:

  ignored

------------------------------------------------------------------------

### Method `ping()`

ping the GraphQL server

#### Usage

    GraphqlClient$ping(...)

#### Arguments

- `...`:

  curl options passed on to \[crul::verb-HEAD\]

#### Returns

\`TRUE\` if successful response, \`FALSE\` otherwise

------------------------------------------------------------------------

### Method `load_schema()`

load schema, from URL or local file

#### Usage

    GraphqlClient$load_schema(schema_url = NULL, schema_file = NULL, ...)

#### Arguments

- `schema_url`:

  (character) url for a schema file

- `schema_file`:

  (character) path to a schema file

- `...`:

  curl options passed on to \[crul::verb-GET\]

#### Returns

nothing, loads schema into \`\$schema\` slot

------------------------------------------------------------------------

### Method `dump_schema()`

dump schema to a local file

#### Usage

    GraphqlClient$dump_schema(file)

#### Arguments

- `file`:

  (character) path to a file

#### Returns

nothing, writes schema to \`file\`

------------------------------------------------------------------------

### Method `schema2json()`

convert schema to JSON

#### Usage

    GraphqlClient$schema2json(...)

#### Arguments

- `...`:

  options passed on to \[jsonlite::toJSON()\]

#### Returns

json

------------------------------------------------------------------------

### Method `fragment()`

load schema, from URL or local file

#### Usage

    GraphqlClient$fragment(name, x)

#### Arguments

- `name`:

  (character) fragment name

- `x`:

  (character) the fragment

#### Returns

nothing returned; sets fragments internally

------------------------------------------------------------------------

### Method `exec()`

execute the query

#### Usage

    GraphqlClient$exec(
      query,
      variables,
      encoding = "UTF-8",
      response_headers = FALSE,
      ...
    )

#### Arguments

- `query`:

  (character) a query, of class \`query\` or \`fragment\`

- `variables`:

  (list) named list with query variables values

- `encoding`:

  (character) encoding to use to parse the response. passed on to
  \[crul::HttpResponse\] \`\$parse()\` method. default: "UTF-8"

- `response_headers`:

  If \`TRUE\`, include the response headers as an attribute of the
  return object.

- `...`:

  curl options passed on to \[crul::verb-POST\]

#### Returns

character string of response, if successful

------------------------------------------------------------------------

### Method `prep_query()`

not used right now

#### Usage

    GraphqlClient$prep_query(query)

#### Arguments

- `query`:

  (character) a query, of class \`query\` or \`fragment\`

## Examples

``` r
x <- GraphqlClient$new()
x
#> <ghql client>
#>   url: 

if (FALSE) { # \dontrun{
# make a client
token <- Sys.getenv("GITHUB_TOKEN")
cli <- GraphqlClient$new(
  url = "https://api.github.com/graphql",
  headers = list(Authorization = paste0("Bearer ", token))
)

# if the GraphQL server has a schema, you can load it
cli$load_schema()

# dump schema to local file
f <- tempfile(fileext = ".json")
cli$dump_schema(file = f)
readLines(f)
jsonlite::fromJSON(readLines(f))

# after dumping to file, you can later read schema from file for faster loading
rm(cli)
cli <- GraphqlClient$new(
  url = "https://api.github.com/graphql",
  headers = list(Authorization = paste0("Bearer ", token))
)
cli$load_schema(schema_file = f)

# variables
cli$url
cli$schema
cli$schema$data
cli$schema$data$`__schema`
cli$schema$data$`__schema`$queryType
cli$schema$data$`__schema`$mutationType
cli$schema$data$`__schema`$subscriptionType
head(cli$schema$data$`__schema`$types)
cli$schema$data$`__schema`$directives


# methods
## ping - hopefully you get TRUE
cli$ping()

## dump schema
cli$schema2json()


## define query
### creat a query class first
qry <- Query$new()
## another
qry$query('repos', '{
  viewer {
    repositories(last: 10, isFork: false, privacy: PUBLIC) {
      edges {
        node {
          isPrivate
          id
          name
        }
      }
    }
  }
}')
qry
qry$queries
qry$queries$repos
### execute the query
cli$exec(qry$queries$repos)


# query with a fragment
### define query without fragment, but referring to it
qry <- Query$new()
qry$query('queryfrag', '{
  ropensci: repositoryOwner(login:"ropensci") {
    repositories(first: 3) {
      edges {
        node {
          ...Watchers
        }
      }
    }
  }
  ropenscilabs: repositoryOwner(login:"ropenscilabs") {
    repositories(first: 3) {
      edges {
        node {
          ...Watchers
        }
      }
    }
  }
}')

### define a fragment
frag <- Fragment$new()
frag$fragment('Watchers', '
  fragment on Repository {
    watchers(first: 3) {
      edges {
        node {
          name
       }
    }
  }
}')
frag$fragments
frag$fragments$Watchers

### add the fragment to the query 'queryfrag'
qry$add_fragment('queryfrag', frag$fragments$Watchers)
qry
qry$queries$queryfrag

### execute query: we'll hook together the query and your fragment internally
cli$exec(qry$queries$queryfrag)
} # }
```
