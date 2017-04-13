<properties 
    pageTitle="Selezionare i nomi utente per Linux | Microsoft Azure" 
    description="Informazioni su come selezionare i nomi utente per una macchina virtuale Linux in Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



#<a name="selecting-user-names-for-linux-on-azure"></a>Selezionare i nomi utente per Linux su Azure#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Durante il provisioning di un computer virtuale Linux su Azure specificare il nome di un utente non radice che è possibile usare in un secondo momento di accedere a macchina virtuale. È possibile scegliere il nome del nuovo utente o se il provisioning tramite il portale classico Azure è possibile accettare il nome predefinito "azureuser".

Nella maggior parte dei casi l'utente non esiste in immagine di base e verrà creato durante il processo di provisioning. Se l'utente è presente nell'immagine macchine Virtuali di base, quindi l'agente di Azure Linux semplicemente configura password e/o SSH chiave per l'utente in base alle informazioni specificato quando si crea la macchina virtuale.

**Tuttavia**, Linux definisce un set di nomi utente che non deve essere utilizzato. Il processo di provisioning verrà **non riuscire** se si tenta di effettuare il provisioning di un VM Linux con un utente di sistema esistente, è definito come un utente con UID 0-99. Ad esempio il `root` utente che ha UID 0.

 - Vedere anche: [intervalli di Base Linux Standard - ID utente](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

Di seguito è un elenco di utenti di sistema incorporato comuni per CentOS e Ubuntu che è consigliabile evitare di utilizzare durante il provisioning di un computer virtuale Linux in Azure. In questo elenco è solo un esempio, consultare la documentazione per la distribuzione per assicurarsi che il nome utente che scelto non sono in conflitto con un utente esistente di sistema.


## <a name="centos"></a>CentOS
- abrt
- adm
- audio
- collocazione
- CD-ROM
- cgred
- daemon
- dbus
- chiamate in uscita
- DIP
- disco rigido
- disco floppy
- FTP
- FTP
- giochi
- Gopher
- haldaemon
- interrompere
- kmem
- blocco
- LP
- posta elettronica
- uomo
- memoria
- nfsnobody
- Nessuno
- NTP
- operatore
- oprofile
- postdrop
- suffisso
- qpidd
- radice
- RPC
- rpcuser
- saslauth
- chiusura
- slocate
- sshd
- stapdev
- stapusr
- sincronizzazione
- sistema
- nastro
- test
- tcpdump
- TTY
- utenti
- utempter
- utmp
- UUCP
- vcsa
- video
- Ruota


## <a name="ubuntu"></a>Ubuntu
- adm
- amministratore
- audio
- copia di backup
- collocazione
- CD-ROM
- crontab
- daemon
- chiamate in uscita
- DIP
- disco rigido
- fax
- disco floppy
- associare il
- giochi
- gnats
- IRC
- kmem
- orientamento orizzontale
- libuuid
- elenco
- LP
- posta elettronica
- uomo
- MessageBus
- mlocate
- netdev
- essere sempre informati
- Nessuno
- nogroup
- operatore
- plugdev
- proxy
- radice
- SASL
- ombreggiatura
- src
- SSH
- sshd
- personale
- Sudo
- sincronizzazione
- sistema
- Registro di sistema
- nastro
- TTY
- utenti
- utmp
- UUCP
- video
- segreteria
- whoopsie
- dati www

 