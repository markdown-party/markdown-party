> :books: Looking for the weekly reports ? [You can find them here](WeeklyReports) :books:

# Markdown Party

Welcome to the **Markdown Party** GitHub repository ! **Markdown Party** is the title of the product I'm developing for my Bachelor Thesis at HEIG-VD; it's a web-based collaborative editor available [online](https://markdown.party).

This repository acts as a centralized entry point to the different reports, repositories and resources that I'll use or create for this project.

## Project description

This projects aims at specifying and implementing a collaborative Markdown-based note taking app for the web. The idea is directly inspired by [Overleaf](https://www.overleaf.com), which offers collaborative editing of LaTeX documents. The app will let multiple users collaborate simulatenously on the same document, and let clients temporarily disconnect. The proposed approach is split as follows :

+ High-level definition of functional and non-functional requirements of the app, with a mockup;
+ Prior art analysis on distributed data structures and operational transform for simulatenous collaborative text editing;
+ Definition and specification of data structures and protocols for collaborative text editing (with Markdown highlighting support);
+ Implementation of a synchronization system and validation of its behavior with unit / integration tests; and
+ Design and implementation of a web text editor that makes use of the replication system.

### Deliverables

On top of reports and standard bachelor thesis deliverables at HEIG-VD, the final deliverable will include at least the following items :

+ A web app offering simulatenous edition of notes with other browsers, with proper handling of disconnections and which allows for "offline" edition;
+ A web server which eases content sync between the participants; and
+ A detailed specification of the sync protocol and the implemented data structures.

### Additional perspectives

The following topics may be investigated during the project :

+ Semantics of structured content edition
+ Managing user presence in a real-time system
+ Managing user identity and of the connection process

Please check out the extended description on [Craft Docs](https://www.craft.do/s/aIXV4QygroIsXF).

### Potential partnerships

A positive prior notice has been given by Adobe (Basel). This will be confirmed later.

## Source code

You can check out the source code in the following places :

- [kotlin-echo](https://github.com/markdown-party/kotlin-echo), where a reference implementation of the log replication protocol and data structure is maintained;
- [kotlin-backend](https://github.com/markdown-party/kotlin-backend), where a collaborative Markdown synchronization server is developed; and
- [elm-frontend](https://github.com/markdown-party/elm-frontend), where a SPA frontend is developed.
