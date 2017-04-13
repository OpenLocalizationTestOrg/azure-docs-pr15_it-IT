<properties
   pageTitle="Provider di elaborazione, di rete e di archiviazione | Microsoft Azure"
   description="Panoramica del calcolo, rete e lo spazio di archiviazione provider di risorse (CRP NRP e Security Rollup Package) per le applicazioni di Linux nel modello di distribuzione di Manager delle risorse di Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="tomfitz"/>

# <a name="azure-compute-network-and-storage-providers-for-linux-applications-under-azure-resource-manager-deployment-model"></a>Azure provider di elaborazione, di rete e di archiviazione per le applicazioni di Linux in modello di distribuzione di Manager delle risorse di Azure

L'inclusione di funzionalità di elaborazione, di rete e di archiviazione con il modello di distribuzione di gestione di risorse Azure sostanzialmente semplifica la distribuzione e gestione di applicazioni complesse in esecuzione su IaaS. Molte applicazioni richiedono una combinazione di risorse, tra cui una rete virtuale, Account di archiviazione, macchina virtuale e un'interfaccia di rete. Il modello di distribuzione di gestione di risorse Azure offre la possibilità di creare un modello JSON per distribuire e gestire tutte queste risorse insieme come una singola applicazione.

[AZURE.INCLUDE [virtual-machines-common-compare-deployment-models](../../includes/virtual-machines-common-compare-deployment-models.md)]
