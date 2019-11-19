# Querying Tuples with Apache Cassandra™ in NodeJs
[Tuples](https://docs.datastax.com/en/dse/6.7/cql/cql/cql_using/refTupleType.html) are a handy data type in Cassandra for more complex data structures. Tuples are similar to User Defined Types ( [UDT](https://docs.datastax.com/en/dse/6.7/cql/cql/cql_using/refUDType.html)s ) but their fields are not named and they are [frozen](https://docs.datastax.com/en/dse/6.7/cql/cql/cql_using/refCollectionType.html) by default. This simple example shows how to write and read tuples using the [Node.js DataStax Driver](https://docs.datastax.com/en/developer/nodejs-driver/latest/) and Cassandra.

Contributor(s): [Jorge Bay Gondra](https://github.com/jorgebay), [Andrew Tolbert](https://github.com/tolbertam) - derived from [here](https://github.com/datastax/nodejs-driver/blob/master/examples/tuple/tuple-insert-select.js)

## Objectives

* Demonstrate how to insert and select tuples using the Node.js DataStax Driver with Cassandra.
  
## Project Layout

* [app.js](app.js) - The main application file which contains all the logic to handle tuples

## How this Sample Works

### Inserting Tuples
Inserting tuples in NodeJs requires using the `cassandra.types.Tuples` class and then passing that in as a parameter to the prepared statement as shown below:

```
// Create a new instance of a Tuple
const currencies = new cassandra.types.Tuple('USD', 'EUR');
const query = 'INSERT INTO examples.tuple_forex (name, time, currencies, value)  VALUES (?, ?, ?, ?)';
const params = [ 'market1', cassandra.types.TimeUuid.now(), currencies, new cassandra.types.BigDecimal(11, 1) ];
return client.execute(query, params, { prepare: true});
```

**Note** The `{ prepare: true}` is how to make a statement in Node.js into a prepared statement, see [Prepared Statements in Node.js example](https://github.com/DataStax-Examples/prepared-statements-nodejs) for more details.

### Retrieving Tuples
Tuples are retrieved in NodeJs as a Map of objects.  This means that to access them you need to use the `.get(index)` syntax as shown below:

```    
const row = result.first();
console.log('%s to %s: %s', row['currencies'].get(0), row['currencies'].get(1), row['value']);
```

## Setup and Running

### Prerequisites

* NodeJs version 8
* A Cassandra, DDAC, DSE cluster or Apollo database ( docker is a nice option for local install - [see docs](https://docs.datastax.com/en/docker/doc/docker/dockerQuickStart.html) )

**Note** This application assumes that the cluster is running locally and that the datacenter is named `dc1`.  If this is not the case then you will need to change this in [app.js](app.js):

`const client = new cassandra.Client({ contactPoints: ['127.0.0.1'], localDataCenter: 'dc1' });`

### Running
To run this application use the following command:

`node app.js`

This will produce the following output:

```
Inserting
USD to EUR: 1.1
```

