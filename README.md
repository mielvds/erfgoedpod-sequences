# HTTP sequences for Erfgoedpod

_Run `./render.sh` to generate the diagrams locally_

## Preliminaries

### WebID

Simple universal identification mechanism for the Web and a core aspect of Solid. Used in ErfgoedPod to identify acting organisations in the network (eg. a Cultural Heritage Institution, a Registry, ...) 

Example: http://kb.nl#me

### Linked Data Notifications (LDN)

Communication protocol between two actors in the network. Defines an inbox to receive an LDN. An inbox can be discovered thorugh a `Link` header when requesting a resource, like the WebID. 

Spec: [](https://www.w3.org/TR/ldn/) 

Example:

``` http
POST /inbox HTTP/1.1
Host: registry.nde.nl
Content-Type: application/ld+json;profile="https://www.w3.org/ns/activitystreams"
Content-Language: en

{
  "@context": "https://www.w3.org/ns/activitystreams",
  "summary": "KB created dataset.ttl",
  "type": "Create",
  "actor": "http://kb.nl#me",
  "object": "created pod.kb.nl/dataset.ttl"
}

```

### Eventlog

The eventlog is a mandatory log stored in each Pod or Service Hub (eg. Registry) that participates in the network. Lifecycle events of datasets (and other artefacts) are stored there.

Example:

pod.kb.nl/eventlog

```
@prefix lode: <http://linkedevents.org/ontology/>.
@prefix time: <http://www.w3.org/2006/time#>.

_:1 a lode:Event;
    lode:atTime [ 
        a time:Instant; 
        time:inXSDDateTimeStamp  2020-04-12T10:30:00+10:00 . 
    ];
    lode:involvedAgent <http://kb.nl#me>;
    dc:description "Created pod.kb.nl/dataset.ttl".

...
```
``` log
[2020-04-12T10:30:00+10:00] Created pod.kb.nl/dataset.ttl
[2020-04-12T11:30:00+10:00] Created pod.kb.nl/dataset-desc.ttl
[2020-04-12T12:30:00+10:00] Requested registration: pod.kb.nl/dataset.ttl with registry.nde.nl
[2020-04-12T13:30:00+10:00] registry.nde.nl registered pod.kb.nl/dataset.ttl
```

### Policy

A policy is a configuration file with machine-readable business rules and is the driver for the Orchestrator component. It dictates what actions the Orchestrator should take when it is notified of an event (typically by an incoming Linked Data Notification).
## Register-function API

Read the [Register API specification](/api/api-ldn.md) for more detail on a Web _application programming interface_ (API) for NDE's Register that is aligned with the Solid protocol.
## HTTP Sequences

### Initialize a Cultural Heritage Pod

![init erfgoedpod](http://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/mielvds/erfgoedpod-sequences/main/init-erfgoedpod.puml)

### Register an Orchestrator for a Cultural Heritage Pod

![register with orchestrator](http://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/mielvds/erfgoedpod-sequences/main/register-with-orchestrator.puml)

### Add a new Cultural Heritage Institution to the Registry

![new organisation registry](http://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/mielvds/erfgoedpod-sequences/main/new-organisation-registry.puml)

### Add a new Dataset to the Registry

![new dataset](http://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/mielvds/erfgoedpod-sequences/main/new-dataset.puml)

### Update a new Cultural Heritage Institution to the Registry

![update dataset](http://www.plantuml.com/plantuml/proxy?src=https://raw.githubusercontent.com/mielvds/erfgoedpod-sequences/main/update-dataset.puml)