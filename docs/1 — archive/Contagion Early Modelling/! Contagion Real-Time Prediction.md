#bf5af2/university 

[[To Do]]
[[1 — archive/Contagion Early Modelling/Questions|Questions]]

# The Summary

> [!summary] Pitch
> In a nutshell, this project looks to investigate the possiblity of real-time, 'on-the-ground' contagious disease modelling. The goal of such a system is to alert individuals that they are latently infected before they become symptomatic and infectious to others.

> [!question] Guiding Questions
> - What if a tool could predict that you were infected by a virus in question before you could spread it, let alone return a positive test for it?
> - What if we could propagate positive-infection information through a network of degrees-of-separation in an automatic, intelligent, and scalable manner?
> - Can we produce the required engineering evidence to allow informed decisions to be made about such a tool?

# The Problem

- Contact tracing is labour-intensive, slow, and cannot accurately predict the *probability of individual exposure*.
- There are microscopic models that have been created to predict the probability of exposure in various environments, however these *can be improved with real-time data collected from individually-owned, inter-communicating devices*.

## DP-3T

> [!info]
>  See [Decentralized Privacy-Preserving Proximity Tracing - Wikipedia](https://en.wikipedia.org/wiki/Decentralized_Privacy-Preserving_Proximity_Tracing).

- Uses 'Bluetooth Low-Energy to track and log encounters with other users' (*clients*)
- Has a physical range $\approx 10\,\text{m}$
- Uses '*Ephemeral IDs* (*EphID*), semi-random rotating strings to identify clients'
- When 'two users encounter each other, they exchange *EphIDs* and store them locally'
- Once a positive test is received, the client will make a report to a central server
- Each client will periodically collect reports from the central server and checks for an infected *EphID*
- If a matched *EphID* is found, the client has come in close contact with an infected person and is therefore warned
- Each device performs these comparisons locally, and contact logs are never transmitted off-device
- Consequently, 'the central server cannot by itself ascertain the identity or contact log of any client in the network'

### Ephemeral IDs

- 16 bytes
- 'Logged locally on the receiving device and is never transmitted to a third party'
- Uses a daily-rotating secret key

### Protocol

- Uses both server and client modes of Bluetooth LE
- In server mode, the device advertises its own *EphID* to be read by a receiver
- In client mode, the device scans for other *EphIDs*
- When two devices meet, 'the receiving client reads the *EphID*, writes its own *EphID* to its own server', and stores the encounter in its contact log, alongside
	- a coarse timestamp, and
	- the signal strength.
 - The signal strength is later used 'to estimate the distance between an infected patient and the user'

> [!fail] The Issue
> DP-3T can *only* alert users that they have come into direct close-contact with someone *once that other person has tested positive, and reported it through the app*. What if we could automatically propagate this information directly through the *degrees-of-separation of contacts*, in an intelligent, automatic way?
> 
> Additionally, DP-3T requires each user to own a Bluetooth Low Energy compatible smart-phone, and the metric of signal strength cannot be an accurate, *independent* predictor of infection.

## Shares Analogy

> [!question] Why not implement this directly in software?
> The same reasons why hardware wallets exist in the cryptocurrency world—hardware solutions offer increased security, privacy, and enable a single-purpose device to be developed. This is advantageous from a Government-controlled distribution perspective, and from a network verification-validation perspective.

## GPAI AI Transmission Models

> [!fail] The Overall Issue
> These AI models currently lack any real-time, individual-level devices to inform each human as to their current chance of infection.
> These models still require some central authority to compute them, and notify the populace through conventional channels.
> We wish to investigate whether it is feasible to do this *on-the-fly*, in an automatic, decentralised manner.

> [!summary] Summary
> The three GPAI-identified initiatives that are most relevant to this research question are:
> 1. BlueDot,
> 2. Compositional Cyber-Physical Epidemiology of COVID-19, and
> 3. COVI.
> 
> However, BlueDot and the Compositional Cyber-Physical Epidemiology of COVID-19 are, although closely related to a common intent of real-time (or near real-time) prediction of disease spread, these two studies/systems are macroscopic models that operate on a population level—and fail to apply at the microscopic, individual-level at which we have identified an issue.
> 
> Conversely, COVI is the singular initiative in which we see most significance—although not an identical, perfect-parity solution to that we envisage, COVI was similarly designed to aid in a more effective contact-tracing system in Canada.
> COVI collected many biometrics and other user data to predict a 'daily COVID-19 risk score' for various regular activities that a user may wish to partake in, such as 'taking public transportation' or 'socialising with friends'.
> COVI similarly employed smartphone technology to 'monitor proximity' and provide better-informed conclusions to individual users.
> COVI would generate 'random "contact" IDs' when two COVI-running phones came within two metres, and used this information to notify any contacts made within the past 14 days with considerations made towards the 'computation of the contacts' risk scores'.
> These risk assessment computations would be performed locally on each user's device, similarly to what we envisage.
> Although 'privacy-conscious', COVI ultimately failed to be endorsed by the Canadian government in favour of an alternative application that 'collected less personal data, citing privacy concerns'.
> 
> As COVI was designed as a mobile application, perhaps we could develop a single-function hardware device that circumvents some of the privacy concerns that ultimately quashed COVI.
> Additionally, perhaps we could focus less on a computation of 'risk scores' and implement/improve upon an on-the-fly infection prediction across degrees-of-separation of contacts, but this must be carefully considered for feasibility and novelty.

### A statistical model to predict the risk of COVID-19 infection from international arrivals to New Zealand

- A 'stochastic branching process model'
- Compares 'how different combinations of border control strategies, home isolation, and testing at varying levels of vaccine coverage affect the risk of an infected traveller causing a community outbreak'
- Incorporates assumptions about:
	- NZ's vaccination programme,
	- The efficacy of vaccines towards preventing infection
	- The transmission dynamics of SARS-CoV-2, and
	- The accuracy of testing and contact tracing statistics.

> [!fail] The Issue
> Only targets the case of an infected international traveller causing a community outbreak, and still relies on the problematic, inefficient, manual contact tracing process.

### AlphaFold

- 'Deep learning model trained to make accurate predictions of the shapes of proteins'
- Model was used to create 'predicted structures of six under-studied proteins associated with SARS-CoV-2'

> [!fail] The Issue
> Is not relevant to contract tracing and predicting the probabilities of latent infection amongst a population.

### ASReview against COVID-19

- 'Versatile machine learning tool designed to support the screening phase of literature reviews and topic meta-analyses'
- 'In response to COVID-19, an extension for ASReview was developed for screening COVID-19 related literature'
 
> [!fail] The Issue
> Is not relevant to contract tracing and predicting the probabilities of latent infection amongst a population.

### Bayesian hierarchical semimechanistic model

- 'Bayesian hierarchical semimechanistic model to determine the effectiveness of non-pharmaceutical interventions on COVID-19 transmission'
- 'The rationale [...] was to provide an alternative to simulation studies, which tend to make strong assumptions that are relatively difficult to validate'
- The alternative is to 'develop a data-driven, cross-country model that compares national interventions to the subsequent numbers of cases or deaths within those respective regions'
- 'The researchers found that NPIs demonstrated highly consistent trends across countries'
- 'Closing both schools and universities was consistently highly effective at reducing COVID-19 transmission, as was banning gatherings of 10 people or fewer'
- 'Targeted closures of face-to-face businesses with a high risk of infection, such as restaurants, bars, and nightclubs, had a small-to-moderate effect'
- 'When most NPIs were already in place, stay-at-home orders had only a small additional effect'
- 'The researchers point out numerous limitations of their approach, such as an inability to factor in country demographics, regional differences in interpretations or implementations of NPIs, and a lack of data on some NPIs not captured in this study'
- Limited scalability of results from the study
- 'A more recent study [...] focussed on Europe's second wave shows that business closures, educational institution closures, and gathering bans had smaller effects on reducing transmission compared with the first wave'

> [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### BlueDot

- 'Software as a service designed to facilitate decision-making processes by deriving insights from a large array of near real-time data'
- 'Aggregates hundreds of datasets and deploys statistical models to detect anomalies, such as potential disease outbreaks, and predict how and where diseases might spread'
- 'BlueDot is a successor to BioDiaspora, a scientific research program studying how the world's population is connected through commercial air travel'
- 'In 2018, BlueDot launched its early warning system, Insights'
- 'Two core components':
	- 'Insights, which sends clients near-real-time infectious disease alerts'; and
	- 'Explorer, a cloud-based GIS platform that integrates diverse datasets, including air travel and disease surveillance data'
- Offered a product called 'COVID Data Suite', which contained 'the latest intelligence about COVID-19, including sub-national data and epidemic curves'
- Also offered another product called '"COVID Focus Reports", which "deliver curated research on COVID-19 that examines where the pandemic is heading"'
- 'Every fifteen minutes, an algorithm pulls structured health data, such as'
	- 'official data from the Center for Disease Control or the World Health Organization, as well as'
	- 'unstructured data, including worldwide movements of commercial flight travellers, human, animal and insect population data, and'
	- 'climate data from sattelites, and'
	- 'local information from journalists and healthcare workers'
 - 'Upon detecting anomalies in data, such as an accumulative occurrence of unexplained symptoms, users are provided an alert of these warning signs via the Insights feature'
 - 'Users are able to track in near real-time disease spread, hospitalization as well as predictions on the interactive dashboard provided by the Explorer feature'
 - 'The early-warning system has proven effective [...], including COVID-19'
 - 'On December 31st, 2019, anomalous reports of undiagnosed pneumonia in Wuhan, China were detected on the Insights feature'
 - 'Two hours after the risk detection, an alert was sent to BlueDot customers'
 - 'Based on air travel data such as from global airline ticketing, the Insights feature identified 20 cities that might be the first to be affected by the outbreak'
 - '12 were later found to be among the first cities that reported early cases'
 - 'The BlueDot system has demonstrated similarly prescient forecasts of earlier viruses, including Ebola in 2014 and Zika in 2016'
 - 'BlueDot offers all of the aforementioned services to licensed clients; website advertising is tailored to those in the business, government, and healthcare sectors'

> [!fail] The Issue
> While closely related to the real-time prediction of disease spread that we wish to solve, BlueDot operated on a macroscopic, population level—we are interested in stemming ongoing infection from person to person.

### C-19 Index

- 'An open-source, AI-based predictive model designed to identify individuals who are at the greatest risk of heightened vulnerability to COVID-19, based on pre-existing medical conditions'

> [!fail] The Issue
> Can only be used to predict the risk of an individual given one's pre-existing medical conditions, and cannot do real-time, on-the-fly infection predictions given their recent contact with other people.

### CAIAC

- 'Collective and Augmented Intelligence Against COVID-19'
- 'Prototype of a knowledge management platform developed to aid policymakers' and health officials' decision-making across various topics related to the COVID-19 pandemic'
- 'The objective [...] to assist users with decision-making by providing them with the means to navigate authoritative and up-to-date information with a combination of information representation and query tools'
- 'Developed with three initial COVID-19-related use cases in mind:'
	-  'Contact tracing',
	- 'Targeting aid towards marginalized groups', and
	- 'Addressing the "infodemic"'

> [!fail] The Issue
> Aids policymakers in making informed decisions, but is primarily a data-collection and labelling prototype—and is only tangential to the execution of contact tracing. Is not relevant to the aims of real-time infection prediction.

### Compositional Cyber-Physical Epidemiology of COVID-19

- 'A framework for simulating the progression of the COVID-19 pandemic based on the enforcement of national non-pharmaceutical interventions'
- 'Allows policymakers to simulate pandemic spread dynamics for different government control measures'
- 'Disease spread dynamics are modelled as a cyber-physical system, composed of parameterised ordinary differential equations, representing the disease spread, and a discrete controller that changes the parameters of the system, representing government interventions'
- 'For example, when a critical threshold of new cases per day or percentage of occupied intensive care units is reached, more stringent policy measures will be implemented, which is encoded in the model by a lower reproduction number'

> [!fail] The Issue
> This is another macroscopic, population-level model that cannot be directly applied to a microscopic infection prediction device.

### CoronaCentral

- 'A dashboard that allows users to navigate a large corpus of coronavirus-related literature for SARS-CoV-2, MERS-CoV, and SARS-CoV'

> [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### CoronaCheck

- 'An online tool for automatically checking statistical facts about the coronavirus based on data in the COVID-19 Data Repository'
- 'Given a claim provided by the user, the system assesses the claim's validity and provides users with an explanation for how their statement was assessed'

> [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### COVI

- 'A research project that culminated in the development of an AI-enabled contact-tracing mobile application'
- 'This application aimed to provide users with personalized daily COVID-19 "risk scores" associated with regular activities (such as taking public transportation and socializing with friends)'
- Uses 'smartphones to monitor proximity and accumulate relevant statistics'
- 'These scores were based on users' demographic and health profiles'
- 'The primary objective of the mobile application was to help members of the general public make informed, risk-reducing decisions in a manner that preserved individual privacy'
- 'COVI extends beyond a contact-tracing mobile app by combining contact-tracing information with other user data (e.g., user demographics, health information, symptoms) to predict daily personal risk factors for each user'
- 'In addition, COVI translates these personal risk scores into recommendations based on public health guidelines'
- 'Finally, collected data is used to define epidemiological models and intervention simulations, which could then be shared with public health officials to help them preempt the resurgence of the virus and inform reopening strategies'
- 'COVI was developed to utilize a variety of data from users, all of which would be obtained by consent'
- 'Upon opening the app for the first time, users would be provided with an overview of how the app works and the privacy implications of sharing data with COVI'
- 'It would ask for consent for the collection, use, and disclosure of IP-based geolocation history, random "contact" IDs (generated when a phone is within 2 meters of another phone with COVI installed), and users' current risk levels'
- 'If a user were to start presenting symptoms or be diagnosed with COVID-19, they could report accordingly'
- 'Then, contacts made with that user within the past 14 days would be notified, and the symptoms/diagnosis would be factored into the computation of the contacts' risk scores'
- 'COVI also asks for consent for the collection and use of data pertaining to a user's age, sex, health conditions, active symptoms, ongoing relevant behaviour, coarse geographical location, and app analytics information; all of these data would be fed into the application's risk assessment function, which would compute locally on the user's device'
- 'Data remains on a user's device unless the user opted to allow COVI Canada to receive encrypted, pseudonymized data packets and heat-map information (in aggregated form), which would be used by COVI's underlying ML model and assist in epidemiological research by government-related or research-related third parties'
- 'Once collected, data can be sued to train deep learning ML models to predict contagiousness risks, and to fit an epidemiological model'
- 'The data shared by the app users [...] contact-tracing and epidemiological simulations, to identify new patterns and specific parameters (such as distance, sex, and age) to model how the virus spreads'
- 'Aiming for COVI to be endorsed for use by the Canadian government'
- 'Despite putting forward a demonstrated effort towards building a "privacy-conscious app" and fostering public trust by explaining the rationale behind their app design decisions in their white paper and in numerous public appearances, the Canadian government decided to endorse a different application that collected less personal data, citing privacy concerns by provincial and territorial leaders'
- 'COVI's code and documentation remain accessible [...] as open-source, with a non-exclusive and royalty-free license, "should [others] wish to deploy an AI-enabled health app inspired by our approach"'
- 'The open-access nature, human-centric privacy protocols, and consensual use of encrypted, pseudonymized user data suggest that this tool has a high potential to scale to other geographies'

> [!warning]
> This is *very* close to what we are envisioning...

> [!fail] The (Potential?) Issue
> This was designed as a mobile application—perhaps a hardware solution can solve *some* of its issues (and perhaps we can improve upon the on-the-fly infection prediction across degrees-of-separation), but ultimately, it was *privacy concerns* that killed it.

### COVID-19 Forecast Hub

- 'A central repository for forecast data on key COVID-19 outcomes, such as cases, deaths, and hospital admissions across the United States'
- 'It also aggregates forecast data from a large community of academic and corporate prediction modelling teams to build short-term ensemble forecasts'
- 'Can be explored on an interactive visualization hosted on its site'

> [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### COVID-19 Hospital Capacity Management

- 'A publicly-accessible online dashboard that allows users to view current occupancy rates of hospitals across the US and recommendations for intra-state patient transfers based on current occupancy rates'
- 'It also provides an interactive tool to modify model parameters (eg. patient type, transfer budget, transfer distance threshold) to obtain more customized recommendations within any state or hospital system within the US'

> [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### COVID-19 Take Control Simulator

- 'An interactive epidemic simulation web application'
- 'An educational tool for public use'
- 'Designed to illustrate how the COVID-19 pandemic could develop under different national guidelines throughout the pandemic'
- 'Developers intended that users would understand that "the power to control COVID-19 is in each of our hands" interacting with the app'
- 'Has two interactive pages:'
	- 'the "R calculator" page, in which users can calculate their reproductive number based on the degree to which they observe physical distancing, maintain personal hygiene, practice contact-tracing, self-isolate, as well as the outside weather'; and
	- 'a "Simulator" page, in which users can compute simulations of reported cases and COVID-19-infected individuals based on R values in different Alert Levels and the duration that restrictions are maintained at every Alert Level'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### COVIDcast

- 'A site that acquires or identifies raw data sources and extracts from them US COVID-19-related signals which are intended to inform decision making by a broad range of users—public health authorities, the healthcare industry, the public and private sectors, epidemiologists, data journalists, and the general public'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### CT Pneumonia Analysis

- 'A research paper that proposes a deep learning-based tool for conducting an automatic, efficient, and detailed evaluation of the severity of COVID-19 in chest CT scans'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### Evolutionary Surrogate-Assisted Prescription (ESP)

- 'A machine learning technology designed to automatically determine the most effective non-pharmaceutical intervention strategies to contain the spread of COVID-19'
- 'ESP uses near real-time data collected on pandemic interventions, their economic impact, and disease transmission to train a long short-term memory neural network capable of predicting how NPIs affect the course of the pandemic without needing to estimate parameters'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### GeoSpark Analytics Hyperion COVID-19 Live Dashboard

- 'A dashboard that uses machine learning to identify, track, and analyze events associated with COVID-19 via mentions on online news articles and social media posts'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### IBM COVID-19 Deep Search

- 'Organizes both structured and unstructured COVID-19 data into a knowledge graph that can be navigated and queried to retrieve information'
- 'IBM's Deep Search access page states that the purpose of the platform is to allow scientists and academics to "unlock the knowledge" of published unstructured and structured data pertaining to COVID-19'
- 'Users can do so by either navigating the knowledge graph manually or building query workflows to extract specific answers from the data'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### icolung

- 'A cloud-based software that uses AI to analyze non-contrast thorax CT scans for COVID-19 pathologies'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### Johns Hopkins US Risk Model

- 'A county-level COVID-19 risk modelling framework intended to assist the US government and individuals in making informed decisions'
- 'The goal of their modelling is to identify at-risk populations and to learn the locations and attributes of those that are most exposed to the risk of infection and death from COVID-19'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### LitCovid

- 'An open-source, curated database equipped with advanced search and filtering features designed to facilitate desktop research on topics related to COVID-19'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### n*f*erX

- 'A cloud-based platform that allows users to query relationships between biomedical concepts, based on associations derived between terms in unstructured biomedical literature and experimental [...] data'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### RADLogics Deep Learning CT Image Analysis

- 'An AI-assisted tool designed to quickly and accurately detect the presence of COVID-19 in thoracic CT scans'
- 'Developed early in the COVID-19 pandemic to respond to the growing need to quickly evaluate large numbers of thoracic CT scans for COVID-19 detection, measurements, and the tracking of disease progression'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### Trove

- 'An open-source framework that allows for a low-cost and privacy-preserving method of training of Named Entity Recognition models for patient records'
- 'NER models enable automated extraction of relevant information and terms out of a text document'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.

### Universal Masking is Urgent in the COVID-19 Pandemic: SEIR and Agent Based Models, Empirical Validation, Policy Recommendations

- 'A preprinted research article and an associated online, interactive, agent-based simulation that models the spread of COVID-19 based on the prevalence of mask-wearing in a population'
- 'The objective of this study was the evaluate the effectiveness of mask-wearing in preventing the spread of COVID-19 with new theoretical models and empirical data-analysis techniques'

 > [!fail] The Issue
> Is not relevant to the real-time computation of microscopic-level probabilities of transmission from person to person.
