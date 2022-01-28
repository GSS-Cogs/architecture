# Integrated Data Service - Dissemination

This document provides context, background information and a summary of current progress for the **Dissemination** section of the Integrated Data Programme (IDP). The Integrated Data Programme will produce an Integrated Data Service (IDS). Dissemination is one of the workstreams of the programme. To help differentiate between other workstreams, such as the _analytical platform_, we abbreviate the dissemination specific aspects of the programme and service as IDP-D and IDS-D respectively.

## Background to IDS-D

- [GSS Data Project](https://gss.civilservice.gov.uk/guidance/the-gss-data-project/gss-data-project-final-report/)
- [Connected Open Government Statstics (COGS)](https://gss.civilservice.gov.uk/guidance/the-gss-data-project/)

These previous projects have provided the research and the basis to shape IDP-D into what it is today.

Our progress to date has primarily been in the data space, where our main business goal is to move UK government statisticians from publishing their output as spreadsheets, to publishing using the [CSV on the Web (CSVW)](https://www.w3.org/TR/tabular-data-primer/) open standard.

Over the past few years we have proven that this standard and the various other standards from the W3C for publishing linked statistical data, reference data (classifications and codes), metadata and catalogues fully allow us to capture all the information we want statisticians to publish so that we can move away from spreadsheets and cover the broad goals of allowing end users to find and re-use all this data.

A justification for wanting something other than spreadsheets comes from the understanding that whenever people want to make use of some dataset, there's the often cited 80% time spent wrangling and comprehending the source data, getting it into a shape ready for some further analysis and understanding its caveats. While this figure [may be off the mark](https://blog.ldodds.com/2020/01/31/do-data-scientists-spend-80-of-their-time-cleaning-data-turns-out-no/), it's clear that we can move this effort "upstream" in order to reduce duplication of effort.

In order to prove that CSVW and these other standards fit well, we've had to spend quite a bit of time doing this job ourselves: taking existing published spreadsheets on websites and turning them into "tidy" data as CSV, then adding a JSON file following the spec and further, ensuring that this CSVW result can be turned into our target format, an [RDF Data Cube](https://www.w3.org/TR/vocab-data-cube/).

Our data engineers use Python along with Pandas and a Python package called [`databaker`](https://github.com/GSS-Cogs/databaker) to wrangle spreadsheets into CSV. We've put together a helper library called [`gss-utils`](https://github.com/GSS-Cogs/gss-utils) that the data engineers use to help grab metadata from API endpoints, or from web scraping HTML pages, help discover and download the right spreadsheets given a "landing page" URL. We orchestrate running these notebooks using Jenkins, which then validates the output according to the standard implementation of a CSVW validation tool ([`csvlint`](https://github.com/GSS-Cogs/csvlint.rb)) and then turns the CSVW into an RDF Data Cube using a standard implementation of a CSVW transformation tool ([`csv2rdf`](https://github.com/Swirrl/csv2rdf)). 

These results are uploaded to Swirrl's Publish My Data platform using their APIs and further checked/validated to ensure that they're well formed and are referentially complete.

However, this only proves the standards and it doesn't scale up. So we need to start pushing this work upstream to the statisticians and get them to publish their tidy results directly.

## What does IDS-D do?

We are creating a product which encourages and supports the use of **open standards** and the [architecture of the web](https://www.w3.org/TR/webarch/) to improve the experience of publishers and consumers of government statistics.

Our inspiration is taken from the [data on the web best practices (DWBP)](https://www.w3.org/TR/dwbp/), a set of [35 best practices](https://www.w3.org/TR/dwbp/#bp-summary) advocated by the W3C to address a set of [challenges](https://www.w3.org/TR/dwbp/#challenges) faced when publishing or consuming data on the web. As set out in the DWBP, by publishing data on the web and applying the practices we will achieve the following benefits:

> - **Comprehension**: humans will have a better understanding about the data structure, the data meaning, the metadata and the nature of the dataset.
> - **Processability**: machines will be able to automatically process and manipulate the data within a dataset.
> - **Discoverability**: machines will be able to automatically discover a dataset or data within a dataset.
> - **Reuse**: the chances of dataset reuse by different groups of data consumers will increase.
> - **Trust**: the confidence that consumers have in the dataset will improve.
> - **Linkability**: it will be possible to create links between data resources (datasets and data items).
> - **Access**: humans and machines will be able to access up to date data in a variety of forms.
> - **Interoperability**: it will be easier to reach consensus among data publishers and consumers.

We apply these practices to both statistical **data** (statistical datasets) and **content** (statistical reports).

- improvements to the experience provided by other content management systems (e.g. ONS's [Florence](https://github.com/ONSdigital/florence) or GOV.UK's [Whitehall](https://github.com/alphagov/whitehall)) by giving statisticians the ability to create interactive content - for example, the ONS produces interactive [calculators](https://www.ons.gov.uk/peoplepopulationandcommunity/healthandsocialcare/healthandlifeexpectancies/articles/lifeexpectancycalculator/2019-06-07) which tailor the statistics to the user's specific circumstances.
- neat integration with the [Reproducible Analytical Pipeline (RAP)](https://gss.civilservice.gov.uk/reproducible-analytical-pipelines/) workflow being advocated by the analytical professions, to bring the act of publishing statistics "closer" to where the analyses are performed.
- richer metadata to help consumers gain confidence that they have found the right data to answer their questions

We do not intend to create the _one publishing platform to rule them all_, but rather provide an implementation of the open standards which publishers may (and in most cases, will) adopt to use. However, if publishers of statistics have their own systems meeting their own needs (and many of these systems do exist, e.g. ONS's [Florence](), DfE's [Explore Education Statistics](https://github.com/dfe-analytical-services/explore-education-statistics), Defra's [Data Services Platform](https://environment.data.gov.uk/)), then if these systems also use the open standards our hypothesis is that these statistics would be findable and queryable through a central IDS-D platform. 

### Example

_Note, the unique resource identifier (URIs) which feature throughout this example do not necessarily follow the URI naming scheme we will adopt._

A statistician releases a statistical dataset each year. The collection of annual datasets, a [`dcat:Series`](https://w3c.github.io/dxwg/dcat/#Class:Dataset_Series), is given a given a [uniform resource identifier (URI)](https://www.w3.org/TR/cooluris/) of `http://data.gov.uk/series/name-of-my-statistical-series` _([DWBP #11](https://www.w3.org/TR/dwbp/#VersionIdentifiers): Assign URIs to dataset versions and series)_.

The 2018 edition of a dataset, a [`dcat:Dataset`](https://w3c.github.io/dxwg/dcat/#Class:Dataset), which is part of the series is given the URI `http://data.gov.uk/series/name-of-my-statistical-series/dataset/2018` _([DWBP #9](https://www.w3.org/TR/dwbp/#UniqueIdentifiers): Use persistent URIs as identifiers of datasets)_.

#### Metadata

We provide metadata _([DWBP #2](https://www.w3.org/TR/dwbp/#DescriptiveMetadata): Provide descriptive metadata)_ about the dataset as triples using the [Resource Description Framework (RDF)](https://www.w3.org/TR/rdf11-primer/), including its title, description, and the date it was published. Here is an example of RDF metadata, represented as [Turtle](https://www.w3.org/TR/2014/REC-turtle-20140225/):

```turtle
<http://data.gov.uk/series/name-of-my-statistical-series/dataset/2018> a dcat:Dataset ;
    dcterms:identifier "dataset-2018" ;
    dcterms:title "Dataset, 2018"@en ;
    dcterms:description "This is the first dataset"@en ;
    dcterms:license <https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/> ; # DWBP #4
    dcterms:publisher <https://www.ons.gov.uk/> ;           # DWBP #5
    dcterms:issued "2018-01-01T00:00:00Z"^^xsd:dateTime ;   # DWBP #5
    dcterms:creator <mailto:ross.bowen@ons.gov.uk> ;        # DWBP #5
    dcterms:modified "2018-03-01T00:00:00Z"^^xsd:dateTime ;
    dcterms:accrualPeriodicity <http://purl.org/cld/freq/annual> ; #DWBP #21
    dcterms:spatial <http://statistics.data.gov.uk/id/statistical-geography/K02000001> ;
    dcterms:temporal <http://reference.data.gov.uk/id/gregorian-interval/2008-01-01T00:00:00/P10Y> ;
    dcterms:isReferencedBy <http://data.gov.uk/publication/name-of-my-publication> ;
    dcat:keyword "test"@en, "keyword"@en ;
    dcat:theme <http://data.gov.uk/theme/statistics> ;
    dcat:contactPoint <mailto:ross.bowen@ons.gov.uk> ;
    dcat:landingPage <http://data.gov.uk/series/name-of-my-statistical-series/dataset/2018> ;
    dcat:version "1.1" ;    #DWBP #7
    adms:versionNotes "Dataset was corrected following an error being recognised."@en ;
    dcat:prev <http://data.gov.uk/series/name-of-my-statistical-series/dataset/2017>
    dcat:distribution <http://data.gov.uk/series/name-of-my-statistical-series/dataset/2018/rdf>, 
        <http://data.gov.uk/series/name-of-my-statistical-series/dataset/2018.csv>, 
        <http://data.gov.uk/series/name-of-my-statistical-series/dataset/2018.json> ;
    .
```

[`dcat`](http://www.w3.org/ns/dcat#), [`dcterms`](http://purl.org/dc/terms/) and [`adms`](http://www.w3.org/ns/adms#) are all examples of linked data vocabularies _([DWBP #15](https://www.w3.org/TR/dwbp/#ReuseVocabularies): Reuse vocabularies, preferably standardized ones)_. Our use of these vocabularies allows us to apply other best practices:

- [DWBP #4](https://www.w3.org/TR/dwbp/#DataLicense): Provide data license information.
- [DWBP #5](https://www.w3.org/TR/dwbp/#DataProvenance): Provide data provenance information.
- [DWBP #7](https://www.w3.org/TR/dwbp/#VersioningInfo): Provide a version indicator.


Turtle is a common syntax for RDF, though may feel unfamiliar to those who have not worked with linked data before. Other equivalent serialisations of RDF exist, such as JSON-LD, which may feel more familiar to developers by taking advantage of the common JSON data structure. JSON-LD is used to include structured data in webpages to enable better [search engine optimisation (SEO)](https://developers.google.com/search/docs/advanced/structured-data/intro-structured-data).

```jsonc
{
    "@id": "http://data.gov.uk/series/name-of-my-statistical-series/dataset/2018",
    "@type": "http://www.w3.org/ns/dcat#Dataset",
    "http://purl.org/dc/terms/identifier": "dataset-2018",
    "http://purl.org/dc/terms/title": {
        "@language": "en",
        "@value": "Dataset, 2018"
    },
    "http://purl.org/dc/terms/description": {
        "@language": "en",
        "@value": "This is the first dataset"
    },
    "http://purl.org/dc/terms/license": {
        "@id": "https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/"
    },
    "http://purl.org/dc/terms/publisher": {
        "@id": "https://www.ons.gov.uk/"
    },
    "http://purl.org/dc/terms/issued": {
        "@type": "http://www.w3.org/2001/XMLSchema#dateTime",
        "@value": "2018-01-01T00:00:00+00:00"
    },
    // etc.
}
```

The JSON-LD serialisation may still appear complex to those unfamiliar with RDF due to the appearance of URIs, though this can be simplified through the use of a JSON-LD context - a sort of "header" which maps JSON properties to RDF properties.

```jsonc
{
    // JSON-LD @context:
    "@context": {
        "identifier": "dcterms:identifier",
        "title": {
            "@id": "dcterms:title",
            "@language": "en"
        },
        "description": {
            "@id": "dcterms:description",
            "@language": "en"
        },
        "license": "dcterms:license",
        "publisher": "dcterms:publisher",
        "issued": {
            "@id": "dcterms:issued",
            "@type": "xsd:dateTime"
        },
        // etc.
    },
    // "simplified" JSON data:
    "identifier": "dataset-2018",
    "title": "Dataset, 2018",
    "description": "This is the first dataset",
    "license": "https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/",
    "publisher": "https://www.ons.gov.uk/",
    "issued": "2018-01-01T00:00:00Z",
    // etc.
}
```
#### Observations

The dataset itself contains statistical observations which we transform into RDF and model using the [RDF data cube vocabulary](https://www.w3.org/TR/vocab-data-cube/).

A typical data-frame representation of some statistical data is transformed into an RDF representation, for example this row from a dataset describing life expectancies in Welsh local authorities:

| location  | year | sex | life_expectancy |
|-----------|------|-----|-----------------|
| W06000022 | 2004 | M   | 76.7            |

```turtle
@prefix data: <http://http://data.gov.uk/statistics/life-expectancy-in-wales#obs/> .
@prefix data-dimension: <http://http://data.gov.uk/statistics/life-expectancy-in-wales#dimension/> .
@prefix data-measure: <http://http://data.gov.uk/statistics/life-expectancy-in-wales#measure/> .
@prefix qb: <http://purl.org/linked-data/cube#> .

data:W06000022-2004-M a qb:Observation ;
    data-dimension:location <http://statistics.data.gov.uk/id/statistical-geography/W06000022> ;
    data-dimension:year <http://reference.data.gov.uk/id/year/2004> ;
    data-dimension:sex <http://data.gov.uk/codelist/sex/M> ;
    data-measure:life-expectancy 76.7 ;
    .
```

The CSV's columns and the data items contained within the CSV's cells are given URIs _([DWBP #10](https://www.w3.org/TR/dwbp/#identifiersWithinDatasets): Use persistent URIs as identifiers within datasets)_. The use and reuse of these identifiers allows for different datasets to be linked together and achieves [5* linked data](https://5stardata.info/en/) status.

This introduces some additional burden to data producers as their reference data needs the same care and attention as the statistical data. How do users find existing or coin new URIs for their concepts and then advertise these for reuse by other statistical publishers? The ONS' [Reference Data Management Framework (RDMF)](https://www.ons.gov.uk/aboutus/transparencyandgovernance/datastrategy/ourdatastrategy#strategic-objectives) could potentially play a key role in organising, advertising and encouraging the use of common identifiers across different datasets.

As an example, a codification of the `sex` variable which uses the [census 2021](https://www.ons.gov.uk/census/censustransformationprogramme/questiondevelopment/genderidentity/census2021finalguidanceforthequestionwhatisyoursex) enumeration (and thus only permits answers of `male` or `female`) can be represented in RDF using the [Simple Knowledge Organization System (SKOS)](w3.org/TR/skos-primer/) vocabulary, and related to the identifier representing `sex`.

```turtle
@prefix data-dimension: <http://http://data.gov.uk/statistics/life-expectancy-in-wales#dimension/> .
@prefix qb: <http://purl.org/linked-data/cube#> .
@prefix skos: <http://www.w3.org/2004/02/skos/core#> .

# Relating the sex dimension to its codelist.
data-dimension:sex qb:codeList <http://data.gov.uk/codelist/sex> .

# Define a codelist.
<http://data.gov.uk/codelist/sex> a skos:ConceptScheme. 
    dcterms:title "Sex"@en ;
    dcterms:description ""@en ;
    .

# Create codelist entries for male and female.
<http://data.gov.uk/codelist/sex/M> a skos:Concept ;
    rdfs:label "Male"@en ;
    rdfs:comment "Male biological sex."@en ;
    skos:notation "M" ;
    skos:inScheme <http://data.gov.uk/codelist/sex> ;
    .

<http://data.gov.uk/codelist/sex/F> a skos:Concept ;
    rdfs:label "Female"@en ;
    rdfs:comment "Female biological sex."@en ;
    skos:notation "F" ;
    skos:inScheme <http://data.gov.uk/codelist/sex> ;
    .
```

#### Content negotiation

The dataset has several [`dcat:Distribution`](https://w3c.github.io/dxwg/dcat/#Class:Distribution)s - serialisations of the dataset as different formats such as CSV or JSON. We make these available to a user through standard HTTP content negotiation, with the user able to request specific distributions by adding an `Accept` header to a standard HTTP request _([DWBP #19](https://www.w3.org/TR/dwbp/#Conneg): Use content negotiation for serving data available in multiple formats)_.

For example, a request for a dataset in JSON format may make a request to the generic URI `http://data.gov.uk/series/name-of-my-statistical-series/dataset/2018` with a header `"Accept: application/ld+json"`.
```sh
curl --location http://data.gov.uk/series/name-of-my-statistical-series/dataset/2018 -H "Accept: application/ld+json"
```

The server responds with `303 See Other` to redirect the user to the specific URI for the requested format, e.g. `http://data.gov.uk/series/name-of-my-statistical-series/dataset/2018.json`. The `--location` parameter follows the HTTP redirect. The user then receives a suitable serialisation of the dataset as JSON.

![](https://docs.google.com/drawings/d/16P74YaTOsubrE6BikY_w4Cp3yfor8izp9H-3GK6WBX0/export/svg)
 
For CSV distributions, HTTP responses can also contain a link to the CSVW metadata using the `Link` header and appending `-metadata.json` to the CSV distribution's URI.

```http
HTTP/1.1 200 OK
Content-Type: text/csv
Location: http://data.gov.uk/series/name-of-my-statistical-series/dataset/2018.csv
...
Link: <2018.csv-metadata.json>; rel="describedBy"; type="application/csvm+json"
```

The ability to access multiple serialisations of the data in machine-readable standardised formats applies further best practices:

- [DWBP #12](https://www.w3.org/TR/dwbp/#MachineReadableStandardizedFormat): Use machine-readable standardized data formats.
- [DWBP #14](https://www.w3.org/TR/dwbp/#MultipleFormats): Provide data in multiple formats.
- [DWBP #23](https://www.w3.org/TR/dwbp/#useanAPI): Make data available through an API.

## How does IDS-D do it?

To date, the IDP-D data engineers have been transforming data from already published, highly formatted excel spreadsheets. This has allowed us to prove and test the ideas presented, though a centralised team of data engineers may not be the way we operate in the future (see [Federated data delivery](#federated-data-delivery)).

We start by preparing a [tidy data](https://r4ds.had.co.nz/tidy-data.html) extract of statistical data (one column per variable, one observation per row, one value per cell). This is opposed to the sorts of cross-tabulation or rich table. Up to now, we have been using tools such as [`databaker`](https://github.com/GSS-Cogs/databaker) or [`unpivotr` ](https://github.com/nacnudus/unpivotr) to wrangle tidy data from already published, [highly structured](https://github.com/nacnudus/ukfarm) spreadsheets.

| location  | year | sex | life_expectancy |
|-----------|------|-----|-----------------|
| W06000022 | 2004 | M   | 76.7            |
| W06000022 | 2004 | F   | 80.7            |
| W06000015 | 2004 | M   | 78.7            |
| W06000015 | 2004 | M   | 83.3            |

Alongside this, we prepare [CSV on the Web (CSVW)](https://w3c.github.io/csvw/primer/) metadata. This serves two purposes:
- To provide descriptive and structural metadata - dataset titles and descriptions, column titles and descriptions etc. (_[DWBP #3](https://www.w3.org/TR/dwbp/#StructuralMetadata): Provide structural metadata)_.
- To describe the mapping of [CSV data into RDF](https://w3c.github.io/csvw/csv2rdf/) _([DWBP #10](https://www.w3.org/TR/dwbp/#VersionIdentifiers): Use persistent URIs as identifiers within datasets)_.

A (minimised) example of CSVW metadata looks something like this:

```json
{
   "@id": "http://data.gov.uk/statistics/life-expectancy-in-wales",
   "dcterms:title": "Life Expectancy in Wales",
   "dcterms:description": "Life expectancies of men and women in Wales, from 2004 onward.",
   "dcterms:spatial": "http://statistics.data.gov.uk/id/statistical-geography/W00000094",
   "dcterms:temporal": "http://reference.data.gov.uk/id/gregorian-interval/2004-01-01T00:00:00/P3Y",
   "columns": [
       {
           "name": "location",
           "titles": "location",
           "datatype": "string",
           "propertyUrl": "#dimension/location",
           "valueUrl": "http://statistics.data.gov.uk/id/statistical-geography/{+location}"
       },
       {
           "name": "year",
           "titles": "year",
           "datatype": "integer",
           "propertyUrl": "#dimension/period",
           "valueUrl": "http://reference.data.gov.uk/id/year/{year}"
       },
       {
           "name": "sex",
           "titles": "sex",
           "datatype": "string",
           "propertyUrl": "#dimension/sex",
           "valueUrl": "http://data.gov.uk/sex/{sex}"
       },
       {
           "name": "life_expectancy",
           "titles": "life_expectancy",
           "datatype": "decimal",
           "propertyUrl": "#measure/life-expectancy"
       }
   ],
   "aboutUrl": "#obs-{location}-{year}-{sex}" 
}
```
The algorithm to generate RDF from CSV [forms part of the standard](https://w3c.github.io/csvw/csv2rdf/#generating-rdf), though to summarise, the algorithm iterates over each row and each column of the CSV and populates the [URI templates](https://datatracker.ietf.org/doc/html/rfc6570) provided in the CSVW metadata, where:
- `aboutUrl` generates the subject of the triple,
- `propertyUrl` generates the predicate of the triple,
- `valueUrl` generates the object of the triple.

A row of the CSV once transformed into RDF looks something like this:

```turtle
@prefix data: <http://http://data.gov.uk/statistics/life-expectancy-in-wales#obs/> .
@prefix data-dimension: <http://http://data.gov.uk/statistics/life-expectancy-in-wales#dimension/> .
@prefix data-measure: <http://http://data.gov.uk/statistics/life-expectancy-in-wales#measure/> .
@prefix qb: <http://purl.org/linked-data/cube#> .

data:W06000022-2004-M a qb:Observation ;
    data-dimension:location <http://statistics.data.gov.uk/id/statistical-geography/W06000022> ;
    data-dimension:year <http://reference.data.gov.uk/id/year/2004> ;
    data-dimension:sex <http://data.gov.uk/codelist/sex/M> ;
    data-measure:life-expectancy 76.7 ;
    .
```

We can further describe the `sex` variable with its own title and description, and link it to an appropriate codelist, and this metadata can also be included in the CSVW metadata.

```turtle
@prefix data-dimension: <http://http://data.gov.uk/statistics/life-expectancy-in-wales#dimension/> .
@prefix qb: <http://purl.org/linked-data/cube#> .

data-dimension:sex a qb:Dimension;
    rdfs:label "Sex"@en ;
    rdfs:comment "The state of being male or female."@en ;
    qb:codeList <http://data.gov.uk/codelist/sex> ;
    .
```

We take the CSV file and CSVW metadata and run this through a CI/CD pipeline:
- Use ([`csvlint`](https://github.com/GSS-Cogs/csvlint.rb)) to validate the CSV file against the created metadata (as a form of quality assurance),
- Use ([`csv2rdf`](https://github.com/Swirrl/csv2rdf)) to generate RDF from the CSV file,
- The RDF is added to the PublishMyData (PMD) platform as a draft - a status indicating that the dataset is not public and its audience limited,
- Run further validations on the data once in RDF format. The RDF Data Cube specification gives a list of [integrity constraints](https://www.w3.org/TR/vocab-data-cube/#wf-rules) that we test conformance with.

![](https://docs.google.com/drawings/d/1qlbNOj-JYDlH8_njiPt9oJn9pzHrk4iLi5i9QAA4by4/export/svg)

An example of the metadata and statistical data as RDF can be found [here](data.trig).

### Federated data delivery

> We do not intend to create the _one publishing platform to rule them all_, but rather provide an implementation of the open standards which publishers may (and in most cases, will) adopt to use.

While IDS-D will deliver a service for publishing data and statistical narrative, we recognise that if we insisted all potential participants adopt a specific service as opposed to encouraging the adoption of open standards that we may miss some opportunities:

- Many departments have their own solutions for publishing data and it is possibly an unrealistic expectation that they would immediately abandon their own efforts even if IDS-D is capable of providing a better offering.
- There are data providers outside of of central government (e.g. NHS, local authorities) whose data is still of use to analysts.

Instead, we wish to deliver benefits by encouraging the creation of interoperable data which we can then utilise to provide a better service to data consumers.

IDS-D will deliver tools to aid data producers to produce CSVW metadata. We will explore using [linked data fragments](https://linkeddatafragments.org/) to query across datasets held in different locations across the web, and whether it is feasible for IDS-D to provide information about this to users of the service with the need to ETL the underlying data itself into a monolithic system. This should allow us to present a greater set of data to users than if we limited to approach to only those who publish data to IDS-D directly.

## How does IDS-D view IDS-A?

Many statisticians in government departments currently have their own data platforms which they currently use to produce analyses and their statistical publications. Some departments have a better offering than others - for example, there is some disparity between departments making R and Python available for analysts (see [CARS](https://best-practice-and-impact.github.io/CARS-3/index.html) for a rough idea how different departments are performing) - but we believe many of the departments we are targeting will currently have their own working solution which enables them to create their statistical outputs.

Our working assumption is that IDS-A will provide an _additional_ analysis platform for statisticians in departments, particularly for use where departments require use of data held by other government departments, where previously they would have had to enter into data sharing agreements and performing individual data privacy impact assessments, then performing some technologically awkward data transfer arrangements. 

Some departments may switch entirely to solely using IDS-A for its analyses, but we are assuming that there will be some users who make use of both IDS-A and their own internal platform, or some users who do not use IDS-A at all. Under this assumption, and given the scope of IDS-D to improve the experience of publishing statistics for all statisticians, one of the principles we hold is to keep the experience and interactions with IDS-D the same regardless of whether the analysis is performed using IDS-A or not.

Whether the statistician is using IDS-A or not, our ideal and well tested use case is for the analyst to use their chosen tools to produce CSV and CSVW metadata which can be consumed by IDS-D through some to be determined mechanism.

![](https://docs.google.com/drawings/d/1ON1Fs6I9js3OtuLJr2BE1A8hQAWlx4zjDrxFdCKVRU4/export/svg)
