<properties 
    pageTitle="Come salvare e configurare la configurazione del servizio di gestione delle API utilizzando fra" 
    description="Informazioni su come salvare e configurare la configurazione del servizio di gestione delle API utilizzando fra." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Come salvare e configurare la configurazione del servizio di gestione delle API utilizzando fra

>[AZURE.IMPORTANT] Configurazione di operazioni per la gestione dell'API è attualmente in anteprima. Completa, ma si in anteprima perché siamo attivamente alla ricerca di commenti e suggerimenti su questa caratteristica. È possibile che possiamo fare un sostanziale modificata in base al feedback dei clienti, è consigliabile non a seconda delle funzionalità per l'utilizzo in ambienti di produzione. Se hai commenti e suggerimenti o domande, indicare in `apimgmt@microsoft.com`.

Ogni istanza del servizio di gestione delle API gestisce un database di configurazione che contiene le informazioni sulla configurazione e metadati per l'istanza del servizio. Modifica di un'impostazione nel portale di publisher, usando un cmdlet di PowerShell o effettuare una chiamata API REST, modifiche possono essere eseguite all'istanza del servizio. Oltre a questi metodi, è anche possibile gestire la configurazione di istanza del servizio mediante operazioni, ad esempio, abilitazione di scenari di gestione del servizio:

-   Controllo delle versioni di configurazione, scaricare e archiviare versioni diverse di configurazione del servizio
-   Modifiche alla configurazione di massa: apportare modifiche a più parti di configurazione del servizio nell'archivio locale e integrare le modifiche al server con un'unica operazione
-   Familiarità fra toolchain e flusso di lavoro - utilizzare le operazioni utensili e flussi di lavoro che si ha già familiarità con

Nel diagramma seguente mostra una panoramica dei diversi modi per configurare l'istanza di servizio di gestione delle API.

![Configurare fra][api-management-git-configure]

Quando si apportano modifiche al servizio tramite il portale di publisher, i cmdlet di PowerShell o API REST, vengono gestiti i database di configurazione del servizio mediante la `https://{name}.management.azure-api.net` endpoint, come illustrato sul lato destro del diagramma. Sul lato sinistro del diagramma viene illustrato come gestire la configurazione del servizio mediante fra e fra archivio per il servizio che si trova in `https://{name}.scm.azure-api.net`.

I passaggi seguenti offrono una panoramica della gestione l'istanza del servizio di gestione API utilizzando fra.

1.  Abilitare l'accesso fra nel servizio di posta
2.  Salvare il database di configurazione del servizio per l'archivio fra
3.  Duplicare repo fra nel computer locale
4.  Estrarre l'ultima repo sul computer locale e commit e push modifiche il repo
5.  Distribuire le modifiche dal repo nel database di configurazione del servizio

In questo articolo viene descritto come attivare e utilizzare fra per gestire la configurazione del servizio e fornisce un riferimento per i file e cartelle di archivio di operazioni.

## <a name="to-enable-git-access"></a>Per consentire l'accesso fra

È possibile visualizzare rapidamente lo stato della configurazione fra visualizzando l'icona fra in alto a destra del portale di publisher. In questo esempio, fra access non è ancora abilitata.

![Stato fra][api-management-git-icon-enable]

Per visualizzare e configurare le impostazioni di configurazione fra, è possibile fare clic sull'icona fra o fare clic sul menu di **sicurezza** e passare alla scheda **archivio di configurazione** .

![Abilitare fra][api-management-enable-git]

Per abilitare l'accesso fra, selezionare la casella di controllo **Abilita fra access** .

Dopo alcuni istanti verrà salvata la modifica e viene visualizzato un messaggio di conferma. Si noti che fra icona è cambiato a colori per indicare che è abilitato l'accesso fra e il messaggio di stato ora indica che ci sono modifiche non salvate nell'archivio. Ciò avviene perché il database di configurazione del servizio di gestione delle API non è ancora stato salvato nel repository.

![Operazioni abilitata][api-management-git-enabled]

>[AZURE.IMPORTANT] Le informazioni riservate che non sono definiti come proprietà verranno salvate nell'archivio e verrà mantenuta finché l'utente nella cronologia disattivare e riattivare l'accesso fra. Proprietà forniscono un posto sicuro per la gestione di valori di stringa costante, incluse informazioni riservate, tutti i criteri, e configurazione di API in modo da non dover archiviarli direttamente nelle istruzioni dei criteri. Per ulteriori informazioni, vedere [come utilizzare le proprietà in Criteri di gestione delle API di Azure](api-management-howto-properties.md).

Per informazioni sull'abilitazione o disabilitazione dell'accesso fra tramite l'API REST, vedere [abilitare o disabilitare fra l'accesso con l'API REST](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Per salvare la configurazione del servizio all'archivio fra

Il primo passaggio prima della duplicazione repository consiste nel salvare lo stato corrente della configurazione del servizio per l'archivio. Fare clic su **Salva configurazione all'archivio**.

![Salvare la configurazione][api-management-save-configuration]

Apportare le modifiche desiderate nella schermata di conferma e fare clic su **Ok** per salvare.

![Salvare la configurazione][api-management-save-configuration-confirm]

Dopo alcuni secondi la configurazione viene salvata e viene visualizzato lo stato di configurazione dell'archivio, tra cui la data e l'ora dell'ultima modifica di configurazione e dell'ultima sincronizzazione tra la configurazione del servizio e l'archivio.

![Stato di configurazione][api-management-configuration-status]

Dopo la configurazione viene salvata nel repository, può essere duplicato.

Per informazioni su come eseguire questa operazione tramite l'API REST, vedere [eseguire il Commit snapshot della configurazione tramite l'API REST](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Per duplicare l'archivio nel computer locale

Per duplicare un archivio, è necessario l'URL per l'archivio, un nome utente e una password. Il nome utente e l'URL vengono visualizzati nella parte superiore della scheda **archivio di configurazione** .

![Operazioni duplicato][api-management-configuration-git-clone]

La password viene generata nella parte inferiore della scheda **archivio di configurazione** .

![Generare password][api-management-generate-password]

Per generare una password, prima di tutto assicurarsi che la **scadenza** è impostato sulla data di scadenza desiderata e quindi fare clic su **Genera Token**.

![Password][api-management-password]

>[AZURE.IMPORTANT] Prendere nota della password. Una volta che si lascia questa pagina la password non essere visualizzata nuovamente.

In questo esempio Usa lo strumento fra Bash [Fra per Windows](http://www.git-scm.com/downloads) , ma è possibile utilizzare gli strumenti di operazioni che si ha familiarità con.

Aprire lo strumento fra nella cartella desiderata ed eseguire il seguente comando per duplicare repository fra nel computer locale, con il comando fornito dal portale di publisher.

    git clone https://bugbashdev4.scm.azure-api.net/ 

Specificare il nome utente e la password quando richiesto.

Se si ricevono errori, provare a modificare il `git clone` comando per includere il nome utente e la password, come illustrato nell'esempio seguente.

    git clone https://username:password@bugbashdev4.scm.azure-api.net/

Se si fornisce un messaggio di errore, provare la codifica parte password del comando dell'URL. Il più modo rapido per eseguire questa operazione consiste nell'aprire Visual Studio e il comando seguente nella **Finestra controllo immediato**. Per aprire la **Finestra controllo immediato**, aprire qualsiasi soluzione o un progetto in Visual Studio oppure creare una nuova applicazione console vuota, quindi scegliere **Windows**, **controllo immediato** dal menu **Debug** .

    ?System.NetWebUtility.UrlEncode("password from publisher portal")

Usare la password codificata oltre la posizione di archivio e nome utente per costruire il comando fra.

    git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/

Dopo il salvataggio viene duplicato è possibile visualizzare e lavorare nel file system locale. Per ulteriori informazioni, vedere [informazioni di riferimento relative ai struttura File e cartelle di archivio fra locale](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Per aggiornare l'archivio locale con la configurazione di istanza del servizio aggiornata

Se si apportano modifiche all'istanza del servizio di gestione delle API nel portale di publisher o tramite l'API REST, è necessario salvare queste modifiche all'archivio per poter aggiornare l'archivio locale con le ultime modifiche. Per eseguire questa operazione, fare clic su **Salva configurazione all'archivio** nella scheda **repository configurazione** nel portale di publisher e il comando seguente nell'archivio locale.

    git pull

Prima di eseguire `git pull` assicurarsi di essere nella cartella per l'archivio locale. Se è stata completata solo il `git clone` comando, è necessario modificare la directory per il repo eseguendo un comando simile al seguente.

    cd bugbashdev4.scm.azure-api.net/

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Per inserire le modifiche dal repo locale a repo server

Per inserire le modifiche dal proprio archivio locale archivio di server, è necessario salvare le modifiche e quindi inviarli all'archivio server. Per salvare le modifiche, aprire lo strumento di comando fra nella directory dell'archivio locale e utilizzare i comandi seguenti.

    git add --all
    git commit -m "Description of your changes"

Per inviare tutti i commit al server, eseguire il comando seguente.

    git push

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Per distribuire modifiche alla configurazione del servizio per l'istanza del servizio di gestione delle API

Una volta le modifiche locali sono approvate e inserite all'archivio server, è possibile distribuire per l'istanza del servizio di gestione delle API.

![Distribuire][api-management-configuration-deploy]

Per informazioni su come eseguire questa operazione tramite l'API REST, vedere [distribuire fra le modifiche apportate ai database di configurazione tramite l'API REST](https://msdn.microsoft.com/library/dn781420.aspx#DeployChanges).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Riferimento alla struttura file e cartelle di archivio fra locale

I file e cartelle di archivio locale fra contengono le informazioni di configurazione dell'istanza del servizio.

| Elemento                       | Descrizione                                                                                |
|-------------------------   |--------------------------------------------------------------------------------------------|
| cartella di gestione delle api principale | Contiene configurazione principale per l'istanza del servizio                                  |
| cartella API                | Contiene la configurazione per le API dell'istanza di servizio                            |
| cartella gruppi              | Contiene la configurazione per i gruppi dell'istanza di servizio                          |
| cartella Criteri            | Contiene i criteri dell'istanza di servizio                                              |
| cartella portalStyles        | Contiene la configurazione per le personalizzazioni del portale per sviluppatori dell'istanza di servizio |
| cartella Products            | Contiene la configurazione per i prodotti in istanza del servizio                        |
| cartella modelli           | Contiene la configurazione per i modelli di posta elettronica dell'istanza di servizio                 |

Ogni cartella può contenere uno o più file e in alcuni casi una o più cartelle, ad esempio una cartella per ogni API, un prodotto o un gruppo. I file all'interno di ogni cartella sono specifici per il tipo di entità designato dal nome della cartella.

| Tipo di file | Scopo                                                                |
|-----------|------------------------------------------------------------------------|
| JSON      | Informazioni di configurazione entità corrispondente                  |
| HTML      | Descrizioni sulle entità, spesso visualizzata nel portale per sviluppatori |
| XML       | Istruzioni di criteri                                                      |
| CSS       | Fogli di stile per la personalizzazione del portale per sviluppatori                        |

Questi file possono essere creati, eliminati, modificati e gestiti nel file system locale e cambia distribuito all'istanza del servizio Gestione API.

>[AZURE.NOTE] Entità seguenti non sono presenti nel repository fra e non possono essere configurate procedendo come fra.
>
>-    Utenti
>-    Sottoscrizioni
>-    Proprietà
>-    Entità portale per sviluppatori diversa da quella degli stili

### <a name="root-api-management-folder"></a>Cartella di gestione delle api principale

Radice `api-management` cartella contiene un `configuration.json` file che contiene le informazioni principali sull'istanza del servizio nel formato seguente.

    {
      "settings": {
        "RegistrationEnabled": "True",
        "UserRegistrationTerms": null,
        "UserRegistrationTermsEnabled": "False",
        "UserRegistrationTermsConsentRequired": "False",
        "DelegationEnabled": "False",
        "DelegationUrl": "",
        "DelegatedSubscriptionEnabled": "False",
        "DelegationValidationKey": ""
      },
      "$ref-policy": "api-management/policies/global.xml"
    }

Le prime quattro impostazioni (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, e `UserRegistrationTermsConsentRequired`) mappa per le seguenti impostazioni nella scheda **identità** nella sezione **sicurezza** .

| Impostazione di identità                     | Esegue il mapping a                                               |
|--------------------------------------|-------------------------------------------------------|
| RegistrationEnabled                  | Casella di controllo **reindirizzare gli utenti anonimi alla pagina di accesso** |
| UserRegistrationTerms                | Casella di testo **delle condizioni di utilizzo in iscrizione a utente**               |
| UserRegistrationTermsEnabled         | Casella di controllo **Mostra condizioni per l'utilizzo nella pagina di accesso**         |
| UserRegistrationTermsConsentRequired | Casella di controllo **Richiedi consenso dell'utente**                          |

![Impostazioni di identità][api-management-identity-settings]

Le quattro impostazioni (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, e `DelegationValidationKey`) mappa per le seguenti impostazioni nella scheda **delega** nella sezione **sicurezza** .

| Impostazione di delega           | Esegue il mapping a                                    |
|------------------------------|--------------------------------------------|
| DelegationEnabled            | Casella di controllo **delega accesso e abbonamento**    |
| DelegationUrl                | Casella di testo **URL endpoint delega**        |
| DelegatedSubscriptionEnabled | Casella di controllo di **sottoscrizione di prodotto delegato** |
| DelegationValidationKey      | Casella di testo **Chiave di convalida delegato**        |

![Impostazioni di delega][api-management-delegation-settings]

L'impostazione finale, `$ref-policy`, esegue il mapping al file di istruzioni di criteri globale per l'istanza del servizio.

### <a name="apis-folder"></a>cartella API

Il `apis` cartella contiene una cartella per ogni API dell'istanza di servizio che contiene gli elementi seguenti.

-   `apis\<api name>\configuration.json`-Questa configurazione per l'API e contiene le informazioni relative all'URL del servizio di back-end e le operazioni. Si tratta le stesse informazioni che vengono restituite se si chiama [Get un'API specifica](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI) con `export=true` in `application/json` formato.
-   `apis\<api name>\api.description.html`-si tratta la descrizione dell'API e corrispondono alla `description` proprietà dell' [entità API](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties).
-   `apis\<api name>\operations\`-Questa cartella contiene `<operation name>.description.html` file che corrispondono alle operazioni dell'API. Ogni file contiene la descrizione di una singola operazione API mappata la `description` proprietà dell' [entità operazione](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties) in API REST.

### <a name="groups-folder"></a>cartella gruppi

Il `groups` cartella contiene una cartella per ogni gruppo all'istanza del servizio.

-   `groups\<group name>\configuration.json`-si tratta della configurazione per il gruppo. Si tratta le stesse informazioni che vengono restituite se fosse necessario chiamare l'operazione di [ottenere un gruppo specifico](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) .
-   `groups\<group name>\description.html`-si tratta la descrizione del gruppo e corrispondono alla `description` proprietà dell' [entità di gruppo](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>cartella Criteri

Il `policies` cartella contiene istruzioni per i criteri per l'istanza del servizio.

-   `policies\global.xml`-contiene criteri definiti nell'ambito globale per l'istanza del servizio.
-   `policies\apis\<api name>\`-Se si dispone di alcun criterio definito nell'ambito dell'API, sono contenuti in questa cartella.
-   `policies\apis\<api name>\<operation name>\`cartella - se si dispone di alcun criterio definito nell'ambito di operazione, sono contenuti in questa cartella nel `<operation name>.xml` file che corrispondono alle istruzioni per i criteri per ogni operazione.
-   `policies\products\`-Se si dispone di tutti i criteri definiti nell'ambito del prodotto, sono contenuti in questa cartella che contiene `<product name>.xml` file che corrispondono alle istruzioni per i criteri per ogni prodotto.

### <a name="portalstyles-folder"></a>cartella portalStyles

Il `portalStyles` cartella contiene i fogli di stile e configurazione per le personalizzazioni del portale per sviluppatori per l'istanza del servizio.

-   `portalStyles\configuration.json`-contiene i nomi dei fogli di stile utilizzati dal portale per sviluppatori
-   `portalStyles\<style name>.css`-ogni `<style name>.css` file contiene gli stili per il portale per sviluppatori (`Preview.css` e `Production.css` per impostazione predefinita).

### <a name="products-folder"></a>cartella Products

Il `products` cartella contiene una cartella per ogni prodotto all'istanza del servizio.

-   `products\<product name>\configuration.json`-Questa è la configurazione del prodotto. Si tratta le stesse informazioni che vengono restituite se fosse necessario chiamare l'operazione di [ottenere un prodotto specifico](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) .
-   `products\<product name>\product.description.html`-si tratta la descrizione del prodotto e corrispondono alla `description` proprietà dell' [entità prodotto](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) in API REST.

### <a name="templates"></a>modelli

Il `templates` cartella contiene configurazione per i [modelli di posta elettronica](api-management-howto-configure-notifications.md) dell'istanza del servizio.

-   `<template name>\configuration.json`-si tratta della configurazione per il modello di messaggio di posta elettronica.
-   `<template name>\body.html`-si tratta del corpo del modello di messaggio di posta elettronica.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri modi per gestire l'istanza del servizio, vedere:

-   Gestire l'istanza del servizio utilizzando il seguente cmdlet di PowerShell
    -   [Distribuzione del servizio di riferimento sui cmdlet di PowerShell](https://msdn.microsoft.com/library/azure/mt619282.aspx)
    -   [Gestione dei servizi di riferimento sui cmdlet di PowerShell](https://msdn.microsoft.com/library/azure/mt613507.aspx)
-   Gestire l'istanza del servizio nel portale di publisher
    -   [Gestire la prima API](api-management-get-started.md)
-   Gestire l'istanza del servizio tramite l'API REST
    -   [Guida di riferimento all'API REST di gestione delle API](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## <a name="watch-a-video-overview"></a>Guardare un video introduttivo

> [AZURE.VIDEO configuration-over-git]

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




