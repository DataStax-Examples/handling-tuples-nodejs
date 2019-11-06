# Handling Tuples in NodeJs
This example demonstrates how to handle inserting and selecting tuples in NodeJs.

Contributors: [Jorge Bay Gondra](https://github.com/jorgebay) and [Andrew Tolbert](https://github.com/tolbertam), copied from [here](https://github.com/datastax/nodejs-driver/blob/master/examples/tuple/tuple-insert-select.js)

## Objectives

* To demonstrate how to insert and select tuples in NodeJs.
  
## Project Layout

* app.js - The main application file which contains all the logic to handle tuples

## How this Sample Works
This application works by connecting to a locally hosted DDAC/C*/DSE database, creating a keyspace and schema, and then demonstrating how to insert and retrieve tuples in NodeJs.

### Inserting Tuples
Inserting tuples in NodeJs requires using the `cassandra.types.Tuples` class and then passing that in as a parameter to the prepared statement as shown below:

```    // Create a new instance of a Tuple
    const currencies = new cassandra.types.Tuple('USD', 'EUR');
    const query = 'INSERT INTO examples.tuple_forex (name, time, currencies, value)  VALUES (?, ?, ?, ?)';
    const params = [ 'market1', cassandra.types.TimeUuid.now(), currencies, new cassandra.types.BigDecimal(11, 1) ];
    return client.execute(query, params, { prepare: true});
```

**Note** The `{ prepare: true}` is how to make a statement in NodeJs into a prepared statement, which is best practice.

### Retrieving Tuples
Tuples are retrieved in NodeJs as a Map of objects.  This means that to access them you need to use the `.get(index)` syntax as shown below:

```    
const row = result.first();
console.log('%s to %s: %s', row['currencies'].get(0), row['currencies'].get(1), row['value']);
```

## Setup and Running

### Prerequisites

* NodeJs version 8
* A DDAC, C*, or DSE Cluster

**Note** This application assumes that the cluster is running locally and that the datacenter is named `dc1`.  If this is not the case then you will need to change this in line 4:
`const client = new cassandra.Client({ contactPoints: ['127.0.0.1'], localDataCenter: 'dc1' });`

### Running
To run this application use the following command:

`node app.js`

This will produce the following output:

```
Inserting
USD to EUR: 1.1
```

