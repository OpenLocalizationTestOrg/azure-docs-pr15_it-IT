<properties
  pageTitle="Utilizzando il DNS Azure con altri servizi di Azure | Microsoft Azure"
  description="Informazioni sull'utilizzo di Azure DNS per risolvere il nome per altri servizi di Azure"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>

# <a name="using-azure-dns-with-other-azure-services"></a>Utilizzando il DNS Azure con altri servizi di Azure

Azure DNS è un host DNS management e il nome servizio di risoluzione. In questo modo è possibile creare i nomi DNS pubblici per le altre applicazioni e servizi che è stato distribuito in Azure. La creazione di un nome per un servizio di Azure in un dominio personalizzato è semplice come aggiungere un record di tipo corretto per il servizio.

* Per gli indirizzi IP allocati in modo dinamico, è necessario creare un record CNAME DNS che esegue il mapping al nome DNS Azure creato per il servizio. Standard DNS impediscono l'utilizzo di un record CNAME per apice zona.
* Per indirizzi IP statici, è possibile creare un record DNS A utilizzando qualsiasi nome, inclusi un nome di _dominio naked_ al vertice zona.

Nella tabella seguente sono illustrati i tipi di record supportati che possono essere utilizzati per diversi servizi di Azure. Come si può notare dalla tabella, Azure DNS supporta solo i record DNS per le risorse di rete esposto a Internet. Azure DNS non è possibile usare per la risoluzione degli indirizzi privata interni.

| Servizio di Azure | Interfaccia di rete | Descrizione |
|---------------|-------------------|-------------|
| Gateway di applicazioni | IP pubblico front-end | È possibile creare un record DNS A o CNAME. |
| Bilanciamento del carico | IP pubblico front-end | È possibile creare un record DNS A o CNAME. Bilanciamento del carico può avere un indirizzo IP pubblico IPv6 che viene assegnato dinamicamente. Di conseguenza, è necessario creare un record CNAME per un indirizzo IPv6. |
| Gestore del traffico | Nome pubblico | È possibile creare solo un record CNAME associato al nome trafficmanager.net assegnato al proprio profilo di gestore del traffico. Per ulteriori informazioni, vedere [come gestore del traffico funziona](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example). |
| Servizio cloud | IP pubblico | Per indirizzi IP statici, è possibile creare un record DNS A. Per gli indirizzi IP allocati in modo dinamico, è necessario creare un record CNAME associato al nome _cloudapp.net_ . Questa regola viene applicata macchine virtuali create nel portale di classica perché sono distribuiti come un servizio cloud. Per ulteriori informazioni, vedere [configurare un nome di dominio personalizzato in servizi Cloud](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| Servizio di App | Indirizzo IP esterno | Per gli indirizzi IP esterni, è possibile creare un record DNS A. In caso contrario, è necessario creare un record CNAME associato al nome azurewebsites.net. Per ulteriori informazioni, vedere [eseguire il mapping di un nome di dominio personalizzato a un'applicazione Azure](../app-service-web/web-sites-custom-domain-name.md) |
| Macchine virtuali di Manager delle risorse | IP pubblico | Macchine virtuali di Manager delle risorse può avere indirizzi IP pubblico. Una macchina virtuale con un indirizzo IP pubblico potrebbe anche essere dietro un bilanciamento del carico. È possibile creare un record DNS A o CNAME per l'indirizzo pubblico. Questo nome personalizzato può essere utilizzato per ignorare l'indirizzo VIP nel sistema di bilanciamento del carico. |
| Macchine virtuali classiche | IP pubblico | Macchine virtuali classiche creati tramite PowerShell o CLI può essere configurato con un indirizzo virtuale (riservato) statico o dinamico. È possibile creare un DNS CNAME o un record, rispettivamente. |
