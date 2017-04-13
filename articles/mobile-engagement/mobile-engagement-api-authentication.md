<properties 
    pageTitle="Eseguire l'autenticazione con coinvolgimento Mobile API REST"
    description="Viene descritto come eseguire l'autenticazione con API REST coinvolgimento di Azure Mobile" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/05/2016"
    ms.author="wesmc;ricksal"/>

# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Eseguire l'autenticazione con coinvolgimento Mobile API REST

## <a name="overview"></a>Panoramica

In questo documento viene descritto come ottenere un valido Oauth AAD token per eseguire l'autenticazione con le API REST di impegno Mobile. 

Si presuppone che si dispone di un abbonamento valido a Azure e un'app Mobile coinvolgimento utilizzando uno dei nostro [Sviluppo esercitazioni](mobile-engagement-windows-store-dotnet-get-started.md)è stata creata.

## <a name="authentication"></a>Autenticazione

Microsoft Azure Active Directory in base a OAuth token viene utilizzato per l'autenticazione. 

In ordine alla richiesta di autenticazione un'API, è necessario aggiungere un'intestazione di autorizzazione per tutte le richieste dei seguenti:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] I token di Azure Active Directory scadono in 1 ora.

Esistono diversi modi per ottenere un token. Poiché le API vengono in genere chiamate da un servizio cloud, si desidera utilizzare una chiave dell'API. Una chiave API terminologia Azure viene definita una password principale del servizio. La procedura seguente descrive un modo per la configurazione manuale.

### <a name="one-time-setup-using-script"></a>Copia unica il programma di installazione (script)

È necessario seguire il set di istruzioni riportate di seguito per eseguire la configurazione utilizzando uno script di PowerShell che richiede tempo minimo per l'installazione, ma utilizza le impostazioni predefinite di massimo consentite. Facoltativamente, è possibile seguire le istruzioni di [configurazione manuale](mobile-engagement-api-authentication-manual.md) per eseguire questa operazione dal portale di Azure direttamente e apportare modifiche alla configurazione più dettagliato. 

1. È possibile ottenere la versione più recente di Azure PowerShell dal [qui](http://aka.ms/webpi-azps). Per ulteriori informazioni sulle istruzioni di download, è possibile visualizzare il [collegamento](../powershell-install-configure.md).  

2. Dopo aver installato PowerShell di Azure, utilizzare i comandi seguenti per assicurarsi di avere installato il **modulo di Azure** :

    un. Verificare che il modulo di Azure PowerShell è disponibile nell'elenco dei moduli disponibili. 
    
        Get-Module –ListAvailable 

    ![Moduli di Azure disponibili][1]
        
    b. Se non si trova il modulo di Azure PowerShell dell'elenco è necessario eseguire le operazioni seguenti:
        
        Import-Module Azure 
        
3. Accesso a Gestione risorse Azure da PowerShell per eseguire il comando seguente e fornire il nome utente e la password dell'account Azure: 
        
        Login-AzureRmAccount

4. Se si hanno più abbonamenti deve eseguire le operazioni seguenti:

    un. Ottenere un elenco di tutte le sottoscrizioni e copiare il SubscriptionId dell'abbonamento a cui che si desidera utilizzare. Verificare che l'abbonamento è uguale a quello che è l'App coinvolgimento Mobile che si sta per interagire con le API. 

        Get-AzureRmSubscription

    b. Eseguire il seguente comando fornire il SubscriptionId per configurare l'abbonamento da utilizzare.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>

5. Copiare il testo per lo script [AzureRmServicePrincipalOwner.ps1 di nuovo](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) al computer locale e salvarlo come un cmdlet di PowerShell (ad esempio `APIAuth.ps1`) ed eseguirlo `.\APIAuth.ps1`. 
    
6. Lo script verrà chiesto di specificare un input per **principalName**. Specificare un nome adeguato che si desidera utilizzare per creare l'applicazione di Active Directory (ad esempio APIAuth). 

7. Al termine dell'esecuzione script, verrà visualizzato quanto segue quattro valori che è necessario eseguire l'autenticazione a livello di programmazione con Active Directory, pertanto fare attenzione per copiarli. 
        
    **TenantId**, **SubscriptionId**, **ID applicazione**e **segreto**.

    Si utilizzerà TenantId come `{TENANT_ID}`, ID applicazione come `{CLIENT_ID}` e segreta come `{CLIENT_SECRET}`.

    > [AZURE.NOTE] I criteri di sicurezza predefiniti potrebbero impedire l'esecuzione di script di PowerShell. In caso affermativo, è temporaneamente configurare i criteri di esecuzione per consentire l'esecuzione di script tramite il comando seguente:

        > Set-ExecutionPolicy RemoteSigned

8. Ecco come l'insieme di cmdlet PS risulterebbe simile. 

    ![][3]

9. Selezionare nel portale di gestione di Azure che è stata creata una nuova applicazione di Active Directory con il nome specificato per lo script denominato **principalName** in **che applicazioni Mostra società proprietario**.

    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>Passaggi per ottenere un token valido

1. Chiamare l'API con i parametri seguenti e assicurarsi di sostituire il TENANT\_ID, CLIENT\_ID e CLIENT\_segreto:

    - **URL richiesta** come *https://login.microsoftonline.com/ {TENANT\_ID} / oauth2/token*
    - **Tipo di contenuto HTTP intestazione** come *applicazione/x-www-form-urlencoded*
    - **Corpo della richiesta HTTP** come *concedere\_tipo = client\_credenziali & client_id = {CLIENT\_ID} & client_secret = {CLIENT\_segreto} & resource=https%3A%2F%2Fmanagement.core.windows.net%2F*

    Di seguito è una richiesta di esempio:

        POST /{TENANT_ID}/oauth2/token HTTP/1.1
        Host: login.microsoftonline.com
        Content-Type: application/x-www-form-urlencoded
        grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
        urce=https%3A%2F%2Fmanagement.core.windows.net%2F

    Ecco una risposta di esempio:

        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Content-Length: 1234
    
        {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
        5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}

    In questo esempio viene inclusa la codifica URL dei parametri POST `resource` valore sia effettivamente `https://management.core.windows.net/`. Prestare attenzione a URL anche codificare `{CLIENT_SECRET}` , che potrebbe contenere caratteri speciali.

    > [AZURE.NOTE] Per la verifica, è possibile usare uno strumento client HTTP come [Fiddler](http://www.telerik.com/fiddler) o [estensione Postman Chrome](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 

2. A questo punto ogni chiamata all'API di includere l'intestazione della richiesta di autorizzazione:

        Authorization: Bearer {ACCESS_TOKEN}

    Se viene visualizzato un codice di 401 stato restituito, selezionare il corpo della risposta, potrebbe indicare la il token è scaduto. In questo caso, ottenere un nuovo token.

##<a name="using-the-apis"></a>Utilizzo delle API

Dopo aver creato un token valido, si è pronti effettuare chiamate API.

1. In ogni richiesta API, sarà necessario passare un token valido, in corso che è stato acquistato nella sezione precedente.

2. È necessario collegare alcuni parametri nella richiesta URI che identifica l'applicazione. La richiesta URI simile al seguente

        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

    Per ottenere i parametri fare clic con il nome dell'applicazione e fare clic su Dashboard e si verrà visualizzata una pagina simile alla seguente in tutti i 3 parametri.

    - **1**`{subscription-id}`
    - **2**`{app-collection}`
    - **3**`{app-resource-name}`
    - **4** il nome del gruppo di risorse agirà da **MobileEngagement** a meno che non è stato creato un nuovo PIN. 

    ![Parametri mobile coinvolgimento API URI][2]

>[AZURE.NOTE] <br/>
>1. Ignorare l'indirizzo di radice API venisse eseguito il seguente per le API precedente.<br/>
>2. Se è stato creato l'app portale Azure classica è necessario usare un nome di risorsa dell'applicazione che è diverso dal nome applicazione stesso. Se è stato creato l'app nel portale di Azure è necessario usare il nome dell'App stesso (non vi è alcuna distinzione tra nome della risorsa applicazione e nome dell'App per App create nel portale di nuovo).  

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



