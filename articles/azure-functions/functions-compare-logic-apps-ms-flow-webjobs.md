<properties
    pageTitle="Scegliere tra flusso, App logica, funzioni e WebJobs | Microsoft Azure"
    description="Analizzare e confrontare le per il cloud integration services da Microsoft e decidere quali servizi si consiglia di utilizzare."
    services="functions,app-service\logic"
    documentationCenter="na"
    authors="cephalin"
    manager="wpickett"
    tags=""
    keywords="Microsoft flusso, flusso, App logica, funzioni di azure, funzioni azure webjobs, webjobs, evento architettura per l'elaborazione, calcolo dinamico, senza server"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="chrande; glenga"/>

# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Scegliere tra flusso, App logica, funzioni e WebJobs

In questo articolo vengono confrontati i seguenti servizi nel cloud Microsoft, che può tutti aiutare a risolvere i problemi di integrazione e l'automazione dei processi aziendali:

- [Flusso di Microsoft](https://flow.microsoft.com/)
- [App logica Azure](https://azure.microsoft.com/services/logic-apps/)
- [Funzioni di Azure](https://azure.microsoft.com/services/functions/)
- [Servizio App Azure WebJobs](../app-service-web/web-sites-create-web-jobs.md)

Tutti questi servizi sono utili quando "incollaggio" insieme sistemi diversi. Possibile tutte definiscono input, azioni, condizioni e l'output. È possibile eseguire ciascuno di essi in una pianificazione o trigger. Tuttavia, ogni servizio aggiunge una serie di valore e il confronto non è una domanda "il servizio è la migliore?" ma uno dei "il servizio è più idoneo per questo problema?" Spesso, una combinazione di questi servizi è il modo migliore per creare rapidamente una soluzione completa, scalable integrazione in primo piano.

<a name="flow"></a>
## <a name="flow-vs-logic-apps"></a>App di flusso e logica

Possiamo esaminati Flow Microsoft e Azure logica Apps insieme perché sono entrambe servizi di integrazione *prima di configurazione* , che semplifica l'individuazione di creare flussi di lavoro e processi e integrare con diverse applicazioni SaaS ed enterprise. 

- Flusso integrato nella parte superiore di logica di App
- Hanno la stessa Progettazione flussi di lavoro
- [Connettori](../connectors/apis-list.md) che funzionano in uno anche possibile collaborare in altro

Flussi di consente agli eventuali impiegato per eseguire le integrazioni semplice (ad esempio get SMS per i messaggi importanti) senza scorrere gli sviluppatori o IT. Mano, logica App è possibile abilitare avanzate o critici le integrazioni (ad esempio B2B processi) in cui sono necessari consigliate attrezzi e la sicurezza a livello aziendale. Si tratta di una tipica per un flusso di lavoro di business rimpicciolire straordinario complessità. Di conseguenza, è possibile iniziare con un flusso prima, quindi convertirla in un'app logica in base alle esigenze.

Nella tabella seguente consente di determinare se è adatto per una determinato integrazione flusso o App logica.

|               | Flusso                                                                             | App logica                                                                                          |
|---------------|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Gruppo di destinatari      | utenti di Office, gli utenti aziendali                                                   | Professionisti IT, gli sviluppatori                                                                                 |
| Scenari     | Self-Service                                                                     | Critici                                                                                    |
| Strumento di progettazione   | Nel browser solo interfaccia utente                                                              | Nel browser e [Visual Studio](../app-service/logic/app-service-logic-deploy-from-vs.md), [alla visualizzazione codice](../app-service-logic/app-service-logic-author-definitions.md) disponibili |
| Attrezzi        | Reporting ad-hoc, sviluppare nell'ambiente di produzione                                                    | controllo del codice sorgente, test, supporto e automazione e la gestibilità in [Gestione risorse di Azure](../app-service-logic/app-service-logic-arm-provision.md)|
| Esperienza di amministrazione| [https://Flow.microsoft.com](https://flow.microsoft.com)                       | [https://Portal.Azure.com](https://portal.azure.com)                                                |
| Sicurezza      | Le procedure standard: [sovranità dei dati](https://wikipedia.org/wiki/Technological_Sovereignty), [la crittografia inattivi](https://wikipedia.org/wiki/Data_at_rest#Encryption) per dati riservati e così via. | Il controllo della protezione di Azure: [Sicurezza di Windows Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Centro protezione](https://azure.microsoft.com/services/security-center/), [registri di controllo](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)e altro ancora. |

<a name="function"></a>
## <a name="functions-vs-webjobs"></a>Funzioni e WebJobs

È possibile discutere funzioni Azure e Azure App servizio WebJobs insieme perché sono entrambi i servizi di integrazione *prima di codice* e progettato per gli sviluppatori. Consentono di eseguire uno script o una parte di codice in risposta a vari eventi, ad esempio [Nuovo spazio di archiviazione BLOB](functions-bindings-storage.md) o [una WebHook richiesta](functions-bindings-http-webhook.md). Ecco le analogie: 

- Entrambi si basano su [Azure App servizio](../app-service/app-service-value-prop-what-is.md) e usufruire delle caratteristiche, ad esempio [il codice sorgente](../app-service-web/app-service-continuous-deployment.md), [l'autenticazione](../app-service/app-service-authentication-overview.md)e [monitoraggio](../app-service-web/web-sites-monitor.md).
- Si verificano entrambe servizi incentrati sui sviluppo.
- Entrambi gli script standard di supporto e linguaggi di programmazione.
- Hanno NuGet e NPM supporto tecnico.

Funzioni è alla naturale evoluzione WebJobs in quanto accetta principali vantaggi della WebJobs e li migliora. I miglioramenti includono: 

- Dev semplificata, testare ed esecuzione di codice direttamente nel browser.
- Integrazione con servizi più Azure e altri servizi di terze parti 3o come [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
- Pagare per uso, senza la necessità di pagamento per un [Piano di servizio di App](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
- Automatico, [adattamento dinamico](functions-scale.md).
- Per i clienti esistenti del servizio di App, in esecuzione nel piano di servizio App comunque (in modo da sfruttare le risorse utilizzate sotto).
- Integrazione con applicazioni logica.

Nella tabella seguente vengono riepilogate le differenze tra funzioni e WebJobs:

|                        | Funzioni                                                                                                                                                                | WebJobs                            |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| Proporzioni dei caratteri                | Ridimensionamento configurationless                                                                                                                                                | scala con piano di servizio di App        |
| Prezzi                | Pagare costo per uso o per parte di piano di servizio di App                                                                                                                                  | Parte del piano di servizio di App           |
| Tipo di esecuzione               | attivato, pianificato (da trigger timer)                                                                                                                                  | attivate, continua, pianificata   |
| Attivare gli eventi         | [timer](functions-bindings-timer.md), [DocumentDB Azure](functions-bindings-documentdb.md), [Azure evento hub](functions-bindings-event-hubs), [HTTP/WebHook (GitHub, il margine di flessibilità)](functions-bindings-http-webhook.md), [App Mobile servizio App di Azure](functions-bindings-mobile-apps.md), [Hub di notifica di Azure](functions-bindings-notification-hubs.md), [Bus di servizio Azure](functions-bindings-service-bus.md), [Lo spazio di archiviazione di Azure](articles/functions-bindings-storage.md) | [Spazio di archiviazione azure](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [Bus di servizio Azure](websites-dotnet-webjobs-sdk-service-bus.md)         |
| Sviluppo nel browser | x                                                                                                                                                                        |                                    |
| Finestra script       | sperimentazione                                                                                                                                                             | x                                  |
| PowerShell             | sperimentazione                                                                                                                                                             | x                                  |
| C#                     | x                                                                                                                                                                        | x                                  |
| F#                     | x                                                                                                                                                                        |                                    |
| Bash                   | sperimentazione                                                                                                                                                             | x                                  |
| PHP                    | sperimentazione                                                                                                                                                             | x                                  |
| Python                 | sperimentazione                                                                                                                                                             | x                                  |
| JavaScript             | x                                                                                                                                                                        | x                                  |
| Java                   | sperimentazione                                                                                                                                                             | x                                  |

Se si desidera utilizzare le funzioni o WebJobs dipende che si sta già eseguendo con il servizio di App. Se si dispone di un'applicazione di servizio di App per il quale si desidera eseguire frammenti di codice e si desidera gestire insieme nello stesso ambiente attrezzi, è necessario utilizzare WebJobs. Se si desidera eseguire frammenti di codice per altri servizi Azure o persino 3o terze App o se si desidera gestire i frammenti di codice integrazione separatamente dalle applicazioni servizio App o se si desidera chiamare i frammenti di codice da un'app per logica, è opportuno avvalersi di tutti i miglioramenti nelle funzioni.  

<a name="together"></a>
## <a name="flow-logic-apps-and-functions-together"></a>Concatenate, App logica e funzioni

Come indicato in precedenza, il servizio è ideale per l'utente varia in base alla situazione specifica. 

- Per l'ottimizzazione business semplice, quindi utilizzare flusso.
- Se lo scenario di integrazione è troppo avanzato per il flusso di oppure è necessario disporre attrezzi funzionalità e compliances di sicurezza, utilizzare App logica.
- Se un passaggio nel proprio scenario di integrazione richiede trasformazione altamente personalizzato o codice specifico, quindi scrivere un'app di funzione e quindi attivare una funzione come un'azione in un'applicazione di logica.

È possibile chiamare un'app logica in un flusso. È anche possibile chiamare una funzione in un'app di logica e un'app logica in una funzione. L'integrazione tra flusso, App logica e funzioni continua a migliorare straordinario. È possibile creare un elemento in un servizio e usarla in altri servizi. Pertanto, qualsiasi investimento effettuati in questi tre tecnologie è utile.

## <a name="next-steps"></a>Passaggi successivi

Guida introduttiva a ognuno dei servizi creando il primo flusso, logica app, funzione app o WebJob. Fare clic su uno dei seguenti collegamenti:

- [Introduzione a Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
- [Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Creare la prima funzione Azure](../azure-functions/functions-create-first-azure-function.md)
- [Distribuire WebJobs utilizzando Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

In alternativa, è possibile ottenere altre informazioni su questi servizi di integrazione con i collegamenti seguenti:

- [Utilizzare le funzioni di Azure e Azure App servizio per gli scenari di integrazione da Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
- [Integrazioni apportate semplici da Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
- [Logica App Live Webcast](http://aka.ms/logicappslive)
- [Microsoft flusso domande frequenti](https://flow.microsoft.com/documentation/frequently-asked-questions/)
- [Risorse di documentazione WebJobs Azure](../app-service-web/websites-webjobs-resources.md)
