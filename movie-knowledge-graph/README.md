# 🎬 Movie Knowledge Graph

A hands-on learning project for **Knowledge Graphs** and **Semantic Web** technologies, implementing the same movie database using two different approaches: **RDF/SPARQL** and **Neo4j/Cypher**.

---

## 📖 What is This Project?

This project is my personal learning journey into knowledge graphs. It demonstrates how to:

1. **Transform tabular data** (CSV) into interconnected knowledge graphs
2. **Compare two major approaches**: RDF (Semantic Web) vs Property Graphs (Neo4j)
3. **Query knowledge graphs** using SPARQL and Cypher
4. **Analyze and visualize** graph-structured data

### Why Two Approaches?

| Aspect | RDF/SPARQL | Neo4j/Cypher |
|--------|------------|--------------|
| **Standard** | W3C Standard | Industry Standard |
| **Data Model** | Triples (Subject-Predicate-Object) | Nodes + Relationships |
| **Query Style** | Pattern matching with namespaces | ASCII-art graph patterns |
| **Best For** | Semantic reasoning, linked data | Performance, intuitive queries |

---

## What is a Knowledge Graph?

A **Knowledge Graph** stores information as a network of **entities** (nodes) and **relationships** (edges), instead of traditional tables.

### Example: Traditional Table vs Knowledge Graph

**Traditional (Relational):**
```
Movies Table: ID=1, Title="Inception", Director_ID=5
Directors Table: ID=5, Name="Christopher Nolan"
```

**Knowledge Graph:**
```
(Inception)--[DIRECTED_BY]-->(Christopher Nolan)
(Inception)--[HAS_GENRE]-->(Sci-Fi)
(Interstellar)--[DIRECTED_BY]-->(Christopher Nolan)
```

The power? You can now easily ask: *"What other movies did Inception's director make?"* - just follow the edges!

### RDF Triple Structure

In RDF, every fact is a **triple**: `Subject → Predicate → Object`

```turtle
movie:Inception  movie:directedBy  movie:ChristopherNolan .
movie:Inception  movie:hasGenre    movie:SciFi .
movie:Inception  movie:hasRating   "8.8"^^xsd:float .
```

---

## 🏗 Project Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     movies.csv (Raw Data)                   │
│  movie_id, title, year, genres, director, rating            │
└─────────────────────┬───────────────────────────────────────┘
                      │
        ┌─────────────┴─────────────┐
        ▼                           ▼
┌───────────────────┐     ┌───────────────────┐
│   RDF Builder     │     │   Neo4j Loader    │
│   (rdf_builder.py)│     │   (neo4j_loader.py)│
└─────────┬─────────┘     └─────────┬─────────┘
          │                         │
          ▼                         ▼
┌───────────────────┐     ┌───────────────────┐
│  Turtle File      │     │   Neo4j Database  │
│  (.ttl)           │     │   (Graph DB)      │
│                   │     │                   │
│  Query: SPARQL    │     │  Query: Cypher    │
└───────────────────┘     └───────────────────┘
          │                         │
          └────────────┬────────────┘
                       ▼
              ┌─────────────────┐
              │   Analytics &   │
              │  Visualizations │
              └─────────────────┘
```

---

## 🚀 Quick Start

### Prerequisites
- Python 3.8+
- (Optional) Podman or Docker for Neo4j

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/movie-knowledge-graph.git
cd movie-knowledge-graph

# Create virtual environment
python -m venv venv
venv\Scripts\activate  # Windows
# source venv/bin/activate  # macOS/Linux

# Install dependencies
pip install -r requirements.txt
```

### Run the Project

```bash
# Run full pipeline (works even without Neo4j!)
python run.py

# Or run specific components:
python run.py --rdf-only      # Build RDF graph only
python run.py --neo4j-only    # Neo4j only (requires Neo4j running)
python run.py --demo          # Interactive demo
```

### (Optional) Start Neo4j with Podman

```bash
podman run -d \
  --name movie-neo4j \
  -p 7474:7474 \
  -p 7687:7687 \
  -e NEO4J_AUTH=neo4j/password \
  neo4j:latest
```

Then access Neo4j Browser at: http://localhost:7474

---

## 📁 Project Structure

```
movie-knowledge-graph/
├── run.py                    # Main entry point
├── requirements.txt          # Python dependencies
├── data/
│   └── raw/movies.csv        # Source dataset (7 sample movies)
├── src/
│   ├── rdf_builder.py        # RDF/SPARQL implementation
│   ├── neo4j_loader.py       # Neo4j/Cypher implementation
│   ├── analytics.py          # Query comparisons
│   ├── pipeline.py           # Orchestrates everything
│   └── visualizations.py     # Charts and graphs
├── queries/
│   ├── sparql_examples.py    # SPARQL query collection
│   └── cypher_examples.py    # Cypher query collection
├── notebooks/
│   └── exploration.ipynb     # Interactive Jupyter notebook
├── ontology/
│   └── movie_ontology.ttl    # Formal ontology definition
└── output/
    ├── movie_knowledge_graph.ttl   # Generated RDF graph
    └── visualizations/             # Generated charts
```

---

## 🔍 Query Examples

### SPARQL (RDF) - Find movies by director

```sparql
PREFIX movie: <http://movie-kg.org/ontology#>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>

SELECT ?title ?year ?rating
WHERE {
    ?m a movie:Movie ;
       movie:hasTitle ?title ;
       movie:releasedIn ?year ;
       movie:hasRating ?rating ;
       movie:directedBy ?director .
    ?director foaf:name "Christopher Nolan" .
}
ORDER BY ?year
```

### Cypher (Neo4j) - Same query, different syntax

```cypher
MATCH (m:Movie)-[:DIRECTED_BY]->(d:Director {name: "Christopher Nolan"})
RETURN m.title AS title, m.year AS year, m.rating AS rating
ORDER BY m.year
```

### Results (both queries return the same data):

| title | year | rating |
|-------|------|--------|
| The Dark Knight | 2008 | 9.0 |
| Inception | 2010 | 8.8 |
| Interstellar | 2014 | 8.6 |

---

## 📊 What Gets Generated

When you run `python run.py`, the project:

1. **Builds an RDF Knowledge Graph** → `output/movie_knowledge_graph.ttl`
   - Creates triples for movies, directors, genres
   - Defines ontology classes and properties
   
2. **Loads into Neo4j** (if available)
   - Creates nodes: Movie, Director, Genre
   - Creates relationships: DIRECTED_BY, HAS_GENRE

3. **Runs Analytics**
   - Compares SPARQL vs Cypher query results
   - Shows director and genre analysis

4. **Generates Visualizations** → `output/visualizations/`
   - Genre distribution charts
   - Knowledge graph network diagram
   - Director analysis plots

---

## 📚 Key Concepts I Learned

### RDF & Semantic Web
- **Triple**: The atomic unit - (Subject, Predicate, Object)
- **URI**: Unique identifiers for resources
- **Namespace**: Prefixes to avoid naming conflicts
- **Ontology**: Formal definition of concepts and relationships
- **SPARQL**: SQL-like query language for RDF

### Neo4j & Property Graphs
- **Node**: An entity with labels and properties
- **Relationship**: A typed, directed connection between nodes
- **Cypher**: Declarative graph query language using ASCII art patterns

### When to Use What?
- **Use RDF** when you need: standards compliance, reasoning, linked data, data integration
- **Use Neo4j** when you need: performance, intuitive queries, real-time applications

---

## 📖 Resources

- [RDFLib Documentation](https://rdflib.readthedocs.io/)
- [SPARQL 1.1 Specification](https://www.w3.org/TR/sparql11-query/)
- [Neo4j Documentation](https://neo4j.com/docs/)
- [W3C RDF Primer](https://www.w3.org/TR/rdf11-primer/)

---

*Created as a learning project for Knowledge Graphs and Semantic Web technologies.*
