<properties
    pageTitle="Risoluzione dei problemi: Creare e connettersi a un'area di lavoro di apprendimento | Microsoft Azure"
    description="Soluzioni a problemi comuni per la creazione e la connessione a un'area di lavoro di apprendimento Azure"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="garye"/>


# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Guida alla risoluzione dei problemi: creare e connettersi a un'area di lavoro di apprendimento

Questa guida vengono fornite le soluzioni per alcuni si verificano spesso problemi quando si configurano le aree di lavoro di apprendimento Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Proprietario dell'area di lavoro

Quando si crea una nuova area di lavoro di apprendimento, l'ID immesso nel campo proprietario dell'area di lavoro deve essere un account Microsoft valido (in precedenza Windows Live ID), ad esempio john-contoso@live.com o john-contoso@hotmail.com. Non può essere un account Microsoft non, ad esempio l'account di posta elettronica aziendale. Per creare un account Microsoft gratuito, passare a [www.live.com](http://www.live.com).

Si noti che l'account usato per accedere al portale di Azure per creare l'area di lavoro dispone automaticamente dell'autorizzazione per *aprire* tale area di lavoro, a meno che non si specifica tale account come proprietario. Per aprire un'area di lavoro nel computer risorse Studio, è necessario essere connessi all'account Microsoft che è stata definita come il proprietario dell'area di lavoro oppure è necessario ricevere un invito dal proprietario per partecipare l'area di lavoro. Dal portale di classica Azure è possibile, tuttavia, *gestire* l'area di lavoro che include la possibilità di modificare il proprietario e configurare l'accesso.

Per ulteriori informazioni sulla gestione di un'area di lavoro, vedere [gestire un'area di lavoro di apprendimento Azure].

[Gestire un'area di lavoro di apprendimento Azure]: machine-learning-manage-workspace.md

## <a name="allowed-regions"></a>Aree consentite

Apprendimento è attualmente disponibile in un numero limitato di aree geografiche. Se l'abbonamento non include una di queste aree, venga visualizzato il messaggio di errore, "Non hai alcuna sottoscrizione in aree consentite."

Per richiedere l'aggiunta di un'area all'abbonamento, selezionare **Contattare il supporto tecnico Microsoft** dal portale classica di Azure, scegliere come tipo di problema di **fatturazione** e seguire le istruzioni visualizzate per inviare la richiesta.

![Contattare il supporto tecnico Microsoft][screen1]

## <a name="storage-account"></a>Account di archiviazione

Il servizio di apprendimento è necessario un account di archiviazione per archiviare i dati. È possibile usare un account di archiviazione esistente oppure è possibile creare un nuovo account di archiviazione quando si crea nuova area di lavoro di apprendimento (se si dispone di quota per creare un nuovo account di archiviazione).

<!-- These instructions no longer work, but I'm not sure what to replace them with
To see if you can create a new storage account, in the Classic Portal, go to **Settings** and then click **Usage**.
-->

![Creare l'area di lavoro][screen2]

Dopo aver creata la nuova area di apprendimento, è possibile accedere al computer risorse Studio utilizzando l'account Microsoft specificato come il proprietario dell'area di lavoro. Se viene visualizzato il messaggio di errore, "Area di lavoro non trovata" (simile alla schermata riportata di seguito), utilizzare la procedura seguente per eliminare i cookie del browser.

![Area di lavoro non trovato][screen3]

**Per eliminare i cookie del browser**

Se si usa Internet Explorer, fare clic sul pulsante **Strumenti** in alto a destra e selezionare **Opzioni Internet**.  

![Opzioni Internet][screen4]

Nella scheda **Generale** fare clic su **Elimina...**

![Scheda Generale][screen5]

Nella finestra di dialogo **Elimina cronologia esplorazioni** , verificare che **i cookie e dati del sito Web** sia selezionata e fare clic su **Elimina**.

![Eliminare i cookie][screen6]

Una volta eliminati i cookie, riavviare il browser e quindi passare alla pagina di [Microsoft Azure apprendimento](https://studio.azureml.net) . Quando viene chiesto di immettere il nome utente e la password, immettere lo stesso account Microsoft specificato come il proprietario dell'area di lavoro.

Il nostro obiettivo è rendono l'esperienza di apprendimento il più agevole possibili. È possibile inviare i commenti e i problemi relativi al [forum di apprendimento Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) per consentire a un servizio migliore.

[screen1]:media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
