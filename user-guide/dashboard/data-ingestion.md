# Administration panel

As discussed in a previous section next to the username there are 2 icons: the first represents the **Activity** and the second the **Administration** panel.

Let us have a quick look at the **Administration** panel and to understand some basics. Please pay attention that this is available only if you have the **ADMIN** privilege. You can consult all the privileges linked to your account by going to **Preferences** under the username icon.

![](../../.gitbook/assets/image%20%2867%29.png)

As depicted above there are multiple subcategories and we will go in depth with the most important ones:

1. Behaviour  
2. Extract 
3. **Ingest**
4. **Ontology**
5. Plugin
6. REST
7. **Security**
8. System Notifications

Ingestion is the process that governs data loading into BigConnect. There are multiple ways to perform it. The easiest was already covered in a previous section: use the graph tool and simply upload or drag and drop files.

However we might be interested in fetching data from a database. This is in fact possible and has to be configured in the **Ingest** section of the **Administration** panel.

![](../../.gitbook/assets/image%20%2841%29.png)

As we can see above the interface is very easy and intuitive. Initially we have to press the **Add** button and fill in all the required fields. An example can be seen below:

![](../../.gitbook/assets/image%20%2851%29.png)

Please notice the fact that the jdbc driver has to be in the classpath otherwise an error message will be shown:

![](../../.gitbook/assets/image%20%287%29.png)

Another issue that might occur is if the DB is not started or if the username password is wrong. Then the following error will be displayed:

![](../../.gitbook/assets/image%20%2822%29.png)

After successfully filling in all required information click on **Save**. A new row will appear in the data ingestion details table:

![](../../.gitbook/assets/image%20%2852%29.png)

The second step after we have a valid connection to a database is to create a datasource that will identify what data is to be fetched.

Click on the **+** sign.

![](../../.gitbook/assets/image%20%2870%29.png)

As we can see above there are multiple steps to be configured and they will be explained in more depth in a video tutorial, but the basic idea is that we configure a database connection, then specify the target tables for our data, specify the relationships according to our internal ontology and then we can finally start an actual data import job.

