## Flask, Neo4j, and Docker

This is a little example of Flask and Neo4j running together in Docker. You will need to export environment variables `NEO4J_USER` and `NEO4J_PASS`, otherwise the username and password for the Neo4j database will default to `neo4j` and `password`. Build and run the application with docker-compose. On Linux:

```
export NEO4J_USER=<username>
export NEO4J_Pass=<password>
docker-compose Build
docker-compose up
```

The application will run at http://localhost:5000. You can query the database using the `query` url parameter. If you want to use variables in your query, you can add them in as extra url parameters. For example:

`http://localhost:5000?query=CREATE (p:Person {name: "Joe"})`
```
[]
```

`http://localhost:5000?query=CREATE (p:Person) SET p.name={name}, p.age={yrsOld} RETURN p&name=Jack&yrsOld=49`
```
[
  {
    "p": "<Node id=6 labels={'Person'} properties={'age': '49', 'name': 'Jack'}>"
  }
]
```

`http://localhost:5000?query=MERGE (a:Person {name: {person1}})-[r:Friends]->(b:Person {name: {person2}}) RETURN a as person1, b as person2, r as relation&person1=Joe&person2=Jack`

```
[
  {
    "person1": "<Node id=7 labels={'Person'} properties={'name': 'Joe'}>",
    "person2": "<Node id=8 labels={'Person'} properties={'name': 'Jack'}>",
    "relation": "<Relationship id=1 start=7 end=8 type='Friends' properties={}>"
  }
]
```

When you run the application, a directory will be created in the hosts file system at $HOME/neo4j, exposing the data, import, config and logs volumes. The neo4j database will persist in the hosts $HOME/neo4j/data directory. To change a password you must do so through neo4j, changing the NEO4J_USER and NEO4J_PASS environment variables will not automatically change the password and will only result in authentication errors.
