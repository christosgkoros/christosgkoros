+++
title = 'OpenAPI to gRPC with Quarkus'
date = 2022-08-23T10:06:03+03:00
tags = ['openapi', 'grpc', 'quarkus', 'api-specification-first']
description = 'How to implement one of your REST services as gRPC'
+++

![Photo by Emile Perron on Unsplash](/posts/2022/08/23/openapi-to-grpc-with-quarkus.webp "Photo by Emile Perron on Unsplash")

## Introduction

REST (OpenAPI) and gRPC are two of the most popular formats for APIs. REST is the style of choice of most public APIs, and gRPC is a popular alternative for internal APIs that need an efficient network.

The OpenAPI Specification defines a standard to describe REST APIs and their capabilities.

By default, gRPC uses Protocol Buffers, Google’s open-source mechanism for serializing structured data. The Protocol buffer schema is also an API specification.

TM Forum is a global association for organizations in the telecommunications industry. TM Forum has developed a set of OpenAPI specifications covering the telco domain.

## Use Case

This article was born as I wanted to familiarize myself with gRPC and understand its benefits and constraints. Yet, with OpenAPIs used extensively nowadays due to the API-First Approach, it did not feel right to create a simple API out of my head.

Instead, what would happen if we implemented a mature OpenAPI with gRPC? TM Forum APIs are a perfect fit for our purpose, as they are complex but implementation agnostic.

## TL;DR

A POC of a GRPC service, using only an OpenAPI specification as input and everything else generated from that.

[GitHub - Gorosc/openapi-to-grpc-quarkus](https://github.com/Gorosc/openapi-to-grpc-quarkus)

[Postman](https://www.postman.com/gkoroscapidesign/workspace/openapi-to-grpc-quarkus-workspace/overview)

## Design Choices

I chose [TMF 720 Digital Identity API](https://github.com/tmforum-apis/TMF720_DigitalIdentity), one of the latest additions in the TMF's API Table. It provides the ability to manage a digital identity (credentials, passwords, biometrics, etc.). The business logic is not that important in the scope of this POC. It is a mature API but not too bloated as older TMF APIs, and we will implement it as a CRUD API.

![Digital Identity API](/posts/2022/08/23/digital_identity_api.webp "Digital Identity API")

For the implementation, the tech choices are:

* Quarkus framework: We could implement the Service using plain Java and the default server included in gRPC. However, I chose a framework since we do have to manage configuration and database connections. I did not feel like implementing those areas by hand. Quarkus has first-class support for gRPC.

* MongoDB: The API has a complex schema. Its separate entities don't have much meaning alone. Thus we want to save the whole model as a Document, and MongoDB is the first that comes to mind.

* OpenAPI Generator is used to generate the Protobuf schema from the OpenAPI.

### Solution

After evaluating gnostic, google's tool, I decided to the OpenAPI generator. OpenAPI generator a) comes as a maven plugin and can be used in our build chain b) breaks messages and services into different proto files. As a result, the gRPC generator will produce more accessible files to work within our IDE.

![Software Architecture](/posts/2022/08/23/openapi-to-grpc-solution.webp "Software Architecture")

*In the absence of time, this POC is not production-ready. Validation and proper error handling are missing. But, following a test-driven development approach, all main flows are covered.*

## Implementation

### Setup

Our maven setup will include Quarkus and the necessary dependencies for gRPC and Mongo:

{{< gist christosgkoros 4ab902a1cf15a30f5c3a056b10892785 >}}

OpenAPI generator comes into play as a plugin. It will be the first plugin in our build chain. The critical part of this configuration is the output folder */src/main/proto* as this is where Quarkus expects to find the proto schemas.

{{< gist christosgkoros c396fb08a9e450297c6e1534c6c9ae30 >}}

Our initial file structure will look like this:

![Initial file structure](/posts/2022/08/23/initial-file-structure.webp "Initial file structure")

## Generation

Let’s go ahead and invoke “mvn compile.” The plugins will execute sequentially, and the outcome will be like the following:

![After generation](/posts/2022/08/23/after-generation.webp "After generation")

The OpenAPI generator plugin has populated our */src/main/proto* folder with the generated protobuf schema for the services and the models. We will use the *digital_identity_service.proto* for our use case. It looks like this:

{{< gist christosgkoros 5d517e1e4b7867a21671640d454b5024 >}}

We are going to implement each of the methods of the gRPC Service. Quarkus has generated an interface for the service that we can use, automatically making it reactive with [Mutiny](https://quarkus.io/guides/mutiny-primer)

{{< gist christosgkoros dd5d6bc5dd8a759e0a5c2588175ce23e >}}

*We could use the default gRPC Java implementation DigitalIdentityServiceGrpc.DigitalIdentityServiceImplBase. However, since we are using Quarkus it made sense to go reactive. That way this POC is more usable as a reference implementation.*

## Create Digital Identity

Create is the best option for our first implementation to have some data available for our following operations.

The TMF specification utilizes different models for Create, Read and Update. As a result, the code generation produces multiple gRPC Model Classes that one another cannot map directly.

* DigitalIdentity
* DigitalIdentityCreate
* DigitalIdentityUpdate

*Solution: We will use [com.google.protobuf.util.JSONFormat](https://developers.google.com/protocol-buffers/docs/reference/java/com/google/protobuf/util/JsonFormat) to serialize the gRPC Models to JSON. The difference in those models is only some fields (id, status, createdAt etc.). Thus, deserializing the JSON to a different Model is an efficient mapping method. This is not an overhead as we also need JSON serialization for Mongo (check below).*

{{< gist christosgkoros 37a298b1dafa1b3babc6bf7902b57cf0 >}}

*Hypermedia is not defined in either protobuf or gRPC. I still found it interesting to populate the href attribute with the method and the input required to fetch the specific Digital Identity.*

We don’t want to spill any gRPC logic to our DB domain and Mongo Connector. Since we don’t have Models for Persistence, our Mongo Service will accept JSON formatted Strings as input and return as output. Of course, this has the undesired effect of having unvalidated and untyped input in our database. We are going to accept the risk in the scope of this POC.

{{< gist christosgkoros 61d8d4adb51871299c97c96b02982b8b >}}

## List Digital Identities

Similarly, we retrieve the results from Mongo and use JSONFormat to deserialize them into gRPC Models. We also map the Multi that we receive from the database to a Uni. We collect all results to a List and add them to the Uni response.

{{< gist christosgkoros e3d20eb5e399889e41f235450fd2f232 >}}

In Mongo, we are using as id the native _id ObjectId. But, since this is not part of our specification (and thus, the JSON deserialization will fail), we replace it with id.

{{< gist christosgkoros 148d118c6e6fa20716de721bcb6e486c >}}

## Retrieve Digital Identity

Retrieving a single digital identity and mapping is the same as List.

{{< gist christosgkoros 9642ad99ac81b9ad447461a3e3f6e4ab >}}

As we search with *_id*, we don’t expect more than one result. The gRPC service does not need to know the detail that Mongo still returns a Multi in this case.

{{< gist christosgkoros 86c02cf4110160c90a8cde665dadba6da >}}

## Delete Digital Identity

Empty in gRPC is an object we need to create and send back.

{{< gist christosgkoros f4a496ddb13d48bd9535b256f32431f5 >}}

The Mongo delete operation returns a void. I challenged myself to make it return the document or a long (number of hits) but settled with a simple void.

{{< gist christosgkoros 61ce2ecbbbc5e44ad56b7659336112f8 >}}

## Patch Digital Identity

Patch is very like Create, but we extract two arguments from the request, and the Mongo service returns the document.

{{< gist christosgkoros 1cc9671da5445f129b49cec0e0d213e6 a>}}

For the update to be a merge patch as the operation dictates, we need to use the *$set* operator.

{{< gist christosgkoros b09ccd16feb1022c26243cb35f0471e0 >}}

## Wrap Up

We created the service, and we fulfilled our basic requirements. All service-related code is generated from the OpenAPI specification. We implemented the glue code. However, it could probably also be part of the generation.

I also came to some valuable insights:

* gRPC is more of a framework than a specification
* Protobuff schema has less detail than the OpenAPI, and the generation left out some information like the business errors and codes.
* Protobuf Util JSONFormat can be used to create JSON documents for Mongo DB storage.
* JSONFormat can also be used to map between similar objects.
* Since we have a more efficient message format for our service network layer, the JSON serialization needed for Mongo strikes me as odd.

## References

* [Quarkus GRPC](https://quarkus.io/guides/grpc-getting-started)
* [OpenAPI Generator](https://openapi-generator.tech/docs/generators/protobuf-schema)
* [TMF Open API table](https://projects.tmforum.org/wiki/display/API/Open+API+Table)