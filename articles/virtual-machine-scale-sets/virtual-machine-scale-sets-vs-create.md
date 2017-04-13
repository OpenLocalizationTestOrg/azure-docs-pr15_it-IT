<properties
    pageTitle="Distribuire macchina virtuale scala Set mediante Visual Studio | Microsoft Azure"
    description="Distribuire insiemi di scala macchina virtuale mediante Visual Studio e un modello di Manager delle risorse"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="guybo"/>

# <a name="deploy-virtual-machine-scale-set-using-visual-studio"></a>Distribuire macchina virtuale scala Set mediante Visual Studio

In questo articolo viene illustrato come distribuire una scala di macchina virtuale ai Azure Set mediante un gruppo di distribuzione Visual Studio delle risorse.


[Azure macchina virtuale scala set](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) sono una risorsa Azure calcolare per distribuire e gestire una raccolta di macchine virtuali simili con facilità di integrazione opzioni per ridimensionamento automatico e il bilanciamento del carico. È possibile eseguire il provisioning e distribuire macchine Virtuali scala insiemi utilizzare [i modelli di Azure risorsa Manager (ARM)](https://github.com/Azure/azure-quickstart-templates). Modelli di ARM possono essere distribuiti utilizzando resto CLI Azure, PowerShell e anche direttamente da Visual Studio. Visual Studio fornisce un set di esempio modelli che può essere distribuito come parte di un progetto di distribuzione di Azure risorse del gruppo.

Distribuzioni di gruppo di risorse Azure rappresentano un modo per raggruppare e pubblicare un set di risorse Azure correlate in un'operazione su una singola distribuzione. Altre informazioni sulle loro qui: [creazione e la distribuzione di gruppi di risorse Azure tramite Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Prerequisiti in corso

Per iniziare la distribuzione di macchine Virtuali scala set in Visual Studio è necessario quanto segue:

- Visual Studio 2013 o 2015
- Azure SDK 2.7, 2,8 o 2.9

Nota: Queste istruzioni si supponga di che utilizzare Visual Studio 2015 con [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Creazione di un progetto

1. Creare un nuovo progetto di Visual Studio 2015 scegliendo **File | Nuovo | Progetto**.

    ![File nuovo][file_new]

2. In **c# | Cloud**, scegliere **Gestione risorse di Azure** per creare un progetto per la distribuzione di un modello di ARM.

    ![Creazione di progetto][create_project]

3.  Dall'elenco dei modelli, selezionare il modello di Set di Windows macchina virtuale scala o Linux.

    ![Selezionare modello][select_Template]

4. Una volta creato il progetto è verranno visualizzati gli script di PowerShell distribuzione, un modello di gestione risorse di Azure e un file di parametri per il Set di scala macchina virtuale.

    ![Esplora soluzioni][solution_explorer]

## <a name="customize-your-project"></a>Personalizzare il progetto

A questo punto è possibile modificare il modello per personalizzare per esigenze specifiche dell'applicazione, ad esempio le proprietà di estensione macchine Virtuali di aggiungere o modificare le regole di bilanciamento del carico. Per impostazione predefinita i modelli di impostare scala macchine Virtuali sono configurati per distribuire l'estensione AzureDiagnostics che semplifica l'aggiunta di regole autoscale. Distribuisce anche un bilanciamento del carico con un indirizzo IP pubblico, configurato con le regole in entrata di NAT che consentono di connettersi alle istanze di macchine Virtuali con SSH (Linux) o RDP (Windows)-l'intervallo di porte front-end compreso 50000, ovvero nel caso di Linux, se si SSH alla porta 50000 dell'indirizzo IP pubblico (o il nome di dominio) verrà indirizzato alla porta 22 di macchine Virtuali prima del Set di scala. Connessione alla porta 50001 verrà reindirizzata alla porta 22 della seconda macchina virtuale e così via.

 Un buon modo per modificare i modelli con Visual Studio consiste nell'utilizzare la struttura di JSON per organizzare i parametri, variabili e risorse. Le informazioni dello schema di Visual Studio può indicare gli errori nel modello scelto prima di distribuirlo.

![Esplora JSON][json_explorer]

## <a name="deploy-the-project"></a>Distribuire il progetto

6. Distribuire il modello ARM in Azure per creare la risorsa macchine Virtuali scala impostata. Fare clic con il pulsante destro sul nodo del progetto, scegliere **Distribuisci | Nuova distribuzione**.

    ![Distribuire modello][5deploy_Template]

7. Selezionare l'abbonamento a nella finestra di dialogo "Distribuire al gruppo di risorse".

    ![Distribuire modello][6deploy_Template]

8. Da qui è possibile anche creare un nuovo gruppo di risorse Azure per distribuire il modello.

    ![Nuovo gruppo di risorse][new_resource]

9. Selezionare il pulsante **Modifica parametri** per immettere i parametri che verranno passati al modello di alcuni valori, ad esempio il nome utente e password per il sistema operativo sono necessari per creare la distribuzione. Se non si dispone di PowerShell Tools per Visual Studio installati, è consigliabile selezionare "Salva password" per evitare un nascosti PowerShell prompt dei comandi o utilizzare [keyvault supporto](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).

    ![Modificare i parametri][edit_parameters]

10. Fare clic su **Distribuisci**. Nella finestra di **Output** verrà visualizzato lo stato di avanzamento di distribuzione. Si noti che l'azione è in esecuzione script **Distribuisci AzureResourceGroup.ps1** .

    ![Finestra di output][output_window]

## <a name="exploring-your-vm-scale-set"></a>Esplorare i Set di scala macchine Virtuali

Una volta completata la distribuzione, è possibile visualizzare il nuovo Set scala macchine Virtuali di Visual Studio **Cloud Explorer** (aggiornare l'elenco). Esplora cloud consente di gestire le risorse Azure in Visual Studio durante lo sviluppo di applicazioni. È inoltre possibile visualizzare il Set di scala macchine Virtuali nel [Portale di Azure](https://portal.azure.com) e [Azure Resource Explorer](https://resources.azure.com/).

![Esplora cloud][cloud_explorer]

 Il portale rappresenta il modo migliore per visivamente gestire l'infrastruttura di Azure con un web browser, mentre Esplora risorse Azure offre un modo semplice per explorer ed eseguire il debug Azure risorse, assegnando una finestra nella visualizzazione istanza"" e anche con i comandi di PowerShell per le risorse che attualmente visualizzata. Mentre macchine Virtuali scala set in anteprima, Esplora risorse illustra i maggior parte dei dati di dettaglio per il set di scala macchine Virtuali.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito macchine Virtuali scala set tramite Visual Studio per personalizzare ulteriormente il progetto in base alle esigenze dell'applicazione. Aggiunta di una risorsa approfondimenti, aggiungendo infrastruttura per il modello, ad esempio autonoma macchine virtuali o la distribuzione di applicazioni utilizzando l'estensione di script personalizzati, ad esempio configurando autoscale. Un'ottima fonte di modelli di esempio sono disponibili in [Modelli di Guida introduttiva di Azure](https://github.com/Azure/azure-quickstart-templates) GitHub repository (ricerca di "vmss").

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
