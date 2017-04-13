<properties
    pageTitle="Utilizzare azioni autoscale per inviare posta elettronica e webhook le notifiche di avviso. | Microsoft Azure"
    description="Informazioni su come utilizzare azioni autoscale per chiamare URL web o inviare notifiche tramite posta elettronica in Azure Monitor. "
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="ashwink"/>

# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Usare azioni autoscale per inviare posta elettronica e webhook le notifiche di avviso in Azure Monitor

Questo articolo descrive come impostare i trigger in modo che è possibile chiamare URL web specifico o inviare messaggi di posta elettronica in base alle azioni autoscale in Azure.  

## <a name="webhooks"></a>Webhooks
Webhooks consentono di inviare le notifiche di avviso Azure ad altri sistemi per le notifiche di post-elaborazione o personalizzate. Routing, ad esempio, l'avviso per i servizi in grado di gestire una richiesta web in arrivo per inviare che una notifica SMS, log degli errori, servizi di un team chat di messaggistica e così via. Webhook URI deve essere un endpoint HTTP o HTTPS valido.

## <a name="email"></a>Posta elettronica
Messaggio di posta elettronica da inviare a qualsiasi indirizzo di posta elettronica valido. Gli amministratori e creazione della sottoscrizione in cui viene eseguita la regola anche essere avvisati.


## <a name="cloud-services-and-web-apps"></a>Servizi cloud e Web Apps
È possibile scegliere di dal portale di Azure per servizi Cloud e Server farm (Web Apps).

- Scegliere l'unità di misura metriche **scala da** .

![scalare di](./media/insights-autoscale-to-webhook-email/insights-autoscale-scale-by.png)

## <a name="virtual-machine-scale-sets"></a>Set di scala macchina virtuale
Per più recente macchine virtuali creati con Gestione risorse (set scala macchina virtuale), è possibile configurare questa utilizzando API REST, modelli di Manager delle risorse, PowerShell e CLI. Un'interfaccia portale non sono ancora disponibile.
Quando si usa il modello API REST o Manager delle risorse, includere l'elemento di notifiche con le opzioni seguenti.

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
|Campo                              |Obbligatorio? |Descrizione|
|---                                |---        |---|
|operazione                          |Sì        |valore deve essere "Scala"|
|sendToSubscriptionAdministrator    |Sì        |valore deve essere "true" o "false"|
|sendToSubscriptionCoAdministrators |Sì        |valore deve essere "true" o "false"|
|customEmails                       |Sì        |valore può essere null [] o matrice di stringhe dei messaggi di posta elettronica|
|webhooks                           |Sì        |valore può essere Uri null o valido|
|serviceUri                         |Sì        |un valido https Uri|
|proprietà                         |Sì        |valore deve essere {} vuota o può contenere coppie di parole chiave valore|


## <a name="authentication-in-webhooks"></a>Autenticazione di webhooks
Esistono due tipi di autenticazione URI:

1. Autenticazione di base token, in cui si salva webhook URI con un ID token come un parametro di query. Ad esempio https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue
2. Autenticazione di base, in cui si usa un ID utente e password. Per esempiohttps://userid:password@mysamplealert/webcallback?someparamater=somevalue&parameter=value

## <a name="autoscale-notification-webhook-payload-schema"></a>Schema di payload AutoScale notifica webhook
Quando viene generata la notifica autoscale, i metadati seguenti sono incluso nel payload webhook:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


|Campo  |Obbligatorio?|    Descrizione|
|---|---|---|
|stato |Sì    |Lo stato che indica che è stata generata un'azione autoscale|
|operazione| Sì |Per un aumento delle istanze, sarà "Scale Out" e per una riduzione delle istanze, sarà "Scala In"|
|scelta rapida|   Sì |Il contesto di azione autoscale|
|timestamp| Sì |Indicatore di data e ora quando è stata attivata l'azione autoscale|
|ID |Sì|   Manager delle risorse ID dell'impostazione autoscale|
|nome   |Sì|   Il nome dell'impostazione autoscale|
|Dettagli|   Sì |Descrizione dell'azione che il servizio autoscale ha e la modifica nel conteggio delle istanze|
|subscriptionId|    Sì |ID abbonamento della risorsa di destinazione che viene eseguita la scala|
|resourceGroupName| Sì|    Nome del gruppo di risorse della risorsa di destinazione che viene eseguita la scala|
|resourceName   |Sì|   Nome della risorsa di destinazione che viene eseguita la scala|
|tipo di risorsa   |Sì|   I tre valori supportati: "microsoft.classiccompute/domainnames/slots/roles" - ruoli servizio Cloud, "microsoft.compute/virtualmachinescalesets" - macchina virtuale scala set e "Microsoft.Web/serverfarms" - Web App|
|resourceId |Sì|Manager delle risorse ID risorsa di destinazione che viene eseguita la scala|
|portalLink |Sì    |Azure portale collegamento alla pagina di riepilogo della risorsa di destinazione|
|oldCapacity|   Sì |Il corrente (precedente) conteggio delle istanze quando Autoscale ha un'azione di scala|
|newCapacity|   Sì |Nuovo numero di istanza Autoscale architetture la risorsa|
|Proprietà|    No| Facoltativo. Insieme di < chiave, valore > coppie di parole (ad esempio dizionario < String, String >). Il campo proprietà è facoltativo. In un'interfaccia utente personalizzato o un flusso di lavoro app in base a logica, è possibile immettere chiavi e i valori che è possono passare usando il payload. In alternativa per passare proprietà personalizzate alla chiamata in uscita webhook consiste nell'utilizzare webhook URI stesso (come parametri query)|
