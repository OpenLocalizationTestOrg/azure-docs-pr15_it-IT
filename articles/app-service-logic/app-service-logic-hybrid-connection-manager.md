<properties 
    pageTitle="Utilizzando la gestione di connessione ibrida | Microsoft Azure" 
    description="Installare e configurare la gestione di connessione ibrida e collegare ai connettori locale nelle App logica" 
    services="app-service\logic" 
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/>

# <a name="connect-to-on-premises-connectors-using-the-hybrid-connection-manager"></a>Connettersi ai connettori locale utilizzando la gestione di connessione ibrido

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2014-12-01-anteprima dello schema. Disponibilità generale App logica (GA) viene utilizzato un gateway per la connettività locale. Altre informazioni sul nuovo [gateway](app-service-logic-gateway-connection.md) e [Logica App GA](https://azure.microsoft.com/documentation/services/logic-apps/).

Per utilizzare un sistema locale, App logica utilizza ibrido Connection Manager. Alcuni connettori possono connettersi a un sistema locale, ad esempio SQL Server, SAP, SharePoint e così via. 

La gestione di connessione ibrida (HCM) è un clic-una volta installer installata in un server IIS all'interno della rete, il firewall. Usa un inoltro Bus di servizio Azure, HCM autentica del sistema locale con il connettore in Azure. 

> [AZURE.NOTE] Gestione connessione ibrida è necessario solo se si è connessi a una risorsa locale all'interno del firewall. Se non si è connessi a un sistema locale, quindi non è necessario ibrido Connection Manager.

Per iniziare, è necessario:

- Azure Bus di servizio inoltro dello spazio dei nomi SA stringa di connessione. Vedere [Servizio Bus prezzi](https://azure.microsoft.com/pricing/details/service-bus/) per determinare il livello include inoltro.
- Locale sistema informazioni di accesso, tra cui nome utente e password. Ad esempio, se ci si connette a un Server SQL locale, è necessario l'account di accesso di SQL Server e la password.
- Informazioni sul server locale, comprese porta numero e nome server. Ad esempio, se ci si connette a un Server SQL locale, è necessario il nome di SQL Server e il numero di porta TCP.

## <a name="get-the-service-bus-connection-string"></a>Ottenere la stringa di connessione Bus di servizio

Nel portale di Azure, copiare la radice di servizio Bus SA stringa di connessione. Questa stringa di connessione, il connettore Azure si connette al sistema locale. 

1. Nel [portale classica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885), selezionare lo spazio dei nomi Bus di servizio e selezionare **Le informazioni di connessione**:

    ![][SB_ConnectInfo]

2. Copiare la stringa di connessione SA:

    ![][SB_SAS]

## <a name="install-the-hybrid-connection-manager"></a>Installare la gestione di connessione ibrido

1. Nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), selezionare il connettore che è stato creato. Per aprirlo, è possibile selezionare **Sfoglia**, selezionare **API App**e quindi selezionare il connettore o API App. 
<br/><br/>
In **Connessione ibrida**, la configurazione è **incompleto**:
<br/>
![][2] 

2. Selezionare **ibrido connessione**. La stringa di connessione Bus di servizio che precedentemente immesse è presente nell'elenco.
3. Copiare la **stringa di configurazione principale**:
<br/>
![][PrimaryConfigString]

4. In programma di **Gestione connessione ibrido locale**, è possibile scaricare il gestore di connessione ibrido o installarlo direttamente dal portale. 
<br/><br/>
Per installare direttamente dal portale, accedere al server IIS locale, passare al portale di e selezionare **Configura e Download**.
<br/><br/>
Per scaricare la gestione di connessione ibrida, passare al server IIS locale e passare all' **applicazione ClickOnce** (http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application). L'installazione viene avviato automaticamente in modo da eseguire.

5. Nella finestra **Imposta comunicare ascoltatore** immettere la **Stringa di configurazione principale** precedentemente incollati (nel passaggio 3) e scegliere **Installa**.

Al termine dell'installazione, Visualizza seguenti:
<br/>
![][3] 

Quando si visualizza il connettore nuovamente, lo stato di connessione ibrida è **connesso**. Potrebbe essere necessario chiudere il connettore e alla riapertura del documento:
<br/>
![][4] 

> [AZURE.NOTE] Per passare alla stringa di connessione dati secondaria, eseguire nuovamente la configurazione ibrida connessione e immettere la **Stringa di configurazione secondario**.


## <a name="tcp-ports-and-security"></a>Porte TCP e la sicurezza

Quando si crea una connessione ibrida, viene creato un sito Web sul server IIS locale locale. Il server IIS può essere in una rete Perimetrale. Requisiti di porte TCP sul server IIS includono:

Porte TCP | Perché
--- | ---
 | Nessun porte TCP in ingresso necessari.
9350 - 9354 | Queste porte vengono utilizzate per la trasmissione dei dati. Il responsabile di relay Service Bus analizza porta 9350 per determinare se è disponibile la connettività TCP. Se è disponibile, si presuppone che porta 9352 sia disponibile. Dati del traffico attraverso la porta 9352. <br/><br/>Consentire le connessioni in uscita a queste porte.
5671 | Quando la porta 9352 è utilizzata per il traffico dati, porta 5671 viene utilizzata il canale di controllo. <br/><br/>Consentire le connessioni in uscita a questa porta. 
80, 443 | Se le porte 9352 e 5671 non possono essere utilizzati, *quindi* porte 80 e 443 sono le porte fallback utilizzate per la trasmissione dei dati e il canale di controllo.<br/><br/>Consentire le connessioni in uscita a queste porte.
Porta di sistema locale | Sul computer locale, aprire la porta utilizzata dal sistema. Ad esempio SQL Server utilizza in genere la porta 1433. Aprire la porta TCP.

[Un firewall con Bus di servizio di hosting](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## <a name="troubleshooting"></a>Risoluzione dei problemi

![][HCMFlow]

### <a name="on-premises-troubleshooting"></a>Risoluzione dei problemi in locale

1. Nel server IIS confermare il ruolo di web IIS sia installato e tutti i servizi IIS vengono avviati.
2. In server IIS confermare ibrido Connection Manager viene installato e in esecuzione:
 - In Gestione IIS (inetmgr), il sito Web ***MicrosoftAzureBizTalkHybridListener*** dovrebbero essere elencati e che sia in esecuzione. 
 - In questo sito Web venga usata ***HybridListenerAppPool*** che viene eseguita come account utente predefiniti locali del *servizio di rete* . Questo AppPool anche deve essere avviato.
3. In server IIS confermare che il connettore è installato e in esecuzione: 
 - Per il connettore viene creato un sito Web. Ad esempio, se è stato creato un connettore SQL, è un sito Web ***MicrosoftSqlConnector_nnn*** . In Gestione IIS (inetmgr), verificare il sito Web è elencato e avviato. 
 - Questo sito Web utilizza il proprio pool di applicazioni IIS denominato ***HybridAppPoolnnn***. Questo AppPool viene eseguita come account utente predefiniti locali del *servizio di rete* . In questo sito Web e AppPool deve entrambi essere avviato. 
 - Individuare il connettore locale. Ad esempio, se il sito Web connettore utilizza la porta 6569, passare a http://localhost:6569. Un documento predefinito non è configurato in modo che un `HTTP Error 403.14 - Forbidden error` è previsto.
4. Nel firewall, verificare le porte TCP elencati in questo argomento sono aperte.
5. Esaminare il sistema di origine o di destinazione:
 - Alcuni sistemi locali richiedono file dipendenza aggiuntiva. Ad esempio, se ci si connette a SAP locale, alcuni file SAP aggiuntivi installare sul server IIS.
 - Controllare la connessione al sistema con l'account di accesso. Ad esempio, la porta TCP utilizzata dal sistema deve essere aperta, ad esempio la porta 1433 per SQL Server. L'account di accesso che è stata immessa nel portale di Azure deve avere accesso al sistema.
6. In server IIS controllare i registri eventi per individuare eventuali errori. 
7. Pulizia e reinstallare la gestione di connessione ibrido: 
 - In IIS, eliminare manualmente il sito Web connettore e il relativo pool. 
 - Eseguire nuovamente la gestione di connessione ibrida e confermare che l'immissione corretta **Stringa di configurazione principale** per il connettore.



### <a name="in-the-azure-classic-portal"></a>Nel portale di classica Azure

1. Verificare che lo spazio dei nomi del servizio Bus ha lo stato **attivo** .
2. Quando si crea il connettore, immettere la stringa di connessione servizio Bus SA. Non immettere la stringa di connessione ACS.


## <a name="faq"></a>Domande frequenti

**Domanda**: sono disponibili due ibrido connessione Manager. Che cos'è la differenza? 

**Answer**: c'è la tecnologia [Ibrida connessioni](../biztalk-services/integration-hybrid-connection-overview.md) utilizzati principalmente dal Web Apps (in precedenza siti Web) e App Mobile (in precedenza mobile services) a cui connettersi locale. Questo gestore di connessioni ibrida è l' [installazione](../biztalk-services/integration-hybrid-connection-create-manage.md) e utilizza un servizio di BizTalk Azure (dietro le quinte). Supporta solo protocolli TCP e HTTP.

Tramite connettori ad angolo servizio App Azure, sono disponibili una gestione connessione ibrida.  Indica la gestione di connessione ibrido *non* usare un BizTalk Azure Service (dietro le quinte) e supporta più i protocolli TCP e HTTP. Visualizzare i [connettori e API App nell'elenco](app-service-logic-connectors-list.md).

Entrambi utilizzano Bus di servizio Azure per connettersi al server locale.

**Domanda**: quando crea un'App API personalizzata, è possibile utilizzare App servizio ibrido Connection Manager a cui connettersi locale? 

**Answer**: non nel modo consueto. È possibile utilizzare un connettore incorporato, configurare la gestione di connessione App ibrida servizio per la connessione al sistema locale. Quindi, utilizzare il connettore con l'App API personalizzato, possibilmente usando un'App di logica. Al momento non è possibile sviluppare o creare il proprio ibrido API App (ad esempio il connettore SQL o File).

Se l'API personalizzato utilizza una porta TCP o HTTP, è possibile utilizzare [Le connessioni ibrida](../biztalk-services/integration-hybrid-connection-overview.md) e la gestione di connessione ibrida. In questo scenario, viene utilizzato un servizio di BizTalk Azure. [Connettersi a SQL Server locale da un'app web](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md) possono risultare utili.  


## <a name="read-more"></a>Per saperne di più

[Monitorare le app di logica](app-service-logic-monitor-your-logic-apps.md)<br/>
[Servizio Bus prezzi](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 
