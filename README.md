# MemexGATE

<img src="./logo/logo.png" align="right" width="300" />

Introduction... what is it?
------------------------
A server side application and environment for running large scale [General Architecture Text Engineering](https://gate.ac.uk/) tasks over document resources such as online ads, debarment information, federal and district court appeals, press releases, news articles, social media streams, etc. The MemexGATE application is itself run in conjunction with [Behemoth](https://github.com/DigitalPebble/behemoth) to provide an annotation-based implementation of document corpi and a number of modules operating on these documents. The project can be used to simplify the deployment of document analysers on a large scale.

Features
---------
 * ingesting from common data sources ([Warc](http://www.digitalpreservation.gov/formats/fdd/fdd000236.shtml), [Nutch Segments](http://nutch.apache.org), etc...), raw files (PDF, MSWord, Excel...) and Hadoop Sequence Files
 * text processing ([Apache Tika](http://tika.apache.org), [Apache UIMA](http://uima.apache.org), [GATE](https://gate.ac.uk), Language Identification) such as tokenization, sentence splitting, part-of-speech tagging, etc.
 * named entity recognition e.g. identification and explicit featurization of proper names, people. locations, organizations, date/time expressions, measures (percent, money, weight), email addresses, U.S. business addresses, U.S. district attorney names, U.S. Federal and State Judges, U.S. Districts, U.S. Courts, dates, websites, ages, genders, legal lexicon, etc.
 * classification of named entities into predefined categories of interest
 * shallow parsing of entities present within taxonomies or lexicon of terms e.g. legal lexicon
 * generating output for external tools ([Apache Solr](http://lucene.apache.org/solr), [Elasticsearch](https://www.elastic.co/), [Mahout](http://mahout.apache.org))

Use Cases
----------
 * Scrape all court documents from prosecution offices (at Federal and State level) and determine, based on terminology used in the releases, how many cases of a particular nature/type are being brought before the court(s).
 * Scrape all press releases from prosecution offices (at Federal and State level) and determine, based on terminology used in the releases, how many cases of a particular type are being brought forward.
 * Based on domain research and use of domain specific entities, define features (via Natural Language Processing and/or Named Entity Recognition) and make them searchable for researchers and investigators alike
 * Advance the ability to visualize connections between ads, debarment information, court documents, press releases, etc 

This tool heavily leverages the [GATE](http://gate.ac.uk) software. GATE is an acronym for General Architecture for Text Engineering.
Please see below for all of the steps required to use the software. 
The document corpus' I've made available can be used with the MemexGATE application to do interesting things with legal documents such as

 * natural language processing e.g. tokenization, sentence splitting, part-of-speech tagging, etc
 * named entity recognition e.g. identification of proper names, people. locations, organizations, date/time expressions, measures (percent, money, weight), email addresses, business addresses, etc.
 * classification of named entities into predefined categories of interest
 * shallow parsing of entities present within taxonomies or lexicon of terms e.g. legal lexicon

# Dockerfile
MemexGATE is available on Dockerhub for rapid deployment and prototyping of textual document engineering and processing pipelines.
To get the MemexGATE application and environment make sure you have [Docker](https://www.docker.com/) installed then simply
```
$ docker pull lewismc/memex-gate
$ docker run -t -i lewismc/memex-gate /bin/bash

N.B. If you are on MacOSX you may need to run the following two commands first
$ boot2docker start
$ $(boot2docker shellinit)
```
You will not be within your own environment with all of the tools required to run MemexGATE, namely Hadoop 2.2.0, Mahout 0.10.0, Tika 1.9, Gate 8.1, etc.
You can run MemexGATE as follows
```
root@e4e137838adc:/usr/local# memexgate
   _____                                 ________    ___________________________
  /     \   ____   _____   ____ ___  ___/  _____/   /  _  \__    ___/\_   _____/
 /  \ /  \_/ __ \ /     \_/ __ \  \/  /   \  ___  /  /_\  \|    |    |    __)_
/    Y    \  ___/|  Y Y  \  ___/ >    <\    \_\  \/    |    \    |    |  v0.1  \
\____|__  /\___  >__|_|  /\___  >__/\_ \______  /\____|__  /____|   /_______  /
        \/     \/      \/     \/      \/       \/         \/                 \/
Server side framework for large scale General Architecture Text Engineering tasks.
Usage: run COMMAND
where COMMAND is one of:
  ioWarc           load documents from WARC
  ioNutch          load documents from Nutch segment(s)
  ioHadoop         load documents from Hadoop Sequence files
  importer         generate a SequenceFile containing BehemothDocuments given a directory of raw docs
  reader           read and inspect document corpus
  exporter         read and execute intermediate document extraction creating new corpus
  filter           filter documents and create new corpus
  gate             process documents using MemexGATE apps
  tika             parse documents using Tika
  uima             process documents using UIMA
  mahout           generate vectors for clustering with Mahout
  solr             send documents to Solr for indexing
  elastic          send documents to ElasticSearch for indexing
  language-id      identify the language of documents
Most commands print help when invoked w/o parameters.
``` 

#Prerequisites for Manual Installation

 * [Python](https://www.python.org/) - >= 1.7
 * [Apache Maven](http://mavem.apache.org) - preferably the latest
 * [Apache Hadoop](http://hadoop.apache.org) - v1.2.1
 * [Behemoth](https://github.com/DigitalPebble/behemoth) - check out the latest from Github

#Installation
There is VERY little installation required to run MemexGATE over and above provisioning your Hadoop node/cluster and then installing Behemoth as stated in the prerequisites above. 
MemexGATE is a first class citizen within the Behemoth framework meaning that the Behemoth [Processing with GATE instructions](https://github.com/DigitalPebble/behemoth/wiki/tutorial#processing-with-gate) can be followed to the T. 

This follows the following procedure
 * The zipped MemexGATE application must be pushed onto the distributed filesystem by copying the file from your local file system onto the hdfs as follows
```
hadoop fs -copyFromLocal /mylocalpath/legisgate.zip /apps/legisgate.zip
``` 
 * create a file *behemoth-site.xml* file in your Hadoop/conf directory and add the following properties:
```
<property>
  <name>gate.annotationset.input</name>
  <value></value>
  <description>Map the information at the behemoth format onto the select annotationset 
  </description>
</property>
<property>
  <name>gate.annotationset.output</name>
  <value></value>
  <description>AnnotationSet to consider when serializing to the behemoth format
  </description>
</property>
<property>
  <name>gate.annotations.filter</name>
  <value>Token</value>
  <description>Annotations types to consider when serializing to the behemoth format, separated by commas 
  </description>
</property>
<property>
  <name>gate.features.filter</name>
  <value>Token.string</value>
  <description>if specified, only the feature listed for a type will be kept
  </description>
</property>
<property>
  <name>gate.emptyannotationset</name>
  <value>false</value>
  <description>if specified all the annotations in the Behemoth document will be deleted before
 processing with GATE </description>
</property>
```
 
Usage
------
Run MemexGATE on your Behemoth document corpus as follows
```
hadoop jar gate/target/behemoth-gate*job.jar com.digitalpebble.behemoth.gate.GATEDriver 
 "input path" "target output path" /apps/legisgate.zip
e.g. hadoop jar gate/target/behemoth-gate*job.jar com.digitalpebble.behemoth.gate.GATEDriver 
 /data/behemothcorpus /data/behemoth_legisgate_corpus /apps/legsigate.zip
```
If you've followed the Behemoth installation instructions and successfully run legisgate from within Behemoth, you are ready to explore other [Behemoth modules](https://github.com/DigitalPebble/behemoth/wiki/Behemoth-Modules). 
For example, a next step might be to use the [Behemoth Solr Module](https://github.com/DigitalPebble/behemoth/wiki/Solr-module) to persist the data into an indexing engine such as [Apache Solr](http://lucene.apache.org/solr) or maybe Elasticsearch.

Acknowledgements
-----------------
A huge degree of thanks go to Julien Nioche of DigitalPebble Ltd. who developed and maintains the Behemoth software. Thank you Julien for licensing your code under ALv2.0.
This work is funded through the DARPA Memex project.

Contacts
---------
Lewis John McGibbney 0 lewis.j.mcgibbney@jpl.nasa.gov

#License
MemexGATE is licensed permissively under the [Apache Software License v2.0](http://www.apache.org/licenses/LICENSE-2.0)
