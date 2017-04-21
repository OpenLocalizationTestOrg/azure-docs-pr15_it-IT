<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli-with-azure-resource-manager-arm"></a>Utilizzo di Azure CLI con Azure Gestione risorse (ARM)

Prima di poter utilizzare CLI Azure con modelli e i comandi di Manager delle risorse per la distribuzione di Azure risorse e carichi di lavoro con i gruppi di risorse, sarà necessario (naturalmente di) un account con Azure. Se non si dispone di un account, è possibile ottenere una [Azure versione di valutazione gratuita di seguito](https://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Se non si dispone già di un account Azure, ma si ha un abbonamento a abbonamento MSDN, è possibile ottenere gratuito Azure crediti attivando i [vantaggi dell'abbonato MSDN qui](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) - o è possono utilizzare account gratuito. Uno funzionerà per l'accesso Azure.

### <a name="step-1-verify-the-azure-cli-version"></a>Passaggio 1: Verificare la versione di Azure CLI

Per usare Azure CLI per ARM modelli e i comandi fondamentale, è necessario disporre almeno versione 0.8.17. Per verificare la versione in uso, digitare `azure --version`. Verrà visualizzata simile al seguente:

    $ azure --version
    0.8.17 (node: 0.10.25)

Se è necessario aggiornare la versione di Azure CLI, vedere [CLI Azure](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-verify-you-are-using-a-work-or-school-identity-with-azure"></a>Passaggio 2: Verificare si utilizza un lavoro o dell'istituto di istruzione identità con Azure

È possibile utilizzare la modalità di comando ARM solo se si utilizza un [tenant di Azure Active Directory](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) o un [Nome dell'entità servizio](https://msdn.microsoft.com/library/azure/dn132633.aspx). (Sono detti anche *gli ID dell'organizzazione*.)

Per vedere se si dispone di uno, connettersi digitando `azure login` e usando il lavoro o dell'istituto di istruzione nome utente e la password quando richiesto. Se si dispone, è necessario verificare simile al seguente:

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
  	|info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

Se non viene visualizzato, è necessario creare un nuovo tenant (o servizio principale) con l'identità dell'account Microsoft. (Si tratta spesso maiuscole/minuscole con gli abbonamenti MSDN personali o sottoscrizioni di valutazione gratuite.) Per creare un lavoro o dell'istituto di istruzione id dal proprio account Azure creata con un id Microsoft, vedere [associare un Azure Active Directory con un nuovo abbonamento Azure](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Se si ritiene che è necessario avere già un id dell'organizzazione, potrebbe essere necessario contattare la persona che ha creato l'account dell'utente.

### <a name="step-3-choose-your-azure-subscription"></a>Passaggio 3: Selezionare l'abbonamento Azure

Se si dispone di un solo abbonamento nell'account Azure, CLI Azure stesso associa con l'abbonamento per impostazione predefinita. Se si dispone di più di una sottoscrizione, è necessario selezionare l'abbonamento a cui si desidera utilizzare digitando `azure account set <subscription id or name> true` dove _id abbonamento o il nome_ è l'id di abbonamento o il nome dell'abbonamento che si desidera lavorare con la sessione corrente.

Verrà visualizzato il seguente:

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### <a name="step-4-place-your-azure-cli-in-the-arm-mode"></a>Passaggio 4: Posizionare il CLI Azure in modalità ARM

Per utilizzare la modalità di gestione delle risorse Azure (ARM) con CLI Azure, digitare `azure config mode arm`. Verrà visualizzato il seguente:

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE] È possibile tornare a utilizzare i comandi di gestione del servizio Azure digitando `azure config mode asm`.
