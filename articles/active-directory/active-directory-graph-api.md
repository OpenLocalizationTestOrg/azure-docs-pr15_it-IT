<properties
   pageTitle="Grafico di Azure Active Directory API | Microsoft Azure"
   description="Una panoramica e Guida introduttiva Guida per l'API di grafico che consente l'accesso a Azure Active Directory tramite i punti finali API REST."
   services="active-directory"
   documentationCenter=""
   authors="PatAltimore"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin" />

# <a name="azure-active-directory-graph-api"></a>Grafico di Azure Active Directory API

> [AZURE.IMPORTANT] Azure Active Directory grafico API funzionalità è disponibile anche tramite [Microsoft Graph](https://graph.microsoft.io/), una API unificata che include API da altri servizi Microsoft, ad esempio Outlook, OneDrive, OneNote, pianificazione e Office Graph, accessibile tramite un unico endpoint e con un token di accesso singolo.

API di grafico Azure Active Directory fornisce l'accesso a Azure Active Directory tramite i punti finali API REST. Applicazioni possono utilizzare l'API di grafico per eseguire creare, leggere, aggiornare ed eliminazione (CRUD) nella directory dati e oggetti. Ad esempio, l'API Graph supporta le seguenti operazioni comuni per un oggetto utente:

- Creare un nuovo utente in una directory

- Ottenere proprietà dettagliata di un utente, ad esempio i gruppi

- Per aggiornare le proprietà dell'utente, ad esempio il percorso e il numero di telefono, o cambiare la propria password

- Controllare l'appartenenza ai gruppi dell'utente per l'accesso basato sui ruoli

- Disattivare un account utente o eliminare completamente

Oltre a oggetti utente, è possibile eseguire operazioni simili su altri oggetti, ad esempio gruppi e le applicazioni. Per chiamare l'API di grafico su una directory, l'applicazione deve essere registrato con Azure Active Directory e configurato per consentire l'accesso alla directory. In genere grazie a un flusso di consenso utente o amministratore.

Per iniziare a utilizzare l'API di grafico Azure Active Directory, vedere la [Guida di Guida introduttiva all'API di grafico](active-directory-graph-api-quickstart.md)o visualizzare la [documentazione di riferimento interattiva API di grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).


## <a name="features"></a>Caratteristiche

API di grafico che fornisce le caratteristiche seguenti:

- **I punti finali API REST**: l'API di grafico è un servizio REST costituito da endpoint che è possibile accedere tramite richieste HTTP standard. L'API Graph supporta i tipi di contenuto XML o Javascript Object Notation (JSON) per le richieste e le risposte. Per ulteriori informazioni, vedere [informazioni di riferimento API REST Azure Active Directory grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **Autenticazione con Azure Active Directory**: tutte le richieste all'API grafico devono essere autenticata aggiungendo un JSON Web Token (JWT) nell'intestazione di autorizzazione della richiesta. Questo token viene acquisito una richiesta all'endpoint token di Azure Active Directory e fornendo credenziali valide. È possibile utilizzare il flusso di credenziali client OAuth 2.0 o il codice di autorizzazione concedere flusso per acquisire un token per chiamare il grafico. Per ulteriori informazioni, [OAuth 2.0 in Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645545.aspx).

- **Autorizzazione basato sui ruoli (RBAC)**: gruppi di sicurezza vengono usati per eseguire RBAC dell'API di grafico. Ad esempio, se si desidera determinare se un utente ha accesso a una risorsa specifica, l'applicazione può chiamare l'operazione di [Controllare l'appartenenza ai gruppi (transitivi)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) , che restituisce true o false.

- **Query differenziale**: se si desidera verificare la presenza di modifiche in una directory fra due periodi di tempo senza dover apportare query frequenti all'API di grafico, è possibile effettuare una richiesta di query differenziale. Questo tipo di richiesta restituirà solo le modifiche apportate tra la richiesta di query differenziale precedente e la richiesta corrente. Per ulteriori informazioni, vedere [Query differenziale API di Azure Active Directory grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).

- **Estensioni directory**: se si sviluppa un'applicazione che è necessario leggere o scrivere le proprietà univoche per gli oggetti directory, è possibile registrare e usare i valori di estensione tramite l'API di grafico. Ad esempio, se l'applicazione richiede una proprietà ID di Skype per ogni utente, è possibile registrare la nuova proprietà nella directory e sarà disponibile su ogni oggetto utente. Per ulteriori informazioni, vedere [estensioni dello Schema di Azure Active Directory grafico API Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

- **Protetta dagli ambiti di autorizzazione**: AAD grafico API espone ambiti di autorizzazione che consentono di proteggere acconsentito accedere ai dati AAD e supportano una varietà di tipi di app client, tra cui:
 - quelle ottenute con un'interfaccia utente che vengono fornite delegato l'accesso ai dati tramite autorizzazione da parte dell'utente connesso (delegato)
  - quelle che utilizzano applicazione: consente di definire controllo dell'accesso basato sui ruoli, ad esempio il client di servizio/daemon (ruoli app)

    Delega sia app ruolo autorizzazione ambiti rappresentano un privilegi esposti dall'API Graph e possono essere richiesti da applicazioni client tramite le autorizzazioni di registrazione dell'applicazione [funzionalità nel portale di classica Azure](https://manage.windowsazure.com). Client possono verificare gli ambiti di autorizzazione concesso loro controllando la richiesta di ambito ("scp") ricevuta nel token di accesso delegato autorizzazioni e richiedere i ruoli ("ruoli") per le autorizzazioni del ruolo di app. Ulteriori informazioni sugli [ambiti di Azure Active Directory grafico API autorizzazione](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).


## <a name="scenarios"></a>Scenari

L'API di grafico consente a molti scenari di applicazioni. Gli scenari seguenti sono le più comuni:

- **Applicazione line of Business (singola Tenant)**: In questo scenario, uno sviluppatore aziendale funziona per un'organizzazione che ha un abbonamento a Office 365. Lo sviluppatore è la creazione di un'applicazione web che interagisce con Azure Active Directory per eseguire attività ad esempio l'assegnazione di una licenza a un utente. Questa operazione richiede l'accesso all'API di grafico, i registri di sviluppo la singola applicazione di Azure Active Directory del tenant e configura leggere e scrivere le autorizzazioni per l'API di grafico. Quindi l'applicazione è configurata per usare le proprie credenziali o quelle dell'utente dell'accesso attualmente per acquisire un token per chiamare l'API di grafico.

- **Software come un'applicazione di servizio (multi-Tenant)**: In questo scenario, un fornitore software indipendente () è lo sviluppo di applicazione web multi-tenant ospitato che offre funzionalità di gestione utenti di altre organizzazioni che usano Azure Active Directory. Queste caratteristiche richiedono l'accesso agli oggetti directory e pertanto l'applicazione deve chiamare l'API di grafico. Lo sviluppatore registra l'applicazione in Azure Active Directory, configurarlo per richiedere leggere e scrivere le autorizzazioni per l'API di grafico e quindi consente l'accesso esterno in modo da altre organizzazioni possono consenso per usare l'applicazione nella directory di. Quando un utente in un'altra organizzazione autentica all'applicazione per la prima volta, vengono visualizzate una finestra di dialogo di consenso dell'utente con autorizzazioni che richiesta dall'applicazione.  Concessione dell'autorizzazione otterranno quindi l'applicazione quelli necessarie le autorizzazioni per l'API di grafico nella directory dell'utente. Per ulteriori informazioni su framework consenso dell'utente, vedere [Panoramica di Framework consenso](active-directory-integrating-applications.md).

## <a name="see-also"></a>Vedere anche

[Guida rapida di Azure Active Directory grafico API](active-directory-graph-api-quickstart.md)

[Documentazione di grafico resto Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Azure Active Directory Guida per gli sviluppatori](active-directory-developers-guide.md)
