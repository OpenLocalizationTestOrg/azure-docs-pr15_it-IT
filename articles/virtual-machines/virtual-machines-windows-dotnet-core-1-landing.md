<properties
   pageTitle="Azure macchina virtuale DotNet Core dell'esercitazione 1 | Microsoft Azure"
   description="Esercitazione DotNet Core Azure macchina virtuale"
   services="virtual-machines-windows"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="nepeters"/>

# <a name="automating-application-deployments-to-azure-virtual-machines"></a>Automazione delle distribuzioni di applicazioni per macchine virtuali di Azure

Questa serie in quattro parti dei dettagli di distribuzione e la configurazione di Azure risorsa e le applicazioni che usano modelli di gestione delle risorse di Azure. In questa serie, un modello di esempio viene distribuito ed esaminata il modello di distribuzione. L'obiettivo di questa serie è per formare sulla relazione tra le risorse Azure o per fornire mani nell'ambiente di distribuzione di modelli di gestione di risorse Azure completamente integrato. In questo documento prende in considerazione un livello di gestione risorse Azure knowledge base, prima di iniziare questa esercitazione, acquisire familiarità con i concetti di base Manager delle risorse di Azure.

## <a name="music-store-application"></a>Applicazione di archiviazione musica

L'esempio utilizzato in questa serie è .net applicazione Core simulazione un archivio di musica esperienza di acquisto. Questa applicazione può essere distribuita al sistema virtuale di Windows o Linux, sono state create le distribuzioni di esempio per entrambe. L'applicazione include un'applicazione web e un database SQL. Prima di leggere gli articoli in questa serie, distribuire l'applicazione con il pulsante di distribuzione presente in questa pagina. Quando è completamente distribuito, l'architettura applicazione / Azure simile a nel diagramma seguente. 

Il modello di gestione risorse archivio musica sono disponibili qui, [Musica modello di Windows Store](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

![Applicazione di archiviazione di musica](./media/virtual-machines-windows-dotnet-core/music-store.png)

Ognuno di essi, incluso il modello associare JSON viene esaminata in questi quattro articoli.

- [**Architettura delle applicazioni**](./virtual-machines-windows-dotnet-core-2-architecture.md) , componenti di applicazioni, ad esempio siti web e database devono essere ospitati risorse computer Azure, ad esempio macchine virtuali e i database di SQL Azure. In questo documento vengono esaminati necessità di elaborazione mapping, alle risorse Azure e la distribuzione di queste risorse con un modello di gestione di risorse Azure. 

- [**Accesso e la sicurezza**](./virtual-machines-windows-dotnet-core-3-access-security.md) : durante l'hosting di applicazioni di Azure, è necessario considerare come si accede all'applicazione e funzionamento di diversi dell'applicazione componenti di accedere a altro. In questo documento dettaglio fornendo e protezione di accesso a internet a un'applicazione e accesso tra i componenti dell'applicazione.

- [**Disponibilità e scalabilità**](./virtual-machines-windows-dotnet-core-4-availability-scale.md) – disponibilità e scalabilità fare riferimento alla possibilità di applicazioni per rimanere in esecuzione durante il tempo di inattività infrastruttura e la possibilità di calcolo le risorse per rispondere alla domanda dell'applicazione. In questo documento dettagli dei componenti necessari per distribuire un bilanciamento del carico e applicazione disponibilità.

- [**Distribuzione di applicazioni**](./virtual-machines-windows-dotnet-core-5-app-deployment.md) - quando la distribuzione delle applicazioni in macchine virtuali di Azure, è necessario considerare il metodo di calcolo in base al quale file binari dell'applicazione sono installati nel computer virtuale. In questo documento dettaglio automazione installazione dell'applicazione utilizzando le estensioni di Script personalizzati di Azure macchina virtuale.

L'obiettivo durante lo sviluppo di modelli di gestione di risorse Azure è per automatizzare la distribuzione dell'infrastruttura di Azure e l'installazione e configurazione di applicazioni ospitata su questa infrastruttura Azure. Esecuzione di questi articoli viene fornito un esempio di questa esperienza.

## <a name="deploy-the-music-store-application"></a>Distribuire l'applicazione di archiviazione musica

L'applicazione dell'archiviazione musica può essere distribuito utilizzando questo pulsante.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-windows%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

Il modello di gestione di risorse Azure richiede i valori di parametro seguenti.

|Nome parametro |Descrizione   |
|---|---|
|ADMINUSERNAME   | Nome utente amministratore viene utilizzata la macchina virtuale e il Database di SQL Azure.  |
|ADMINPASSWORD | Password da utilizzare per la macchina virtuale Azure e Database SQL.  |
|NUMBEROFINSTANCES | Il numero di macchine virtuali da creare. Ognuna di queste macchine virtuali ospitare l'applicazione di web Store musica e tutto il traffico è il bilanciamento del carico tra di loro. |
|PUBLICIPADDRESSDNSNAME | Nome DNS univoco globale associato all'indirizzo IP pubblico. |

Quando è stata completata la distribuzione dei modelli, quindi individuare l'indirizzo IP pubblico utilizzando qualsiasi browser internet. .Net verrà presentata le principali musica sito.

## <a name="next-steps"></a>Passaggi successivi

<hr>

[Passaggio 1 - architettura delle applicazioni con i modelli di Azure Manager delle risorse](./virtual-machines-windows-dotnet-core-2-architecture.md)

[Passaggio 2 - accesso e la sicurezza in Azure modelli Manager delle risorse](./virtual-machines-windows-dotnet-core-3-access-security.md)

[Passaggio 3 - disponibilità e scalabilità nei modelli di gestione risorse di Azure](./virtual-machines-windows-dotnet-core-4-availability-scale.md)

[Passaggio 4 - distribuzione di applicazioni con i modelli di Azure Manager delle risorse](./virtual-machines-windows-dotnet-core-5-app-deployment.md)


