<properties
    pageTitle="Tipi di traffico Manager Endpoint | Microsoft Azure"
    description="Questo articolo illustra i diversi tipi di endpoint che possono essere usate con Azure il traffico Manager"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="traffic-manager-endpoints"></a>Gestore del traffico endpoint

Gestione il traffico di Microsoft Azure consente di controllare la modalità di distribuzione il traffico di rete per le distribuzioni di applicazioni esecuzione nei data center diverso. Configurare la distribuzione di ogni applicazioni come un punto finale di traffico Manager. Quando il traffico Manager riceve una richiesta DNS, sceglie un endpoint da restituire nella risposta DNS. Gestore del traffico nella scelta si basa sullo stato dell'endpoint corrente e il metodo di routing del traffico. Per ulteriori informazioni, vedere [Come funziona il traffico Manager](traffic-manager-how-traffic-manager-works.md).

Esistono tre tipi di endpoint supportati da Gestione il traffico:

- **Endpoint Azure** vengono utilizzati per servizi ospitati in Azure.
- **Endpoint esterni** vengono utilizzati per servizi ospitati all'esterno di Azure, in locale o con un provider di hosting diverso.
- **I punti finali annidate** vengono utilizzati per combinare i profili di gestore del traffico per creare combinazioni di routing del traffico più flessibile per supportare le esigenze di distribuzioni più complesse.

Non esiste nessuna restrizione su come i punti finali dei diversi tipi vengono combinati in un unico profilo di gestore del traffico. Ogni profilo può contenere qualsiasi combinazione di tipi di endpoint.

Le sezioni seguenti descrivono ogni tipo di endpoint in modo più approfondito.

## <a name="azure-endpoints"></a>Endpoint Azure

Endpoint Azure vengono utilizzati per servizi basati su Azure in gestore del traffico. Sono supportati i tipi di Azure risorse seguenti:

- Servizi cloud macchine virtuali IaaS 'classica e PaaS.
- Web App
- Risorse PublicIPAddress (che possono essere collegati alle macchine virtuali direttamente o tramite un servizio di bilanciamento del carico di Azure). Il publicIpAddress deve avere un nome DNS assegnato da utilizzare in un profilo di gestore del traffico.

Risorse PublicIPAddress sono Manager delle risorse di Azure. Non sono presenti nel modello di distribuzione classica. Pertanto sono solo supportati in gestore del traffico del Manager delle risorse Azure esperienze. Gli altri tipi di endpoint sono supportati tramite Gestione risorse e il modello di distribuzione classica.

Quando si usano i punti finali Azure, il traffico Manager rileva una VM IaaS 'Classica', servizio cloud o da un'App Web viene interrotta e avviata. Questo stato viene visualizzato nello stato dell'endpoint. Per informazioni dettagliate, vedere [il monitoraggio di gestore del traffico endpoint](traffic-manager-monitoring.md#endpoint-and-profile-status) . Quando viene interrotto il servizio sottostante, il traffico Manager non esegue verifiche integrità endpoint o il traffico diretto all'endpoint. Non gestore del traffico fatturazione si verificano eventi dell'istanza interrotto. Dopo il riavvio del servizio, creare un curriculum fatturazione e l'endpoint sia idoneo per ricevere il traffico. Il rilevamento non è valida per gli endpoint PublicIpAddress.

## <a name="external-endpoints"></a>Endpoint esterni

Endpoint esterni vengono utilizzati per servizi all'esterno di Azure. Ad esempio un servizio ospitato locale o con un provider diverso. Endpoint esterni possono utilizzare singolarmente o combinati con i punti finali Azure lo stesso profilo di gestore del traffico. La combinazione di Azure endpoint con endpoint esterni Abilita diversi scenari:

- In uno dei due un modello di failover attivo attivo o attivo-passivo, utilizzare Azure per fornire maggiore ridondanza esistenza di un oggetto locale dell'applicazione.
- Per ridurre la latenza di applicazione per gli utenti in tutto il mondo, estendere un'applicazione locale esistente in altre aree geografiche in Azure. Per ulteriori informazioni, vedere [gestore del traffico 'Prestazioni' routing del traffico](traffic-manager-routing-methods.md#performance-traffic-routing-method).
- Usare Azure per fornire capacità aggiuntiva per un oggetto esistente locale applicazione, continuamente o come soluzione 'burst nel cloud' Raccoglitore nella richiesta di riunione.

In alcuni casi può essere utile usare endpoint esterni di fare riferimento a servizi Azure (per esempi, vedere [domande frequenti](#faq)). In questo caso, verifiche integrità vengono addebitate con la velocità di Azure endpoint, non il tasso di endpoint esterni. Tuttavia, a differenza di Azure endpoint, se si arresta o eliminare il servizio sottostante prevenzione fatturazione finché non si disattiva o eliminare l'endpoint di traffico Manager.

## <a name="nested-endpoints"></a>Endpoint annidate

Endpoint annidato combinare più profili di gestore del traffico per creare combinazioni di routing del traffico flessibili e supportare le esigenze di distribuzioni complesse. Con i punti finali annidate, un profilo 'figlio' viene aggiunto come un endpoint per un profilo 'parent'. I profili di padre e figlio possono contenere altri endpoint di qualsiasi tipo, inclusi altri profili annidati. Per ulteriori informazioni, vedere [i profili di gestore del traffico annidate](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web App come endpoint

Durante la configurazione di applicazioni Web come endpoint in gestore del traffico applicate alcune considerazioni aggiuntive:

1. Solo Web Apps SKU 'Standard' o versioni successive sono idonei per l'utilizzo con il traffico Manager. Tentativi per aggiungere un'App Web di uno SKU inferiore esito negativo. Il downgrade SKU di un'App Web esistente, i risultati in gestore del traffico non inviare il traffico a tale Web App.

2. Quando un endpoint riceve una richiesta HTTP, utilizza l'intestazione 'host' nella richiesta per determinare quali App Web devono soddisfare la richiesta. L'intestazione host contiene il nome DNS utilizzato per avviare la richiesta, ad esempio 'contosoapp.azurewebsites.net'. Per utilizzare un nome DNS diverso con l'App Web, il nome DNS deve essere registrato come un nome di dominio personalizzato per l'App. Quando si aggiunge un endpoint Web App come endpoint Azure, il nome del DNS Manager il traffico profilo viene automaticamente registrato per l'App. La registrazione viene rimosso automaticamente quando viene eliminato il punto finale.

3. Ogni profilo di gestore del traffico può contenere al massimo un endpoint Web App da ogni regione Azure. Per risolvere per il vincolo, è possibile configurare un'applicazione Web come un endpoint esterno. Per ulteriori informazioni, vedere [domande frequenti](#faq).

## <a name="enabling-and-disabling-endpoints"></a>Attivare e disattivare i punti finali

La disattivazione di un estremo in gestore del traffico può essere utile per rimuovere temporaneamente il traffico da un endpoint che si trova nella modalità di manutenzione o viene ridistribuito. Dopo il punto finale esegue nuovamente, sarà possibile riattivare.

Endpoint possono essere attivati e disabilitati tramite il portale di gestione di traffico, PowerShell, CLI o API REST, ognuno dei quali sono supportati in Gestione risorse e il modello di distribuzione classica.

>[AZURE.NOTE] La disattivazione di un estremo Azure ha niente a che fare con lo stato di distribuzione di Azure. Un servizio di Azure (ad esempio un macchine Virtuali o un'App Web rimane in esecuzione e grado di ricevere il traffico anche quando disabilitata in Gestione il traffico. Il traffico può essere gestito direttamente all'istanza del servizio invece che tramite il nome del DNS Manager il traffico profilo. Per ulteriori informazioni, vedere [come funziona il gestore del traffico](traffic-manager-how-traffic-manager-works.md).

Idoneità corrente di ogni endpoint a cui inviare il traffico dipende dai fattori seguenti:

- Lo stato di profilo (abilitato/disabilitato)
- Lo stato dell'endpoint (abilitato/disabilitato)
- I risultati dell'integrità verifica la presenza di tale endpoint

Per informazioni dettagliate, vedere [il monitoraggio di gestore del traffico endpoint](traffic-manager-monitoring.md#endpoint-and-profile-status).

>[AZURE.NOTE] Poiché il traffico Manager funziona a livello di DNS, non è in grado di influenzare connessioni esistenti in qualsiasi punto finale. Quando un endpoint non è disponibile, il traffico Manager indirizza nuove connessioni a un altro endpoint disponibile. Tuttavia, l'host dietro l'endpoint disabilitato o non corretti potrebbe continueranno a ricevere il traffico tramite connessioni esistenti fino a quando non vengono terminate tali sessioni. Applicazioni è consigliabile limitare la durata della sessione per consentire il traffico Svuota da connessioni esistenti.

Se tutti gli endpoint di un profilo sono disabilitati o se il profilo stesso è disattivato, il traffico Manager invia una risposta di 'NXDOMAIN' a una nuova query DNS.

## <a name="faq"></a>Domande frequenti

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>È possibile utilizzare il traffico Manager con i punti finali da più abbonamenti?

Tramite i punti finali da più abbonamenti non è possibile con Azure Web Apps. Azure Web App è necessario che qualsiasi nome di dominio personalizzato con Web Apps è utilizzato solo all'interno di una singola sottoscrizione. Non è possibile utilizzare App Web di più abbonamenti con lo stesso nome di dominio.

Per altri tipi di endpoint, è possibile usare il traffico di gestione con i punti finali da più di una sottoscrizione. Come si configura gestore del traffico dipende se si usa il modello di distribuzione classica o l'esperienza di gestione risorse.

- In Gestione risorse endpoint da tutti gli abbonamenti possono essere aggiunti al gestore del traffico, purché chi ha configurato il profilo di gestore del traffico ha accesso in lettura all'endpoint. È possibile concedere le autorizzazioni tramite [controllo dell'accesso basato sui ruoli Gestione risorse di Azure (RBAC)](../active-directory/role-based-access-control-configure.md).
- Nell'interfaccia di modello distribuzione classica gestore del traffico richiede che delle applicazioni Web configurato come endpoint Azure o servizi Cloud si trovino nella stessa sottoscrizione il profilo di gestore del traffico. Endpoint del servizio cloud in altri abbonamenti possono essere aggiunti al gestore del traffico come endpoint 'esterno'. Questi endpoint esterni vengono addebitati come endpoint di Azure, anziché il tasso di esterno.

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>È possibile utilizzare il traffico Manager con guide 'Gestione temporanea' servizio Cloud?

Sì. Servizio cloud prova bande orarie può essere configurata in Gestione il traffico come endpoint esterno. Verifiche integrità addebitate ancora alla tariffa endpoint Azure. Poiché il tipo di endpoint esterno è in uso, le modifiche al servizio sottostante non vengono selezionate automaticamente. Con gli endpoint esterni, il traffico Manager in grado di rilevare quando il servizio Cloud viene interrotto o eliminato. Di conseguenza, il responsabile di traffico finché non fatturazione per verifiche integrità l'endpoint è stato disabilitato o eliminato.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Gestore del traffico supporta IPv6 endpoint?

Il traffico Manager non è attualmente disponibile addressible IPv6 server dei nomi. Tuttavia, il traffico Manager ancora possono essere usate dal client IPv6 che si connettono a IPv6 i punti finali. Un client non richiedere DNS direttamente al gestore del traffico. Se, tuttavia, il client utilizza un servizio DNS ricorsiva. Un client solo IPv6 invia richieste al servizio DNS ricorsiva mediante IPv6. Quindi il servizio ricorsivo dovrebbe essere possibile contattare il server dei nomi gestore del traffico attraverso IPv4.

Gestore del traffico risponde con il nome DNS dell'endpoint. Per supportare un endpoint IPv6, deve essere presente un record DNS AAAA che fa riferimento il nome dell'endpoint di DNS per l'indirizzo IPv6. Verifiche integrità gestore del traffico supportano solo indirizzi IPv4. Il servizio deve esporre un endpoint IPv4 sullo stesso nome DNS.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>È possibile utilizzare il traffico Manager con più Web App nella stessa regione?

In genere, il traffico Manager viene utilizzato per indirizzare il traffico alle applicazioni distribuite in diverse aree geografiche. Tuttavia, può anche essere utilizzato in un'applicazione ha più di una distribuzione nella stessa regione. I punti finali Azure gestore del traffico non consentire più endpoint Web App dall'area di Azure stesso da aggiungere lo stesso profilo di gestore del traffico.

La procedura seguente offre una soluzione per questo vincolo:

1. Verificare che gli endpoint siano presenti diversi web app 'unità scala'. Un nome di dominio deve eseguire il mapping a un singolo sito in un'unità in scala specificato. Due Web Apps nella stessa unità di scala di conseguenza, non è possibile condividere un profilo di gestore del traffico.
2. Aggiungere il nome del dominio per l'orgoglio come un nome host personalizzato a ogni Web App. Ogni Web App deve essere in un'unità in scala diverso. Tutte le app Web deve appartenere allo stesso abbonamento.
3. Aggiungere endpoint Web App (un unico) al proprio profilo di gestore del traffico, come un endpoint Azure.
4. Aggiungere ogni endpoint Web App aggiuntive al proprio profilo di gestore del traffico come un endpoint esterno. Endpoint esterni possono essere aggiunti solo utilizzando il modello di distribuzione di Manager delle risorse.
5. Creare un record CNAME DNS del dominio per l'orgoglio che indichi il nome del DNS Manager il traffico profilo (<>.... trafficmanager.net).
6. Accedere al sito tramite il nome di dominio per l'orgoglio, non il nome del DNS Manager il traffico profilo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [come funziona il gestore del traffico](traffic-manager-how-traffic-manager-works.md).
- Informazioni sulle gestore del traffico [endpoint monitoraggio e failover automatico](traffic-manager-monitoring.md).
- Informazioni sui [metodi di routing il traffico](traffic-manager-routing-methods.md)di gestore del traffico.
