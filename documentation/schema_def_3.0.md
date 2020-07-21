# Schema Definition v3.0

## Change log

Several important innovations have been introduced to this version. In no particular order, these include:

*  A new, optional `layout` section to capture presentation ordering of elements: [Layout](#layoutArray)
*  Ability to flexibly embed field tables and examples within message presentations using custom HTML tags
*  Changes to examples array, making them global and differentiating raw versus illustrative examples. 
*  Ability to control the positioning of fields and examples within HTML descriptive sections.


## Format

The files describing the APIs per the FinSpec specification are represented as JSON objects and conform to the JSON standards.

All identifiers in the specification are **case sensitive**.

The schema exposes two types of fields. Fixed fields, which have a declared name, and Patterned fields, which declare a regex pattern for the field name. Patterned fields can have multiple occurrences as long as each has a unique name.

### Note On Descriptive Text [New in 3.0]

The FinSpec project aims to accommodate the need for both machine-readable and human-readable API documentation. This requires a careful balance between highly-structured, machine-readable elements, and more loosely-structured descriptive elements designed for humans (typically HTML descriptions).

In particular, machines will require fast, look-up access to structured elements such as field arrays, whereas human authors like to surround field lists with explanatory, descriptive text.

To accommodate this, FinSpec 3.0 defines additional custom HTML tags which to be carried in selected HTML description fields, allowing fine-grained control over how field lists and examples are positioned within other descriptive elements.

Custom HTML Tag   | Description | Available In
-------------|----------------------|------------
`<finspec-fields><finspec-fields>` | An instruction to insert a table representing the list of fields (described in the same Fieldset) as a block-level HTML div at that point. | [Fieldset](#fieldsetObject), [FieldsetWithContext](#fieldsetWithContextObject)
`<finspec-example data-ref="example_key"><finspec-example>` | An instruction to insert the (globally-defined) example object with the indicated key as a block-level HTML div at that point. | [Datatype](#datatypeObject), [InfoSection](#infoSectionObject), [Fieldset](#fieldsetObject), [FieldsetWithContext](#fieldsetWithContextObject)

The use of these custom HTML tags is optional. HTML description sections which do not contain these elements should be intepreted as an instruction to insert and relevant field table and examples directly after the relevant HTML description. 


## File Structure

The FinSpec representation of the API is made of a single file.

By convention, the API file has `.json` extension.

## <a name="vendorExtensions">Vendor Extensions</a>

While the FinSpec Specification tries to accommodate most use cases, additional data can be added to extend the specification at certain points.

The extensions properties are always prefixed by `"x-"` and can have any valid JSON format value.

<table>
  <thead>
    <tr>
        <th style="width:20%">Field Pattern</th>
        <th style="width:20%">Type</th>
        <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a name="finspecExtensions"></a>^x-</td>
      <td>Any</td>
      <td>Allows extensions to the FinSpec Schema. The field name MUST begin with `x-`, for example, `x-internal-id`. The value can be `null`, a primitive, an array or an object.</td>
    </tr>
  </tbody>
</table>

---

## Objects

### Root document

This is the root document object for the API specification.

#### Fixed Fields


Field Name   | Type                                       | Description
-------------|:------------------------------------------:|-----------------------
finspec      |`string`                                    | **Required**<br/>Specifies the FinSpec Specification version being used. The value MUST be "3.0".
info         | [Info](#infoObject)                 | **Required**<br/>Provides metadata about the specification. The metadata can be used by the clients if needed.
protocol     | [Protocol](#protocolObject)         |**Required**<br/>Metadata describing the protocol.
changes      | [Changes](#changesObject)           | **Optional**<br/>A description of the changes made in this version of the API specification.
datatypes    | [Datatype](#datatypeObject)         | **Required**<br/>A list of datatypes used in the API specification.
nav     | [Nav](#navObject)         | **Required**<br/>A proposed UI navigation structure for the API specification.
layout     | [Layout](#layoutArray)         | **Optional**<br/>[Added in 3.0] A layout object to control the logical presentation of messages in a printed or displayed document.
blocks | [Blocks](#blocksObject) | **Required if supported by protocol**<br/>A list of common blocks used by the messages in the API specification. e.g. Header, footer, etc.
messages     | [Messages](#messagesObject)         | **Required**<br/>A list of messages used in the API specification.
examples     | [Examples](#examplesObject)         | **Optional**<br/>A keyed list of message examples.
notes     | [Notes](#notesArray)         | **Optional**<br/>An list of notes about the specification as a whole.
workflows    | [Workflows](#workflowsObject)           | **Optional**<br/>A list of finite state machine style workflow (optional)

The Root object may contain [vendor extensions](#vendorExtensions).


### <a name="infoObject"></a>Info

```json
{
    "version": "11.5",
    "issueDate": "2015-08-26",
    "liveDate": "2015-08-26",
    "issuer": "FixSpec Ltd.",
    "title": "FixSpec Example FIX Specification",
    "status": "FINAL",
    "logo": "https://fixspec.com/images/fixspec.jpg",
    "contacts": [...]
}
```

The object provides metadata about the API specification.

Field Name | Type | Description
---|:---:|---
<a name="infoVersion"></a>version | `string` | **Required**<br/>A semantic version number of the API.
<a name="infoIssuer"></a>issuer | `string` | **Required**<br/>The name of the API issuer.
<a name="infoIssueDate"></a>issueDate | `string` | **Required**<br/>The issue date of the API description expressed in YYYY-MM-DD format.
<a name="infoLiveDate"></a>liveDate | `string` | **Optional**<br/>The anticipated production-live date of the API description expressed in YYYY-MM-DD format.
<a name="infoTitle"></a>title | `string` | **Required**<br/>The title of the API service.
<a name="infoLogo"></a>logo | `string` | **Optional**<br/>A fully-qualified URL to a suitable logo image for the spec author.
<a name="infoContacts"></a>contacts | [Contacts](#contactsObject) | **Optional**<br/>A List of contact details for the owners of the API.
<a name="infoStatus"></a>status | `string` | **Optional**<br/>A description of the current state of this specification (eg DRAFT or FINAL).


The Info object may contain [vendor extensions](#vendorExtensions).


### <a name="contactsObject"></a>Contacts

```json
{
    "contacts": [
        {
            "name": "Customer Services",
            "email": "happytohelp@fixspec.com",
            "url": "https://fixspec.com"
        },
        ...
    ]
}
```

An array of contact information for the specification. Each specification must contain at least one piece of contact information.

Field Name | Type | Description
---|:---:|---
<a name="contactName"></a>name | `string` | **Required**<br/>The identifying name of the contact person/organization.
<a name="contactPhone"></a>phone | `string` | **See note below**<br/>The phone number (including international dialling code) for the contact.
<a name="contactUrl"></a>url | `string` | **See note below**<br/>The URL pointing to the contact information. MUST be in the format of a URL.
<a name="contactEmail"></a>email | `string` | **See note below**<br/>The email address of the contact person/organization. MUST be in the format of an email address.

**NOTE:** At least one of `phone`, `url` or `email` must be provided.

The Contact object may contain [vendor extensions](#vendorExtensions).


### <a name="protocolObject"></a>Protocol

```json
{
  "protocol": {
      "name" : "FIX",
      "description": "FIX Protocol",
      "isFIX": true,
      "isOffsetBased": false,
      "isTagValue": true,
      "isBinary": false,
      "isObject": false,
      "charset": "UTF8",
      "hasHeader": true,
      "hasFooter": true
  }
}
```

The object provides metadata about the protocol.

Field Name | Type | Description
---|:---:|---
<a name="protocolName"></a>name | `string` | **Required**<br/>The common name for the protocol.
<a name="protocolDesc"></a>description | `string` | **Optional**<br/>An optional description of the protocol.
<a name="protocolIsFix"></a>isFIX | `boolean` | **See note below**<br/>Is this the FIX protocol? (Useful short-cut for one of the most popular protocols).
<a name="protocolIsOffsetBased"></a>isOffsetBased | `boolean` | **See note below**<br/>Is this a offset-based message such as ITCH?
<a name="protocolIsTagValue"></a>isTagValue | `boolean` | **See note below**<br/>Is the protocol tag-value based, such as FIX or SWIFT?
<a name="protocolIsObject"></a>isObject|`boolean`| **See note below**<br/>Does the specification describe the set of object attributes returned from an SDK?
<a name="protocolIsTagValue"></a>isBinary|`boolean`| **Optional (default to `FALSE`)**<br/>Indicates whether protocol is text or binary.
<a name="protocolEndianness"></a>endianness | `string` | **Required for binary protocols only**<br/>Default endianness of numerical types for binary/native APIs. Value MUST be from the list: `"little"` and `"big"`.
<a name="protocolCharset"></a>charset | `string`| **Optional**<br/>Legal character set used for string values e.g. 'UTF8', 'ISO 8859-1', etc.
<a name="protocolHasHeader"></a>hasHeader | `boolean`| **Optional**<br/>Do messages in this protocol always have a defined header block?
<a name="protocolHasFooter"></a>hasFooter | `boolean`| **Optional**<br/>Do messages in this protocol always have a defined footer block?

**NOTE:** At least one of the following protocol attributes must be present: `isFIX`, `isOffsetBased`, `isTagValue` and `isObject`.

The Protocol object may contain [vendor extensions](#vendorExtensions).


### <a name="changesObject"></a>Changes

```json
{
  "changes": {
      "summary": "<p>These are my changes since the last known version</p>",
      "lastVersion": "11.2",
      "lastVersionDate": "2001-01-01",
      "history": [
          {
              "version": "11.1",
              "issueDate": "2016-10-08",
              "liveDate": "2016-10-08",
              "summary": "This is my summary of changes in the 11.1 version."
          },
          ...
      ]
  }
}
```

An optional block describing changes in this, and prior, spec versions.

Field Name | Type | Description
---|:---:|---
<a name="changesSummary"></a>summary | `string` | **Required**<br/>A HTML or text description of the changes made.
<a name="changesLastVersion"></a>lastVersion | `string` | **See note below**<br/>The version number of the immediately prior spec version (i.e. the starting spec to which changes have been applied).
<a name="changesLastVersionDate"></a>lastVersionDate | `string` | **See note below**<br/>The date of the immediately prior spec version (i.e. the starting spec to which changes have been applied) in YYYY-MM-DD format.
<a name="changesHistory"></a>history | [History](#historyObject) | **Optional**<br/>A historical list of prior changes.

**NOTE:** At least one of `lastVersion` and `lastVersionDate` must be present.

The Changes object may contain [vendor extensions](#vendorExtensions).



### <a name="historyObject"></a>History

```json
{
  "version": "11.1",
  "issueDate": "2016-10-08",
  "liveDate": "2016-10-08",
  "summary": "This is my summary of changes in the 11.1 version."
}
```

An optional block describing changes in this, and prior, spec versions.

Field Name | Type | Description
---|:---:|---
<a name="historyVersion"></a>version | `string` | **Required**<br/> The spec version number to which this history record relates.
<a name="historyIssueDate"></a>issueDate | `string` | **Required**<br/> The issue date of the spec to which this history record relates, in YYYY-MM-DD format.
<a name="historyLiveDate"></a>liveDate | `string` | **Optional**<br/>The live date of the spec version to which this history record relates, in YYYY-MM-DD format.
<a name="historySummary"></a>summary | `string` | **Required**<br/>A HTML or text description of the changes applied in the indicated spec version.

The History object may contain [vendor extensions](#vendorExtensions).



### <a name="datatypeObject"></a>Datatype

```json
{
    "datatypes:": [
        {
            "name": "Price",
            "baseType": "integer",
            "length": 8,
            "precision": 8,
            "description": "Eight byte integer field with eight implied decimal places."
        },
        {
            "name": "Qty",
            "baseType": "number",
            "description": "Float field capable of storing either a whole number (no decimal places) of 'shares' (securities denominated in whole units) or a decimal value containing decimal places for non-share quantity asset classes (securities denominated in fractional units)."
        },
        {
            "name": "MultipleStringValue",
            "baseType": "string",
            "description": "String field containing one or more space delimited multiple character values (e.g. |277=AV AN A| )."
        },
        {
            "name": "UInt32E",
            "baseType": "integer",
            "length": 4,
            "isUnsigned": true,
            "description": "Little-Endian encoded 32 bit unsigned integer."
        },
        ...
    ]
}
```

Describes the type of data stored in the field value of the API.

Field Name | Type | Description
---|:---:|---
<a name="name"></a>name | `string` | **Required**<br/>Name of the type. e.g. int, uint8e, Price, Boolean, etc.
<a name="baseType"></a>baseType | `string` | **Required**<br/>Specifies the base type of the value. Value MUST be from the list: `"char"`, `"integer"`, `"number"`, `"string"`, `"ascii"`, `"uint8"`, `"int8"`, `"uint16"`, `"int16"`, `"uint32"`, `"int32"`, `"uint64"`, `"int64"`, `"float"`, `"double"`                    
<a name="description"></a>description | `string` | **Required**<br/>Description/remark for this type.
<a name="length"></a>length | `integer` | **Optional**<br/>Length of value for this type in number of bytes (binary) or characters (text).
<a name="precision"></a>precision | `integer` | **Optional**<br/>Implied number of decimal places for floating numbers represented as integers.
<a name="isUnsigned"></a>isUnsigned | `boolean` | **Optional**<br/>Whether numerical value is unsigned or not. Default: false
<a name="isBitField"></a>isBitField | `boolean` | **Optional**<br/>Whether type represents a bit field. Default: false
<a name="pattern"></a>pattern | `string` | **Optional**<br/>Regular expression to describe the pattern of the value for this datatype.
<a name="padChar"></a>padChar | `string` | **Optional**<br/>Character used for padding.
<a name="padSide"></a>padSide | `string` | **Optional**<br/>Whether field value should be padded to left or right. Value MUST be from the list: `"left"` and `"right"`.
<a name="datatypeExamples"></a>examples | `array` | **Optional**<br/>Array of **keys** referring to entries in the global [examplesObject](#examplesObject) which describe this datatype.


The Datatype object may contain [vendor extensions](#vendorExtensions).

### <a name="navObject"></a>Navigation

```json
{
    "nav": {
        "info": {
            "General": {
                "items": [
                    {
                        "key": "info_item_unique_key",
                        "name": "Information section header"
                    },
                    ...
                ]
            },
            ...
        },
        "technical": {
            "Session": {
                "items": [...]
            },
            ...
            "Order Entry": {
                "items": [...]
            },
            ...
        },
        "block": {
            "Core": {
                "items": [...]
            },
            ...
        }
    }
}
```

The navigation object allows the capture of intended UI navigation for a specification; how the author intended messages and information sections to be logically grouped for display. Think of it as the electronic equivalent of a Table Of Contents.

The top-level `nav` object may have up to four sections (but a minimum of one), relating to various content types: `info`, `blocks`, `technical` and `functional`.

Each of these sections may have as many sub-menus as required, with each sub-menu represented as a keyed JSON fragment. In the example here, "Session" and "Order Entry" are considered sub-menus of the `technical` type.

At the end nodes of this hierarchy should be a NavItemsArray (`items`) array, which is a simple array containing references to the relevant information section, block or fieldset. The definition of the end-node NavItems contains the unique `key` for the detail to be displayed, the same unique key used for keying the section or fieldset in the relevant JSON section allowing the navigation and detail to be linked together.

Field Name | Type | Description
---|:---:|---
<a name="navName"></a>name | `string` | **Required**<br/>Name of the section or fieldset.
<a name="key"></a>key | `string` | **Required**<br/>Unique key linking for the information section or fieldset. Links to the key found in the relevant blocks or messages object.

Both the (top-level) Navigation object and the (lowest-level) NavItemsArray objects may contain [vendor extensions](#vendorExtensions).


### <a name="layoutArray"></a>Layout [New in 3.0]

```json
{
    "layout": [
        {
            "prepend": "<h1>Section Header</h1><p>This is some introductory text for this section that should appear before all pages.</p>",
            "append": "<p>This text will appear after all pages.</p>",
            "orientation": "landscape",
            "pages": [
                {
                    "prepend": "<h1>Page Header</h1><p>This text will appear before all messages.</p>",
                    "append": "<p>This text will appear after all messages.</p>",
                    "messages": [
                        {
                            "type": "info",
                            "key": "info_item_unique_key"
                        },
                        {
                            "type": "technical",
                            "key": "technical_message_key"
                        },
                        ...
                    ]
                },
                ...                
            ]
        },
        ...
    ]
}
```

The layout object is designed to capture the intended layout of messages when presented either in print format (eg PDF) or as single-page documentation online.

The top-level `layout` array contains one or more "section" objects, each representing a distinct segment of a document. And each "section" object can indicate a series of pages which - in turn - links to one or more messages described elsewhere in the FinSpec. Much the same as in Word, section objects can indicate an orientation of either portrait or landscape to control the presentation. Pages can point to one or more messages within should be understood to have an implicit page break when output to Word or PDF. 

At both the section and page level, it is possible to include `prepend` and `append` HTML sections to add additional information that is perhaps not directly relevant for messages.  

####Section

Field Name | Type | Description
---|:---:|---
<a name="prepend"></a>prepend | `string` | **Optional**<br/>HTML content to be output **before** any pages in this section.
<a name="append"></a>append | `string` | **Optional**<br/>HTML content to be output **after** all pages in this section.
<a name="orientation"></a>orientation | `string` | **Optional**<br/>The page orientation used for Word/PDF. Valid values as `portrait` and `landscape`. Default is `portrait`.
<a name="pages"></a>pages | [PageArray](#pageArray) | **Optional**<br/>An array of pages contained in this section. Each page ends with an implicit page break.

#### <a name="pageArray"></a>PageArray

Field Name | Type | Description
---|:---:|---
<a name="prepend"></a>prepend | `string` | **Optional**<br/>HTML content to be output **before** any messages on this page.
<a name="append"></a>append | `string` | **Optional**<br/>HTML content to be output **after** all messages on this page.
<a name="messages"></a>messages | [PageMessageArray](#pageMessageArray) | **Optional**<br/>An array of references to messages which should appear on this page.

#### <a name="pageMessageArray"></a>PageMessageArray

Field Name | Type | Description
---|:---:|---
<a name="type"></a>type | `string` | **Required**<br/>The type of message that this refers to. Valid values are `info`, `block`, `technical` and `functional`.
<a name="key"></a>key | `string` | **Required**<br/>The unique key reference for the message.


The `layout`, `section` and `pageArray` objects may contain [vendor extensions](#vendorExtensions), but entries in the `pageMessageArray` should not contain extensions.


### <a name="blocksObject"></a>Blocks


```json
{
    "blocks": {
        "block_1": {
            "name": "Standard FIX Header",
            "description": "The standard FIX message header",
            "historyKey": "block_1_hist",
            "isHeader": true,
            "isTrailer": false,
            "fields": [...]
        },
        "block_2": {
            "name": "Standard FIX Trailer",
            "description": "The standard FIX message trailer",
            "historyKey": "block_2_hist",
            "isHeader": false,
            "isTrailer": true,
            "fields": [...]
        },
        "block_3": {
            "name": "Instrument",
            "description": "Re-used instrument identifier block",
            "historyKey": "block_3_hist",
            "isHeader": false,
            "isTrailer": false,
            "fields": [...]
        },
        ...
    }
}
```

A list of re-useable blocks (or components) used in the messages in the API specification. From FinSpec Schema v2.0, this is a keyed JSON fragment, with each block's key being a unique reference to that block for reference in other fieldsets. While we recommend using random keys here, your implementation may allow other keys such as slugged names. Each block (eg `block_1` in the example here) represents a [FieldSet](#fieldSetObject).

**IMPORTANT** It is NOT assumed that these JSON keys are maintained across specification versions; for example, if you choose to set the key equal to a slugged name then this key will "break" should you ever change the block name. For this reason, the `historyKey` attribute is used to maintain a consistent identifier across specification versions. It is `historyKey` and not JSON key that should, therefore, be used in comparison logic.

The Blocks object may **not** contain vendor extensions.


### <a name="messagesObject"></a>Messages

```json
{
    "messages": {
        "info": {
            "info_1" : {
                "name": "Symbology",
                "description": "This is some text description of symbology demanded by the recipient.",
                "historyKey": "info_1_hist"
            },
            ...
        },
        "technical": {
            "technical_1": {
                "name": "Logon",
                "wireId": "A",
                "description": "The Logon message authenticates a user establishing a connection to a remote system.",
                "fields": [...],
                "isSession": true,
                "direction": "both",
                "historyKey": "technical_1_hist"
            },
            ...
        },
        "functional": {
            "functional_1": {
                "name": "VWAP Order",
                "wireId": "D",
                "description": "The new order message type targeting the VWAP algorithmic strategy.",
                "fields": [...],
                "historyKey": "functional_1_hist",
                "baseKey": "technical_3",
                "context": {...},
                "examples": [...]
            },
            ...
        }
    }
}
```
A list of all messages used in the API specification.

The section can be used to carry up to three types of message - `info` sections, `technical` messages, and `functional` views, where a functional view is a technical message within a specific business context.

Field Name | Type | Description
---|:---:|---
<a name="messagesInfo"></a>info | [InfoSection](#infoSectionObject) | **See note below**<br/>Series of free-text, descriptive sections which do not directly relate to messaging e.g. Symbology, Market Phases etc.
<a name="messagesTechnical"></a>technical | [Fieldset](#fieldsetObject) | **See note below**<br/>List of session and application-level e.g. Logon, Heartbeat, etc.
<a name="messagesFunctional"></a>functional | [FieldsetWithContext](#fieldsetWithContextObject) | **Optional**<br/>List of functional messages, describing technical messages within a given context.

**NOTE** To pass validation, a specification must contain either the `info` or `technical` sections (or both).

For those upgrading from 1.x schema versions, note that the `session` and `application` blocks have now been removed in favour of a single `technical` block, combined with a new `isSession` attribute at the individual message level. Note also that the JSON messages lists within a given block are keyed JSON fragments, as opposed to simple arrays under 1.x.

The Messages object may contain [vendor extensions](#vendorExtensions).


### <a name="infoSectionObject"></a>Info Section

```json
{
    "name": "Symbology",
    "description": "<p>The symbology used on our venue is ...",
    "historyKey": "unique_history_key"
}
```

An informational section used to convey specification data unrelated to messages. Example usage may include descriptions of symbology, connectivity approach or hours of operation.

Field Name | Type | Description
---|:---:|---
<a name="infoSectionName"></a>name | `string` | **Required**<br/>Name (header) of the section.
<a name="infoSectionDescription"></a>description | `string` | **Required**<br/>HMTL or plain text.
<a name="infoSectionHistoryKey"></a>historyKey | `string` | **Optional** <br/>Unique, persistent key suitable for linking an information section across versions.

The InfoSection object may contain [vendor extensions](#vendorExtensions).


### <a name="fieldsetObject"></a>Fieldset

```json
{
    "name": "NewOrderSingle",
    "wireId": "D",
    "direction": "in",
    "description": "...",
    "fields": [...],
    "historyKey": "unique_history_key",
    "isSession": false,
    "examples": [...]
}
```

A list of attributes collectively used to describe a fieldset (either a re-used block or a technical message) in the specification.

Field Name | Type | Description
---|:---:|---
<a name="fielsetName"></a>name | `string` | **Required**<br/>Name of the message.
<a name="fieldsetWireId"></a>wireId | `string` or `integer` | **Required (except blocks)**<br/>Wire identifier for the message (eg A for Logon in FIX).
<a name="fieldsetDescription"></a>description | `string` | **Required**<br/>HMTL or plain text description of the purpose and use of the message.
<a name="fieldsetDirection"></a>direction | `string` | **Optional**<br/>Indication of message flow. Value MUST be from the list: `"in"`, `"out"`, and `"both"`. Default: `both`.
<a name="fieldsetHistoryKey"></a>historyKey | `string` | **Optional** <br/>Unique, persistent key suitable for linking a fieldset across versions.
<a name="fieldsetIsSession"></a>isSession | `boolean` | **Optional** <br/>Boolean to indicate whether this fieldset relates to session-level connectivity. Default is `false`.
<a name="fieldsetIsHeader"></a>isHeader | `boolean` | **Optional** <br/>Boolean to indicate whether this fieldset represents a common header block. Default is `false`.
<a name="fieldsetIsTrailer"></a>isTrailer | `boolean` | **Optional** <br/>Boolean to indicate whether this fieldset represents a common trailer block. Default is `false`.
<a name="fieldsetFields"></a>fields | [Field](#fieldObject) | **Required**<br/> List of fields available within a message.
<a name="fieldsetExamples"></a>examples | `array` | **Optional**<br/>Array of **keys** referring to entries in the global [examplesObject](#examplesObject) which illustrate this Fieldset. Note that by placing entries here, the author is indicating that exaples should be displayed as a list at the end of the message as opposed to embedded within the description.
<a name="fieldsetNotes"></a>notes     | [Notes](#notesArray)         | **Optional**<br/>An list of notes about this fieldset.

The Fieldset object may contain [vendor extensions](#vendorExtensions).


### <a name="fieldsetWithContextObject"></a>FieldsetWithContext

```json
{
    "name": "NewMarketOrder",
    "wireId": "D",
    "direction": "in",
    "description": "...",
    "context": {
        "expressionType": "groovy",
        "expression": "$40 == '1'",
        "description": "OrdType (40) equals 1 (Market)"
    },
    "isSession": false,
    "fields": [...],
    "baseKey": "technical_key_1",
    "historyKey": "unique_history_key",
    "examples": [...]    
}
```

A functional view describes a message within a particular business context. Common use cases for functional views include:
- Views differentiating state (eg Order Acknowledgement vs Execution)
- Views differentiating order types (eg Limit Order vs VWAP)
- Views differentiating asset class (eg Spot FX Order vs FX Forward Order)


Field Name | Type | Description
---|:---:|---
<a name="fieldsetWithContextName"></a>name | `string` | **Required**<br/>Name of the message.
<a name="fieldsetWithContextWireId"></a>wireId | `string` or `integer` | **Required**<br/>Wire identifier for the message (eg A for Logon in FIX).
<a name="fieldsetWithContextDescription"></a>description | `string` | **Required**<br/>HMTL or plain text description of the purpose and use of the message.
<a name="fieldsetWithContextDirection"></a>direction | `string` | **Optional**<br/>Indication of message flow. Value MUST be from the list: `"in"`, `"out"`, and `"both"`. Default: `both`.
<a name="fieldsetWithContextIsSession"></a>isSession | `boolean` | **Optional**<br/>Indication of whether the message is related to establishing, maintaining or terminating a session connection.
<a name="fieldsetWithContextBaseKey"></a>baseKey | `string` | **Optional** <br/>The unique key given to the technical message upon which this functional view is based.
<a name="fieldsetWithContextHistoryKey"></a>historyKey | `string` | **Optional** <br/>Unique, persistent key suitable for linking a fieldset across versions.
<a name="fieldsetWithContextContext"></a>context | [Context](#contextObject) | **Required**<br/> Digital description of the intended message context.
<a name="fieldsetWithContextFields"></a>fields | [Field](#fieldObject) | **Required**<br/> List of fields available within a message.
<a name="fieldsetWithContextExamples"></a>examples | `array` | **Optional**<br/>Array of **keys** referring to entries in the global [examplesObject](#examplesObject) which illustrate this Fieldset. Note that by placing entries here, the author is indicating that exaples should be displayed as a list at the end of the message as opposed to embedded within the description.
<a name="fieldsetWithContextNotes"></a>notes     | [Notes](#notesArray)         | **Optional**<br/>An list of notes about this message.

The FieldsetWithContext object may contain [vendor extensions](#vendorExtensions).


### <a name="contextObject"></a>Context

```json
{
    "expressionType": "groovy",
    "expression": "$40 == '1'",
    "description": "OrdType (40) equals 1 (Market)"
}
```

Used to provide a digital description of the context for a functional (context-specific) message.

Field Name | Type | Description
---|:---:|---
<a name="contextExpressionType"></a>expressionType| `string` | **Required**<br/>Type of grammar used in expression field. Value MUST be `groovy`, `javascript` or `jsep`. Default: `groovy`.
<a name="contextExpression"></a>expression| `string` | **Required**<br/>Expression. The message context is considered to be in effect if this expression evaluates to true.
<a name="contextDescription"></a>description | `string` | **Required**<br/>Human-readable description of the context.

The Context object may contain [vendor extensions](#vendorExtensions).


### <a name="fieldObject"></a>Field

```json
{
    "fields": [
        {
            "position": 0,
            "name": "Header",
            "description": "Standard FIX header",
            "blockKey": "header_block_key",
        },
        {
            "position": 1,
            "name": "ClearingAccount",
            "description": "Clearing Account Type",
            "datatype": "uint8",
            "length": 1,
            "values": [
                {
                    "wireValue": 1,
                    "description": "Client"
                },
                {
                    "wireValue": 3,
                    "description": "House"
                }
            ]
        },
        {
            "position": 2,
            "name": "Price",
            "alwaysRequired": false,
            "conditions": [...],
            "description": "Price per share",
            "wireId": "44",
            "datatype": "Price"
        },
        ...
    ]
}
```

```json
{
    "fields": [
        ...,
        {
            "offset": 12,
            "name": "OrderInst",
            "description": "Order instructions bit field",
            "datatype": "uint8",
            "bits": [
                {
                    "offset": 0,
                    "width": 1,
                    "name": "LocateReqd",
                    "values": [
                        {
                            "wireValue": 0,
                            "description": "No Locate required"
                        },
                        {
                            "wireValue": 1,
                            "description": "Locate required"
                        }
                    ]
                },
                ...
            ]
        },
        ...
    ]
}
```

An array of fields to be present in a given fieldset (message or block).

Each field entry takes one of two forms:
- A reference to a block defined elsewhere using the `blockKey` reference, or
- An individual field defined in the fieldset.

**IMPORTANT** Re-used header and trailer blocks MUST be explicitly referenced within `fields` arrays just like any other block. There is no "assumption" of their presence based on protocol. (This requirement allows FinSpec to support the definition of multiple header/trailer blocks, with flexibility for authors to indicate the correct choice for a given fieldset).

Field Name | Type | Description
---|:---:|---
<a name="fieldOffset"></a>offset | `integer` | **Required for offset-based protocols**<br/>Zero-based offset of the field within the message for binary APIs.
<a name="fieldPosition"></a>position | `integer` | **Optional**<br/>Zero-based position of the field within the message for text/tag-value APIs. Where absent, ordering should be assumed from positioning within the JSON.
<a name="fieldkey"></a>key | `string` | **Optional**<br/>Unique field identifier. In case the same wire_id appears multiple times in the same message.
<a name="fieldWireId"></a>wireId | `string` | **Required for tag-value protocols**<br/>Wire identifier for the field (eg tag 35 in FIX).
<a name="fieldBlockKey"></a>blockKey | `string` | **Required for blocks** <br/>The unique reference for the block from the `blocks` section.
<a name="fieldDescription"></a>description | `string` | **Optional**<br/>Description of the purpose and use of the field.
<a name="fieldName"></a>name | `string` | **Required**<br/> Name of the field.
<a name="fieldDatatype"></a>datatype | [Datatype](#datatypeObject) | **Required**<br/> Field datatype from the list of API datatypes.
<a name="fieldMinValue"></a>minValue | `number` | **Optional**<br/>Minimum permitted field value for numeric types. Default: zero.
<a name="fieldMaxValue"></a>maxValue | `number` | **optional**<br/>Maximum permitted field value for numeric types. Default: unlimited
<a name="fieldExclusiveMinValue"></a>exclusiveMinValue | `boolean` | **Optional**<br/>Whether 'minValue' value is exclusive or not.
<a name="fieldExclusiveMaxValue"></a>exclusiveMaxValue | `boolean` | **optional**<br/>Whether 'maxValue' value is exclusive or not.
<a name="fieldMinLength"></a>minLength | `integer` | **Optional**<br/>Minimum permitted length of value for string types.
<a name="fieldMaxLength"></a>maxLength | `integer` | **Optional**<br/>Maximum permitted length of value for string types.
<a name="fieldLength"></a>length | `integer` | **Optional**<br/>Field value length in number of bytes (binary) or characters (text).
<a name="fieldValues"></a>values | [EnumArray](#enumArray) | **Optional**<br/>List of possible values if the field is enumerated.
<a name="fieldPattern"></a>pattern | `string` | **Optional**<br/>Regular expression to describe the pattern of the value for this field.
<a name="fieldAlwaysRequired"></a>alwaysRequired | `boolean` | **Optional**<br/>Boolean flag to indicate that this field is present in 100% of cases. Default for tag-value protocols: false
<a name="fieldConditions"></a>conditions | [ConditionArray](#conditionArray) | **Optional**<br/>Field conditional requirements.
<a name="fieldFields"></a>fields | [Field](#fieldObject) | **Optional**<br/>List of nested fields, indicating a repeating group under the current field.
<a name="fieldBits"></a>bits | [BitArray](#bitArray) | **Optional**<br/>Details about how bits are organized if this is a bit field.
<a name="fieldNotes"></a>notes     | [Notes](#notesArray)         | **Optional**<br/>An list of notes about this particular field.

The Field object may contain [vendor extensions](#vendorExtensions).


### <a name="enumArray"></a>Enum Array

```json
{
    "values": [
        {
            "wireValue": 1,
            "name": "Client",
            "description": "This is a long description",
            "isDefault": true
        },
        ...
    ]
}
```

A simple array of permitted enumerations for this field / bit field.

Field Name | Type | Description
---|:---:|---
<a name="valueWireValue"></a>wireValue | `string` or `number`| **Required**<br/>Enumeration as it appears **on the wire**.
<a name="valueName"></a>name | `string`| **Required**<br/>Short, common name for this enumeration.
<a name="valueDescription"></a>description | `string` | **Optional**<br/>Longer description of enumerated value.
<a name="valueIsDefault"></a>isDefault | `boolean` | **Optional**<br/>Whether this value should be considered the default for the field if it is absent from the message.

The EnumArray object may contain [vendor extensions](#vendorExtensions).


### <a name="bitArray"></a>BitsArray

```json
{
    "bits": [
        {
            "offset": 0,
            "width": 1,
            "name": "LocateReqd",
            "values": [
                {
                    "wireValue": 0,
                    "description": "No Locate required"
                },
                ...
            ]
        },
        ...
    ]
}
```

A simple array of bits within a field.

**NOTE** Where observed, the "parent" field is expected to have a datatype with an `isBitfield` value of true. If this is not the case, then the `bits` array should be ignored, and the parent should be treated as a non-bitfield field.

Field Name | Type | Description
---|:---:|---
<a name="bitOffset"></a>offset | `integer` | **Required**<br/>Bit offset within a bit field indicating start of bits group.
<a name="bitWidth"></a>width | `integer` | **Required**<br/>Width of bits group in terms of number of bits.
<a name="bitName"></a>name | `string`| **Required**<br/>Name of the bits group.
<a name="bitDescription"></a>description | `string` | **Optional**<br/>Description of the purpose and use of the bits group.
<a name="bitValues"></a>values | [EnumArray](#enumArray) | **Optional**<br/>List of possible values for this bits group.

The BitsArray object may contain [vendor extensions](#vendorExtensions).


### <a name="conditionArray"></a>ConditionArray

```json
{
    "conditions": [
        {
            "label": "PriceOnLimitOrder",
            "expressionType": "groovy",
            "expression": "($40 == 2)",
            "isReqd": true,
            "isAbsent": false
        },
        {
            "label": "NoPriceOnMarketOrder",
            "expressionType": "groovy",
            "expression": "($40 == 1)",
            "isReqd": false,
            "isAbsent": true
        },        
        ...
    ]
}
```

A simple array of conditions, **each of which should be evaluated in turn and the loop exited when the first evaluates to true**. See below for a description of condition grammar.

**NOTE** For conditions to be examined, `alwaysRequired` should be set to `false` on the parent field, to avoid the parent field always being considered mandatory.

Field Name | Type | Description
---|:---:|---
<a name="conditionLabel"></a>label| `string` | **Required**<br/>Name of the condition rule.
<a name="conditionExpressionType"></a>expressionType| `string` | **Optional**<br/>Type of grammar used in expression field. Value MUST be `"groovy"` or `"jsep"`. Default: `groovy`.
<a name="conditionExpression"></a>expression| `string` | **Required**<br/>Condition expression. This block will only take effect if this expression evaluates to true.
<a name="conditionIsReqd"></a>isReqd | `boolean` | **Required**<br/>Field is mandatory in such condition.
<a name="conditionIsAbsent"></a>isAbsent | `boolean` | **Required**<br/>Field should not be present in such condition.
<a name="conditionValues"></a>values | [EnumArray](#enumArray) | **Optional**<br/>List of (restricted) set of valid values if the expression evaluates to true.

The Condition object may contain [vendor extensions](#vendorExtensions).


#### Condition Expression Grammar

```js
Field 40 must equal a value of 2
($40 == 2)
Field 40 may equal 3 or 4
($40 == 3)  || ($40 == 4)
Field 40 must have a value of 2, and field 44 must be greater than zero
($40 == 2) && ($44 > 0)
Field 44 must be present
$44
Field 44 must NOT be present
!$44
```

```js
($2_OrderQty > 100)
```

In simple terms, it's a conditional expression used in if statements which evaluate to true or false.

For tag values based APIs, the value of the tag is expressed as: `$<wireId>` e.g. $40

For others e.g. native APIs, the value of a field is expressed as: `$<position>_<name>` e.g. $2_Price

Tokens that can be used in condition expression are:

1. Comparison operators e.g. ==, >, etc.
2. Logical operators e.g. &&, ||, etc.
3. Brackets
4. Use of ! to indicate 'not'
5. In the case of `groovy`, the comparison operator of ==~ may be used to indicate a regular expression.


### <a name="examplesObject"></a>ExamplesObject [Revised in 3.0]

```json
{
    "examples": {
        "example_1": {
            "name": "Market order example",
            "raw": "8=FIX.4.2|9=69|35=A|34=12|49=SENDER|52=20140228-05:42:38.026|56=TARGET|98=0|108=120|10=238|",
            "example": "8=FIX.4.2|9=69|5=A|34=12|49=SENDER|52=20140228-05:42:38.026|56=TARGET|98=0|108=120|10=238|",
            "description": "Market order example"
        },
        ...
    }
}
```

A keyed array of examples of various types. This object is placed in the root of the FinSpec document and referred to from within Fieldset objects using the unique key associate with each example. This structure allows examples to be conveniently re-used or shared between different Fieldsets.

Field Name | Type | Description
---|:---:|---
<a name="exampleName"></a>name | `string` | **Optional**<br/>Example name or title.
<a name="exampleRaw"></a>raw | `string` | **Optional**<br/>Actual example of a **complete** message (not just a fragement of it). Note that this should be plain text and not HTML. 
<a name="exampleExample"></a>example | `string` | **Optional**<br/>A more user-friendly display, intended to allow users to understand the example more clearly, perhaps by only liksting certain fields/values of interest. May contain HTML.
<a name="exampleDescription"></a>description | `string` | **Required**<br/>Description (or purpose) of the example. May contain HTML.

Each (sub-)object within the examplesObject may contain [vendor extensions](#vendorExtensions), but the top-level examplesObject may not.

---

### <a name="notesArray"></a>NotesArray

```json
{
    "notes": [
        {
            "note": "One really interesting fact about this field is...",
            "created_by_text": "Joe Bloggs",
            "updated_at": "2019-02-25 14:07:07"
        },
        ...
    ]
}
```

A simple array of noted associated with a document/fieldset/field (depending on where the array appears).

Field Name | Type | Description
---|:---:|---
<a name="noteNote"></a>note | `string` | **Required**<br/>The text of the note (may contain HTML).
<a name="noteCreatedByText"></a>created_by_text | `string` | **Required**<br/>Full name of the person (or process) that created the note.
<a name="noteUpdatedAt"></a>updated_at | `string` | **Required**<br/>Timestamp of the late update to this note in YYYY-MM-DD HH:mm:ss format.

The NotesArray object may contain [vendor extensions](#vendorExtensions).

---


## <a name="workflows"></a>WorkflowsObject

```json
{
    "workflows": [
        {
            "nav": {...},
            "event": {...},
            "states": {...},
            "transitions": {...}
        },
        ...
    ]
}
```

The workflows object section is a simple list of workflow objects, each defined as follows:

Field Name | Type | Description
---|:---:|---
<a name="workflowNav"></a>nav | [WorkflowNav](#workflowNavObject) | **Required**<br/>List of individual workflow described in this specifications. Must contain at least 1 workflow.
<a name="workflowStates"></a>states | [State](#stateObject) | **Required**<br/>list of possible individual states the object (e.g. Orders, Quote) can take in the workflow
<a name="workflowEvent"></a>event | [Event](#eventMessageObject) | **optional**<br/>List of market related events that trigger or result from a transition.
<a name="workflowTransitions"></a>transitions | [Transition](#transitionObject) | **Required**<br/> Description of transitions (including triggering event - FIX msg - and resulting state)


### <a name="workflowNavObject"></a>workflowNav

```json
 "nav": {
    "bbbbbbbb": {
        "name": "Request for Quote - Order Placement session",
        "description": "As an alternative to a streaming price subscription, a “Request for Quote” (RFQ) model is supported for FX Spot, FX Forward Outright, NDF & FX Swaps"
            }
        },
```

The array consists of workflow nav objects defined by the following fields:

Field Name | Type | Description
---|:---:|---
<a name="workflowName"></a>name | `string` | **Required**<br/>Name of the workflow.
<a name="workflowDescription"></a>description | `string` | **Required**<br/>Description of the specific workflow being modelled.


### <a name="stateObject"></a>State

```json
{
      "nav": "bbbbbbbb",
      "name": "Submitted",
      "isInitial": true,
      "description": "Request for Quote for Streaming price subscription"
},
```
The 'states' object is a list of state attributes which are defined by the fields below:

Field Name | Type | Description
---|:---:|---
<a name="stateNav"></a>nav | `string` | **Required**<br/> The workflow to which this state belongs.
<a name="stateName"></a>name | `string` | **Required**<br/>Name given to this specific state
<a name="statedescription"></a>description | `string` | **Optional**<br/>Description of this specific state
<a name="isInitial"></a>isInitial | `boolean` | **Required**<br/>Is this an initial state? can not transition from any other state.
<a name="isFinal"></a>isFinal | `boolean` | **Required**<br/>Is this a final state? Can not transition to any further state.
<a name="stateisDefaultError"></a>isDefaultError | `boolean` | **Optional (default to `FALSE`)**<br/>Is this the default state/message for rejection

**NOTE:** Within state objects, it is expected that exactly one state is marked as `isInitial`, and a second state is marked as `isFinal`.


### <a name="eventObject"></a>event

```json
"MARKET_OPEN": {
                "description": "This is my market open",
                "time": "09:00:00"
            },
```
The 'event' object is  defined by the fields below:

Field Name | Type | Description
---|:---:|---
<a name="eventDescription"></a>description | `string` | **Required**<br/>Description of this specific event.
<a name="eventTime"></a>time | `string` | **Optional**<br/> Time in UTC at which the event is triggered.


### <a name="transitionObject"></a>Transition

```json
{
    "description": "Cancellation of an open order",
    "start": ["Order.Acknowledged","Order.PartiallyFilled"],
    "trigger": {
                    "type": "technical",
                    "key": "KBfFbiqQ",
                    "direction": "in"
        },
    "responses": [
        {
            "messageWireId": "9",
            "where": {
                "expressionType": "groovy",
                "expression": "($150 == 4)&($39 == 0)"
              },
            "end": "Order.Acknowledged",
            "isSuccess": false
        },
        {
            "messageWireId": "9",
            "where": {
                "expressionType": "groovy",
                "expression": "($150 == 4)&($39 == 1)"
              },
            "end": "Order.PartiallyFilled",
            "isSuccess": false
        },
        {
            "messageWireId": "8",
            "where": {
                "expressionType": "groovy",
                "expression": "($150 == 4)&($39 == 4)"
              },
            "end": "Order.Closed",
            "isSuccess": true
        }
    ]
}
```
The 'transition' object is defined by the fields below:

Field Name | Type | Description
---|:---:|---
<a name="transitionDescription"></a>description | `string` | **Required**<br/>Description of this transition
<a name="transitionStart">transitionStart</a>start | `Array` | **Required**<br/>List of states the transition can initiate from.
<a name="transitionTrigger">trigger</a>trigger | [Trigger](#triggerObject) | **Optional**<br/> Solicited message (Action) required to trigger the transition.
<a name="responses"></a>responses | [Array](#responseObject) | **Required**<br/>List of possible responses: messages with field conditions.


#### <a name="triggerObject"></a>Trigger


The 'trigger' object is defined by the fields below:


Field Name | Type | Description
---|:---:|---
<a name="transitionType"></a>Type | `string` | **Required**<br/>Type of the message serving as a transition. Possible values: "technical", "functional", "event".
<a name="transitionKey"></a>Key | `string` | **Required**<br/> Reference of the message.
<a name="transitionDirection"></a>Direction | `string` | **Required**<br/>Direction of the message. Possible values: "In" or "Out"
<a name="transitionWhere"></a>Where | `string` | **Optional**<br/>Specific field condition in the message, expressed as a `groovy` expression.


#### <a name="responseObject"></a>Response

```json
"bbbb2266": {
    "type": "technical",
    "messageKey": "qdmCfuBx",
    "with": {
        "45": {
            "last": "34"
        },
        "372": {
            "last": "35"
        }
    },
    "description": "Cancel RFQ reject(j)",
    "end": "QuoteRequest.Active",
    "isSuccess": false
}
```

The 'response' object is defined by the fields below:

Field Name | Type | Description
---|:---:|---
<a name="responseMsgType"></a>Type | `string` | **Required**<br/>Type of the message serving as a response. Possible values: "technical", "functional", "event".
<a name="responseMsgKey"></a>MessageKey | `string` | **Required**<br/>Reference of the message.
<a name="responseWith"></a>With | [With](#withObject) | **Required**<br/>List of fields with specific expected values.
<a name="responseEnd"></a>End | `string` | **Required**<br/>Resulting state at the end of the transition
<a name="responseIsSuccess"></a>IsSuccess | `boolean` | **Required**<br/>Did the transition successfully complete?
<a name="responseDescription"></a>Description | `string` | **Optional**<br/>Description of the response.


#### <a name="withObject"></a>With

The 'with' object contains a series of objects. For the sake of clarity, we have organised those objects under 5 different blocks:

Block Name |  Description
---|---
[withSimpleValue](#withSimpleValueObject) | Objects used for simple fixed value of the tag.
[withReferencedValue](#withReferencedValueObject) | Objects used to define where to get the value of the tag from.
[withRange](#withRangeObject) | Objects used to define here to get the value of the tag from.
[withMath](#withMathObject) | Objects used to define the operators to compute the value of the tag.
[withIf](#withIfObject) | Objects used to define the conditions to compute the value of the tag.

Please read the following sections for details on the content of each block of objects.

#### <a name="withSimpleValueObject"></a>withSimpleValue

```json

"with": {
    	"150": {
			"fixed": "2"
		},
        "64": {
			"date": "20190131"
		},   
        "60": {
			"time": "NOW"
		}             
    }    

```
The 'withSimpleValue' object will contain one of the following fields:

Field Name | Type | Description
---|:---:|---
<a name="withSimpleValueFixed">Fixed</a> | `string` | **Optional**<br/>Fixed value input in field.
<a name="withSimpleValueDate">Date</a> | `string` | **Optional**<br/>date to be computed in the following format YYYYMMDD. Can also be a compute instruction like TODAY, YESTERDAY.
<a name="withSimpleValueTime">Time</a> | `string` | **Optional**<br/>Time in timestampUTC. Can also be a compute instruction like NOW.

#### <a name="withReferencedValueObject"></a>withReferencedValue

```json
"with": {
    	"37": {
			"last": "37"
		},
    	"58": {
			"last": "58",
            "default": "Trade"
		},        
        "194": {
			"this": "31"
		}           
    }
```

The 'withReferencedValue' object will contain one of the following fields:

Field Name | Type | Description
---|:---:|---
<a name="withReferencedValueThis">This</a> | `integer` | **Optional**<br/>tag of the field from which the value is copied - in this message.
<a name="withReferencedValueLast">Last</a> | `integer` | **Optional**<br/>tag of the field from which the value is copied - in previous linked message.
<a name="withReferencedValueDefault">Default</a> | `string` | **Optional**<br/>Default value if reference "this" or "last" tag is not found.

Note that This and Last are mutually exclusive.

#### <a name="withRangeObject"></a>withRange

```json
"32": {
		"min": {
			"fixed": 1
		},
		"max": {
			"last": "38"
		}
    }
```

The 'withRange' object will contain one of the following objects:

Field Name | Type | Description
---|:---:|---
<a name="withRangeMin">Min</a> | [with](#withObject) | **Optional**<br/>Min value of this field - used for integer fields.
<a name="withRangeMax">Max</a> | [with](#withObject) | **Optional**<br/>Max value of this field - used for integer fields.

#### <a name="withMathObject"></a>withMath

```json
"6": {
	"divide": {
		"numerator": {
			"sum": [
				{
					"multiply": [
						{
                        "last": "14"
						},
						{
                        "last": "6"
						}
					]
				},
				{
					"multiply": [
						{
                        "this": "31"
						},
						{
                        "this": "32"
						}
					]
				}
			]
		},
		"denominator": {
			"last": "38"
		}
	}
},
```
The 'withMath' object will contain one of the following objects:

Field Name | Type | Description
---|:---:|---
<a name="withMathSum">Sum</a> | [with](#withObject) | **Optional**<br/>Sum operation.
<a name="withMathSubstract">Substract</a> | [with](#withObject) | **Optional**<br/>Subtraction operation.
<a name="withMathMultiply">Multiply</a> | [with](#withObject) | **Optional**<br/>Multiply operation.
<a name="withMathDivide">Divide</a> | [divide](#divideObject) | **Optional**<br/>Divide operation.

The <a name="divideObject"></a>'Divide' object will contain the following 2 objects:

Field Name | Type | Description
---|:---:|---
<a name="withMathDivideNumerator">Numerator</a> | [with](#withObject) | **Required**<br/>Numerator of the divide operation.
<a name="withMathDivideDenominator">Denominator</a> | [with](#withObject) | **Required**<br/>Denominator of the divide operation.


#### <a name="withIfObject"></a>withIf

```json
"31": {
	"if": [
		{
			"condition": "$54 == 1",
			"max": {
				"last": "44",
				"default": 99999
			}
		},
		{
			"condition": "$54 != 1",
			"min": {
				"last": "44",
				"default": 0.001
			}
		}
	]
},
```

The 'withIf' object is used to set the conditions to define the value of a tag

Field Name | Type | Description
---|:---:|---
<a name="withIfArray">if</a> | [withIfArray](#withIfArray) | **Required**<br/>Array of conditions used to define the value of a tag - It contains at least 1 object.

The <a name="withIfArray"></a>If is a series of 'Ifcondition' objects defined by the following mandatory 2 objects:

Field Name | Type | Description
---|:---:|---
<a name="withIfArrayCondition">Condition</a> | `string` | **Required**<br/>Tag value condition expressed in `groovy` expression.
<a name="withIfArrayRef">#withObject</a> | [with](#withObject) | **Required**<br/>Resulting value of the tag.
