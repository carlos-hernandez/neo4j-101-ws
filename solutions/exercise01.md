# Exercise 1: Solutions

1. Create a single node with no labels and no properties
    ```Cypher
    CREATE ();
    ```
1. Create multiple nodes in a single CREATE statement
    ```Cypher
    CREATE (n1:Node), (n2:Node);
    ```
1. Create a node with multiple labels
    ```Cypher
    CREATE (n:Label1:Label2);
    ```
1. Remove a label from a node
    ```Cypher
    MATCH (n:Label1)
    REMOVE n:Label2;
    ```
1. Create a node and return it in the same statement
    ```Cypher
    CREATE (n) RETURN n;
    ```
1. Create a full path in a single CREATE statement
    ```Cypher
    CREATE p = (n1:Node { value: 1 })-[:IS_RELATED_TO]->(n2:Node { value: 2 })
    RETURN p;
    ```
1. Update an existing node and set a property if a match is found
    ```Cypher
    MATCH (n:Node { value: 1 })
    SET n.key = 'key'
    RETURN n;
    ```
1. Copy properties between two nodes
    ```Cypher
    MATCH (n1:Node { value: 1 }), (n2:Node { value: 2 })
    SET n1 = n2
    RETURN n1, n2;
    ```
1. Delete a node with existing relationships
    ```Cypher
    MATCH (n:Node { value: 1 })
    DETACH DELETE n;
    ```
1. Delete an specific type of relationship
    ```Cypher
    MATCH (n:Node)-[r:IS_RELATED_TO]->()
    DELETE r;
    ```
1. Delete a node with a specific label
    ```Cypher
    MATCH (n:Label1)
    DETACH DELETE n;
    ```
