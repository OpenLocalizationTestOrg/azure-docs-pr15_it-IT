<properties
    pageTitle="Azure AD Connect più domini"
    description="In questo documento viene impostazione e configurazione di più domini di primo livello con Office 365 e Azure Active Directory."
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

# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Supporto per più di dominio per la federazione con Azure Active Directory
La documentazione seguente vengono fornite indicazioni su come utilizzare più domini di primo livello e sottodomini quando la federazione con domini di Azure Active Directory o Office 365.

## <a name="multiple-top-level-domain-support"></a>Supporto per più domini di primo livello
La federazione più, domini di primo livello con Azure Active Directory richiede un'ulteriore configurazione che non sono necessaria se la federazione con un dominio di primo livello.

Quando un dominio federato con Azure Active Directory, diverse proprietà del dominio in Azure.  Uno dei più importante è IssuerUri.  Si tratta di un URI che viene utilizzato da Azure Active Directory per identificare il dominio che il token è associato.  URI non è necessario risolvere diverso, deve essere un URI valido.  Per impostazione predefinita, Azure Active Directory imposta il valore dell'identificatore servizio federazione il locale ADFS configurazione.

>[AZURE.NOTE]L'identificatore di servizio federazione è un URI che identifica un servizio federativo.  Il servizio federativo è un'istanza di ADFS che funziona come servizio token di sicurezza. 

È possibile visualizzare IssuerUri utilizzando il comando PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Quando si desidera aggiungere più domini di primo livello, si verifica un problema.  Ad esempio, si supponga disporre federazione configurazione tra Azure Active Directory e l'ambiente locale.  Per questo documento si sta utilizzando bmcontoso.com.  A questo punto ho aggiunto un dominio di primo livello, secondo, bmfabrikam.com.

![Domini](./media/active-directory-multiple-domains/domains.png)

Quando si tenta di convertire il dominio bmfabrikam.com deve essere federato, viene visualizzato un errore.  Il motivo è Azure Active Directory ha un vincolo che non consente la proprietà IssuerUri per avere lo stesso valore per più di un dominio.  
  

![Errore di federazione](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parametro SupportMultipleDomain

Per risolvere questo, è necessario aggiungere un altro IssuerUri che può essere eseguita tramite il `-SupportMultipleDomain` parametro.  Questo parametro viene utilizzato con i cmdlet seguenti:
    
- `New-MsolFederatedDomain`
- `Convert-MsolDomaintoFederated`
- `Update-MsolFederatedDomain`

Questo parametro rende Azure Active Directory configurare il IssuerUri in modo che si basa sul nome del dominio.  Questo sarà univoco in elenchi in linea in Azure Active Directory.  Utilizzando il parametro consente il comando di PowerShell completare correttamente.

![Errore di federazione](./media/active-directory-multiple-domains/convert.png)

Trovare le impostazioni del nuovo dominio bmfabrikam.com che è possibile vedere le risorse seguenti:

![Errore di federazione](./media/active-directory-multiple-domains/settings.png)

Si noti che `-SupportMultipleDomain` non modificare altri endpoint che ancora configurato in modo che punti al nostro servizio federazione in adfs.bmcontoso.com.

Un altro aspetto che `-SupportMultipleDomain` indica che assicura che il sistema di ADFS include il valore emittente appropriato in token rilasciati per Azure Active Directory. A tale scopo eseguendo la parte dominio degli utenti UPN e configurazione del dominio in IssuerUri, vale a dire https://{upn suffisso} / adfs, servizi/protezione. 

In questo modo durante l'autenticazione di Azure Active Directory o Office 365, l'elemento IssuerUri token dell'utente viene utilizzato per individuare il dominio in Azure Active Directory.  Se non è possibile trovare una corrispondenza che l'autenticazione avrà esito negativo. 

Ad esempio, se un utente UPN è bsimon@bmcontoso.com, l'elemento IssuerUri i problemi di ADFS token Active Directory verrà impostato su http://bmcontoso.com/adfs/services/trust. Questa operazione deve corrispondere alla configurazione di Azure Active Directory e l'autenticazione avrà esito positivo.

Di seguito è la regola Richiedi personalizzate che implementata questa logica:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


>[AZURE.IMPORTANT]Per utilizzare il parametro - SupportMultipleDomain quando si tenta di aggiungere nuovi o convertire già aggiunto domini, è necessario che il programma di installazione la protezione federati per supportare loro originariamente.  


## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Come aggiornare il trust tra ADFS e Azure Active Directory
Se non si configurazione trust tra ADFS e l'istanza di Azure Active Directory, è necessario ricreare la protezione.  In questo modo, quando è originariamente installazione senza il `-SupportMultipleDomain` parametro, il IssuerUri è impostato con il valore predefinito.  Nella schermata seguente è possibile visualizzare che il IssuerUri è impostato su https://adfs.bmcontoso.com/adfs/services/trust.

Il momento in tal caso, se si ha aggiunto un nuovo dominio nel portale di Azure Active Directory e si tenta di convertirlo utilizzando `Convert-MsolDomaintoFederated -DomainName <your domain>`, viene restituito l'errore seguente.

![Errore di federazione](./media/active-directory-multiple-domains/trust1.png)

Se si tenta di aggiungere la `-SupportMultipleDomain` parametro è verrà visualizzato l'errore seguente:

![Errore di federazione](./media/active-directory-multiple-domains/trust2.png)

È sufficiente tenta di eseguire `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` nel dominio originale anche verrà generato un errore.

![Errore di federazione](./media/active-directory-multiple-domains/trust3.png)

Utilizzare la procedura seguente per aggiungere un altro dominio di primo livello.  Se si è già stato aggiunto un dominio e non si utilizza il `-SupportMultipleDomain` parametro iniziano con la procedura per la rimozione e l'aggiornamento del dominio originale.  Se non è stato aggiunto un dominio di primo livello ancora è possibile iniziare con la procedura per aggiungere un dominio tramite PowerShell di Azure AD Connect.

Utilizzare la procedura seguente per rimuovere la protezione di Microsoft Online e aggiornare il dominio originale.

2.  Nel server federativo ADFS Apri **AD FS Management.** 
2.  A sinistra, espandere **Relazioni di Trust** e **Attendibilità componente**
3.  A destra, eliminare la voce di **Piattaforma identità di Microsoft Office 365** .
![Rimuovere Microsoft Online](./media/active-directory-multiple-domains/trust4.png)
1.  Su un computer che ha [Modulo Azure Active Directory per Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) in cui è installato, eseguire le operazioni seguenti: `$cred=Get-Credential`.  
2.  Immettere il nome utente e la password di un amministratore globale per creare una federazione con il dominio di Azure Active Directory
2.  Immettere nella PowerShell`Connect-MsolService -Credential $cred`
4.  PowerShell immettere `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Si tratta del dominio originale.  Utilizzando domini indicati che sarebbe:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`


Utilizzare la procedura seguente per aggiungere il nuovo dominio di primo livello tramite PowerShell

1.  Su un computer che ha [Modulo Azure Active Directory per Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) in cui è installato, eseguire le operazioni seguenti: `$cred=Get-Credential`.  
2.  Immettere il nome utente e la password di un amministratore globale per creare una federazione con il dominio di Azure Active Directory
2.  Immettere nella PowerShell`Connect-MsolService -Credential $cred`
3.  Immettere nella PowerShell`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Utilizzare la procedura seguente per aggiungere il nuovo dominio di primo livello con Azure AD Connect.

1.  Avvio Azure AD Connect dal desktop o menu start
2.  Scegliere "Aggiungi un altro dominio Active Directory Azure" ![aggiunge un ulteriore dominio di Azure Active Directory](./media/active-directory-multiple-domains/add1.png)
3.  Immettere un annuncio Azure e le credenziali Active Directory
4.  Selezionare il dominio di secondo che si desidera configurare per la federazione.
![Aggiungere un ulteriore dominio di Azure Active Directory](./media/active-directory-multiple-domains/add2.png)
5.  Fare clic su Installa


### <a name="verify-the-new-top-level-domain"></a>Verificare il nuovo dominio di primo livello
Tramite il comando PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`è possibile visualizzare IssuerUri aggiornati.  Nella schermata seguente mostra la federazione impostazioni sono state aggiornate il nostro http://bmcontoso.com/adfs/services/trust dominio originale

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

E IssuerUri nel nuovo dominio è stata impostata su https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)


##<a name="support-for-sub-domains"></a>Supporto per i domini secondari
Quando si aggiunge un sottodominio, a causa di domini modo Azure Active Directory gestito, eredita le impostazioni dell'elemento padre.  Questo errore indica che il IssuerUri deve corrispondere gli elementi padre.

Pertanto consente ad esempio, ad esempio dispone di bmcontoso.com e quindi aggiungere corp.bmcontoso.com.  Questo errore indica che il IssuerUri per un utente da corp.bmcontoso.com dovranno essere **http://bmcontoso.com/adfs/services/trust.**  Tuttavia, la regola standard implementata sopra per Azure Active Directory, verrà generato un token con un emittente come **http://corp.bmcontoso.com/adfs/services/trust.** che non corrisponderanno del dominio necessari valore e autenticazione avrà esito negativo.

### <a name="how-to-enable-support-for-sub-domains"></a>Come attivare il supporto per sottodomini
Per risolvere il problema di ADFS componente protezione di terze parti per Microsoft Online deve essere aggiornato.  A questo scopo è necessario configurare una regola attestazione personalizzata in modo che da rimuovere disattivazione dei sottodomini da suffisso UPN dell'utente quando si crea il valore emittente personalizzato. 

Richiedi seguenti verranno procedere come segue:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));

Utilizzare la procedura seguente per aggiungere un'attestazione personalizzata per il supporto sottodomini.

1.  Apri AD FS Management
2.  Fare clic con il pulsante destro la protezione di Microsoft Online RP e scegliere Richiedi modifica regole
3.  Selezionare la regola Richiedi terza e sostituire ![Richiedi modifica](./media/active-directory-multiple-domains/sub1.png)
4.  Sostituire la richiesta corrente:
    
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
        
    con
    
        `c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));`
    
![Sostituire Richiedi](./media/active-directory-multiple-domains/sub2.png)
5.  Fare clic su Ok.  Fare clic su Applica.  Fare clic su Ok.  Chiudere AD FS Management.

