# Ontology QUality Ontology (OQUO)
The Ontology QUality Ontology (OQUO - [https://purl.archive.org/oquo-core](https://purl.archive.org/oquo-core)) is a modular ontology that contains the semantic definitions to represent quality models based on metrics, characteristics and subcharacteristics; in addition to the entities needed to represent ontology evaluations.

OQUO ([https://purl.org/oquo-core](https://purl.archive.org/oquo-core)) contains the common entities needed to represent quality models and evaluations. the module oquo-core is the result of reusing and aligning 4 different ontologies:

  - The Quality Model Ontology - [http://purl.org/net/QualityModel](http://purl.org/net/QualityModel)
  - The Evaluation Result Ontology - [http://purl.org/net/EvaluationResult](http://purl.org/net/EvaluationResult)
  - The Extensible Observation Ontology (OBOE) - [http://ecoinformatics.org/oboe/oboe.1.2/](http://ecoinformatics.org/oboe/oboe.1.2/)
  - The Issue Procedure Ontology (IPO) - [http://purl.org/ipo/core](http://purl.org/ipo/core)


The OQUO model can be used for defining and integrating quality models that combines metrics from different quality evaluation frameworks. We provide an example of how to use [OQUO for that purpose in QASAR, a tool for ontology quality assurance](./oquo-qasar/README.md).

## Tools to implement OQUO

The following tools help to produce OQUO-compliant data and to extend OQUO with new modules.

### Creating OQUO observations

OQUO represents each quality measurement as an observation that links the evaluated entity, the metric, the scale, the measured value and the time of the evaluation. Two tools generate this RDF so that it does not have to be written by hand:

- **[oquo-instance-creator](https://github.com/tecnomod-um/oquo-instance-creator)**: Java library, built on [Apache Jena](https://jena.apache.org/), that generates OQUO instances (observations, measurements, quality values, evaluations and time instants) from the results of quality metrics. Use it to integrate OQUO output directly into Java applications. Compatible with Java 8 and later.
- **[oquo-instance-creator-endpoint](https://github.com/tecnomod-um/oquo-instance-creator-endpoint)**: REST service, built on Spring Boot and on the library above, that exposes the same functionality to any language. Its `POST /toOquoObservation` method receives a JSON list of observations (entity, metric, scale, ranking function, value and timestamp) and returns the corresponding OQUO-compliant RDF. Requires Java 17 or later.

### Adding scale conversions to OQUO modules

- **[oquo-scale-conversion-creator](https://github.com/tecnomod-um/oquo-scale-conversion-creator)**: command-line Java tool, built on the [OWL API](https://github.com/owlcs/owlapi), that adds scale conversions to OQUO-based modules, such as [oquo-huron](https://purl.org/oquo-huron), from a TSV file. Each row maps the raw values of a metric to the five categories of the OQuaRE scale (`oquo:OquareFiveCategoriesScale`). For ratio input scales, each category is given as an interval of raw values, and the tool creates an `oquo:CardinalToOrdinalScaleConversion`. For ordinal input scales, each category is given as an input scale item, and the tool creates an `oquo:OrdinalToOrdinalScaleConversion`. In both cases it also creates the corresponding point mappings. Rows are validated before the ontology is modified, and the `--overwrite-scales` option replaces any existing conversion between the same input and output scales. Optionally, the tool can also create missing metrics and input scales. Requires Java 17 or later.


## Metadata conventions

OQUO declares a set of annotation properties for recording the provenance of any of its elements (ontologies, characteristics, metrics, scales, issues…) and tracing them to their sources: publications, authors, web pages and code. They are reused from standard vocabularies and, being annotations, they do not affect reasoning. Ontologies that extend OQUO use them as they need.

| Metadata | Annotation property | Value |
|---|---|---|
| Resource the element is derived from, such as a publication | `dc:source` | IRI, e.g. a DOI (`https://doi.org/10.xxxx/yyyy`) |
| Author of the element | `dc:creator` | IRI of the author (e.g. their ORCID) or their name |
| Source of the definition of the element | `obo:IAO_0000119` | Text, e.g. a citation |
| Web page of the element | `foaf:homepage` | IRI |
| Source code repository of the element | `schema:codeRepository` | IRI, e.g. a GitHub repository |
| Any other related resource | `rdfs:seeAlso` | IRI |

Prefixes:

```turtle
@prefix dc:     <http://purl.org/dc/elements/1.1/> .
@prefix obo:    <http://purl.obolibrary.org/obo/> .
@prefix foaf:   <http://xmlns.com/foaf/0.1/> .
@prefix schema: <http://schema.org/> .
@prefix rdfs:   <http://www.w3.org/2000/01/rdf-schema#> .
```

Use IRIs as values whenever possible (DOIs, ORCIDs, URLs), so the metadata can be resolved and processed by machines.

The provenance of evaluation results (who or what ran an evaluation, which data it used, what it generated) is described with the PROV-O classes and properties of the core, not with these annotations.

Example:

```turtle
<https://purl.org/oquo-huron> a owl:Ontology ;
    dc:creator <https://orcid.org/0000-0003-0201-3115> ;
    schema:codeRepository <https://github.com/tecnomod-um/huron> ;
    dc:source <https://doi.org/10.1109/ACCESS.2023.3316512> ;
    rdfs:seeAlso <https://semantics.inf.um.es/huron/> .

oquo-huron:SomeMetric
    dc:source <https://doi.org/10.xxxx/zzzz> ;
    obo:IAO_0000119 "Author, A. (2020). Title. Journal." .
```