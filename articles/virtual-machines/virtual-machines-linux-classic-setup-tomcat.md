<properties
    pageTitle="Configurare Tomcat Apache in una macchina virtuale Linux | Microsoft Azure"
    description="Informazioni su come configurare Apache Tomcat7 utilizzando una Azure macchine () che eseguono Linux."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="how-to-set-up-tomcat7-on-a-linux-virtual-machine-with-microsoft-azure"></a>Come configurare Tomcat7 su un computer virtuale Linux con Microsoft Azure

Apache Tomcat (o semplicemente Tomcat, in precedenza anche Tomcat Giacarta) è un server web Apri origine e un contenitore servlet sviluppato da Apache Software Foundation (ASF). Tomcat implementazione Servlet Java e le specifiche di Java Server Pages (JSP) da DOM Microsystems e offre un ambiente di server web HTTP Java puro in cui eseguire il codice Java. Nella configurazione più semplice Tomcat viene eseguito in un processo solo sistema operativo. Questo processo viene eseguito Java virtual machine (JVM). Ogni richiesta HTTP da un browser per Tomcat viene elaborato come un thread separato nel processo di Tomcat.  

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


In questa guida è verranno installati tomcat7 su un'immagine Linux e distribuirlo in Microsoft Azure.  

Si apprenderà:  

-   Informazioni su come creare una macchina virtuale in Azure.
-   Informazioni su come preparare la macchina virtuale per tomcat7.
-   Come installare tomcat7.

Si presuppone che il lettore abbia già un abbonamento a Azure.  Se non è possibile iscriversi per una versione di valutazione gratuita in [http://azure.microsoft.com](https://azure.microsoft.com/). Se si dispone di un abbonamento MSDN, vedere [prezzi speciali di Microsoft Azure: MSDN, MPN e vantaggi Bizspark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Per ulteriori informazioni su Azure, vedere [Novità Azure?](https://azure.microsoft.com/overview/what-is-azure/).

In questo argomento si presuppone familiarità base di tomcat e Linux.  

##<a name="phase-1-create-an-image"></a>Fase 1: Creare un'immagine
In questa fase, è necessario creare una macchina virtuale con un'immagine di Linux in Azure.  

###<a name="step-1-generate-an-ssh-authentication-key"></a>Passaggio 1: Generare una chiave di autenticazione SSH
SSH è uno strumento importante per gli amministratori di sistema. Tuttavia, la configurazione della protezione di access in base a una determinata umane password non è una buona norma. Utenti malintenzionati può essere suddivisa in sistema basato su un nome utente e una password semplice.

Fortuna è un modo per lasciare aperta accesso remoto e non da preoccuparsi password. Il metodo è costituito da autenticazione con crittografia asimmetrica. Chiave privata dell'utente è quello che concede l'autenticazione. È anche possibile bloccare l'account utente per impedire l'autenticazione tramite password completamente.

Un altro vantaggio di questo metodo è password diverse per accedere al server diversi non è necessario. È possibile autenticare utilizzando la chiave privata personale in tutti i server, che consente di evitare ricordare password diversi.

È inoltre possibile effettuare l'accesso con richiesta una password con questo metodo.

Seguire questa procedura per generare la chiave di autenticazione SSH.

1.  Scaricare e installare puttygen da uno dei seguenti percorsi: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.  Eseguire PUTTYGEN. EXE.
3.  Fare clic su **Genera** per generare le chiavi. Il processo è possibile aumentare casualità spostando il puntatore del mouse sull'area vuota nella finestra.  
![][1]
4.  Al termine del processo genera Puttygen.exe viene visualizzata la chiave generata. Per esempio:  
![][2]
5.  Selezionare e copiare la chiave pubblica nella **chiave** e salvarlo in un file denominato publicKey.pem. Non fare clic su **Salva chiave pubblica**, perché il formato di file della chiave pubblica salvato è la differenza tra la chiave pubblica che vogliamo.
6.  Fare clic su **Salva chiave privata** e salvarla in un file denominato privateKey.ppk.

###<a name="step-2-create-the-image-in-the-azure-portal"></a>Passaggio 2: Creare l'immagine nel portale di Azure.
Nel [portale di Azure](https://portal.azure.com/), fare clic su **Nuovo** nella barra delle attività per creare un'immagine, scegliere l'immagine di Linux in base alle proprie esigenze. Nell'esempio seguente viene utilizzata l'immagine Ubuntu 14.04.
![][3]

Per **Nome Host** specificare il nome per l'URL che si client Internet e verrà utilizzato per accedere a questa macchina virtuale. Definire l'ultima parte del nome del DNS, ad esempio tomcatdemo, e Azure genereranno l'URL come tomcatdemo.cloudapp.net.  

Per **Chiave di autenticazione SSH**, copiare il valore di chiave file **publicKey.pem** , che contiene la chiave pubblica generata da puttygen.  
![][4]

Configurare altre impostazioni in base alle esigenze e quindi fare clic su Crea.  

##<a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Fase 2: Preparare la macchina virtuale per Tomcat7
In questa fase, verrà configura un endpoint per il traffico tomcat e quindi connettersi alla nuova macchina virtuale.
###<a name="step-1-open-the-http-port-to-allow-web-access"></a>Passaggio 1: Aprire la porta HTTP per consentire l'accesso web
Endpoint di Azure è costituito da un protocollo TCP o UDP, insieme a una porta pubblica e privata. Porta privata è la porta che il servizio è in attesa nella macchina virtuale. La porta pubblica è la porta che il servizio cloud Azure è in attesa esternamente il traffico in arrivo, basati su Internet.  

Porte TCP 8080 sono il numero di porta predefinita su che controllano tomcat. Aprire questa porta con un endpoint Azure consentirà si e altri accesso client di Internet per tomcat pagine.  

1.  Nel portale di Azure, fare clic su **Sfoglia** -> **macchina virtuale**, quindi fare clic su macchina virtuale che è stato creato.  
![][5]
2.  Per aggiungere un endpoint alla macchina virtuale, fare clic sulla casella **i punti finali** .
![][6]
3.  Fare clic su **Aggiungi**.  
    1.  Per l' **endpoint**, digitare un nome per l'endpoint in Endpoint e quindi digitare 80 in **Porta pubblica**.  

        Se si imposta su 80, non necessario includere il numero di porta nell'URL che consente di accedere tomcat. Ad esempio http://tomcatdemo.cloudapp.net.    

        Se si imposta su un altro valore, ad esempio 81, è necessario aggiungere il numero di porta all'URL per accedere a tomcat. Ad esempio, http://tomcatdemo.cloudapp.net:81 /.
    2.  Digitare 8080 nella porta privato. Per impostazione predefinita, tomcat ascolto sulla porta TCP 8080. Se è stata modificata l'impostazione predefinita attesa porta del tomcat, è necessario aggiornare porta privata per essere che uguale il tomcat porta di ascolto.  
    ![][7]

4.  Fare clic su **OK** per aggiungere l'endpoint alla macchina virtuale.



###<a name="step-2-connect-to-the-image-you-created"></a>Passaggio 2: Connettersi all'immagine che è stato creato.
È possibile scegliere qualsiasi strumento SSH per connettere la macchina virtuale. In questo esempio viene utilizzata Putty.  

Prima di tutto, ottenere il nome DNS del computer virtuale dal portale di Azure. **Fare clic su Sfoglia** -> **macchine virtuali** -> il nome del computer virtuale -> **proprietà**e quindi cercare nel campo **Nome di dominio** del riquadro **delle proprietà** .  

È possibile ottenere il numero di porta per le connessioni SSH dal campo **SSH** . Ecco un esempio.  
![][8]

Scaricare Putty da [qui](http://www.putty.org/) .  

Al termine del download, fare clic sul file eseguibile PUTTY. EXE. Configurare le opzioni di base con il nome host e porta numero ottenuto dalle proprietà del computer virtuale. Ecco un esempio:  
![][9]

Nel riquadro sinistro fare clic su **connessione** -> **SSH** -> **Auth** e quindi fare clic su **Sfoglia** per specificare il percorso del file **privateKey.ppk** , che contiene la chiave privata generati da puttygen nella fase 1: creare un'immagine. Ecco un esempio:  
![][10]

Fare clic su **Apri**. Potrebbe ricevere l'avviso tramite una finestra di messaggio. Se è stato configurato il nome del DNS e il numero di porta correttamente, fare clic su **Sì**.
![][11]  


Verrà visualizzato quanto segue:  
![][12]

Immettere il nome utente specificato al momento della creazione della macchina virtuale nella fase 1: creare un'immagine. Verrà visualizzato il seguente:  
![][13]





##<a name="phase-3-install-software"></a>Fase 3: Installare il software
In questa fase, si installa l'ambiente di runtime del linguaggio, tomcat e altri componenti tomcat.  

###<a name="java-runtime-environment"></a>Ambiente runtime Java
Tomcat è scritto in linguaggio. Esistono due tipi di linguaggio Development Kit (JDKs) (OpenJDK e Oracle JDK) ed è possibile scegliere l'opzione desiderata.  

>AZURE. Nota: Entrambi JDKs hanno quasi lo stesso codice per le classi dell'API di linguaggio, ma il codice per la macchina virtuale è effettivamente diverso. Quando si tratta di raccolte, OpenJDK viene usare librerie aperte mentre Oracle tende a utilizzare quelli chiusi. Ma JDK Oracle ha più classi e alcuni dei bug e JDK Oracle è più stabile OpenJDK.

I comandi seguenti scaricare JDKs diversi.  

Apri jdk   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  

jdk Oracle  

-   Per scaricare JDK dal sito Web Oracle:  

        wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  

-   Per creare una directory per i file di JDK:  

        sudo mkdir /usr/lib/jvm  

-   Per estrarre i file JDK nella/usr/raccolta/jvm/directory:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  

-   Per impostare come predefinito JVM JDK Oracle:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  
        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

####<a name="test"></a>Condizione:
Per verificare se l'ambiente di runtime del linguaggio sia installato correttamente, è possibile utilizzare un comando simile al seguente:  

    java -version  

Se è stato installato Apri jdk, visualizzato un messaggio simile al seguente:![][14]

Se è installato oracle jdk, visualizzato un messaggio simile al seguente:![][15]

###<a name="tomcat7"></a>Tomcat7
Utilizzare il comando seguente per installare tomcat7:  

    sudo apt-get install tomcat7  

Se non si utilizza tomcat7, utilizzare la variazione appropriata del comando.  

####<a name="test"></a>Condizione:

Per verificare se tomcat7 è installato, passare alla tomcat del nome del server DNS (http://tomcatexample.cloudapp.net/ è l'URL di esempio in questo articolo). Se viene visualizzata una pagina simile al seguente, è necessario tomcat7 installato corretto.
![][16]


###<a name="install-other-tomcat-components"></a>Installare altri componenti Tomcat
Sono disponibili altri componenti tomcat facoltativo che è possibile installare anche.  

Usare il comando **sudo ricerca apt cache tomcat7** per visualizzare tutti i componenti disponibili. I comandi seguenti sono illustrati esempi di installare alcune parti utili.  

    sudo apt-get install tomcat7-admin      #admin web applications
    sudo apt-get install tomcat7-user         #tools to create user instances  

##<a name="phase-4-configure-tomcat"></a>Fase 4: Configurare Tomcat
In questa fase, si amministra tomcat.
###<a name="start-and-stop-tomcat7"></a>Avviare e arrestare tomcat7
Il server tomcat7 verrà avviato automaticamente durante l'installazione. È possibile anche avviarlo se stessi con il comando seguente:   

    sudo /etc/init.d/tomcat7 start

Per smettere di tomcat7:  

    sudo /etc/init.d/tomcat7 stop

Per visualizzare lo stato di tomcat7:  

    sudo /etc/init.d/tomcat7 status

Per riavviare tomcat servizi:  

    sudo /etc/init.d/tomcat7 restart

###<a name="tomcat-administration"></a>Amministrazione Tomcat
È possibile modificare il file di configurazione Tomcat utente per impostare le credenziali di amministratore con il comando seguente:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Ecco un esempio:  
![][17]  

>AZURE. Nota: Creare una password complessa per il nome utente amministratore.  

Dopo aver modificato il file, è necessario riavviare i servizi tomcat7 con il seguente comando per assicurarsi che le modifiche abbiano effetto:  

    sudo /etc/init.d/tomcat7 restart  

Aprire il browser e immettere l'URL **http://<your tomcat server DNS name>/manager/html**. Ad esempio in questo articolo, l'URL è http://tomcatexample.cloudapp.net/manager/html.  

Dopo la connessione, verrà visualizzata sarà simile al seguente:  
![][18]

##<a name="common-issues"></a>Problemi comuni

###<a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Non è possibile accedere al computer virtuale con Tomcat e Moodle da Internet

-   **Sintomo**  
Tomcat è in esecuzione, ma non è possibile vedere la pagina predefinita Tomcat in un browser.
-   **Maiuscole/minuscole principali possibili**   
    1.  La porta di ascolto tomcat non corrisponde al porta privato dell'endpoint del computer virtuale per il traffico tomcat.  

        Verificare le impostazioni di endpoint pubblici e privati porte e verificare che la porta privato è che diverso da quello del tomcat porta di ascolto. Vedere la sezione fase 1: Creare un'immagine per istruzioni sulla configurazione endpoint per la macchina virtuale.  

        Per determinare la porta di ascolto tomcat, aprire /etc/httpd/conf/httpd.conf (versione finale Red Hat) o /etc/tomcat7/server.xml (Debian versione finale). Per impostazione predefinita, la porta di ascolto tomcat è 8080. Ecco un esempio:  

            <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"  URIEncoding="UTF-8"            redirectPort="8443" />  

        Se si utilizza una macchina virtuale come Debian o Ubuntu e si desidera modificare l'impostazione predefinita porta di Tomcat ascoltare (ad esempio 8081), è anche consigliabile aprire la porta per il sistema operativo. Prima di tutto, aprire il profilo:  

            sudo vi /etc/default/tomcat7  

        Rimuovere il commento per l'ultima riga, quindi modificare "no" a "Sì".  

            AUTHBIND=yes

    2.  Il firewall ha disabilitato la porta di ascolto di tomcat.

        Se è possibile vedere soltanto la pagina predefinita Tomcat dall'host locale, il problema è probabile che la porta ascoltata da tomcat è bloccata dal firewall. È possibile utilizzare lo strumento w3m per passare alla pagina web. I comandi seguenti installare w3m e passare alla pagina Tomcat predefinito:  

            sudo yum  install w3m w3m-img
            w3m http://localhost:8080  

-   **Soluzione**
    1. Se il tomcat ascoltare porta non è stessa come porta privata dell'endpoint per il traffico verso la macchina virtuale, è necessario modificare la porta privata per essere che uguale il tomcat porta di ascolto.   

    2.  Se il problema è dovuto firewall/iptables, aggiungere le seguenti linee /etc/sysconfig/iptables:  

            -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
            -A INPUT -p tcp -m tcp --dport 443 -j ACCEPT  

        Si noti che la seconda riga è necessario solo per il traffico https.  

        Assicurarsi che questo sia sopra tutte le righe che si globalmente limitare l'accesso, ad esempio le operazioni seguenti:  

            -A INPUT -j REJECT --reject-with icmp-host-prohibited  

        Per ricaricare la iptables, eseguire il comando seguente:  

            service iptables restart  

        È stato verificato in CentOS 6.3.

###<a name="permission-denied-when-upload-you-project-files-to-varlibtomcat7webapps"></a>Autorizzazione negato quando si carica di progetto i file con /var/lib/tomcat7/webapps /  

-   **Sintomo**  
Quando si utilizza un client SFTP (ad esempio FileZilla) per connettere la macchina virtuale e passare a /var/lib/tomcat7/webapps/per pubblicare il sito, viene visualizzato un messaggio di errore analogo al seguente:  

        status: Listing directory /var/lib/tomcat7/webapps
        Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
        Error:  /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
        Error:  File transfer failed

-   **Maiuscole/minuscole principali possibili** Si non dispongono di autorizzazioni per accedere alla cartella /var/lib/tomcat7/webapps.  
-   **Soluzione**  
È necessario ottenere l'autorizzazione dell'account principale. È possibile modificare il proprietario di tale cartella dal livello principale per il nome utente che è stato utilizzato durante il provisioning del computer. Ecco un esempio con il nome dell'account azureuser:  

        sudo chown azureuser -R /var/lib/tomcat7/webapps

    Utilizzare l'opzione -R per applicare le autorizzazioni relative a tutti i file all'interno di una directory troppo.  

    Si noti che funziona anche per le directory. L'opzione -R viene modificato le autorizzazioni relative a tutti i file e directory all'interno della directory. Ecco un esempio:  

        sudo chown -R username:group directory  

    Il comando Modifica proprietà (utente e gruppo) per tutti i file e directory all'interno di directory e directory stessa.  

    Il comando seguente solo cambia l'autorizzazione della directory cartella ma lascia il file e cartelle all'interno della directory.  

        sudo chown username:group directory



[1]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]: ./media/virtual-machines-linux-classic-setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
