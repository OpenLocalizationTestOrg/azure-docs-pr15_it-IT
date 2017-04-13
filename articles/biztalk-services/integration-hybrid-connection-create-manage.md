<properties 
    pageTitle="Creare e gestire le connessioni ibrido | Microsoft Azure" 
    description="Informazioni su come creare una connessione ibrida, gestire la connessione e installare ibrido Connection Manager. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="ccompy"/>


# <a name="create-and-manage-hybrid-connections"></a>Creare e gestire le connessioni ibrido


## <a name="overview-of-the-steps"></a>Panoramica della procedura
1. Creare una connessione ibrida, immettere il **nome host** o il **nome di dominio completo** di risorse locale nella rete privata.
2. Collegare le applicazioni web Azure o App per dispositivi mobili Azure alla connessione ibrida.
3. Installare ibrido Connection Manager nella risorsa locale e connettersi alla connessione ibrido specifico. Portale di Azure offre un'esperienza singolo clic per installare e collegare.
4. Gestire le connessioni ibrida e le chiavi di connessione.

Questo argomento illustra la procedura seguente. 

> [AZURE.IMPORTANT] È possibile impostare un endpoint ibrido connessione a un indirizzo IP. Se si usa un indirizzo IP, è possibile o potrebbero non raggiungere la risorsa locale, a seconda del client. La connessione ibrido dipende dal client di eseguire una ricerca DNS. Nella maggior parte dei casi, il __client__ è il codice dell'applicazione. Se il client non viene effettuata una ricerca DNS (non vengano risolvere l'indirizzo IP come se fosse un nome di dominio (x.x.x. x)), quindi il traffico non vengono inviato tramite la connessione ibrida.
>
> Ad esempio (minuscole) impostati **10.4.5.6** come l'host locale:
> 
> **Funziona nello scenario seguente:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **Non funziona nello scenario seguente:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`


## <a name="CreateHybridConnection"></a>Creare una connessione ibrido

Nel portale di Azure utilizzano Web Apps **o** servizi BizTalk, è possibile creare una connessione ibrido. 

**Per creare connessioni ibrido con Web Apps**, vedere [Connettersi Azure Web App a una risorsa locale](../app-service-web/web-sites-hybrid-connection-get-started.md). È anche possibile installare la gestione di connessione ibrido (HCM) da un'applicazione web, è preferibile. 

**Per creare connessioni ibrido in servizi di BizTalk**:

1. Accedere al [portale classica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel riquadro di spostamento sinistra, selezionare **Servizi BizTalk** e quindi selezionare il BizTalk Service. 

    Se non si dispone di un BizTalk Service esistente, è possibile [creare un servizio BizTalk](biztalk-provision-services.md).
3. Selezionare la scheda **Connessioni ibrido** :  
![Scheda connessioni ibrido][HybridConnectionTab]

4. Selezionare **Crea una connessione ibrido** o selezionare il pulsante **Aggiungi** nella barra delle applicazioni. Immettere quanto segue:

    Proprietà | Descrizione
--- | ---
Nome | Il nome della connessione ibrido deve essere univoco e non può essere lo stesso nome BizTalk Service. È possibile immettere un nome di ma specifica con lo scopo. Alcuni esempi:<br/><br/>Buste paga*SQL Server*<br/>SupplyList*SharepointServer*<br/>I clienti*OracleServer*
Nome host | Immettere il nome host completo solo il nome host o l'indirizzo IPv4 della risorsa locale. Alcuni esempi:<br/><br/>mySQLServer<br/>*mySQLServer*. *Dominio*. com*società*corp.<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*. *società*. com<br/>10.100.10.10<br/><br/>Se si usa l'indirizzo IPv4, tenere presente che il codice applicazione client o potrebbe non consente di risolvere l'indirizzo IP. Vedere la nota importante nella parte superiore di questo argomento.
Porta | Immettere il numero di porta della risorsa locale. Ad esempio, se si usa Web Apps, immettere la porta 80 o 443. Se si usa SQL Server, immettere la porta 1433.

5. Selezionare il segno di spunta per completare la configurazione. 

#### <a name="additional"></a>Aggiuntive

- È possibile creare più connessioni ibrida. Vedere la [servizi BizTalk: edizioni grafico](biztalk-editions-feature-chart.md) per il numero di connessioni consentite. 
- Ogni connessione ibrido viene creato con una coppia di stringhe di connessione: applicazione tasti che tasti di inviare e locale ASCOLTARE. Ogni coppia primaria e chiave secondaria. 


## <a name="LinkWebSite"></a>Collegare il servizio di Azure App Web App o App per dispositivi mobili

Per collegare un Web App o un'App Mobile in Azure App servizio per una connessione ibrido esistente, selezionare **usare una connessione ibrido esistente** e l'ibrido connessioni. Vedere [accesso locale risorse con le connessioni ibrido in Azure App servizio](../app-service-web/web-sites-hybrid-connection-get-started.md).

## <a name="InstallHCM"></a>Installare la gestione di connessione ibrido locale

Dopo aver creata una connessione ibrida, installare la gestione di connessione ibrido sulla risorsa locale. Può essere scaricato dalle applicazioni web Azure o dal BizTalk Service. Passaggi da servizi BizTalk: 

1. Accedere al [portale classica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel riquadro di spostamento sinistra, selezionare **Servizi BizTalk** e quindi selezionare il BizTalk Service. 
3. Selezionare la scheda **Connessioni ibrido** :  
![Scheda connessioni ibrido][HybridConnectionTab]
4. Nella barra delle applicazioni, selezionare **Il programma di installazione locale**:  
![Programma di installazione locale][HCOnPremSetup]
5. Selezionare **installazione e configurazione** di eseguire o scaricare ibrido Connection Manager nel sistema locale. 
6. Selezionare il segno di spunta per avviare l'installazione. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Aggiuntive
- Gestione connessione ibrido può essere installato nei sistemi operativi seguenti:

    - Windows Server 2008 R2 (.NET Framework 4.5 + e Windows Management Framework 4.0 + obbligatorio)
    - Windows Server 2012 (Windows Management Framework 4.0 + obbligatorio)
    - Windows Server 2012 R2


- Dopo aver installato ibrido Connection Manager, si verifica quanto segue: 

    - La connessione ibrido ospitati in Azure viene configurata automaticamente per utilizzare la stringa di connessione dell'applicazione principale. 
    - La risorsa via locale viene configurata automaticamente per utilizzare la stringa di connessione via locale principale.

- La gestione di connessione ibrida è necessario utilizzare una stringa di connessione locale valido per l'autorizzazione. L'App Web di Azure o App Mobile deve utilizzare una stringa di connessione dell'applicazione valido per l'autorizzazione.
- È possibile ridimensionare le connessioni ibrido installando un'altra istanza di gestione connessione ibrido in un altro server. Configurare comunicare ascoltatore locale per usare lo stesso indirizzo come primo comunicare ascoltatore locale. In questo caso, il traffico è distribuiti in modo casuale (circolare) tra listener locale attivo. 


## <a name="ManageHybridConnection"></a>Gestire le connessioni ibrido
Per gestire le connessioni ibrida, è possibile:

- Usare il portale di Azure e passare al BizTalk Service. 
- Utilizzare [le API REST](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Copia/rigenera le stringhe di connessione ibrido

1. Accedere al [portale classica Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. Nel riquadro di spostamento sinistra, selezionare **Servizi BizTalk** e quindi selezionare il BizTalk Service. 
3. Selezionare la scheda **Connessioni ibrido** :  
![Scheda connessioni ibrido][HybridConnectionTab]
4. Selezionare la connessione ibrida. Nella barra delle applicazioni, selezionare **Gestione connessione**:  
![Gestire le opzioni][HCManageConnection]

    **Gestione connessione** vengono elencate le stringhe di connessione dell'applicazione e locale. È possibile copiare le stringhe di connessione o rigenerare tasto di scelta rapida utilizzati nella stringa di connessione. 

    **Se si seleziona Rigenera**, condiviso tasto di scelta rapida utilizzati nella stringa di connessione è cambiato. Eseguire le operazioni seguenti:
    - Nel portale di classica Azure, selezionare **Le chiavi di sincronizzazione** nell'applicazione di Azure.
    - Eseguire di nuovo **Il programma di installazione locale**. Quando si esegue nuovamente il programma di installazione locale, la risorsa locale viene configurata automaticamente per utilizzare la stringa di connessione principale aggiornati.


#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Utilizzare criteri di gruppo per controllare le risorse locali di una connessione ibrido

1. Scaricare i [modelli di amministrazione di gestione connessione ibrida](http://www.microsoft.com/download/details.aspx?id=42963).
2. Estrarre i file.
3. Nel computer in cui i criteri di gruppo vengono modificati, eseguire le operazioni seguenti:  

    - Copia il. File ADMX nella cartella *%WINROOT%\PolicyDefinitions* .
    - Copia il. File adm nella cartella *%WINROOT%\PolicyDefinitions\en-us* .

Una volta copiato, è possibile utilizzare Editor criteri di gruppo per modificare i criteri.




## <a name="next"></a>Successivo

[Connettere le app Web Azure a una risorsa locale](../app-service-web/web-sites-hybrid-connection-get-started.md)  
[Connettersi a SQL Server locale da Azure Web Apps](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)   
[Cenni preliminari sulle connessioni ibrido](integration-hybrid-connection-overview.md)


## <a name="see-also"></a>Vedere anche

[API REST per la gestione dei servizi BizTalk in Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk servizi: Grafico edizioni](biztalk-editions-feature-chart.md)  
[Creare un BizTalk Service Azure nel portale classica](biztalk-provision-services.md)  
[BizTalk Services: Schede del Dashboard, monitorare e scala](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
