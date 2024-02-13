Configurare i firewall F1 ed F2 così da ottenere la seguente connettività tra le diverse aree:
- L'area GREEN può aprire nuove comunicazioni verso tutte le altre aree della rete e deve solo rispondere a comunicazioni precedentemente avviate da lei stessa
Tutte le altre aree non devono essere in grado di instaurare nuove comunicazioni direttamente con l'area GREEN
- L'area DMZ è abilitata a ricevere nuove comunicazioni in entrata da parte di INTERNET (e anche di GREEN la quale può counicare con tutti)
- L'area RED può ricevere nuove comunicazioni solo da parte dell'area DMZ
Bisogna abilitare l'accesso reomto SSH - porta 22 sul firewall F2
Bisogna abilitare l'area RED ad effettuare nuove richieste di ping (echo-request) verso l'area DMZ
- L'area DMZ può solo rispondere alle richieste di ping (echo-reply) ricevute dall'area RED

Natting
Abilitare il portforwarding della porta 80 sull'indirizzo 10.0.5.3
Abilitare il portforwarding della porta 4443 sull'indirizzo 10.0.4.2 traslando la porta sulla 443
Abilitare il portforwarding della porta 2525 sull'indirizzo 10.0.5.2


1. CONFIGURAZIONE DEL FIREWALL DI F1 
Creare un nuovo file di configurazione per il firewall F1, firewall.sh, e configurare le interfacce e le rotte statiche. 

In /etc/network/firewall.sh :

#Ripuliamo le regole e le catene del firewall:
iptables -F #Flush delle regole
iptables -X #Eliminazione delle catene


#Politica di default da impostare per le catene di default del firewall
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP
    # -P sta per policy

# Abilitiamo SSH sul firewall F1 e su F2 per poter accedere da remoto ai firewall:

#Per f1: 


iptables -A INPUT -p tcp --dport 22 -s 10.0.7.12/30 -i eth0 -j ACCEPT
## aggiunge una regola che accetta il traffico TCP sulla porta (destinato alla porta di F1) 22, che è la porta di default di SSH, proveniente dal network di CDTAP. Nota che CDTAP ci consente di collegare il nostro computer fisico al firewall F1 e F2.

iptables -A OUTPUT -p tcp --sport 22 -d 10.0.7.12/30 -o eth0 -j ACCEPT
## Aggiunge una regola che consente al traffico TCP di uscire tramite eth0, e quindi andare verso al CDTap, attraverso la porta 22, che è la porta di default di SSH. --sport 22 indica che il pacchetto è uscito dalla porta 22, cioè la porta di default di SSH.

######################################################################################################################################
## -A indica l'aggiunta di una regola, sta per Append quindi aggiunge una regola in fondo alla catena
## -p indica il protocollo, in questo caso tcp, perchè stiamo parlando di ssh che usa il protocollo tcp 

## --dport indica la porta di destinazione, in questo caso la porta 22, che è la porta di default di ssh
## --sport indica la porta sorgente, in questo caso la porta 22, che è la porta di default di ssh

## -s indica l'indirizzo sorgente, in questo caso l'indirizzo 10.0.7.12/30, che sarebbe l'indirizzo di CDTap
## -d indica l'indirizzo di destinazione, in questo caso l'indirizzo di CDTap

## -i indica l'interfaccia in ingresso, in questo caso eth0, rivolta verso CDTap 
## -o indica l'interfaccia in uscita, in questo caso eth0, rivolta verso CDTap

## -j indica l'azione da compiere, in questo caso ACCEPT, cioè accettare il pacchetto. Sta per Jump, si salta alle regole successive.
######################################################################################################################################


#Per f2:
iptables -A FORWARD -p tcp --dport 22  -i eth0 -o eth2 -d 10.0.7.6 -j ACCEPT
## Aggiunge una regola che consente al traffico TCP di passare da eth0 a eth2, e quindi andare verso F2 (destinazione 10.0.7.6), attraverso la porta 22, che è la porta di default di SSH. --dport 22 indica che il pacchetto è diretto alla porta 22, cioè la porta di default di SSH.

iptables -A FORWARD -p tcp --sport 22  -i eth2 -o eth0 -s 10.0.7.6 -m state --state ESTABLISHED,RELATED -j ACCEPT
## Aggiunge una regola che consente al traffico TCP di passare da eth2 a eth0, e quindi andare verso CDTap, attraverso la porta 22, che è la porta di default di SSH. --sport 22 indica che il pacchetto è uscito dalla porta 22, cioè la porta di default di SSH. -m state --state ESTABLISHED,RELATED indica che il pacchetto è una risposta ad una richiesta di connessione, quindi è una connessione stabilita.
## Quindi, si tratta di inoltrare verso il TAP il traffico ssh proveniente da F2.


##Creazione di sottocatene da agganciare a Forward.
iptables -N GreenToAll
iptables -N AllToGreen
iptables -N inetToDMZ
iptables -N DMZtoInet

iptables -A FORWARD -s 10.0.6.0/24 -i eth1 -j GreenToAll
## Aggiunge una regola che consente al traffico proveniente da 10.0.6.0/24 (cioè l'area GREEN), in ingresso su F1 tramite eth1 (interfaccia di rete rivolta verso Green) di essere inoltrato a GreenToAll. Questo significa che il traffico proveniente da GREEN può andare ovunque.

iptables -A FORWARD -d 10.0.6.0/24 -o eth1 -j AllToGreen
## Aggiunge una regola che consente al traffico diretto a Green di uscire da F1 tramite eth1 (interfaccia di rete rivolta verso Green) a AllToGreen. Questo significa che il traffico diretto a GREEN può provenire da ovunque.


iptables -A FORWARD -i eth0 -o eth2 (-s 10.0.7.12/30) -d 10.0.4.0/23 -j inetDMZ 
##La cosa tra parentesi non è stata scritta da Pacenza.
## Aggiunge una regola che consente al traffico proveniente da eth0 (interfaccia di rete rivolta verso CDTap) e diretto tramite eth2 verso DMZ (10.0.4.0/23) di essere inoltrato a inetToDMZ. Questo significa che il traffico proveniente da CDTap e diretto a DMZ passerà per inetToDMZ e attraverso F1.

iptables -A FORWARD -i eth2 -o eth0 -s 10.0.4.0/23 -j DMZtoInet
## Aggiunge una regola che consente al traffico proveniente da DMZ e diretto verso CDTap di uscire da F1 tramite eth0 (interfaccia di rete rivolta verso CDTap) a DMZtoInet. Questo significa che il traffico proveniente da DMZ e diretto a CDTap passerà per DMZtoInet e attraverso F1.

#Aggiungiamo le policy di accept alle sottocatene:

iptables -A greenToAll -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT

iptables -A AllToGreen -m state --state ESTABLISHED,RELATED -j ACCEPT

iptables -A inetToDMZ -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT

iptables -A DMZtoInet -m state --state ESTABLISHED,RELATED -j ACCEPT