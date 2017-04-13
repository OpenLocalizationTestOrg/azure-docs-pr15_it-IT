<properties
  pageTitle="Controllo delle versioni SDK client e server nell'App Mobile e servizi mobili | Servizio App Azure"
  description="Elenco dei client SDK e compatibilità con le versioni SDK server per servizi mobili e App Mobile Azure"
  services="app-service\mobile"
  documentationCenter=""
  authors="adrianhall"
  manager="erikre"
  editor=""/>

<tags
  ms.service="app-service-mobile"
  ms.workload="mobile"
  ms.tgt_pltfrm="mobile-multiple"
  ms.devlang="dotnet"
  ms.topic="article"
  ms.date="10/01/2016"
  ms.author="adrianha"/>

# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Controllo delle versioni client e server nell'App Mobile e servizi mobili

L'ultima versione di servizi di Windows Azure Mobile è la funzionalità di **App Mobile** del servizio di App Azure.

SDK di client e server App Mobile originariamente si basano su quelle in servizi mobili, ma sono *non* compatibile con tra loro.
Ovvero, è necessario utilizzare un client *Mobile App* SDK con un server di *App Mobile* SDK e allo stesso modo per *I servizi di dispositivi mobili*. Il presente contratto viene applicato tramite un valore di intestazione speciale utilizzato da client e server SDK, `ZUMO-API-VERSION`.

Nota: ogni volta che il documento si riferisce a un back-end *Servizi mobili* , essa non necessariamente essere ospitati in servizi mobili. È ora possibile eseguire la migrazione di un servizio per dispositivi mobili per l'esecuzione del servizio di App senza apportare modifiche al codice, ma il servizio da continuerà a utilizzare *Servizi mobili* SDK versioni.

Per ulteriori informazioni sulla migrazione al servizio App senza apportare modifiche al codice, vedere l'articolo [eseguire la migrazione di un servizio di dispositivi mobili al servizio App Azure].

## <a name="header-specification"></a>Specifica di intestazione

Il tasto `ZUMO-API-VERSION` è possibile specificare l'intestazione HTTP o la stringa di query. Il valore è una stringa di versione il modulo **x.y.z**.

Per esempio:

OTTENERE https://service.azurewebsites.net/tables/TodoItem

INTESTAZIONI: VERSIONE API ZUMO: 2.0.0

REGISTRARE https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Come rifiutare esplicitamente il controllo della versione

È possibile rifiutare esplicitamente versione controllo impostando il valore **vero** per l'app impostazione **MS_SkipVersionCheck**. Specificare questo file Web. config o nella sezione Impostazioni applicazione del portale di Azure.

> [AZURE.NOTE] Sono disponibili numerose modifiche di comportamento tra servizi mobili e App Mobile, in particolare nelle aree della sincronizzazione offline, l'autenticazione e le notifiche push. Si consiglia di scegliere solo dalla versione controllo dopo aver provato completo per verificare che le modifiche di comportamento integrità funzionalità dell'applicazione.

## <a name="summary-of-compatibility-for-all-versions"></a>Riepilogo delle compatibilità per tutte le versioni

Il grafico mostra la compatibilità tra tutti i tipi di client e server. Un back-end classificato come mobili **servizi** o **App** per dispositivi mobili basata su server SDK che utilizza.

|                           | **Servizi di telefonia mobile** Node o .NET | **App per dispositivi mobili** Node o .NET |
| ----------                | -----------------------             |   ----------------              |
| [Client di servizi di dispositivi mobili] | Ok                                  | Errore\*                         |
| [Client App mobile]     | Errore\*                             | Ok                              |

\*Può essere controllato specificando **MS_SkipVersionCheck**.


<!-- IMPORTANT!  The anchors for Mobile Services and Mobile Apps MUST be 1.0.0 and 2.0.0 respectively, since there is an exception error message that uses those anchors. -->

<!-- NOTE: the fwlink to this document is http://go.microsoft.com/fwlink/?LinkID=690568 -->

## <a name="1.0.0"></a>Server e client di servizi mobili

Il client SDK nella tabella seguente sono compatibili con **I servizi di dispositivi mobili**.

Nota: il client di servizi mobili SDK *non* inviare un valore di intestazione `ZUMO-API-VERSION`. Se il servizio riceve l'intestazione o un valore stringa di query, verrà restituito un errore, a meno che non si è scelto in modo esplicito indietro come descritto in precedenza.

### <a name="MobileServicesClients"></a>Client di *servizi* Mobile SDK

| Piattaforma client                   | Versione                                                                   | Valore dell'intestazione di versione |
| -------------------               | ------------------------                                                  | -------------------  |
| Client gestito (Windows, Xamarin) | [1.3.2](https://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.2) | n/d                  |
| iOS                               | [2.2.2](http://aka.ms/gc6fex)                                             | n/d                  |
| Android                           | [2.0.3](https://go.microsoft.com/fwLink/?LinkID=280126)                   | n/d                  |
| HTML                              | [1.2.7](http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js) | n/d     |

### <a name="mobile-services-server-sdks"></a>Server di *servizi* Mobile SDK

| Piattaforma server  | Versione                                                                                                        | Intestazione di versione accettato |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [WindowsAzure.MobileServices.Backend.* versione 1.0. x](https://www.nuget.org/packages/WindowsAzure.MobileServices.Backend/) | **Senza intestazione versione** |
| Node          | (disponibile a breve)                        | **Senza intestazione versione** |

<!-- TODO: add Node npm version -->

### <a name="behavior-of-mobile-services-backends"></a>Comportamento di back-end servizi mobili

| VERSIONE DI API ZUMO | Valore di MS_SkipVersionCheck | Risposta |
| ---------------- | ---------------------------- | -------- |
| Non viene specificato    | Qualsiasi                          | 200 - OK |
| Qualsiasi valore        | Vero                         | 200 - OK |
| Qualsiasi valore        | FALSO/non specificato          | 400 - richiesta non valida |

## <a name="2.0.0"></a>Azure App Mobile client e il server

### <a name="MobileAppsClients"></a>Client mobili *App* SDK

Il controllo della versione è stata introdotta inizia con le versioni seguenti di client SDK per **App Mobile Azure**:

| Piattaforma client                   | Versione                   | Valore dell'intestazione di versione |
| -------------------               | ------------------------  | -----------------    |
| Client gestito (Windows, Xamarin) | [2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) | 2.0.0 |
| iOS                               | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=529823) | 2.0.0  |
| Android                           | [3.0.0](http://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) | 3.0.0 |

<!-- TODO: add HTML version when released -->

### <a name="mobile-apps-server-sdks"></a>Server di *App* per dispositivi mobili SDK

Il controllo della versione è incluso nella seguente versioni SDK server:

| Piattaforma server  | SDK                                                                                                        | Intestazione di versione accettato |
| ---------------- | ------------------------------------------------------------                                                   | ----------------------- |
| .NET             | [Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) | 2.0.0 |
| Node          | [app di mobile Azure)](https://www.npmjs.com/package/azure-mobile-apps)                         | 2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamento di back-end App Mobile

| VERSIONE DI API ZUMO | Valore di MS_SkipVersionCheck | Risposta |
| ---------------- | ---------------------------- | -------- |
| x.y.z o Null    | Vero                         | 200 - OK |
| Null             | FALSO/non specificato          | 400 - richiesta non valida |
| 1.x.y            | FALSO/non specificato          | 400 - richiesta non valida |
| 2.0.0-2.x.y      | FALSO/non specificato          | 200 - OK |
| 3.0.0-3.x.y      | FALSO/non specificato          | 400 - richiesta non valida |


## <a name="next-steps"></a>Passaggi successivi

- [Eseguire la migrazione di un servizio per dispositivi mobili al servizio App Azure]


[Client di servizi di dispositivi mobili]: #MobileServicesClients
[Client App mobile]: #MobileAppsClients


[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Eseguire la migrazione di un servizio per dispositivi mobili al servizio App Azure]: app-service-mobile-migrating-from-mobile-services.md

