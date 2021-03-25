# Architecture

In this document, I aim to provide an overview of the architecture of the architecture and design decisions behind one of my Bachelor's thesis subprojects, [kotlin-echo](https://github.com/markdown-party/kotlin-echo). This library helps manage a log of operations, and provides some easy-to-use abstractions to handle operation replication across multiple peers. It also offers a Kotlin-friendly API to access the distributed data, with first-class support of concurrency through [coroutines](https://github.com/Kotlin/kotlinx.coroutines). It is heavily inspired by the notion of Local-First software [[2]](#bib_kleppmann_local_first).

## Motivation

As part of my Bachelor's thesis, I'm implementing a web-based collaborative Markdown editor. This editor aims at providing the following features :

+ Multiple users may work on a shared document simultaneously. They could see each other's edits in real time.
+ Users may disconnect intermittently and still be able to work offline on the document.

Therefore, the system should offer some [strong eventual consistency](https://en.wikipedia.org/wiki/Eventual_consistency) guarantees. This means that all the peers should converge to the same state, assuming they receive all the updates from the other peers. In the industry, we can find two main paradigms which aim at solving this problem :

+ Operational Transformation (abbreviated as **OT**) [[3]](#bib_ellis_gibbs_ot); and
+ Conflict-Free Replicated Data Types (abbreviated as **CRDTs**) [[5]](#bib_shapiro_comprehensive_crdt).

Operational Transformation is notoriously difficult [[4]](#bib_gentle_i_was_wrong); it is generally implemented as a centralized system. On the other hand, CRDTs tend to be looked down upon because of their metadata growth or, sometimes, their lack of proper intent preservation [[10]](#bib_herron_ot_vs_crdt). Nevertheless, because CRDTs tend to be easier to implement in a distributed fashion, they seem like a reasonable approach to consider when building a collaborative application.

But what about `kotlin-echo` then ? This library is designed as set of primitives to build and implement CRDTs. It tries to separate the concerns of **state replication** from the concepts of **operation aggregation**.

### Glossary

Here is a (non-exhaustive) list of some terminology I will be using throughout the document, and what these terms mean :

+ _User_ : An individual who will be using the system. They may work on multiple machines, and consist of one or multiple _peers_.
+ _Peer_, _site_ : [Peers](https://en.wikipedia.org/wiki/Peer-to-peer) are (generally) _equally priviledged, equipotent participants in the application._ They form a _network of nodes_, which may (or may not) be a [complete graph](https://en.wikipedia.org/wiki/Complete_graph). Peers may also participate in asymmetrical communication with other peers, such as in a primary-replica scheme.
+ _Peer identifier_ : A piece of data which gives each _peer_ a unique, global identity. Two peers with equal identifiers are the same peer, and two peers with non-equal identifiers are different entities.
+ _Logical clock (abbrev. LC)_, _Lamport timestamp_ : A tuple consisting of a _peer identifier_ and a _sequence number_ that's unique for this peer. [[8]](#bib_lamport_time_clocks).
+ _Hybrid logical clock (abbrev. HLC)_ : A combination of a physical clock and a sequence number. [[7]](#bib_kulkarni_logical_clock).
    - An important characteristic of a HLC is that, for operations, the relationship _e happens before f_ (on the same site) implies `hlc(e) < hlc(f)`. Additionally, HLCs keep track of the physical time of the sites, allowing for some optimizations.
+ _Operation_ : A domain-specific action that a user undertakes in the collaborative app. It has an intent (the effect that the user expected to see applied). The complexity of the operations is really dependent on the domain.
    - In a distributed counter, candidate operations might be **incrementation** and **decrementation**.
    - In a distributed set, candiate operations may be **addition** or **removal**.
    - In a distributed text editor, candidate operations may be **insertion**, **removal**, and **highlighting**.
+ _Aggregation_ : A domain-specific way to transform a sequence of operations into a state.

### Literature review and state of the art

#### Operational Transformation

This technique is usually centralized, and particularly cumbersome and difficult to implement [[4]](#bib_gentle_i_was_wrong). Usually, one has to implment a centralized operation log; eacch site will then send operations to the central server. Whenever a new operation is received, the server checks what the "expected" result was, and transforms the operation with the current log contents. The transformed operation is then sent to all the other sites, so it can be applied properly.

#### Causality tracking with clocks

- TODO

#### CmRDTs and CvRDTs

Conflict-Free Replicated Data Types can generally be separated in two distinct groups : Commutative Replicated Data Types [CmRDTs] and Convergent Replicated Data Types (CvRDTs) [[5]](#bib_shapiro_comprehensive_crdt). These variants work slightly differently from each other :

+ CmRDTs use operations to broadcast state updates. These operations can be applied in a **commutative** fashion, but not necessarily **idempotently**. Therefore, the broadcasting network mut make sure that operations are distributed to all the other sites exactly once.
+ CvRDTs are based on _semi-lattices_. A _semi-lattive_ is a partial order with an upper join for any finite and non-emtpy subset of elements [[6]](#bib_davey_lattice). Generally, CvRDTs also have an additional lower join. CvRDTs converge by replicating the whole state of the CvRDT, broadcasting it, and combining states on each site with the semi-lattice properties. Because this process is idempotent, commutative and associative, the CvRDT states will eventually converge to a common value.

#### Existing libraries and frameworks

+ Kotlin libraries on [(GitHub)](https://github.com/search?l=Kotlin&q=crdt&type=Repositories)
    - [concordant/c-crdtlib](https://github.com/concordant/c-crdtlib) : a recently released library by [concordant.io](https://concordant.io), a company co-founded by Marc Shapiro. It offers some implementations of delta-CRDTs, with Kotlin-multiplatform support. It supports a [limited subset](https://github.com/concordant/c-crdtlib/blob/c4e0c9eef1e969dd9225d68f39140bc50ede4af6/src/commonMain/kotlin/crdtlib/crdt/DeltaCRDTFactory.kt#L37-L79) of CRDTs through a [factory design pattern](https://github.com/concordant/c-client/blob/b02fd8d23af54362d79a360db0803c02b85b998e/src/commonMain/kotlin/client/Collection.kt#L79-L91). Therefore, it's not possible for library users to integrate or design their own CRDTs.
    - [alexandrepiveteau/distributed-kotlin](https://github.com/alexandrepiveteau/distributed-kotlin) : a library that implements 3 unoptimized CvRDTs, a WOOT [[12]](#bib_oster_woot) implementation, and a causal graph [[1]](#bib_grishchenko_deep_hypertext) implementation. This library does not offer any convenient API to replicate content over multiple sites.
    - [jackqack/kotlin-crdt](https://github.com/jackqack/kotlin-crdt) : an implementation of some CvRDTs and some CmRDTs, with basic synchronization ([limited to an ORSet implementation ?](https://github.com/jackqack/kotlin-crdt/blob/master/src/main/kotlin/node/CrdtNode.kt)).
    - [halfbreak/crdts](https://github.com/halfbreak/crdts) : Basic GCounter, PNCounter and GSet implementations with a basic protobuf definition.
    - [thealmikey/Lww-Dict-Crdt-Kotlin-Challenge](https://github.com/thealmikey/Lww-Dict-Crdt-Kotlin-Challenge) : a last-write-wins map-like structure.
    - [bind-disney/akka-distributed-data-examples](https://github.com/bind-disney/akka-distributed-data-examples) : examples using Akka's distributed data library, with usages of [AbstractReplicatedData](https://doc.akka.io/japi/akka/current/akka/cluster/ddata/AbstractReplicatedData.html) as a primitive to build a [Two-Phase Set](https://github.com/bind-disney/akka-distributed-data-examples/blob/master/src/com/itransition/spb/crdt/custom/TwoPhaseSet.kt).
    - [dannwebster/krdt](https://github.com/dannwebster/krdt) A collection of counters and sets implementations.


## `kotlin-echo` architecture

### Principles

`kotlin-echo` makes use of CRDT-inspired data structures, to implement the replication of Markdown text as follows :

- A CRDT is used to replicate a generic operation log. This log acts as the source of truth of operations performed on different sites by the users, and keeps track of order / causality of operations across sites. A small replication protocol syncs this log across sites efficiently.
- A set of Markdown-specific operations is defined. These operations are then replicated via the operation log, and aggregated as text. Because the replication process is clearly separated from the Markdown-specific operations, the definition of the latter is really flexible.

### Protocols

### Various optimizations

#### Physical-time based window

#### Flexible projections

#### Shortening the application log

### Designing a Kotlin-friendly API

#### Using the library (taken from the `kotlin-echo` [`README.md`](https://github.com/markdown-party/kotlin-echo))

Let's implement a distributed counter, which lets sites increment and decrement a shared value. We start by defining the events, as well as a `OneWayProjection` that aggregates them :

```kotlin
enum class Event { Increment, Decrement }

val counter = OneWayProjection<Int, Event> { op, acc ->
    when (op) {
        Increment -> acc + 1
        Decrement -> acc - 1
    }
}
```

We can then create a new site, and yield some new events :

```kotlin
val site = mutableSite<Event>(SiteIdentifier.random())

// This is a suspend fun.
site.event {
    yield(Increment)
}
```

It's then possible to observe the values of a site as a cold `Flow` :

```kotlin
val total: Flow<Int> = site.projection(initial = 0, counter) // emits [0, 1, ...]
```

As new events get `yield` in the `MutableSite`, the cold `Flow` will emit some additional elements
which contain the distributed counter total.

At some point, you may be interested in syncing multiple sites together. This can be done with a suspending actor pattern, which will not resume until both sites cooperatively finish :

```kotlin
suspend fun myFun() {
    val alice = mutableSite<Event>(SiteIdentifier.random())
    val bob = mutableSite<Event>(SiteIdentifier.random())

    sync(alice, bob)
}
```

Additional examples are available in the [demo folder](https://github.com/markdown-party/kotlin-echo/tree/main/src/test/kotlin/markdown/echo/demo).

## Future developments

## Bibliography

<a id="bib_grishchenko_deep_hypertext">[1]</a>
Victor Grishchenko
Deep hypertext with embedded revision control implemented in regular expressions
Proceedings of the 6th International Symposium on Wikis and Open Collaboration, 2010

<a id="bib_kleppmann_local_first">[2]</a>
Martin Kleppmann, Adam Wiggins, Peter van Hardenberg, and Mark McGranaghan
Local-first software: you own your data, in spite of the cloud. 
2019 ACM SIGPLAN International Symposium on New Ideas, New Paradigms, and Reflections on Programming and Software (Onward!), October 2019, pages 154–178. doi:10.1145/3359591.3359737

<a id="bib_ellis_gibbs_ot">[3]</a>
C.A. Ellis, S.J. Gibbs
Concurrency control in groupware systems
SIGMOD '89: Proceedings of the 1989 ACM SIGMOD international conference on Management of data, June 1989, pages 399–407, doi:10.1145/67544.66963

<a id="bib_gentle_i_was_wrong">[4]</a>
Joseph Gentle
I was wrong. CRDTs are the future
https://josephg.com/blog/crdts-are-the-future/

<a id="bib_shapiro_comprehensive_crdt">[5]</a>
Marc Shapiro, Nuno Pregui ̧ca, Carlos Baquero, Marek Zawirski
A comprehensive study of Convergent and Commutative Replicated Data Types
[Research Report] RR-7506, Inria – Centre Paris-Rocquencourt; INRIA. 2011, pp.50

<a id="bib_davey_lattice">[6]</a>
Davey, B. A.; Priestley, H. A. (2002)
Introduction to Lattices and Order (second ed.)
Cambridge University Press. ISBN 0-521-78451-4

<a id="bib_kulkarni_logical_clock">[7]</a>
Sandeep Kulkarni, Murat Demirbas, Deepak Madeppa, Bharadwaj Avva, and Marcelo Leone
Logical Physical Clocks and Consistent Snapshots in Globally Distributed Databases
Aguilera M.K., Querzoni L., Shapiro M. (eds) Principles of Distributed Systems. OPODIS 2014. Lecture Notes in Computer Science, vol 8878. Springer, Cham. doi:10.1007/978-3-319-14472-6_2

<a id="bib_lamport_time_clocks">[8]</a>
Leslie Lamport
Time, Clocks and the Ordering of Events in a Distributed System
Communications of the ACM 21, 7 (July 1978), 558-565. Reprinted in several collections, including Distributed Computing: Concepts and Implementations, McEntire et al., ed. IEEE Press, 1984. | July 1978, pp. 558-565

<a id="bib_chu_carroll_gap_buffer">[9]</a>
Mark C. Chu-Carroll
"Gap Buffers, or, Don’t Get Tied Up With Ropes?"
ScienceBlogs, 2009-02-18 https://scienceblogs.com/goodmath/2009/02/18/gap-buffers-or-why-bother-with-1 Accessed on 11.03.2021

<a id="bib_herron_ot_vs_crdt">[10]</a>
Andrew Herron
To OT or CRDT, that is the question
https://www.tiny.cloud/blog/real-time-collaboration-ot-vs-crdt/

<a id="bib_sypytkowski_state_based">[11]</a>
Bartosz Sypytkowski
An introduction to state-based CRDTs (10 articles, listed in the introduction of the first link)
https://bartoszsypytkowski.com/the-state-of-a-state-based-crdts/ Accessed on 11.03.2021

<a id="bib_oster_woot">[12]</a>
Gérald Oster, Pascal Urso, Pascal Molli, Abdessamad Imine
Real time group editors without Operational transformation
[Research Report] RR-5580, INRIA. 2005, pp.24. inria-00071240
