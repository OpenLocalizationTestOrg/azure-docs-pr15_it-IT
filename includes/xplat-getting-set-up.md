<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli"></a>Usa CLI Azure

I passaggi seguenti consentono di utilizzare Azure CLI facilmente con la versione più recente e la sottoscrizione corretto. Se è necessario installare CLI Azure e connetterlo al proprio account, vedere l' [interfaccia di riga di comando Azure Azure CLI ()](xplat-cli-install.md).

### <a name="step-1-update-azure-cli-version"></a>Passaggio 1: Versione di aggiornamento Azure CLI

Per usare Azure CLI per i comandi fondamentale con la modalità di gestione dei servizi, è una versione recente se possibile. Per verificare la versione in uso, digitare `azure --version`. Verrà visualizzata simile al seguente:

    $ azure --version
    0.8.17 (node: 0.10.25)

Se si desidera aggiornare la versione di Azure CLI, vedere [CLI Azure](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-set-the-azure-account-and-subscription"></a>Passaggio 2: Impostare l'account Azure e sottoscrizione

Dopo aver connesso il CLI Azure con l'account che si desidera utilizzare, potrebbe essere più di una sottoscrizione. Se si, rivedere le sottoscrizioni disponibili per il proprio account digitando `azure account list`e quindi selezionare l'abbonamento a cui si desidera utilizzare digitando `azure account set <subscription id or name> true` dove _id abbonamento o il nome_ è l'id di abbonamento o il nome dell'abbonamento che si desidera lavorare con la sessione corrente. Verrà visualizzato il seguente:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE] Se non si dispone già di un account Azure, ma si ha un abbonamento a abbonamento MSDN, è possibile ottenere gratuito Azure crediti attivando i [vantaggi dell'abbonato MSDN qui](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) - o è possono utilizzare account gratuito. Uno funzionerà per l'accesso Azure.
