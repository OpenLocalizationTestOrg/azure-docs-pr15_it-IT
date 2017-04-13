<properties
    pageTitle="Consentire a un'applicazione di informazioni riservate Azure Stack chiave archivio revtrieve | Microsoft Azure"
    description="Utilizzare un'applicazione di esempio per lavorare con Azure Stack chiave archivio"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="run-the-sample-application-for-key-vault"></a>Eseguire l'applicazione di esempio per chiave archivio 

In questa guida è necessario utilizzare un'applicazione di esempio per recuperare informazioni riservate e la password dall'archivio di chiave.

## <a name="download-the-samples-and-prepare"></a>Scaricare gli esempi e preparare

Scaricare gli esempi di client Azure chiave archivio dalla [pagina degli esempi di Azure chiave archivio client](https://www.microsoft.com/en-us/download/details.aspx?id=45343).

Estrarre il contenuto del file ZIP nel computer locale.

Leggere il file **README.md** (si tratta di un file di testo) e quindi seguire le istruzioni.

## <a name="run-sample-1--hellokeyvault"></a>Eseguire l'esempio #1 - HelloKeyVault
HelloKeyVault è un'applicazione console che illustra gli scenari principali supportate da archivio chiave:

  1. Creare/Importa una chiave (modulo di sicurezza hardware o software)
  2. Crittografare un segreto utilizzando una chiave del contenuto
  3. Disporre la chiave del contenuto utilizzando una chiave di archivio di chiave
  4. Annullare la chiave del contenuto
  5. Decrittografare il segreto
  6. Impostare un segreto

Applicazione console deve essere eseguita senza modifiche, ad eccezione del fatto che le impostazioni di configurazione appropriato nell'App verranno aggiornate in base alla seguente procedura:

1. Aggiornare le impostazioni di configurazione app in HelloKeyVault\App.config con l'URL di archivio, ID principale dell'applicazione e segreto. Le informazioni possono essere generate facoltativamente utilizzando **scripts\GetAppConfigSettings.ps1**.
2. Aggiornare i valori delle variabili obbligatorie nella GetAppConfigSettings.ps1.
3. Aprire la finestra di Windows PowerShell.
4. Eseguire lo script GetAppConfigSettings.ps1 all'interno della finestra di PowerShell.
5. Copiare i risultati dello script al file HelloKeyVault\App.config.


## <a name="next-steps"></a>Passaggi successivi

[Distribuire una macchina virtuale con una password di archivio di chiave](azure-stack-kv-deploy-vm-with-secret.md)

[Distribuire una macchina virtuale con un certificato chiave archivio](azure-stack-kv-push-secret-into-vm.md)