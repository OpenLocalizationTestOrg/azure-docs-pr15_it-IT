<properties 
    pageTitle="Geografico distribuito scala con ambienti servizio App" 
    description="Informazioni su come scalare orizzontalmente App tramite geografico distribuzione con il traffico Manager e App servizio ambienti." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="stefsch"/>   

# <a name="geo-distributed-scale-with-app-service-environments"></a>Geografico distribuito scala con ambienti servizio App

## <a name="overview"></a>Panoramica ##
Scenari di applicazioni che richiedono scala molto elevata possono superare la capacità di elaborazione delle risorse disponibile per una singola distribuzione di un'app.  Le applicazioni di voto, eventi sportivi ed eventi di intrattenimento televisiva sono tutti gli esempi di scenari che richiedono molto alta. Requisiti di alta possono essere soddisfatti ridimensionando orizzontalmente le app, con più distribuzioni app effettuate in una singola regione e di più aree di gestire il carico eccessivo requisiti.

Gli ambienti servizio App sono una piattaforma ideale per scalabilità orizzontale.  Una volta un App ambiente configurazione è stata selezionata in grado di supportare un tasso di richiesta, gli sviluppatori distribuire App servizio ambienti aggiuntivi in modo "stampo" per ottenere una capacità di carico punta desiderato.

Si supponga ad esempio un'app in esecuzione in una configurazione dell'ambiente servizio di App è stata testata per gestire le richieste di 20K per secondo.  Se la capacità di carico punta desiderato non è 100K RPS, cinque (5) App servizio ambienti possibile creati e configurati per garantire che l'applicazione può gestire il carico previsto massimo.

Dal momento che i clienti di access in genere App utilizzo di un dominio personalizzato (o per l'orgoglio), gli sviluppatori devono un modo per distribuire le richieste di app in tutte le istanze di ambiente del servizio di App.  Un'ottima soluzione per eseguire questa operazione consiste nel risolvere il dominio personalizzato tramite un [profilo di Azure il traffico Manager][AzureTrafficManagerProfile].  Il profilo di gestore del traffico può essere configurato per indirizzare tutti i singoli ambienti servizio App.  Gestore del traffico consente di gestire automaticamente i clienti della distribuzione a tutti gli ambienti di servizio App in base alle impostazioni nel profilo di Manager il traffico di bilanciamento del carico.  Questo approccio funziona indipendentemente se tutti gli ambienti di servizio App sono distribuiti in una singola regione Azure o distribuiti in tutto il mondo in più aree di Azure.

Inoltre, dal momento che i clienti di access App mediante il dominio per l'orgoglio, i clienti di rilevano il numero di App servizio ambienti in esecuzione di un'app.  Di conseguenza gli sviluppatori possono in modo semplice e rapido aggiungere e rimuovere, App servizio ambienti in base a carico di traffico osservati.

Il diagramma concettuale seguente illustra un'app orizzontalmente su scalabilità tre ambienti di servizio App in una singola regione.

![Architettura concettuale][ConceptualArchitecture] 

Il resto di questo argomento illustra i passaggi di configurazione una topologia distribuita per l'applicazione di esempio con più ambienti di servizio di App.

## <a name="planning-the-topology"></a>La pianificazione della topologia ##
Prima di creazione di una ridotta app distribuito, può essere utile alcune parti informazioni anticipo.

- **Dominio personalizzato per l'app:**  Che cos'è il nome di dominio personalizzato che i clienti verranno usato per accedere all'app?  Per l'applicazione di esempio il nome di dominio personalizzato è *www.scalableasedemo.com*
- **Gestore del traffico dominio:**  Un nome di dominio deve essere scelto durante la creazione di un [profilo di Azure il traffico Manager][AzureTrafficManagerProfile].  Questo nome verrà combinato con suffisso *trafficmanager.net* per registrare una voce di dominio viene gestita da Gestione il traffico.  Per l'applicazione di esempio, il nome scelto è *scalable ase demo*.  Di conseguenza il nome di dominio completo che viene gestito da Gestione il traffico è *scalable ase demo.trafficmanager.net*.
- **Strategia per il ridimensionamento ingombro app:**  Ingombro applicazione verrà distribuito in ambienti diversi servizi di App nell'area singola?  Più aree?  Un e sconto di entrambi gli approcci?  La decisione deve essere basata sulle aspettative nel punto in cui avranno origine traffico dei clienti, nonché come anche possibile ridimensionare il resto di un'app di supporto dell'infrastruttura back-end.  Ad esempio, con un'applicazione senza stato 100%, un'app può essere market ridimensionata utilizzando una combinazione di più ambienti di servizio App regione Azure moltiplicato per App servizio ambienti distribuiti in più aree di Azure.  Con 15 + pubbliche Azure aree disponibili per la selezione da, gli utenti possono creare realmente una ridotta a livello mondiale hyper scala applicazione.  Per l'applicazione di esempio utilizzato per questo articolo, tre ambienti di servizio di App sono stati creati in una singola regione Azure (sud centrale USA).
- **Convenzione di denominazione per gli ambienti di servizio App:**  Ogni ambiente servizio App richiede un nome univoco.  Oltre a uno o due ambienti di servizio App può essere utile disporre di una convenzione di denominazione per identificare ogni ambiente servizio App.  Per l'applicazione di esempio è stata utilizzata una convenzione di denominazione semplice.  I nomi dei tre ambienti di servizio di App sono *fe1ase*, *fe2ase*e *fe3ase*.
- **Convenzione di denominazione per le app:**  Poiché verranno distribuite più istanze dell'app, è necessario un nome per ogni istanza dell'app distribuita.  Una caratteristica di poco nota ma molto utile di App servizio ambienti è lo stesso nome app può essere utilizzato in ambienti diversi servizi di App.  Poiché ogni ambiente servizio App ha un suffisso dominio univoco, è possibile riutilizzare lo stesso nome app in ogni ambiente gli sviluppatori.  Ad esempio uno sviluppatore può avere App denominato nel modo seguente: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*e così via.  Per l'applicazione di esempio attraverso ogni istanza app ha anche un nome univoco.  I nomi delle varianti app usati sono *webfrontend1*, *webfrontend2*e *webfrontend3*.


## <a name="setting-up-the-traffic-manager-profile"></a>Configurare il profilo di gestore del traffico ##
Una volta più istanze di un'app sono distribuite in più ambienti di servizio App, le istanze di singoli app possono essere registrate con il traffico Manager.  Per l'applicazione di esempio gestore del traffico per *scalable ase demo.trafficmanager.net* per inoltrare i clienti per uno dei seguenti istanze di app distribuito necessario profilo:

- **webfrontend1.fe1ase.p.azurewebsites.net:**  Un'istanza dell'app esempio distribuita nell'ambiente di servizio prima di App.
- **webfrontend2.fe2ase.p.azurewebsites.net:**  Un'istanza dell'app esempio distribuita nel secondo ambiente servizio App.
- **webfrontend3.fe3ase.p.azurewebsites.net:**  Un'istanza dell'app esempio distribuita nell'ambiente di servizio App terza.

È il modo più semplice per registrare più endpoint servizio App Azure siano in esecuzione nella **stessa** area geografica Azure, con la Powershell [il traffico di gestione di Azure risorse di supporto][ARMTrafficManager].  

Il primo passaggio consiste nel creare un profilo di Azure il traffico Manager.  Il codice seguente mostra come è stato creato il profilo per l'applicazione di esempio:

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Si noti come il parametro *RelativeDnsName* è stato impostato su *scalable ase demo*.  Si tratta come il nome di dominio *scalable ase demo.trafficmanager.net* viene creato e associato a un profilo di gestore del traffico.

Il parametro *TrafficRoutingMethod* definisce il bilanciamento del carico criteri di gestione di traffico viene utilizzato per determinare come distribuire il carico cliente tra tutti gli endpoint disponibili.  In questo esempio *Weighted* metodo scelto.  Verrà creato le richieste di assistenza clienti vengano distribuite in tutti gli endpoint applicazione registrata dipende dal peso relativo associato a ogni endpoint. 

Con il profilo creato, ogni istanza app viene aggiunta al profilo come un endpoint di Azure nativo.  Il codice riportato di seguito recupera un riferimento a ogni front-end web app e quindi aggiunge ogni app come endpoint gestore del traffico tramite il parametro *TargetResourceId* .


    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10
    
    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
    
Si noti come c'è una chiamata al *Componente AzureTrafficManagerEndpointConfig* per ogni istanza di singoli app.  Il parametro *TargetResourceId* ogni comando di Powershell fa riferimento a uno dei tre istanze di app distribuita.  Il profilo di gestore del traffico verrà distribuiti carico in tutti gli tre endpoint registrati nel profilo.

Tutti gli tre endpoint utilizzare lo stesso valore (10) per il parametro *peso* .  Il risultato richieste dei clienti distribuzione gestore del traffico in tutte le istanze di tre app relativamente uniforme. 


## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Dominio personalizzato dell'applicazione nel dominio di gestore del traffico verso ##
Il passaggio finale necessario è in modo che puntino il dominio personalizzato dell'app nel dominio di gestore del traffico.  Per l'applicazione di esempio ciò significa che punta *www.scalableasedemo.com* *scalable ase demo.trafficmanager.net*.  Questo passaggio deve essere completate nel Registrar che gestisce il dominio personalizzato.  

Usare gli strumenti di gestione dei domini del registrar, un record CNAME record devono essere create vale a dire il dominio personalizzato nel dominio di gestore del traffico.  Nell'immagine seguente è illustrato un esempio di aspetto questa configurazione CNAME:

![Record CNAME per dominio personalizzato][CNAMEforCustomDomain] 

Sebbene non illustrate in questo argomento, tenere presente che ogni istanza di singoli app deve disporre di dominio personalizzato registrato anche.  In caso contrario se una richiesta di rende a un'istanza di app e l'applicazione non è presente il dominio personalizzato registrato con l'app, la richiesta avrà esito negativo.  

In questo esempio il dominio personalizzato è *www.scalableasedemo.com*ogni istanza dell'applicazione è il dominio personalizzato è associato.

![Dominio personalizzato][CustomDomain] 

Per una panoramica della registrazione di un dominio personalizzato con le applicazioni di servizio App Azure, vedere il seguente articolo su come [registrare domini personalizzati][RegisterCustomDomain].

## <a name="trying-out-the-distributed-topology"></a>Provare la topologia distribuito ##
Il risultato finale della configurazione del DNS e il traffico Manager è che le richieste di *www.scalableasedemo.com* passeranno attraverso la sequenza di seguito:

1. Un browser o un dispositivo renderà una ricerca DNS per *www.scalableasedemo.com*
2. La voce CNAME presso il registrar causa la ricerca a reindirizzati a Azure il traffico di gestione DNS.
3. Per *demo.trafficmanager.net scalable ase* sulla base di uno dei server DNS Manager il traffico di Azure viene effettuata una ricerca DNS.
4. In base a criteri (il parametro *TrafficRoutingMethod* utilizzato in precedenza per la creazione del profilo di gestore del traffico) di bilanciamento del carico, il traffico Manager verrà selezionare uno dei endpoint configurati e restituire il FQDN di tale endpoint nel browser o dispositivo.
5.  Poiché il nome di dominio completo dell'endpoint è l'Url di un'istanza di app in esecuzione in un ambiente App, il browser o il dispositivo verrà chiesto di un server DNS Microsoft Azure per risolvere il FQDN a un indirizzo IP. 
6. Nel browser o dispositivo invierà la richiesta HTTP/S all'indirizzo IP.  
7. La richiesta verrà recapitati in una delle istanze di app in esecuzione su uno degli ambienti di servizio di App.

Nell'immagine console seguente mostra una ricerca DNS del dominio personalizzato dell'applicazione di esempio viene risolto correttamente a un'istanza di app in esecuzione su uno dei tre esempio App servizio ambienti (in questo caso il secondo dei tre ambienti di servizio App):

![Ricerca DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Informazioni e collegamenti aggiuntivi ##
Tutti gli articoli e la modalità-per per gli ambienti servizio App sono disponibili nel [file Leggimi per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

Documentazione in di Powershell [il traffico di gestione di Azure risorse di supporto][ARMTrafficManager].  

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]:  https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]:  https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]:  https://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
