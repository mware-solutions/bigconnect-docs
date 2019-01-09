# Getting started

The first thing you will see is the login screen.

![Login](../../.gitbook/assets/image%20%2860%29.png)

If you are using the default installation guide you can introduce any username and click the "Log In" button.

This will bring you to the welcome page called **Dashboard**.

![](../../.gitbook/assets/image%20%282%29.png)

Important things to notice 

* The title of the dashboard is **Default - demo** \( where **Default -** is simply a prefix and **demo** is the logged in username as you can see in the top right corner ****\) . This dashboard was created automatically and won't be recreated on subsequent logins.
* The caret next to the dashboard name enables the user to do several actions like sharing, renaming or deleting the dashboard. 

**Attention**! By default the dashboard is private and can be seen only by the current user.

![](../../.gitbook/assets/image%20%2847%29.png)

* The **Welcome to BigConnect** widget offers the basic information regarding all the actions that a user can trigger in the landing screen. 
* The most important two are **Discover** and **Analyze** which can be found right at the top besides the **Dashboard** tab. ****The former handles creating, editing and saving searches and the later data visualization, enrichment, persistence etc.

![](../../.gitbook/assets/image%20%2865%29.png)

* Present on the navigation bar we can find the following:

![](../../.gitbook/assets/image%20%2821%29.png)

The first icon is the **Activity**. As we learned earlier BigConnect supports long running processes. In order to track their progress after being started one can find them depicted in this view. Please pay attention that regular/system notifications are not to be shown here.

The second icon represents the **Administration** page. This is shown only if the user has the appropriate privileges. 

The third represents the **username**. If one clicks on it two new options are displayed : **Preferences** and **Logout**. The former offers multiple user related settings as the later logs out the logged in user.

* Underneath the navigation bar one can find 2 buttons : **Edit** and **Refresh**. As one might suspect when clicking on **Edit** the whole dashboard becomes interactive. The **Refresh** performs live reload of the available dashboard widgets. Let's click on **Edit**.

![](../../.gitbook/assets/image%20%283%29.png)

As one can see above there is now the possibility to remove certain widget or to add new ones.

The **Welcome to BigConnect** is useful only for new joiners so let us remove that. Also we may want to rearrange the widgets a bit. Please remember to click the **Done Editing** button when finished or simply press the **Esc** key otherwise nothing will be saved. Please find below our new simplified dashboard : 

![](../../.gitbook/assets/image%20%2812%29.png)

Currently we have 3 widgets present on our dashboard : **Entity Counts** \( amount of independent entities we have \), **Notifications** \( user related notifications \) and **Relationship Counts** \( number of relations between our entities \).

**Attention!** If one decides to remove the **Notifications** widget then one will not be able to see any notifications. \( e.g. Being added to a new space \)

These initial widgets only show basic information. For real use cases one will use the more advanced widgets. Let us click once again the **Edit** button and then **Add** widget in order to have a look at what other widgets are available.

![](../../.gitbook/assets/image%20%2820%29.png)

![](../../.gitbook/assets/image%20%288%29.png)

1. **Behaviour** - ****enables a user to configure an existing behaviour and to display it on the **Dashboard**. 
2. **Entity Counts** - displays the total counts of concepts or entities loaded in BigConnect. It will be shown as a pie chart.
3. **Notifications** - self explanatory displaying system and user notifications.
4. **Relationship Counts** - displays the total number of relationships available between all existing entities. It will be shown as a bar chart.
5. **Saved Search** - runs and displays an up to date saved search on the available data
6. **Search Results** - displays tabular saved search results for performing additional aggregations
7. **Welcome to BigConnect** - basic information about BigConnect functionalities

**Attention!** Please take into consideration that this is not limited to your user, but in fact to all existing users. 

### Example dashboard

Until now we only configured the dashboard but no data was available. Please have a look below at a simple example and let's analyze one by one the widgets. In the coming sections we will understand more about how data is ingested and manipulated to provide the target insights.

![](../../.gitbook/assets/image%20%2830%29.png)

**Entity Counts** 

There are 3 entities available: Person, Document and City. Each weight can be consulted by hovering on the pie chart slice corresponding to the target concept.

**Relationship Counts**

There is 1 type available: **Has Entity**. 

In our example there is a central Document that links all our concepts so this is the reason we have **5 Has Entity** relationships**.** 

**Saved Search**

There is one saved search configured returning only the top 5 results for the available data. 

