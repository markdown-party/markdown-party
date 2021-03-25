# Architecture

## Log d'opérations distribué

Le log utilisé par `markdown-party` possède les propriétés suivantes :

- chaque opération est associée à un identifiant unique global. Cet identifiant est une hybrid logical clock [[7]](#bib_kulkarni_logical_clock) qui possède, comme une estampille de Lamport [[8]](#bib_lamport_time_clocks), un identifiant de site et (au moins) un numéro de séquence.
- les opérations sont stockées dans une queue, triée suivant l'ordre total défini par les identifiants. Comme les identifiants sont basés sur une horloge logique hybride, il est ainsi possible de filtrer les opérations ayant eu lieu _avant_ un certain timestamp physique.
- il est possible d'insérer des évènements à des emplacements arbitraires du log. Néanmoins, la plupart des insertions devraient avoir lieu aux extrémités (notamment car les sites auront tendance à converger et produire des évènements ayant eu lieu "après" les derniers évènements des autres sites); il est donc possible d'utiliser des structures de données optimisées pour ce cas d'utilisation (telles qu'un gap buffer).
- un site ne peut insérer une opération que si celle-ci a un identifiant constitué de son identifiant de site, et d'un numéro de séquence unique et postérieur à toutes les opérations déjà contenues dans le log. Il n'est (en général) pas possible de supprimer les opérations du log.

**Cette structure de données est un CvRDT.** En effet, il est possible de définir l'opération `Merge` suivante :

```text
# On considère que le log est implémenté naïvement, sous la forme d'une liste triée par l'identifiant (site, estampille).
# Log = List<(Identifier, Operation)>

FONCTION Merge (a, b)
    c := Nouveau Log
    TANT QUE a != [] OU b != []
        SI a == [] ALORS
            (head, tail) := b
            c += head
            b := tail
        
        SINON SI b == [] ALORS
            (head, tail) := a
            c += head
            a := tail
        
        SINON SI a[0] > b[0] ALORS
            (head, tail) := b
            c += head
            b := tail
        
        SINON SI a[0] < b[0] ALORS
            (head, tail) := a
            c += head
            a := tail

        # Note : on sait que a[0] == b[0]
        SINON
            (head, tailA) := a
            (    , tailB) := b
            c += head
            a := tailA
            b := tailB
        FIN SI                           
    FIN TANT QUE
    RETOUR C
FIN FONCTION
```

Cette fonction `Merge` est idempotente, commutative (sous condition qu'aucun identifiant ne peut être associé à deux opérations différentes), et associative. On peut donc s'en servir pour répliquer les opérations. Malheureusement, cette version naïve de la réplication souffre d'un défaut majeur : il est nécessaire de copier l'intégralité de la structure avant de faire un `Merge`. L'approche proposée consiste donc à implémenter un protocole de réplication qui permette d'optimiser le transfert des données entre les sites.

### Protocole de réplication

#### Messages

Un certain nombre de messages est défini pour permettre la réplication d'opérations.

**Sender**
**Receiver**

#### Machines d'état finies

##### Aggrégation / projection

- `Preparing`
    + `advertised : List<SiteIdentifier>`
- `Listening`
    + `advertised: List<SiteIdentifier>`
    + `log: MutableLog<Op>`
- `Cancelling`
- `Preparing`

| État actuel | Conditions       | Message                      | Nouvel état                                  |
|-------------|------------------|------------------------------|----------------------------------------------|
| Preparing   |                  | <- Done                      | Cancelling                                   |
| Preparing   |                  | <- Advertisement(site)       | Preparing [advertised += site]               |
| Preparing   |                  | <- Ready                     | Listening [advertised = advertised]          |
| Listening   | advertised != [] | -> Request(advertised.first) | Listening [advertised -= advertised.first ]  |
| Listening   |                  | <- Done                      | Cancelling                                   |
| Listening   |                  | <- Advertisement(site)       | Listening [advertised += site]               |
| Listening   |                  | <- Event(data)               | Listening [log += data]; emit(aggrgate(log)) |
| Cancelling  |                  | -> Done                      | Completed                                    |
| Completed   |                  |                              | FIN                                          |

##### Émission

##### Réception (mémoire)
##### Envoi (mémoire)

## Opérations d'édition du Markdown

### Principes
### Aggrégation et optimisations

## Bibliographie

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
ScienceBlogs, 2009-02-18 https://scienceblogs.com/goodmath/2009/02/18/gap-buffers-or-why-bother-with-1 Accédé le 11.03.2021
