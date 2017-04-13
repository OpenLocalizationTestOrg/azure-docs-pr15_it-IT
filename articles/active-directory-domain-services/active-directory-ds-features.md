<properties
    pageTitle="Servizi di dominio Azure Active Directory: Caratteristiche | Microsoft Azure"
    description="Caratteristiche dei servizi di dominio di Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Servizi di dominio Active Directory Azure

## <a name="features"></a>Caratteristiche
Le caratteristiche seguenti sono disponibili in servizi di dominio Active Directory di Azure gestiti domini.

- **Esperienza di distribuzione semplice:** È possibile attivare servizi di dominio di Azure Active Directory per il tenant di Azure Active Directory con pochi clic. Indipendentemente dalla presenza del tenant di Azure Active Directory è un tenant cloud o sincronizzato con la directory locale, dominio gestito da destinare rapidamente.

- **Il supporto per join dominio:** È possibile facilmente computer connessione al dominio della rete virtuale Azure in che dominio gestito è disponibile. Esperienza di partecipazione a dominio su Windows client e Server sistemi operativi funziona perfettamente con domini serviti da servizi di dominio Active Directory Azure. È inoltre possibile utilizzare join dominio automatizzata utensili rispetto a tali domini.

- **Istanza di un dominio per directory di Azure Active Directory:** È possibile creare un unico dominio di Active Directory per ogni directory di Azure Active Directory.

- **Creare domini con nomi personalizzati:** È possibile creare domini con nomi personalizzati (ad esempio, ' contoso100.com') tramite servizi di dominio Active Directory Azure. È possibile usare uno dei due nomi di dominio verificato o non verificati. Facoltativamente, è anche possibile creare un dominio con il suffisso dominio incorporati (vale a dire "*. onmicrosoft.com') offerti dai directory Azure Active Directory.

- **Integrato con Azure Active Directory:** Non è necessario configurare o gestire la replica di servizi di dominio Active Directory Azure. Account utente, appartenenze ai gruppi e le credenziali utente (password) dalla directory di Azure Active Directory vengono automaticamente disponibile in servizi di dominio Active Directory Azure. Nuovi utenti, gruppi o le modifiche degli attributi dal tenant di Azure Active Directory o directory locale vengono sincronizzate automaticamente in servizi di dominio Active Directory Azure.

- **Autenticazione Kerberos e NTLM:** Con il supporto per l'autenticazione Kerberos e NTLM, è possibile distribuire applicazioni basate su autenticazione di Windows.

- **Utilizzare le credenziali/password aziendale:** Le password degli utenti nel tenant di Azure Active Directory lavorare con i servizi di dominio di Azure Active Directory. Gli utenti possono usare le credenziali aziendale per macchine di connessione al dominio, accedere in modo interattivo o tramite desktop remoto e autenticare dominio gestito.

- **Binding LDAP & LDAP leggere supporto:** È possibile utilizzare le applicazioni basate su LDAP associato a autenticazione degli utenti in domini serviti da servizi di dominio Active Directory Azure. Inoltre, le applicazioni che utilizzano le operazioni di lettura LDAP degli attributi utente/computer di query dalla directory anche grado di funzionare con servizi di dominio Active Directory Azure.

- **Sicura LDAP (LDAPS):** È possibile abilitare l'accesso alla directory su LDAP sicuro (LDAPS). L'accesso sicuro LDAP è disponibile all'interno della rete virtuale per impostazione predefinita. Tuttavia, è possibile anche attivare l'accesso sicuro LDAP tramite internet.

- **Criteri di gruppo:** È possibile utilizzare una singola incorporata oggetto Criteri di gruppo ogni per gli utenti e computer contenitori per applicare la conformità con i criteri di protezione necessari per gli account utente e computer di dominio.

- **Gestione DNS:** Membri del gruppo 'AAD controller di dominio Administrators' possono gestire DNS del dominio gestito con strumenti di amministrazione DNS noti come snap-in MMC Amministrazione DNS.

- **Creare personalizzato (organizzative):** Membri del gruppo 'AAD controller di dominio Administrators' possono creare unità organizzative personalizzate nel dominio gestito. Questi utenti sono concesso privilegi amministrativi su unità organizzative personalizzate, in modo che è possibile aggiungere e rimuovere account del servizio, computer, gruppi e così via all'interno di queste unità organizzative personalizzate.

- **Disponibile in più aree geografiche Azure:** Vedere la pagina [servizi Azure dall'area](https://azure.microsoft.com/regions/#services/) fonti di informazioni sulle aree di Azure in cui è disponibili Azure servizi di dominio Active Directory.

- **Disponibilità:** Servizi di dominio Active Directory Azure offre disponibilità per il dominio. Questa caratteristica offre garanzia di maggiore disponibilità di servizio e adattabilità agli errori. Offerte di monitoraggio incorporata automatizzato rimedi da errori, utilizzare le nuove istanze per sostituire le istanze non riuscite e fornire assistenza "Continua" per il dominio.

- **Utilizzare gli strumenti di gestione familiari:** È possibile utilizzare gli strumenti di gestione di Windows Server Active Directory comuni, ad esempio il centro di amministrazione di Active Directory o Active Directory PowerShell per amministrare domini gestiti.
