Tutti i firewall e i router della topologia devono essere abilitati per consentire le connessioni di tipo ssh. Per fare ciò è necessario modificare il file di configurazione ssh di sshd, che si trova in /etc/ssh/sshd_config. 
Mettere permitRootLogin yes e PasswordAuthentication yes.

poi, riavviare il servizio sshd con il comando:
    service ssh restart 
oppure
    systemctl restart ssh
