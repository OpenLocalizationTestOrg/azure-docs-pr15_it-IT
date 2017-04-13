<properties
    pageTitle="Azure AD Connect: Porte | Microsoft Azure"
    description="Questa pagina è una pagina di documentazione tecnica per le porte che devono essere aperto per Azure AD Connect"
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
    ms.date="08/25/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-required-ports-and-protocols"></a>Identità ibrido obbligatorio porte e protocolli
Il documento seguente è un riferimento tecnico per fornire informazioni sul necessari porte e protocolli necessari per l'implementazione di una soluzione di identità ibrida. Utilizzare la figura seguente e fare riferimento alla tabella corrispondente.

![Che cos'è Azure AD Connect](./media/active-directory-aadconnect-ports/required1.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabella 1 - Azure AD connettersi e Active Directory locale
In questa tabella descrive le porte e protocolli necessari per le comunicazioni tra server Azure AD Connect e Active Directory locale.

Protocollo | Porte | Descrizione
--------- | --------- |---------
DNS|53 (TCP/UDP)| Ricerche DNS in strutture di destinazione.
Kerberos|88 (TCP/UDP)| Autenticazione Kerberos per l'insieme di strutture di Active Directory.
MS-RPC |135 (TCP/UDP)| Utilizzato durante la configurazione iniziale della procedura guidata Azure AD Connect durante l'associazione per l'insieme di strutture di Active Directory.
LDAP|389 (TCP/UDP)| Usato per l'importazione di dati da Active Directory. Dati vengono crittografati con accesso Kerberos e bollo.
LDAP/SSL|636 (TCP/UDP)| Usato per l'importazione di dati da Active Directory. Il trasferimento di dati è stato effettuato l'accesso e crittografato. Utilizzato solo se si usa SSL.
RPC |49152 - 65535 (casuale elevato RPC Port)(TCP/UDP)| Usato per la configurazione iniziale di Azure AD Connect durante l'associazione per gli insiemi di strutture di Active Directory. Per ulteriori informazioni, vedere [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)e [KB224196](https://support.microsoft.com/kb/224196) .

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabella 2 - Azure AD connettersi e Azure Active Directory
La tabella seguente descrive le porte e protocolli necessari per le comunicazioni tra il server Azure AD Connect Azure Active Directory.

Protocollo |Porte |Descrizione
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Consente di scaricare CRL (degli elenchi di revoche) per verificare i certificati SSL.
HTTPS|443(TCP/UDP)| Usato per la sincronizzazione con Azure Active Directory.

Per un elenco di URL e indirizzi è necessario aprire nel firewall, vedere [Office 365 URL e intervalli di indirizzi IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-federation-serverswap"></a>Tabella 3 - Azure AD connettersi e server federativo/WAP
La tabella seguente descrive le porte e protocolli necessari per le comunicazioni tra il server di Azure AD Connect e server federativo/WAP.  

Protocollo |Porte |Descrizione
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Consente di scaricare CRL (degli elenchi di revoche) per verificare i certificati SSL.
HTTPS|443(TCP/UDP)| Usato per la sincronizzazione con Azure Active Directory.
WinRM|5985| Comunicare ascoltatore WinRM

## <a name="table-4---wap-and-federation-servers"></a>Tabella 4 - WAP e server federativi
La tabella seguente descrive le porte e protocolli necessari per le comunicazioni tra i server federativi e i server WAP.

Protocollo |Porte |Descrizione
--------- | --------- |---------
HTTPS|443(TCP/UDP)| Usato per l'autenticazione.

## <a name="table-5---wap-and-users"></a>Tabella 5 - WAP e i relativi utenti
La tabella seguente descrive le porte e protocolli necessari per le comunicazioni tra gli utenti e i server WAP.

Protocollo |Porte |Descrizione
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Usato per l'autenticazione del dispositivo.
TCP|49443 (TCP)| Usato per l'autenticazione del certificato.

## <a name="table-6a--6b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabella 6a e 6b - agente di Azure Active Directory connettersi integrità per (AD FS/sincronizzazione) e Azure Active Directory
Le tabelle seguenti descrivono i punti finali, porte e protocolli necessari per le comunicazioni tra Azure Active Directory connettersi agente e Azure Active Directory

### <a name="table-6a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabella 6a - porte e protocolli per agente di Azure Active Directory connettersi integrità per (AD FS/sincronizzazione) e Azure Active Directory
La tabella seguente descrive le seguenti porte in uscita e i protocolli necessari per le comunicazioni tra gli agenti di Azure Active Directory connettersi integrità e Azure Active Directory.  

Protocollo |Porte  |Descrizione
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| In uscita
Bus di servizio Azure|5671 (TCP/UDP)| In uscita

### <a name="6b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>6B - endpoint per agente di Azure Active Directory connettersi integrità per (AD FS/sincronizzazione) e Azure Active Directory
Per un elenco di endpoint, vedere [la sezione requisiti per l'agente di Azure Active Directory connettersi integrità](active-directory-aadconnect-health-agent-install.md#requirements).
