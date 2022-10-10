# Application JSONB Filtering

Application JSONB filtering supports either the dot notation:

```
$.container.list[0].element
```
or the bracket–notation:

```
$['container']['list'][0]['element']
```

### Jsonb-filter expression

Every filter operation is sent using a POST request. Additionally, a new Content-Type header has
been made for application JSONB Filtering. An example can be seen below:
```
curl --location --request POST 'http://localhost:8181/rests/data/network-topology:network-topology/topology=uniconfig/node=SampleNodeName/frinx-uniconfig-topology:configuration' \
--header 'Content-Type: application/filter-data+json' \
--data-raw '{
    "query": "$.frinx-uniconfig-topology:configuration"
}'
```

The filter is located in the body of the request, not in the URI. Since it is located in the body, 
there is no need to escape characters. The body structure looks like this:

``` json
{
    "query": "$['container']['list'][0]"
}
```

If the user wants to filter the list elements based on name, the query filter would look like this:

``` json
{
    "query": "$['container']['list'][?(@.name == 'foo')]"
}
```

By default, the filter returns the same output structure as when calling a GET request. There is
an option to add the whole parent structure, where the body will look like this:

``` json
{
    "query": "$['container']['list'][?(@.name == 'foo')]",
    "addParentStructure": true
}
```

This will filter out all the elements in the **list** whose name is **foo**.

### Operators

Operators mentioned in the table below are used to construct a path.

| Operator                     | Description                                                     |
|------------------------------|-----------------------------------------------------------------|
| $                            | The root element to query. This starts all path expressions.    |
| @                            | The current node being processed by a filter predicate.         |
| *                            | Wildcard. Available anywhere a name or numeric are required.    |
| ..                           | Deep scan. Available anywhere a name is required.               |
| .\<name\>                    | Dot-notated child.                                              |
| \['\<name\>' (, '\<name\>')] | Bracket-notated child or children.                              |
| \[\<number\> (, \<number\>)] | Array index or indexes.                                         |
| \[start:end]                 | Array slice operator.                                           |
| \[?(\<expression\>)]         | Filter expression. Expression must evaluate to a boolean value. |

### Functions

Functions can be called at the end of the query path. The input to the function is the output of the path
expression. The function output is dictated by the function itself.

| Operator  | Description                                                        | Output Type |
|-----------|--------------------------------------------------------------------|-------------|
| min()     | Provides the min value of an array of numbers                      | Double      |
| max()     | Provides the max value of an array of numbers                      | Double      |
| avg()     | Provides the average value of an array of numbers                  | Double      |
| stddev()  | Provides the standard deviation value of an array of numbers       | Double      |
| length()  | Provides the length of an array                                    | Integer     |
| sum()     | Provides the sum value of an array of numbers                      | Double      |
| keys()    | Provides the property keys (An alternative for terminal tilde ~)   | Set<E>      |
| concat(X) | Provides a concatinated version of the path output with a new item | like input  |
| append(X) | add an item to the json path output array                          | like input  |

### Filter Operators

Filters are logical expressions used to filter arrays. A typical filter would be **[?(@.age > 18)]** where **@** represents 
the current element being processed. More complex filters can be created with logical operators **&&** and **||**. 
String literals must be enclosed by:
* A single quote: **[?(@.name == 'foo')]**
* A double quote: **[?(@.name == "foo")]**

| Operator | Description                                                                   |
|----------|-------------------------------------------------------------------------------|
| ==       | left is equal to right (note that 1 is not equal to '1')                      |
| !=       | left is not equal to right                                                    |
| <        | left is less than right                                                       |
| <=       | left is less or equal to right                                                |
| \>       | left is greater than right                                                    |
| \>=      | left is greater than or equal to right                                        |
| =~       | left matches regular expression  <br /> **\[?(@.name =~ /foo.*?/i)]**         |
| in       | left exists in right <br /> **[?(@.size in ['S', 'M'])]**                     |
| nin      | left does not exists in right                                                 |
| subsetof | left is a subset of right <br /> **[?(@.sizes subsetof ['S', 'M', 'L'])]**    |
| anyof    | left has an intersection with right <br /> **[?(@.sizes anyof ['M', 'L'])]**  |
| noneof   | left has no intersection with right <br /> **[?(@.sizes noneof ['M', 'L'])]** |
| size     | size of left (array or string) should match right                             |
| empty    | left (array or string) should be empty                                        |


### Jsonb-filter examples

Suppose we have the following data, and we want to do some filtering on them.

``` json
{
  "ietf-interfaces:interfaces": {
    "interface": [
      {
        "name": "eth0",
        "type": "ethernetCsmacd",
        "enabled": true,
        "speed": 10
      },
      {
        "name": "eth1",
        "type": "ethernetCsmacd",
        "enabled": true,
        "speed": 20
      },
      {
        "name": "eth1.10",
        "type": "l2vlan",
        "speed": 5
      },
      {
        "name": "lo1",
        "type": "softwareLoopback",
        "speed": 10
      }
    ],
    "action": {
      "name": "test-name",
      "type": "test-type"
    }
  },
  "fast": 10
}
```

| JsonPath                                                  | Description                                                       |
|-----------------------------------------------------------|-------------------------------------------------------------------|
| $.ietf-interfaces:interfaces.interface\[*].name           | The names of all interfaces                                       |
| $..name                                                   | All names                                                         |
| $.ietf-interfaces:interfaces.*                            | All things under interfaces                                       | 
| $.ietf-interfaces:interfaces..type                        | The type of everything                                            |
| $.ietf-interfaces:interfaces.interface\[2]                | The third interface                                               |
| $.ietf-interfaces:interfaces.interface\[-2]               | The second to last book                                           |
| $.ietf-interfaces:interfaces.interface\[0,1]              | The first two books                                               |
| $.ietf-interfaces:interfaces.interface\[:2]               | All interfaces from index 0 (inclusive) until index 2 (exclusive) |
| $.ietf-interfaces:interfaces.interface\[1:2]              | All interfaces from index 1 (inclusive) until index 2 (exclusive) |
| $.ietf-interfaces:interfaces.interface\[-2:]              | The last two interfaces                                           |
| $.ietf-interfaces:interfaces.interface\[2:]               | Interface number two from tail                                    |
| $.ietf-interfaces:interfaces.interface\[?(@.enabled)]     | All interfaces that have the enabled element                      |
| $.ietf-interfaces:interfaces.interface\[?(@.speed >= 10)] | All interfaces whose speed is greater or equal than 10            |
| $..interface\[?(@.speed <= $\['fast'])]                   | All interfaces that are not 'fast'                                |
| $..interface\[?(@.type =~ /.*Csmacd/i)]                   | All interfaces matching regex (ignore case)                       |
| $.ietf-interfaces:interfaces.interface.length()           | The number of interfaces                                          |

