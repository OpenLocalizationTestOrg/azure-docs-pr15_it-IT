<properties
   pageTitle="Utilizzo di SAP in macchine virtuali di Windows | Microsoft Azure"
   description="Cancella sull'uso di SAP su Windows macchine () in Microsoft Azure"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/04/2016"
   ms.author="sedusch"/>

# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>Utilizzo di SAP in macchine virtuali di Windows Azure

Il cloud Computing è diffuso termine che sta per essere maggiore di importanza del settore IT da tutte le organizzazioni multinazionali e grandi aziende di piccole dimensioni. Microsoft Azure è la piattaforma di servizi Cloud Microsoft che offre un'ampia gamma di nuove opportunità. Ora i clienti sono in grado di eseguire il provisioning e deselezionare effettuare il provisioning di applicazioni come servizi Cloud, in modo che non sono le limitazioni tecniche o budget rapidamente. Invece di investire tempo e budget in infrastruttura hardware, società consente di tracciare l'applicazione, processi aziendali e sui vantaggi per i clienti e utenti.

Con macchine virtuali di Microsoft Azure, Microsoft offre un'infrastruttura completa di piattaforma (IaaS). Le applicazioni SAP NetWeaver basata sono supportate in macchine virtuali di Azure (IaaS). White paper riportata di seguito viene descritto come pianificare e implementare le applicazioni SAP NetWeaver basato sul macchine virtuali di Windows Azure. È anche possibile implementare le applicazioni SAP NetWeaver basato su [macchine virtuali Linux](virtual-machines-linux-classic-sap-get-started.md).

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver su Azure - HA

Titolo: SAP NetWeaver su Azure - cluster SAP ASCS/SCS istanze utilizzando Cluster di Failover di Windows Server su Azure con SIOS DataKeeper

Riepilogo: ' il documento viene descritto come utilizzare SIOS DataKeeper per impostare una configurazione SAP ASCS/SCS disponibilità in Azure. SAP protegge i singoli punti di componenti di errore come SAP ASCS/SCS o servizi di replica di Accodamento con configurazioni Cluster di Failover di Windows Server che richiedono dischi condivisi. Questi componenti SAP sono essenziali per la funzionalità di un sistema SAP. Funzionalità di disponibilità deve pertanto mettere in atto per assicurarsi che i componenti sia in grado di sostenere un errore di un server o una macchina virtuale come lavorare con le configurazioni Cluster di Windows per gli ambienti di Hyper-V e privo. In agosto 2015 Azure nella stessa non può fornire dischi condivisi necessari per le finestre in base a configurazioni disponibile necessarie per questi componenti SAP critici. Tuttavia con l'aiuto del prodotto DataKeeper da SIOS, è possono creare configurazioni Cluster di Failover di Windows Server in base alle esigenze per SAP ASCS/SCS piattaforma IaaS Azure. In questo articolo descrive un approccio passo a passo come installare una configurazione Cluster di Failover di Windows Server con disco condiviso fornito da Datakeeper SIOS in Azure. Il documento verranno illustrate le informazioni dettagliate in configurazioni sul lato Azure, Windows e SAP che la configurazione di disponibilità funzionano in modo ottimale. Carta si integra con la documentazione di installazione di SAP e note di SAP che rappresentano le risorse principale per le installazioni e distribuzioni del software SAP in dato piattaforme.

Ultimo aggiornamento: Agosto 2015

[Download di questa Guida](http://go.microsoft.com/fwlink/?LinkId=613056)
