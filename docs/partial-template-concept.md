# Partial Template Concept

Partial templates are a helpful tool when creating templates in the FHIR Converter. Leveraging partial templates allows you to reference them in multiple templates in the future, preventing you from having to rewrite the same code over again. Within the FHIR converter release, there are four types of partial templates: **Resources**, **References**, **Data Types**, and **Code Systems**. The following sections will describe the purpose of each category of released partial templates and give you things to consider when you’re creating your own partial templates.

## Data Type

Data type templates are building blocks used to define the contents of a data field in a HL7 v2 segment. In most cases, the data types in HL7 v2 map to FHIR attributes as part of the FHIR resource. The FHIR Converter includes a large number of data types as part of the release and new data type templates will be added as they are developed by the HL7 Community. As you create templates, you can create your own custom data types that map data fields in your HL7 v2 implementation to FHIR.

## Code System

Code system templates define mappings of common codes from HL7 v2 to FHIR bundles. An example of this is mapping “F” in HL7 v2 to “female” in a FHIR resource for gender. The FHIR Converter contains a set of starting code systems that have been defined for the released templates. You can also create your own code system templates. These templates will typically use if/else statements to map values from HL7 v2 to values in FHIR. You can see examples of this in the *Code Systems* folder of the released templates.

## Resource

Resource partial templates are used to create one specific FHIR resource in the FHIR bundle. Examples of these are patient, encounter, and condition. What you need in your resource may be message type specific or you may be able to use the same resource template across multiple message types.

While the resource template maps to a single FHIR resource type, it may pull from multiple segments in an HL7 v2 message. For example the released patient resource pulls from PID (Patient ID) and NK1 (Next of Kin) segments to generate the resource.

Most of the resource templates will reference data type and code system partial templates. Resource templates are created by parsing the HL7 v2 elements and mapping those directly to the FHIR attribute. When parsing these elements, the helper functions can be helpful to ensure that you are able to pull the exact data that you need. For more details on the helper functions, please see the helper function section of the [How-To-Guide for template creation](template-creation-how-to-guide.md) and for a full list of helper functions, see the [helper function summary](helper-functions-summary.md) page.

## Reference

Reference templates allow you to create references between two related resources. This is used to help ensure that the context of the HL7 v2 message is carried across into the FHIR bundle. Below is an example where the reference template creates a reference between the condition found in the diagnosis (from DG1 segment) and the encounter (PV1). The reference ensures that when the condition resource is created, there is a reference to the correct encounter that the condition came from:

```json
{
    "resource":{
        "resourceType": "Encounter",
        "id":"{{ID}}",
        "diagnosis":
        [
            {
                "condition":
                {
                    "reference":"{{REF}}",
                },
            },
        ],
    },
},
```

When this template is called in the main template, you must specify the values for **ID** and **REF**. Below is the example in the ADT_A01.hbs template. DG1Instance is a parameter passed to get each condition from the encounter.

```
{{>References/Encounter/diagnosis.condition.hbs ID=(generateUUID ../../PV1) REF=(generateUUID DG1Instance)}}
```

## Summary

Outside of the four types of partial templates outlined above, you are welcome to create your own types of partial templates. For more details on template creation, please see the [how-to guide](template-creation-how-to-guide.md) that gives tips on creating templates. You can also visit the [handlebars website](https://handlebarsjs.com/) for full handlebars documentation.
