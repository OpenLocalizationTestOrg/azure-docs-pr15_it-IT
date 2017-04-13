<properties
    pageTitle="Cenni preliminari sulle connessioni ibrido | Microsoft Azure"
    description="Informazioni sulle connessioni ibrida, sicurezza, le porte TCP e configurazioni supportate. MABS WABS."
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
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ccompy"/>


# <a name="hybrid-connections-overview"></a>Cenni preliminari sulle connessioni ibrido
Introduzione alle connessioni ibrida, elenca le configurazioni supportate e le porte TCP necessarie.


## <a name="what-is-a-hybrid-connection"></a>Che cos'è una connessione ibrida

Le connessioni ibrido sono una caratteristica di Azure BizTalk Services. Connessioni ibrido rappresentano un modo semplice e comodo per connettersi la caratteristica Web Apps in Azure App servizio (in precedenza siti Web) e la caratteristica di App Mobile di Azure App Service (precedentemente Mobile Services) a risorse locale all'interno del firewall.

![Connessioni ibrido][HCImage]

Ibrido connessioni vantaggi:

- App Web e App Mobile possono accedere esistente dati locali e servizi in modo sicuro.
- Più Web Apps o App Mobile è possibile condividere una connessione di ibrido per accedere a una risorsa in locale.
- Porte TCP minime sono necessarie per accedere alla rete aziendale.
- Le applicazioni con le connessioni ibrido accedere solo la risorsa locale specifiche pubblicato tramite la connessione ibrida.
- Connettersi a qualsiasi risorsa locale che utilizza una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP e la maggior parte dei servizi Web personalizzati.

    > [AZURE.NOTE] Servizi basati su TCP che utilizzano le porte dinamiche (ad esempio passiva FTP o estesa passiva) non sono attualmente supportati. LDAP anche non è supportato. LDAP utilizza una porta TCP statica, ma è anche possibile utilizzare UDP. Di conseguenza, non è supportata.

- Possono essere usate con tutti i Framework supportati da Web Apps (.NET, PHP, Java, Python, Node) e App Mobile (Node, .NET).
- Web App e App Mobile possono accedere alle risorse locale nello stesso modo come se il Web o App Mobile si trova nella rete locale. Ad esempio, lo stessa connessione utilizzata locale utilizzabile anche in Azure.


Connessioni ibrido offrono gli amministratori dell'organizzazione con controllo e visibilità delle risorse aziendali accessibile da applicazioni ibrido, tra cui:

- Impostazioni di criteri di gruppo, gli amministratori possono consentire le connessioni ibrido nella rete e inoltre designare risorse accessibili tramite applicazioni ibride.
- Registri di controllo ed evento nella rete aziendale forniscono visibilità alle risorse da ibrido connessioni.


## <a name="example-scenarios"></a>Scenari di esempio

Le connessioni ibrido supportano combinazioni framework e le applicazioni seguenti:

- Accesso di .NET framework per SQL Server
- Accesso di .NET framework per i servizi HTTP/HTTPS con WebClient
- Accesso PHP a SQL Server MySQL
- Accesso linguaggio SQL Server, MySQL e Oracle
- Accesso ai servizi HTTP/HTTPS Java

Quando si usa connessioni ibrido per accedere a SQL Server locale, tenere presente quanto segue:

- Express denominata le istanze di SQL deve essere configurate per utilizzare le porte statiche. Per impostazione predefinita, SQL Express istanze denominate utilizza porte dinamiche.
- Express predefinito le istanze di SQL utilizzare una porta statica, ma TCP deve essere abilitato. Per impostazione predefinita, TCP non è abilitato.
- Quando si utilizza cluster o gruppi di disponibilità, il `MultiSubnetFailover=true` modalità non è supportata.
- Il `ApplicationIntent=ReadOnly` non è supportato.
- Autenticazione di SQL Server potrebbe essere necessario del metodo di autorizzazione-to-end supportati dall'applicazione Azure e SQL server locale.


## <a name="security-and-ports"></a>Sicurezza e porte

Connessioni ibrido utilizzano l'autorizzazione condiviso accesso firma (SA) per proteggere le connessioni da applicazioni Azure e la gestione di connessione ibrido locale per la connessione ibrida. Connessione separata chiavi vengono create per l'applicazione e la gestione di connessione ibrido locale. Questi tasti di connessione possono essere riportati e revocati in modo indipendente.

Connessioni ibrida disponibili per la distribuzione di sicuro di tasti per le applicazioni e la gestione di connessione ibrido locale.

Vedere [creare e gestire le connessioni ibrida](integration-hybrid-connection-create-manage.md).

*Autorizzazioni nell'applicazione sono distinto dal connessione ibrida*. Qualsiasi metodo autorizzazione appropriata può essere utilizzato. Il metodo di autorizzazione dipende dai metodi di autorizzazione-to-end supportati su cloud Azure e i componenti locale. Ad esempio, l'applicazione Azure accede a un SQL Server locale. In questo scenario, autorizzazione SQL possono essere il metodo di autorizzazione più supportata-to-end.

#### <a name="tcp-ports"></a>Porte TCP
Le connessioni ibrido richiedono connettività TCP o HTTP solo in uscita dalla rete privata. Non è necessario aprire le porte del firewall o modificare la configurazione di rete perimetrale per consentire a qualsiasi connettività in ingresso alla rete aziendale.

Le seguenti porte TCP vengono usate per le connessioni ibrido:

Porta | A cosa serve
--- | ---
9350 - 9354 | Queste porte vengono utilizzate per la trasmissione dei dati. Il responsabile di relay Service Bus analizza porta 9350 per determinare se è disponibile la connettività TCP. Se è disponibile, si presuppone che porta 9352 sia disponibile. Dati del traffico attraverso la porta 9352. <br/><br/>Consentire le connessioni in uscita a queste porte.
5671 | Quando la porta 9352 è utilizzata per il traffico dati, porta 5671 viene utilizzata il canale di controllo. <br/><br/>Consentire le connessioni in uscita a questa porta.
80, 443 | Queste porte vengono utilizzate per alcune richieste di dati di Azure. Inoltre, se le porte 9352 e 5671 non possono essere utilizzati, *quindi* porte 80 e 443 sono le porte fallback utilizzate per la trasmissione dei dati e il canale di controllo.<br/><br/>Consentire le connessioni in uscita a queste porte. <br/><br/>**Nota** Non è consigliabile utilizzare queste come le porte fallback al posto di altre porte TCP. HTTP/WebSocket viene utilizzato come protocollo anziché TCP nativi per i canali di dati. Potrebbe verificarsi prestazioni ridotte.



## <a name="next-steps"></a>Passaggi successivi

[Creare e gestire le connessioni ibrido](integration-hybrid-connection-create-manage.md)<br/>
[Connettere le app Web Azure a una risorsa locale](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[Connettersi a SQL Server locale da un'app web Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>


## <a name="see-also"></a>Vedere anche

[API REST per la gestione dei servizi BizTalk in Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
[servizi BizTalk: edizioni grafico](biztalk-editions-feature-chart.md)<br/>
[Creare un BizTalk Service nel portale di Azure](biztalk-provision-services.md)<br/>
[BizTalk Services: Schede del Dashboard, monitorare e scala](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
