<properties
   pageTitle="Gestire i record DNS inverso per i servizi Azure usa CLI Azure | Microsoft Azure"
   description="Come gestire i record DNS inverso o dei record PTR per i servizi di Azure tramite CLI Azure in Gestione risorse"
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli"></a>Come gestire i record DNS inverso per i servizi Azure usa CLI Azure

[AZURE.INCLUDE [DNS-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modello di distribuzione classica](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Convalida dei record DNS inverso
Per garantire che una terza parte non è possibile creare record DNS inverso mapping ai propri domini DNS, Azure consente solo la creazione di un record DNS inverso nel punto in cui una delle operazioni seguenti è vera:

- "ReverseFqdn" è possibile eseguire la stessa il Fqdn"" per la risorsa indirizzo IP pubblico per il quale è stato specificato o "Fqdn" per qualsiasi indirizzo IP pubblico all'interno della stessa sottoscrizione ad esempio, "ReverseFqdn" è "contosoapp1.northus.cloudapp.azure.com.".

- "ReverseFqdn" Inoltra restituisce il nome o l'indirizzo IP dell'indirizzo IP pubblico per cui è stato specificato o a qualsiasi indirizzo IP pubblico "Fqdn" o IP all'interno della stessa sottoscrizione ad esempio, "ReverseFqdn" è "app1.contoso.com". che è un alias CName "contosoapp1.northus.cloudapp.azure.com"

Controlli di convalida vengono eseguiti solo quando la proprietà DNS inversa per un indirizzo IP pubblico viene impostata o modificata. Convalida nuova periodica non viene eseguita.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Aggiungere gli indirizzi IP pubblico esistenti DNS inverso
È possibile aggiungere un indirizzo IP pubblico esistente utilizzando il set di indirizzi ip pubblico rete azure DNS inverso:

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Se si desidera aggiungere un indirizzo IP pubblico esistente che non dispone già di un nome DNS inverso DNS, è anche necessario specificare un nome DNS. È possibile aggiungere eseguire questa operazione utilizzando il set di indirizzi ip pubblico azure rete:

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Creare un indirizzo IP pubblico con il sistema DNS inverso
È possibile aggiungere un nuovo indirizzo IP pubblico con la proprietà DNS inversa specificata utilizzando il pubblico-ip di rete azure creare:

    azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Visualizzazione DNS inverso per gli indirizzi IP pubblici esistenti
È possibile visualizzare il valore configurato per un indirizzo IP pubblico esistente utilizzando il percorso di rete azure ip pubblico visualizzare:

    azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Rimuovere gli indirizzi IP pubblici esistenti DNS inverso
È possibile rimuovere una proprietà DNS inversa da un indirizzo IP pubblico esistente con azure rete ip pubblico set. In tal caso, l'impostazione della proprietà ReverseFqdn a vuoto:

    azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]
