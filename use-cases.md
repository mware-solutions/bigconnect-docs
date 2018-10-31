# Use Cases

Utilizarea de modele si algoritmi de tip machine learning pentru optimizarea acivitatilor tactice si de raspuns.

Tool-urile open source sunt cele mai bune pentru rularea de algoritmi ML. Printre cele mai cunoscute sunt:

{% embed url="https://spark.apache.org/graphx/" %}

{% embed url="https://spark.apache.org/mllib/" %}

## Futuristic Service Centres

Decision support systems for coding, classification, requesting experts, tracking and case summary.

#### Description

Using machine learning to accelerate coding, classifying, tracking, requesting experts, and drafting the summary for the investigation case, by providing suggestions and automating low risk scenarios.

#### Functional Requirement

* The AI solution must process an initial observation report, including natural language processing \(NLP\) and text classification for English and Romanian
* The AI solution must have in built decision trees to triage and code cases, and route reports requiring specific specialised input \(identified through machine learning comparison to historic cases\) to relevant department experts for action
* The AI solution must automatically draft a summary of the case, using input from the initial observation report, department expert input and historical comparison
* The system shall allow the lead detective / duty officer to edit and update the case summary before it is published into the system
* The AI solution must support existing processes and systems in police stations, with officers still able to log into the same user interface

## Criminal Investigation

Crime pattern recognition to help with unsolved crimes

#### Description

Analyze historic crime data \(based on existing systems and unstructured summaries and reports\) to determine patterns between unsolved crime cases and/or solved cases that will provide intelligence on potential individuals involved and locations

#### Functional Requirement

* The system must be able to integrate and classify data in order to identify patterns between data sets containing location, personal data \(Gender, Height, Characteristics etc\), objects \(Traces, Fingerprints, Tools etc.\), time, and criminal data \(held in external systems\)
* Machine learning algorithms must be able to link patterns found in the data to relevant information \(e.g. similar charachteristics in solved crimes\) relating to unsolved crimes
* The system must prioritise potential leads, based on an appropriate metric \(e.g. percentage fit to unsolved case\)
* The system should define lists of suspects for unsolved crimes in a visual and easy to understand format
* The AI solution must automatically identify and classify crime features
* The AI solution must analyze the ‘modus operandi’ \(the method used to commit a crime\) of each crime pattern
* The AI solution must produce outputs on a map

## Forensics

Pattern recognition based substance analysis

#### Description

Using machine learning to help identify substances \(e.g chemicals and materials\) for evidence and using historic data to look for patterns where similar chemicals have been used in certain crimes

#### Functional Requirement

* The solution must support automation of processes to assist the lab technicians in analysis of substances through lab machines
* The AI solution must be able to read the output of substance analysis machines and compare the substance to the existing databases
* The AI solution must be able to identify and compare with other cases that have seen a similar substances used
* The AI solution must have the capability to link to other data sources, including external global and medical research substance databases
* The AI solution will start with one type of substance and must be extendable to other substances such as materials paint, DNA and blood, as well as chemicals

## Operations

Real-time modelling to optimize patrol car route and placement for maximum coverage

#### Description

Dynamic network analysis, with input of historic data, traffic information, live patrol car data, road network data and live task estimate to complete. Ability to plan and adjust the patrol cars’ route and placements to maximize coverage and improve response times. Live external incorporated to provide resources with the right information to complete tasks as effective as possible

#### Functional Requirement

* The AI solution must be a neural network analysis that can identify the optimal placement of patrol cars by using historic datasets on time to complete incident or tasks, type of incident or task, patrol car routes
* The AI solution must identify cars on patrol that will be unavailable for long periods of time and configure the routes of other patrol cars in real time to maximise patrol coverage
* The solution must be able to automatically alert dispatchers or patrols about the new routes
* The AI solution must take into account the resources and patrol cars available for dispatch
* The AI solution should connect to appropriate live data sources providing patrol cars proactive situational awareness information relevant to the location of dispatched officers and patrols

## Road Safety

Traffic analysis to support campaigns and target wanted offenders

#### Description

Analyze traffic incident data to identify geographical location with high concentration of driving offenders. This will feed into organizing and running campaigns in theses specific locations and tracking & catching offenders.

#### Functional Requirement

* The AI solution must be able to perform pattern recognition analysis on structured \(from traffic incident datasets\) and unstructured data \(images\)
* The AI solution must clearly show where police resources should be deployed and campaigns targeted based on hotspots
* The AI solution must show through the hotspots the areas with a high concentration of traffic offences and offenders, catergorized to support decision making
* The AI solution must be able in the future to automatically alert officers and trigger automatic dispatch of patrol cars to hotspot areas

## Customers

Customer communication sentiment and emotion analysis across all customer channels

#### Description

Voice and text analysis across all customer channels \(live chat, email, phone calls\) to determine emotions of customers by analyzing behavioral cues such as tone of voice, style of writing, etc.

#### Functional Requirement

* The AI solution must be able to analyse and segment all forms of customer interaction input \(voice – calls, text - live chat, emails and video\) over time using sentiment analysis
* The solution should support visual dashboards showing the analyzed behaviour and emotion of customers
* The solution should also allow for the analysis of employee performance based on their reaction to certain customer behaviour
* The AI solution must collect and centrally store the data generated from the sentiment analysis
* In future the solution must conduct sentiment analysis in real-time and use the data to priorize and triage customer requests

## Predictive Modeling

Predictive modelling to automatically dispatch / use for future pattern recognition

#### Description

Combining crime prediction and hotspots \(including traffic data\) with demographic information \(feeding back the data to enable learning\) to proactively place patrol cars and officers, streamlining dispatch, and providing geographical situational awareness.

#### Functional Requirement

* The AI solution must use machine learning to create a predictive model using variables such as socio- demographic information, weather information and historic crime data to identify patterns of crime and how this can change if variable such as demographics change
* The AI solution must use resource / time attendance data to allocate resources to best cover potential areas of crime \(resource planning\) and feedback how deployment of officers and patrols to those areas impacted crime
* The AI solution must link to real time area and event information
* The predictive model must continually update as the circumstances and variables in the area being modelled change

## Speech-to-Text and Natural Language Processing

Using speech-to-text and NLP to complete structured basic forms where currently there is time consuming system entry or note-taking on scene e.g. experts attendng crime scene

#### Functional Requirement

* The AI solution must facilitate the narration of reports into a computer based structured form 
* The AI solution must use NLP \(models and algorithms\) to convert language requirements into language a computer can understand





