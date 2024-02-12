Per settare i PC:
in edit config inserire una riga così creata:
                                ip numeroIpv4 numeroMaschera numeroGateway
quindi per esempio:
ip 10.0.0.2 255.255.255.0 10.0.0.1 
dove 10.0.0.2 è l'indirizzo ip del pc, 255.255.255.0 è la mask e 10.0.0.1 è il gateway, cioè l'hop successivo (il router)


Per settare i firewall invece, dobbiamo prima far sì che le loro interfacce siano configurate con gli indirizzi ip giusti, quindi, per ciascuna scheda di rete
dobbiamo configurare tutto correttamente. Per farlo, in /etc/network/interfaces, dobbiamo inserire le righe giuste:


modiificare /etc/network/interfaces : 

ESEMPIO PER FIREWALL F1:

#per eth0:
    auto eth0
    iface eth0 inet static  #questo comando dice che l'interfaccia è statica
    network 10.0.2.0
    netmask 255.255.255.252
    address 10.0.2.1 
    broadcast 10.0.2.3

#per eth1:
    auto eth1
    iface eth1 inet static
    network 10.0.0.0        #sarebbe l'ip del router verso cui siamo connessi con questa interfaccia
    netmask 255.255.255.0
    address 10.0.0.1        #indirizzo ip della scheda di rete stessa
    broadcast 10.0.0.255

#Aggiunta delle rotte verso 10.0.1.0/24 , cioè il router a cui non siamo connessi direttamente
post-up route add -net 10.0.1.0 netmask 255.255.255.0 gw 10.0.2.2 dev eth0  


ALTRO ESEMPIO PER FIREWALL F2:

#per eth0:
    auto eth0
    iface eth1 inet static 
    network 10.0.2.0/30
    netmask 255.255.255.252
    address 10.0.2.2
    broadcast 10.0.2.3

#per eth1:
auto eth1 
iface eth1 inet static 
network 10.0.1.0
netmask 255.255.255.0
address 10.0.1.1
broadcast 10.0.1.255

#aggiunta della rotta verso l'altro router
post-up route add -net  10.0.0.0 netmask 255.255.255.0 gw 10.0.2.0 dev eth0


Fatto questo, ricordarsi di avviare tutto facendo : 
                service networking restart
Poi attivare l'ip forwarding (cioè fare in modo che il firewall possa inoltrare i pacchetti) con il comando:
                sysctl -w net.ipv4.ip_forward=1
                