# Administration panel

As discussed in a previous section next to the username there are 2 icons: the first represents the **Activity** and the second the **Administration** panel.

Let us have a quick look at the **Administration** panel and to understand some basics. Please pay attention that this is available only if you have the **ADMIN** privilege. You can consult all the privileges linked to your account by going to **Preferences** under the username icon.

![](../../.gitbook/assets/image%20%2869%29.png)

As depicted above there are multiple subcategories and we will go in depth with the most important ones:

1. Behaviour  
2. Extract 
3. **Ingest**
4. **Ontology**
5. Plugin
6. REST
7. **Security**
8. System Notifications

### **Ingestion**

**Ingestion** is the process that governs data loading into BigConnect. There are multiple ways to perform it. The easiest was already covered in a previous section: use the graph tool and simply upload or drag and drop files.

However we might be interested in fetching data from a database. This is in fact possible and has to be configured in the **Ingest** section of the **Administration** panel.

![](../../.gitbook/assets/image%20%2842%29.png)

As we can see above the interface is very easy and intuitive. Initially we have to press the **Add** button and fill in all the required fields. An example can be seen below:

![](../../.gitbook/assets/image%20%2853%29.png)

Please notice the fact that the jdbc driver has to be in the classpath otherwise an error message will be shown:

![](../../.gitbook/assets/image%20%287%29.png)

Another issue that might occur is if the DB is not started or if the username password is wrong. Then the following error will be displayed:

![](../../.gitbook/assets/image%20%2822%29.png)

After successfully filling in all required information click on **Save**. A new row will appear in the data ingestion details table:

![](../../.gitbook/assets/image%20%2854%29.png)

The second step after we have a valid connection to a database is to create a datasource that will identify what data is to be fetched.

Click on the **+** sign.

![](../../.gitbook/assets/image%20%2872%29.png)

As we can see above there are multiple steps to be configured and they will be explained in more depth in a video tutorial, but the basic idea is that we configure a database connection, then specify the target tables for our data, specify the relationships according to our internal ontology and then we can finally start an actual data import job.

### Ontology

The power of BigConnect lies in its ability to correlate data using a specific ontology. Basically data is identified based on the setup preferences. For example if our use case needs a concept **City** to depict all city names then we can simply include it in the ontology and then all the data can be processed accordingly. 

By default BigConnect ships with a default ontology that can be modified to suit one's needs.  One important thing to remember is that properties are inherited so if we create a new concept **City** under **Thing** of course then we do not need to repeat all the already defined properties that **Thing** has.

### Security

This subcategory addresses the security scope of BigConnect and it is splitted in 2:

1. Role management
2. User management

The role management comes by default with the "administrator" role that is able to view all the workspaces, all the configurations and so on.

The need for new roles can appear quite early in a team's lifecycle as it is not a good practice to share more information than needed inside a group of individuals. Basically a role will unite a group of privileges.

The user management part provides an overview of all registered users in BigConnect. 

![](../../.gitbook/assets/image%20%2823%29.png)

As we can see above the demo instance had 2 users, both OFFLINE \( meaning local \). BigConnect can have remote users powered by LDAP for example, but the overall functionality remains the same.

We can filter them by username, display name or status.

Also we can delete and edit users \( change email, name, privileges etc. \) .

An important thing to notice is that we can also add a new user directly from this panel by pressing the **Add** button.

![](../../.gitbook/assets/image%20%2846%29.png)

Currently though this is not very useful as BigConnect is mostly used within large companies with no external exposure so there is LDAP integration in place.
