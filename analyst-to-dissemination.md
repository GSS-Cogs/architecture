# Disseminating Analysis

> Use Case: _As a data analyst / statistician, I want to disseminate and/or publish the results of my analysis._

As we move to using [Reproducible Analytical
Pipelines](https://gss.civilservice.gov.uk/reproducible-analytical-pipelines/) (RAPs), the typical output from an
analytical session, for instance using R-Studio / R-Markdown, could be a number Markdown documents along with the
analysed/aggregated statistical datasets as [tidy](https://vita.had.co.nz/papers/tidy-data.pdf) CSV files.

These output datasets sit alongside the reports and ideally allow the end user to reproduce any data points and charts
given in the reports.

Note that we distinguish between the input data and artefacts to the RAP and the outputs from the RAP. The inputs will
tend to be micro-level, disclosive data, with high sensitivity. The outputs will tend to be aggregated statistical-level
data and reports that we will assume have gone through some disclosure-control process to ensure that they are properly
tagged with a security label such that they can be disseminated to the target audience.

## Open Web Standards

The Integrated Data Platform uses open, web standards to disseminate and/or publish statistical data on the web.
Fundamentally, data is made available on the web using the Resource Description Format (RDF) as Linked Data using the
following standards:

<dl>
<dt><a href="https://www.w3.org/TR/vocab-dcat-2/">Data Catalog Vocabulary</a> (DCAT)</dt>
<dd><em>A vocabulary designed to facilitate interoperability between data catalogs published on the Web</em>.
Defines catalogues, datasets, their distributions (downloadable files) and metadata like titles, descriptions,
publishers, issued dates, etc.
</dd>
<dt><a href="https://www.w3.org/TR/vocab-data-cube/">Data Cube Vocabulary</a></dt>
<dd>Focussed on the publication of multi-dimensional data on the web, based on the SDMX ISO standard for
statistical data exchange.</dd>
<dt><a href="https://www.w3.org/2004/02/skos/specs">Simple Knowledge Organization System</a> (SKOS)</dt>
<dd></dd>
<dt>CSV on the Web (CSV-W)</dt>
<dd></dd>
</dl>

## Publish My Data

* Upload RDF Data Cubes and SKOS vocabularies into Draftsets
* Use DCAT to encapsulate metadata.
* Can use web browser based admin interface, or programatically via Drafter API.
* Authorization is via Auth0 (now Okta)
* Available roles for editing, QA, publishing and admin.

![drafter](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.github.com/GSS-Cogs/architecture/master/drafter.puml)

## CSV on the Web

* Tidy data: each variable is a column and each observation (or case) is a row
* JSON metadata describes the table, allows validation and transformation to RDF.
* CSV2RDF: standardized transform into RDF Data Cube

![CSVW dataset](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.github.com/GSS-Cogs/architecture/master/dataset-csvw.puml)