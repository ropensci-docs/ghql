# ghql

General purpose GraphQL client

## ghql API

The main interface in this package is \[GraphqlClient\], which produces
a client (R6 class) with various methods for interacting with a GraphQL
server. \[GraphqlClient\] also accepts various input parameters to set a
base URL, and any headers required, which is usually the required set of
things needed to connect to a GraphQL service.

\[Query\] is an interface to creating GraphQL queries, which works
together with \[GraphqlClient\]

\[Fragment\] is an interface to creating GraphQL fragments, which works
together with \[GraphqlClient\]

## See also

Useful links:

- <https://docs.ropensci.org/ghql/>

- <https://github.com/ropensci/ghql>

- Report bugs at <https://github.com/ropensci/ghql/issues>

## Author

Scott Chamberlain <myrmecocystus@gmail.com>
