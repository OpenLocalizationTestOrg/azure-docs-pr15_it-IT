<properties
pageTitle="Applicazione di Azure Active Directory e oggetti principale del servizio | Microsoft Azure"
description="Una discussione della relazione tra applicazione e oggetti principale del servizio di Azure Active Directory"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>

# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Applicazione e servizio dell'entità di Azure Active Directory
Quando si leggono su un Azure Active Directory (AD) "applicazione", non è sempre Cancella esattamente quali viene fatto riferimento dell'autore. L'obiettivo di questo articolo è per renderla più chiara, definendo gli aspetti concettuali e concreti di integrazione di applicazioni Azure Active Directory, con un esempio di registrazione e consenso per un' [applicazione multi-tenant](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Panoramica
Un'applicazione di Azure Active Directory è più ampia rispetto a solo una porzione del software. Si tratta di un termine concettuale che fa riferimento non solo all'applicazione, ma anche la registrazione (nello specifico: configurazione di identità) con Azure Active Directory, che consente di partecipare alle autenticazione e l'autorizzazione "conversazioni" in fase di esecuzione. Per definizione, un'applicazione può funzionare in un ruolo di [client](active-directory-dev-glossary.md#client-application) (utilizzo di una risorsa), un ruolo del [server di risorse](active-directory-dev-glossary.md#resource-server) (esposizione API client) o persino entrambe. Il protocollo conversazione è definito da un [flusso di concedere l'autorizzazione di OAuth 2.0](active-directory-dev-glossary.md#authorization-grant), con l'obiettivo di consentire la risorsa/client access/proteggere i dati di una risorsa rispettivamente. Ora passiamo un livello più approfondito e vedere come il modello di applicazione di Azure Active Directory rappresenta un'applicazione internamente. 

## <a name="application-registration"></a>Registrazione dell'applicazione
Quando si registra un'applicazione nel [portale classica Azure][AZURE-Classic-Portal], vengono creati due oggetti nel tenant di Azure Active Directory: oggetto applicazione e un oggetto principale del servizio.

#### <a name="application-object"></a>Oggetto Application
Un'applicazione di Azure Active Directory è *definito* dalla relativa uno e solo oggetto applicazione che si trova nel tenant di Azure Active Directory in cui l'applicazione è stata registrata, definito tenant "casa" dell'applicazione. L'oggetto applicazione vengono fornite informazioni correlate identità per un'applicazione e il modello dal quale relativi oggetti dell'entità servizio corrispondente sono *derivate* per l'utilizzo in fase di esecuzione. 

È possibile pensare dell'applicazione come rappresentazione *globale* dell'applicazione (da usare in tutti i tenant) e il capitale servizio come la rappresentazione *locale* (per l'utilizzo in un tenant specifico). L' grafico di Azure Active Directory [entità applicazione] [ AAD-Graph-App-Entity] definisce lo schema per l'oggetto applicazione. Oggetto applicazione ha pertanto una relazione 1:1 con l'applicazione software e 1:*n* relazione con il relativo oggetti principale di servizio *n* corrispondente.

#### <a name="service-principal-object"></a>Oggetto principale del servizio
L'oggetto dell'entità servizio definisce i criteri e le autorizzazioni per un'applicazione, fornendo la base per un'identità di protezione rappresentare l'applicazione quando accedono alle risorse in fase di esecuzione. L' grafico di Azure Active Directory [entità ServicePrincipal] [ AAD-Graph-Sp-Entity] definisce lo schema per l'oggetto dell'entità servizio. 

Un oggetto principale del servizio è necessario in ogni tenant per cui deve essere rappresentata un'istanza di utilizzo dell'applicazione, per consentire l'accesso sicuro alle risorse di proprietà per gli account utente del tenant. Un'applicazione singola tenant avrà un solo principale del servizio (nella relativa tenant home). Un multi-tenant [applicazione Web](active-directory-dev-glossary.md#web-client) avrà anche un'entità servizio in ogni tenant nel punto in cui un amministratore o degli utenti selezionati da tenant hanno consenso, in modo che possa accedere alle risorse. In seguito consenso, l'oggetto dell'entità servizio verrà consultato le richieste di autorizzazione futuri. 

> [AZURE.NOTE] Le modifiche apportate all'oggetto applicazione, vengono applicate anche nell'oggetto di principale servizio iniziale tenant dell'applicazione solo (il tenant in cui è stato registrato). Per le applicazioni multi-tenant, le modifiche apportate all'oggetto applicazione non risulteranno oggetti principale di servizio dei tenant qualsiasi consumer, finché non tenant consumer rimuove l'accesso e concede l'accesso.

## <a name="example"></a>Esempio
Il diagramma seguente illustra la relazione tra oggetto applicazione di un'applicazione e corrispondente service oggetti principali, nel contesto di un'applicazione di esempio multi-tenant chiamato **HR app**. In questo scenario sono disponibili tre tenant di Azure Active Directory: 

- **Adatum** - tenant utilizzato da società sviluppato **HR app**
- **Contoso** - tenant utilizzato dall'organizzazione Contoso, ossia consumer dell' **app di risorse Umane**
- **Fabrikam** - tenant utilizzato dall'organizzazione Fabrikam, che utilizza anche l' **app di risorse Umane**

![Relazione tra un oggetto applicazione e un oggetto principale del servizio](./media/active-directory-application-objects/application-objects-relationship.png)

Nella figura precedente passaggio 1 è il processo di creazione dell'applicazione e oggetti dell'entità servizio nel tenant iniziale dell'applicazione.

Nel passaggio 2, gli amministratori di Contoso e Fabrikam completato consenso, un oggetto principale viene creato nel tenant di Azure Active Directory della propria azienda e assegnato le autorizzazioni che l'amministratore abbia concesso o meno. Si noti inoltre che l'app HR potrebbe essere configurato/progettata per consentire il consenso dagli utenti per uso personale.

Nel passaggio 3 tenant consumer HR dell'applicazione di (Contoso e Fabrikam) ogni avere proprio oggetto principale del servizio. Ogni rappresenta l'utilizzo di un'istanza dell'applicazione in fase di esecuzione disciplinata dalle autorizzazioni acconsentito dall'amministratore rispettivo.

## <a name="next-steps"></a>Passaggi successivi
Applicazione oggetto di un'applicazione sono accessibili tramite l'API di Azure Active Directory grafico, come rappresentato dalla relativa OData [entità applicazione][AAD-Graph-App-Entity]

Oggetto dell'entità di un'applicazione del servizio sono accessibili tramite l'API di Azure Active Directory grafico, come rappresentato dalla relativa OData [entità ServicePrincipal][AAD-Graph-Sp-Entity]



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com