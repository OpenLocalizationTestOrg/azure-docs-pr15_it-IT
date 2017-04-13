<properties
    pageTitle="Gestire e monitorare i connettori e App API nel servizio App | Microsoft Azure"
    description="Visualizzare le prestazioni dei connettori e App API nelle App logica; architettura microservices"
    services="app-service\logic"
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger"
    manager="anneta"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="mandia"/>

# <a name="manage-and-monitor-your-built-in-api-apps-and-connectors"></a>Gestire e monitorare le app API e i connettori incorporati

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2014-12-01-anteprima dello schema.

Creazione di un'App API predefinita. E adesso?

In Azure, tutte le applicazioni API è un sito web separato ospitati in Azure. Di conseguenza, è possibile vedere facilmente il numero di richieste effettuato e la quantità di dati è stata utilizzata dal connettore. È anche possibile l'App API di backup, consente di creare avvisi, attivare la sicurezza Stagnola e aggiungere utenti e ruoli.

Questo argomento illustra alcune delle diverse opzioni per gestire l'API App.

Per visualizzare queste caratteristiche incorporate, aprire l'App API nel [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Se l'App API è attivato il startboard, selezionarla per aprire la finestra delle proprietà. È possibile anche selezionare **Sfoglia**, selezionare **API App**e quindi selezionare l'App API:

![][browse]

## <a name="see-the-properties-you-entered"></a>Visualizzare le proprietà che è stata immessa

Quando si apre l'App API, sono disponibili diverse caratteristiche e le attività:

![][settings]

Si può:

- **Impostazioni** Mostra le informazioni specifiche sulle App API, inclusi i dettagli dell'abbonamento ed elenca gli utenti che hanno accesso all'app API. È possibile aumentare o ridurre il numero di istanze dell'App API utilizzando la funzionalità di scala; tra le altre caratteristiche.
- Utilizzare i pulsanti **avviare** e **arrestare** per controllare l'API App.
- Quando gli aggiornamenti vengono applicati ai file sottostanti utilizzati per l'App API, è possibile fare clic su **aggiornamento** per ottenere le versioni più recenti. Ad esempio, esiste una soluzione o un aggiornamento della protezione rilasciato da Microsoft, scegliere **Aggiorna** automaticamente l'App API per includere questa correzione gli aggiornamenti.
- Selezionare **Cambia piano** per l'aggiornamento o downgrade basata sull'utilizzo dei dati dell'API App. È anche possibile utilizzare questa caratteristica per visualizzare l'utilizzo di dati.
- Quando si crea un connettore con tabelle, ad esempio il connettore SQL, è possibile immettere facoltativamente un nome di tabella a cui connettersi. Uno schema in base alla tabella viene automaticamente create e sono disponibili quando si fa clic su **Scarica schemi**. È quindi possibile utilizzare questo schema scaricato per creare una trasformazione o una mappa.

## <a name="change-your-connector-or-api-configuration-values-you-entered"></a>Modificare un connettore o valori di configurazione dell'API che è stata immessa

Dopo aver configurato o creato il connettore integrato, è possibile modificare i valori immessi. Ad esempio, se è stato configurato il connettore di SQL e si desidera modificare il nome di SQL Server o il nome della tabella, è possibile eseguire questa operazione in e il API App per il connettore.

Passaggi:

1. Aprire il connettore o API App. Quando si esegue, viene aperto e il API App.
2. Selezionare il collegamento ipertestuale **Nozioni di base**, in proprietà Host. Il collegamento ipertestuale è assegnato un nome come *slackconnector* o *microsoftsqlconnector123*:

    ![][apiapphost]

3. Nella finestra e API App Host, selezionare **Impostazioni**. In e l'impostazioni selezionare **Impostazioni applicazione**. I valori di configurazione sono elencati sotto **Le impostazioni dell'App**:

    ![][hostsettings]

4. Fare clic su impostazioni che si desidera modificare, immettere il nuovo valore e **salvare** le modifiche.


## <a name="install-the-hybrid-connection-manager---optional"></a>Installare la gestione di connessione ibrido - facoltativo

![][hcsetup]

La gestione di connessione ibrido offre la possibilità di connettersi a un sistema locale, ad esempio SQL Server o SAP. Connettività ibrido utilizza Bus di servizio Azure per connettersi e controllare la sicurezza tra le risorse Azure e le risorse locali.

Vedere [utilizzando la gestione di connessione ibrido nel servizio App Azure](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] Gestione connessione ibrida è necessario solo se si è connessi a una risorsa locale all'interno del firewall. Se non si è connessi a un sistema locale, la gestione di connessione ibrido potrebbe non essere elencata in blade il connettore.

## <a name="monitor-the-performance"></a>Monitorare le prestazioni
Dati sulle prestazioni sono caratteristiche incorporate e incluso in tutte le applicazioni API create. Queste sono specifiche per l'API App ospitato in Azure. Metriche di esempio:

![][monitoring]

Si può:

- Selezionare **errori e le richieste** per aggiungere dati sulle prestazioni diversi inclusi codici di errore HTTP simboli noti, ad esempio 200, 400 o codici di stato HTTP 500. È anche possibile vedere i tempi di risposta, vedere quante richieste all'API App e vedere risulta la quantità di dati e la quantità di dati in uscita. In base a misurazioni sulle prestazioni, è possibile creare gli avvisi di posta elettronica se una metrica supera una determinata soglia scelta.
- In **uso**, è possibile vedere quanto spazio **CPU** viene utilizzata dall'App API, rivedere la **Quota utilizzo** corrente in MB e l'utilizzo di massima dei dati in base al livello di costo. **Stimata dedicato** consentono di determinare i costi potenziali dell'esecuzione dell'applicazione API.
- Selezionare **i processi** per aprire Esplora processo. Mostra le istanze di web e le relative proprietà, incluso l'uso di thread conta. numeri e della memoria.

Utilizzo di questi strumenti, è possibile determinare se il piano di servizio App devono essere scalabilità o ridotta, in base alle esigenze aziendali. Queste funzioni sono predefinite per il portale con senza gli strumenti necessari.

## <a name="control-the-security"></a>Controllare la sicurezza

API App utilizzare sicurezza basata sui ruoli. Questi ruoli riguardano l'intera esperienza Azure, tra cui API App e altre risorse Azure. I ruoli includono:

Ruolo | Descrizione
--- | ---
Proprietario | Avere accesso completo all'ambiente di gestione e può concedere l'accesso ad altri utenti o gruppi.
Per i collaboratori | Avere accesso completo all'ambiente di gestione. Non è possibile concedere l'accesso ad altri utenti o gruppi.
Utilità per la lettura | Visualizzare tutte le risorse ad eccezione di informazioni riservate.
Amministratore di accesso utente | Consente di visualizzare tutte le risorse, creare o gestire ruoli e creare o gestire ticket di supporto.

Vedere [controllo dell'accesso basato sui ruoli nel portale di Microsoft Azure](../active-directory/role-based-access-control-configure.md).

È semplice aggiungere utenti e assegnare ruoli specifici all'API App. Il portale Mostra gli utenti che dispongono di accesso e i ruoli assegnati:

![][access]  

- Selezionare **gli utenti** per aggiungere un utente, assegnare un ruolo e rimuovere un utente.
- Selezionare **i ruoli** per visualizzare tutti gli utenti di un ruolo specifico, aggiungere un utente a un ruolo e rimuovere un utente da un ruolo.


## <a name="more-good-stuff"></a>Ulteriori dati utili
- Selezionare la **definizione dell'API** per aprire il file di Swagger creato automaticamente per l'app API specifica.
- Selezionare **le dipendenze** per visualizzare i file necessari affinché l'API App. Ad esempio, se si usa il connettore SAP, installare alcuni file aggiuntivi nella gestione di connessione ibrido locale. Queste dipendenze vengono visualizzate in blade l'app API.

>[AZURE.IMPORTANT] Quando si visualizzano le proprietà di app API e individuare la casella **Essentials**, sono disponibili collegamenti **Host** e **Gateway** che aprono pale nuove:
>
> ![][host]
>
>Queste proprietà sono specifiche per il sito Web che ospita l'API App. Quando si usa un API App o un connettore incorporato, la maggior parte delle proprietà non è applicabile ed è consigliabile che non si aggiornano queste proprietà. Se è stato creato il proprio App API in Visual Studio distribuito all'abbonamento Azure, è possibile utilizzare le pale Host e al Gateway. <br/><br/>


>[AZURE.NOTE] Per iniziare a usare App logica prima di iscriversi a un account Azure, passare [all'App di logica di prova](https://tryappservice.azure.com/?appservice=logic). È possibile creare un'app di logica breve starter. Nessun carte di credito necessari e non impegni.

## <a name="read-more"></a>Per saperne di più

[Monitorare le app di logica](app-service-logic-monitor-your-logic-apps.md)<br/>
[Elenco di App API nel servizio di App e connettori](app-service-logic-connectors-list.md)<br/>
[Controllo dell'accesso basato sui ruoli nel portale di Microsoft Azure](../active-directory/role-based-access-control-configure.md)<br/>
[Utilizzando la gestione di connessione ibrido nel servizio App Azure](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png
