## Title: Smart Building Things

### Submitter(s):

[Edison Chung (MINES St. Etienne)](https://orcid.org/0000-0002-5672-5508)

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

When operating smart buildings, aggregating and managing all data provided by heterogeneous devices in these buildings still requires a lot of manual effort. Besides the hurdles of data acquisition that relies on multiple protocols, the acquired data generally lacks contextual information and metadata about its location and purpose. Usually, each service or application that consumes data from building things requires information about its content and its context like, e.g.:
- which thing produces the data (sensor, meter, actuator, other technical component...) in a building
- which physical quantity or process is represented (temperature, energy supply, monitoring, actuation)
- which other building things are involved (e.g. sensor hosted by a duct or a space).
Through the increased use of model based data exchange over the whole life cycle of a building, often referred to as Building Information Modelling (BIM), a curated source for data describing the building itself is available including, amongst others, the topology of the building structured into sites, storeys and spaces.

Automatically tracking down data and their related things in a building would especially ease the configuration and operation of Building Automation and Control Systems (BACS) systems and Heating Ventilation and Air-Conditioning (HVAC) services during commissioning, operation, maintenance and retrofitting. To tackle these challenges, still, building experts make use of metadata and naming conventions, which are manually implemented in Building Management Systems (BMS) databases to annotate data and things. An important relationship of a thing is it´s location in the topology of a building, where the data is produced or used. For example, the temperature sensor of a space, the temperature setpoint of a zone, a mixing damper flap actuator of a HVAC component, etc. In addition, other attributes of things are of interest, such as cost or specific manufacturer data. One difficulty is especially the lack of a standardized way of creating, linking and sharing this information in an automated manner. On the contrary manufacturers, service providers and users introduce their own metadata, e.g. the Web of Things (WoT) Thing Description (TD), which aim at providing normalized and syntactic interoperability between things.

This use case is motivated by the need to enhance semantic interoperability between things in smart buildings and provide contextual links to the building. Data of the building is obtained from a BIM model. The use case builds on Web of Data technologies and reuses schemas available from the linked building data domain. It should serve as a use case template for many applications in an Internet of Building Things (IoBT).

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

- [Web of Things Thing Description (WoT TD)](https://www.w3.org/TR/wot-thing-description/)
- [Web of Things Binding Templates](https://w3c.github.io/wot-binding-templates/)

### Description:

The goal of this use case is to show the potential to automate workflows and address the heterogeneity of data as observed in the smart building domain. The examples show the potential benefits of combining WoT TD with contextual data obtained from a BIM.

The use cases is based on the [Open Smart Home Dataset](https://github.com/TechnicalBuildingSystems/OpenSmartHomeData/blob/master/00_OpenSmartHomeData.ttl)), which defines a BIM model for a flat combined with observations made by typical smart home sensors. We extend the dataset with thing descriptions of some of the items. The respective thing description of a temperature sensor in the kitchen of the considered flat is as follows:

```json
{
    "id": "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-temp-Sensor",
    "@context": [
        "https://www.w3.org/2019/wot/td/v1",
        {
            "osh": "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#",
            "bot": "https://w3id.org/bot#",
            "sosa": "http://www.w3.org/ns/sosa/",
            "om": "http://www.ontology-of-units-of-measure.org/resource/om-2/",
            "ssns": "http://www.w3.org/ns/ssn/systems/",
            "brick": "https://brickschema.org/schema/1.1/Brick#",
            "schema": "http://schema.org"
        }
    ],
    "title": "Kitchen-temp-Sensor",
    "description": "Kitchen Temperature Sensor",
    "@type": ["sosa:Sensor", "brick:Zone_Air_Temperature_Sensor", "bot:element"],
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
            "unit": "om:degreeCelsius",
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
    "ssns:hasSystemCapability": {
        "@id": "osh:Kitchen-temp-Sensor-Capa",
        "@type": "ssns:SystemCapability",
        "ssns:hasSystemProperty": {
            "@type": ["ssns:MeasurementRange"],
            "schema:minValue": 0.0,
            "schema:maxValue": 40.0,
            "schema:unitCode": "om:degreeCelsius"
        }
    }
}
```

Where the contextual information on the measurement range of the sensor is specified using the [SSNS](http://www.w3.org/ns/ssn/systems/) schema. The location information of the thing [Kitchen-temp-Sensor]( https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-temp-Sensor) is provided based on the [Building Topology Ontology (BOT)](https://w3id.org/bot), a minimal ontology developed by the [W3C Linked Building Data Community Group (W3C LBD CG)](https://www.w3.org/community/lbd/) to describe the topology of buildings in the semantic web. Additionally, the thing description of the corresponding actuator is given below.

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
            "brick": "https://brickschema.org/schema/1.1/Brick#"
        }
    ],
    "title": "Kitchen-tempS-Actuator",
    "description": "Kitchen Temperature Setpoint Actuator",
    "@type": ["sosa:Actuator", "brick:Zone_Air_Temperature_Setpoint", "bot:element"],
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

#### _Combining Topological Context and Thing Descriptions_

The scenario considered is related to the replacement of a temperature sensor in a BACS. The topological information localising the things, e.g. the [temperature sensor](https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-temp-Sensor) can be used to automatically commission the newly replaced sensor and link it to existing control algorithms. For this purpose, the identifiers of suitable sensors and actuators are needed and can be, for example, queried via [SPARQL](https://www.w3.org/TR/sparql11-query/). Here the query uses some additional classification of sensors from [BRICK schema](https://brickschema.org/ontology/1.1).

```sparql
PREFIX bot: <https://w3id.org/bot>
PREFIX brick: <https://brickschema.org/schema/1.1/Brick#>
PREFIX osh: <https://w3id.org/ibp/osh/OpenSmartHomeDataSet#>
SELECT ?sensor ?actuator
WHERE{
  ?space a bot:Space .
  ?space bot:containsElement ?sensor .
  ?space bot:containsElement ?actuator .
  ?sensor a brick:Zone_Air_Temperature_Sensor .
  ?actuator a brick:Zone_Air_Temperature_Setpoint .
}
```

Similarly this data can obtained via a REST API built upon the [HTTP](https://tools.ietf.org/html/rfc7231#section-4) protocol. Below is an example endpoint applying [REST](https://roy.gbiv.com/pubs/dissertation/top.htm) style for getting the same information for a specific space name:

```json
GET "https://server.example.com/api/locations?space=osh:Kitchen&sensorType=brick:Zone_Air_Temperature_Sensor&actuatorType=brick:Zone_Air_Temperature_Setpoint"
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
  ],
  "actuators": [
    "https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-tempS-Actuator"
  ]
}
```

#### _Automated Update of Fault Detection Rule based on Thing Description_

Another related use case in smart buildings, which would greatly benefit from harmonised thing descriptions and attached localisation information is related to the detection of unexpected behaviour, errors and faults. An example for such a detection of faults is the rule-based surveillance of sensor values. A generic rule applicable to sensors is that the observation values stay within the measurement range of the sensor. Again, in the case of maintenance as described above a sensor is replaced.

Some agent configuring fault detection rules, can obtain the measurement range from the sensor's TD (see above) to obtain the parameters to configure the mentioned rule. Again, a query or API call retrieving this information (schema:minValue/ schema:maxValue) can be used to update the upper and lower bound of the values provided by the [sensor](https://w3id.org/ibp/osh/OpenSmartHomeDataSet#Kitchen-temp-Sensor).

### Security Considerations:

Security in smart buildings is of importance. In particular, access control needs to be properly secured. Also, from other observations, e.g. electricity consumption, clues can be made such as presence in a home. Hence, security needs to properly in place 

### Privacy Considerations:

Privacy considerations can be of a concern if observations of sensors can be matched to individuals.

### Existing standards:

- [SAREF4Bldg an ETSI Standard](https://saref.etsi.org/saref4bldg/)
- [SOSA/SSN a W3C Recommendation](https://www.w3.org/TR/vocab-ssn/)
- [Industry Foundation Classes (IFC) an ISO standard](http://www.buildingsmart-tech.org/ifcOWL/IFC4_ADD2)
- [Building Topology Ontology](https://w3id.org/bot)
- [BRICK](https://brickschema.org/ontology/1.1)