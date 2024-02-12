TAP: è un'interfaccia di rete virtuale che può essere creata all'interno del proprio dispositivo. Ciò ci servirà per far sì che i computerini in GNS3 possano comunicare con il nostro computer fisico e di conseguenza con internet.

Nel terminale DEL PC FISICO, eseguire i seguenti comandi:

tunctl -g netdev -t tap0 
#crea un'interfaccia di rete TAP virtuale chiamata tap0. tunctl sta per TUN/TAP control program. -g netdev indica che l'interfaccia di rete TAP sarà gestita dal gruppo netdev. -t tap0 indica che l'interfaccia di rete TAP si chiamerà tap0.

ifconfig tap0 10.0.7.13                     #assegna l'indirizzo IP
ifconfig tap0 netmask 255.255.255.252       #assegna la subnet mask, è punto a punto quindi come mask bastano 2 bit 
ifconfig tap0 broadcast 10.0.3.15           #assegna l'indirizzo di broadcast

ifconfig tap0 up                          #attiva l'interfaccia di rete TAP

per verificare che l'interfaccia di rete TAP sia stata creata correttamente, eseguire il comando:
        ifconfig tap0


Ora invece vanno create delle regole di firewalling per utilizzare l'interfaccia di rete TAP appena creata.

iptables -t nat -F 
## pulisce le regole di NAT, cioè elimina tutte le regole nella tabella NAT del modulo kernel ITABLES. Questo comando è utile per evitare che ci siano regole di NAT preesistenti che potrebbero interferire con le nuove regole di NAT che andremo a creare.

iptables -t nat -X 
## elimina tutte le catene personalizzate nella tabella NAT del modulo kernel ITABLES. Questo comando è utile per evitare che ci siano catene personalizzate preesistenti che potrebbero interferire con le nuove catene personalizzate che andremo a creare.

iptables -F 
#Pulisce tutte le regole nella tabella di filtro (filter) di iptables, che filtra il traffico di rete in base ad esempio a indirizzo ip, porta, protocollo, ecc.

iptables -X 
#Elimina tutte le catene personalizzate nella tabella di filtro (filter) di iptables.


iptables -t nat -A POSTROUTING -o enp0s25 -j MASQUERADE  
##enp0s25 è l'interfaccia di rete del PC fisico che è connessa a internet. Questo comando fa in modo che i pacchetti in uscita ##dall'interfaccia di rete TAP vengano mascherati con l'indirizzo IP dell'interfaccia di rete enp0s25. In questo modo i pacchetti in ##uscita dall'interfaccia di rete TAP sembreranno provenire dall'interfaccia di rete enp0s25.


iptables -A FORWARD -i tap0 -j ACCEPT   # abilita il forwarding dei pacchetti in entrata sull'interfaccia di rete TAP



Poi, eseguire il seguente comando per abilitare il forwarding dei pacchetti in entrata sull'interfaccia di rete TAP:
sysctl -w net.ipv4.ip_forwarding=1


route add -net 10.0.0.0/21 gw 10.0.7.14 dev tap0
#aggiunge una rotta di default per l'interfaccia di rete TAP. Questo comando è utile per far sì che i pacchetti in uscita dall'interfaccia di rete TAP siano instradati correttamente.

Andrebbe fatto uno script.sh per automatizzare il tutto, andrebbbe eseguito ogni volta che si riavvia il PC fisico altrimenti. 

CONFIGURAZIONE DEL FILE /etc/hosts
Per poter utilizzare i nomi di dominio invece degli indirizzi IP, è necessario configurare il file /etc/hosts.
Aprendo il file /etc/hosts con un editor di testo, aggiungere le seguenti righe:
F1:
10.0.7.2 R1   (sarebbe l'ip di eth3 di R1, che "punta" verso F1)
10.0.7.6 F2
10.0.7.10 R2

E così via successivamente su R1, F2, R2, ecc. 
