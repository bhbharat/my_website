---
date: 2022-01-20
title: Neo4j & Cypher
linkTitle: Neo4j & Cypher
description: >
  Copy the codes from here to run in Neo4j.
author: Bharat Bhushan
resources:
  - src: "**.{png,jpg}"
    title: "Image"
---


{{< imgproc neo4j Fill "500x200" >}}
{{< /imgproc >}}

This blog contains codes and snippets related to Neo4j, Cypher and apoc library. Copy the codes from here to run in Neo4j. 

## Neo4j


```bash
bin\neo4j console
bin\neo4j-admin dump --to=import\NER.dump --database=neo4j
bin\neo4j-admin load --from=import\NER.dump --database=neo4j
```

```python

def run_queries(graph,query):
    for i in query.split(';'):
        graph.run(i)
def import_graphml(output_file,graph,delete=True):
    if delete:
        graph.delete_all()
    graph.run(f"""  
    call apoc.import.graphml('file:///C:/Users/if441f/2022_Projects/NCR/{output_file}',{{}})
    """)
    graph.run("""
    match (n)
    call apoc.create.addLabels(id(n),[split(n.labels,":")[1]]) 
    yield node
    remove n.labels
    return node
    """)
    graph.run("""
    match (n)-[r]->(n1)
    remove r.label
    """)

def save_graphml(output_file,graph):
    graph.run(f"""   
        call apoc.export.graphml.all('file:///C:/Users/if441f/2022_Projects/NCR/{output_file}',{{}})
        """)

def create_constraints(graph,columns):
    for i in columns:
        try:
            graph.run(f"CREATE CONSTRAINT ON (n:{i}) ASSERT n.name IS UNIQUE")
        except:
            pass
  
def drop_constraints(graph,columns):
    for i in columns:
        try:
            graph.run(f"DROP CONSTRAINT ON (n:{i}) ASSERT n.name IS UNIQUE")
        except:
            pass

```

```cypher

// Periodic Iterate
call apoc.periodic.iterate(  
    "load csv with headers from 'file:///C:/Users/if441f/2022_Projects/NCR/input/cleaned_files/cd_page_web_columns.csv' as row
return row",
" 
    match (n:NC_KEY) where n.name=row.NC_KEY
    merge (n1:Defect {name:row.Defect})
    merge (n)-[:Defect]->(n1)
",{batchSize:1000}
) yield batches return batches

// see meta data
call apoc.meta.graph()

```

```cypher

// add following lines in conf/apoc.conf file:
apoc.import.file.enabled=true 
apoc.export.file.enabled=true 
apoc.import.file.use_neo4j_config=false 
apoc.export.file.use_neo4j_config=false

// add following lines in conf/neo4j.conf file:
dbms.security.procedures.unrestricted=algo.,apoc.,gds.* 
dbms.connector.bolt.listen_address=:7688 
dbms.connector.http.listen_address=:7475

// save graphml dataset
call apoc.export.graphml.all('test.graphml',{})

// load graphml dataset
call apoc.import.graphml('dsrm_export.graphml',{});
match (n)
call apoc.create.addLabels(id(n),[split(n.labels,":")[1]]) 
yield node
return node
```

```cypher
// load csv and create nodes/relationships
load csv with headers from "file:///Cleaned_tables.csv" as row
with keys(row) as columns, row
unwind columns  as column
with column,row
where column<>"NC_KEY" and column<>'null' and row[column] <> "null"
merge (n:NCR {name:row.NC_KEY})
with column,row
call apoc.cypher.doIt("match (n:NCR {name:'"+row.NC_KEY+"'}) "+"merge (n)-[:HAS]->(n1:"+column+" {name:'"+row[column]+"'})",{})
yield value
return value
```

```cypher
// Rename columns
match (n)
with collect(n) as nodes,n
unwind [['ACC_CD',"Area_Control_Code"],['AP_DESC','Airplance_Description'],
['IT_DESC',"Item_Description"],['IT_ID','Item_Id'],['MDL_DESC','Model_Description'],['PHY_IT_LOC_TXT','Item_Location'],
['REC_TYP','Record_Type'],['REV_NO','Revision_No']] as rename
call apoc.refactor.rename.label(rename[0],rename[1],nodes) 
yield committedOperations 
return committedOperations 
```

```cypher
// change relationship 
match (n0:NARR_NO)-->(n1:SEQ_VER_NO)-[r]->(n2:Date)
call apoc.refactor.from(r,n0) yield input,output,error return input,output,error

// collapse node
match (n0:NARR_NO)-->(n1:SEQ_VER_NO)-->(n2:Discrepancy)
call apoc.refactor.collapseNode([n1,n2],'HAS') yield input,output,error return input,output,error
```

```cypher
// merge nodes
match (n:AP_DESC)
with n.name as name, collect(n) as nodes
call apoc.refactor.mergeNodes(nodes, {properties:"override",mergeRels:true}) yield node return node

// unlist a property
match (n)-[r]->(n1:Part) 
where labels(n) in [['Discrepancy'],['Disposition']]
with n,split(n1.name,",") as part
unwind part as p
with n,p
where p<>""
merge (k:Part_No {name:trim(p)})
merge (n)-[:HAS]->(k)

// invert relationships
MATCH (n:NC_KEY)-[r]->(n1)
where labels(n1) in [['AP_DESC'],['JOB_ID'],['REL_WRK_PKG_ID'],['LN_NO'],['ACC_CD'],['MDL_DESC'],['IT_DESC'],['IT_ID']]
CALL apoc.refactor.invert(r) yield input, output
RETURN input, output

```


