
<properties
    pageTitle="Caricare un'immagine personalizzata per Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come caricare un'immagine personalizzata per RemoteApp di Azure"
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="ericor" />



# <a name="upload-a-custom-image-for-azure-remoteapp"></a>Caricare un'immagine personalizzata per RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Ora che è stato creato l'immagine di modello personalizzato o sono aggiornati con le modifiche, è necessario caricare l'immagine nella raccolta immagine RemoteApp di Azure. Seguire questi passaggi.


## <a name="before-you-start"></a>Prima di iniziare

1.      Verificare che l'immagine personalizzata soddisfi i [requisiti di immagini](remoteapp-imagereqs.md) e i [requisiti dell'applicazione](remoteapp-appreqs.md).
2.      Installare il [modulo di Azure PowerShell](../powershell-install-configure.md).

## <a name="step-by-step-on-how-to-upload-custom-image"></a>Procedura dettagliata su come caricare immagine personalizzata

1.      Aprire il portale di gestione di Azure e passare alla pagina RemoteApp.
2.      Nella scheda **le immagini dei modelli** , fare clic su **Carica** nella parte inferiore della pagina.
4.      Immettere un nome descrittivo per l'immagine e specificare il percorso di account di archiviazione. Assicurarsi che deve trovarsi nella stessa posizione come insieme RemoteApp o un percorso in cui si desidera crearne uno.
5.      Quando richiesto, scaricare lo script al PC locale.
6.      Copiare i parametri del comando nella casella di testo negli Appunti.
7.      Aprire una finestra di Windows PowerShell privilegi elevati.
8.      Nella finestra di Windows PowerShell con privilegi elevata passare nella stessa cartella in cui è stato scaricato lo script.
9.      Incollare il comando copiato e premere **INVIO**.

    Il processo di caricamento, verrà avviata e durata può dipendere da diversi fattori, tra cui la velocità della rete e le dimensioni dell'immagine

11.    Se il caricamento non riesce a causa di interruzione della rete o elementi simili, è sempre possibile riprendere il processo di caricamento che si è iniziato. Per riprendere il caricamento, eseguire lo script usando la stessa riga di comando.

> [AZURE.WARNING] Non è mai modificare lo script di caricamento. Controlli specifici sono stati per assicurarsi che l'immagine soddisfi i requisiti di immagini e i requisiti dell'applicazione.

## <a name="common-problems"></a>Problemi comuni

- Assicurarsi di utilizzare Windows PowerShell, non Azure PowerShell. È necessario installare il modulo di Azure PowerShell perché alcuni moduli sono necessari durante il processo di caricamento.
- Non è mai modificare lo script, convalida esistono alle esigenze.
- Se il file disco rigido virtuale viene bloccato durante il caricamento, copiare il file o spostarlo in un nuovo percorso e il tentativo di caricamento. Potrebbe essere un processo di Windows che impedisce carica.  
