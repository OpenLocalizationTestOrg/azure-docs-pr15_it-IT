<properties
    pageTitle="Creare una macchina virtuale in esecuzione MySQL | Microsoft Azure"
    description="Creare una macchina virtuale Azure che eseguono Windows Server 2012 R2 e il database MySQL utilizzando il modello di distribuzione classica."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="cynthn"/>


# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2012-r2"></a>Installare MySQL in una macchina virtuale creata con il modello di distribuzione classica che eseguono Windows Server 2012 R2

[MySQL](http://www.mysql.com) è un database SQL comuni Apri origine. In questa esercitazione viene illustrato come installare ed eseguire la versione della community di MySQL 5.6.23 come MySQL Server in un computer virtuale che eseguono Windows Server 2012 R2. Per istruzioni sull'installazione MySQL su Linux, vedere: [come installare MySQL in Azure](virtual-machines-linux-mysql-install.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="create-a-virtual-machine-running-windows-server-2012-r2"></a>Creare una macchina virtuale che eseguono Windows Server 2012 R2

Se non si dispone di una macchina virtuale che eseguono Windows Server 2012 R2, è possibile utilizzare questa [esercitazione](virtual-machines-windows-classic-tutorial.md) per creare la macchina virtuale. 

## <a name="attach-a-data-disk"></a>Allegare un disco dati

Dopo aver creata la macchina virtuale, è possibile allegare un disco dati aggiuntivi. Questa opzione consigliata per carichi di lavoro di produzione ed evitare lo spazio nell'unità di sistema operativo (c), che include il sistema operativo.

Informazioni su [come allegare un disco di dati a una macchina virtuale di Windows](virtual-machines-windows-classic-attach-disk.md) e seguire le istruzioni per allegare un disco vuoto. Impostare l'opzione di cache host su **Nessuno** o **sola lettura**.

## <a name="log-on-to-the-virtual-machine"></a>Accedere al computer virtuale

Successivamente, sarà necessario [accedere al computer virtuale](virtual-machines-windows-classic-connect-logon.md) in modo che è possibile installare MySQL.

##<a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Installare ed eseguire MySQL Community Server sul computer virtuale

Seguire questa procedura per installare, configurare ed eseguire la versione della Community di MySQL Server:

> [AZURE.NOTE] Questa procedura è per la 5.6.23.0 versione della Community di MySQL e Windows Server 2012 R2. L'esperienza dell'utente potrebbe essere diverso per diverse versioni di Windows Server o MySQL.

1.  Dopo aver collegato al computer virtuali utilizzando Desktop remoto, fare clic su **Internet Explorer** dalla schermata start.
2.  Selezionare il pulsante **Strumenti** in alto a destra (icona Ruota cogged) e quindi fare clic su **Opzioni Internet**. Fare clic sulla scheda **protezione** , fare clic sull'icona **Siti attendibili** e quindi fare clic sul pulsante **siti** . Aggiunge http://*. mysql.com all'elenco dei siti attendibili. Fare clic su * *Chiudi**, quindi fare clic su * *OK**.
3.  Nella barra degli indirizzi di Internet Esplora, digitare http://dev.mysql.com/downloads/mysql/.
4.  Utilizzare il sito MySQL per individuare e scaricare la versione più recente del programma di installazione MySQL per Windows. Quando si sceglie il programma di installazione di MySQL, scaricare la versione che contiene l'intero set (ad esempio, il mysql-installer-community-5.6.23.0.msi con dimensioni massime file di 282.4 MB) e salvare il programma di installazione.
5.  Quando il programma di installazione termine del download, fare clic su **Esegui** per avviare l'installazione.
6.  Nella pagina **Contratto di licenza per** accettare il contratto di licenza e fare clic su **Avanti**.
7.  Nella pagina **scelta di un tipo di installazione** fare clic sul tipo di installazione desiderato e quindi fare clic su **Avanti**. I passaggi seguenti presuppongono la selezione del tipo di installazione **solo Server** .
8.  Nella pagina di **installazione** , fare clic su **Esegui**. Al termine dell'installazione, fare clic su **Avanti**.
9.  Nella pagina **Configurazione del prodotto** , fare clic su **Avanti**.
10. Nella pagina **tipo e la rete** , specificare le opzioni tipo e la connettività di configurazione desiderata, inclusa la porta TCP, se necessario. Selezionare **Mostra opzioni avanzate**e quindi fare clic su **Avanti**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_TypeNetworking.png)

11. Nella pagina **account e ruoli** specificare una password complessa di radice MySQL. Aggiungere altri account utente MySQL in base alle esigenze e quindi fare clic su **Avanti**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AccountsRoles_Filled.png)

12. Nella pagina **Servizio Windows** specificare le modifiche alle impostazioni predefinite per eseguire il MySQL Server come un servizio di Windows in base alle esigenze e quindi fare clic su **Avanti**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_WindowsService.png)

13. Nella pagina **Opzioni avanzate di** specificare le modifiche alle opzioni di registrazione in base alle esigenze e quindi fare clic su **Avanti**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AdvOptions.png)

14. Nella pagina **Applicazione di configurazione del Server** , fare clic su **Esegui**. Dopo aver completata la procedura di configurazione, fare clic su **Fine**.
15. Nella pagina **Configurazione del prodotto** , fare clic su **Avanti**.
16. Nella pagina **Installazione completata** , fare clic su **Log copia negli Appunti** se si desidera esaminare in un secondo momento e quindi fare clic su **Fine**.
17. Dalla schermata start digitare **mysql**e quindi fare clic su **MySQL 5.6 riga di comando Client**.
18. Immettere la password di root specificato nel passaggio 11 e viene visualizzata con una richiesta nel punto in cui è possibile inviare comandi per configurare MySQL. Per altre informazioni sui comandi e sintassi, vedere [Manuali di riferimento MySQL](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_CommandPrompt.png)

19. È anche possibile configurare impostazioni predefinite di configurazione server, ad esempio la directory di base e i dati e le unità, con voci nel file c:\Programmi\Microsoft (x86) \MySQL\MySQL Server 5.6\my-default.ini file. Per ulteriori informazioni, vedere [5.1.2 valori predefiniti di configurazione Server](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Configurare i punti finali

Se si desidera che il servizio di MySQL Server sia disponibile ai computer client MySQL su Internet, è necessario configurare un endpoint per la porta TCP su cui è in attesa del MySQL Server e creare una regola aggiuntiva di Windows Firewall. Si tratta di porte TCP 3306 a meno che non è specificata un'altra porta nella pagina di **tipo e la rete** (passaggio 10 della procedura precedente).


> [AZURE.NOTE] Considerare con attenzione le implicazioni di sicurezza di questa operazione, perché questa operazione deve essere il servizio MySQL Server disponibile per tutti i computer connessi a Internet. È possibile definire l'insieme di indirizzi IP di origine che possono essere utilizzate con un controllo elenco di accesso all'endpoint. Per ulteriori informazioni, vedere [come impostare la punti finali a una macchina virtuale](virtual-machines-windows-classic-setup-endpoints.md).


Per configurare un endpoint del servizio MySQL Server:

1.  Nel portale di Azure classico fare clic su **macchine virtuali**, fare clic sul nome del computer virtuale MySQL e quindi fare clic sui **punti finali**.
2.  Nella barra dei comandi, fare clic su **Aggiungi**.
3.  Nella pagina **Aggiungi un endpoint a una macchina virtuale** fare clic sulla freccia a destra.
4.  Se si usa la porta MySQL TCP di 3306 predefinita, fare clic su **MySQL** nella **casella Nome**e quindi fare clic sul segno di spunta.
5.  Se si utilizza una porta TCP diversa, digitare un nome univoco nella **casella Nome**. Selezionare **TCP** in protocollo, digitare il numero di porta nella **Porta pubblici** e **Privati porta**e quindi fare clic sul segno di spunta.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Aggiungere una regola di Windows Firewall per consentire il traffico MySQL

Per aggiungere una regola di Windows Firewall che consenta il traffico MySQL da Internet, eseguire il comando seguente in una finestra del prompt di Windows PowerShell virtuale computer server MySQL.

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public


    
## <a name="test-your-remote-connection"></a>Verificare la connessione remota


Per verificare la connessione al servizio MySQL Server in esecuzione nel computer virtuale Azure, è innanzitutto necessario determinare il nome DNS corrispondente al servizio cloud che contiene la macchina virtuale che eseguono MySQL Server.

1.  Nel portale di classica Azure, fare clic su **macchine virtuali**, fare clic sul nome del computer virtuale server MySQL e quindi fare clic su **Dashboard**.
2.  Dal dashboard macchina virtuale annotare il valore di **Nome DNS** nella sezione **Informazioni di riepilogo rapido** . Ecco un esempio:

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_DNSName.png)

3.  Dal computer locale con MySQL o il client MySQL, eseguire il seguente comando per eseguire l'accesso come utente MySQL.

        mysql -u <yourMysqlUsername> -p -h <yourDNSname>

    Ad esempio, per il dbadmin3 nome utente MySQL e il nome DNS testmysql.cloudapp.net per la macchina virtuale, utilizzare il comando seguente.

        mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come condurre MySQL, vedere la [Documentazione MySQL](http://dev.mysql.com/doc/).
