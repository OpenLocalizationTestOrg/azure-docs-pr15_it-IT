<properties 
    pageTitle="Una distribuzione di servizio cloud (Node) sul passaggio | Microsoft Azure" 
    description="Informazioni su come distribuire l'applicazione Azure in un ambiente di gestione temporanea, quindi distribuire in un ambiente di produzione utilizzando Scambia virtuale indirizzi IP." 
    services="cloud-services" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>



# <a name="staging-an-application-in-azure"></a>Gestione temporanea di un'applicazione di Azure

Distribuire un'applicazione nel pacchetto per l'ambiente di gestione temporanea Azure testata prima di spostarlo all'ambiente di produzione in cui l'applicazione è accessibile su Internet. Ambiente di gestione temporanea è esattamente come l'ambiente di produzione, tranne per il fatto che è possibile accedere solo l'applicazione a fasi con un URL offuscato generato da Azure. Dopo aver verificato il corretto funzionamento dell'applicazione, può essere distribuito nell'ambiente di produzione eseguendo scambio virtuale indirizzi IP.

> [AZURE.NOTE] La procedura descritta in questo articolo vengono applicate solo alle applicazioni di nodo ospitate come un servizio Cloud di Azure.

## <a name="step-1-stage-an-application"></a>Passaggio 1: Inserisce un'applicazione

Questa attività illustra come inserisce un'applicazione tramite **Microsoft Azure PowerShell**.

1.  Quando un servizio di pubblicazione, è sufficiente passare la **-intervallo aperto** parametro per il cmdlet **AzureServiceProject pubblica** .

    ```powershell
    Publish-AzureServiceProject -Slot staging
    ```

2.  Accedere al [portale classica Azure] e selezionare **Servizi Cloud**. Dopo il servizio cloud viene creato e lo stato di colonna di **gestione temporanea** è stato aggiornato sia in **esecuzione**, fare clic sul nome del servizio.

    ![portale la visualizzazione di un servizio in esecuzione][cloud-service]

3.  Selezionare il **Dashboard**e quindi selezionare **gestione temporanea**.

    ![dashboard del servizio cloud][cloud-service-dashboard]

4. Osservare il valore della voce **URL del sito** a destra. Il nome DNS è un ID interno offuscato che ha generato Azure.

    ![url del sito][cloud-service-staging-url]

A questo punto è possibile verificare che l'applicazione funziona correttamente nell'ambiente di gestione temporanea utilizzando l'URL del sito di gestione temporanea.

## <a name="step-2-upgrade-an-application-in-production-by-swapping-vips"></a>Passaggio 2: Aggiornare un'applicazione di produzione da VIP scambio

Dopo aver verificato la versione aggiornata di un'applicazione nell'ambiente di gestione temporanea, è possibile impostarlo disponibili in produzione scambiando gli indirizzi IP virtuale (VIP) degli ambienti di prova e di produzione.

> [AZURE.NOTE] Questo passaggio presuppone che hanno distribuito un'applicazione di produzione e in più fasi la versione aggiornata dell'applicazione.

1.  Accedere al [portale classica Azure], fare clic su **Servizi Cloud** e quindi selezionare il nome del servizio.

2.  Dal **Dashboard**, selezionare **gestione temporanea**e quindi fare clic su **Scambia** nella parte inferiore della pagina. Verrà aperta la finestra di dialogo Scambia VIP.

    ![finestra di dialogo Scambia VIP][vip-swap-dialog]

3.  Esaminare le informazioni e quindi fare clic su **OK**. Due distribuzioni iniziano l'aggiornamento della distribuzione di gestione temporanea passa alla produzione e la distribuzione di produzione passa alla gestione temporanea.

Una distribuzione in più fasi e aggiornamento di una distribuzione di produzione scambiando VIP con la distribuzione di gestione temporanea completata.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Come distribuire un aggiornamento del servizio di produzione scambiando VIP in Azure]

[Portale classica Azure]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
[cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
[vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
[Come distribuire un aggiornamento del servizio di produzione scambiando VIP in Azure]: cloud-services-how-to-manage.md#how-to-swap-deployments-to-promote-a-staged-deployment-to-production
