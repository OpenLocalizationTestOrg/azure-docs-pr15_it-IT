<properties
   pageTitle="Introduzione al Gateway di applicazione | Microsoft Azure"
   description="Questa pagina offre una panoramica del servizio Gateway di applicazioni per il bilanciamento del carico di livello 7, tra cui le dimensioni di gateway, HTTP caricare affinità sessione bilanciamento del carico, basata su cookie e trasferire SSL."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="application-gateway-overview"></a>Panoramica del Gateway di applicazioni

## <a name="what-is-application-gateway"></a>Che cos'è Gateway applicazione

Gateway di applicazioni di Microsoft Azure fornisce applicazione recapito Controller (ADC) come un servizio, che offre vari livello 7 bilanciamento del carico funzionalità dell'applicazione. Consente ai clienti di ottimizzare la produttività farm web possibilità di scaricare terminazione SSL intensivo CPU al Gateway dell'applicazione. Fornisce anche altre funzionalità di routing livello 7 inclusi circolari distribuzione del traffico in arrivo, in base a cookie affinità sessione, routing percorso in base a URL e possibilità di ospitare più siti Web dietro un singolo Gateway dell'applicazione. Applicazione Gateway associato anche un firewall di applicazione web (WAF) per la protezione dell'applicazione con la maggior parte delle OWASP superiore 10 web vulnerabilità comuni. Gateway di applicazioni può essere configurato come gateway esposto internet, gateway solo interno o una combinazione di entrambi. Gateway di applicazione è completamente Azure gestito, scalabilità e disponibilità. Fornisce una vasta gamma di diagnostica e funzionalità di registrazione per una migliore gestibilità. Gateway di applicazione funziona con macchine virtuali, servizi cloud e applicazioni web esposto interna o esterna.

Gateway di applicazioni è un accessorio virtuale dedicato per l'applicazione ed è costituita da più istanze di lavoro per disponibilità e scalabilità elevate. Quando si crea un gateway di applicazioni, un endpoint (VIP pubblico o interno ILB IP) è associato e utilizzato per il traffico di rete in ingresso. L'indirizzo VIP o ILB IP viene fornito dal servizio di bilanciamento del carico Azure al livello di trasporto (TCP/UDP) e tutto il traffico rete in arrivo vengano il bilanciamento del carico per le istanze di lavoro di Gateway di applicazioni. Il Gateway di applicazioni quindi indirizza il traffico HTTP/HTTPS se in base alla configurazione è una macchina virtuale cloud servizio interno o un indirizzo IP esterno. Per il contratto di servizio e prezzi, fare riferimento alle pagine del [contratto di servizio](https://azure.microsoft.com/support/legal/sla/) e [prezzi](https://azure.microsoft.com/pricing/details/application-gateway/) .

## <a name="features"></a>Caratteristiche

Gateway di applicazioni supporta attualmente distribuzione delle applicazioni 7 layer con le caratteristiche seguenti:

- **[Firewall di applicazione web (Preview)](application-gateway-webapplicationfirewall-overview.md)** - il firewall di applicazione web (WAF) in Azure applicazione Gateway protegge le applicazioni web da attacchi comuni basato sul web come inserimento SQL, attacchi di script e assume il controllo della sessione.
- **Bilanciamento del carico HTTP** - applicazione Gateway consente di bilanciamento del carico circolari. Bilanciamento del carico viene eseguita al livello 7 e viene utilizzata per il traffico HTTP (S).
- **Affinità sessione basata su cookie** - questa funzionalità è utile quando si desidera mantenere una sessione utente nello stesso back-end. Utilizzando i cookie gateway gestito il Gateway di applicazione in grado di indirizzare il traffico successive da una sessione utente alla stessa back-end per l'elaborazione. Questa caratteristica è importante nei casi in cui lo stato della sessione viene salvato in locale nel server di back-end per una sessione utente.
- **[Trasferire secure Sockets Layer (SSL)](application-gateway-ssl-arm.md)** - questa caratteristica richiede l'attività costosa di decrittografare HTTPS disattivare il server web. Chiudendo la connessione SSL gateway applicazione e inoltrare la richiesta al server non crittografato, il server web è unburdened per la decrittografia.  Gateway di applicazioni crittografa nuovamente la risposta prima di inviarlo al client. Questa funzionalità è utile in scenari in cui back-end si trova nella stessa rete virtuale protetta del Gateway dell'applicazione in Azure.
- **[To End SSL](application-gateway-backend-ssl.md)** - Gateway applicazione supporta la crittografia to end del traffico. A tale scopo, applicazione Gateway chiusura della connessione SSL gateway dell'applicazione. Il gateway quindi applica le regole di routing per il traffico nuovamente la crittografia del pacchetto e inoltra il pacchetto back-end appropriato in base alle regole di routing definite. Le risposte dal server web attraversa lo stesso processo di tornare all'utente finale.
- **[Basato su URL distribuzione del contenuto](application-gateway-url-route-overview.md)** - questa caratteristica consente di utilizzare diversi server di back-end per il traffico diversi. Il traffico per una cartella sul server web o per una rete CDN può essere indirizzato a un diverso back-end, ridurre il carico non necessario sul back-end che non servono contenuto specifico.
- **[Più siti routing](application-gateway-multi-site-overview.md)** - applicazione gateway consente di consolidare fino a 20 siti Web su un gateway singola applicazione.
- **[Supporto Websocket](application-gateway-websocket.md)** - un'altra ottima funzionalità del Gateway di applicazione è il supporto nativo per Websocket.
- **[Il monitoraggio dell'integrità](application-gateway-probe-overview.md)** - gateway applicazione fornisce integrità predefinito monitoraggio delle risorse back-end e personalizzato analizza per verificare la presenza di scenari più specifici.

## <a name="benefits"></a>Vantaggi

Gateway di applicazioni è utile per:

- Applicazioni che richiedono richieste dalla stessa sessione utente/client per raggiungere la stessa macchina virtuale back-end. Esempi di queste applicazioni da acquisti App carrello e i server di posta elettronica web.
- Applicazioni per sbloccare il server web farm dal sovraccarico di terminazione SSL.
- Applicazioni, ad esempio una rete di distribuzione di contenuti, che richiede più richieste HTTP sulla stessa connessione TCP lunga per essere distribuiti o si carica bilanciato nei diversi server di back-end.
- Applicazioni che supportano il traffico websocket
- Proteggere le applicazioni web attacchi comuni basato sul web come SQL inserimento attacchi di script e assume il controllo della sessione.

Gateway di applicazioni il bilanciamento del carico come un servizio gestito Azure consente la creazione di un bilanciamento del carico di livello 7 dietro al servizio di bilanciamento del carico software Azure. Il traffico manager può essere utilizzato per completare lo scenario, come illustrato nell'immagine seguente. In cui il traffico gestore fornisce il reindirizzamento e la disponibilità, bilanciamento del carico fornisce disponibilità e scalabilità area geografica e gateway applicazione fornisce bilanciamento del carico area cross livello 7.

![asdasd](./media/application-gateway-introduction/tm-lb-ag-scenario.png)

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="gateway-sizes-and-instances"></a>Istanze e le dimensioni di gateway

Gateway di applicazioni è attualmente disponibile in tre formati: piccole, medie e grandi dimensioni. Le dimensioni di un'istanza ridotta devono essere sviluppo e scenari di test.

Esistono due SKU di Gateway di applicazioni: WAF e Standard.

È possibile creare fino a 50 gateway per applicazioni per abbonamento e gateway ogni applicazione può avere fino a 10 istanze. Ogni gateway applicazione possono essere composti da 20 listener http. Per un elenco completo dei limiti di gateway applicazione visitare la pagina [Limiti di servizio](../azure-subscription-service-limits.md#application-gateway) .

Nella tabella seguente mostra una velocità media delle prestazioni per ogni istanza di gateway dell'applicazione:

| Risposta back-end | Piccolo | Media | Grande|
|---|---|---|---|
| 6K | 7.5 Mbps | 13 Mbps | 50 Mbps |
|100 KB | 35 Mbps | 100 Mbps| 200 Mbps |

>[AZURE.NOTE] Questi valori sono approssimativa per una velocità di gateway di applicazione. La velocità effettiva dipende da vari dettagli sull'ambiente, ad esempio dimensioni medio, posizione in cui le istanze di back-end e il tempo di elaborazione per visualizzare una pagina. Per i numeri delle prestazioni esatta, è necessario eseguire il proprio test, tali valori vengono forniti solo per la capacità Guida alla pianificazione.

## <a name="health-monitoring"></a>Il monitoraggio dell'integrità

Gateway applicazione Azure controlla automaticamente lo stato delle istanze di back-end tramite basic o esegue la ricerca dello stato personalizzato. Tramite le ricerche integrità, in questo modo che solo integro hosts rispondere per il traffico. Per ulteriori informazioni, vedere [Panoramica monitoraggio dell'integrità di Gateway di applicazioni](application-gateway-probe-overview.md).

## <a name="configuring-and-managing"></a>Configurare e gestire

Per l'endpoint gateway applicazione può avere un indirizzo IP pubblico, IP privato o entrambi quando è configurato. Applicazione Gateway viene configurato all'interno di una rete virtuale nella propria subnet. Subnet creato o utilizzato per il gateway di applicazione non può contenere altri tipi di risorse, le risorse sola consentiti nella subnet sono gateway per altre applicazioni. Per proteggere le risorse back-end back-end server possono essere contenuti in un'altra subnet nella stessa rete virtuale il gateway di applicazione. Subnet aggiuntive che necessari per le applicazioni di back-end, non come gateway applicazione contattarle l'indirizzo ip, gateway di applicazioni in grado di fornire funzionalità ADC per il server di back-end.

È possibile creare e gestire un gateway di applicazioni utilizzando API REST, i cmdlet di PowerShell, CLI Azure o [portale Azure](https://portal.azure.com/).

## <a name="next-steps"></a>Passaggi successivi

Dopo l'apprendimento gateway applicazione, è possibile [creare un gateway di applicazioni](application-gateway-create-gateway-portal.md) oppure è possibile [creare un gateway di applicazioni SSL trasferire](application-gateway-ssl-arm.md) alle connessioni HTTPS bilanciamento del carico.

Per informazioni su come creare un gateway di applicazione tramite basati su URL distribuzione del contenuto, passare a [creare un gateway di applicazione tramite routing basato su URL](application-gateway-create-url-route-arm-ps.md) per ulteriori informazioni.

