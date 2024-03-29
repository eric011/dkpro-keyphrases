=====================================================================
                                                                       
                              ======                                   
                              README                                   
                              ======                                   
                                                                       
                             KEA 5.0
                          07 November 2007

		      http://www.nzdl.org/Kea/
                                                                       
           Java Programs for Automatic Keyphrase Extraction
		    with Controlled Vocabularies
                                                                       
          Copyright (C) 2000-2007  Eibe Frank, Olena Medelyan

                   email: {eibe,olena}@cs.waikato.ac.nz            
                                                                       
=====================================================================

Contents:
---------

1. Installation and Testing

2. Getting started

   - Building a keyphrase extraction model
   - Extracting keyphrases
   - Important comment

3. History and Further Information

4. Copyright


----------------------------------------------------------------------

NOTE:
-----

The lib directory contains 

a) the latest version of WEKA, the GPL'ed
   machine learning workbench available from 

   http://www.cs.waikato.ac.nz/ml/weka

b) 5 libraries from the Jena-2.4 package:

   http://jena.sourceforge.net/

Kea has been developed in Java 5.0, on Eclipse under Linux. 
It has been also tested on Windows XP and Mac OS.  


----------------------------------------------------------------------

1. Installation and Testing:
----------------

a) Download the complete package and unzip it. 

b) Set KEAHOME to be the directory which contains this README.
	
	e.g.: export KEAHOME=/home/olena/kea-5.0_full

c) Add $KEAHOME to your CLASSPATH environment variable.

	e.g. export CLASSPATH=$CLASSPATH:$KEAHOME

d) Add $KEAHOME/lib/*.jar to your CLASSPATH environment variable.

	e.g. export CLASSPATH=$CLASSPATH:$KEAHOME/lib/commons-logging.jar
	     export CLASSPATH=$CLASSPATH:$KEAHOME/lib/icu4j_3_4.jar
	     export CLASSPATH=$CLASSPATH:$KEAHOME/lib/iri.jar
	     export CLASSPATH=$CLASSPATH:$KEAHOME/lib/jena.jar
	     export CLASSPATH=$CLASSPATH:$KEAHOME/lib/snowball.jar 
	     export CLASSPATH=$CLASSPATH:$KEAHOME/lib/weka.jar
	     export CLASSPATH=$CLASSPATH:$KEAHOME/lib/xercesImpl.jar
	     export CLASSPATH=$CLASSPATH:$KEAHOME/lib/kea-5.0.jar 


e) Compile and run TestKea

	e.g. javac TestKea
	     java -Xmx526M TestKea

This will take several minutes.
First Kea will create a model based on 25 training documents ($KEAHOME/testdocs/en/train/) and
save them under $KEAHOME/testdocs/en/model.
Second Kea will extract keyphrases for 5 test documents in $KEAHome/testdocs/en/test and save them
under the same names as the text files, but with the extension *.key. Compare them against 
keyphrases assigned by humans in $KEAHOME/testdocs/en/manual_keyphrases.

You can experiment with the TestKea code to create models and extract keyphrases from documents in
other languages contained in the testdocs directory.

Use TestKea as a reference to use Kea-5.0 from your own code.
You can also run Kea from the command line, as described in 2.

You can extract keyphrases with reference to a different vocabulary.
Put it into $KEAHOME/VOCABULARIES/ and insure it is either in SKOS format, 
or in the text format required by KEA. 
See below (2.d) for details. If you don't have a vocabulary
get one from KEA's website: http://www.nzdl.org/Kea/download.html

The JavaDoc documentation (generated from the source code) is located
in the $KEAHOME/doc directory. 

----------------------------------------------------------------------

2. Getting started:
-------------------

Building a keyphrase extraction model
=====================================

To extract keyphrases for new documents, you first need to build a KEA
keyphrase extraction model from a set of documents (preferably from
the same domain) for which you have author-assigned keyphrases. To
this end you have to go through the following steps:

a) Create a directory, called, for example, "training_documents",
   containing the documents that you want to use for training the
   keyphrase extractor.

b) Rename the document files in that directory so that they end with
   the suffix ".txt". (If your documents are PDFs, use pdftotext on Linux 
   to convert them into text format.)

c) Delete the author-assigned keyphrases from those documents
   and put them into separate ".key" files. For example, if
   your document file is called doc1.txt, move the keyphrases
   into a new file called "doc1.key". It is important that
   you put each keyphrase on a separate line in this file!

d) Now you need to provide a controlled vocabulary.
   (For free keyphrase indexing skip this step!)
   A list of controlled vocabularies is available on http://www.nzdl.org/Kea/download.html. 
   You can use any other thesaurus in SKOS format. Their number is constantly increasing,
   latest SKOS vocabularies are listed on http://esw.w3.org/topic/SkosDev/DataZone.
   Make sure the SKOS vocabulary has the extension .rdf.
   You can also use a thesaurus in a plain text format, provided it is stored
   in the same manner as the Agrovoc thesaurus text files: .en, .use and .rel

      - .en is the main index with TermID and Term separated by a blank, one pair per line.
      - .use is the list of non-descriptor with corresponding descriptors
        in a format NonDescriptorID DescriptorID separated by a tab symbol, one pair per line.
      - .rel is the list of semantic relations between terms in a format
        TermID followed by the tab symbol, followed by the IDs of semantically related terms,
	separated by blank symbols.  

c) Build the keyphrase extraction model by running the
   KEAModelBuilder:

   java kea.main.KEAModelBuilder -l <name_of_directory> -m <name_of_model> -v <vocabulary_name> -f <skos|text>

   (e.g. java kea.main.KEAModelBuilder -l testdocs/en/train/ -m model -v agrovoc -f skos)

   If you use "-v agrovoc -f skos", Kea will search for "agrovoc.rdf" in the
   directory VOCABULARIES.

   For free keyphrase indexing, use the option "-v none".

   This will use the documents in <name_of_directory> to build a
   keyphrase extraction model and save it in <name_of_model>.

KEAModelBuilder has a few other options that you can view if you run
KEAModelBuilder without any arguments. Here is a list of all the
options:

-l <directory name>
        Specifies name of directory.
-m <model name>
        Specifies name of model.
-v <vocabulary name>
        Specifies vocabulary name.
-f <vocabulary format>
        Specifies vocabulary format (text or skos or none).
-i <document language>
        Specifies document language (en (default), es, de, fr).
-e <encoding>
        Specifies encoding.
-d
        Turns debugging mode on.
-k
        Use keyphrase frequency statistic.
-p
        Disallow internal periods.
-x <length>
        Sets the maximum phrase length (default: 5).
-y <length>
        Sets the minimum phrase length (default: 1).
-o
        Set the minimum number of occurences (default: 2).
-s <name of stopwords class>
        Sets the list of stopwords to use (default: StopwordsEnglish).
-t <name of stemmer class>
        Set the stemmer to use (default: SremovalStemmer).
-n
        Do not check for proper nouns.

   
The -e option allows you to specify a different character encoding
supported by Java. For example, to extract keyphrases from Chinese
documents encoded using GBK, you would use specify "-e GBK" as an
argument.

The -d option generates some output that shows the progress of the
model builder.

If -k is set, the keyphrase frequency attribute is used in the
model. For more info on this, have a look at the paper on
"Domain-specific keyphrase extraction" listed below. Using this option
improves accuracy if the domain of the documents for which you want to
extract keyphrases is the same as the domain of the training
documents. In other words, if you want to extract keyphrases from
papers on radiology, and your training documents are about radiology,
you should use this option.

If -p is set, KEA does not consider phrases with internal periods as
candidate keyphrases. It is important to use this if a full stop is
not always followed by white space in the documents.

Using -s and -t you can set different classes for stopword detection
and stemming respectively (for languages other than English). 
If you are using an skos vocabulary, use the -i option to specify the language,
as in 


Extracting keyphrases
=====================

To extract keyphrases for some documents, put them into an empty
directory. Then rename them so that they end with the suffix ".txt".

If you've previously built a keyphrase extraction model you can now
apply keyphrases for these documents using:

java KEAKeyphraseExtractor -l <name_of_directory> -m <name_of_model>  -v <vocabulary_name> -f <skos|text>

(See section "Building a keyphrase extraction model" above for information on 
the controlled vocabulary and its format.)

This will create a ".key" file for each document in the
directory. Each file will contain five extracted keyphrases for the
corresponding document.

If a ".key" file is already present it won't be overwritten!  Instead,
the keyphrases present in that file will be used to evaluate the
extraction model. The stemmed extracted phrases are compared to the
stemmed versions of the phrases in the ".key"
file. KEAKeyphraseExtractor reports the number of hits among the total
number of extracted phrases for those documents that have associated
".key" files in the directory.

KEAKeyphraseExtractor has a few options. Here they are:

-l <directory name>
        Specifies name of directory.
-m <model name>
        Specifies name of model.
-v <vocabulary name>
        Specifies vocabulary name.
-f <vocabulary format>
        Specifies vocabulary format.
-e <encoding>
        Specifies encoding.
-i <document language>
        Specifies document language (en (default), es, de, fr).
-n
        Specifies number of phrases to be output (default: 5).
-t <name of stemmer class>
        Set the stemmer to use (default: SremovalStemmer).
-s <name of stopwords class>
        Set the stopwords class to use (default: EnglishStopwords).
-d
        Turns debugging mode on.
-b
        Builds global dictionaries for computing TFIDF from the test collection.
-a
        Also write stemmed phrase and score into ".key" file.


Important comment
-----------------

To get good results, it is important that the input text for KEA is as
"clean" as possible. That means html tags etc. in the input documents
need to be deleted before the model is built and before keyphrases are
extracted from new documents.
Also, make sure that you have enough documents in both training and extraction phase.
For example, for training at least 20-30 manually indexed documents are required. 
It is important that manually assigned keyphrases in the files ".key" correspond to the entries
in the controlled vocabulary that you use.

----------------------------------------------------------------------

3. History and Further Information:
-----------------------

Kea has been developed at the Digital Library Lab at the Waikato University
in New Zealand. All information about Kea algorithm is available from 
the New Zealand Digital Library web site at http://www.nzdl.org/Kea/.

Kea-5.0 combines Kea-4.1 and Kea-3.0, so that one single program with updated
libraries is used for either controlled or free indexing.

Kea-3.0 which was developed by Eibe Frank, Gordon Paynter, Craig Nevill-Manning
and Carl Gutwin. 

Olena Medelyan (http://www.cs.waikato.ac.nz/~olena) re-implemented Kea-3.0 into Kea-4.0 
for controlled keyphrase extraction as a part of her Master Thesis, for 
indexing of agricultural documents with terms from the agricultural thesaurus
Agrovoc (http://www.fao.org/agrovoc/). 

Kea-4.1 is the extended version, which allows the usage of any controlled vocabulary
in the specified text format (see below), or the SKOS format (http://www.w3.org/2004/02/skos/),
a W3C RDF specification for encoding ontologies, thesauri, dictionaries and term lists.

-----------------------------------------------------------------------

4. Copyright:
-------------

KEA is distributed under the GNU public license. 

 *    This program is free software; you can redistribute it and/or modify
 *    it under the terms of the GNU General Public License as published by
 *    the Free Software Foundation; either version 2 of the License, or
 *    (at your option) any later version.
 *
 *    This program is distributed in the hope that it will be useful,
 *    but WITHOUT ANY WARRANTY; without even the implied warranty of
 *    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 *    GNU General Public License for more details.
 *
 *    You should have received a copy of the GNU General Public License
 *    along with this program; if not, write to the Free Software
 *    Foundation, Inc., 675 Mass Ave, Cambridge, MA 02139, USA.

Please read the file COPYING in Kea home directory.

----------------------------------------------------------------------- 
