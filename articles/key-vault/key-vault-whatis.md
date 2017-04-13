<properties
    pageTitle="Che cos'è archivio chiave Azure? | Microsoft Azure"
    description="Archivio chiave Azure consente di crittografia per proteggere e informazioni riservate utilizzati da cloud applicazioni e servizi. Utilizzando Azure chiave archivio, è possono crittografare clienti chiavi e informazioni riservate (ad esempio chiavi di autenticazione, lo spazio di archiviazione account chiavi, le chiavi di crittografia di dati. File PFX e la password) utilizzando i tasti che sono protetti tramite moduli di protezione hardware (HSM)."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="cabailey"/>



# <a name="what-is-azure-key-vault"></a>Che cos'è archivio chiave Azure?

Azure chiave archivio è disponibile nella maggior parte delle aree geografiche. Per ulteriori informazioni, vedere [chiave archivio prezzi pagina](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduzione

Archivio chiave Azure consente di crittografia per proteggere e informazioni riservate utilizzati da cloud applicazioni e servizi. Utilizzando chiave archivio, è possibile crittografare chiavi e informazioni riservate (ad esempio chiavi di autenticazione, lo spazio di archiviazione account chiavi, le chiavi di crittografia di dati. File PFX e la password) utilizzando i tasti che sono protetti tramite moduli di protezione hardware (HSM). Per garanzia aggiunta, è possibile importare o generare chiavi in HSM. Se si sceglie di eseguire questa operazione, i processi di Microsoft le chiavi FIPS 140-2 livello 2 convalidato HSM (hardware e firmware).  

Archivio chiave semplifica il processo di gestione delle chiavi e consente di mantenere il controllo delle chiavi per l'accesso e crittografare i dati. Gli sviluppatori possono creare chiavi per sviluppo e la verifica in minuti e quindi migrare loro ai tasti di produzione. Gli amministratori di sicurezza possono concedere e revocare autorizzazione in base alle esigenze dei tasti.

Utilizzare la tabella seguente per una migliore capire come archivio chiave consentono di soddisfare le esigenze di sviluppatori e amministratori di sicurezza.





| Ruolo        | Descrizione accurata           | Soluzione dall'archivio chiave Azure  |
| ------------- |-------------|-----|
| Sviluppo di un'applicazione Azure      | "Desidera scrivere un'applicazione per Azure che utilizza chiavi per la firma e crittografia, ma si desidero questi tasti di esterno dall'applicazione in modo che la soluzione è adatta per un'applicazione che viene distribuita aree geografiche. <br/><br/>Desidero anche questi tasti e informazioni riservate da proteggere, senza dover scrivere il codice dall'utente. Inoltre, è questi tasti e informazioni riservate facili da utilizzare da applicazioni, con prestazioni ottimali per." | Chiavi √ sono archiviate in un archivio e richiamate dal URI quando necessario.<br/><br/> Chiavi √ siano protette da Azure, usando su algoritmi standard, la lunghezza della chiave e moduli di protezione hardware (HSM).<br/><br/> Chiavi √ vengono elaborate nell'HSM che si trovano nei Data Center di Azure stesso come le applicazioni. In questo modo maggiore affidabilità e la latenza ridotta rispetto se i tasti si trovano in un percorso diverso, ad esempio locale.|
| Per sviluppatori di Software come un servizio      |"Non si desidera la responsabilità o potenziali responsabilità per chiavi tenant i clienti e informazioni riservate. <br/><br/>Come si può ai clienti di gestione delle chiavi in modo che è possibile concentrarsi su eseguire operazioni ottimale, che fornisce le principali funzionalità software?" | Clienti √ possono importare le rispettive chiavi Azure e gestirli. Quando un'applicazione di SaaS deve eseguire le operazioni di crittografia utilizzando i tasti dei clienti, archivio chiave vengono eseguite queste operazioni per conto dell'applicazione. L'applicazione non vedrà le chiavi dei clienti.|
| Responsabile della protezione (compagnia) | "Si desiderano le applicazioni conformi a FIPS 140-2 livello 2 HSM per la gestione delle chiavi protetta. <br/><br/>Si desidera verificare che la propria organizzazione nel controllo del ciclo di vita chiave che è possibile monitorare l'utilizzo della chiave. <br/><br/>E anche se si utilizzano più servizi Azure e risorse, desidera gestire i tasti da un'unica posizione in Azure."     |√ HSM sono FIPS 140-2 livello 2 convalidati.<br/><br/>√ Chiave archivio è progettato in modo che Microsoft non visualizzare o estrarre le chiavi.<br/><br/>√ accanto alla registrazione in tempo reale di utilizzo della chiave.<br/><br/>√ L'archivio offre un'unica interfaccia, indipendentemente da quanti archivi avere in Azure, quali aree sono supporto e utilizzano delle applicazioni. |


Chiunque disponga di un abbonamento a Azure possa creare e utilizzare archivi chiavi. Sebbene chiave archivio vantaggi gli sviluppatori e per gli amministratori di sicurezza, potrebbe implementata e gestito dall'amministratore dell'organizzazione che gestisce altri servizi Azure per un'organizzazione. Ad esempio, l'amministratore da effettuare l'accesso con un abbonamento Azure, creare un archivio per l'organizzazione in cui memorizzare le chiavi e quindi essere responsabile dell'attività operative, ad esempio:

+ Creare o importare una chiave o segreto
+ Revocare o eliminare una chiave o segreto
+ Autorizzare utenti o alle applicazioni di accedere archivio chiave, in modo che può gestire o utilizzare le chiavi e informazioni riservate
+ Configurare l'utilizzo della chiave (ad esempio, eseguire l'accesso o crittografare)
+ Monitorare l'utilizzo di chiavi

L'amministratore quindi fornire agli sviluppatori con URI chiamare dalle applicazioni e fornire all'amministratore della sicurezza con le informazioni di registrazione di utilizzo della chiave. 

   ![Panoramica dell'archivio chiave Azure][1]

Gli sviluppatori inoltre possono gestire i tasti direttamente tramite API. Per ulteriori informazioni, vedere [Guida per gli sviluppatori di archivio di chiave](key-vault-developers-guide.md).

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione introduttiva recupero di un amministratore, vedere [Guida introduttiva di Azure chiave archivio](key-vault-get-started.md).

Per ulteriori informazioni sull'utilizzo della registrazione di archivio di chiave, vedere [La registrazione di Azure chiave archivio](key-vault-logging.md).

Per ulteriori informazioni sull'utilizzo di chiavi e informazioni riservate con Azure chiave archivio, vedere [sulle chiavi, informazioni riservate e certificati](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
