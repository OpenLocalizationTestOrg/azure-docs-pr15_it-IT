<properties 
pageTitle="Distribuzione HANA HANA S/4 o bianco e nero/4 in una macchina virtuale Azure | Microsoft Azure" 
description="La distribuzione HANA S/4 o bianco e nero/4 HANA su una macchina virtuale Azure" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/> 
<tags 
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/> 


# <a name="deploying-s4-hana-or-bw4-hana-on-microsoft-azure"></a>La distribuzione HANA S/4 o bianco e nero/4 HANA in Microsoft Azure 

In questo articolo viene descritto come distribuire HANA S/4 in Microsoft Azure tramite SAP Cloud accessorio raccolta 3.0.
Le schermate mostrano il processo passo dopo passo. Distribuzione di altre soluzioni basate su SAP HANA come bianco e nero/4 HANA funziona in modo da una prospettiva di processo. Solo è necessario selezionare un'altra soluzione.

Per iniziare con una raccolta di accessorio Cloud di SAP (SAP CAL) passare [qui](https://cal.sap.com/). Esiste un blog da SAP sui nuovi [SAP Cloud accessorio raccolta 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


Screenshot seguente mostra dettagliate sulla distribuzione HANA S/4 in Microsoft Azure. Il processo funziona nello stesso modo per altre soluzioni likeBW/4 HANA.


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

La prima immagine Mostra tutte le soluzioni basate su SAP CAL HANA sono disponibili in Microsoft Azure.
Exemplarily il "SAP S/4 HANA locale edizione" (soluzione nella parte inferiore la schermata) è stato scelto per il processo.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

Prima di tutto un nuovo account SAP CAL deve essere creato. Sono disponibili due opzioni per Azure - standard Azure e Azure su continente Cina che viene gestito da 21Vianet partner.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

Quindi è necessario immettere l'ID di abbonamento Azure presenti nel portale di Azure - vedere anche ulteriormente verso il basso come ottenerlo. Successivamente un certificato di gestione Azure deve essere scaricato.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

In nuovo Azure uno portale trova l'elemento "Sottoscrizioni" sul lato sinistro. Fare clic su per visualizzare tutte le sottoscrizioni attive per l'utente.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

Selezionare uno degli abbonamenti, quindi scegliere "Gestione dei certificati" spiega che vi è un nuovo concetto utilizzando "identità di servizio" per il nuovo modello di gestione di risorse Azure.
Licenza CAL SAP non viene adattato ancora per il nuovo modello e richiede il portale di Azure ex per lavorare con i certificati di gestione e il modello "classico".

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

Uno qui possibile visualizzare il portale di Azure ex. Il caricamento di un certificato di gestione offre SAP CAL le autorizzazioni per creare macchine virtuali all'interno di un abbonamento del cliente. In "ABBONAMENTI" scheda una possibile trovare l'ID di abbonamento che deve essere immesse nel portale di CAL SAP.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

Nella scheda seconda, è possibile caricare il certificato di gestione che è stato scaricato prima da SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

Viene visualizzata una piccola finestra di dialogo a selezionare il file di certificato scaricato.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

Dopo il certificato è stato caricato la connessione tra CAL SAP e il cliente abbonamento Azure può essere verificata all'interno di CAl SAP. Un messaggio piccola deve popup che indica che la connessione è valida.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

Al termine dell'installazione di un account è necessario selezionare una soluzione che deve essere distribuita e crea un'istanza.
Con la modalità "base", è molto semplice. Immettere un nome di istanza, scegliere un'area di Azure e definire la password master per la soluzione.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

In un secondo momento in base alle dimensioni e alla complessità della soluzione (stima applicando CAL SAP) viene visualizzata come "attivi" e pronto per l'utilizzo. È molto semplice.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

Esamina alcuni dettagli di una soluzione è possibile visualizzare il tipo di macchine virtuali distribuite. In questo caso sono state create tre macchine virtuali di Azure scopo e dimensioni dei caratteri diverse.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

Nel portale di Azure, macchine virtuali sono disponibili a partire dallo stesso nome che è stato concesso in licenza CAL SAP.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

A questo punto è possibile connettersi alla soluzione tramite il pulsante Connetti nel portale di CAL SAP. Finestra di dialogo piccola contiene un collegamento a una Guida utente che descrive tutte le credenziali predefinita per l'uso con la soluzione.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

È anche possibile accedere al client macchine Virtuali di Windows e iniziare ad esempio la grafica SAP preconfigurato.







