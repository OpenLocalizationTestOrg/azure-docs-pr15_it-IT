<properties 
pageTitle="Distribuzione di SAP IDE EHP7 SP3 per SAP ERP 6.0 in Microsoft Azure | Microsoft Azure" 
description="Distribuzione di SAP IDE EHP7 SP3 per SAP ERP 6.0 in Microsoft Azure" 
services="virtual-machines-windows" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
keywords=""/> 
<tags 
ms.service="virtual-machines-windows" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-windows" 
ms.workload="infrastructure-services" 
ms.date="09/16/2016" 
ms.author="hermannd"/> 


# <a name="deploying-sap-ides-ehp7-sp3-for-sap-erp-60-on-microsoft-azure"></a>Distribuzione di SAP IDE EHP7 SP3 per SAP ERP 6.0 in Microsoft Azure 

In questo articolo viene descritto come distribuire IDE SAP in esecuzione con SQL Server e del sistema operativo Windows in Microsoft Azure tramite SAP Cloud accessorio raccolta 3.0. Le schermate mostrano il processo passo dopo passo. Distribuzione di altre soluzioni nell'elenco funziona nello stesso modo dal punto di vista processo. Solo è necessario selezionare un'altra soluzione.

Per iniziare con una raccolta di accessorio Cloud di SAP (SAP CAL) passare [qui](https://cal.sap.com/). Esiste un blog da SAP sui nuovi [SAP Cloud accessorio raccolta 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


Screenshot seguente mostra dettagliate sulla distribuzione IDE SAP in Microsoft Azure. Il processo funziona nello stesso modo per altre soluzioni.


![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

La prima immagine Mostra tutte le soluzioni che sono disponibili in Microsoft Azure. Evidenziata soluzione basato su Windows IDE SAP che è disponibile solo in Azure è stato scelto per il processo.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

Prima di tutto un nuovo account SAP CAL deve essere creato. Sono disponibili due opzioni per Azure - standard Azure e Azure su continente Cina che viene gestito da 21Vianet partner.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

Quindi è necessario immettere l'ID di abbonamento Azure presenti nel portale di Azure - vedere anche ulteriormente verso il basso come ottenerlo. Successivamente un certificato di gestione Azure deve essere scaricato.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

In nuovo Azure uno portale trova l'elemento "Sottoscrizioni" sul lato sinistro. Fare clic su per visualizzare tutte le sottoscrizioni attive per l'utente.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

Selezionare uno degli abbonamenti, quindi scegliere "Gestione dei certificati" spiega che vi è un nuovo concetto utilizzando "identità di servizio" per il nuovo modello di gestione di risorse Azure.
Licenza CAL SAP non viene adattato ancora per il nuovo modello e richiede il portale di Azure ex per lavorare con i certificati di gestione e il modello "classico".

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

Uno qui possibile visualizzare il portale di Azure ex. Il caricamento di un certificato di gestione offre SAP CAL le autorizzazioni per creare macchine virtuali all'interno di un abbonamento del cliente. In "ABBONAMENTI" scheda una possibile trovare l'ID di abbonamento che deve essere immesse nel portale di CAL SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

Nella scheda seconda, è possibile caricare il certificato di gestione che è stato scaricato prima da SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

Viene visualizzata una piccola finestra di dialogo a selezionare il file di certificato scaricato.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

Dopo il certificato è stato caricato la connessione tra CAL SAP e il cliente abbonamento Azure può essere verificata all'interno di CAl SAP. Un messaggio piccola deve popup che indica che la connessione è valida.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

Al termine dell'installazione di un account è necessario selezionare una soluzione che deve essere distribuita e crea un'istanza.
Con la modalità "base", è molto semplice. Immettere un nome di istanza, scegliere un'area di Azure e definire la password master per la soluzione.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

In un secondo momento in base alle dimensioni e alla complessità della soluzione (stima applicando CAL SAP) viene visualizzata come "attivi" e pronto per l'utilizzo. È molto semplice.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

Esamina alcuni dettagli di una soluzione è possibile visualizzare il tipo di macchine virtuali distribuite. In questo caso non esiste una macchina virtuale Azure singola delle dimensioni D12 creato da SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

Nel portale di Azure, possono trovare la macchina virtuale inizia con il nome dell'istanza stessa che è stato concesso in licenza CAL SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

A questo punto è possibile connettersi alla soluzione tramite il pulsante Connetti nel portale di CAL SAP. Finestra di dialogo piccola contiene un collegamento a una Guida utente che descrive tutte le credenziali predefinita per l'uso con la soluzione.
[Di seguito](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) è il collegamento alla Guida per la soluzione IDE.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

È anche possibile accedere al macchina virtuale di Windows e iniziare ad esempio la grafica SAP preconfigurato.





