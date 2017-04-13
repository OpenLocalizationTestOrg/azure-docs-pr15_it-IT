<properties
   pageTitle="Risolvere i problemi di Gateway non valido applicazione Gateway (502) | Microsoft Azure"
   description="Informazioni su come risolvere i problemi 502 Gateway applicazione"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Risoluzione dei problemi di gateway non valido nell'applicazione Gateway

## <a name="overview"></a>Panoramica

Dopo aver configurato un Gateway di applicazioni Azure, uno degli errori che possono verificarsi utenti è "Errore del Server: 502 - server Web ha ricevuto risposta non valida funge da un server proxy o un gateway". Questo problema può verificarsi per i motivi principali seguenti:

- Pool di back-end del Gateway di applicazione Azure non è configurato o vuoto.
- Nessuno dei macchine virtuali o le istanze in macchine Virtuali scala impostare è integro.
- Back-end macchine virtuali o le istanze di macchine Virtuali scala impostare non rispondono per la verifica dell'integrità predefinito.
- Esegue la ricerca non valida o non corretta configurazione di stato personalizzati.
- Richiedere il timeout o problemi di connettività con le richieste degli utenti.

## <a name="empty-backendaddresspool"></a>BackendAddressPool vuoto

### <a name="cause"></a>Causa

Se il Gateway di applicazione non dispone di macchine virtuali o macchine Virtuali scala impostare configurato nel pool di indirizzi di back-end, non è possibile indirizzare le richieste di assistenza clienti e genera un errore di gateway non valido.

### <a name="solution"></a>Soluzione

Assicurarsi che il pool di indirizzi di back-end non è vuoto. Può essere eseguita tramite PowerShell, CLI o portale.

    Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

L'output del cmdlet precedente deve contenere pool di indirizzi di back-end non vuoto. Nell'esempio seguente viene nel punto in cui vengono restituiti due pool che sono configurati con nome di dominio completo o indirizzi IP per back-end macchine virtuali. Lo stato di provisioning del BackendAddressPool deve essere 'completato".
    
    BackendAddressPoolsText: 
            [{
                "BackendAddresses": [{
                    "ipAddress": "10.0.0.10",
                    "ipAddress": "10.0.0.11"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool01",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
            }, {
                "BackendAddresses": [{
                    "Fqdn": "xyx.cloudapp.net",
                    "Fqdn": "abc.cloudapp.net"
                }],
                "BackendIpConfigurations": [],
                "ProvisioningState": "Succeeded",
                "Name": "Pool02",
                "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
                "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
            }]


## <a name="unhealthy-instances-in-backendaddresspool"></a>Istanze non corretti in BackendAddressPool

### <a name="cause"></a>Causa

Se tutte le istanze di BackendAddressPool sono danneggiate, applicazione Gateway non avrebbe qualsiasi back-end per indirizzare la richiesta di utente a. Quando le istanze di back-end sono corretti ma non si hanno l'applicazione necessaria distribuito, questa potrebbe essere le maiuscole/minuscole.

### <a name="solution"></a>Soluzione

Assicurarsi che le istanze siano corretti e l'applicazione è configurata correttamente. Verificare se le istanze di back-end sono in grado di rispondere a un ping da un altro macchine Virtuali di VNet stesso. Se è configurato con un punto finale pubblico, assicurarsi che la richiesta del browser per l'applicazione web sia manutenzione.

## <a name="problems-with-default-health-probe"></a>Problemi relativi alla verifica dell'integrità predefinito

### <a name="cause"></a>Causa

502 errori possono essere anche gli indicatori di frequenti che la verifica dell'integrità predefinita non è in grado di raggiungere macchine virtuali di back-end. Quando viene eseguito il provisioning di un'istanza di Gateway di applicazioni, viene configurato automaticamente una verifica dell'integrità predefiniti per ogni BackendAddressPool utilizzando le proprietà del BackendHttpSetting. Per impostare la ricerca non è necessario alcun input dell'utente. Quando è configurato un regola di bilanciamento del carico, viene creata un'associazione tra un BackendHttpSetting e BackendAddressPool. Una ricerca predefinita è configurata per ognuna di queste associazioni e Gateway applicazione avvia una connessione di controllo periodici a ogni istanza BackendAddressPool porta specificato nell'elemento BackendHttpSetting. Nella tabella seguente sono elencati i valori associati la verifica dell'integrità predefinito.


|Proprietà di verifica | Valore | Descrizione|
|---|---|---|
| URL di verifica| http://127.0.0.1/ | Percorso URL |
| Intervallo | 30 | Intervallo di ricerca in secondi |
| Timeout  | 30 | Timeout ricerca in secondi |
| Soglia non corretti | 3 | Ricercare numero di tentativi. Il server di back-end verrà contrassegnato come inattivo dopo il numero di errori consecutivi verifica raggiunge la soglia non corretti. |

### <a name="solution"></a>Soluzione

- Assicurarsi che un sito predefinito sia configurato e ascolto in 127.0.0.1.
- Se BackendHttpSetting specifica una porta diversa da quella 80, il sito predefinito deve essere configurato per ascoltare la porta.
- La chiamata a http://127.0.0.1:port deve restituire un codice di risultato HTTP 200. Deve essere restituito entro il periodo di timeout di 30 sec.
- Assicurarsi che porta configurata sia aperta e che non sono presenti le regole del firewall o i gruppi di sicurezza rete di Azure, cui bloccare il traffico in ingresso o in uscita sulla porta configurata.
- Se si Azure macchine virtuali classiche o un servizio Cloud con FQDN o IP pubblico, verificare che l' [endpoint](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) corrispondente viene aperto.
- Se la macchina virtuale viene configurata tramite Gestione risorse di Azure e non rientra VNet nel punto in cui viene distribuito applicazione Gateway, [Gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) deve essere configurato per consentire l'accesso alla porta desiderata.


## <a name="problems-with-custom-health-probe"></a>Problemi relativi alla verifica dello stato personalizzato

### <a name="cause"></a>Causa

Le ricerche di stato personalizzati consentono maggiore flessibilità le impostazioni predefinite sondaggio comportamento. Quando si usano le ricerche personalizzate, è possibile configurare l'intervallo di verifica, l'URL e percorso da verificare e quante risposte non riuscite per accettare prima di segnare l'istanza di back-end del pool come danneggiato. Le seguenti proprietà aggiuntive vengono aggiunti.

|Proprietà di verifica| Descrizione|
|---|---|
| Nome | Nome della ricerca. Questo nome viene utilizzato per fare riferimento per la ricerca in impostazioni HTTP back-end. |
| Protocollo | Protocollo utilizzato per inviare la ricerca. La ricerca utilizzerà il protocollo definito nelle impostazioni HTTP back-end |
| Host |  Nome host per inviare la ricerca. Si applica solo quando più siti sono configurato nel Gateway di applicazioni. Questa è la differenza tra nome host macchina virtuale.  |
| Percorso | Percorso relativo della ricerca. Path valido inizia con "/". La ricerca viene inviata a \<protocollo\>://\<host\>:\<porta\>\<percorso\> |
| Intervallo | Ricercare intervallo in secondi. Questo è l'intervallo di tempo tra due ricerche consecutive.|
| Timeout | Ricercare timeout in secondi. La ricerca viene contrassegnata come errore se non viene ricevuta una risposta valida durante questo periodo di timeout. |
| Soglia non corretti | Ricercare numero di tentativi. Il server di back-end verrà contrassegnato come inattivo dopo il numero di errori consecutivi verifica raggiunge la soglia non corretti. |


### <a name="solution"></a>Soluzione

Verificare che la verifica dell'integrità della personalizzato sia configurato correttamente come illustrato nella tabella precedente. Oltre alle procedure di risoluzione dei problemi precedente, verificare anche le operazioni seguenti:

- Assicurarsi che la ricerca in modo corretto per la [Guida](application-gateway-create-probe-ps.md).
- Se l'applicazione di Gateway viene configurato per un singolo sito, per impostazione predefinita l'Host del nome deve essere specificato come '127.0.0.1', se non diversamente configurato in verifica personalizzato.
- Assicurarsi che una chiamata a http://\<host\>:\<porta\>\<percorso\> restituisce un codice di risultato HTTP 200.
- Assicurarsi che siano all'interno di intervalli accettabili intervallo, timeout e UnhealtyThreshold.

## <a name="request-time-out"></a>Timeout richiesta

### <a name="cause"></a>Causa

Quando si riceve una richiesta dell'utente, applicazione Gateway Applica regole configurate alla richiesta e viene distribuito in un'istanza di back-end del pool. È in attesa per un intervallo di tempo di una risposta dall'istanza di back-end configurabile. Per impostazione predefinita, questo intervallo è **30 secondi**. Se l'applicazione Gateway non riceve una risposta dall'applicazione di back-end in questo intervallo, richiesta utente vedrà un errore 502.

### <a name="solution"></a>Soluzione

Gateway di applicazioni consente agli utenti di configurare l'impostazione mediante BackendHttpSetting, che potranno essere applicati al pool diversi. Pool di back-end diversi può avere diverse BackendHttpSetting e timeout richiesta pertanto diversi configurato.

    New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non consentono di risolvere il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).
