<properties
    pageTitle="Certificato indicazioni rinnovo per gli utenti di Office 365 e Azure Active Directory | Microsoft Azure"
    description="In questo articolo viene spiegato agli utenti di Office 365 risolvere i problemi con i messaggi di posta elettronica che notifica che il rinnovo di un certificato."
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


# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Rinnovare la federazione certificati per Office 365 e Azure Active Directory

##<a name="overview"></a>Panoramica

Per la federazione completata tra Azure Active Directory (Azure Active Directory) e Active Directory Federation Services (ADFS), i certificati utilizzati da ADFS per firmare i token di sicurezza di Azure Active Directory devono corrispondere che cos'è configurato in Azure Active Directory. Qualsiasi mancata corrispondenza può comportare protezione interrotti. Azure Active Directory garantisce che queste informazioni vengono mantenute sincronizzate quando si distribuisce ADFS e Proxy di applicazione Web (per extranet accesso).

In questo articolo fornisce informazioni aggiuntive per gestire i token certificati di firma e mantenerle sincronizzata con Azure Active Directory, nei casi seguenti:

* Non si distribuisce il Proxy di applicazione Web e pertanto non sono disponibili in extranet i metadati di federazione.
* Non si usa la configurazione predefinita di ADFS per token certificati di firma.
* Si sta utilizzando un provider di identità di terze parti.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configurazione predefinita di ADFS per token certificati di firma

La firma di token di token decrittografia certificati sono in genere certificati e sono ideali per un anno. Per impostazione predefinita, ADFS include un processo di rinnovo automatico denominato **AutoCertificateRollover**. Se si utilizza ADFS 2.0 o versioni successive, Office 365 e Azure AD aggiornare automaticamente il certificato prima della scadenza.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Notifica di rinnovo dal portale di Office 365 o un messaggio di posta elettronica

>[AZURE.NOTE] Se è stato ricevuto un messaggio di posta elettronica o una notifica portale richiesto per rinnovare il certificato per Office, vedere [Gestione assume la forma di certificati di firma di token](#managecerts) per verificare se è necessario eseguire altre azioni. Microsoft è presente un problema di notifiche per il rinnovo del certificato inviato, anche se è richiesta alcuna operazione potenziali.

Azure Active Directory tenta di eseguire il monitoraggio dei metadati di federazione e aggiornare il token di certificati di firma come indicato da questi metadati. 30 giorni prima della scadenza del token di certificati di firma Azure Active Directory viene verificato se nuovi certificati disponibili tramite il polling dei metadati di federazione.

* Se correttamente sondaggio i metadati di federazione e recuperare i nuovi certificati, notifica tramite posta elettronica o un avviso nel portale di Office 365 non viene rilasciato all'utente.
* Se non è possibile recuperare il nuovo token di certificati di firma, sia perché i metadati di federazione non è raggiungibili o attivazione automatica dei certificati non è attivato, Azure Active Directory invia una notifica tramite posta elettronica e un messaggio di avviso nel portale di Office 365.

![Notifica del portale di Office 365](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] Se si utilizza ADFS, per garantire la continuità aziendale, verificare che il server di disporre sempre degli aggiornamenti seguenti, in modo che non si verificano errori di autenticazione per i problemi noti. Consente di ridurre problemi di server proxy ADFS noti per il rinnovo e periodi di rinnovo future:
>
>Server 2012 R2 - [Windows Server cumulativo di maggio 2014](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 e 2012, [si verifica un errore di autenticazione tramite proxy in Windows 2008 R2 SP1 o Windows Server 2012](http://support.microsoft.com/kb/3094446)

## Verificare se è necessario aggiornare i certificati<a name="managecerts"></a>

### <a name="step-1-check-the-autocertificaterollover-state"></a>Passaggio 1: Controllare lo stato di AutoCertificateRollover

Nel server ADFS, aprire PowerShell. Verificare che il valore di AutoCertificateRollover è impostato su True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] Se si utilizza ADFS 2.0, eseguire Microsoft.Adfs.Powershell Add-Pssnapin.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Passaggio 2: Verificare che ADFS e Azure Active Directory siano aggiornati

Nel server ADFS, aprire il prompt dei comandi PowerShell di Azure Active Directory e connettersi a Azure Active Directory.

>[AZURE.NOTE] È possibile scaricare Azure Active Directory PowerShell [qui](https://technet.microsoft.com/library/jj151815.aspx).

    Connect-MsolService

Controllare i certificati configurati AD FS e Azure Active Directory attendibile le proprietà del dominio specificato.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Se identificazioni personali in entrambi gli output corrispondano, i certificati sono sincronizzati con Azure Active Directory.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Passaggio 3: Controllare se il certificato sta per scadere

Nell'output di Get-MsolFederationProperty o Get-AdfsCertificate, cercare la data in "Non dopo." Se la data è inferiore a 30 giorni non al computer, è necessario intervenire.

| AutoCertificateRollover | Certificati sincronizzati con Azure Active Directory | Metadati di federazione sono accessibili | Validità | Azione |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| Sì | Sì | Sì | - | Non sono necessari interventi. Vedere [rinnovare token automaticamente il certificato di firma](#autorenew). |
| Sì | No  | - | Meno di 15 giorni | Rinnovare immediatamente. Vedere [token rinnovare manualmente il certificato di firma](#manualrenew). |
| No | - | - | Meno di 30 giorni | Rinnovare immediatamente. Vedere [token rinnovare manualmente il certificato di firma](#manualrenew). |

\[Non è importante -]

## Rinnova token certificato di firma automaticamente (scelta consigliata)<a name="autorenew"></a>

Non è necessario eseguire le operazioni di manuale se entrambe le seguenti condizioni sono vere:
- Sono state realizzate Proxy di applicazione Web, che può consentire l'accesso ai metadati federazione dalla rete extranet.
- Si sta utilizzando la configurazione predefinita di ADFS (AutoCertificateRollover attivata).

Controllare le operazioni seguenti per verificare che il certificato può essere aggiornato automaticamente.

**1. la proprietà di ADFS AutoCertificateRollover deve essere impostata su True.** Questo indica che ADFS genera automaticamente la firma di token nuovo e certificati decrittografia token, prima il vecchio quelli scadenza.

**2. i metadati di federazione ADFS sono accessibili.** Verificare che i metadati di una federazione siano accessibili al pubblico posizionandosi l'URL seguente da un computer su internet pubblica (all'esterno della rete aziendale):


/federationmetadata/2007-06/federationmetadata.xml https:// (your_FS_name)

dove `(your_FS_name) `viene sostituito con il nome host del servizio di federazione l'organizzazione utilizza, ad esempio fs.contoso.com.  Se in grado di verificare entrambe queste impostazioni, non è necessario eseguire altre operazioni.  

Esempio: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Rinnova token manualmente il certificato di firma<a name="manualrenew"></a>

È possibile scegliere di eseguire il rinnovo token certificati di firma manualmente. Ad esempio gli scenari seguenti potrebbero funzionare meglio per il rinnovo manuale:
* Token certificati di firma sono certificati non autofirmati. La causa più comune per questo è che l'organizzazione gestisce i certificati di ADFS registrati da un'autorità di certificazione dell'organizzazione.
* Protezione di rete non i metadati di federazione sia disponibile pubblicamente.

In questi scenari, ogni volta che si aggiorna il token di certificati di firma è necessario aggiornare anche il dominio di Office 365 tramite il comando di PowerShell MsolFederatedDomain di aggiornamento.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Passaggio 1: Verificare che ADFS disponga di nuovo i token certificati di firma

**Configurazione non predefinita**

Se si utilizza una configurazione non predefinita di ADFS (dove **AutoCertificateRollover** è impostato su **False**), probabilmente si utilizza certificati personalizzati (non firmati). Per ulteriori informazioni su come rinnovare token ADFS certificati di firma, vedere [linee guida per i clienti che non utilizzano ADFS autofirmato certificati](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Federazione metadati non sono disponibili al pubblico**

Mano, se **AutoCertificateRollover** è impostato su **True**, ma i metadati di una federazione non è accessibili al pubblico, prima di tutto assicurarsi che siano stati generati nuovi certificati di firma del token da ADFS. Verificare di che avere nuovo token certificati di firma eseguendo la procedura seguente:

1. Verificare che si è connessi al server ADFS primario.
2. Controllare i certificati di firma correnti in ADFS, aprire una finestra di comando di PowerShell ed eseguire il comando seguente:

    PS c:\>ADFSCertificate Get-CertificateType-la firma di token

    >[AZURE.NOTE] Se si utilizza ADFS 2.0, è necessario eseguire prima di tutto Microsoft.Adfs.Powershell Add-Pssnapin.

3. Esaminare l'output del comando in tutti i certificati. Se ADFS ha generato un nuovo certificato, è necessario verificare due certificati nell'output: uno per cui il valore di **IsPrimary** è **True** e la data di **NotAfter** è all'interno di 5 giorni e una per il quale **IsPrimary** è **False** e **NotAfter** è su un anno in futuro.

4. Se è visualizzato solo un certificato e la data di **NotAfter** entro 5 giorni, è necessario generare un nuovo certificato.

5. Per generare un nuovo certificato, eseguire il comando seguente al prompt dei comandi di PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

6. Verificare l'aggiornamento eseguendo nuovamente il comando seguente: c: PS\>ADFSCertificate Get-CertificateType-la firma di token

Due certificati dovrebbero essere elencati a questo punto, uno dei quali dispone di una data di **NotAfter** di circa un anno in futuro e per cui il valore di **IsPrimary** è **False**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Passaggio 2: Aggiornare il token di nuovo l'accesso certificati per la protezione di Office 365

Aggiornare Office 365 con il nuovo token di accesso di certificati da utilizzare per la protezione, come indicato di seguito.

1.  Aprire il modulo di Microsoft Azure Active Directory per Windows PowerShell.
2.  Eseguire $cred = Get-Credential. Quando questo cmdlet richiede le credenziali, digitare le credenziali dell'account di amministratore del servizio cloud.
3.  Eseguire Connetti MsolService-$cred delle credenziali. Questo cmdlet consente di connettersi al servizio cloud. Creazione di un contesto che si connette al servizio cloud è necessario prima di eseguire il cmdlet aggiuntive installato tramite lo strumento.
4.  Se si esegue questi comandi in un computer che non corrisponde al server primario federazione ADFS, eseguire MSOLAdfscontext Set-Computer <AD FS primary server>, dove <AD FS primary server> è il nome FQDN interno del server ADFS primario. Questo cmdlet consente di creare una scelta rapida che si connette a ADFS.
5.  Eseguire l'aggiornamento MSOLFederatedDomain-DomainName <domain>. Questo cmdlet di aggiornare le impostazioni da ADFS nel servizio cloud e configura la relazione di attendibilità tra i due.


>[AZURE.NOTE] Se è necessario supportare più domini di primo livello, ad esempio contoso.com e fabrikam.com, è necessario utilizzare il parametro **SupportMultipleDomain** con i cmdlet. Per ulteriori informazioni, vedere [supporto per più domini di livello superiore](active-directory-aadconnect-multiple-domains.md).

## Ripristino di protezione di Azure Active Directory tramite Azure AD Connect<a name="connectrenew"></a>

Se è configurata la farm di ADFS e protezione di Azure Active Directory utilizzando Azure AD Connect, è possibile utilizzare Azure AD Connect per rilevare se è necessario eseguire altre azioni per i token certificati di firma. Se è necessario rinnovare i certificati, è possibile utilizzare Azure AD Connect per farlo.

Per ulteriori informazioni, vedere [il ripristino di protezione](./active-directory-aadconnect-federation-management.md#repairing-the-trust).
