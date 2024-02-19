# reference_processing
a repo with scripts to finetune an LLM to process bibliographic references of scholarly documents



#### Motivation

- analyzing references of scholarly documents can reveal insights not only on the documents themselves but on the science/research in general

- they are several open bibliometric databases which contains citations links and can be readily used for bibliometric analyis

- However, they typically don't consider other types of documents such as policy documents, popular science books, patents... although those can be informative about the impact of science

- With reference processing, we aim to develop a set of tools which, given a document, can extract its references and link them to an external bibliographic database

#### Existing tools

- there are several other tools which can be used for this task such as 

  - CERMINE (Content ExtRactor and MINEr)
  - ParsCit
  - GrobId https://grobid.readthedocs.io/en/latest/

- We found them difficult to train however. 

- we aim to capitalize on recent advances in large language models to create an easy to train and easy to use sets of tools 

#### Subtasks

this task can be broken into several sub-tasks

1. reference section identification: identify and extract the reference section of a scholarly document

2. reference segmentation: isolate  each reference / delimit it from others

3. reference parsing: chunk each reference in its bibliographic components (authors, title, publication year, journal/book, page numbers etc ...)

4. reference matching : using the bibliographic components find the reference in an external bibliographic database and retrieve its identifier (eg. Crossref doi, Arxiv number etc...)

#### SUBTASK 3: REFERENCE PARSING (Some challenges in reference parsing and our approach) 

- different styles (see Citation Style Language (CSL) which lists over 1500 different styles : https://citationstyles.org/) 
- Efforts required to create good training dataset (especially if one would like to output json with parsed names of authors etc ...
- Halucinations by LLM (even bigger models are not immune to making up bibliographic components if not present ...)
- 

our approach is  to use a mix of synthetic training data and real ones , mixing citation sytles

- we use the GIANT dataset

Grennan, M., Schibel, M., Collins, A., & Beel, J. (2019). GIANT: The 1-Billion Annotated Synthetic Bibliographic-Reference-String Dataset for Deep Citation Parsing. Irish Conference on Artificial Intelligence and Cognitive Science. https://api.semanticscholar.org/CorpusID:210089080

dataset available here: 
https://doi.org/10.7910/DVN/LXQXAO


**TO DO**
- the jupyter notebook contains just a proof of concept
- before pushing the finetuned model to huggingface, we still need to create a better training dataset, which includes also real references (not only synthetic ones) and include also edge cases as well as a better balance of citation styles

   









