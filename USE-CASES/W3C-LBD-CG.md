# TODOs need to be removed!


## Title: Smart Linked Building Data

### Submitter(s):

Edison Chung (MINES St. Etienne)

[Hervé Pruvost (Fraunhofer IIS EAS)](https://orcid.org/0000-0001-7604-8543)

[Georg Ferdinand Schneider (Schaeffler Technologies AG)](https://orcid.org/0000-0002-2033-859X)

### Reviewer(s):

Michael Lagally (Oracle)  
Sebastian Kaebisch (Siemens)  

### Tracker Issue ID:

<please leave blank>

### Category:

<please leave blank>

### Class:

<please leave blank>

### Status:

<please leave blank>

### Target Users

- device owners
- device user
- service provider
- device manufacturer
- gateway manufacturer
- identity provider
- directory service operator

### Motivation:

When operating buildings, aggregating and managing all data provided by their heterogeneous devices still require lot of manual effort. Besides the hurdles of data acquisition that relies on multiple protocols, the acquired data generally lacks much contextual information and metadata about its location and purpose. Usually, each service or application that consumes data requires information about its content and its context like e.g.:
- which thing produces the data (sensor, meter, actuator, other technical component...) in a building
- which physical quantity or process is represented (temperature, energy supply, monitoring, actuation)
- which other building things are involved (e.g. sensor hosted by a duct or a space).

Automatically tracking down data and their related things in a building would especially ease the configuration and operation of Building Automation and Control Systems (BACS) systems and Heating Ventilation and Air-Conditioning (HVAC) services during commissioning, operation, maintenance and retrofitting. To tackle this challenges, still, building experts make use of data properties, metadata and naming conventions which are manually implemented in Building Management Systems (BMS) databases to annotate data and things. An important relationship in the data is the location of a thing to one or more topological elements of a building where the data is produced or used. Foe example, the temperature sensor of a space, the temperature setpoint of a zone, a mixing damper flap acutator of a HVAC component, etc. In addition, other attributes of things are of interest, such as cost or specific manufacturer data. One difficulty is especially the lack of a standardized way of creating and sharing this information in an automated manner. On the contrary manufacturers, service providers and users introduce their own metadata, e.g. the Web of Things (WoT) Thing Description (TD), which aim at providing normalized and syntactic interoperability between things.

This use case is motivating by the need to enhance semantic interoperability between things in smart buildings and their relationships to the building. The use case provides and example web of Web of Data technologies and reuses schemas available from the Linked Building Data domain. Is should serve as a use case template for many applications in an Internet of Building Things (IoBT).

### Expected Devices:

- Actuators
- Sensors
- Devices from the building context
- Devices from the HVAC system
- Smart devices

### Expected Data:

- Sensor ID
- Thing Descriptions
- Protocol integrations
- Sensor readings
- Semantic location 
- Geolocation

### Dependencies - Affected WoT deliverables and/or work items:

- Web of Things Thing Description (WoT TD) needed

### Description:

The goal of this use case is to show the potential to automate workflows and address the heterogeneity of data as observed in the smart building domain. The examples use a combination of Web of Data standards combined with WoT TD.

#### Combining Topological Context and Thing Descriptions

The scenario considered is related to the replacement of a [temperature sensor](https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-temp-Sensor) in a BACS. The following TD describes a kitchen with a number of sensors, including the replaced one, in a building (The use case is based on the [Open Smart Home Dataset](https://github.com/TechnicalBuildingSystems/OpenSmartHomeData/blob/master/00_OpenSmartHomeData.ttl)).

Example JSON-LD describing the topology of a [kitchen](https://github.com/TechnicalBuildingSystems/OpenSmartHomeData/blob/master/00_OpenSmartHomeData.ttl) where the temperature sensor and the temperature setpoint actuator are deployed:

```json
{
    "@context": {
        "osh": "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#",
        "bot": "https://w3id.org/bot#",
        "sosa": "http://www.w3.org/ns/sosa/",
        "ssn": "http://www.w3.org/ns/ssn/",
        "dog": "http://elite.polito.it/ontologies/dogont.owl#"
    },
    "@id": "osh:Kitchen",
    "@type": ["bot:Space", "dog:Kitchen", "sosa:FeatureOfInterest"],
    "bot:containsElement": [{
            "@id": "osh:Kitchen-temp-Sensor"
        },
        {
            "@id": "osh:Kitchen-tempT-Sensor"
        },
        {
            "@id": "osh:Kitchen-humid-Sensor"
        },
        {
            "@id": "osh:Kitchen-brigh-Sensor"
        },
        {
            "@id": "osh:Kitchen-tempS-Actuator"
        },
        {
            "@id": "osh:Kitchen-heater"
        }
    ],
    "ssn:hasProperty": [
        {
            "@id": "osh:Kitchen-temp",
            "@type": "sosa:ObservableProperty",
            "sosa:isObservedBy": {
                "@id": "osh:Kitchen-temp-Sensor"
            }
        }, {
            "@id": "osh:Kitchen-tempS",
            "@type": "sosa:ActuatableProperty"
        }
    ]
}
```

The location information of the tangible object [kitchen](https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen) is provided based on the [Building Topology Ontology (BOT)](https://w3id.org/bot), a minimal ontology developed by the [W3C Linked Building Data Community Group (W3c LBD CG)](https://www.w3.org/community/lbd/) to describe the topology of buildings in the semantic web.

The topological information localising the things, e.g. the [temperature sensor](https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-temp-Sensor) can be used to automatically commission the newly replaced sensor and link it to existing control algorithms. For this purpose the identifiers of suitable sensors and actuators are needed and can be, for example, queried via [SPARQL](https://www.w3.org/TR/sparql11-query/).

```sparql
PREFIX bot: <https://w3id.org/bot>
PREFIX brick: <https://brickschema.org/schema/1.1/Brick#>

SELECT ?sensor ?actuator
WHERE{
  ?space a bot:Space .
  ?space bot:containsElement ?sensor .
  ?space bot:containsElement ?actuator .
  ?sensor a brick:Zone_Air_Temperature_Sensor .
  ?actuator a brick:Zone_Air_Temperature_Setpoint .
}
```

Similarly a query can be implemented within an API built upon the [HTTP:](https://tools.ietf.org/html/rfc7231#section-4) scheme. Below is an example endpoint applying [REST](https://roy.gbiv.com/pubs/dissertation/top.htm) style for getting same information for a specific space name:

```json
GET "https://example-wot-servername/api/locations?space=Kitchen&sensors=true&actuators=true"
API response:
{
  "location": {
    "site": {
      "id": "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Site1",
      "name": "Site1"
    },
    "building": {
      "id": "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Building1",
      "name": "Building1"
    },
    "zone": null,
    "storey": {
      "id": "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Level2",
      "name": "Level2"
    },
    "space": {
      "id": "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen",
      "name": "Kitchen"
    },
  "sensors": [
    "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-temp-Sensor",
    "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-tempT-Sensor",
    "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-humid-Sensor",
    "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-brigh-Sensor"
  ],
  "actuators": [
    "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-tempS-Actuator"
  ]
}
```

#### Automated Update of Fault Detection Rule based on Thing Description

Another related case in smart buildings, which would greatly benefit from harmonised thing descriptions is related to the detection of unexpected behaviour, errors and faults. An example for such a fault detection is the rule-based surveillance of sensor values. A generic rule is that the sensor values should be withing the operation range of the sensor. Again in the case of maintenance as described above a sensor is replaced. The respective sensor provides its capability and operating range in its TD (see example below). These are specified using the [SSNS](http://www.w3.org/ns/ssn/systems/) schema.

```json
{
    "id": "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-temp-Sensor",
    "@context": [
        "https://www.w3.org/2019/wot/td/v1",
        {
            "osh": "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#",
            "bot": "https://w3id.org/bot#",
            "sosa": "http://www.w3.org/ns/sosa/",
            "ssn": "http://www.w3.org/ns/ssn/",
            "om": "http://www.ontology-of-units-of-measure.org/resource/om-2/",
            "dog": "http://elite.polito.it/ontologies/dogont.owl#",
            "ssns": "http://www.w3.org/ns/ssn/systems/",
            "schema": "http://schema.org"
        }
    ],
    "title": "Kitchen-temp-Sensor",
    "description": "Kitchen Temperature Sensor",
    "@type": ["sosa:Sensor", "dog:TemperatureSensor", "bot:element"],
    "@reverse": {
        "bot:containsElement": {
            "@id": "osh:Kitchen"
        }
    },
    "securityDefinitions": {
        "basic_sc": {
            "scheme": "basic",
            "in": "header"
        }
    },
    "security": [
        "basic_sc"
    ],
    "properties": {
        "Kitchen-temp": {
            "type": "number",
            "minimum": 0.0,
            "maximum": 40.0,
            "unit": "om:degree_Celsius",
            "forms": [
                {
                    "href": "https://kitchen.example.com/temp",
                    "contentType": "application/json",
                    "op": "readproperty"
                }
            ],
            "readOnly": true,
            "writeOnly": false
        }
    },
    "sosa:observes": {
        "@id": "osh:Kitchen-temp",
        "@type": "sosa:ObservableProperty"
    },
    "ssns:hasOperatingRange": {
        "@id": "osh:Kitchen-temp-Sensor-OpRa",
        "@type": "ssns:OperatingRange",
        "ssns:inCondition": {
            "@type": ["ssns:Condition", "schema:PropertyValue"],
            "schema:minValue": 0.0,
            "schema:maxValue": 40.0,
            "schema:unitCode": "om:degreeCelsius"
        }
    },
    "ssns:hasSystemCapability": {
        "@id": "osh:Kitchen-temp-Sensor-Capa",
        "@type": "ssns:SystemCapability",
        "ssns:hasSystemProperty": {
            "@type": ["ssns:MeasurementRange", "schema:PropertyValue"],
            "schema:minValue": 0.0,
            "schema:maxValue": 40.0,
            "schema:unitCode": "om:degreeCelsius"
        }
    }
}

```

Again a query or call retrieving this information (minimum/maximum) can be used to the update the upper and lower bound of the values provided by the [sensor](https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-temp-Sensor).

Example of a temperature setpoint actuator:

```json
{
    "id": "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-tempS-Actuator",
    "@context": [
        "https://www.w3.org/2019/wot/td/v1",
        {
            "osh": "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#",
            "bot": "https://w3id.org/bot#",
            "sosa": "http://www.w3.org/ns/sosa/",
            "ssn": "http://www.w3.org/ns/ssn/",
            "dog": "http://elite.polito.it/ontologies/dogont.owl#"
        }
    ],
    "title": "Kitchen-tempS-Actuator",
    "description": "Kitchen Temperature Setpoint Actuator",
    "@type": ["sosa:Actuator", "dog:ThermostaticRadiatorValve", "bot:element"],
    "@reverse": {
        "bot:containsElement": {
            "@id": "osh:Kitchen"
        }
    },
    "securityDefinitions": {
        "basic_sc": {
            "scheme": "basic",
            "in": "header"
        }
    },
    "security": [
        "basic_sc"
    ],
    "actions": {
        "Kitchen-tempS": {
            "forms": [
                {
                    "href": "https://kitchen.example.com/tempS"
                }
            ]
        }
    },
    "ssn:forProperty": {
        "@id": "osh:Kitchen-tempS",
        "@type": "sosa:ActuatableProperty"
    }
}
```

### Security Considerations:

Security in smart buildings is of importance. In particular, access control needs to be propertly secured. Also from other observations, e.g. electricity consumption, clues can be made such as precence in a home. Hence, security needs to properly in place 

### Privacy Considerations:

Privacy considerations can be of a concern if observations of sensors can be matched to individuals.

### Gaps:

<Describe any gaps that are not addressed in the current WoT standards and building blocks>

### Existing standards:

- [SAREF4Bldg an ETSI Standard](https://saref.etsi.org/saref4bldg/)
- [SOSA/SSN a W3C Recommendation](https://www.w3.org/TR/vocab-ssn/)
- [Industry Foundation Classes (IFC) an ISO standard](http://www.buildingsmart-tech.org/ifcOWL/IFC4_ADD2)
- [Building Topology Ontology](https://w3id.org/bot)
- [BRICK](https://brickschema.org/ontology/1.1)

### Comments:

#### Related use cases from the Linked Building Data (LBD) community:  
Source (<https://w3c-lbd-cg.github.io/lbd/UseCasesAndRequirements/>)

- 4.49 Building Energy Management System for Energy Efficient Operation (https://w3c-lbd-cg.github.io/lbd/UseCasesAndRequirements/#uc_id_37)
  - parametrization of the Building Energy Management System (BEMS)
  - initial and continuous commissioning

- 4.44 BEMS Commissioning (https://w3c-lbd-cg.github.io/lbd/UseCasesAndRequirements/#uc_id_32)
  - Setup of devices and tests.
  - This could include process of metadata creation

- 4.53 BEMS Retrofit (https://w3c-lbd-cg.github.io/lbd/UseCasesAndRequirements/#uc_id_41)
  - Retrofitting of HVAC equipment
  - parametrization of the BEMS

- 4.15 BEMS Planing and Design (https://w3c-lbd-cg.github.io/lbd/UseCasesAndRequirements/#uc_id_3)
  - design of BACS systems

- 4.16 Integration of BIM and district level 3D models with real-time data from sensors and user feedback to analyze and correlate buildings utilization and provide real-time feedback about energy-related behaviors (https://w3c-lbd-cg.github.io/lbd/UseCasesAndRequirements/#uc_id_4)
  - Linking data with its building topological contextual
  - Localization of data in 3D geometries

- 4.17 BEMS Operating (https://w3c-lbd-cg.github.io/lbd/UseCasesAndRequirements/#uc_id_5)
  - Operation of HVAC systems using connected Devices
  - Use of HVAC system topology
  - TUBES
