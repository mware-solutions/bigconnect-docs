# Administration panel

As discussed in a previous section next to the username there are 2 icons: the first represents the **Activity** and the second the **Administration** panel.

Let us have a quick look at the **Administration** panel and to understand some basics. Please pay attention that this is available only if you have the **ADMIN** privilege. You can consult all the privileges linked to your account by going to **Preferences** under the username icon.

Another thing to remember is that privileges are not the same as roles. Currently only users with the administration role have full access to all BigConnect information. 

![](../../.gitbook/assets/image%20%2872%29.png)

As depicted above there are multiple subcategories and we will go in depth with the most important ones:

1. Behaviour  
2. Extract 
3. Ingest
4. Ontology
5. Plugin
6. REST
7. Security
8. System Notifications

### **Behaviour**

A behaviour is a union between multiple saved searches. Currently it can be used only in the **Dashboard** for visualization.

Let us take a basic example like returning all persons that have already paid their taxes for their properties this year but that do not have children yet.

For using a behaviour we should define 2 saved searches: TAXES\_PAID and NO\_CHILDREN.

Then we simply click on the **Add** button that will take us to the following: 

![](../../.gitbook/assets/image%20%2868%29.png)

A behaviour has an overall score, in this case 4, and we add the 2 saved queries using the **Add Query** button. Please take into consideration that the score is given only if the search query returns any data. If the score of a query is higher than the threshold then the behaviour will display the search results no matter the score of the other query. As a last mention if the same concept is found by both searches it will be displayed only once.

### Extract

Here we can configure certain patterns that will be applied only for text. Please pay attention to the fact that every audio file or video file can be interpreted and translated into text.

The UI is straightforward:

![](../../.gitbook/assets/image%20%2815%29.png)

### **Ingestion**

**Ingestion** is the process that governs data loading into BigConnect. There are multiple ways to perform it. The easiest was already covered in a previous section: use the graph tool and simply upload or drag and drop files.

However we might be interested in fetching data from a database. This is in fact possible and has to be configured in the **Ingest** section of the **Administration** panel.

![](../../.gitbook/assets/image%20%2843%29.png)

As we can see above the interface is very easy and intuitive. Initially we have to press the **Add** button and fill in all the required fields. An example can be seen below:

![](../../.gitbook/assets/image%20%2854%29.png)

Please notice the fact that the jdbc driver has to be in the classpath otherwise an error message will be shown:

![](../../.gitbook/assets/image%20%287%29.png)

Another issue that might occur is if the DB is not started or if the username password is wrong. Then the following error will be displayed:

![](../../.gitbook/assets/image%20%2823%29.png)

After successfully filling in all required information click on **Save**. A new row will appear in the data ingestion details table:

![](../../.gitbook/assets/image%20%2855%29.png)

The second step after we have a valid connection to a database is to create a datasource that will identify what data is to be fetched.

Click on the **+** sign.

![](../../.gitbook/assets/image%20%2875%29.png)

As we can see above there are multiple steps to be configured and if you want more details please check out our youtube channel. The the basic idea is that we configure a database connection, then specify the target tables for our data, specify the relationships according to our internal ontology and then we can finally start an actual data import job.

### Ontology

The power of BigConnect lies in its ability to correlate data using a specific ontology. Basically data is identified based on the setup preferences. For example if our use case needs a concept **City** to depict all city names then we can simply include it in the ontology and then all the data can be processed accordingly. 

By default BigConnect ships with a default ontology that can be modified to suit one's needs.  It is composed of 3 core components: concepts, properties and relationships. 

One important thing to remember is that properties are inherited so if we create a new concept **City** under **Thing** then we do not need to repeat all the already defined properties that **Thing** has. Please also consider the fact that if one wants to search using a property then that property has to be made searchable. Otherwise it won't have any impact in the data discovery process.

As a last remark in the Relationships section one can only see the custom relationships and not all the existing relationships. e.g. **Has Entity** will not be included.

### Plugin

This subcategory covers all the plugins registered in BigConnect, both backend and frontend. For more details about creating a plugin please have a look in the appropriate section from the Developer Guide.

### REST

This subcategory covers the exposed BigConnect API. One can use it to test certain endpoints directly from the UI, but it is also very useful if redesigning the web layer.

### Security

This subcategory addresses the security scope of BigConnect and it is splitted in 2:

1. Role management
2. User management

The role management comes by default with the "administrator" role that is able to view all the workspaces, all the configurations and so on.

The need for new roles can appear quite early in a team's lifecycle as it is not a good practice to share more information than needed inside a group of individuals. Basically a role will unite a group of privileges.

The user management part provides an overview of all registered users in BigConnect. 

![](../../.gitbook/assets/image%20%2824%29.png)

As we can see above the demo instance had 2 users, both OFFLINE \( meaning local \). BigConnect can have remote users powered by LDAP for example, but the overall functionality remains the same.

We can filter them by username, display name or status.

Also we can delete and edit users \( change email, name, privileges etc. \) .

An important thing to notice is that we can also add a new user directly from this panel by pressing the **Add** button.

![](../../.gitbook/assets/image%20%2847%29.png)

Currently though this is not very useful as BigConnect is mostly used within large companies with no external exposure so there is LDAP integration in place.

