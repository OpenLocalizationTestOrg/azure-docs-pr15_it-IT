<properties
    pageTitle="Eseguire la migrazione da servizi Mobile a un servizio di App per dispositivi mobili"
    description="Informazioni su come migrare facilmente l'applicazione Servizi mobili a un'App di App servizio Mobile"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="adrianha"/>

# <a name="article-top"></a>Eseguire la migrazione del servizio Mobile Azure esistente al servizio App Azure

Con la [disponibilità generale di Azure App servizio]siti Azure Mobile Services possono essere facilmente migrati sul posto in modo da sfruttare tutte le caratteristiche del servizio di App Azure.  In questo documento spiega cosa aspettarsi durante la migrazione del sito da servizi di Windows Azure Mobile al servizio App Azure.

## <a name="what-does-migration-do"></a>Quali operazioni eseguire la migrazione nel sito

Migrazione del servizio Mobile Azure trasforma il servizio Mobile in un'applicazione di [Servizio App Azure] senza influire sul codice.  L'hub di notifica, connessione dati SQL, le impostazioni di autenticazione, programmate e nome di dominio rimangono invariati.  Client mobili tramite il servizio Mobile Azure continuare a funzionare normalmente.  Migrazione riavvia il servizio dopo il trasferimento al servizio App Azure.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>Perché è necessario eseguire la migrazione del sito

È consigliabile eseguire la migrazione il servizio Mobile Azure per sfruttare le caratteristiche di Azure servizio App, tra cui:

  *  Nuove caratteristiche host, tra cui [WebJobs] e [nomi di dominio personalizzato].
  *  Connettività per le risorse locale utilizzando [VNet] oltre [Ibrido connessioni].
  *  Monitoraggio e la risoluzione dei problemi Relic nuovo o [Applicazione approfondimenti].
  *  Test di produzione e utensili attrezzi predefiniti, tra cui [bande orarie di gestione temporanea], da ripristinare.
  *  [Ridimensionamento automatico], bilanciamento del carico e [il monitoraggio delle prestazioni].

Per ulteriori informazioni sui vantaggi del servizio di Azure App, vedere l'argomento di [Servizi e App servizio Mobile] .

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare operazioni principali del sito, è necessario [eseguire il backup del servizio Mobile] script e database SQL.

## <a name="migrating-site"></a>La migrazione dei siti

Il processo di migrazione esegue la migrazione di tutti i siti in una singola regione di Azure.

Per eseguire la migrazione del sito:

  1.  Accedere al [portale classica Azure].
  2.  Selezionare un servizio di dispositivi mobili nell'area che si desidera eseguire la migrazione.
  3.  Fare clic sul pulsante di **eseguire la migrazione al servizio di App** .

    ![Il pulsante Migrate][0]

  4.  Leggere la migrazione alla finestra di dialogo servizio App.
  5.  Immettere il nome del servizio Mobile nell'apposita casella.  Ad esempio, se il nome di dominio è contoso.azure mobile.net, immettere _contoso_ nell'apposita casella.
  6.  Fare clic sul pulsante dei segni di graduazione.

Controllare lo stato della migrazione in Monitoraggio attività. Il sito è elencato come *eseguire la migrazione* nel portale classica di Azure.

  ![Monitoraggio attività di migrazione][1]

Ogni migrazione può richiedere da 3 a 15 minuti per ogni servizio mobile viene eseguita la migrazione.  Il sito rimane disponibile durante la migrazione.
Il sito viene riavviato al termine del processo di migrazione.  Il sito è disponibile durante il processo di riavvio, che può avere una durata pochi secondi.

## <a name="finalizing-migration"></a>Completamento della migrazione

Piano per testare il sito da un client mobile al termine del processo di migrazione.  Assicurarsi che è possibile eseguire tutte le azioni comuni di client senza le modifiche apportate ai client mobili.  

### <a name="update-app-service-tier"></a>Selezionare un servizio App appropriato prezzi livello

È necessario maggiore flessibilità nella prezzi dopo la migrazione al servizio App Azure.

  1.  Accedere al [portale di Azure].
  2.  Selezionare **tutte le risorse** o **Servizi App** , quindi fare clic sul nome del servizio Mobile migrati.
  3.  Per impostazione predefinita verrà aperto e l'impostazioni.
  4.  Fare clic su **App servizio piano** nel menu impostazioni.
  5.  Fare clic sul riquadro **Livello prezzi** .
  6.  Fare clic sul riquadro appropriato alle proprie esigenze e quindi fare clic su **Seleziona**.  Potrebbe essere necessario fare clic su **Visualizza tutto** per visualizzare i prezzi disponibili livelli.

Come punto di partenza, è consigliabile livelli seguenti:

| Livello di prezzi servizio per dispositivi mobili | Servizio di App prezzi livello |
| :-------------------------- | :----------------------- |
| Libera                        | F1 gratuito                  |
| Base                       | B1 Basic                 |
| Standard                    | S1 Standard              |

Esiste un notevole flessibilità nella scelta destra prezzi livello per l'applicazione.  Fare riferimento [all'App servizio prezzi] per informazioni dettagliate sui prezzi del nuovo servizio App.

> [AZURE.TIP]Il livello di App Service Standard contiene accesso a molte funzionalità da utilizzare, tra cui [bande orarie di gestione temporanea], backup automatici e il ridimensionamento automatico.  Estrarre le nuove funzionalità mentre ci sono!

### <a name="review-migration-scheduler-jobs"></a>Esaminare i processi coinvolti nella migrazione utilità di pianificazione

Utilità di pianificazione processi non sarà visibili fino a 30 minuti dopo la migrazione.  Continuano a programmate per l'esecuzione in background.
Per visualizzare i processi pianificati dopo che sono visibili nuovamente:

  1.  Accedere al [portale di Azure].
  2.  Selezionare **Sfoglia >**, immettere **pianificazione** nella casella _filtro_ e quindi selezionare **Le raccolte di utilità di pianificazione**.

Esistono un numero limitato di utilità di pianificazione gratuito processi disponibili dopo la migrazione.  Rivedere l'utilizzo e di [Azure utilità di pianificazione plan di messaggistica unificata].

### <a name="configure-cors"></a>Configurare CORS se necessario

Condivisione di risorse tra origine è una tecnica per consentire a un sito Web accedere a un'API Web in un dominio diverso.  Se si utilizza servizi di Windows Azure Mobile con un sito Web associato, è necessario configurare CORS come parte della migrazione.  Se si accede a servizi di Windows Azure Mobile esclusivamente da dispositivi mobili, quindi CORS non è necessario configurare tranne raramente.

Le impostazioni di CORS migrate sono disponibili come l'impostazione di App **MS_CrossDomainWhitelist** .  Per eseguire la migrazione del sito per la funzione CORS servizio App:

  1.  Accedere al [portale di Azure].
  2.  Selezionare **tutte le risorse** o **Servizi App** , quindi fare clic sul nome del servizio Mobile migrati.
  3.  Per impostazione predefinita verrà aperto e l'impostazioni.
  4.  Fare clic su **CORS** nel menu API.
  5.  Immettere eventuali origini consentiti nell'apposita casella, premendo INVIO dopo ognuna di esse.
  6.  Dopo l'elenco delle origini consentiti è corretto, fare clic sul pulsante Salva.

> [AZURE.TIP]Uno dei vantaggi dell'utilizzo di un servizio di App Azure è che è possibile eseguire il sito web e il servizio mobile nello stesso sito.  Per ulteriori informazioni, vedere la sezione [passaggi successivi](#next-steps) .

### <a name="download-publish-profile"></a>Scaricare un nuovo profilo di pubblicazione

Durante la migrazione al servizio App Azure, viene modificato il profilo di pubblicazione del sito.  Se si intende pubblicare il sito dall'interno di Visual Studio, è necessario un nuovo profilo di pubblicazione.  Per scaricare il nuovo profilo di pubblicazione:

  1.  Accedere al [portale di Azure].
  2.  Selezionare **tutte le risorse** o **Servizi App** , quindi fare clic sul nome del servizio Mobile migrati.
  3.  Fare clic su **Recupera profilo di pubblicazione**.

Il file PublishSettings viene scaricato nel computer in uso.  Si tratta in genere _NomeSito_. PublishSettings.  Importare le impostazioni di pubblicazione nel progetto esistente:

  1.  Aprire Visual Studio e il progetto di servizio Mobile Azure.
  2.  Pulsante destro del mouse sul progetto in **Esplora soluzioni** e selezionare **pubblica...**
  3.  Fare clic su **Importa**
  4.  Fare clic su **Sfoglia** e selezionare lo scaricati file di impostazioni di pubblicazione.  Fare clic su **OK**
  5.  Fare clic su **Convalida connessione** per assicurarsi che il lavoro impostazioni di pubblicazione.
  6.  Fare clic su **pubblica** per pubblicare il sito.


## <a name="working-with-your-site"></a>Utilizzo di post-migrazione del sito

Iniziare a lavorare con il nuovo servizio App nel [portale di Azure] successive alla migrazione.  Di seguito sono alcune note operazioni specifiche che consentono di eseguire nel [Portale classica di Azure], insieme a equivalenti servizio App.

### <a name="publishing-your-site"></a>Download e il sito coinvolti nella migrazione di pubblicazione

Il sito è disponibile tramite fra o ftp e possibile ripubblicare con diversi meccanismi diversi, incluso WebDeploy TFS, Mercurial, GitHub e FTP.  Le credenziali di distribuzione vengono eseguite con il resto del sito.  Se non è stata impostata le credenziali di distribuzione o non si ricorda, è possibile reimpostare loro:

  1. Accedere al [portale di Azure].
  2. Selezionare **tutte le risorse** o **Servizi App** , quindi fare clic sul nome del servizio Mobile migrati.
  3. Per impostazione predefinita verrà aperto e l'impostazioni.
  4. Fare clic su **credenziali distribuzione** sul processo di pubblicazione dal menu.
  5. Immettere le nuove credenziali di distribuzione nelle caselle disponibili, quindi fare clic sul pulsante Salva.

È possibile utilizzare le credenziali per duplicare il sito con fra o configurare distribuzioni automatiche da GitHub, TFS o Mercurial.  Per ulteriori informazioni, vedere la [documentazione relativa alla distribuzione di Azure App servizio].

### <a name="appsettings"></a>Impostazioni dell'applicazione

La maggior parte delle impostazioni di un servizio per dispositivi mobili migrato sono disponibili tramite le impostazioni dell'App.  È possibile ottenere un elenco delle impostazioni app dal [portale di Azure].
Per visualizzare o modificare le impostazioni dell'app:

  1. Accedere al [portale di Azure].
  2. Selezionare **tutte le risorse** o **Servizi App** , quindi fare clic sul nome del servizio Mobile migrati.
  3. Per impostazione predefinita verrà aperto e l'impostazioni.
  4. Fare clic su **Impostazioni applicazione** dal menu Generale.
  5. Scorrere fino alla sezione Impostazioni di App e trovare le impostazioni di app.
  6. Fare clic sul valore dell'impostazione di app per modificare il valore.  Fare clic su **Salva** per salvare il valore.

È possibile aggiornare le impostazioni dell'app più nello stesso momento.

> [AZURE.TIP]Sono disponibili due impostazioni applicazione con lo stesso valore.  Ad esempio, si può vedere _ApplicationKey_ e _MS\_ApplicationKey_.  Aggiornare entrambi le impostazioni dell'applicazione nello stesso momento.

### <a name="authentication"></a>Autenticazione

Tutte le impostazioni di autenticazione sono disponibili come le impostazioni dell'App nel sito migrato.  Per aggiornare le impostazioni di autenticazione, è necessario modificare le impostazioni di app appropriata.  Nella tabella seguente mostra le impostazioni di app appropriato per il provider di autenticazione:

| Provider          | ID client                 | Segreto del client                | Altre impostazioni             |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Account Microsoft | **MS\_MicrosoftClientID**  | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook          | **MS\_FacebookAppID**      | **MS\_FacebookAppSecret**     |                            |
| Twitter           | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** |                            |
| Google            | **MS\_GoogleClientID**     | **MS\_GoogleClientSecret**    |                            |
| Azure Active Directory          | **MS\_AadClientID**        |                              | **MS\_AadTenants**          |

Nota: **MS\_AadTenants** viene memorizzato come un elenco di domini tenant (campi "Tenant consentiti" nel portale di servizi mobili) separati da virgola.

> [AZURE.WARNING] **Non utilizzare i meccanismi di autenticazione nel menu impostazioni**
>
> Servizio App Azure fornisce un separata sistema autenticazione e l'autorizzazione di "senza codice" di nel _autenticazione / autorizzazione_ dal menu impostazioni e l'opzione _Autenticazione Mobile_ (obsoleta) nel menu impostazioni.  Queste opzioni non sono compatibili con il servizio di Mobile Azure migrati.  È possibile [eseguire l'aggiornamento del sito](app-service-mobile-net-upgrading-from-mobile-services.md) per sfruttare l'autenticazione di Azure App servizio.

### <a name="easytables"></a>Dati

Scheda _dati_ della finestra di dialogo servizi Mobile è stata sostituita dalle _Tabelle semplice_ all'interno del portale Azure.  Per accedere a tabelle semplice:

  1. Accedere al [portale di Azure].
  2. Selezionare **tutte le risorse** o **Servizi App** , quindi fare clic sul nome del servizio Mobile migrati.
  3. Per impostazione predefinita verrà aperto e l'impostazioni.
  4. Fare clic su **tabelle semplice** nel menu per dispositivi mobili.

È possibile aggiungere una tabella facendo clic sul pulsante **Aggiungi** o accedere le tabelle esistenti, fare clic su un nome di tabella.  Esistono varie operazioni è possibile farlo da blade, tra cui:

* Modifica delle autorizzazioni di tabella
* Modifica script operativi
* Gestione dello schema di tabella
* Eliminazione della tabella
* Cancellare il contenuto della tabella
* Eliminazione di righe specifiche della tabella

### <a name="easyapis"></a>API

Scheda _API_ nei servizi Mobile è stata sostituita da _API semplice_ all'interno del portale Azure.  Per accedere alle API semplice:

  1. Accedere al [portale di Azure].
  2. Selezionare **tutte le risorse** o **Servizi App** , quindi fare clic sul nome del servizio Mobile migrati.
  3. Per impostazione predefinita verrà aperto e l'impostazioni.
  4. Fare clic su **API semplice** nel menu per dispositivi mobili.

L'API migrate già presenti in e il.  È anche possibile aggiungere un'API da questo blade.  Per gestire un'API specifica, fare clic su API.
Dalla nuova pala, è possibile modificare le autorizzazioni e modificare gli script per l'API.

### <a name="on-demand-jobs"></a>Utilità di pianificazione processi

Tutti i processi di utilità di pianificazione sono disponibili tramite la sezione insiemi di processo di pianificazione.  Per accedere all'utilità di pianificazione processi:

  1. Accedere al [portale di Azure].
  2. Selezionare **Sfoglia >**, immettere **pianificazione** nella casella _filtro_ e quindi selezionare **Le raccolte di utilità di pianificazione**.
  3. Selezionare la raccolta di processo per il sito.  È denominato _NomeSito_-processi.
  4. Fare clic su **Impostazioni**.
  5. Fare clic su **utilità di pianificazione processi** in Gestione.

Programmate sono elencati con la frequenza specificata prima della migrazione.  Processi su richiesta sono disabilitati.  Per eseguire un processo su richiesta:

  1. Selezionare il processo che si desidera eseguire.
  2. Se necessario, fare clic su **Attiva** per abilitare il processo.
  3. Fare clic su **Impostazioni**, quindi **pianificazione**.
  4. Selezionare una ricorrenza di **una sola volta**, quindi fare clic su **Salva**

I processi su richiesta risiede in `App_Data/config/scripts/scheduler post-migration`.  È consigliabile convertire tutti i processi su richiesta in [WebJobs] o [funzioni].  Scrivere nuovi processi utilità di pianificazione come [WebJobs] o [funzioni].

### <a name="notification-hubs"></a>Hub di notifica

Servizi mobile utilizza hub di notifica per le notifiche push.  Le seguenti impostazioni App vengono usate per collegare l'Hub di notifica del servizio Mobile dopo la migrazione:

| Impostazione dell'applicazione                    | Descrizione                              |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace**             | Il Namespace Hub di notifica           |
| **MS\_NotificationHubName**             | Il nome dell'Hub di notifica                |
| **MS\_NotificationHubConnectionString** | La stringa di connessione Hub di notifica   |
| **MS\_NamespaceName**                   | Un alias per MS_PushEntityNamespace      |

L'Hub di notifica viene gestito tramite il [portale di Azure].  Prendere nota del nome di notifica Hub (è possibile trovare questo utilizzando le impostazioni di App):

  1. Accedere al [portale di Azure].
  2. Selezionare **Sfoglia**>, quindi selezionare **Gli hub di notifica**
  3. Fare clic sul nome di notifica Hub associato al servizio per dispositivi mobili.

> [AZURE.NOTE]Se l'HUb di notifica è di tipo "Misto", non è visibile.  "Misto" digitare hub utilizzano hub di notifica e legacy caratteristiche Bus di servizio di notifica.  [Convertire gli spazi dei nomi misto] prima di continuare.  Al termine della conversione, l'hub di notifica viene visualizzata nel [portale di Azure].

Per ulteriori informazioni, consultare la documentazione [Hub di notifica] .

> [AZURE.TIP]Funzionalità di gestione di hub di notifica nel [portale di Azure] sono ancora in anteprima.  Il [Portale classica Azure] rimane disponibile per la gestione di tutti gli hub la notifica.

### <a name="legacy-push"></a>Impostazioni Push legacy

Se è stato configurato del servizio per dispositivi mobili prima dell'introduzione su hub di notifica Push, si sta utilizzando _push legacy_.  Se si utilizza Push e non è presente un Hub di notifica elencato nella configurazione, è probabile che si usano _push legacy_.  Questa funzionalità viene eseguita la migrazione con tutte le altre funzionalità.  Tuttavia, è consigliabile eseguire l'aggiornamento a un hub di notifica subito dopo la migrazione è stata completata.

Nel frattempo, tutte le impostazioni di push legacy (con l'eccezione degni di nota del certificato APN) sono disponibili in Impostazioni applicazione.  Aggiornare il certificato APN sostituendo il file appropriato nel file System.

### <a name="app-settings"></a>Altre impostazioni di App

Le seguenti impostazioni aggiuntive app sono incluse nella migrazione dal servizio Mobile e disponibile in *Impostazioni* > *Le impostazioni dell'App*:

| Impostazione dell'applicazione              | Descrizione                             |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName**         | Il nome dell'app                    |
| **MS\_MobileServiceDomainSuffix** | Prefisso del dominio. ad esempio Azure mobile.net |
| **MS\_ApplicationKey**            | Il tasto applicazione                    |
| **MS\_chiave master**                 | La chiave master app                     |

Il tasto applicazione e la chiave master sono identici per i tasti dell'applicazione dal servizio Mobile originale.  In particolare, il tasto viene inviato per client mobili per convalidare l'uso di API per dispositivi mobili.

### <a name="cliequivalents"></a>Equivalenti della riga di comando

È possibile utilizzare il comando di _azure per dispositivi mobili_ più per gestire il sito di servizi di dispositivi mobili Windows Azure.  Se, tuttavia, molte funzioni sono state sostituite con il comando _sito azure_ .  Utilizzare la tabella seguente per trovare equivalenti per i comandi comuni:

| _Azure Mobile_ Comando                     | Comando equivalente _Sito Azure_            |
| :----------------------------------------- | :----------------------------------------- |
| posizioni per dispositivi mobili                           | elenco di località del sito                         |
| elenco di dispositivi mobili                                | elenco dei siti                                  |
| Mostra dispositivi mobili _nome_                         | sito mostra _nome_                           |
| riavvio mobile _nome_                      | riavvio del sito _nome_                        |
| dispositivi mobili Ridistribuisci _nome_                     | sito distribuzione Ridistribuisci _commitId_ _nome_ |
| chiave per dispositivi mobili imposta _tipo_ di _nome_ _valore_       | sito appsetting Elimina _chiave_ _nome_ <br/> sito appsetting aggiungere _chiave_= _nome_ del_valore_ |
| _nome_ dell'elenco configurazione per dispositivi mobili                  | _nome_ dell'elenco appsetting sito                |
| configurazione di dispositivi mobili ottenere _nome_ _chiave_             | sito appsetting Mostra _chiave_ _nome_          |
| configurazione di dispositivi mobili imposta _nome_ _chiave_             | sito appsetting Elimina _chiave_ _nome_ <br/> sito appsetting aggiungere _chiave_= _nome_ del_valore_ |
| _nome_ dell'elenco dominio per dispositivi mobili                  | elenco di dominio sito _nome_                    |
| dominio mobile aggiungere _nome di_ _dominio_          | dominio sito aggiungere il _nome_ di _dominio_            |
| dominio mobile Elimina _nome_                | _sito dominio Elimina _nome_ _         |
| scala mobile Mostra _nome_                   | sito mostra _nome_                           |
| scala mobile Cambia _nome_                 | sito scala modalità _modalità_ _nome_ <br /> sito scala istanze _istanze_ _nome_ |
| _nome_ dell'elenco appsetting per dispositivi mobili              | _nome_ dell'elenco appsetting sito                |
| dispositivi mobili appsetting aggiungere _nome_ _chiave_ _valore_ | sito appsetting aggiungere _chiave_= _nome_ del_valore_   |
| dispositivi mobili appsetting Elimina _nome_ _chiave_      | sito appsetting Elimina _chiave_ _nome_        |
| dispositivi mobili appsetting Mostra _nome_ _chiave_        | sito appsetting Elimina _chiave_ _nome_        |

Aggiornare l'autenticazione o le impostazioni di notifica push aggiornando l'impostazione di applicazione appropriata.
Modificare i file e la pubblicazione del sito tramite ftp o fra.

### <a name="diagnostics"></a>La registrazione e diagnostica

Registrazione diagnostica normalmente è disabilitata in un servizio di App Azure.  Per attivare la registrazione diagnostica:

  1. Accedere al [portale di Azure].
  2. Selezionare **tutte le risorse** o **Servizi App** , quindi fare clic sul nome del servizio Mobile migrati.
  3. Per impostazione predefinita verrà aperto e l'impostazioni.
  4. Selezionare il menu di caratteristiche **Registri diagnostici** .
  5. Fare clic **su** per i registri seguenti: **Applicazione di registrazione (file System)**, **messaggi di errore dettagliati**e **analisi richiesta non riuscita**
  6. Fare clic su **File System** per la registrazione server Web
  7. Fare clic su **Salva**

Per visualizzare i log:

  1. Accedere al [portale di Azure].
  2. Selezionare **tutte le risorse** o **Servizi App** , quindi fare clic sul nome del servizio Mobile migrati.
  3. Fare clic sul pulsante **Strumenti**
  4. Selezionare **Log flusso** nel menu RISPETTA.

I registri vengono visualizzati nella finestra di generazione.  È anche possibile scaricare i registri per analisi successive usando le proprie credenziali di distribuzione. Per ulteriori informazioni, vedere la documentazione per la [registrazione] .

## <a name="known-issues"></a>Problemi noti

### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Eliminazione di un duplicato di App Mobile viene eseguita la migrazione causa un'interruzione del sito

Se si duplica il servizio mobile migrato tramite PowerShell Azure e quindi Elimina il duplicato, viene rimosso la voce DNS per il servizio di produzione.  Il sito non è più essere accessibili da Internet.  

Soluzione: Se si desidera duplicare il sito, farlo tramite il portale.

### <a name="changing-webconfig-does-not-work"></a>Modifica config non funziona

Se si dispone di un sito ASP.NET, diventa il `Web.config` file non viene applicato.  Il servizio di App Azure crea un idoneo `Web.config` file durante l'avvio per il supporto runtime Servizi mobili.  È possibile ignorare alcune impostazioni (ad esempio le intestazioni personalizzate) usando un file di trasformazione XML.  Creare un file in chiamato `applicationHost.xdt` -questo file deve terminare con la `D:\home\site` directory il servizio di Azure.  Caricare il `applicationHost.xdt` file tramite uno script di distribuzione personalizzato o direttamente con Kudu.  Di seguito viene illustrato un esempio di documento:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Per ulteriori informazioni, vedere la documentazione [XDT trasformare esempi] su GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Migrati servizi Mobile non è possibile aggiungere al gestore del traffico

Quando si crea un profilo di gestore del traffico, è possibile scegliere direttamente un servizio per dispositivi mobili migrato al profilo.  Utilizzare un "endpoint esterno".  L'endpoint esterno può essere aggiunti solo tramite PowerShell.  Per ulteriori informazioni, vedere l' [esercitazione gestore del traffico](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Passaggi successivi

Ora che l'applicazione viene eseguita la migrazione al servizio di App, sono disponibili anche altre caratteristiche che consentono:

  * Distribuzione [bande orarie di gestione temporanea] consentono di organizzare le modifiche al sito ed eseguire un / B-test.
  * [WebJobs] offrono pensata come sostitutivo di processi pianificato su richiesta.
  * È possibile [distribuire continuamente] al sito tramite un collegamento al sito a GitHub, TFS o Mercurial.
  * È possibile usare [Applicazione approfondimenti] per monitorare il sito.
  * Serve un sito Web e API Mobile dallo stesso codice.

### <a name="upgrading-your-site"></a>L'aggiornamento del sito di servizi mobili a Azure Mobile App SDK

  * Per i progetti basati su Node server, il nuovo [SDK Node App Mobile] offre diverse nuove caratteristiche. Ad esempio, è possibile ora eseguire sviluppo locale e il debug, usare una versione qualsiasi Node sopra 0,10 e personalizzare con qualsiasi middleware Express.js.

  * Per. Progetti server basata su rete, i nuovi [pacchetti Mobile App SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) una maggiore flessibilità sulle dipendenze NuGet.  Questi pacchetti supportano l'autenticazione di servizio App nuovo e possono essere combinate con qualsiasi progetto ASP.NET. Per maggiori informazioni sull'aggiornamento, vedere [eseguire l'aggiornamento del servizio Mobile .NET esistente al servizio di App](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Servizio di App prezzi]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Informazioni dettagliate sui applicazione]: ../application-insights/app-insights-overview.md
[Ridimensionamento automatico]: ../app-service-web/web-sites-scale.md
[Servizio App Azure]: ../app-service/app-service-value-prop-what-is.md
[Documentazione relativa alla distribuzione di Azure servizio App]: ../app-service-web/web-sites-deploy.md
[Portale classica Azure]: https://manage.windowsazure.com
[Portale di Azure]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[Piani di Azure utilità di pianificazione]: ../scheduler/scheduler-plans-billing.md
[distribuire continuamente]: ../app-service-web/app-service-continuous-deployment.md
[Convertire gli spazi dei nomi misti]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[nomi di dominio personalizzato]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilità generale di servizio App Azure]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Connessioni ibrido]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[Registrazione]: ../app-service-web/web-sites-enable-diagnostic-log.md
[App per dispositivi mobili Node SDK]: https://github.com/azure/azure-mobile-apps-node
[Servizi e App servizio mobile]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Hub di notifica]: ../notification-hubs/notification-hubs-push-notification-overview.md
[monitoraggio delle prestazioni]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[Eseguire il backup del servizio Mobile]: ../mobile-services/mobile-services-disaster-recovery.md
[gestione temporanea bande orarie]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md
[Esempi di trasformazione XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Funzioni]: ../azure-functions/functions-overview.md
