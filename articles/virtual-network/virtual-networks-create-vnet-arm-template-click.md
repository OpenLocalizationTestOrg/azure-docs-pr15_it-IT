<properties
   pageTitle="Creare una rete virtuale utilizzando un modello ARM | Microsoft Azure"
   description="Informazioni su come creare una rete virtuale utilizzando un modello ARM | Manager delle risorse."
   services="virtual-network"
   documentationCenter=""
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial"/>

# <a name="create-a-virtual-network-by-using-an-arm-template"></a>Creare una rete virtuale utilizzando un modello ARM

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]In questo documento è descritta la creazione di un VNet utilizzando il modello di distribuzione di Manager delle risorse. È anche possibile [creare una rete virtuale nel modello di distribuzione classica](virtual-networks-create-vnet-classic-pportal.md).

Si verrà informazioni su come scaricare e modificare e modello ARM esistente dal GitHub e distribuire il modello da GitHub, PowerShell e CLI Azure.

Se si è sufficiente distribuisce il modello ARM direttamente da GitHub, senza alcuna modifica, andare al [distribuire un modello da github](#deploy-the-arm-template-by-using-click-to-deploy).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]