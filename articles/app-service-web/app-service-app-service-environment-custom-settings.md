<properties
    pageTitle="Impostazioni personalizzate per gli ambienti servizio App"
    description="Impostazioni di configurazione personalizzate per gli ambienti servizio App"
    services="app-service"
    documentationCenter=""
    authors="stefsch"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="stefsch"/>

# <a name="custom-configuration-settings-for-app-service-environments"></a>Impostazioni di configurazione personalizzate per gli ambienti servizio App

## <a name="overview"></a>Panoramica ##
Poiché App servizio ambienti sono isolati a un singolo cliente, esistono alcune impostazioni di configurazione che possono essere applicate esclusivamente ad App servizio ambienti. In questo articolo vengono illustrate diverse personalizzazioni specifiche disponibili per gli ambienti servizio App.

Se non si dispone di un ambiente App, vedere [come creare un ambiente App](app-service-web-how-to-create-an-app-service-environment.md).

È possibile memorizzare le personalizzazioni ambiente servizio App utilizzando una matrice nell'attributo **clusterSettings** nuovo. Questo attributo è disponibile nel dizionario "Proprietà" di *hostingEnvironments* Manager delle risorse Azure entità.

Il frammento di modello Manager delle risorse abbreviato seguente mostra l'attributo **clusterSettings** :


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

L'attributo **clusterSettings** può essere inclusi in un modello di Manager delle risorse per aggiornare l'ambiente di servizio App.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Utilizzare Esplora risorse Azure per aggiornare un ambiente App
In alternativa, è possibile aggiornare l'ambiente di servizio App tramite [Esplora risorse Azure](https://resources.azure.com).  

1. In Esplora risorse, passare al livello di nodo per l'ambiente di servizio App (**abbonamenti** > **resourceGroups** > **provider** > **Micrososft.Web** > **hostingEnvironments**). Fare clic sull'ambiente di servizio App specifici che si desidera aggiornare.

2. Nel riquadro destro fare clic su **Lettura/scrittura** nella barra superiore per consentire interattivi modifica in Esplora risorse.  

3. Fare clic su blu **Modifica** pulsante per rendere il modello di gestione risorse modificabili.

4. Scorrere verso il basso del riquadro di destra. L'attributo **clusterSettings** è nella parte inferiore, in cui è possibile immettere o aggiornare il relativo valore.

5. Digitare (o copiare e incollare) la matrice di valori di configurazione desiderata nell'attributo **clusterSettings** .  

6. Fare clic su verde **inserire** pulsante che sono presenti nella parte superiore del riquadro di destra per eseguire il commit le modifiche all'ambiente di servizio di App.

Tuttavia, si invia la modifica, bastano circa 30 minuti moltiplicati per il numero di front-end nell'ambiente di servizio di App per la modifica abbia effetto.
Ad esempio, se un ambiente App include quattro front-end, sarà necessaria circa due ore per l'aggiornamento di configurazione completare. Durante la modifica di configurazione software, nessun altro operazioni di ridimensionamento o operazioni di modifica di configurazione possono avvenire nell'ambiente di servizio di App.

## <a name="disable-tls-10"></a>Disabilitare TLS 1.0 ##
Una domanda ricorrente dai clienti, in particolare i clienti che utilizzano conformità PCI i controlli, eseguire la procedura disattivare esplicitamente TLS 1.0 per le applicazioni.

TLS 1.0 può essere disattivata la voce **clusterSettings** seguenti:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Modifica ordine famiglia di prodotti di crittografia TLS ##
Un'altra domanda dai clienti è se è possibile modificare l'elenco di cifre negoziata dal loro server e questo può essere ottenuto modificando **clusterSettings** come illustrato di seguito. Possibile recuperare l'elenco di gruppi di crittografia disponibili dal [in questo articolo MSDN] (https://msdn.microsoft.com/library/windows/desktop/aa374757(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [AZURE.WARNING]  Se valori non corretti sono impostati per il gruppo di crittografia che non conoscono SChannel, tutte le comunicazioni TLS al server potrebbero interrompersi. In questo caso, sarà necessario rimuovere la voce *FrontEndSSLCipherSuiteOrder* da **clusterSettings** e inviare il modello di gestione risorse aggiornato per ripristinare le impostazioni di famiglia di prodotti di crittografia predefinite.  Utilizzare questa funzionalità con cautela.

## <a name="get-started"></a>Guida introduttiva
Il modello di sito di gestione risorse Guida introduttiva di Azure include un modello con la definizione di base per la [creazione di un ambiente App](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).


<!-- LINKS -->

<!-- IMAGES -->
