Netfilter è un framework all'interno del kernel Linux che consente la manipolazione dei pacchetti di rete. È il componente di base per molte operazioni di rete, come il routing, la traduzione degli indirizzi di rete (NAT), il filtraggio dei pacchetti e la gestione del traffico.

Netfilter fornisce un insieme di ganci all'interno del kernel che possono essere utilizzati per intercettare e manipolare i pacchetti di rete mentre attraversano la pila di rete. Questi ganci possono essere utilizzati per implementare vari tipi di funzionalità di rete a livello di kernel.

IPTables è uno degli strumenti più comuni che utilizza il framework Netfilter per fornire un firewall a livello di sistema e capacità di NAT. Con IPTables, gli amministratori di sistema possono definire regole per decidere quali pacchetti dovrebbero essere accettati, rifiutati o reindirizzati.

In sintesi, Netfilter è un potente strumento per la manipolazione dei pacchetti di rete a livello di kernel in un sistema Linux, e IPTables è uno strumento che sfrutta questo framework per fornire un firewall e altre funzionalità di rete.



TABELLE DI IP TABLES:

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




CATENE DI IPTABLES DI DEFAULT:

Ogni tabella ha un insieme di catene predefinite. Queste catene sono:
PREROUTING: Questa catena è utilizzata per i pacchetti appena arrivati sull'interfaccia di rete.





1. La catena INPUT viene utilizzata per il traffico diretto al sistema locale. Ad esempio, se un pacchetto arriva al tuo computer e deve essere destinato a un'applicazione in esecuzione su di esso, il pacchetto passerà attraverso la catena INPUT. Puoi definire regole in questa catena per consentire o bloccare specifici tipi di traffico in ingresso.

2. La catena OUTPUT viene utilizzata per il traffico generato dal sistema locale. Ad esempio, se il tuo computer invia una richiesta HTTP a un server remoto, il pacchetto passerà attraverso la catena OUTPUT. Puoi definire regole in questa catena per controllare quali tipi di traffico generato dal tuo sistema possono uscire.

3. La catena FORWARD viene utilizzata per il traffico che attraversa il tuo sistema. Ad esempio, se il tuo computer funge da router e deve instradare il traffico tra due reti, il pacchetto passerà attraverso la catena FORWARD. Puoi definire regole in questa catena per controllare il flusso del traffico tra le reti.

4. La catena PREROUTING viene utilizzata per il traffico che arriva al tuo sistema e deve essere modificato prima di essere instradato. Ad esempio, puoi utilizzare questa catena per eseguire il port forwarding, in cui i pacchetti destinati a una determinata porta vengono reindirizzati a un'altra macchina sulla rete.

5. La catena POSTROUTING viene utilizzata per il traffico che lascia il tuo sistema e deve essere modificato dopo essere stato instradato. Ad esempio, puoi utilizzare questa catena per eseguire la traduzione degli indirizzi di rete (NAT), in cui gli indirizzi IP e le porte dei pacchetti in uscita vengono modificati prima di lasciare il sistema.


Ogni catena ha una politica predefinita, che può essere "ACCEPT" (accetta il pacchetto), "DROP" (scarta il pacchetto) o "REJECT" (scarta il pacchetto e invia un messaggio di errore al mittente). Puoi anche aggiungere regole personalizzate a queste catene per definire comportamenti specifici per il tuo firewall.



Le catene INPUT, OUTPUT, PREROUTING e POSTROUTING in IPTables sono utilizzate per gestire diversi tipi di traffico di rete. Ecco le differenze tra queste catene:

1. **INPUT vs PREROUTING**: La catena INPUT gestisce i pacchetti che sono destinati direttamente al sistema, mentre la catena PREROUTING gestisce tutti i pacchetti che entrano nel sistema, indipendentemente dal fatto che siano destinati al sistema stesso o che debbano essere inoltrati ad un'altra destinazione. PREROUTING è la prima catena che un pacchetto incontra quando entra nel sistema, e può essere utilizzata per modificare i pacchetti prima che vengano instradati.

2. **OUTPUT vs POSTROUTING**: La catena OUTPUT gestisce i pacchetti generati dal sistema stesso, mentre la catena POSTROUTING gestisce tutti i pacchetti che lasciano il sistema, indipendentemente dal fatto che siano stati generati dal sistema stesso o che siano stati inoltrati attraverso di esso. POSTROUTING è l'ultima catena che un pacchetto incontra prima di lasciare il sistema, e può essere utilizzata per modificare i pacchetti dopo che sono stati instradati.

In sostanza, PREROUTING e POSTROUTING sono utilizzate per gestire i pacchetti prima e dopo il processo di routing, rispettivamente, mentre INPUT e OUTPUT sono utilizzate per gestire i pacchetti che sono destinati al sistema stesso o che sono generati dal sistema stesso.



Le catene FORWARD, PREROUTING e POSTROUTING in IPTables sono tutte coinvolte nel processo di gestione dei pacchetti di rete, ma sono utilizzate in momenti diversi e per scopi diversi. Ecco come si differenziano:

1. **FORWARD**: La catena FORWARD è utilizzata per gestire i pacchetti che sono destinati ad essere inoltrati da un'interfaccia di rete a un'altra sullo stesso sistema. In altre parole, se il tuo sistema sta agendo come un router o un gateway, i pacchetti che passano attraverso il sistema da una rete all'altra passeranno attraverso la catena FORWARD. Puoi utilizzare questa catena per definire regole su come questi pacchetti inoltrati dovrebbero essere gestiti.

2. **PREROUTING**: La catena PREROUTING è la prima catena che un pacchetto incontra quando entra nel sistema. Questa catena è utilizzata per gestire i pacchetti prima che vengano instradati. Puoi utilizzare questa catena per modificare i pacchetti in ingresso prima che vengano instradati al loro destino finale.

3. **POSTROUTING**: La catena POSTROUTING è l'ultima catena che un pacchetto incontra prima di lasciare il sistema. Questa catena è utilizzata per gestire i pacchetti dopo che sono stati instradati. Puoi utilizzare questa catena per modificare i pacchetti in uscita dopo che sono stati instradati al loro destino finale.

In sostanza, la catena FORWARD è utilizzata per gestire i pacchetti che sono in transito attraverso il sistema, mentre le catene PREROUTING e POSTROUTING sono utilizzate per gestire i pacchetti prima e dopo il processo di routing, rispettivamente.


Ogni catena ha una policy di default, che può essere "ACCEPT" (accetta il pacchetto), "DROP" (scarta il pacchetto) o "REJECT" (scarta il pacchetto e invia un messaggio di errore al mittente). Puoi anche aggiungere regole personalizzate a queste catene per definire comportamenti specifici per il tuo firewall.




REGOLE DI FILTRAGGIO DI IPTABLES:
Le regole di filtraggio in IPTables sono utilizzate per controllare come i pacchetti di rete vengono gestiti. Ogni regola è associata a una specifica catena (INPUT, OUTPUT, FORWARD, ecc.) e definisce un'azione da eseguire su un pacchetto che corrisponde a determinati criteri.

Una regola di filtraggio in IPTables è composta da una serie di componenti:

1. **Criteri di corrispondenza**: Questi definiscono le condizioni che un pacchetto deve soddisfare per corrispondere alla regola. I criteri possono includere aspetti come l'indirizzo IP di origine o di destinazione, la porta di origine o di destinazione, il protocollo (TCP, UDP, ICMP, ecc.), e così via.

2. **Azione (o "target")**: Questo definisce cosa fare con un pacchetto che corrisponde ai criteri di corrispondenza. Le azioni comuni includono ACCEPT (accetta il pacchetto), DROP (scarta il pacchetto senza inviare una risposta), REJECT (scarta il pacchetto e invia una risposta di errore al mittente), e LOG (registra il pacchetto).

Ecco un esempio di una regola di filtraggio in IPTables:

```bash
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

Questa regola aggiunge (-A) alla catena INPUT una regola che accetta (-j ACCEPT) tutti i pacchetti TCP (-p tcp) destinati alla porta 22 (--dport 22), che è la porta standard per SSH.

Le regole in IPTables vengono esaminate in ordine, dalla prima all'ultima, e la prima regola che corrisponde a un pacchetto determina l'azione che viene eseguita su di esso. Se nessuna regola corrisponde a un pacchetto, viene applicata l'azione predefinita per la catena, che può essere impostata con il comando `iptables -P`.




I TARGET:

I target in IPTables definiscono l'azione da eseguire quando un pacchetto corrisponde a una regola. Ecco i target più comuni:

1. **ACCEPT**: Questo target fa sì che il pacchetto venga accettato e prosegua il suo percorso attraverso le catene di regole.

2. **DROP**: Questo target scarta il pacchetto. Il pacchetto viene semplicemente eliminato e non viene inviata alcuna risposta al mittente.

3. **REJECT**: Questo target scarta il pacchetto, ma a differenza di DROP, invia una risposta al mittente per informarlo che il pacchetto è stato scartato.

4. **LOG**: Questo target registra il pacchetto. Le informazioni sul pacchetto vengono scritte nel log del sistema. Di solito, questo target viene utilizzato in combinazione con un altro target, come ACCEPT o DROP.

5. **RETURN**: Questo target termina l'elaborazione della catena corrente e restituisce il controllo alla catena chiamante. Se non c'è una catena chiamante, l'azione predefinita per la catena viene eseguita.

6. **QUEUE**: Questo target passa il pacchetto a una coda utente per l'elaborazione. Può essere utilizzato in combinazione con un programma utente che gestisce la coda per eseguire un'elaborazione più complessa o personalizzata dei pacchetti.

7. **DNAT (Destination NAT)**: Questo target è utilizzato per modificare l'indirizzo di destinazione di un pacchetto. È comunemente utilizzato in scenari di port forwarding, dove i pacchetti destinati a una certa porta su un sistema vengono reindirizzati a un altro sistema.

8. **SNAT (Source NAT)**: Questo target è utilizzato per modificare l'indirizzo sorgente di un pacchetto. È comunemente utilizzato in scenari in cui un sistema agisce come un gateway o un router, e i pacchetti che escono dal sistema devono apparire come se provenissero dal sistema stesso, piuttosto che dalla macchina originale.

9. **MASQUERADE**: Questo target è una forma speciale di SNAT che è utile quando l'indirizzo IP del sistema è dinamico, come in molti collegamenti a Internet domestici. MASQUERADE automaticamente sostituisce l'indirizzo IP sorgente del pacchetto con l'indirizzo IP corrente del sistema.

L'ordine delle regole in una catena è importante. Le regole vengono esaminate dall'alto verso il basso, e la prima regola che corrisponde a un pacchetto determina l'azione che viene eseguita su di esso. Se nessuna regola corrisponde a un pacchetto, viene eseguita l'azione predefinita per la catena.