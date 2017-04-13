<properties
   pageTitle="Gestire i record DNS inverso per i servizi di Azure (classico) tramite PowerShell | Microsoft Azure"
   description="Modalità di gestione dei record DNS inverso o PTR per i servizi di Azure tramite PowerShell nel modello di distribuzione classica. "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Come gestire i record DNS inverso per i servizi Azure (classico) tramite PowerShell Azure

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di Manager delle risorse](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Convalida dei record DNS inverso
Per garantire che una terza parte non è possibile creare record DNS inverso mapping ai propri domini DNS, Azure consente solo la creazione di un record DNS inverso nel punto in cui una delle operazioni seguenti è vera:

- L'inverso FQDN DNS è il nome del servizio Cloud per cui è stata specificata o il nome di un servizio Cloud all'interno della stessa sottoscrizione, ad esempio, DNS inverso è "contosoapp1.cloudapp.net.".
- Inoltra il FQDN DNS inverso restituisce il nome o indirizzo IP di servizio Cloud per cui è stato specificato o a nome di un servizio Cloud o IP all'interno della stessa sottoscrizione ad esempio DNS inverso è "app1.contoso.com". che è un alias CName contosoapp1.cloudapp.net.

Controlli di convalida vengono eseguiti solo quando la proprietà DNS inversa di un servizio Cloud viene impostata o modificata. Convalida nuova periodica non viene eseguita.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Aggiungere DNS inverso ai servizi Cloud esistenti
È possibile aggiungere un record DNS inverso a un servizio Cloud esistente utilizzando il cmdlet "Set-AzureService":

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>Creare un servizio Cloud con DNS inverso
È possibile aggiungere un nuovo servizio Cloud con la proprietà DNS inversa specificata utilizzando il cmdlet "Set-AzureService":

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>Visualizzazione DNS inverso dei servizi Cloud esistenti
È possibile visualizzare il valore configurato per un servizio Cloud esistente utilizzando il cmdlet "Get-AzureService":

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Rimuovere il DNS inverso dai servizi Cloud esistente
È possibile rimuovere una proprietà DNS inversa da un servizio Cloud esistente utilizzando il cmdlet "Set-AzureService". È possibile impostare il valore della proprietà DNS inverso vuota:

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]
