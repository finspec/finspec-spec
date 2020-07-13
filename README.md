# The FinSpec Specification

## Welcome to the FinSpec Project!

The goal of FinSpec is to define a standard, protocol-agnostic, machine-readable specification to describe and document pre- and post-trade interfaces and workflows in financial services including (but not limited to) order entry, execution, market data, allocations and settlement.

Our hope is that the specification will allow both humans and computers to discover and understand the capabilities of an interface without needing to read through lengthy PDF documentation.  As well as creating interactive API documentation, correctly formatted FinSpec documents can be used by developers to validate and mock APIs, acclerating development and reducing errors.

## Versioning

The current version of the FinSpec specification is 3.0: To read more about the schema definition: [Get Started](https://github.com/finspec/finspec-spec/blob/master/schemas/v3.x/3-0-schema.json).

FinSpec schema versions 3.x are actively supported. Schema versions in the 2.x range are no longer actively developed, but will be supported by FixSpec until the end of 2020. All prior versions are no longer supported.

We would **strongly recommend** upgrading to 3.0 as soon as possible. Please contact happytohelp@fixspec.com for (free) assistance to upgrade schema version.

### Revision History

Version | Date | Notes | Guide
--- | --- | --- | ---
[[3.0](https://github.com/finspec/finspec-spec/blob/master/schemas/v3.x/3-0-schema.json)] | 2020-07-30 | Layout extensions. | [[Link](https://gitlab.com/fixspec/finspec-spec/-/blob/master/documentation/schema_def_3.0.md)]
[[2.1](https://github.com/finspec/finspec-spec/blob/master/schemas/v2.1/schema.json)] | 2019-01-18 | Update to workflow schema. | [[Link](https://gitlab.com/fixspec/finspec-spec/-/blob/master/documentation/schema_def_2.1.md)]
[[2.0](https://github.com/finspec/finspec-spec/blob/master/schemas/v2.0/schema.json)] | 2018-06-27 | Support for navigation and history keys. Extended block (component) support. Retire 0.x versions. | [[Link](https://gitlab.com/fixspec/finspec-spec/-/blob/master/documentation/schema_def_2.0.md)]
[1.2] | 2018-02-14 | Support for machine readable bit fields. | (Retired)
[1.1] | 2017-03-10 | Support for functional (context-specific) messages. | (Retired)
[1.0] | 2017-02-28 | Enhanced protocol metadata, informational messages and change history. | (Retired)
[0.3] | 2016-04-29 | Add support for finite state machine workflow. | (Retired)
[0.2] | 2016-03-16 | Datatypes related enhancements. Added conditions block to field. | (Retired)
[0.1] | 2016-01-06 | Initial release of FinSpec Specification. | (Retired)

## Schema Validation

We *STRONGLY* recommend validating FinSpec schema validation prior to deployment or sharing with counterparties. You can find FinSpec Schema validator [here](https://github.com/finspec/finspec-validator).

## License

Copyright 2020 FixSpec Limited

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at [apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
