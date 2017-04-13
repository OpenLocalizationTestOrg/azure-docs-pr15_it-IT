<properties
    pageTitle="Connettersi a Stack Azure con CLI | Microsoft Azure"
    description="Informazioni su come utilizzare l'interfaccia della riga di comando multipiattaforma (CLI) per gestire e distribuire risorse nella pila di Azure"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="helaw"/>

# <a name="install-and-configure-azure-stack-cli"></a>Installare e configurare Azure Stack CLI

In questo documento è guidare il processo di utilizzo Azure interfaccia riga di comando (CLI) per gestire le risorse di Azure Stack piattaforme client Linux e Mac.  

## <a name="install-azure-stack-cli"></a>Installare Stack di Azure CLI

Se si usa Mac o Linux, è possibile ottenere CLI tramite il comando seguente:
  
    `npm install -g azure-cli@0.10.4`.


## <a name="connect-to-azure-stack"></a>Connettersi a Stack Azure
Nella procedura seguente configurare CLI Azure a cui connettersi dello Stack di Azure. Quindi accedere e recuperare informazioni sull'abbonamento.

1.  Recuperare il valore di id risorsa di directory attiva eseguendo questa PowerShell:
        
          (Invoke-RestMethod -Uri https://api.azurestack.local/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]

2.  Utilizzare il comando CLI seguente per aggiungere l'ambiente dello Stack di Azure, assicurandosi di aggiornare *-id risorsa di directory active* con i dati URL recuperato nel passaggio precedente:

           azure account env add AzureStack --resource-manager-endpoint-url "https://api.azurestack.local" --management-endpoint-url "https://api.azurestack.local" --active-directory-endpoint-url  "https://login.windows.net" --portal-url "https://portal.azurestack.local" --gallery-endpoint-url "https://portal.azurestack.local" --active-directory-resource-id "https://azurestack.local-api/" --active-directory-graph-resource-id "https://graph.windows.net/"

3.  Accesso tramite il comando seguente (sostituire *nomeutente* con il proprio nome utente):

        azure login -e AzureStack -u “<username>”

    >[AZURE.NOTE]Se si ricevono errori di convalida certificato, disattivare la convalida dei certificati eseguendo il comando `set        NODE_TLS_REJECT_UNAUTHORIZED=0`.

4.  Impostare la modalità di configurazione Azure per gestione di risorse di Azure tramite il comando seguente:

        azure config mode arm

5.  Recuperare un elenco delle sottoscrizioni.

        azure account list     

## <a name="next-steps"></a>Passaggi successivi

[Distribuire modelli con CLI Azure](azure-stack-deploy-template-command-line.md)

[Connettersi con PowerShell](azure-stack-connect-powershell.md)

[Gestire le autorizzazioni utente](azure-stack-manage-permissions.md)
