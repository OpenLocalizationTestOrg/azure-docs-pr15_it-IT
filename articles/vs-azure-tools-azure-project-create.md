<properties
   pageTitle="Creazione di un progetto Azure con Visual Studio | Microsoft Azure"
   description="Creazione di un progetto Azure con Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="creating-an-azure-project-with-visual-studio"></a>Creazione di un progetto Azure con Visual Studio

Azure Tools per Visual Studio offrono un modello che consente di creare un servizio cloud per Azure. Gli strumenti anche consentono di configurare, eseguire il debug e distribuire il servizio cloud in Azure.

Una soluzione di servizio cloud Azure contiene i tipi di progetti seguenti:

- **Progetto Azure**

    Il progetto Azure contiene associazioni ai progetti ruolo della soluzione. Include inoltre la definizione di servizio e i file di configurazione del servizio. File di definizione del servizio consente di definire le impostazioni di runtime per l'applicazione inclusi i ruoli necessari, i punti finali e le dimensioni del computer virtuale. Il file di configurazione del servizio configura quante istanze di un ruolo da eseguire e i valori delle impostazioni definite per un ruolo. Per ulteriori informazioni su queste impostazioni, vedere [How to: configurare i ruoli per un servizio Cloud di Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

- **Progetto ruolo Web**

    Un ruolo di lavoro eseguita elaborazione in background. Un ruolo di lavoro può comunicare con i servizi di archiviazione e con altri servizi basati su Internet. Un ruolo di lavoro possa essere presenti più endpoint HTTP, HTTPS o TCP.

    - **Ruolo Web ASP.NET**per la creazione di un'applicazione di un front-end web ASP.NET
    - **Ruolo Web ASP.NET MVC5**
    - **Ruolo Web ASP.NET MVC4**
    - **Ruolo Web ASP.NET MVC3**
    - **Ruolo di WCF del servizio Web**per la creazione di un servizio WCF
    - **Ruolo di Silverlight Business applicazione Web** (richiede Visual Studio 2012)

- **Ruolo di lavoro cache**

    Ruolo che fornisce una cache dedicata all'applicazione.

- **Ruolo di lavoro con coda Bus del servizio**

    Coda bus servizio che offre funzionalità Accodamento dei messaggi per comunicare con il processo di lavoro. Per ulteriori informazioni, vedere [come utilizzare servizio Bus code](http://go.microsoft.com/fwlink/?LinkId=260560).

## <a name="to-create-an-azure-cloud-service-project-in-visual-studio"></a>Per creare un progetto di servizio cloud Azure in Visual Studio

1. Avviare Microsoft Visual Studio come amministratore.

1. Sulla barra dei menu scegliere **File**, **Nuovo** **progetto**.

1. Nel riquadro di **Tipi di progetto** , scegliere **Cloud** dal progetto c# o Visual Basic nodi di un modello.

1. Nel riquadro **modelli** scegliere **Servizio Cloud di Azure**.

1. Specificare quale versione di .NET Framework che si desidera utilizzare per sviluppare il progetto.

1. Immettere un nome e un percorso per il progetto e un nome per la soluzione. Scegliere il pulsante **OK** .

1. Nella finestra di dialogo **Nuovo progetto di Azure** scegliere i ruoli che si desidera aggiungere e quindi sul pulsante freccia destra per aggiungerli alla soluzione. È possibile aggiungere il numero di ruoli in base alle esigenze.

1. Per rinominare un ruolo è stato aggiunto a un progetto, passare il mouse sul ruolo nella finestra di dialogo **Nuovo progetto di Azure** e scegliere l'icona **rinominare** a destra del ruolo. È anche possibile rinominare un ruolo all'interno della soluzione dopo che è stato aggiunto.
