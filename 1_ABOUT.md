# 1. About this project

- [1. About this project](#1-about-this-project)
  - [1.1. Goals and objectives](#11-goals-and-objectives)
    - [1.1.1. Deliverables](#111-deliverables)
    - [1.1.2. Additional perspectives](#112-additional-perspectives)
    - [1.1.3. Potential partnerships](#113-potential-partnerships)

## 1.1. Goals and objectives

This projects aims at specifying and implementing a collaborative Markdown-based note taking app for the web. The idea is directly inspired by [Overleaf](https://www.overleaf.com), which offers collaborative editing of LaTeX documents. The app will let multiple users collaborate simulatenously on the same document, and let clients temporarily disconnect. The proposed approach is split as follows :

+ High-level definition of functional and non-functional requirements of the app, with a mockup;
+ Prior art analysis on distributed data structures and operational transform for simulatenous collaborative text editing;
+ Definition and specification of data structures and protocols for collaborative text editing (with Markdown highlighting support);
+ Implementation of a synchronization system and validation of its behavior with unit / integration tests; and
+ Design and implementation of a web text editor that makes use of the replication system.

### 1.1.1. Deliverables

On top of reports and standard bachelor thesis deliverables at HEIG-VD, the final deliverable will include at least the following items :

+ A web app offering simulatenous edition of notes with other browsers, with proper handling of disconnections and which allows for "offline" edition;
+ A web server which eases content sync between the participants; and
+ A detailed specification of the sync protocol and the implemented data structures.

### 1.1.2. Additional perspectives

The following topics may be investigated during the project :

+ Semantics of structured content edition
+ Managing user presence in a real-time system
+ Managing user identity and of the connection process

Please check out the extended description on [Craft Docs](https://www.craft.do/s/aIXV4QygroIsXF).

### 1.1.3. Potential partnerships

A positive prior notice has been given by Adobe (Basel). This will be confirmed later.
