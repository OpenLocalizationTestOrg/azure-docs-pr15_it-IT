<properties
    pageTitle="Accedere a chiave archivio firewall | Microsoft Azure"
    description="Informazioni su come accedere archivio chiave da un'applicazione di un firewall"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="accessing-key-vault-behind-firewall"></a>Accesso all'archivio di chiave firewall
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-portshostsip-addresses-should-i-open-to-enable-access-to-key-vault"></a>Q: archivio chiave client applicazione deve essere un firewall, gli indirizzi di porte/host/IP è utile aperto per consentire l'accesso all'archivio chiave?

Per accedere a un archivio di tasto che applicazione client archivio chiave deve essere in grado di accedere più punti finali per varie funzionalità.

- Autenticazione (tramite Azure Active Directory)
- Gestione dell'archivio di chiave (che include l'impostazione di criteri di accesso e anche creare/lettura/aggiornamento/eliminazione) tramite Gestione risorse di Azure
- Accesso e la gestione di oggetti (chiavi e informazioni riservate) archiviati in archivio chiave stesso, passano attraverso il punto finale archivio chiave specifiche (ad esempio [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

A seconda della configurazione e l'ambiente, esistono alcune varianti.   

## <a name="ports"></a>Porte

Tutto il traffico alla chiave archivio per tutte le 3 funzioni (l'autenticazione e gestione dei dati in access piano) vengono illustrate HTTPS: porta 443. Tuttavia per CRL, si verificherà occasionalmente il traffico HTTP (porta 80). Client che supportano OCSP non devono raggiungere CRL, ma in alcuni casi può raggiungere [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Autenticazione

Applicazione client chiave archivio sarà necessario accedere endpoint di Azure Active Directory per l'autenticazione. Endpoint utilizzato dipende la configurazione di tenant AAD e il tipo di capitale - dell'entità utente, principale del servizio e il tipo di account, ad esempio Account Microsoft o l'ID dell'organizzazione.  

| Tipo di capitale | Punto finale: porta |
|----------------|---------------|
| Utente che usa l'Account Microsoft<br> (ad esempiouser@hotmail.com) | **Globale:**<br> Login.microsoftonline.com:443<br><br> **Azure Cina:**<br> Login.chinacloudapi.CN:443<br><br>**Azure governo degli Stati Uniti:**<br> accesso us.microsoftonline.com:443<br><br>**Azure Germania:**<br> Login.microsoftonline.de:443<br><br> e <br>Login.Live.com:443   |
| Dell'entità utente/servizio con ID Org AAD (ad esempiouser@contoso.com) | **Globale:**<br> Login.microsoftonline.com:443<br><br> **Azure Cina:**<br> Login.chinacloudapi.CN:443<br><br>**Azure governo degli Stati Uniti:**<br> accesso us.microsoftonline.com:443<br><br>**Azure Germania:**<br> Login.microsoftonline.de:443 |
| Dell'entità utente/servizio utilizzando (ad esempio organigramma ID + ADFS o altro endpoint federatouser@contoso.com) | Tutti gli endpoint sopra per ID Org più ADFS o altri endpoint federati |

Sono disponibili altri scenari possibili complesse. Per ulteriori informazioni, vedere [Flusso autenticazione Azure Active Directory](/documentation/articles/active-directory-authentication-scenarios/), [Le applicazioni di integrazione con Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) e [Protocolli di autenticazione Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx) .  

## <a name="key-vault-management"></a>Gestione Key archivio

Per la gestione di archivio di chiave (CRUD e impostazione di criteri di accesso), l'applicazione client archivio chiave deve accedere endpoint Manager delle risorse di Azure.  

| Tipo di operazione | Punto finale: porta |
|----------------|---------------|
| Operazioni chiave archivio controllo piano<br> tramite Gestione risorse Azure | **Globale:**<br> Management.Azure.com:443<br><br> **Azure Cina:**<br> Management.chinacloudapi.CN:443<br><br> **Azure governo degli Stati Uniti:**<br> Management.usgovcloudapi.NET:443<br><br> **Azure Germania:**<br> Management.microsoftazure.de:443 |
| Grafico di Azure Active Directory API | **Globale:**<br> Graph.Windows.NET:443<br><br> **Azure Cina:**<br> Graph.chinacloudapi.CN:443<br><br> **Azure governo degli Stati Uniti:**<br> Graph.Windows.NET:443<br><br> **Azure Germania:**<br> Graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Gestione Key dell'archivio

Per la gestione degli oggetti (chiavi e informazioni riservate) archivio chiave tutti e operazioni di crittografia, client archivio chiave deve accedere il punto finale chiave archivio. A seconda della posizione dell'archivio chiave, il suffisso DNS endpoint è diverso. Il punto finale di archivio di chiave è del formato: < nome archivio >. < area geografica--suffisso dns specifico-> come descritto nella tabella seguente.  

| Tipo di operazione | Punto finale: porta |
|----------------|---------------|
| Gestione dell'archivio chiave di crittografiche operazioni sulle chiavi chiavi creata/lettura/aggiornamento/eliminazione e informazioni riservate, analoghe set/get tag e altri attributi sugli oggetti archivio chiave (tasti/informazioni riservate)     | **Globale:**<br> &lt;Nome archivio&gt;. vault.azure.net:443<br><br> **Azure Cina:**<br> &lt;Nome archivio&gt;. vault.azure.cn:443<br><br> **Azure governo degli Stati Uniti:**<br> &lt;Nome archivio&gt;. vault.usgovcloudapi.net:443<br><br> **Azure Germania:**<br> &lt;Nome archivio&gt;. vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Intervalli di indirizzi IP

Servizio di archivio di chiave utilizzata a sua volta altre risorse Azure ad esempio infrastruttura PaaS, non è quindi possibile fornire un specifico intervallo di indirizzi IP contenenti gli endpoint del servizio di archivio chiave in qualsiasi momento. Tuttavia se il firewall supporta solo l'indirizzo IP intervalli quindi, consultare il documento di [Intervalli di indirizzi IP di Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653) .   Per l'autenticazione e identità (Azure Active Directory), l'applicazione deve essere in grado di connettersi agli endpoint descritto in [autenticazione e gli indirizzi di identità](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="next-steps"></a>Passaggi successivi

- Nel caso di domande su chiave archivio, visitare i [Forum di archivio di Azure chiave](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
