---
layout: page
title: Assess API descriptions, schema, and examples
permalink: /guides/api-lint/
menuInclude: no
menuTopTitle: Guides
---

## Introduction

For server-side projects that utilise RAML or OpenAPI (OAS), use the tool `api-lint` to assess the API description files and schema and examples.

The tool is available for use during FOLIO Continuous Integration builds, and also for local use prior to commit.
Currently RAML 1.0 and OAS 3.0 are handled.

For [RAML-using](/start/primer-raml/) projects, this new "api-lint" tool is preferred. The previous tool "[lint-raml](/guides/raml-cop/)" (runLintRamlCop) is still available, but is deprecated. Its behind-the-scenes technology is outdated.

## Procedure

Each discovered API description file is provided to the nodejs script.

That utilises the AML Modeling Framework [AMF](https://github.com/aml-org/amf), specifically the `amf-client-js` library, to parse and validate the definition.

Note: Some modules might find new violations being reported.
Refer to [Interpretation of messages](#interpretation-of-messages) below.

## Usage

For use during FOLIO CI builds, refer to the Jenkinsfile [configuration](#jenkinsfile) below.

For local use, clone the "[folio-tools](https://github.com/folio-org/folio-tools)" repository parallel to clones of back-end project repositories, and use its [api-lint](https://github.com/folio-org/folio-tools/tree/master/api-lint) facilities.
Refer to that document for local installation instructions.

### Python

The Python script will search the configured directories to find relevant API description files, and will then call the node script to process each file.

<a id="properties"></a>Where the main options are:

* `-t,--types` -- The type of API description files to search for.
  Required. Space-separated list.
  One or more of: `RAML OAS`
* `-d,--directories` -- The list of directories to be searched.
  Required. Space-separated list.
* `-e,--excludes` -- List of additional sub-directories and files to be excluded.
  Optional. Space-separated list.
  By default it excludes certain well-known directories (such as `raml-util`).
  Use the option `--loglevel debug` to report what is being excluded.
* `-w,--warnings` -- Cause "warnings" to fail the workflow, in the absence of "violations".
  Optional. By default, if there are no "violations", then the workflow is successful and so any "warnings" would not be displayed.

See help for the full list:

```
python3 ../folio-tools/api-lint/api_lint.py --help
```

Example for RAML:

```
cd $GH_FOLIO/mod-courses
python3 ../folio-tools/api-lint/api_lint.py \
  -t RAML \
  -d ramls
```

Example for OAS:

```
cd $GH_FOLIO/mod-eusage-reports
python3 ../folio-tools/api-lint/api_lint.py \
  -t OAS \
  -d src/main/resources/openapi \
  -e headers
```

Example for both RAML and OpenAPI (OAS), i.e. when preparing for transition:

```
cd $GH_FOLIO/mod-foo
python3 ../folio-tools/api-lint/api_lint.py \
  -t RAML OAS \
  -d ramls src/main/resources/oas
```

### Node

The node script can also be used stand-alone to process a single file.

See usage notes with: `node amf.js --help`

### Jenkinsfile

To use "api-lint" with FOLIO Continuous Integration, add the following configuration to the project's [Jenkinsfile](/guides/jenkinsfile/).

Note that the project should also use "[api-doc](/guides/api-doc/)" which utilises the same configuration for the [properties](#properties) apiTypes, apiDirectories, etc.)

#### Jenkinsfile for RAML

These properties correlate with the described script [options](#python).

```
buildMvn {
...
  doApiLint = true
  apiTypes = 'RAML' // Required. Space-separated list: RAML OAS
  apiDirectories = 'ramls' // Required. Space-separated list
  apiExcludes = 'types.raml' // Optional. Space-separated list
  apiWanings = false // Optional. true|false
```

**Note:** This tool replaces the deprecated "lint-raml" (runLintRamlCop) facility.
Do not use both.

**Note:** For RAML-using projects that are upgrading from the old CI facility, be aware that this new api-lint tool is more thorough. Refer to the ticket linked in the "[Interpretation of messages](#interpretation-of-messages)" section below.

Examples:

* [mod-courses](https://github.com/folio-org/mod-courses/blob/master/Jenkinsfile)
  -- RAML
  * Its [uprade PR](https://github.com/folio-org/mod-courses/pull/122). See each commit which explained each step.

#### Jenkinsfile for OAS

These properties correlate with the described script [options](#python).

```
buildMvn {
...
  doApiLint = true
  apiTypes = 'OAS' // Required. Space-separated list: RAML OAS
  apiDirectories = 'src/main/resources/openapi' // Required. Space-separated list
  apiExcludes = 'headers' // Optional. Space-separated list
  apiWanings = false // Optional. true|false
```

Examples:

* [mod-eusage-reports](https://github.com/folio-org/mod-eusage-reports/blob/master/Jenkinsfile)
  -- OAS
* [mod-search](https://github.com/folio-org/mod-search/blob/master/Jenkinsfile)
  -- OAS

## Reports

At GitHub, detected issues are listed on the front page of each pull-request.
For any branch or pull-request build, follow the "details" link via the coloured checkmark (or orange dot while building) through to Jenkins.
Then see "Artifacts" at the top-right for the processing report.
Or follow across to Jenkins "classic" view, and find the report in the left-hand panel.

## Ensure valid JSON Schema

While api-lint can detect many issues with broken JSON Schema, it is advisable to ensure that the schema are valid locally before pushing changes to CI.
Otherwise the messages from api-lint can be confusing.

There are many schema validation tools. One such is
[z-schema](https://github.com/zaggino/z-schema):

```
for f in schema/*.json; do z-schema --pedanticCheck $f; done
```

Of course it is also advisable to follow on to verify the API descriptions locally with api-lint before pushing to continuous integration.

## Interpretation of messages

When errors are encountered, then a summary of conformance "Violations" and "Warnings" is presented at the top, followed by detail about each.
The detail includes the location of the relevant file and the line number of the problem.

Note that if there are only warnings but no violations, then nothing is presented.
Use the `--warnings` option described above.

Note that this `api-lint` tool is more thorough than our previous CI tool (based on raml-cop and its underlying raml-1-parser).
So projects might find new violations being reported.
(See some migration issues at [FOLIO-3017](https://issues.folio.org/browse/FOLIO-3017).)

