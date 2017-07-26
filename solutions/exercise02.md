# Exercise 2: Solutions

1. Load the CSV files found in the `exercise02` folder to create the graph.
    ```Cypher
    CREATE CONSTRAINT ON (player:Player) ASSERT player.id IS UNIQUE;
    
    USING PERIODIC COMMIT 300
    LOAD CSV WITH HEADERS FROM "file:///exercise02/players.csv" AS csvLine
    CREATE (player:Player { id: toInteger(csvLine.id), name: csvLine.name });

    CREATE CONSTRAINT ON (club:Club) ASSERT club.id IS UNIQUE;
    
    USING PERIODIC COMMIT 300
    LOAD CSV WITH HEADERS FROM "file:///exercise02/clubs.csv" AS csvLine
    CREATE (club:Club { id: toInteger(csvLine.id), name: csvLine.name });

    CREATE CONSTRAINT ON (nationalTeam:NationalTeam) ASSERT nationalTeam.id IS UNIQUE;
    
    USING PERIODIC COMMIT 300
    LOAD CSV WITH HEADERS FROM "file:///exercise02/nationalTeams.csv" AS csvLine
    CREATE (nationalTeam:NationalTeam { id: toInteger(csvLine.id), name: csvLine.name });

    USING PERIODIC COMMIT 300
    LOAD CSV WITH HEADERS FROM "file:///exercise02/rels-player-club.csv" AS csvLine
    MATCH (player:Player { id: toInteger(csvLine.playerId)}), (club:Club { id: toInteger(csvLine.clubId)})
    CREATE (player)-[:PLAYS_FOR]->(club);

    USING PERIODIC COMMIT 300
    LOAD CSV WITH HEADERS FROM "file:///exercise02/rels-player-nationalTeam.csv" AS csvLine
    MATCH (player:Player { id: toInteger(csvLine.playerId)}), (nationalTeam:NationalTeam { id: toInteger(csvLine.nationalTeamId)})
    CREATE (player)-[:REPRESENTS]->(nationalTeam);

    DROP CONSTRAINT ON (player:Player) ASSERT player.id IS UNIQUE;
    DROP CONSTRAINT ON (nationalTeam:NationalTeam) ASSERT nationalTeam.id IS UNIQUE;
    DROP CONSTRAINT ON (club:Club) ASSERT club.id IS UNIQUE;

    MATCH (n)
    WHERE n:Player OR n:Club OR n:NationalTeam
    REMOVE n.id;
    ```
1. Once the data is loaded, answer the following questions:
    * Which are the top 10 clubs with the most players being called by their national teams? Display the club name and the number of international players.
        ```Cypher
        MATCH (club:Club)<-[PLAYS_FOR]-()-[:REPRESENTS]->()
        RETURN club.name AS Club, count(*) AS `International Players`
        ORDER BY `International Players`
        DESC LIMIT 10;
        ```
    * Using the top 1 club from the previous question, who are the players and where do they come from? Display the country name, the number of players and a list with its names.
        ```Cypher
        MATCH (club:Club)<-[:PLAYS_FOR]-(player:Player)-[:REPRESENTS]->(nationalTeam:NationalTeam)
        WHERE club.name='Manchester United'
        RETURN nationalTeam.name AS Country, count(*) AS Count, collect(player.name) AS `International Players`
        ORDER BY Count DESC;
        ```
    * In which clubs are playing the members of a given national team? Display the club name, the number of players and a list with its names.
        ```Cypher
        MATCH (club:Club)<-[:PLAYS_FOR]-(player:Player)-[:REPRESENTS]->(nationalTeam:NationalTeam)
        WHERE nationalTeam.name = 'England'
        RETURN club.name AS Club, count(*) AS Count, collect(player.name) AS `International Players`
        ORDER BY Count DESC;
        ```
    * What country needs to pick from less clubs to build their national team? Display the country name and the number of distinct clubs.
        ```Cypher
        MATCH (club:Club)<-[:PLAYS_FOR]-()-[:REPRESENTS]->(nationalTeam:NationalTeam)
        RETURN nationalTeam.name AS Country, count(DISTINCT club) AS Distinct_Teams
        ORDER BY Distinct_Teams;
        ```
