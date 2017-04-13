<properties
   pageTitle="Che cos'è Azure Analysis Services | Microsoft Azure"
   description="È possibile ottenere il quadro generale di Analysis Services in Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="what-is-azure-analysis-services"></a>Che cos'è Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Basato su motore analitica consolidate in Microsoft SQL Server Analysis Services, Azure Analysis Services consente aziendale modellazione dei dati nel cloud.

> [AZURE.IMPORTANT] Azure Analysis Services è in **Anteprima**. Esistono alcune operazioni che non funzionano solo ancora. Assicurarsi di estrarre [le aspettative anteprima](#preview-expectations) più avanti in questo articolo. E assicurarsi di tenere sotto controllo il [blog di Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920) per informazioni più recenti.

## <a name="built-on-sql-server-analysis-services"></a>Basata su SQL Server Analysis Services
Azure Analysis Services è compatibile con la stessa SQL Server 2016 Analysis Services Enterprise Edition si conosce già. Azure Analysis Services supporta modelli tabulari a livello di compatibilità 1200. DirectQuery, partizioni, protezione a livello di riga, bidirezionale relazioni e traduzioni sono supportate.

## <a name="use-the-tools-you-already-know"></a>Usare gli strumenti che già noti
![Strumenti di sviluppo BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

Durante la creazione di modelli di dati di Azure Analysis Services, utilizzare gli stessi strumenti di SQL Server Analysis Services. Creare e distribuire modelli tabulari utilizzando l'ultima versione di [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) o utilizzare i modelli di [Powershell Azure](../powershell-install-configure.md) e [Gestione di risorse di Azure](../azure-resource-manager/resource-group-overview.md) in [SQL Server Management Studio (Express)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connect-to-data-sources"></a>Connettersi a origini dati
Modelli di dati distribuita in server in Azure supporto per la connessione a dati origini locali nell'organizzazione o nel cloud. Combinare i dati da entrambe locali e cloud origini dati per una distribuzione ibrida di soluzione di Business Intelligence.

![Origini dati](./media/analysis-services-overview/aas-overview-data-sources.png)

Poiché il server nel cloud, la connessione a origini dati cloud è continua. Quando la connessione a origini dati locali, [gateway dati locali](analysis-services-gateway.md) garantisce connessioni veloce, protette con il server Analysis Services nel cloud.  

 \*Alcune origini dati non sono ancora supportate in anteprima. Per ulteriori informazioni, vedere [le aspettative anteprima](#preview-expectations) più avanti in questo articolo.

## <a name="explore-your-data-from-anywhere"></a>Esplorare i dati da qualsiasi luogo
Connettersi e [recuperare dati](analysis-services-connect.md) dal server sulle ovunque. Azure Analysis Services supporta la connessione da Power BI Desktop, Excel, App personalizzate e strumenti basati su browser.

![Visualizzazioni dei dati](./media/analysis-services-overview/aas-overview-visualization.png)

 \*Power BI incorporato non è ancora supportato in anteprima.

## <a name="secure"></a>Proteggere

#### <a name="user-authentication"></a>Autenticazione dell'utente
Autenticazione dell'utente per Azure Analysis services viene gestita da [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Quando si tenta di accedere a un database di Azure Analysis Services, gli utenti usare un'identità di account organizzazione dell'accesso al database che sta tentando di accedere. Queste identità utente devono essere membri del Azure Active Directory predefinito per l'abbonamento in cui si trova il server Azure Analysis Services. [Integrazione della directory](https://technet.microsoft.com/library/jj573653.aspx) tra AAD e un server di Active Directory locale è un'ottima soluzione per ottenere le locale agli utenti l'accesso a un database di Azure Analysis Services, ma non è necessario per tutti gli scenari.

Utenti di accedere con il nome dell'entità utente (UPN) del proprio account e la password. Durante la sincronizzazione con Active Directory locale, spesso UPN dell'utente è l'indirizzo di posta elettronica dell'organizzazione.

Gestione delle autorizzazioni per la gestione delle risorse di server Azure Analysis Services, assegnare gli utenti ai ruoli all'interno di abbonamento Azure. Per impostazione predefinita, gli amministratori di abbonamento dispongono delle autorizzazioni di proprietario per la risorsa server in Azure. Altri utenti possono essere aggiunti tramite Gestione risorse di Azure.

#### <a name="data-security"></a>Protezione dei dati
Azure Analysis Services utilizza lo spazio di archiviazione Blob Azure per mantenere lo spazio di archiviazione e i metadati per i database di Analysis Services. File di dati all'interno di Blob vengono crittografati utilizzando la crittografia sul lato Server (SSE) di Azure Blob. Quando si usa la modalità Query diretta, solo i metadati vengono memorizzati; accesso ai dati effettivi dall'origine dati in fase di query.

#### <a name="on-premises-data-sources"></a>Origini dati locali
Proteggere l'accesso ai dati che si trovano locale all'interno dell'organizzazione possono essere ottenuti installando e configurando un [gateway di dati in locale](analysis-services-gateway.md). Gateway fornire l'accesso ai dati per Query diretta e modalità in memoria. Quando un modello di Azure Analysis Services si connette a un'origine dati locale, verrà creata una query con le credenziali crittografate per l'origine dati locale. Il servizio cloud di gateway analizza la query e inserisce la richiesta per un Bus di servizio Azure. Il gateway locale polling Bus di servizio Azure per le richieste in sospeso. Il gateway quindi Ottiene la query, decrittografa le credenziali e si connette all'origine dati per l'esecuzione. I risultati vengono quindi inviati dall'origine dati al gateway e quindi al database di Azure Analysis Services.

All'interno di Azure Analysis Services è disciplinata dai [Legali Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e l' [Informativa sulla Privacy di servizi Online Microsoft](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Per ulteriori informazioni sulla sicurezza di Windows Azure, vedere il [Centro protezione di Microsoft](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="get-help"></a>È possibile ottenere assistenza
Azure Analysis Services è semplice per configurare e gestire. È possibile trovare tutte le informazioni che necessarie per creare e gestire un server di seguito. Quando si crea un modello di dati per distribuire il server, è molto simile come nel caso di creazione di un modello di dati che si distribuisce a un server locale. Esiste una libreria di concettuale e sulle procedure, esercitazioni e articoli di riferimento in [Analysis Services su MSDN](https://msdn.microsoft.com/library/bb522607.aspx).

Sono disponibili numerosi video utili [Azure Analysis](https://channel9.msdn.com/series/Azure-Analysis-Services)Services su Channel 9.

Aspetti da modificare rapidamente. È sempre possibile ottenere informazioni aggiornate sulla [blog Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920).

## <a name="community"></a>Community
Analysis Services sono una ricca community degli utenti. Partecipare a una conversazione nel [forum di Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Commenti e suggerimenti
Aggiungere i suggerimenti o le richieste di funzionalità? Assicurarsi di inserire i commenti [Azure Analysis Services](https://aka.ms/azureanalysisservicesfeedback)feedback.

Aggiungere i suggerimenti relativi alla documentazione? È possibile aggiungere commenti utilizzando Disqus nella parte inferiore di ogni articolo.

## <a name="preview-expectations"></a>Anteprima aspettative
Azure Analysis Services è attualmente in anteprima. Esistono alcuni aspetti che dovrebbe essere presente.

##### <a name="server-modes"></a>Modalità di server
Azure Analysis Services supporta attualmente modalità tabulare per modelli tabulari a livello di compatibilità 1200. Modalità multidimensionale e Data Mining e Power Pivot per la modalità di SharePoint non sono supportate.

##### <a name="data-sources"></a>Origini dati
Per l'anteprima di origini dati seguenti sono supportate nei modelli tabulari 1200 distribuiti in un server Azure Analysis Services.

| **Cloud** | **Locale** |
|--------------|------------|
| Database SQL | SQL Server |
| SQL Data Warehouse | PUNTI DI ACCESSO |
|      | Oracle |
|       | Teradata |


### <a name="data-source-providers"></a>Provider delle origini dati
Modelli di dati di Azure Analysis Services possono richiedere diversi provider di dati per connettersi a origini dati rispetto ai modelli di dati di SQL Server Analysis Services. Requisiti del provider di dati dipendono da origine dati nel cloud o locale e il tipo di modello di dati. Query in memoria o diretto. Per ulteriori informazioni, vedere [connessioni di origine dati](analysis-services-datasource.md).


### <a name="client-connections"></a>Connessioni client
Power BI incorporato non è ancora supportato in anteprima.

Cartelle di lavoro di Excel con connessioni attive a un server Azure Analysis Services e salvata in OneDrive o SharePoint Online non sono supportate.



## <a name="next-steps"></a>Passaggi successivi
Dopo aver scoperto ulteriori informazioni su Azure Analysis Services, è possibile iniziare. Informazioni su come [creare un server](analysis-services-create-server.md) in Azure e [distribuire un modello tabulare](analysis-services-deploy.md) in essa.
