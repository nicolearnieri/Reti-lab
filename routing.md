Eseguendo il comando route -n vedremo che nella tabella di routing ci sono solo le rotte point to point, per ogni macchina F ed R



PUNTO 1: FIREWALL F1 

Il firewall F1 deve comunicre con tutte le reti nella topologia: mancano le rotte verso CD4, CD5 (no area), Area DMZ e Area RED

F1: 
    eth0 : 10.0.7.14
    eth1 : 10.0.6.1
    eth2 : 10.0.7.1

(Per il resto degli IP controlla il foglio a penna o la topologia lol) 

Per aggiungere delle rotte eseguiamo il comando: 
        route add -net <network> netmask <mask> gw <gateway> dev <device>
Però nota bene: se lo esegui da terminale al riavvio della macchina dovrai eseguire di nuovo tutto, quindi conviene scrivere le rotte nel file /etc/network/interfaces usando il post-up prima, quindi:
        post-up route add -net <network> netmask <mask> gw <gateway> dev <device>


            Nel file  /etc/network/interfaces di F1:

#Rotta di default verso internet (tap):
post-up route add default gw 10.0.7.13 dev eth0

#####################################################################################################################################
errore: io avevo scritto così
post-up route add -net 10.0.7.12 netmask 255.255.255.252 gateway 10.0.7.13 dev eth0
Da chatGPT: 
Stai aggiungendo una rotta specifica per la rete 10.0.7.12/30 (determinata dal netmask 255.255.255.252). I pacchetti destinati a questa rete verranno inviati al gateway 10.0.7.13 attraverso l’interfaccia eth0.

Nell'altro comando: 
post-up route add default gw 10.0.7.13 dev eth0
Stai impostando una rotta di default. Questo significa che qualsiasi pacchetto destinato a una rete per la quale non esiste una rotta specifica nella tabella di routing verrà inviato al gateway 10.0.7.13 attraverso l’interfaccia eth0. Questo è spesso chiamato “gateway di default” ed è tipicamente la rotta verso Internet.
#####################################################################################################################################

#Rotta verso CD4
post-up route add -net 10.0.7.4 netmask 255.255.255.252 gw 10.0.7.2 dev eth2
#Rotta verso CD5
post-up route add -net 10.0.7.8/30 gw 10.0.7.2 dev eth2

#nota : 10.0.7.2 sarebbe l'ip non segnato come network del router successivo stesso ma l'ip del device eth3 di F2 (?)


#Rotta verso Area RED
post-up route add -net 10.0.0.0 netmask 255.255.252.0 gw 10.0.7.2 dev eth2
## post-up route add -net 10.0.0.0/22 gw 10.0.7.2 dev eth2

#Rotta verso Area DMZ
post-up route add -net 10.0.4.0 netmask 255.255.254.0 gw 10.0.7.2 dev eth2


_______________________ fine file 

Eseguire il comando : 
    service networking restart 
Per verificare le nuove rotte, dopo aver fatto ciò possiamo ri-eseguire anche route -n 






PUNTO 2: FIREWALL F2 


Anche il firewall F2 deve comunicare con tutte le reti della topologia: mancano le rotte verso internet, Area GREEN, Area DMZ, area RED e CD2

F2: 
    eth0 : 10.0.7.6
    eth1 : 10.0.7.9

Nota: F2 comunica direttamente con CD4 e CD5

Nel file /etc/network/interfaces aggiungiamo le rotte:


                    Nel file  /etc/network/interfaces di F2:

#Rotta di default verso internet, verso GREEN e verso DMZ, verso CD2
post-up route add default gw 10.0.7.5 dev eth0

####################################################################################################################
oppure :

#verso DMZ 
post-up route add -net 10.0.4.0/23 gw 10.0.7.5 dev eth0

#Verso GREEN 
post-up route add -net 10.0.6.0 netmask 255.255.255.0 gw 10.0.7.5 dev eth0

######################################################################################################################

#Verso l'area RED
post-up route add -net 10.0.0.0/22 gw 10.0.7.10 dev eth1



_______________________ fine file 

Eseguire il comando : 
    service networking restart 
Per verificare le nuove rotte, dopo aver fatto ciò possiamo ri-eseguire anche route -n 









PUNTO 3: ROUTER R1
Le rotte mancanti nel router R1 sono quelle verso l'area GREEN, l'area RED e CD5

R1:
    eth0 : 10.0.7.5
    eth1 : 10.0.4.1
    eth2 : 10.0.5.1
    eth3 : 10.0.7.2


Nel file /etc/network/interfaces aggiungiamo le rotte:

#Rotta di default verso internet
post-up route add default gw 10.0.7.1 dev eth3

#Rotta verso l'area GREEN
post-up route add -net 10.0.6.0/24 gw 10.0.7.1 dev eth3

## internet e green potevano andare insieme 

#Rotta verso l'area RED 
post-up route add -net 10.0.0.0/22 gw 10.0.7.6 dev eth0

#Rotta verso CD5
post-up route add -net 10.0.7.8/30 gw 10.0.7.6 dev eth0

_______________________ fine file 

Eseguire il comando : 
    service networking restart 
Per verificare le nuove rotte, dopo aver fatto ciò possiamo ri-eseguire anche route -n 





PUNTO 4: ROUTER R2
Mancano le rotte verso l'area GREEN, l'area DMZ, CD2 e CD4

R2:
    eth0 : 10.0.7.10
    eth1 : 10.0.2.1
    eth2 : 10.0.0.1

Nel file /etc/network/interfaces aggiungiamo le rotte:

#Rotta di default, per tutti i punti insieme:
post-up route add default gw 10.0.7.9 dev eth0

_______________________ fine file 

Eseguire il comando : 
    service networking restart 
Per verificare le nuove rotte, dopo aver fatto ciò possiamo ri-eseguire anche route -n 



