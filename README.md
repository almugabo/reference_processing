# reference_processing
a repo with scripts to finetune an LLM to process bibliographic references of scholarly documents



### Motivation

- analyzing references of scholarly documents can reveal insights not only on the documents themselves but on the science/research in general

- they are several open bibliometric databases which contains citations links and can be readily used for bibliometric analyis

- However, they typically don't consider other types of documents such as policy documents, popular science books, patents... although those can be informative about the impact of science

- With reference processing, we aim to develop a set of tools which, given a document, can extract its references and link them to an external bibliographic database

### Existing tools

- there are several other tools which can be used for this task such as 

  - CERMINE (Content ExtRactor and MINEr)
  - ParsCit
  - GrobId https://grobid.readthedocs.io/en/latest/

- We found them difficult to train however. 

- we aim to capitalize on recent advances in large language models to create an easy to train and easy to use sets of tools 

### Subtasks

this task can be broken into several sub-tasks

1. reference section identification: identify and extract the reference section of a scholarly document

2. reference segmentation: isolate  each reference / delimit it from others

3. **reference parsing**: chunk each reference in its bibliographic components (authors, title, publication year, journal/book, page numbers etc ...)

4. reference matching : using the bibliographic components find the reference in an external bibliographic database and retrieve its identifier (eg. Crossref doi, Arxiv number etc...)


**N.B**

We plan to use LLM for subtask 2 and substask 3 (below only substast 3, still working on a juypter notebook for finetuning a model for substask 2) 

### SUBTASK 3: REFERENCE PARSING (Some challenges in reference parsing and our approach) 



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



----- 


example of what the (minimally) fine-tuned model does: 

For **reference parsing** i.e chunk each reference in its bibliographic components (authors, title, publication year, journal/book, page numbers etc ...) we finetune an LLM to tag the main bibliographic components using html-like tags

**please parse following bibliographic reference into its main components using html-like tags ###reference: {text}**

examples: 

Reference_string 
``
Gaudy J., Willoughby K., Lamm C., Karavanis E., Logue DN. Possible natural MCF-like disease in a domestic lamb in Scotland: TABLE 1:. Veterinary Record 2012;17122:563.1-563. Doi: 10.1136/vr.101137.

Reference_tagged 

`<author>Gaudy J., Willoughby K., Lamm C., Karavanis E., Logue DN.</author> <title>Possible natural MCF-like disease in a domestic lamb in Scotland: TABLE 1:</title>. <container-title>Veterinary Record</container-title> <year>2012</year>;<volume>171</volume><issue>22</issue>:<page>563.1-563</page>. Doi: <DOI>10.1136/vr.101137</DOI>.`

--------

Reference_string 

 Smith, W.. 1954. William Paley’s Theological Utilitarianism in America. The William and Mary Quarterly. 113: 402.

Reference_tagged 

`<author>Smith, W.</author>. <year>1954</year>. <title>William Paley’s Theological Utilitarianism in America</title>. <container-title>The William and Mary Quarterly</container-title>. <volume>11</volume><issue>3</issue>: <page>402</page>.`

--------

Reference_string 

 Jones IG. 1961. The Election of 1868 in Merthyr Tydfil: A Study in the Politics of an Industrial Borough in the Mid-Nineteenth Century. The Journal of Modern History 33: 270–286.

Reference_tagged 

`<author>Jones IG</author>. <year>1961</year>. <title>The Election of 1868 in Merthyr Tydfil: A Study in the Politics of an Industrial Borough in the Mid-Nineteenth Century</title>. <container-title>The Journal of Modern History</container-title> <volume>33</volume>: <page>270–286</page>.`

--------
Reference_string 

Malaspina EF. History of International Law. Introduction to Swiss Law, Carl Grossmann; 2018, p. 85–105.

Reference_tagged 

`<author>Malaspina EF</author>. <title>History of International Law. Introduction to Swiss Law, Carl Grossmann</title>; <year>2018</year>, p. <page>85–105</page>.`


### Processing tagged references 

The returned tagged reference can then be processed with regular expressions to create dictionary

```python

import re

def extract_text_to_json(input_text):
    # Regular expression to find content between tags, including multiline content
    pattern = r'<(\w+)>\s*(.*?)\s*</\1>'
    
    # Find all matches in the input text
    matches = re.findall(pattern, input_text, re.DOTALL)

    # Create a dictionary with tags as keys and extracted text as values
    extracted_data = {tag: text for tag, text in matches}
    return extracted_data

xref_tagged = '''<author>Smith, W.</author>. <year>1954</year>. <title>William Paley’s Theological Utilitarianism in America</title>. <container-title>The William and Mary Quarterly</container-title>. <volume>11</volume><issue>3</issue>: <page>402</page>.'''

extract_text_to_json(xref_tagged)

{'author': 'Smith, W.',
 'year': '1954',
 'title': 'William Paley’s Theological Utilitarianism in America',
 'volume': '11',
 'issue': '3',
 'page': '402'}

``` 




