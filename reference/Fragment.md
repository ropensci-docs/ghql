# Fragment

ghql fragment class

## Value

a \`Fragment\` class (R6 class)

## Public fields

- `fragments`:

  (list) list of fragments

## Methods

### Public methods

- [`Fragment$print()`](#method-Fragment-print)

- [`Fragment$fragment()`](#method-Fragment-fragment)

------------------------------------------------------------------------

### Method [`print()`](https://rdrr.io/r/base/print.html)

print method for the \`Fragment\` class

#### Usage

    Fragment$print(x, ...)

#### Arguments

- `x`:

  self

- `...`:

  ignored

------------------------------------------------------------------------

### Method `fragment()`

create a fragment by name

#### Usage

    Fragment$fragment(name, x)

#### Arguments

- `name`:

  (character) fragment name

- `x`:

  (character) the fragment

#### Returns

nothing returned; sets fragments internally

## Examples

``` r
# make a fragment class
frag <- Fragment$new()

# define a fragment
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

# define another fragment
frag$fragment('Stargazers', '
  fragment on Repository {
    stargazers(first: 3) {
      edges {
        node {
          name
       }
    }
  }
}')
frag
#> <ghql: fragment>
#>   fragments:
#>     Watchers 
#>     Stargazers 
frag$fragments
#> $Watchers
#> fragment on Repository {
#>     watchers(first: 3) {
#>       edges {
#>         node {
#>           name
#>        }
#>     }
#>   }
#> } 
#> 
#> $Stargazers
#> fragment on Repository {
#>     stargazers(first: 3) {
#>       edges {
#>         node {
#>           name
#>        }
#>     }
#>   }
#> } 
#> 
frag$fragments$Watchers
#> fragment on Repository {
#>     watchers(first: 3) {
#>       edges {
#>         node {
#>           name
#>        }
#>     }
#>   }
#> } 
frag$fragments$Stargazers
#> fragment on Repository {
#>     stargazers(first: 3) {
#>       edges {
#>         node {
#>           name
#>        }
#>     }
#>   }
#> } 
```
