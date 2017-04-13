<properties 
    pageTitle="Iniziare a utilizzare l'autenticazione certificato basato su iOS | Microsoft Azure" 
    description="Informazioni su come configurare l'autenticazione basata su certificato soluzioni con i dispositivi iOS" 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/21/2016" 
    ms.author="markvi" />



# <a name="get-started-with-certificate-based-authentication-on-ios---public-preview"></a>Iniziare a utilizzare l'autenticazione certificato basato su iOS - Public Preview

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


In questo argomento viene illustrato come configurare e utilizzare l'autenticazione basata su certificato (CBA) in un dispositivo iOS per gli utenti del tenant nei piani di Office 365 Enterprise, Business e per l'istruzione. 

CBA consente di essere autenticati da Azure Active Directory con un certificato client in un dispositivo Android o iOS quando ci si connette l'account di Exchange online per: 

- Applicazioni per dispositivi mobili di Office, ad esempio Microsoft Outlook e Microsoft Word   
- Client di Exchange ActiveSync (EAS) 

Configurazione di questa caratteristica non è più necessario immettere una combinazione di nome utente e password in alcune telefonica e applicazioni di Microsoft Office in un dispositivo mobile. 
 

## <a name="supported-scenarios-and-requirements"></a>Requisiti e scenari supportati  



### <a name="general-requirements"></a>Requisiti generali 


Per tutti gli scenari di questo argomento sono necessarie le attività seguenti:  

- Accesso alle autorità di certificato da emettere certificati client.  

- Autorità certificati deve essere configurata in Azure Active Directory. È possibile trovare informazioni dettagliate sulle procedure per completare la configurazione nella sezione [Guida introduttiva](#getting-started) .  

- Autorità di certificazione principale e un'autorità di certificazione intermedie deve essere configurata in Azure Active Directory.  

- Ogni autorità di certificazione deve avere un elenco di revoche di certificati (CRL) che si desidera fare riferimento tramite un URL è connessa a Internet.  

- Il certificato deve essere rilasciato per l'autenticazione client.  


- Per solo client di Exchange ActiveSync, il certificato client deve avere indirizzo di posta elettronica routing dell'utente in Exchange online in nome dell'entità o il valore di nome RFC822 del campo nome alternativo oggetto. Azure Active Directory associa il valore RFC822 per l'attributo indirizzo Proxy nella directory.  



### <a name="office-mobile-applications-support"></a>Supporto per applicazioni per dispositivi mobili di Office 

| App                      | Supporto tecnico      |
| ---                       | ---          |
| Word / Excel o PowerPoint | ![Controllo][1]  |
| OneNote                   | ![Controllo][1]  |
| OneDrive                  | ![Controllo][1]  |
| Outlook                   | ![Controllo][1]  |
| Yammer                    | ![Controllo][1]  |
| Skype per le aziende        | Prossimamente  |


### <a name="requirements"></a>Requisiti  

La versione del sistema operativo per dispositivi deve essere iOS 9 e versioni successive 

Configurare un server federativo.  

Azure autenticatore è richiesto per le applicazioni Office in iOS.  

Per Azure Active Directory revocare un certificato client, il token ADFS deve essere attestazioni seguenti:  

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
(Il numero seriale del certificato client) 

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
(La stringa per autorità di certificazione client) 

Azure Active Directory aggiunge le richieste al token di aggiornamento se sono disponibili in token ADFS (o qualsiasi altra token SAML). Quando il token di aggiornamento deve essere convalidata, queste informazioni vengono usate per controllare la revoca. 

Come ottimale, è necessario aggiornare le pagine di errore ADFS con le operazioni seguenti:

- Requisiti per l'installazione autenticatore Azure in iOS

- Istruzioni su come ottenere un certificato utente. 

Per ulteriori informazioni, vedere [personalizzare le pagine di accesso in ADFS Active Directory](https://technet.microsoft.com/library/dn280950.aspx).  



### <a name="exchange-activesync-clients-support"></a>Supporto per i client Exchange ActiveSync 


In iOS 9 o versioni successive, il client di posta iOS nativo è supportato. Per tutte le altre applicazioni di Exchange ActiveSync, per determinare se questa caratteristica verrà supportata, contattare lo sviluppatore dell'applicazione.  



## <a name="getting-started"></a>Guida introduttiva 


Per iniziare, è necessario configurare l'autorità di certificazione di Azure Active Directory. Per ogni autorità di certificazione, caricare le operazioni seguenti: 

- La parte pubblica del certificato in formato *cer* 

- Internet affiancate URL in cui si trovano elenchi di revoche certificati (CRL)
 

Di seguito è lo schema per un'autorità di certificazione: 

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


Per caricare le informazioni, è possibile usare il modulo di Azure Active Directory mediante Windows PowerShell.  
Di seguito sono illustrati esempi per l'aggiunta, rimozione o modifica di un'autorità di certificazione. 



### <a name="configuring-your-azure-ad-tenant-for-certificate-based-authentication"></a>Configurare il tenant di Azure Active Directory per certificato di autenticazione basata su 

1. Avviare Windows PowerShell con privilegi di amministratore. 

2. Installare il modulo di Azure Active Directory. È necessario installare la versione [1.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0) o versione successiva.  

        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 

3. Connettersi al tenant di destinazione: 

        Connect-AzureAD 

### <a name="adding-a-new-certificate-authority"></a>Aggiunta di una nuova autorità di certificazione

1. Impostare diverse proprietà di autorità di certificazione e aggiungerla a Azure Active Directory: 

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. Ottenere l'autorità di certificazione: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="retrieving-the-list-certificate-authorities"></a>Recupero di autorità di certificazione elenco

Recuperare l'autorità di certificazione attualmente archiviati in Azure Active Directory per il tenant: 

        Get-AzureADTrustedCertificateAuthority 


### <a name="removing-a-certificate-authority"></a>Rimozione di un'autorità di certificazione

1.  Recuperare l'autorità di certificazione: 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Rimuovere il certificato per l'autorità di certificazione: 

        Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### <a name="modfiying-a-certificate-authority"></a>Modfiying un'autorità di certificazione 

1.  Recuperare l'autorità di certificazione: 

        $c=Get-AzureADTrustedCertificateAuthority 


2. Modificare le proprietà in Autorità di certificazione: 

        $c[0].AuthorityType=1 

3. Impostare l' **autorità di certificazione**: 

        Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## <a name="testing-office-mobile-applications"></a>Verifica delle applicazioni di Office mobile  

Per verificare l'autenticazione certificato sull'applicazione di Office mobile: 

1.  In un dispositivo di prova, installare un'applicazione per dispositivi mobili di Office (ad esempio OneDrive) dall'App Store.

2.  Verificare che il certificato utente sia stato preparato al dispositivo test. 

3.  Avviare l'applicazione. 

4.  Immettere il nome utente e quindi selezionare il certificato utente che si desidera utilizzare. 

Deve essere completato l'accesso. 





## <a name="testing-exchange-activesync-client-applications"></a>Verifica delle applicazioni client di Exchange ActiveSync

Per accedere a Exchange ActiveSync tramite autenticazione basata su certificato, un profilo EAS contenente il certificato client devono essere disponibile per l'applicazione. Il profilo EAS deve contenere le informazioni seguenti:

- Il certificato da utilizzare per l'autenticazione utente 

- Endpoint EAS deve essere Office365 (come questa funzionalità è attualmente supportata solo nell'ambiente di Exchange online multi-tenant)

Un profilo EAS può essere configurato e inserire nel dispositivo tramite l'utilizzo di un MDM, ad esempio Intune oppure inserendo manualmente il certificato nel profilo EAS sul dispositivo.  

### <a name="testing-eas-client-applications-on-ios"></a>Verifica delle applicazioni client EAS in iOS 

Per verificare l'autenticazione certificato con l'applicazione di posta nativi in iOS 9 o versioni successive: 

1.  Configurare un profilo EAS che soddisfa i requisiti sopra. 

2.  Installare il profilo sul dispositivo iOS (utilizzando un MDM, ad esempio Intune o dell'applicazione di configurazione di Apple)

3.  Dopo aver installato correttamente il profilo, aprire l'applicazione di posta elettronica nativo e verificare che la sincronizzazione della posta



## <a name="revocation"></a>Revoca

Per revocare il certificato client, Azure Active Directory recupera l'elenco di revoche di certificati (CRL) dagli URL caricato come parte di informazioni autorità di certificazione e memorizza nella cache. Pubblicare l'ultimo timestamp (**Data di validità** proprietà) nell'elenco CRL viene utilizzato per garantire il CRL è ancora valido. Il CRL fa periodicamente per revocare l'accesso ai certificati che fanno parte dell'elenco.

Se una revoca più immediata è richiesta (ad esempio, se un utente viene a mancare un dispositivo), è possibile invalidato il token di autorizzazione dell'utente. Per invalidate il token di autorizzazione, impostare il campo **StsRefreshTokenValidFrom** per l'utente usando Windows PowerShell. È necessario aggiornare il campo **StsRefreshTokenValidFrom** per ogni utente che si desidera revocare l'accesso per.
 
Per assicurarsi che la revoca persiste, è necessario impostare la **Data di validità** del CRL a una data dopo il valore impostato dal **StsRefreshTokenValidFrom** e assicurarsi che il certificato in questione è nell'elenco CRL.
 
I passaggi seguenti illustrano il processo di aggiornamento e invalidando il token di autorizzazione impostando il campo **StsRefreshTokenValidFrom** . 

1. Connettersi con le credenziali di amministratore per il servizio di MSOL: 

        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 

1.  Recuperare il valore corrente di StsRefreshTokensValidFrom per un utente: 

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
        $user.StsRefreshTokensValidFrom 


1.  Configurare un nuovo valore StsRefreshTokensValidFrom per l'utente uguale timestamp corrente: 

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


La data che è impostare deve essere in futuro. Se la data non è in futuro, il **StsRefreshTokensValidFrom** non è impostata. Se la data in futuro, **StsRefreshTokensValidFrom** è impostato per l'ora corrente (non la data indicata dal comando Set-MsolUser). 



<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png