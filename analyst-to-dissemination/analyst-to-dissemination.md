# Disseminating Statistical Analysis

> Use Case: _As a data analyst / statistician, I want to disseminate and/or publish the results of my analysis._

## Reproducible Analytical Pipelines

As we move to using [Reproducible Analytical
Pipelines](https://gss.civilservice.gov.uk/reproducible-analytical-pipelines/) (RAPs), we want to ensure that the output
from an analytical session, for instance using R-Studio / R-Markdown, comprises a number Markdown documents along with
the analysed/aggregated statistical datasets as [tidy](https://vita.had.co.nz/papers/tidy-data.pdf) CSV files.

RAP, as envisioned right now, rightly concentrates more on the "analysis as code" part of the process, using modern
software development practices to build automated, repeatable pipelines.

The old process of statistics production in Government:
![old process](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.github.com/GSS-Cogs/architecture/main/analyst-to-dissemination/old-rap.puml)

Using RAP:
![rap](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.github.com/GSS-Cogs/architecture/main/analyst-to-dissemination/rap.puml)

The outputs envisaged from RAP are PDF / HTML documents, presumably along with Spreadsheets, to be uploaded to a
document publishing system like gov.uk [Whitehall Publisher](https://www.gov.uk/guidance/how-to-publish-on-gov-uk/introduction-and-access-to-whitehall-publisher).

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

![drafter](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.github.com/GSS-Cogs/architecture/main/analyst-to-dissemination/drafter.puml)

## CSV on the Web

* Tidy data: each variable is a column and each observation (or case) is a row
* JSON metadata describes the table, allows validation and transformation to RDF.
* CSV2RDF: standardized transform into RDF Data Cube
* Also use for codelists: foreign key in separate tables, transformed to RDF/SKOS.

![CSVW dataset](http://www.plantuml.com/plantuml/proxy?cache=no&src=https://raw.github.com/GSS-Cogs/architecture/main/analyst-to-dissemination/dataset-csvw.puml)

## Linked Data

Publishing and/or disseminating data on the web as linked data simply means using URIs:
* Make the data available as retrievable (HTTP GET) documents at the given URIs.
* Ensure that these documents are parseable as RDF.
* Use URIs in the data, e.g. via CSV-W JSON `aboutUrl`, `valueUrl` and `propertyUrl`. CSV-W JSON is a subset of JSON-LD,
  and the CSV+JSON can be parsed as just another RDF serialization.