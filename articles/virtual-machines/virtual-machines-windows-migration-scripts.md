<properties
    pageTitle="Strumenti per la gestione del servizio di Azure community per la migrazione di Manager delle risorse di Azure"
    description="In questo articolo catalogo gli strumenti per sono disponibili dalla community per facilitare la migrazione delle risorse IaaS dalla gestione dei servizi di Azure stack Manager delle risorse di Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="singhkay"/>

# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Strumenti per la gestione del servizio di Azure community per la migrazione di Manager delle risorse di Azure

In questo articolo catalogo gli strumenti per sono disponibili dalla community per facilitare la migrazione delle risorse IaaS dalla gestione dei servizi di Azure stack Manager delle risorse di Azure.

>[AZURE.NOTE]Questi strumenti non sono supportati ufficiale dal supporto Microsoft. Pertanto sono aperti origine su Github e si è soddisfatti accettare PRs per correzioni o altri scenari. Per segnalare un problema, utilizzare la funzionalità di problemi Github.
>
> Eseguire la migrazione con questi strumenti causeranno il tempo di inattività per la macchina virtuale classica. Se si sta cercando migrazione delle piattaforme supportate, visitare 
>
>- [Piattaforma supportata la migrazione di risorse IaaS da classico stack Manager delle risorse di Azure](./virtual-machines-windows-migration-classic-resource-manager.md)
>- [Approfondimento tecnico su piattaforma supportata la migrazione da classico per gestione di risorse di Azure](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
>- [Eseguire la migrazione di risorse IaaS classico per gestione di risorse di Azure tramite PowerShell Azure](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## <a name="asm2arm"></a>ASM2ARM

Si tratta di un modulo di script di PowerShell per la migrazione del **singolo** virtuale macchina da stack di gestione dei servizi di Azure allo stack di Manager delle risorse di Azure. 

[Creare un collegamento per la documentazione dello strumento](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz

migAz è un'opzione aggiuntiva per eseguire la migrazione di un insieme completo di Azure servizio Gestione IaaS risorse alle risorse di Azure Manager delle risorse IaaS. La migrazione può verificarsi all'interno della stessa sottoscrizione o tra le varie sottoscrizioni e tipi di sottoscrizione (ex: abbonamenti CSP).

[Creare un collegamento per la documentazione dello strumento](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)