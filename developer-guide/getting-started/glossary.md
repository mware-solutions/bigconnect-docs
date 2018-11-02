# Glossary

### vertex <a id="vertex"></a>

A node in the graph that can have properties and edges to other vertices.

### edge <a id="edge"></a>

A connection between two vertices in the graph.

### data model \(ontology\) <a id="ontology"></a>

The valid concepts, properties, and relationships for a BigConnect installation.

### concept <a id="concept"></a>

A type defined in the data model \(e.g. person, place, or company\). Uniquely identified by an IRI and assigned to every vertex in the graph.

### IRI <a id="iri"></a>

Internationalized Resource Identifier. e.g. **person** or **worksFor**

### property <a id="property"></a>

A field defined in the data model as valid for one or more concepts. Uniquely identified by an IRI and optionally set on vertices in the graph.

### relationship <a id="relationship"></a>

A connection defined in the data model as valid from one concept to another. Uniquely identified by an IRI and stored as edges between vertices in the graph.

### DW <a id="gpw"></a>

Acronym for Data Worker.

### data worker <a id="graph-property-worker"></a>

Type of BigConnect plugin that responds to changes in the graph and often used for data enrichment and analytics. DWs can respond to creation or update events on vertices, properties, or edges.

### raw <a id="raw"></a>

The property on a vertex used to store any imported data.

### visibility <a id="visibility"></a>

The data access control applied to vertices, properties, and edges. The term 'visibility' is borrowed from Accumulo.

### visibility source <a id="visibility-source"></a>

The data stored on behalf of the visibility user interface component to support displaying and editing data access control settings. This data is converted by the configured [VisibilityTranslator](https://github.com/mware-solutions/bigconnect/blob/master/bc-common/src/main/java/com/mware/core/security/VisibilityTranslator.java) to the [BcVisibility](https://github.com/mware-solutions/bigconnect/blob/master/bc-common/src/main/java/com/mware/core/security/BcVisibility.java) value used to enforce data access control.

### visibility json <a id="visibility-json"></a>

A JSONObject consisting of the visibility source and a list of workspace ids. This value is stored as metadata on all vertices, properties, and edges to support data access control.

### authorization <a id="authorization"></a>

The data access control rights granted to BigConnect users to control their access to vertices, properties, and edges. The term 'authorization' is borrowed from Accumulo.

### workspace \(or space\) <a id="case"></a>

A named collection of vertices that can be shared for collaboration with other BigConnect users. New and changed vertices, properties, and edges are only visible in a workspace until being published by a user with the PUBLISH privilege.

