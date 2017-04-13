<properties
    pageTitle="Panoramica del portale di Microsoft Azure"
    description="Informazioni su come usare il portale di Microsoft Azure."
    services=""
    documentationCenter=""
    authors="davidwrede"
    manager="dwrede"
    editor="jimbe"/>

<tags
    ms.service="na"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/16/2015"
    ms.author="dwrede"/>

# <a name="microsoft-azure-portal-overview"></a>Panoramica del portale di Microsoft Azure

Il portale di Microsoft Azure è una posizione centralizzata in cui è possibile eseguire il provisioning e gestire le risorse Azure.  In questa esercitazione verrà acquisire familiarità con il portale e viene illustrato come utilizzare alcune di queste funzionalità chiave:
- **Marketplace esaustivo** che consente di sfogliare migliaia di elementi da Microsoft e altri fornitori che possono essere acquistati e/o il provisioning.
- Un' **esperienza uniforme e scalabilità Sfoglia** che semplifica l'individuazione delle risorse è rilevante ed eseguire varie operazioni di gestione.
- **Pagine di gestione coerente** (o pale) che consentono di gestire vari di Azure servizi tramite un modo coerente di esposizione impostazioni, azioni, fatturazione informazioni, dati di monitoraggio e sull'utilizzo dell'integrità e molto altro.
- Un' **esperienza personale** che consente di creare una schermata start personalizzate che visualizza le informazioni che si desidera visualizzare ogni volta che si accede.  È anche possibile personalizzare qualsiasi delle pale gestione che contengono le sezioni.

 ![Orientamento dell'interfaccia utente del portale Azure][UIOrientation]

## <a name="before-you-get-started"></a>Prima di iniziare

È necessario un abbonamento valido a Azure elaborata in questa esercitazione.  Se non si dispone di uno, quindi [iscrizione per un periodo di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/) oggi.  Dopo avere creato una sottoscrizione, è possibile accedere al portale in [https://portal.azure.com].

## <a name="how-to-create-a-resource"></a>Creazione di una risorsa

Azure è marketplace con migliaia di elementi creati da un'unica posizione.  Supponiamo che si desidera creare un nuovo 2012 macchine Virtuali di Windows Server.  La + nuovo hub è il punto di ingresso in un set di curated delle categorie in primo piano da marketplace.  Ogni categoria è un piccolo gruppo di elementi in primo piano insieme a un collegamento a marketplace completo che mostra tutte le categorie e ricerca. Per creare il nuovo 2012 macchine Virtuali di Windows Server, eseguire le operazioni seguenti:  

1.  Windows Server 2012 è in primo piano, in modo che è possibile selezionare la categoria di calcolo.  
2.  Compilare alcuni elementi di base in una maschera.
3.  Fare clic su 'Crea' e vengono avviata la macchine Virtuali di provisioning immediatamente.

Hub notifiche verranno Avvisa quando la risorsa è stata creata e verrà aperta una pala gestione (può sempre Sfoglia alle risorse in un secondo momento).

![Categorie del portale][PortalCategories]


## <a name="how-to-find-your-resources"></a>Come trovare le risorse

È sempre possibile aggiungere risorse frequentemente al startboard, ma potrebbe essere necessario passare a un elemento che si non accede di frequente.  Hub Sfoglia illustrato di seguito è il modo per accedere a tutte le risorse.  È possibile filtrare in base al abbonamento, scegliere/ridimensionare colonne e passare a pale gestione facendo clic su singoli elementi.

![Esplorare Hub][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Modalità di gestione e accesso delegati per una risorsa

Da questa blade è possibile connettere la macchina virtuale con desktop remoto, eseguire il monitoraggio delle metriche di prestazioni chiave, controllare l'accesso a questa macchina virtuale utilizzando l'accesso basato sui ruoli (RBAC), configurare la macchina virtuale e altre attività di gestione importanti.  Delega di accesso in base al ruolo è fondamentale per la gestione proporzioni.  Fare clic [qui](./active-directory/role-based-access-control-configure.md) per ottenere ulteriori informazioni. Per delegare l'accesso a una risorsa, eseguire le operazioni seguenti:

1.  Passare alla risorsa.
2.  Fare clic su 'Tutte le impostazioni' nella sezione nozioni di base.
3.  Nell'elenco impostazioni, fare clic su "Utenti".
4.  Fare clic su 'Aggiungi' nella barra dei comandi.
5.  Scegliere un utente e un ruolo.

![Gestione di una risorsa][ManageResource]

## <a name="how-to-customize-a-resource-blade"></a>Come personalizzare una pala delle risorse

Azure configuri blade per le risorse, ma i riquadri in queste pale potranno essere al controllo.  È possibile accedere facilmente a personalizzare la modalità per aggiungere, rimuovere, ridimensionare o ridisporre i riquadri. Per personalizzare una pala, eseguire le operazioni seguenti:

1.  Passare alla risorsa.
2.  Fare clic sul "..." nella parte superiore e il si desidera personalizzare.
3.  Fare clic su 'Aggiungi parti'.
4.  Iniziare a parti di trascinamento della selezione.  

![Personalizzazione pale][CustomizeBlades]

## <a name="how-to-get-help"></a>Come è possibile ottenere assistenza

Se hai mai un problema, siamo qui automaticamente.  Il portale dispone di una pagina di Guida e supporto tecnico che consentono di individuare eventuali nella direzione corretta.  A seconda del [piano di supporto](https://azure.microsoft.com/support/plans/), è anche possibile creare ticket di supporto direttamente nel portale.  Dopo aver creato un ticket di supporto, è possibile gestire il ciclo di vita del biglietto all'interno del portale. È possibile ottenere assistenza e supporto pagina posizionandosi Sfoglia -> Guida + supporto.  

![Guida e supporto tecnico][HelpSupport]

## <a name="summary"></a>Riepilogo

Esaminiamo quanto appreso in questa esercitazione:
- Si è appreso come iscriversi, ottenere una sottoscrizione e passare al portale
- Se si ha orientamento con il portale dell'interfaccia utente e appreso come creare e visualizzare le risorse
- È stato illustrato come creare una risorsa ed esplorare le risorse
- Si ha conosciuto blade struttura o gestione e come è possibile gestire in modo coerente tipi diversi di risorse
- Viene descritto come personalizzare il portale per visualizzare le informazioni è rilevante per il primo piano e Allinea al centro
- Viene descritto come controllare l'accesso alle risorse mediante accesso basato sui ruoli (RBAC)
- Viene descritto come ottenere assistenza e supporto

Portale di Microsoft Azure completamente semplifica la creazione e la gestione delle applicazioni nel cloud.  Consultare il [blog di gestione](https://azure.microsoft.com/blog/topics/management/) per mantenere aggiornati come siamo costantemente [ascolto commenti e suggerimenti](https://feedback.azure.com/forums/223579-azure-preview-portal/) e apportare miglioramenti.  [Blog di ScottGu](http://weblogs.asp.net/scottgu) rappresenta un'altra ideale per cercare tutti gli aggiornamenti di Azure.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
