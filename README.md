📘 README (English Version)
# Abnormal Behavior Knowledge Graph (ABKG)

This repository provides a modular, Neo4j-ready knowledge graph dataset for **abnormal behavior detection**.  
The structure is designed for computer-vision–based systems, combining:

- **Visually detectable entities** (Person, Object, Behavior, Scene, Crowd)
- **Inference-level semantic events** (Fighting, ArmedAssault, etc.)
- **Multi-file modular CSV structure**
- **Neo4j-compatible ID format** (`:ID`, `:START_ID`, `:END_ID`, `:TYPE`)

You can load this graph:

- **Locally** using Neo4j Desktop / Neo4j Server  
- **Online** using **Neo4j AuraDB (cloud / web)** without any installation

---

# 📂 Folder Structure



abnormal-behavior-knowledge-graph/
│
├── Person.csv
├── Behavior.csv
├── Object.csv
├── Scene.csv
├── Crowd.csv
├── SemanticBehavior.csv
│
├── Person_performed_Behavior.csv
├── Person_holds_Object.csv
├── Person_locatedIn_Scene.csv
├── Object_locatedIn_Scene.csv
├── Crowd_locatedIn_Scene.csv
├── SemanticBehavior_involvesPerson.csv
└── SemanticBehavior_involvesObject.csv


---

# 🧩 Node Types

| Node Type | Description |
|-----------|-------------|
| **Person** | Humans detected by vision |
| **Behavior** | Low-level actions (walking, running, falling…) |
| **Object** | Detected physical objects (gun, knife, flame, smoke) |
| **Scene** | Physical environment (mall, school corridor…) |
| **Crowd** | Group density / motion patterns |
| **SemanticBehavior** | Inferred high-level events (Fighting, ArmedAssault…) |

---

# 🔗 Relationship Types

| Relationship | Meaning |
|--------------|---------|
| **PERFORMED** | Person → Behavior |
| **HOLDS** | Person → Object |
| **LOCATED_IN** | Person → Scene |
| **OBJECT_LOCATED_IN** | Object → Scene |
| **CROWD_LOCATED_IN** | Crowd → Scene |
| **INVOLVES_PERSON** | SemanticBehavior → Person |
| **INVOLVES_OBJECT** | SemanticBehavior → Object |

---

# 🧠 Knowledge Graph Schema (Mermaid Diagram)

```mermaid
graph TD
    Person --> |PERFORMED| Behavior
    Person --> |HOLDS| Object
    Person --> |LOCATED_IN| Scene
    Object --> |OBJECT_LOCATED_IN| Scene
    Crowd --> |CROWD_LOCATED_IN| Scene

    SemanticBehavior --> |INVOLVES_PERSON| Person
    SemanticBehavior --> |INVOLVES_OBJECT| Object

🖥️ Load the Graph Locally (Neo4j Desktop / Neo4j Server)
1. Copy all CSV files into your Neo4j import directory:
<neo4j-home>/import/

2. Open Neo4j Browser:

http://localhost:7474

3. Run the following Cypher:
▶ Load Nodes
LOAD CSV WITH HEADERS FROM "file:///Person.csv" AS row
MERGE (:Person {id: row.`id:ID(Person)`});

LOAD CSV WITH HEADERS FROM "file:///Behavior.csv" AS row
MERGE (:Behavior {id: row.`id:ID(Behavior)`, behavior_type: row.behavior_type});

LOAD CSV WITH HEADERS FROM "file:///Object.csv" AS row
MERGE (:Object {id: row.`id:ID(Object)`, object_type: row.object_type});

LOAD CSV WITH HEADERS FROM "file:///Scene.csv" AS row
MERGE (:Scene {id: row.`id:ID(Scene)`, scene_type: row.scene_type});

LOAD CSV WITH HEADERS FROM "file:///Crowd.csv" AS row
MERGE (:Crowd {id: row.`id:ID(Crowd)`, density: row.density});

LOAD CSV WITH HEADERS FROM "file:///SemanticBehavior.csv" AS row
MERGE (:SemanticBehavior {id: row.`id:ID(SemanticBehavior)`, semantic_type: row.semantic_type});

▶ Load Relationships
LOAD CSV WITH HEADERS FROM "file:///Person_performed_Behavior.csv" AS row
MATCH (p:Person {id: row.`:START_ID(Person)`})
MATCH (b:Behavior {id: row.`:END_ID(Behavior)`})
MERGE (p)-[:PERFORMED]->(b);

LOAD CSV WITH HEADERS FROM "file:///Person_holds_Object.csv" AS row
MATCH (p:Person {id: row.`:START_ID(Person)`})
MATCH (o:Object {id: row.`:END_ID(Object)`})
MERGE (p)-[:HOLDS]->(o);

LOAD CSV WITH HEADERS FROM "file:///Person_locatedIn_Scene.csv" AS row
MATCH (p:Person {id: row.`:START_ID(Person)`})
MATCH (s:Scene {id: row.`:END_ID(Scene)`})
MERGE (p)-[:LOCATED_IN]->(s);

LOAD CSV WITH HEADERS FROM "file:///Object_locatedIn_Scene.csv" AS row
MATCH (o:Object {id: row.`:START_ID(Object)`})
MATCH (s:Scene {id: row.`:END_ID(Scene)`})
MERGE (o)-[:OBJECT_LOCATED_IN]->(s);

LOAD CSV WITH HEADERS FROM "file:///Crowd_locatedIn_Scene.csv" AS row
MATCH (c:Crowd {id: row.`:START_ID(Crowd)`})
MATCH (s:Scene {id: row.`:END_ID(Scene)`})
MERGE (c)-[:CROWD_LOCATED_IN]->(s);

LOAD CSV WITH HEADERS FROM "file:///SemanticBehavior_involvesPerson.csv" AS row
MATCH (sb:SemanticBehavior {id: row.`:START_ID(SemanticBehavior)`})
MATCH (p:Person {id: row.`:END_ID(Person)`})
MERGE (sb)-[:INVOLVES_PERSON]->(p);

LOAD CSV WITH HEADERS FROM "file:///SemanticBehavior_involvesObject.csv" AS row
MATCH (sb:SemanticBehavior {id: row.`:START_ID(SemanticBehavior)`})
MATCH (o:Object {id: row.`:END_ID(Object)`})
MERGE (sb)-[:INVOLVES_OBJECT]->(o);

☁️ Load the Graph on Neo4j AuraDB (Cloud)
1. Open Aura Console:

https://console.neo4j.io/

2. Select your instance → click Import → Upload files

Upload all 13 CSV files.

3. Click Connect → Open Browser
4. Run the same Cypher import scripts above

file:///YourFile.csv works directly in Aura.

No installation. Fully web-based.

🔍 Example Usage Queries
Find all semantic events:
MATCH (s:SemanticBehavior)-[r]->(n)
RETURN s, r, n;

Who holds dangerous objects?
MATCH (p:Person)-[:HOLDS]->(o:Object)
RETURN p, o;

Activities happening in a mall:
MATCH (x)-[:LOCATED_IN]->(s:Scene {scene_type:"Mall"})
RETURN x;

📄 License

MIT License

🤝 Contributing

Pull requests are welcome — especially:

New behavior types

Additional object classes

Extended scenes

More semantic event rules
