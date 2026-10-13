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