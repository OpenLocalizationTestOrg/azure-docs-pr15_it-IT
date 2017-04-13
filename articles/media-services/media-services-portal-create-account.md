<properties
    pageTitle=" Creare un account di servizi multimediali di Windows Azure con il portale di Azure | Microsoft Azure"
    description="In questa esercitazione sono illustrati i passaggi della creazione di un account di servizi multimediali di Windows Azure con il portale di Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Creare un account di servizi multimediali di Windows Azure tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [RESTO](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Per completare questa esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

Portale di Azure consente di creare rapidamente un account Azure Media Services (AMS). È possibile usare l'account per accedere ai servizi di supporto che consentono di memorizzare, crittografare, codificare, gestire e trasmettere contenuti multimediali in Azure. Al momento si crea un account di servizi di supporto, anche creare un account di archiviazione associato (o utilizzarne una esistente) nella stessa area geografica come l'account di servizi di supporto.

In questo articolo vengono illustrati alcuni concetti comuni e viene illustrato come creare un account di servizi multimediali con il portale di Azure.

## <a name="concepts"></a>Concetti

Accesso ai servizi di supporto richiede due account associato:

- Un account di servizi di supporto. L'account consente di accedere a un insieme di servizi multimediali basate su cloud che sono disponibili in Azure. Un account di servizi multimediali non archiviare contenuti multimediali effettivo. Archivia piuttosto metadati sui contenuti e processi di elaborazione media nell'account. Al momento che si crea l'account, selezionare un'area di servizi multimediali disponibile. Il paese selezionato è un data center in cui sono memorizzati i record dei metadati per il proprio account.

    Di seguito sono indicate le aree disponibili servizi multimediali (AMS): Europa Nord America, Europa occidentale, ovest degli Stati Uniti, Stati Uniti orientali, Sudest asiatico, Asia orientale, ovest, Giappone Giappone est. Servizi multimediali di non utilizzare i gruppi di affinità.
    
    AMS è ora disponibile anche nei seguenti data center: Brasile sud ovest India, India Sud e India centrale. È ora possibile usare il portale di Azure per creare gli account del servizio di supporto ed eseguire varie attività descritte in questo documento. Tuttavia, codifica Live non è abilitata in questi centri dati. Inoltre, non tutti i tipi di codifica riservato unità sono disponibili in tali centri.
    
    - Brasile Sud: Solo Standard e unità riservato di codifica di base sono disponibili.
    - India-ovest, India Sud: 

- Un account di archiviazione Azure. Gli account di archiviazione devono trovarsi nella stessa area geografica come l'account di servizi di supporto. Quando si crea un account di servizi di supporto, è possibile scegliere un account di archiviazione esistente nella stessa regione oppure è possibile creare un nuovo account di archiviazione nella stessa regione. Se si elimina un account di servizi multimediali, BLOB nell'account correlati dello spazio di archiviazione non vengono eliminati.

## <a name="create-an-ams-account"></a>Creare un account AMS

La procedura descritta in questa sezione viene illustrato come creare un account AMS.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **+ nuova** > **Web + Mobile** > **Servizi multimediali**.

    ![Creazione di servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. **Creare** account servizi multimediali immettere valori obbligatori.

    ![Creazione di servizi multimediali](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. In **Nome Account**immettere il nome del nuovo account AMS. Il nome di un account di servizi multimediali è tutto minuscole caratteri alfanumerici senza spazi e caratteri da 3 a 24.
    2. In abbonamento, scegliere tra i diversi abbonamenti Azure che è possibile accedere a.
    
    2. Nel **Gruppo di risorse**, selezionare la risorsa nuova o esistente.  Un gruppo di risorse è una raccolta di risorse che condividono ciclo di vita, autorizzazioni e criteri. Altre informazioni [di seguito](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. In **posizione**, selezionare l'area geografica che verrà utilizzato per memorizzare i record dei metadati ed elementi multimediali per il proprio account di servizi di supporto. Quest'area verrà utilizzata per elaborare e trasmettere file multimediali. Solo le aree di servizi multimediali disponibili vengono visualizzate nella casella di riepilogo a discesa. 
    
    3. In **Account di archiviazione**, selezionare un account di archiviazione per fornire l'archiviazione blob del contenuto multimediale dal proprio account di servizi di supporto. È possibile selezionare un account di archiviazione esistente nella stessa area geografica dell'account di servizi multimediali oppure è possibile creare un account di archiviazione. Viene creato un nuovo account di archiviazione nella stessa regione. Le regole per i nomi degli account di archiviazione sono gli stessi account servizi di supporto.

        Altre informazioni sull'archiviazione [di seguito](storage-introduction.md).

    4. Selezionare **Aggiungi a dashboard** per visualizzare l'avanzamento della distribuzione account.
    
7. Fare clic su **Crea** nella parte inferiore del modulo.

    Una volta l'account è stata creata, viene modificato lo stato sia in **esecuzione**. 

    ![Impostazioni di servizi di supporto](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Per gestire il proprio account AMS (ad esempio, caricare video, codificare risorse, monitorare l'avanzamento di processo) utilizzare la finestra **Impostazioni** .

## <a name="manage-keys"></a>La gestione delle chiavi

È necessario il nome dell'account e le informazioni chiave primarie a livello di programmazione accesso all'account di servizi di supporto.

1. Nel portale di Azure, selezionare il proprio account. 

    Sul lato destro viene visualizzata la finestra di **Impostazioni** . 

2. Nella finestra **Impostazioni** selezionare **chiavi**. 

    Windows **Gestisci chiavi** Mostra il nome dell'account e chiavi primarie e secondarie viene visualizzato. 
3. Fare clic sul pulsante Copia per copiare i valori.
    
    ![Tasti di servizi multimediali di Microsoft](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile caricare file nel proprio account AMS. Per ulteriori informazioni, vedere [caricare i file](media-services-portal-upload-files.md).

## <a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


