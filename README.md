# Tagger for Recognizing Privacy-Sensitive Information

The tagger recognizes privacy-sensitive information in German emails and similar text genres using [flair](https://github.com/flairNLP/flair) as a framework. 

We followed flair's [code](https://github.com/flairNLP/flair/blob/master/resources/docs/TUTORIAL_7_TRAINING_A_MODEL.md#training-a-named-entity-recognition-ner-model-with-transformers) for model training and the settings described in [Schweter and Akbik (2021)](https://arxiv.org/abs/2011.06993) (without using document context) to fine-tune a transformer on 3,000 pseudonymized emails of CodE Alltag containing manually and automatically detected privacy-sensitive entities. For more information see our paper.

When using this tagger, take data privacy regulations applying to your use case into account. Use it in a privacy-enhancing manner.

## Requirements

- [flair](https://github.com/flairNLP/flair) (requires python >=3.6): `pip install flair`
  (tested with flair 0.9-0.11)
- [SoMaJo](https://github.com/tsproisl/SoMaJo): `pip install SoMaJo`
  (optional, but recommended)

Accredit these resources appropriately.

## Usage

First, download this [model](https://privacy-tagger.aau.at/model.pt) (2.6 GB) into a directory of your choice.

The tagger runs within the flair framework. 
To detect privacy-sensitive entities in text using the SoMaJo tokenizer run:

```
from flair.models import SequenceTagger
from flair.data import Sentence
from somajo import SoMaJo

# insert the path to the downloaded model here
tagger = SequenceTagger.load('path/to/model.pt')

tokenizer = SoMaJo("de_CMC", split_camel_case=False)

paragraphs = ['Hallo Max!', 'Ich bin zu erreichen unter +43 123 456789.', 'Liebe Grüße, Erika Musterfrau']
sentences = []

for sentence in tokenizer.tokenize_text(paragraphs):
    sentences.append(Sentence([token.text for token in sentence]))
    
tagger.predict(sentences)
```

For entire files use:

```
from flair.models import SequenceTagger
from flair.data import Sentence
from somajo import SoMaJo

# insert the path to the downloaded model here
tagger = SequenceTagger.load('path/to/model.pt')

tokenizer = SoMaJo("de_CMC", split_camel_case=False)
sentences = []

for sentence in tokenizer.tokenize_text_file("example_file.txt", paragraph_separator="empty_lines"):
    sentences.append(Sentence([token.text for token in sentence]))
    
tagger.predict(sentences)
```

## Entities

The following privacy-sensitive entities are supported:

- FEMALE (female given names)
- MALE (male given names)
- FAMILY (family names)
- ORG (organization names)
- USER (user names)
- DATE (dates)
- STREET (street names)
- STREETNO (street number)
- CITY (names of cities, towns, villages or regions)
- ZIP (zip codes)
- PASS (passwords)
- UFID (IDs, IPs, IBANs ...)
- EMAIL (email addresses)
- URL (URLs)
- PHONE (phone and fax numbers)

Note that ORG, CITY, URL and EMAIL currently do not get recognized well due to their replacements in the pseudonymized texts. Recognizing these entity types may be backed up with regular expressions or standard NER models. 
Further, although given names do not reflect the non-binary gender identities individuals may identify with (amplified by a purely binary approach), the tagger was trained on data containing more mentions of male given names and therefore can be biased in this regard.

## Citation

If you use the tagger, please cite:

```
@inproceedings{eder-etal-2022,
    title = {{``}Beste Gr{\"u}{\ss}e, Maria Meyer{''} {---} Pseudonymization of Privacy-Sensitive Information in Emails},
    author = "Eder, Elisabeth  and
      Wiegand, Michael  and
      Krieg-Holz, Ulrike  and
      Hahn, Udo",
    booktitle = "Proceedings of the Thirteenth Language Resources and Evaluation Conference",
    month = jun,
    year = "2022",
    address = "Marseille, France",
    publisher = "European Language Resources Association",
    url = "https://aclanthology.org/2022.lrec-1.79",
    pages = "741--752",
}
```
