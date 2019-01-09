# Data advanced visualization

In the previous section we have seen how one can discover its data, filtering and obtaining programmatic insights.

In this section we will see how the programmatic approach that filters out the target data can be used as the baseline for further data analysis.

Let us click on the **Analyze** tab.

![](../../.gitbook/assets/image%20%2842%29.png)

One thing to remember is that data can be visualized by using a BigConnect tool. Currently there are 2 tools available :

* Graph - entities become vertices and relationships become edges
* Map - based on geolocation information one can visualize their data across a specific region

## Starting off

To the left of the screen, on the sidebar, one can find all its tools stacked together. In the snippet above we had none available. 

So in order to add our initial data to BigConnect let us first select a tool. One can use the **New** button available on the sidebar or simply choose a tool from the center of the screen.

#### Graph

Let us select the Graph tool first. This will open the following:

![](../../.gitbook/assets/image%20%2859%29.png)

There are 3 self explanatory options available in order to populate our graph with some data:

* search already ingested data 
* insert new data
* manually create new entities, relationships etc.

For now let us upload some files and try to visualize the data. Clicking on **Upload** will display the following popup:

![](../../.gitbook/assets/image%20%2858%29.png)

We will click Attach Files and select the relevant files.

![](../../.gitbook/assets/image%20%2853%29.png)

We have chosen 1 file and can now click on **Import**.

![](../../.gitbook/assets/image%20%2831%29.png)

To the right we can now find a new **Graph** tool named **Untitled**. We would need a better name so in order to change it we simply hover it and click on **Edit**. There is one more action available and that is **Delete**.

Currently our graph contains only a document. The contents are related to everyday English conversations as it can be easily observed to the right in the details pane.

If our ontology were to contain the **Person** and **City** concepts then BigConnect could easily infer these in our document and present them on the graph as below:

![](../../.gitbook/assets/image%20%2866%29.png)

Important!

1. We can simply use drag and drop to add additional files to our graph that will be inferred to entities, relationships etc.

    2. When changes are operated on the graph they are by default applied only to the current space and if we want to make them global then we need to publish them. In order to see if there are any pending changes we can look at the left bottom of the screen and check the number next to the **Workspace changes**. If changes exist then something similar to the snippet below will be presented when clicking on the indicated number:

![](../../.gitbook/assets/image%20%2824%29.png)

**Explanation**: An entity's title was deleted but the change was not yet published.

We can now choose to **Undo** it \( revert the change \) or **Publish** it \( propagate it globally \).

There are a lot of functionalities that will be covered in an in depth video tutorial.

#### Map

This tool is extremely useful for data that has a geolocation property. BigConnect knows to infer this information if asked to when ingesting the data or when reprocessing it. Also we can manually add a new property GeoLocation to an entity.

![](../../.gitbook/assets/image%20%2829%29.png)

In the example above we can see Washington was recognized on the map and if we click on the pin then we are shown the details pane as in the graph tool.

There are some additional features available in the map as opposed to the graph like drawing a heatmap, grouping the entities together in a cluster etc. 

These will be all covered in a video tutorial.

