<properties
    pageTitle="Tomcat in un computer virtuale | Microsoft Azure"
    description="In questa esercitazione utilizza risorse create con il modello di distribuzione classica e viene illustrato come creare una macchina virtuale di Windows e configurarlo per l'esecuzione Apache Tomcat applicazione server."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.workload="web"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Esecuzione di un server applicazioni Java in una macchina virtuale creata con il modello di distribuzione classica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Con Azure, è possibile utilizzare una macchina virtuale per fornire funzionalità server. Ad esempio, una macchina virtuale in esecuzione in Azure può essere configurata per ospitare un server di applicazioni Java, ad esempio Apache Tomcat. Dopo aver completato questa Guida, sarà necessario comprendere le procedure creare una macchina virtuale in esecuzione in Azure e configurarlo per eseguire un server applicazioni Java.

Si apprenderà:

* Come creare una macchina virtuale che contiene un linguaggio Development Kit (JDK) già installato.
* Come effettuare l'accesso remoto al computer virtuale.
* Come installare un server applicazioni Java nel computer virtuale.
* Come creare un endpoint per la macchina virtuale.
* Come aprire una porta nel firewall per il server applicazioni.

Ai fini di questa esercitazione, un server di applicazioni Apache Tomcat verrà installato in una macchina virtuale. Installazione completata verrà generato in un'installazione Tomcat, ad esempio le operazioni seguenti.

![Computer virtuale che esegue Tomcat Apache][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Per creare una macchina virtuale

1. Accedere al [portale classica Azure](https://manage.windowsazure.com).
2. Fare clic su **Nuovo**, fare clic su **calcolare**, fare clic su **macchina virtuale**e quindi fare clic su **Dalla raccolta**.
3. Nella finestra di dialogo **Seleziona immagine di macchina virtuale** selezionare **JDK 7 Windows Server 2012**.
Si noti che è disponibile se si dispone di applicazioni legacy non è possibile eseguire nella JDK 7 **JDK 6 Windows Server 2012** .
4. Fare clic su **Avanti**.
5. Nella finestra di dialogo **configurazione macchina virtuale** :
    1. Specificare un nome per la macchina virtuale.
    2. Specificare la dimensione da utilizzare per la macchina virtuale.
    3. Immettere un nome per l'amministratore nel campo **Nome utente** . Tenere presente questo nome e la password che immessa successivamente, verranno utilizzati quando accede in remoto a macchina virtuale.
    4. Immettere una password nella casella **nuova password** e immettere nuovamente nella casella **Conferma** . Questa è la password dell'account di amministratore.
    5. Fare clic su **Avanti**.
6. Nella finestra successiva finestra di dialogo **configurazione macchina virtuale** :
    1. Per il **servizio Cloud**, usare il valore predefinito **Crea un nuovo servizio cloud**.
    2. Il valore di **nome DNS del servizio Cloud** deve essere univoco in cloudapp.net. Se necessario, modificare il valore in modo che Azure indica che sia univoco.
    2. Specificare l'area geografica, gruppo affinità o virtuali. Ai fini di questa esercitazione, specificare un'area, ad esempio **US ovest**.
    2. Per **Account di archiviazione**, selezionare **Usa un account di archiviazione generato automaticamente**.
    3. Per **Impostare la disponibilità**, selezionare **(nessuno)**.
    4. Fare clic su **Avanti**.
7. In finale nella finestra di dialogo **configurazione macchina virtuale** :
    1. Accettare le voci dell'endpoint.
    2. Fare clic su **completa**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Effettuare l'accesso remoto a macchina virtuale

1. Accedere al [portale classica Azure](https://manage.windowsazure.com).
2. Fare clic su **macchine virtuali**.
3. Fare clic sul nome del computer virtuale che si desidera effettuare l'accesso a.
4. Iniziata la macchina virtuale, un menu a comparsa nella parte inferiore della pagina consente connessioni.
5. Fare clic su **Connetti**.
6. Seguire le istruzioni visualizzate in base alle esigenze per connettere la macchina virtuale. Questa documentazione del salvataggio o apertura del file con estensione rdp che contiene i dettagli della connessione. Potrebbe essere necessario copiare l'url: porta come l'ultima parte della prima riga del file con estensione rdp e incollarlo in un'applicazione di accesso remota.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Per installare un server applicazioni Java nel computer virtuale

È possibile copiare un server applicazioni Java alla macchina virtuale oppure è possibile installare un server di applicazioni Java tramite un programma di installazione.

Ai fini di questa esercitazione, verrà installato Tomcat.

1. Quando è stato effettuato l'accesso al computer virtuale, aprire una sessione del browser per [Apache Tomcat](http://tomcat.apache.org/download-70.cgi).
2. Fare doppio clic sul collegamento relativo **Programma di installazione servizio Windows 32 o 64 bit**. In questo modo, Tomcat verrà installato come servizio Windows.
3. Quando richiesto, scegliere di eseguire il programma di installazione.
4. All'interno della **Apache Tomcat configurazione** guidata, seguire le istruzioni visualizzate per installare Tomcat. Ai fini di questa esercitazione, è bene accettare le impostazioni predefinite. Quando viene visualizzata la finestra di dialogo **completamento dell'installazione guidata Tomcat Apache** , è possibile controllare se lo si desidera **Eseguire Tomcat Apache** per trasferire Tomcat inizia ora. Fare clic su **Fine** per completare il processo di configurazione Tomcat.

## <a name="to-start-tomcat"></a>Per avviare Tomcat
Se non non scelto per l'esecuzione Tomcat nella finestra di dialogo **completamento dell'installazione guidata Tomcat Apache** , avviarlo aprendo un prompt dei comandi sul computer virtuale e in esecuzione **net avviare Tomcat7**.

Viene visualizzato se si esegue il browser del computer virtuale e aprire <http://localhost:8080>Tomcat.

Per visualizzare Tomcat esecuzione dal computer esterno, è necessario creare un endpoint e aprire una porta.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Per creare un endpoint per la macchina virtuale
1. Accedere al [portale classica Azure](https://manage.windowsazure.com).
2. Fare clic su **macchine virtuali**.
3. Fare clic sul nome del computer che esegue il server di applicazioni Java virtuale.
4. Fare clic sui **punti finali**.
5. Fare clic su **Aggiungi**.
6. Nella finestra di dialogo **Aggiungi endpoint** assicurarsi **Aggiungi autonoma endpoint** sia selezionata e quindi fare clic su **Avanti**.
7. Nella finestra di dialogo **Nuovo dettagli endpoint** :
    1. Specificare un nome per l'endpoint; ad esempio **HttpIn**.
    2. Specificare il protocollo **TCP** .
    3. Specificare **80** per la porta pubblica.
    4. Specificare **8080** per la porta privata.
    5. Fare clic sul pulsante di **completamento** per chiudere la finestra di dialogo. Verrà ora creato l'endpoint.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Per aprire una porta nel firewall per la macchina virtuale
1. Accedere al computer virtuale.
2. Fare clic su **Start di Windows**.
3. Fare clic su **Pannello di controllo**.
4. Fare clic su **sistema e sicurezza**, fare clic su **Windows Firewall**e quindi fare clic su **Impostazioni avanzate**.
5. Fare clic su **Regole in entrata**e quindi fare clic su **Nuova regola**.
 ![Nuova regola in entrata][NewIBRule]
6. Per il **Tipo di regola**, selezionare **porta**e quindi fare clic su **Avanti**.
 ![Nuova porta regola in entrata][NewRulePort]
7. Nella schermata **porte e protocolli** selezionare **TCP**, specificare **8080** come **porta locale specifica**e quindi fare clic su **Avanti**.
 ![Nuova regola in entrata][NewRuleProtocol]
8. Nella schermata di **azione** , selezionare **Consenti la connessione**e quindi fare clic su **Avanti**.
 ![Nuova azione regola in entrata][NewRuleAction]
9. Nella schermata di **profilo** , assicurarsi che **dominio**, **pubblici** e **privati**siano selezionate e quindi fare clic su **Avanti**.
 ![Nuovo profilo di regola in entrata][NewRuleProfile]
10. Nella schermata **nome** specificare un nome per la regola, ad esempio **HttpIn** (il nome della regola non è necessario associare il nome dell'endpoint tuttavia) e quindi fare clic su **Fine**.  
 ![Nuovo nome regola in entrata][NewRuleName]

A questo punto, il sito Web Tomcat dovrebbe essere visti dall'utente da un browser esterno tramite l'URL del modulo * *http://*il\_DNS\_nome*. cloudapp.net**dove ** *il\_DNS\_nome** * è il nome del DNS specificato quando è stata creata.

## <a name="application-lifecycle-considerations"></a>Considerazioni sul ciclo di vita dell'applicazione
* È possibile creare il proprio archivio di applicazione web (WAR) e aggiungere la cartella **WebApp** . Ad esempio, creare un progetto web dynamic Java servizio pagina (JSP) base ed esportarlo come file WAR, copiare il WAR nella cartella **WebApp** Apache Tomcat del computer virtuale, viene eseguita in un browser.
* Per impostazione predefinita se è installato il servizio Tomcat, è impostata per l'avvio manuale. È possibile tornare per l'avvio automatico tramite snap-in servizi. Avviare il componente aggiuntivo servizi facendo clic su **Start di Windows**, **Strumenti di amministrazione**e quindi **servizi**. Fare doppio clic su servizio **Tomcat Apache** e impostare il **tipo di avvio** su **automatico**.

    ![Impostazione di un servizio per l'avvio automatico][service_automatic_startup]

    Il vantaggio di mandare start Tomcat è che verrà avviato se riavvio del computer virtuale (ad esempio, dopo aver installati gli aggiornamenti software che richiedono il riavvio).

## <a name="next-steps"></a>Passaggi successivi
Informazioni sui servizi (ad esempio lo spazio di archiviazione di Azure, bus di servizio e Database SQL) che può essere necessario includere con le applicazioni di linguaggio visualizzando le informazioni disponibili al [Centro per sviluppatori di linguaggio](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProfile.png
