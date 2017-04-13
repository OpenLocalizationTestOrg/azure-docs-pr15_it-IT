<properties
   pageTitle="Creare un'identità aziendale o dell'istituto di istruzione in AAD | Microsoft Azure"
   description="Informazioni su come creare un'identità aziendale o dell'istituto di istruzione in Azure Active Directory per l'uso con macchine virtuali Linux."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="creating-a-work-or-school-identity-in-azure-active-directory-to-use-with-linux-vms"></a>Creazione di un'identità aziendale o dell'istituto di istruzione in Azure Active Directory per l'uso con macchine virtuali Linux

Se è stato creato un account Azure personale o ha un abbonamento MSDN personale creato l'account Azure per sfruttare crediti Azure MSDN - si usava identità di un *account Microsoft* per la creazione. Numerose funzionalità di Azure - [modelli di gruppo di risorse](../azure-resource-manager/resource-group-overview.md) è un esempio - richiede un account aziendale o dell'istituto di istruzione (un'identità gestite da Azure Active Directory) per l'uso. È possibile seguire le istruzioni seguenti per creare un nuovo account aziendale o dell'istituto di istruzione perché per uno dei principali vantaggi dell'account Azure personale che è dotato di un dominio di Azure Active Directory predefinito che è possibile utilizzare per creare un nuovo account aziendale o dell'istituto di istruzione che è possibile usare con Azure funzionalità che richiedono il.

Tuttavia, le modifiche recenti consentono di gestire l'abbonamento con qualsiasi tipo di account Azure utilizzando il `azure login` metodo di accesso interattiva descritto [di seguito](../xplat-cli-connect.md). È possibile utilizzare tale meccanismo oppure è possibile seguire le istruzioni che seguono. È anche possibile [creare un lavoro o dell'istituto di istruzione identità di Azure Active Directory per l'uso con macchine virtuali di Windows](virtual-machines-windows-create-aad-work-id.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]
