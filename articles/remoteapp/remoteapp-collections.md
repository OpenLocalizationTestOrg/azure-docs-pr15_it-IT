<properties 
    pageTitle="Quali tipi di raccolta necessari per RemoteApp di Azure | Microsoft Azure" 
    description="Informazioni sui tipi di raccolte disponibili con RemoteApp di Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>Quali tipi di raccolta necessari per RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

RemoteApp Azure consente di condividere App e le risorse con gli utenti in qualsiasi dispositivo. Facciamo mediante la creazione di raccolte per mettere in attesa le App e le risorse e quindi si condividono le raccolte con utenti. Esistono 2 opzioni insieme diverso di rete diversa e opzioni di autenticazione - alle proprie esigenze?

Utile esaminare le considerazioni diversi e le scelte da prendere sfruttare al meglio la raccolta di Azure RemoteApp. 


## <a name="quick-differences-between-the-collection-types"></a>Rapide differenze tra i tipi di raccolta

|           | Cloud | Ibrido |
|-----------|-------|--------|
|Utilizzare un VNET esistente| Sì| Sì|
|Richiede account connessi Active Directory (DirSync)| No| Sì|
|Richiede l'aggiunta al dominio| No| Sì|
|È necessario accedere VNET controller di dominio| No| Sì|

## <a name="cloud-collections"></a>Raccolte cloud
- Veloce creare - insieme è rapidamente il provisioning, ovvero le app di accedere agli utenti più velocemente.
- Visualizzare il proprio App o condividere nostra. È possibile utilizzare un'immagine personalizzata (creata da una macchina virtuale Azure) o una delle immagini incluse con l'abbonamento.
- Non è necessario configurare una connessione tra la raccolta e il dominio locale.
- Ma è possibile usare il proprio VNET Azure facoltativamente per consentire l'accesso nel proprio ambiente locale per la condivisione dei dati o per utilizzare l'autenticazione di Windows non in risorse ad esempio SQL Server (mediante l'autenticazione di database).


OK, come si crea?

- Cloud? Crea con l'opzione **Crea rapido** nel portale.
- Cloud + VNET? Utilizzando l'opzione **Crea con VNET** ma non si desidera aggiungere un dominio.

## <a name="hybrid-collections"></a>Raccolte ibrido
- Accesso completo alla rete locale + VNET Azure.
- Consente l'accesso di join dominio per dati e le applicazioni. Applicazioni remote può eseguire l'autenticazione contro di Active Directory locale, quindi possono accedere alle risorse del dominio.
- Attivare Gestione con le soluzioni System Center esistenti e criteri di gruppo di Windows (tramite un'immagine personalizzata basata su Windows Server 2012 R2) e il monitoraggio avanzate
- Supporto [ExpressRoute](https://azure.microsoft.com/services/expressroute/) per connettere il VNET Azure il VNET locale.

Utilizzando l'opzione **Crea con VNET** e scegliere un dominio.

## <a name="authentication-options"></a>Opzioni di autenticazione
Azure RemoteApp supporta gli account Microsoft e account Azure Active Directory, ma non tutte le raccolte di tutti i metodi. 

| Tipo di account                      |                                                             | Cloud | Cloud + VNET | Ibrido |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|--------|
| Account Microsoft                 |                                                             | Sì   | Sì          | No     |
| Azure Active Directory (AD Azure) |                                                             |       |              |        |
|                                   | Azure Active Directory solo                                               | Sì   | Sì          | No     |
|                                   | Connettersi a Active Directory con la sincronizzazione delle password                               | Sì   | Sì          | Sì    |
|                                   | AD Connect senza la sincronizzazione delle password                            | Sì   | Sì          | No     |
|                                   | Active Directory connettersi con ADFS                                       | Sì   | Sì          | Sì    |
|                                   | provider di identità supportati Azure 3o festa (ad esempio Ping) | Sì   | Sì          | Sì    |
| Autenticazione a più fattori       |                                                             | Sì   | Sì          | Sì    |



### <a name="cloud-and-cloud--vnet"></a>Cloud e Cloud + VNET 
Con gli insiemi di cloud, è possibile utilizzare Microsoft account, account Azure Active Directory o una combinazione dei due. Utilizzare gli account più adatta per gli utenti.

Ci sono esigenze specifiche per l'uso di account di Microsoft. 

Se si desidera usare gli account Azure Active Directory, è necessario assicurarsi che il tenant di Azure Active Directory corrisponda a quello associato all'abbonamento. Una volta creato l'abbonamento RemoteApp di Azure, il tenant di Azure Active Directory in uso è stato automaticamente associato all'abbonamento. Tutti gli utenti di Azure Active Directory che assegnare l'autorizzazione deve essere tenant stesso. Se necessario, è possibile [modificare il tenant di Azure Active Directory](remoteapp-changetenant.md) associato all'abbonamento.
 
### <a name="hybrid-or-cloud--azure-ad--ad"></a>Ibrido (o cloud + Azure Active directory + Active Directory)

Uso di Azure Active Directory locale Active Directory è un prerequisito per una raccolta ibrido +. È necessario utilizzare AD Connect per integrare due directory. Ma è anche possibile avere alcune quando si tratta di come si configura AD Connect. 

Esistono 2 AD Connect scenari - utilizzando la sincronizzazione delle password o la federazione di Active Directory. Verificare le [informazioni di connessione di Active Directory](../active-directory/active-directory-aadconnect.md) per determinare quali di questi funziona meglio dell'utente.

Con un insieme di cloud, è possibile utilizzare Azure Active directory + Active Directory. Assicurarsi che la stessa configurare i passaggi da seguire.

Estrarre [Azure Active Directory + requisiti di Active Directory per Azure RemoteApp](remoteapp-ad.md) per i passaggi necessari per configurare Azure Active Directory e Active Directory.

## <a name="go-create-your-collection"></a>Passare a creare la raccolta.
OK, penso che sono state rapida a questo punto, in modo che solo un elemento a sinistra per eseguire - creare il primo insieme di Azure RemoteApp.

[Creare un insieme di cloud](remoteapp-create-cloud-deployment.md) o [creare un insieme di ibrido](remoteapp-create-hybrid-deployment.md) - semplicemente ottenere creazione.
