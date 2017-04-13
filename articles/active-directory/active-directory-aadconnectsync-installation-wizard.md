<properties
    pageTitle="Sincronizzazione di Azure AD Connect: esecuzione dell'installazione guidata una seconda volta | Microsoft Azure"
    description="Viene illustrata la procedura guidata installazione la seconda volta che si esegue."
    keywords="Installazione guidata di Azure AD Connect consente di configurare le impostazioni di manutenzione la seconda volta che si esegue"
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Sincronizzazione di Azure AD Connect: esecuzione dell'installazione guidata una seconda volta
Alla prima esecuzione di installazione guidata di Azure AD Connect illustra come configurare l'installazione. Se si esegue nuovamente l'installazione guidata, verrà chiesto di opzioni per la manutenzione.

È possibile trovare l'installazione guidata nel menu start denominato **Azure AD Connect**.

![Menu Start](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Quando si avvia l'installazione guidata, viene visualizzata una pagina con queste opzioni:

![Pagina con un elenco di operazioni aggiuntive.](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Se è stato installato ADFS con Azure AD Connect, sono disponibili anche altre opzioni. Le opzioni aggiuntive che disponibili per ADFS vengono riportate nella [gestione di ADFS](active-directory-aadconnect-federation-management.md#ad-fs-management).

Selezionare una delle attività e fare clic su **Avanti** per continuare.

> [AZURE.IMPORTANT] Mentre è aperto l'installazione guidata, vengono sospesi tutte le operazioni di motore di sincronizzazione. Verificare che si chiude l'installazione guidata non appena si apportato le modifiche di configurazione.

## <a name="view-current-configuration"></a>Configurazione di visualizzazione corrente
Questa opzione fornisce una breve descrizione delle opzioni di attualmente configurate.

![Pagina con un elenco di tutte le opzioni e il relativo stato](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Fare clic su **Indietro** per tornare indietro. Se si selezionano **Esci**, chiudere la procedura guidata installazione.

## <a name="customize-synchronization-options"></a>Personalizzare le opzioni di sincronizzazione
Questa opzione viene utilizzata per apportare modifiche alla configurazione di sincronizzazione. Viene visualizzato un sottoinsieme delle opzioni dal percorso di installazione di configurazione personalizzata. Questa opzione è disponibile anche se è stata utilizzata per l'installazione rapida inizialmente.

- [Aggiungere più directory](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Per la rimozione di una directory, vedere [eliminare un connettore](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
- [Unità Organizzativa di filtro e Cambia dominio](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
- Rimuovere il filtro di gruppo.
- [Caratteristiche facoltative cambia](active-directory-aadconnect-get-started-custom.md#optional-features).

Altre opzioni di installazione iniziale non possano essere modificati e non sono disponibili. Queste opzioni sono:

- Modificare l'attributo da usare per userPrincipalName e sourceAnchor.
- Modificare il metodo di unione per gli oggetti da foresta diversa.
- Abilitare il filtro basato sul gruppo.

## <a name="refresh-directory-schema"></a>Aggiornamento dello schema di directory
Questa opzione viene utilizzata se è stato modificato lo schema in uno dei propri locali insiemi di strutture di Active Directory. Ad esempio, potrebbe avere installato Exchange o l'aggiornamento a uno schema di Windows Server 2012 con gli oggetti dispositivo. In questo caso, è necessario indicare Azure AD Connect rileggere lo schema da Active Directory e aggiornare la cache. Questa azione rigenera anche le regole di sincronizzazione. Se si aggiunge lo schema di Exchange, ad esempio, le regole di sincronizzazione per Exchange vengono aggiunti alla configurazione.

Quando si seleziona questa opzione, sono elencate tutte le directory di configurazione. È possibile mantenere l'impostazione predefinita e aggiorna tutti gli insiemi di strutture oppure deselezionare alcune di esse.

![Pagina con un elenco di tutte le directory nell'ambiente](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurare la modalità di gestione temporanea
Questa opzione consente di attivare e disattivare la modalità di gestione temporanea nel server. Ulteriori informazioni sulla modalità e la modalità di utilizzo di gestione temporanea disponibili nelle [operazioni](active-directory-aadconnectsync-operations.md#staging-mode).

L'opzione Mostra se gestione temporanea è attualmente attivato o disattivato:  
![Opzione che viene inoltre visualizzato lo stato corrente della modalità di gestione temporanea](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Per modificare lo stato, selezionare questa opzione e selezionare o deselezionare la casella di controllo.  
![Opzione che viene inoltre visualizzato lo stato corrente della modalità di gestione temporanea](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Modificare account utente
Questa opzione consente di modificare dalla sincronizzazione delle password per la federazione o viceversa. Non è possibile modificare a **non si configura**.

Per ulteriori informazioni su questa opzione, vedere [accesso degli utenti](active-directory-aadconnect-user-signin.md#changing-user-sign-in-method).

## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni sul modello di configurazione usato dal servizio di sincronizzazione per [Il Provisioning dichiarativo Understanding](active-directory-aadconnectsync-understanding-declarative-provisioning.md)Azure AD Connect.

**Argomenti della panoramica**

- [Sincronizzazione di Azure AD Connect: comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
