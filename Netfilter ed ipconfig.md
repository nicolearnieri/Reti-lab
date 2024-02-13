Netfilter è un framework all'interno del kernel Linux che consente la manipolazione dei pacchetti di rete. È il componente di base per molte operazioni di rete, come il routing, la traduzione degli indirizzi di rete (NAT), il filtraggio dei pacchetti e la gestione del traffico.

Netfilter fornisce un insieme di ganci all'interno del kernel che possono essere utilizzati per intercettare e manipolare i pacchetti di rete mentre attraversano la pila di rete. Questi ganci possono essere utilizzati per implementare vari tipi di funzionalità di rete a livello di kernel.

IPTables è uno degli strumenti più comuni che utilizza il framework Netfilter per fornire un firewall a livello di sistema e capacità di NAT. Con IPTables, gli amministratori di sistema possono definire regole per decidere quali pacchetti dovrebbero essere accettati, rifiutati o reindirizzati.

In sintesi, Netfilter è un potente strumento per la manipolazione dei pacchetti di rete a livello di kernel in un sistema Linux, e IPTables è uno strumento che sfrutta questo framework per fornire un firewall e altre funzionalità di rete.





Netfilter, e di conseguenza IPTables, opera su diverse tabelle, ognuna delle quali ha uno scopo specifico. Ogni tabella contiene una serie di "catene" e ogni catena è una lista di "regole". Le tabelle principali sono:

1. **Tabella Filter**: Questa è la tabella predefinita e la più utilizzata. Consente il filtraggio dei pacchetti.
    Contiene le catene INPUT (per i pacchetti destinati direttamente al sistema), OUTPUT (per i pacchetti generati dal sistema) e FORWARD (per i pacchetti che passano attraverso il sistema).

2. **Tabella NAT**: Questa tabella è utilizzata per la traduzione degli indirizzi di rete (NAT). 
    Contiene le catene PREROUTING (per alterare i pacchetti appena arrivano), OUTPUT (per alterare i pacchetti generati localmente prima del routing) e POSTROUTING (per alterare i pacchetti mentre stanno lasciando il sistema).

3. **Tabella Mangle**: Questa tabella è utilizzata per la manipolazione speciale dei pacchetti. Può essere utilizzata per alterare vari campi dei pacchetti IP come i bit TOS (Type of Service) e TTL (Time to Live). 
    Contiene le catene PREROUTING (per alterare i pacchetti appena arrivano), OUTPUT (per alterare i pacchetti generati localmente prima del routing), INPUT (per i pacchetti destinati direttamente al sistema), FORWARD (per i pacchetti che passano attraverso il sistema) e POSTROUTING (per alterare i pacchetti mentre stanno lasciando il sistema). 
    HA TUTTE LE CATENE.

4. **Tabella Raw**: Questa tabella è utilizzata principalmente per configurare le eccezioni al tracking dei connessioni. 
Consente di lavorare con i pacchetti prima che il kernel decida di tracciare o non tracciare una connessione; si possono esonerare dei pacchetti dal monitoraggio dello stato. 
    Contiene le catene PREROUTING (per i pacchetti appena arrivano), OUTPUT (per i pacchetti generati localmente prima del routing) e POSTROUTING (per i pacchetti mentre stanno lasciando il sistema).

Ogni regola in una catena viene esaminata in ordine. Se un pacchetto soddisfa i criteri di una regola, viene eseguita l'azione associata a quella regola (ad esempio, accettare, rifiutare o reindirizzare il pacchetto). Se non viene trovata alcuna regola corrispondente, viene eseguita l'azione predefinita per quella catena.