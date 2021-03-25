# Architecture

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

