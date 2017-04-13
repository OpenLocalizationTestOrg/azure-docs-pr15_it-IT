<properties
    pageTitle="Azure AD Connect: Passaggi successivi e su come gestire Azure AD Connect | Microsoft Azure"
    description="Informazioni su come estendere la configurazione predefinita e attività operative per Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>Passaggi successivi e su come gestire Azure AD Connect
Di seguito è avanzata operativi che consentono di personalizzare Azure Active Directory connettersi per soddisfare le esigenze dell'organizzazione.  

## <a name="add-additional-sync-administrators"></a>Aggiungere amministratori di sincronizzazione aggiuntive
Per impostazione predefinita solo l'utente che si è verificato l'installazione e administrators locale saranno in grado di gestire il motore di sincronizzazione installata. Per altre persone a essere in grado di accedere e gestire il motore di sincronizzazione, individuare il gruppo denominato ADSyncAdmins nel server locale e aggiungerli al gruppo.

## <a name="assigning-licenses-to-azure-ad-premium-and-enterprise-mobility-users"></a>Assegnazione di licenze agli utenti di Azure Active Directory Premium e mobilità aziendale

Ora che gli utenti sono stati sincronizzati nel cloud, è necessario assegnare loro una licenza in modo è possibile procedere con applicazioni basate su cloud, ad esempio Office 365.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Per assegnare un premio di Azure Active Directory o la licenza corrente di famiglia di prodotti mobilità Enterprise
--------------------------------------------------------------------------------
1. Effettuare l'accesso al portale di Azure come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Nella pagina Active Directory, fare doppio clic sulla cartella che contiene gli utenti che si desidera attivare.
4. Nella parte superiore della pagina della directory, selezionare **licenze**.
5. Nella pagina licenze selezionare Active Directory Premium o Enterprise mobilità Suite e quindi fare clic su **Assegna**.
6. Nella finestra di dialogo selezionare gli utenti che si desidera assegnare licenze a e quindi fare clic sull'icona di segno di spunta per salvare le modifiche.


## <a name="verifying-the-scheduled-synchronization-task"></a>Verificare l'attività di sincronizzazione pianificata
Se si desidera controllare lo stato di una sincronizzazione è possibile eseguire questa operazione controllando nel portale di Azure.

### <a name="to-verify-the-scheduled-synchronization-task"></a>Per verificare l'attività di sincronizzazione pianificata
--------------------------------------------------------------------------------
1. Effettuare l'accesso al portale di Azure come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Nella pagina Active Directory, fare doppio clic sulla cartella che contiene gli utenti che si desidera attivare.
4. Nella parte superiore della pagina della directory, selezionare **Integrazione della Directory**.
5. In integrazione con active directory locale l'ora dell'ultima sincronizzazione.

<center>![Cloud](./media/active-directory-aadconnect-whats-next/verify.png)</center>

## <a name="starting-a-scheduled-synchronization-task"></a>Avvio di un'attività di sincronizzazione pianificata
Se è necessario eseguire un'attività di sincronizzazione è possibile eseguire questa operazione eseguendo nuovamente la guidata Azure AD Connect.  È necessario specificare le credenziali di Azure Active Directory.  Nella procedura guidata, selezionare l'attività di **personalizzare le opzioni di sincronizzazione** e fare clic su Avanti nella procedura guidata. Al termine, verificare che sia selezionata la casella **Avvia il processo di sincronizzazione non appena viene completato la configurazione iniziale** .

<center>![Cloud](./media/active-directory-aadconnect-whats-next/startsynch.png)</center>

Per ulteriori informazioni sulla sincronizzazione Azure AD Connect: utilità di pianificazione, vedere [Pianificazione di connessione di Azure Active Directory](active-directory-aadconnectsync-feature-scheduler.md)


## <a name="additional-tasks-available-in-azure-ad-connect"></a>Altre operazioni disponibili in Azure AD Connect
Dopo l'installazione iniziale di Azure AD Connect, è possibile sempre avviare la creazione guidata nuovo dal collegamento Azure AD Connect start pagina o desktop.  Si noterà che attraversano nuovamente la procedura guidata offre alcune nuove opzioni sotto forma di altre attività.  

La tabella seguente contiene un riepilogo di tali attività e una breve descrizione di ciascuno.

![Partecipare a regola](./media/active-directory-aadconnect-whats-next/addtasks.png)


Altre attività | Descrizione
------------- | ------------- |
Visualizzare lo scenario selezionato  |Consente di visualizzare la soluzione di Azure AD Connect corrente.  Sono inclusi impostazioni generali, le directory sincronizzate, le impostazioni di sincronizzazione e così via.
Personalizzare le opzioni di sincronizzazione | Consente di modificare la configurazione corrente, incluse l'aggiunta di altri insiemi di strutture di Active Directory per la configurazione o l'attivazione opzioni di sincronizzazione, ad esempio utente, gruppo, dispositivo o password writeback.
Attivare la modalità di gestione temporanea |  In questo modo è per le informazioni sul passaggio che verrà sincronizzate in un secondo momento, ma non verranno esportati in Azure Active Directory o Active Directory.  In questo modo è possibile visualizzare in anteprima la sincronizzazione prima che si verifichino.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
