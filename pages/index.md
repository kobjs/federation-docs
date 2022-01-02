---
layout: page
title: SME GraphQL Federation
permalink: /
---

# Welcome to the SME GraphQL Federation 
{% assign date = '2021-12-30' %}

The SME GraphQL Federation relies on GraphQL schema and the Apollo Federation Gateway to provide a single 
endpoint to all data graphs for participating SME application services. 


![assets/img/docsy-jekyll.png](./assets/img/docsy-jekyll.png)

## Purpose

The SME GraphQL Federation offers secure, real-time access to queries and mutations for participating SME applications 
and third-party systems across the SME application portfolio in a consistent, reliable manner with options to register 
for lifecycle event notifications to trigger loosely coupled, cross system choreography.

SME Applications simply implement the USM OAuth2 protocol to enable authenticated and authorized end users and 
system users access to the the SME GraphQL Federation servcies. 

## Architecture Design  Principles

The SME Federation was conceived as a solution to the following design principles and goals.

- Independent Services with Defined APIs
  - All Applications and Services offer APIs to expose features
  - Use standardized API definition language to describe *model* and API *functionality*

 
- Consistent, Standardized API Design
  - Leverage Schema Design Guidelines and Best Practices
  - Collaborative Schema Design Review Process


- Loosely Coupled, Event Driven Architecture
  - Use Events to Notify Interested Applications of Lifecycle Events (e.g. Release Published)
  - Leverage AWS MSK (Kafka) Platform to enable Event Streams
  - Define Global SME Event Header to simply Application event filtering
  - Use Best Practices for Service Logging to enable Tracing across Service Choreography
  - Enable Applications to subscribe to Event Streams using Filters


- Standardized API Authentication and Authorization
  - Support for Single Sign-On across Service APIs
  - InfoSec Compliant Process to On/Off Board of End Users and System Users
  - Support Federated End-User or System User Authorization to enable each Service to enforce user authorization
  

## Features

What are these features? You should see the {% include doc.html name="Getting Started" path="getting-started" %}
guide for a complete summary. Briefly:

 - *Single, Unified Data Graph for All SME Applications* - Application clients can access data from 
   *multiple*  backend-end services in a **single** graphql query.
 - *Federated Design and Build Process* - SME Application teams design and build at their own pace while 
   still participating in the SME Federation.
 - *Separation of Concerns* - SME application focus on their own **domain data graph** avoiding the effort to design 
   and build data or API access methods to access other applications.
 - *Incremental, Evolution of Unified Data Graph* - SME application teams can *add* or *extend* the unified data 
   graph incrementally over time without impacting other applications.
 - *GraphQL Schema Management* - GraphQL schema *design-first* tools to edit, validate, package and release versioned 
   graphql schemas to Nexux for use by backend-services.
 - *GraphQL Schema Design Guides* -  User guides, design patterns and examples to encourage consistent schema design 
   and best practices.
   

For features, getting started with development, see the {% include doc.html name="Getting Started" path="getting-started" %} page. Would you like to request a feature or contribute?
[Open an issue]({{ site.repo }}/issues)
