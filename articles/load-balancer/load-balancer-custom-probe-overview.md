<properties
  pageTitle="Caricare le ricerche personalizzate di bilanciamento del carico e monitorare lo stato di integrità | Microsoft Azure"
  description="Informazioni su come utilizzare le ricerche personalizzate per Azure di bilanciamento del carico per monitorare istanze supporto bilanciamento del carico"
  services="load-balancer"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="understand-load-balancer-probes"></a>Comprendere le ricerche bilanciamento carico

Bilanciamento del carico Azure offre la possibilità di monitorare l'integrità istanze del server tramite le ricerche. Quando una verifica cessa di rispondere, bilanciamento del carico interrompe l'invio di nuove connessioni all'istanza non corretti. Connessioni esistenti non sono interessate e nuove connessioni vengono inviate a istanze integro.

Ruoli del servizio cloud (ruoli di lavoro e ruoli web) usare un agente di guest per il monitoraggio di verifica. Quando si utilizza macchine virtuali di supporto di bilanciamento del carico, è necessario configurare TCP o HTTP ricerche personalizzate.

## <a name="understand-probe-count-and-timeout"></a>Informazioni su verifica conteggio e timeout

Comportamento di verifica dipende:

- Il numero di esito negativo ricerche che consentono di un'istanza l'etichetta come attivo.
- Il numero di ricerche non riuscite che causano un'istanza l'etichetta come inattivo.

Il valore di timeout e la frequenza di SuccessFailCount per determinare se un'istanza è confermata all'esecuzione o non è in esecuzione. Nel portale di Azure, il timeout è impostato su due volte il valore della frequenza.

La configurazione di ricerca di tutte le istanze di bilanciamento del carico per un endpoint (ovvero, un set bilanciamento del carico) deve corrispondere. Di conseguenza, che non è possibile assegnare una configurazione di ricerca diversi per ogni istanza del ruolo o la macchina virtuale nel servizio di hosting stesso per una combinazione di endpoint in questione. Ad esempio, ogni istanza deve avere timeout e porte locali identiche.

>[AZURE.IMPORTANT] Una ricerca di bilanciamento del carico utilizza l'indirizzo IP 168.63.129.16. L'indirizzo IP pubblico facilita la comunicazione interna piattaforma risorse di per la porta-your-proprietari-IP scenario di rete virtuale Azure. L'indirizzo IP pubblico virtuale 168.63.129.16 viene utilizzato in tutti i paesi e rimarrà invariato. È consigliabile consentire l'indirizzo IP in tutti i criteri di firewall locale. Essa non deve essere considerato un rischio perché solo la piattaforma Azure interna possa origine di un messaggio da tale indirizzo. Se non esegue questa operazione, si verificherà un comportamento imprevisto in diversi scenari come configurazione stesso intervallo di indirizzi IP di 168.63.129.16 e verificano duplicato indirizzi IP.

## <a name="learn-about-the-types-of-probes"></a>Informazioni sui tipi di ricerche

### <a name="guest-agent-probe"></a>Verifica agente guest

La ricerca è disponibile per i servizi Cloud Azure solo. Bilanciamento del carico utilizza l'agente di guest macchina virtuale è in attesa e risponde con una risposta HTTP 200 OK solo quando l'istanza è pronto (vale a dire non in un'altra provincia, ad esempio occupato, riciclate o arresto).

Per ulteriori informazioni, vedere [configurazione di file di definizione del servizio (csdef) per le ricerche integrità](https://msdn.microsoft.com/library/azure/ee758710.aspx) o [iniziare a creare una connessione Internet di bilanciamento del carico per i servizi cloud](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Caratteristiche di una verifica dell'agente guest contrassegnare un'istanza come danneggiato?

Se l'agente di guest cessa di rispondere con HTTP 200 OK, bilanciamento del carico contrassegna l'istanza come non risponde e smette di inviare il traffico a tale istanza. Bilanciamento del carico continua a effettuare il ping dell'istanza. Se l'agente di guest risponde con un 200 HTTP, bilanciamento del carico invia nuovamente a quell ' istanza il traffico.

Quando si utilizza un ruolo web, sito Web in genere viene eseguito il codice in w3wp.exe, non monitorate da di Azure agente tessuti o guest. Ciò significa che non verranno segnalati errori di w3wp.exe (ad esempio, le risposte HTTP 500) per l'agente di guest e il servizio di bilanciamento del carico non avrà l'occorrenza dalla rotazione.

### <a name="http-custom-probe"></a>Verifica personalizzato HTTP

La ricerca di bilanciamento del carico HTTP personalizzata verrà ignorata la ricerca di agente guest predefinito, il che significa che è possibile creare la logica personalizzata per determinare lo stato dell'istanza di ruolo. Bilanciamento del carico analizza l'endpoint ogni 15 secondi, per impostazione predefinita. L'istanza viene considerata come la rotazione di bilanciamento del carico se risponde con un 200 HTTP entro il periodo di timeout (31 secondi per impostazione predefinita).

Può essere utile se si desidera implementare la logica per rimuovere le istanze di rotazione di bilanciamento carico. Ad esempio, è possibile decidere di rimuovere un'istanza se sopra 90% CPU e restituisce uno stato non 200. Se si dispone di ruoli di web che utilizzano w3wp.exe, anche significa che viene visualizzato automatico monitoraggio del sito Web, poiché gli errori nel codice del sito Web restituirà uno stato non 200 per la ricerca di bilanciamento del carico.

>[AZURE.NOTE] La ricerca personalizzata HTTP supporta percorsi relativi e il protocollo HTTP. HTTPS non è supportata.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Caratteristiche di una verifica personalizzata HTTP contrassegnare un'istanza come danneggiato?

- L'applicazione HTTP restituisce un codice di risposta HTTP diverso da 200 (ad esempio 403, 404 o 500). Verrà visualizzata una conferma positiva istanza dell'applicazione eseguire subito fuori servizio.
- Il server HTTP non risponde tutto dopo il periodo di timeout. A seconda il valore di timeout è impostato, potrebbero le implicazioni che verifica più le richieste di viaggio senza risposta prima la ricerca viene contrassegnata come non è in esecuzione (ovvero, prima di SuccessFailCount le ricerche vengono inviate).
- Il server chiude la connessione tramite un ripristino TCP.

### <a name="tcp-custom-probe"></a>Verifica personalizzato TCP

Le ricerche TCP avviare una connessione per eseguire un handshake tre elementi con la porta definita.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Caratteristiche di una ricerca personalizzata TCP contrassegnare un'istanza come danneggiato?

- Il server TCP non risponde tutto dopo il periodo di timeout. Quando la ricerca viene contrassegnata come non è in esecuzione dipende dal numero di richieste di verifica non riuscito che sono stati configurati per passare senza risposta prima di segnare la ricerca come non è in esecuzione.
- La ricerca riceve una TCP Reimposta l'istanza di ruoli.

Per ulteriori informazioni sulla configurazione di una verifica dell'integrità HTTP o una verifica TCP, vedere [informazioni introduttive sulla creazione di un in Gestione risorse tramite PowerShell di bilanciamento del carico esposto a Internet](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Aggiungere le istanze integro in rotazione bilanciamento carico

Porte TCP e HTTP le ricerche sono considerate integro e contrassegnare l'istanza di ruolo come integro quando:

- Bilanciamento del carico Ottiene una verifica positiva si avvia la macchina virtuale per la prima volta.
- Il numero SuccessFailCount (descritto in precedenza) definisce il valore di esito negativo ricerche necessarie per contrassegnare l'istanza di ruolo come integro. Se è stato rimosso un'istanza del ruolo, il numero di ricerche corretta e successivi deve essere uguale o superare il valore di SuccessFailCount per contrassegnare l'istanza di ruoli come in esecuzione.

>[AZURE.NOTE] Se lo stato di un'istanza del ruolo è fluttuanti, bilanciamento del carico che venga più prima di attivare l'istanza di ruoli di nuovo nello stato di integrità. Questa operazione viene eseguita tramite i criteri per proteggere l'utente e l'infrastruttura.

## <a name="use-log-analytics-for-load-balancer"></a>Usare analitica log per bilanciamento del carico

È possibile utilizzare [analitica del Registro di sistema di bilanciamento del carico](load-balancer-monitor-log.md) per verificare se lo stato di integrità verifica conteggio sondaggio. La registrazione è utilizzabile con Power BI o Azure operativi approfondimenti per fornire le statistiche relative allo stato di integrità di bilanciamento del carico.
