<properties
    pageTitle="Provisioning gestione App aziendali nell'anteprima di Azure Active Directory dell'utente | Microsoft Azure"
    description="Informazioni su come gestire il provisioning di account utente per App aziendali utilizzando l'anteprima di Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/12/2016"
    ms.author="asmalser"/>

#<a name="preview-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>Anteprima: Gestione del provisioning per App aziendali nel portale di Azure nuovi account utente

In questo articolo viene descritto come utilizzare il [portale di Azure](https://portal.azure.com) per la gestione di account utente automatico il provisioning e deselezionare il provisioning per le applicazioni che supportano questa opzione, in particolare quelli che sono state aggiunte della categoria "in primo piano" della [raccolta di Azure Active Directory dell'applicazione](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). L'esperienza di gestione nel portale di Azure nuovo è attualmente in anteprima pubblica e in questo articolo descrive le nuove caratteristiche, nonché alcune limitazioni temporanee che sono attive durante il periodo di anteprima. [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md)

Per altre informazioni del provisioning degli account utente automatici e sul suo funzionamento, vedere [automatizzare utente il Provisioning e Deprovisioning alle applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).

##<a name="finding-your-apps-in-the-new-portal"></a>Trovare le app nel portale di nuovo

In settembre 2016, tutte le applicazioni che sono state configurate per single sign-on in una directory da un amministratore di directory usando la [raccolta di Azure Active Directory dell'applicazione](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) all'interno del [portale classica Azure](https://manage.windowsazure.com), possono ora visualizzate e gestire nel portale di Azure nuovo.

Queste applicazioni sono disponibili nella sezione **Applicazioni aziendali** di nuovo portale Azure, quali è possibile accedere tramite il menu di **Altri servizi** nel riquadro di spostamento sinistro. App aziendali sono applicazioni che sono state distribuite e in uso da parte degli utenti all'interno dell'organizzazione.

![Blade applicazioni aziendali][0]

Selezionando il collegamento di **tutte le applicazioni** a sinistra viene visualizzato un elenco di tutte le app che sono stati configurati, tra cui App che sono stati aggiunti dalla raccolta. Selezione di un'app carica e delle risorse per tale app, in cui per tale app, è possibile visualizzare i report e una serie di impostazioni può essere gestita.

Account utente di impostazioni per il provisioning può essere gestito selezionando **Provisioning** sul lato sinistro.

![Blade risorsa applicazione][1]


##<a name="provisioning-modes"></a>Modalità di provisioning

E il **Provisioning** inizia con un menu **modalità** , che mostra le modalità di provisioning sono supportate per un'applicazione aziendale e consente di configurare. Le opzioni disponibili includono:

* **Automatico** - questa opzione viene visualizzata se Azure Active Directory supporta in base all'API di provisioning automatico e/o disattivare il provisioning degli account utente per questa applicazione. Selezionando questa modalità vengono visualizzati un'interfaccia che consente agli amministratori tramite la configurazione di Azure Active Directory per connettersi a Gestione utente dell'applicazione API, la creazione di flussi di lavoro che definiscono come dovrebbero flusso di dati di account utente tra Azure Active Directory e i mapping di account e l'app e gestione di Azure Active Directory il provisioning di servizio.

* **Manuale** : questa opzione viene visualizzata se Azure Active Directory non supportano il provisioning automatico degli account utente per questa applicazione. Questa opzione indica che il record di account utente archiviato nell'applicazione deve essere gestito usando un processo esterno, in base alle funzionalità di gestione e il provisioning di utente fornita dall'applicazione (ad esempio il provisioning di SAML JIT).


##<a name="configuring-automatic-user-account-provisioning"></a>Configurazione del provisioning degli account utente automatico

Selezionare l'opzione **automatico** , viene visualizzata una schermata divisa in quattro sezioni:

###<a name="admin-credentials"></a>Credenziali di amministratore
Verrà visualizzata nel punto in cui le credenziali necessarie per Azure Active Directory per connettersi a Gestione utente dell'applicazione API sono stati immessi. L'input richiesto varia a seconda dell'applicazione. Per ulteriori informazioni sui tipi di credenziali e i requisiti per applicazioni specifiche, vedere l' [esercitazione di configurazione per tale applicazione specifica](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning).

Selezionare il pulsante **Test connessione** consente di verificare le credenziali mediante la ricezione di Azure tentativo di Active Directory per connettersi all'app del provisioning app mediante le credenziali.

###<a name="mappings"></a>Mapping
Si tratta in cui gli amministratori possono visualizzare e modificare il flusso di attributi utente tra Azure Active Directory e l'applicazione di destinazione, quando gli account utente viene completato il provisioning o aggiornati.

Esiste un insieme di mapping tra gli oggetti utente di Azure Active Directory e gli oggetti utente dell'applicazione ogni SaaS preconfigurato. Alcune App gestire altri tipi di oggetti, ad esempio gruppi o contatti. Selezione di uno di questi mapping nella tabella seguente l'editor di mapping a destra, in cui visualizzare e personalizzati.

![Blade risorsa applicazione][2]

Personalizzazioni supportate durante l'anteprima prima includono:

* Attivazione e disattivazione mapping per gli oggetti specifici, ad esempio l'oggetto utente di Azure Active Directory all'oggetto utente dell'applicazione SaaS.

* Modificare gli attributi che scorrono dall'oggetto utente di Azure Active Directory all'oggetto utente dell'applicazione. Per ulteriori informazioni sul mapping degli attributi, vedere [informazioni sui tipi di mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).

* Filtrare le operazioni di provisioning Azure Active Directory da eseguire nell'applicazione di destinazione, ovvero una nuova caratteristica nel portale di Azure. Invece di avere Azure Active Directory-sincronizzare completamente gli oggetti, è possibile limitare le azioni eseguite. Ad esempio, da solo selezionando **aggiornamento**, Azure Active Directory solo gli aggiornamenti utente esistente account in un'applicazione e non creare nuovi file. Selezionando solo **creare**Azure solo crea nuovi account utente ma non vengono aggiornate quelle esistenti. Questa caratteristica consente agli amministratori creare un mapping diversi per la creazione di account e aggiornare i flussi di lavoro. La possibilità di effettuare creare più mapping per app è previsto per in un punto successivo del periodo di anteprima.

###<a name="settings"></a>Impostazioni
In questa sezione consente agli amministratori avviare e arrestare Azure AD il provisioning di servizio per l'applicazione selezionata, nonché se lo si desidera cancellare la cache del provisioning e riavviare il servizio.

Se il provisioning è abilitato per la prima volta per un'applicazione, attivare il servizio cambiando lo **Stato di Provisioning** su **On**. Questo determina Azure AD il provisioning di servizio eseguire una sincronizzazione iniziale, nel punto in cui viene letto gli utenti assegnati nella sezione **utenti e gruppi** , l'applicazione di destinazione per le query. quindi esegue il provisioning azioni definite nella sezione Azure Active Directory **mapping** Durante questo processo, il servizio di provisioning archivia dati memorizzati nella cache sui quali account utente gestire, in modo che gli account non gestito all'interno di applicazioni di destinazione che sono state mai nell'ambito di assegnazione non incidono deselezionare il provisioning di operazioni. Dopo la sincronizzazione iniziale, il servizio di provisioning Sincronizza automaticamente utente e raggruppare oggetti in un intervallo di dieci minuti.

Modificare lo **Stato di Provisioning** su **Off** semplicemente sospende il servizio di provisioning. In questo stato Azure non creare, aggiornare o rimuovere eventuali oggetti utente o gruppo nell'app. Modifica dello stato su Sì, il servizio di occuparsi in cui è stata interrotta.

Selezionare la casella di controllo **deselezionare lo stato corrente e riavviare la sincronizzazione** e salvare interrompe il servizio di provisioning, immagini, i dati memorizzati nella cache sui quali account Azure Active Directory usata per la gestione, riavvia i servizi ed esegue nuovamente la sincronizzazione iniziale. Questa opzione consente agli amministratori di ricominciare il processo di distribuzione provisioning.

###<a name="synchronization-details"></a>Dettagli sincronizzazione
In questa sezione fornisce aggiunta dettagli sul funzionamento del servizio di provisioning, tra cui i nome e il cognome esecuzioni il servizio di provisioning contro l'applicazione e il numero di utenti e raggruppare oggetti gestiti.

I collegamenti vengono forniti al **rapporto di attività di Provisioning**, che offre un file di log di tutti gli utenti e gruppi creati, aggiornati e rimosse tra Azure AD e dettagliate di destinazione dell'applicazione e la **segnalazione errori di Provisioning** che fornisce più messaggi di errore per utenti e raggruppare oggetti che non è riuscito a leggere, creato, aggiornato o rimosso. 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
