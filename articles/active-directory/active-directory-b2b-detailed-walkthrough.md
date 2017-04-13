<properties
   pageTitle="Procedura dettagliata di utilizzando l'anteprima di collaborazione di Azure Active Directory B2B | Microsoft Azure"
   description="Azure Active Directory B2B collaborazione supporta le relazioni tra società abilitando business partner in modo selettivo accedere alle applicazioni aziendali"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# <a name="azure-ad-b2b-collaboration-preview-detailed-walkthrough"></a>Anteprima di collaborazione Azure Active Directory B2B: procedura dettagliata

In questa procedura dettagliata viene descritto come utilizzare la collaborazione di Azure Active Directory B2B. Gli amministratori IT di Contoso si desidera condividere applicazioni con i dipendenti di tre società partner. Nessuna delle società partner è necessario Azure Active Directory.

- Alice dal Partner semplice organigramma
- Davide dall'organizzazione Partner di supporto, è necessario l'accesso a un insieme di App
- Adriana dall'organizzazione Partner complesse, è necessario l'accesso a un insieme di App e appartenenza ai gruppi di Contoso

Dopo inviti vengono inviati agli utenti di partner, è possibile configurarli in Azure Active Directory per concedere l'accesso alle App e appartenenza ai gruppi tramite il portale di Azure. Iniziamo con l'aggiunta di Alice.

## <a name="adding-alice-to-the-contoso-directory"></a>Aggiunta di Alice nella directory di Contoso
1. Creare un file CSV con le intestazioni, come illustrato, compilare solo **posta elettronica**Alice, **DisplayName**e **InviteContactUsUrl**. **DisplayName** è il nome visualizzato nell'invito e il nome visualizzato nella directory di Contoso Azure Active Directory. **InviteContactUsUrl** consente di Alice contattare Contoso. Nell'esempio seguente InviteContactUsUrl specifica il profilo LinkedIn di Contoso. È importante compitare le etichette nella prima riga del file CSV esattamente come specificato nel [riferimento al formato file CSV](active-directory-b2b-references-csv-file-format.md).  
![File CSV di esempio per Alice](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. Nel portale di Azure, aggiungere un utente nella directory di Contoso (Active Directory > Contoso > utenti > Aggiungi utente). Nell'elenco a discesa ""tipo di utente verso il basso, selezionare "Utenti nella società partner". Caricare il file CSV. Verificare che il file CSV è chiusa prima di caricare.  
![Caricamento dei file CSV per Alice](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice a questo punto viene rappresentata come un utente esterno nella directory di Contoso Azure Active Directory.  
![Alice è elencata in Azure Active Directory](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Alice riceverà il seguente messaggio di posta elettronica.  
![Posta elettronica di invito ad Alice](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice fare clic sul collegamento Anna è richiesto per accettare l'invito e accedere usando le credenziali di lavoro. Se Alice non è presente nella directory di Azure Active Directory, Alice viene richiesto di effettuare l'iscrizione.  
![Iscriversi invito ad Alice](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice viene reindirizzata al pannello di accesso di App, vuota finché non viene ha concesso l'accesso alle app.  
![Pannello di accesso ad Alice](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Questa procedura consente la forma più semplice di collaborazione B2B. Un utente nella directory di Contoso Azure AD Alice possibile concedere l'accesso alle applicazioni e gruppi tramite il portale di Azure. A questo punto aggiungere Roberto cui concedere l'accesso alle applicazioni Moodle e Salesforce.

## <a name="adding-bob-to-the-contoso-directory-and-granting-access-to-apps"></a>Aggiungere Roberto alla directory di Contoso e concedere l'accesso alle App
1. Usare Windows PowerShell con il modulo di Azure Active Directory installato per trovare gli ID applicazione di Moodle e Salesforce. È possibile recuperare gli ID utilizzando il cmdlet: `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` verrà visualizzato un elenco di tutte le applicazioni disponibili in Contoso e loro AppPrincialIds.  
![Recupera ID per Roberto](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Creare un file CSV contenente Luca posta elettronica e DisplayName **InviteAppID**, **InviteAppResources**e InviteContactUsUrl. Popolare **InviteAppResources** con AppPrincipalIds di Moodle Salesforce rilevato da PowerShell, separati da uno spazio. Inserire **InviteAppId** con AppPrincipalId stesso di Moodle per personalizzare il messaggio di posta elettronica e pagine con un logo Moodle l'accesso.  
![File CSV di esempio per Roberto](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Caricare il file csv tramite il portale Azure esattamente come è stata effettuata ad Alice. A questo punto è un utente esterno nella directory di Contoso Azure Active Directory.

4. Davide riceverà il seguente messaggio di posta elettronica.  
![Posta elettronica di invito per Roberto](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Davide fa clic sul collegamento e viene richiesto di accettare l'invito. Dopo aver effettuato, ha indirizzato al pannello di accesso e già usare Moodle e Salesforce.  
![Comando per Roberto](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Verrà aggiunto Adriana, cui concedere l'accesso alle applicazioni e appartenenza ai gruppi nella directory di Contoso.

## <a name="adding-carol-to-the-contoso-directory-granting-access-to-apps-and-giving-group-membership"></a>Aggiungere Adriana alla directory di Contoso, concessione dell'accesso alle App e fornisce l'appartenenza ai gruppi

1. Utilizzare Windows PowerShell con il modulo di Azure Active Directory per trovare l'ID dell'applicazione e l'ID di gruppo all'interno di Contoso.
 - Recuperare AppPrincipalId utilizzando cmdlet `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`, uguale Roberto
 - Recuperare ObjectId per i gruppi utilizzando cmdlet `Get-MsolGroup | fl DisplayName, ObjectId`. Verrà visualizzato un elenco di tutti i gruppi di Contoso e loro ObjectID. ID di gruppo possono essere recuperati anche come ID oggetto, nella scheda proprietà del gruppo nel portale di Azure.  
![Recuperare gli ID e gruppi per Adriana](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. Creare file con estensione csv, compilare di Adriana messaggio di posta elettronica, DisplayName, InviteAppID, InviteAppResources, **InviteGroupResources**e InviteContactUsUrl. **InviteGroupResources** viene popolata da ObjectID dei gruppi MyGroup1 ed esterni, separati da uno spazio.  
![File CSV di esempio per Adriana](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Caricare il file csv tramite il portale di Azure.

4. Adriana è un utente nella directory di Contoso ed è anche un membro dei gruppi MyGroup1 ed esterni, come illustrato nel portale di Azure.  
![Adriana è presente nell'elenco in un gruppo in Azure Active Directory](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Adriana riceve un messaggio di posta elettronica contenente un collegamento per accettare l'invito. Dopo l'utente accede, Anna viene reindirizzata al riquadro App Access abbia accesso a Moodle e Salesforce.  

È tutto esiste all'aggiunta di utenti da aziende partner collaborazione con Azure Active Directory B2B. Questa procedura viene illustrato come aggiungere utenti Alice, Davide e Adriana nella directory di Contoso con tre file CSV distinti. Questo processo può essere eseguito più facile da condensa file CSV distinti in un unico file.  
![File CSV di esempio per Alice, Davide e Adriana](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## <a name="related-articles"></a>Articoli correlati
Individuare gli altri articoli sulla collaborazione di Azure Active Directory B2B:

- [Che cos'è la collaborazione di Azure Active Directory B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Come funziona](active-directory-b2b-how-it-works.md)
- [Informazioni sui formati file CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato di token di utenti esterni](active-directory-b2b-references-external-user-token-format.md)
- [Modifiche degli attributi oggetto utente esterno](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitazioni relative all'anteprima corrente](active-directory-b2b-current-preview-limitations.md)
- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
