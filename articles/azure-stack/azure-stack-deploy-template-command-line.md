<properties
    pageTitle="Distribuire modelli con la riga di comando in pila Azure | Microsoft Azure"
    description="Informazioni su come utilizzare l'interfaccia di riga di comando multipiattaforma (CLI) per distribuire modelli di all'interno di ClientVM oppure dopo l'utilizzo di VPN a cui connettersi dello Stack di Azure."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Distribuire modelli di Azure Stack dalla riga di comando

Utilizzare la riga di comando per distribuire modelli di gestione di risorse Azure la prova pratica dello Stack di Azure. Modelli di gestione risorse Azure distribuire ed effettuare il provisioning di tutte le risorse per l'applicazione in un'operazione su una singola e coordinata.

## <a name="download-template"></a>Scaricare modello        
Per verificare una distribuzione con CLI, scaricare il file azuredeploy.json e azuredeploy.parameters.json da [creare lo spazio di archiviazione account esempio modello](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Distribuire modello
Passare alla cartella in cui questi file sono stati scaricati ed eseguire il seguente comando per distribuire il modello:

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

Questo comando distribuisce il modello per il gruppo risorse **cliRG** nel percorso predefinito di prova Stack Azure.

## <a name="validate-template-deployment"></a>Convalidare la distribuzione dei modelli
Per visualizzare l'account di gruppo e lo spazio di archiviazione delle risorse, utilizzare i comandi seguenti:

    azure group list

    azure storage account list

## <a name="next-steps"></a>Passaggi successivi

[Gestire le autorizzazioni utente](azure-stack-manage-permissions.md)
