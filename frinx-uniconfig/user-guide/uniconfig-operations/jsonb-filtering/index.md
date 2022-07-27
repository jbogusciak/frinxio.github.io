# JSONB Filtering

Jsonb-filter is a query parameter that is used for filtering data based
on one or more parameters. This filter is an effective mechanism for
filtering a list of items. Using the jsonb-filter we can retrieve only
those list items that meet the defined conditions.

Currently, we have two options of how to use the JSONB filtering functionality.

## Database JSONB Filtering 
The query parameter is located in the URI. This option is faster because 
filtering is happening on the database side but this filtering has 
fewer features.

## Application JSONB Filtering
A new Content-Type is added. The query parameter is added in the body.
Additional query parameters can be chained (sort by, limit, fields). This 
request is sent as a POST request. This filtering adds more features, but it 
is happening on the UniConfig application side which will be slower than the 
database filtering.


