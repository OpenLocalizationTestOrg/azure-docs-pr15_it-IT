<properties
    pageTitle="Elenco di Azure Active Directory federation compatibilità"
    description="Questa pagina contiene i provider di identità non Microsoft che possono essere utilizzati per implementare il single sign-on."
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
    ms.date="09/12/2016"
    ms.author="billmath"/>

# <a name="azure-ad-federation-compatibility-list"></a>Elenco di Azure Active Directory federation compatibilità
Azure Active Directory fornisce single sign-in e protezione avanzata di applicazione access per Office 365 e altri servizi Online Microsoft per l'implementazione basata solo su cloud e ibrido senza la necessità di qualsiasi soluzione non Microsoft. Office 365, ad esempio la maggior parte dei servizi Online Microsoft, è integrato con Azure Active Directory per servizi directory, l'autenticazione e le autorizzazioni. Azure Active Directory vengono forniti anche servizio single sign-on a migliaia di applicazioni SaaS e le applicazioni web locale. Vedere la raccolta di applicazione di Azure Active Directory per le applicazioni supportate SaaS.

Per le organizzazioni che hanno investito in soluzioni non Microsoft federation, in questo argomento contiene indicazioni per la configurazione di single sign-on per gli utenti di Windows Server Active Directory con Microsoft Online services utilizzando provider di identità non Microsoft nel "Azure Active Directory federation compatibilità elenco" seguente. 


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Gruppo di Computer Oxford](http://oxfordcomputergroup.com/)terze parti, per conto di Microsoft, testati tali esperienze single sign-on con provider di identità non Microsoft con una serie di casi di utilizzo comune Azure Active Directory.

Per informazioni su come ottenere il provider di identità di terze parti elencato qui, contattare il gruppo Computer Oxford [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

>[AZURE.IMPORTANT] Gruppo di Computer Oxford verificato solo le funzionalità di federazione di questi scenari di single sign-on. Gruppo di Computer Oxford non ha eseguito prove sulla sincronizzazione, autenticazione a due fattori, ecc componenti di questi scenari di single sign-on.

>Utilizzo di accesso in base ID alternativo UPN non viene inoltre verificato questo programma.



- [Azure Active Directory](#azure-active-directory)
- [IDM ottimale identità virtuale Server Federation Services](#optimal-idm-virtual-identity-server-federation-services) 
- [6.11 PingFederate](#pingfederate-611) 
- [7.2 PingFederate](#pingfederate-72) 
- [PingFederate 8](#pingfederate-8x)
- [Centrify risponde](#centrify) 
- [IBM Tivoli federato gestione identità 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
- [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
- [Autorità di certificazione SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
- [RadiantOne CFS 3.0](#radiantone-cfs-30) 
- [Okta](#okta) 
- [OneLogin](#onelogin) 
- [Manager di accesso NetIQ 4.0.1](#netiq-access-manager-401) 
- [GRANDE IP con IP di gestione dei criteri di accesso grande versione 11.3 x-11.6](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x) 
- [Portale di area di lavoro VMware versione 2.1](#vmware-workspace-portal-version-21) 
- [Accedere e passare 5.3](#signampgo-53) 
- [IceWall federazione versione 3.0](#icewall-federation-version-30) 
- [Autorità di certificazione sicura Cloud](#ca-secure-cloud) 
- [V 7.1 Manager di accesso Cloud identità Dell uno](#dell-one-identity-cloud-access-manager-v71) 
- [AuthAnvil Single Sign-On 4.5](#authavil-single-sign-on-45) 

>[AZURE.IMPORTANT] Poiché si tratta dei prodotti di terze parti, Microsoft non fornisce supporto per la distribuzione, configurazione, risoluzione dei problemi, procedure consigliate, ecc problemi e domande relative a tali provider di identità. Per il supporto e domande relative a tali provider di identità, contattare direttamente il terzi supportati.

>Questi provider di identità di terze parti sono stati testati per l'interoperabilità con i servizi cloud Microsoft utilizzando WS-Federation e i protocolli di protezione WS solo. Test non includere il protocollo SAML.

## <a name="azure-active-directory"></a>Azure Active Directory 
Azure Active Directory possibile autenticare gli utenti per la federazione con Active Directory locale o senza un server federativo locale tramite la sincronizzazione delle password. 

Di seguito è la matrice di supporto di uno scenario per questa esperienza sign-on: 


| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Nessuno|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|
|Applicazioni moderne utilizzando ADAL, ad esempio Office 2016| Supportati|Nessuno|

Per ulteriori informazioni sull'utilizzo di Azure Active Directory con ADFS vedere [Active Directory Federation Services (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

Per ulteriori informazioni sull'utilizzo di Azure Active Directory con la sincronizzazione delle Password vedere [Azure AD Connect](active-directory-aadconnect.md).


## <a name="optimal-idm-virtual-identity-server-federation-services"></a>IDM ottimale identità virtuale Server Federation Services 
Ottimale IDM virtuale identità Server Federation Services possibile autenticare gli utenti che si trovano in Active Directory locale dei clienti.

Di seguito è lo scenario supportano matrice questa esperienza single sign-on:


| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Autenticazione integrata di Windows|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Per ulteriori informazioni sull'accesso client criteri vedere [limitazione dell'accesso a Office 365 servizi in base all'ubicazione del Client.](https://technet.microsoft.com/library/hh526961.aspx)|



## <a name="pingfederate-611"></a>6.11 PingFederate 

6.11 PingFederate implementato lo standard di identità WS Federation diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on:


| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Nessuno (nelle versioni precedenti necessario eseguire l'aggiornamento a 6.11|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per istruzioni PingFederate su come configurare questo servizio token di sicurezza per offrire l'esperienza single sign-on per gli utenti di Active Directory, scaricare il file pdf [qui.](http://go.microsoft.com/fwlink/?LinkID=266321)

## <a name="pingfederate-72"></a>7.2 PingFederate 
7.2 PingFederate implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on:


| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Nessuno|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per istruzioni di PingFederate su come configurare il servizio token di sicurezza offrire l'esperienza single sign-on per gli utenti di Active Directory, vedere [qui.](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## <a name="pingfederate-8x"></a>PingFederate 8 
PingFederate 8 implementa lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on:


| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Nessuno|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per istruzioni di PingFederate su come configurare il servizio token di sicurezza offrire l'esperienza single sign-on per gli utenti di Active Directory, vedere [qui.](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="centrify"></a>Centrify risponde 
Centrify risponde consente di fornire una federati esperienza single sign-on per Office 365 senza la necessità di ospitare un server federativo locale.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on:


| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Nessuno|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Controllo dell'accesso client non è supportato 

Per ulteriori informazioni su Centrify, vedere [qui.](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli federato gestione identità 6.2.2 
IBM Tivoli federato gestione identità 6.2.2 con IBM sicurezza Access Manager per Microsoft applicazioni 1.4 implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on: 

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Nessuno|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per ulteriori informazioni su IBM Tivoli federato identità Manager, vedere [IBM sicurezza Access Manager per Microsoft Applications.](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0 
SecureAuth IdP 7.2.0 implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un'esperienza single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on: 

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Nessuno|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per ulteriori informazioni su SecureAuth, vedere [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>Autorità di certificazione SiteMinder 12.52 SP1 cumulativo 4
Autorità di certificazione SiteMinder federazione 12.52 implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on: 

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Nessuno|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per ulteriori informazioni sulle autorità di certificazione SiteMinder, vedere [CA SiteMinder federazione.](http://www.ca.com/us/products/ca-single-sign-on.html) 

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0 
RadiantOne Cloud federazione servizio (CFS) 3.0 implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on: 

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Autenticazione integrata di Windows|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per ulteriori informazioni su RadiantOne CFS, vedere [RadiantOne CFS.](http://www.radiantlogic.com/products/radiantone-cfs/)


## <a name="okta"></a>Okta 
Okta implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on: 


| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |L'autenticazione di Windows richiede l'installazione e configurazione di server aggiuntivi relativi al web e applicazione Okta.|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Autenticazione integrata di Windows|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per ulteriori informazioni su Okta, vedere [Okta.](https://www.okta.com/)
 
## <a name="onelogin"></a>OneLogin 
OneLogin in base alla verifica in maggio 2014 implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on: 

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Autenticazione integrata di Windows|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Autenticazione integrata di Windows|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per ulteriori informazioni su OneLogin, vedere [OneLogin.](https://www.onelogin.com/)

## <a name="netiq-access-manager-401"></a>Manager di accesso NetIQ 4.0.1 
Manager di accesso NetIQ 4.0.1 implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on:

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |* Kerberos contratti supportati|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Non è supportato l'autenticazione di Windows|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

* NetIQ supportare l'autenticazione Kerberos mediante la configurazione di un contratto Kerberos.  Per assistenza con questa configurazione, contattare NetIQ o visualizzare la Guida all'installazione. Per ulteriori informazioni sulla gestione di accesso di NetIQ, vedere [Manager di accesso NetIQ.](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>GRANDE IP con versione IP di grande gestione dei criteri di accesso 11.3 x-11.6 
Indirizzi IP grande con Access criteri di gestione (APM) versione grande IP 11.3 x-x 11.6 implementato lo standard di identità SAML diffuso per fornire un'esperienza single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on: 

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Non è supportata |Non è supportata|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per ulteriori informazioni sulla gestione dei criteri di accesso grande IP, vedere [gestione dei criteri di accesso IP grande.](https://f5.com/products/modules/access-policy-manager) 

Per le istruzioni di gestione dei criteri di accesso IP grande su come configurare questo servizio token di sicurezza per offrire l'esperienza single sign-on per gli utenti di Active Directory, scaricare il file pdf [qui.](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## <a name="vmware--workspace-portal-version-21"></a>Portale di area di lavoro VMware versione 2.1 
Portale di area di lavoro VMware versione 2.1 implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on:

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Non è supportato l'autenticazione di Windows|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM | Supportati |Non è supportato l'autenticazione di Windows|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per ulteriori informazioni sul portale dell'area di lavoro VMware versione 2.1, scaricare il file pdf [qui.](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)

## <a name="signgo-53"></a>Accedere e passare 5.3 
Eseguire l'accesso & Vai standard per fornire un servizio single sign-on e il framework degli attributi exchange l'identità WS federazione/WS-Trust diffuso 5.3 implements.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on:

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Contratti Kerberos supportati |
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM | Supportati |Nessuno|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|


Accesso & Vai 5.3 supporta l'autenticazione Kerberos mediante la configurazione di un contratto Kerberos.  Per assistenza con questa configurazione, contattare Ilex o visualizzare la Guida all'installazione [qui.](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)


## <a name="icewall-federation-version-30"></a>IceWall federazione versione 3.0 
IceWall federazione versione 3.0 implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on:

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Non è supportato l'autenticazione di Windows|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM | Supportati |Non è supportato l'autenticazione di Windows|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per ulteriori informazioni sulla federazione IceWall, vedere [di seguito](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) e [qui.](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)

## <a name="ca-secure-cloud"></a>Autorità di certificazione sicura Cloud 

Autorità di certificazione sicura Cloud implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on:

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Non è supportato l'autenticazione di Windows|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM | Supportati |Non è supportato l'autenticazione di Windows|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per ulteriori informazioni sulle autorità di certificazione sicura Cloud, vedere [Cloud sicura CA.](http://www.ca.com/us/products/security-as-a-service.aspx)

## <a name="dell-one-identity-cloud-access-manager-v71"></a>V 7.1 Manager di accesso Cloud identità Dell uno 
Gestione accesso Cloud Dell un'identità implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on:

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Nessuno|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM |  Supportati |Nessuno|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|

Per ulteriori informazioni sulle Dell un'identità Cloud Manager di accesso, vedere [Dell un'identità Cloud Access Manager.](http://software.dell.com/products/cloud-access-manager)

 Per istruzioni su come configurare il servizio token di sicurezza per offrire l'esperienza single sign-on per gli utenti di Office 365, vedere [configurare gli utenti di Office 365.](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) 

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil Single Sign-On 4.5 
AuthAnvil Single Sign in 4.5 implementato lo standard di identità WS federazione/WS-Trust diffuso per fornire un servizio single sign-on e il framework degli attributi exchange.

Di seguito è la matrice di supporto di uno scenario per questa esperienza single sign-on:

| Client |Supporto tecnico  |Eccezioni|
| --------- | --------- |--------- |
| Client basati sul Web, ad esempio Web Access di Exchange e SharePoint Online | Supportati |Non è supportato l'autenticazione di Windows|
| Applicazioni rich client, ad esempio Lync, abbonamento a Office, CRM | Supportati |Non è supportato l'autenticazione di Windows|
| Messaggio di posta elettronica rich client, ad esempio Outlook e ActiveSync |  Supportati |Nessuno|


Per ulteriori informazioni, vedere [AuthAnvil Single Sign-On.](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)
