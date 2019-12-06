# Criteria2Query+ - Extension of Criteria2Query
## Background
Criteria2Query (C2Q) is a tool that extracts eligibility criteria from ClinicalTrials.gov. It uses CoreNLP from Stanford to extract and identify candidate entities for concept mapping to OMOP CDM Standard Vocabularies (such as LOINC and SNOMED-CT). After extraction, the entities are mapped to concepts using a Lucene-based tool, Usagi. The best candidate concept is then assigned a concept set in OHDSI's ATLAS tool and all possible candidates are provided to the user. The user can then either select the most appropriate concept set, or allow C2Q to automatically generate a JSON formatted query for ATLAS. After selection, the query is submitted to ATLAS so that the user now has a cohort for querying a variety of databases.

## Motivation
Though Usagi has some successes, its downside is a reliance on string-based similarity for scoring mappings. For example, the text "_neurological disease_" can map to "_neurological disorder_" and "_urologic disease_," but "_urologic disease_" has a score of 0.90, due to the limited character variance, so Usagi will provide "_urologic disease_" as its best option for mapping.

## Methodology
As an alternative first step, I propose using MetaMap as it generally performs better than Usagi at mapping concepts. Criteria2Query+ is a tool that could be used as an add-on to C2Q. C2Q+ would take the parsed eligibility criteria and then map those terms to MetaMap using a series of options that are relatively synonymous to those of CoreNLP and OMOP CDM tools. After obtaining the CUI from the UMLS associated with OMOP CDM Standard Vocabularies, C2Q+ then maps the CUIs back to the source vocabularies. C2Q+ then compares the MetaMap and Usagi mappings, as well as their scores, for a given term and takes the better of the two to return to the user for a mapping as the "best option." It will still provide all possible concept sets for manual selection. 

## Evaluation
For my gold-standard, I relied on the 18 clinical trials that were chosen for evaluation by Yuan et al. 2017. After the entities were extracted from the NLP step and marked with their respective domains, I compared these tokens to the corresponding mappings linked in ATLAS, which I classified as correct, partially-correct, incorrect, or N/A for entities that were not mapped. For my analysis, I calculated both loose and strict precision, recall, and F-1 scores under the following definitions:
* **Loose includes partially-correct mappings**
* **Strict excludes partially-correct mappings**
* $Precision = \frac{correct \hspace{1mm} mappings}{total \hspace{1mm} existing \hspace{1mm} mappings}$
* $Recall = \frac{correct \hspace{1mm} mappings}{total \hspace{1mm} mappings}$
* $F_1 \hspace{1mm} Score = 2 * (\frac{precision \hspace{1mm} * \hspace{1mm} recall}{precision \hspace{1mm} + \hspace{1mm} recall})$

## Result files
The results of mappings have been stored in Excel files in the **Evaluation** folder under the following names:
* C2QP_MU - Criteria2Query+ with preference given to MetaMap mappings and supplementing unmapped terms with Usagi
* C2QP_UM - Criteria2Query+ with preference given to Usagi mappings and supplementing unmapped terms with MetaMap
* C2QP_BS - Criteria2Query+ with preference given to mappings with the highest score and supplementing unmapped terms with MetaMap
