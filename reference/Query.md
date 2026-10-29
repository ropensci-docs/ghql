# Query

ghql query class

## Value

a \`Query\` class (R6 class)

## Note

we run an internal method \`check_query()\` that runs the public method
\`parse2json()\` - if the query doesn't pass the libgraphqlparser
parser, we return the error message

## Public fields

- `queries`:

  (list) list of queries

## Methods

### Public methods

- [`Query$print()`](#method-Query-print)

- [`Query$query()`](#method-Query-query)

- [`Query$add_fragment()`](#method-Query-add_fragment)

- [`Query$parse2json()`](#method-Query-parse2json)

------------------------------------------------------------------------

### Method [`print()`](https://rdrr.io/r/base/print.html)

print method for the \`Query\` class

#### Usage

    Query$print(x, ...)

#### Arguments

- `x`:

  self

- `...`:

  ignored

------------------------------------------------------------------------

### Method `query()`

define query in a character string

#### Usage

    Query$query(name, x)

#### Arguments

- `name`:

  (character) name of the query

- `x`:

  (character) the query

#### Returns

nothing returned; sets query with \`name\` internally

------------------------------------------------------------------------

### Method `add_fragment()`

add a fragment to a query

#### Usage

    Query$add_fragment(query_name, fragment)

#### Arguments

- `query_name`:

  (character) the query name to add the fragment to

- `fragment`:

  (character) the fragment itself

#### Returns

nothing returned; sets the fragment with the query

------------------------------------------------------------------------

### Method `parse2json()`

parse query string with libgraphqlparser and get back JSON

#### Usage

    Query$parse2json(query, parse_schema = FALSE)

#### Arguments

- `query`:

  (character) a query to parse

- `parse_schema`:

  (logical) enable schema definition parsing? default: \`FAlSE\`

#### Returns

adf

## Examples

``` r
# make a client
qry <- Query$new()

## define query
qry$query('query2', '{
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
#> <ghql: query>
#>   queries:
#>     query2    
qry$queries
#> $query2
#>  
#>  {
#>   viewer {
#>     repositories(last: 10, isFork: false, privacy: PUBLIC) {
#>       edges {
#>         node {
#>           isPrivate
#>           id
#>           name
#>         }
#>       }
#>     }
#>   }
#> } 
#> 
qry$queries$query2
#>  
#>  {
#>   viewer {
#>     repositories(last: 10, isFork: false, privacy: PUBLIC) {
#>       edges {
#>         node {
#>           isPrivate
#>           id
#>           name
#>         }
#>       }
#>     }
#>   }
#> } 

# fragments
## by hand
qry$query('querywithfrag', '{
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
}
fragment Watchers on Repository {
  watchers(first: 3) {
    edges {
      node {
        name
      }
    }
  }
}')
qry
#> <ghql: query>
#>   queries:
#>     query2    
#>     querywithfrag    
qry$queries
#> $query2
#>  
#>  {
#>   viewer {
#>     repositories(last: 10, isFork: false, privacy: PUBLIC) {
#>       edges {
#>         node {
#>           isPrivate
#>           id
#>           name
#>         }
#>       }
#>     }
#>   }
#> } 
#> 
#> $querywithfrag
#>  
#>  {
#>   ropensci: repositoryOwner(login:"ropensci") {
#>     repositories(first: 3) {
#>       edges {
#>         node {
#>           ...Watchers
#>         }
#>       }
#>     }
#>   }
#>   ropenscilabs: repositoryOwner(login:"ropenscilabs") {
#>     repositories(first: 3) {
#>       edges {
#>         node {
#>           ...Watchers
#>         }
#>       }
#>     }
#>   }
#> }
#> fragment Watchers on Repository {
#>   watchers(first: 3) {
#>     edges {
#>       node {
#>         name
#>       }
#>     }
#>   }
#> } 
#> 
qry$queries$querywithfrag
#>  
#>  {
#>   ropensci: repositoryOwner(login:"ropensci") {
#>     repositories(first: 3) {
#>       edges {
#>         node {
#>           ...Watchers
#>         }
#>       }
#>     }
#>   }
#>   ropenscilabs: repositoryOwner(login:"ropenscilabs") {
#>     repositories(first: 3) {
#>       edges {
#>         node {
#>           ...Watchers
#>         }
#>       }
#>     }
#>   }
#> }
#> fragment Watchers on Repository {
#>   watchers(first: 3) {
#>     edges {
#>       node {
#>         name
#>       }
#>     }
#>   }
#> } 


if (FALSE) { # \dontrun{
token <- Sys.getenv("GITHUB_TOKEN")
con <- GraphqlClient$new(
  url = "https://api.github.com/graphql",
  headers = list(Authorization = paste0("Bearer ", token))
)
jsonlite::fromJSON(con$exec(qry$queries$querywithfrag))

## use Fragment class fragments generator
### define query without fragment, but referring to it
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

### define a fragment, and use it later
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
qry$queries
qry$queries$queryfrag
} # }
```
