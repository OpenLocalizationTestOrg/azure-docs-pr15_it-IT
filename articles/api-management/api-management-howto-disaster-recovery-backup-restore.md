<properties 
    pageTitle="Come implementare emergenza utilizzo del servizio backup e ripristino in Azure API Management | Microsoft Azure" 
    description="Informazioni su come utilizzare backup e ripristino per eseguire il ripristino di emergenza Azure API Management." 
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

# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Come implementare emergenza utilizzo del servizio backup e ripristino in Azure API Management

Se si sceglie di pubblicare e gestire l'API tramite Azure API Management si potrà usufruire di molti tolleranza e le funzionalità dell'infrastruttura che in caso contrario è necessario progettare e implementare gestione. La piattaforma Azure consente di ridurre gran parte della potenziali errori di una frazione del costo.

Per recuperare dalla disponibilità problemi con l'area geografica in cui la gestione di API servizio è ospitato si dovrebbero essere pronti a ricostituire il servizio in un'area diversa in qualsiasi momento. In base al obiettivi di disponibilità e l'obiettivo di tempo di ripristino desiderata prenotare un servizio di backup in uno o più aree geografiche e si cerca di mantenere sincronizzati con il servizio active loro configurazione e del contenuto. Il backup di servizio e funzionalità di ripristino fornisce il blocco predefinito necessario per l'implementazione strategia di ripristino di emergenza.

Questa guida viene illustrato come autenticare le richieste di gestione di risorse Azure e come eseguire il backup e ripristinare le istanze del servizio di gestione delle API.

>[AZURE.NOTE] Il processo di backup e ripristino di un'istanza di servizio di gestione dell'API di emergenza è utilizzabile anche per replicare le istanze del servizio di gestione di API per gli scenari, ad esempio gestione temporanea.
>
>Si noti che ogni backup scade dopo 7 giorni. Se si tenta di ripristinare una copia di backup dopo la scadenza sette giorni è scaduto, il ripristino potrebbe non riuscire con un `Cannot restore: backup expired` messaggio.

## <a name="authenticating-azure-resource-manager-requests"></a>Richiede l'autenticazione Manager delle risorse di Azure

>[AZURE.IMPORTANT] API REST di backup e ripristino Usa Gestione risorse di Azure e contiene un meccanismo di autenticazione diverso rispetto alle API REST per gestire le entità di gestione delle API. La procedura descritta in questa sezione viene descritto come eseguire l'autenticazione le richieste di Manager delle risorse di Azure. Per ulteriori informazioni, vedere [le richieste di autenticazione Manager delle risorse di Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Tutte le attività da eseguire sulle risorse tramite Gestione risorse Azure devono essere autenticate con Azure Active Directory mediante la procedura seguente.

-   Aggiungere un'applicazione nel tenant di Azure Active Directory.
-   Impostare le autorizzazioni per l'applicazione è stato aggiunto.
-   È possibile ottenere il token per l'autenticazione delle richieste di gestione di risorse di Azure.

Il primo passaggio consiste nel creare un'applicazione di Azure Active Directory. Accedere al [Portale classica Azure](http://manage.windowsazure.com/) con l'abbonamento che contiene l'istanza del servizio di gestione delle API e passare alla scheda **applicazioni** per l'impostazione predefinita Azure Active Directory.

>[AZURE.NOTE] Se la directory predefinita di Azure Active Directory non è visibile al proprio account, contattare l'amministratore della sottoscrizione Azure per concedere le autorizzazioni necessarie al proprio account. Per informazioni su come individuare la directory predefinita, vedere [individuare la directory predefinita](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-portal).

![Creare l'applicazione di Azure Active Directory][api-management-add-aad-application]

Fare clic su **Aggiungi**, **aggiungere un'applicazione di sviluppo di organizzazione**e scegliere **l'applicazione client nativi**. Immettere un nome descrittivo e fare clic sulla freccia avanti. Inserire un URL di segnaposto, ad esempio `http://resources` per **Reindirizzare URI**, come il campo è obbligatorio, ma il valore non viene utilizzato in un secondo momento. Fare clic sulla casella di controllo per salvare l'applicazione.

Dopo aver salvato l'applicazione, fare clic su **Configura**, scorrere fino alla sezione **autorizzazioni per le altre applicazioni** e fare clic su **Aggiungi applicazione**.

![Aggiungi autorizzazioni][api-management-aad-permissions-add]

Selezionare **Windows** **API di gestione del servizio di Azure** e fare clic sulla casella di controllo per aggiungere l'applicazione.

![Aggiungi autorizzazioni][api-management-aad-permissions]

Fare clic su **Autorizzazioni delegato** accanto all'applicazione di **Windows** **Azure servizio di gestione delle API** appena aggiunto, selezionare la casella di controllo per la **Gestione del servizio Access Azure (preview)**e fare clic su **Salva**.

![Aggiungi autorizzazioni][api-management-aad-delegated-permissions]

Prima di richiamare l'API che generano il backup e ripristino, è necessario ottenere un token. Nell'esempio seguente viene utilizzato il pacchetto di nuget [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) per recuperare il token.

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace GetTokenResourceManagerRequests
    {
        class Program
        {
            static void Main(string[] args)
            {
                var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
                var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

                if (result == null) {
                    throw new InvalidOperationException("Failed to obtain the JWT token");
                }

                Console.WriteLine(result.AccessToken);

                Console.ReadLine();
            }
        }
    }

Sostituire `{tentand id}`, `{application id}`, e `{redirect uri}` seguendo le istruzioni seguenti.

Sostituire `{tenant id}` con l'id tenant dell'applicazione Azure Active Directory appena creata. È possibile accedere l'id, fare clic su **endpoint di visualizzazione**.

![Punti finali][api-management-aad-default-directory]

![Punti finali][api-management-endpoint]

Sostituire `{application id}` e `{redirect uri}` utilizzando l' **Id Client** e l'URL della sezione **Reindirizzare URI** dalla scheda **configurazione** dell'applicazione di Azure Active Directory. 

![Risorse][api-management-aad-resources]

Una volta specificati i valori, nell'esempio di codice deve restituire un token simili all'esempio seguente.

![Token][api-management-arm-token]

Prima di chiamare le operazioni di backup e ripristino descritte nelle sezioni seguenti, impostare l'intestazione di richiesta di autorizzazione per le chiamate resto.

    request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);

## <a name="step1"> </a>Un servizio di gestione delle API di backup
Eseguire il backup di una richiesta di gestione dell'API del servizio problema HTTP seguenti:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

dove:

* `subscriptionId`-id della sottoscrizione contenente il servizio di gestione di API che si sta tentando di backup
* `resourceGroupName`-una stringa sotto forma di 'Api - predefinito-{servizio area}' in `service-region` identifica l'area di Azure Management API service nel punto in cui si sta tentando di backup è ospitato, ad esempio`North-Central-US`
* `serviceName`-il nome del servizio di gestione di API per eseguire un backup del specificato al momento della creazione
* `api-version`-Sostituisci`2014-02-14`

Nel corpo della convocazione, specificare il nome di account di destinazione di archiviazione Azure, tasto di scelta rapida, nome del contenitore di blob e nome backup:

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Impostare il valore della `Content-Type` intestazione richiesta per `application/json`.

Copia di backup è un'operazione lunga che può richiedere più minuti.  Se la convocazione è stata eseguita correttamente e il processo di backup è iniziato si riceverà un `202 Accepted` codice di stato risposta con un `Location` intestazione.  Assicurarsi di ottenere le richieste di URL nel `Location` intestazione per verificare lo stato dell'operazione. Mentre è in corso il backup si continuerà a essere visualizzato un codice di stato '202 accettato'. Un codice di risposta `200 OK` indica il completamento dell'operazione backup.

**Nota**:

- **Contenitore** specificato nella richiesta corpo **deve essere presente**.
* Durante l'esecuzione di backup si **non tentare le operazioni di gestione del servizio** , ad esempio SKU aggiornare o eseguire il downgrade, modifica del nome di dominio e così via. 
* Ripristino di una **copia di backup garantito solo per 7 giorni** rispetto al momento della creazione. 
* **Dati di utilizzo** per la creazione di analitica report **non incluso** nel backup. Consente di recuperare periodicamente i report analitica per custodia [API REST di Azure API Gestione][] .
* Specificare la frequenza con cui eseguire il backup di servizio viene influenzata dalle obiettivo il punto di ripristino. Per ridurre al minimo si consiglia di implementazione di backup regolari, nonché l'esecuzione di backup su richiesta dopo avere apportato modifiche più importanti apportate al servizio di gestione delle API.
* **Le modifiche** apportate alla configurazione del servizio (ad esempio, API, criteri, aspetto portale per sviluppatori) durante l'operazione di backup è nel processo **potrebbero non essere inclusi nel backup e pertanto andranno perse**.

## <a name="step2"> </a>Ripristinare un servizio di gestione delle API
Per ripristinare una gestione API servizio da un backup creato in precedenza effettuare la richiesta HTTP seguente:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

dove:

* `subscriptionId`-id della sottoscrizione contenente il servizio di gestione dell'API si ripristina un backup in
* `resourceGroupName`-una stringa sotto forma di 'Api - predefinito-{servizio area}' in `service-region` identifica l'area geografica di Azure nel punto in cui il servizio di gestione dell'API si ripristina un backup in ospitato, ad esempio`North-Central-US`
* `serviceName`-il nome di gestione dell'API del servizio in corso il ripristino in specificato al momento della creazione
* `api-version`-Sostituisci`2014-02-14`

Nel corpo della convocazione, specificare il percorso di file di backup, ad esempio nome dell'account di archiviazione Azure, tasto di scelta rapida, nome del contenitore di blob e nome backup:

    '{  
        storageAccount : {storage account name for the backup},  
        accessKey : {access key for the account},  
        containerName : {backup container name},  
        backupName : {backup blob name}  
    }'

Impostare il valore della `Content-Type` intestazione richiesta per `application/json`.

Ripristino è un'operazione lunga che potrebbe richiedere fino a 30 minuti completare.  Se la convocazione è stata eseguita correttamente e il processo di ripristino è stato avviato si riceverà un `202 Accepted` codice di stato risposta con un `Location` intestazione.  Assicurarsi di ottenere le richieste di URL nel `Location` intestazione per verificare lo stato dell'operazione. Durante il ripristino in corso si continueranno a ricevere '202 accettato' codice di stato. Un codice di risposta `200 OK` indicano completamento dell'operazione di ripristino.

>[AZURE.IMPORTANT] **SKU** del servizio ripristinato in **deve corrispondere** SKU del servizio di backup da ripristinare.
>
>**Le modifiche** apportate alla configurazione del servizio (ad esempio, API, criteri, aspetto portale per sviluppatori) durante l'operazione di ripristino è in corso **potrebbero essere sovrascritti**.

## <a name="next-steps"></a>Passaggi successivi
Consultare il blog di Microsoft seguenti per due procedure dettagliate diverse del processo di backup e ripristino.

-   [Replicare API Azure Gestione account](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/) 
    -   Grazie a Gisela per il contributo di questo articolo.
-   [Gestione di Azure API: Backup e ripristino della configurazione](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    -   L'approccio dettagliato da Stuart non corrisponde a indicazioni ufficiale ma è molto interessante.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Gestione di Azure API API REST]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
 
