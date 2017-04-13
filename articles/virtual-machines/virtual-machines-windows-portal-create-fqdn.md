<properties
   pageTitle="Creare FQDN per una macchina virtuale nel portale di Azure | Microsoft Azure"
   description="Informazioni su come creare un nome di dominio completo o nome di dominio completo per un Manager delle risorse in base a macchina virtuale nel portale di Azure."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/24/2016"
   ms.author="iainfou"/>

# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Creare un nome di dominio completo nel portale di Azure
Quando si crea una macchine () in del [portale di Azure](https://portal.azure.com) utilizzando il modello di distribuzione di Manager delle risorse, viene creata automaticamente una risorsa IP pubblica per la macchina virtuale. Utilizzare questo indirizzo IP di accedere la macchina virtuale. Anche se il portale non viene creato un [nome di dominio completo](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), o FQDN, per impostazione predefinita, è possibile creare uno dopo aver creata la macchina virtuale. In questo articolo illustra i passaggi per creare un nome DNS o il nome di dominio completo.

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

È ora possibile connettersi in remoto per la macchina virtuale con questo nome DNS ad esempio per RDP Remote Desktop Protocol ().

## <a name="next-steps"></a>Passaggi successivi
Ora che la macchina virtuale ha un nome di indirizzi IP e DNS pubblico, è possibile distribuire comuni framework delle applicazioni o servizi, ad esempio IIS, SQL o SharePoint.

È inoltre possibile leggere altre informazioni sull' [utilizzo di gestione risorse](../azure-resource-manager/resource-group-overview.md) per suggerimenti sulla creazione di distribuzioni di Azure.