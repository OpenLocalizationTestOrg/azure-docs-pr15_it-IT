<properties
    pageTitle="Distribuire una macchina virtuale con una password archiviata in Azure Stack chiave archivio | Microsoft Azure"
    description="Informazioni su come distribuire una macchina virtuale con una password archiviata in Azure Stack chiave archivio"
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

# <a name="deploy-a-vm-by-retrieving-the-password-stored-in-key-vault"></a>Distribuire una macchina virtuale per il recupero della password memorizzata nell'archivio di chiave

Quando è necessario passare un valore sicuro (ad esempio una password) come parametro durante la distribuzione, è possibile archiviare il valore come segreto in un archivio di chiave dello Stack di Azure e fare riferimento al valore in altri modelli di gestione di risorse Azure. Includere solo un riferimento al segreto del modello in modo che il segreto non viene mai esposto. Non è necessario immettere manualmente il valore per il segreto ogni volta che si distribuiscono le risorse. Specificare quali utenti o identità di servizio possono accedere alle informazioni riservate.

## <a name="reference-a-secret-with-static-id"></a>Fare riferimento a un segreto con ID statico

Si fa riferimento il segreto all'interno di un file di parametri, che passa valori per il modello. Si fa riferimento il segreto passando l'identificatore di risorsa dell'archivio di chiave e il nome del segreto. In questo esempio, è necessario che esista già il segreto archivio chiave. Si utilizza un valore statico per l'ID di risorsa.

    "parameters": {
    "adminPassword": {
    "reference": {
    "keyVault": {
    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
    },
    "secretName": "sqlAdminPassword"


>[AZURE.NOTE]Il parametro che accetta il segreto deve essere *securestring*.

## <a name="next-steps"></a>Passaggi successivi
[Distribuire un'app di esempio con chiave archivio](azure-stack-kv-sample-app.md)

[Distribuire una macchina virtuale con un certificato chiave archivio](azure-stack-kv-push-secret-into-vm.md)

