

<properties
   pageTitle="Panoramica di monitoraggio per Azure applicazione Gateway | Microsoft Azure"
   description="Informazioni sulle funzionalità di monitoraggio di Gateway di applicazioni Azure"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="application-gateway-health-monitoring-overview"></a>Panoramica di monitoraggio dell'integrità Gateway dell'applicazione

Azure Gateway di applicazioni per impostazione predefinita esegue il monitoraggio dell'integrità di tutte le risorse in un pool di back-end e verranno rimosse automaticamente tutte le risorse considerata danneggiata dal pool. Gateway di applicazioni continua a monitorare le istanze non corretti e aggiungerli al pool di back-end integro dopo che diventano disponibili e rispondere ai controlli di integrità. Gateway di applicazioni invia che lo stato Analizza con la stessa porta definita nelle impostazioni di HTTP back-end. In questo modo che la ricerca è test la stessa porta che utilizzeranno i clienti a cui connettersi back-end.

![esempio di applicazione gateway sondaggio][1]

Oltre a utilizzare Monitoraggio verifica dello stato predefinito, è inoltre possibile personalizzare la verifica dell'integrità per soddisfare le esigenze dell'applicazione. In questo articolo sono trattate predefiniti e le ricerche dello stato personalizzato.

## <a name="default-health-probe"></a>Verifica dell'integrità predefinito

Un gateway di applicazioni configura automaticamente una verifica dell'integrità predefinito quando non si imposta qualsiasi configurazione di ricerca personalizzata. Il comportamento monitoraggio funziona effettuando una richiesta HTTP negli indirizzi IP configurati per il pool di back-end. Per le ricerche predefinita se le impostazioni di http back-end sono configurate per HTTPS, la ricerca utilizzerà https anche per verificare l'integrità del back-end.

Ad esempio: configurare il gateway di applicazioni per usare i server di back-end, B e C per ricevere il traffico di rete HTTP sulla porta 80. Il controllo dello stato predefinito verifica tre server ogni 30 secondi di una risposta HTTP integro. Una risposta HTTP integro ha un [codice di stato](https://msdn.microsoft.com/library/aa287675.aspx) da 200 a 399.

Se la verifica di ricerca predefinita non riesce di server, il gateway di applicazione quindi lo rimuove dal proprio pool di back-end e il traffico di rete viene interrotto il flusso al server. La ricerca predefinito continuerà a verificare la presenza di server un ogni 30 secondi. Quando un server risponde correttamente a una richiesta da una verifica dell'integrità predefinito, viene aggiunto nuovamente come integro al pool di back-end e il traffico inizia che scorre nuovamente al server.

### <a name="default-health-probe-settings"></a>Impostazioni di verifica dell'integrità predefinite

|Proprietà di verifica | Valore | Descrizione|
|---|---|---|
| URL di verifica| http://127.0.0.1:\<porta\>/ | Percorso URL |
| Intervallo | 30 | Intervallo di ricerca in secondi |
| Timeout  | 30 | Timeout ricerca in secondi |
| Soglia non corretti | 3 | Ricercare numero di tentativi. Il server di back-end verrà contrassegnato come inattivo dopo il numero di errori consecutivi verifica raggiunge la soglia non corretti. |

> [AZURE.NOTE] La porta sarà sempre la stessa porta le impostazioni di HTTP back-end.

La ricerca predefinito esamina solo http://127.0.0.1:\<porta\> per determinare lo stato di integrità. Se è necessario configurare la verifica dell'integrità per passare a un URL personalizzato o modificare altre impostazioni, è necessario utilizzare le ricerche personalizzate come descritto nei passaggi seguenti.

## <a name="custom-health-probe"></a>Verifica dello stato personalizzato

Ricerche personalizzate consentono di avere un controllo più granulare sul monitoraggio dell'integrità. Quando si usano le ricerche personalizzate, è possibile configurare l'intervallo di verifica, l'URL e percorso da verificare e quante risposte non riuscite per accettare prima di segnare l'istanza di back-end del pool come danneggiato.

### <a name="custom-health-probe-settings"></a>Impostazioni di verifica dell'integrità personalizzato

Nella tabella seguente vengono fornite le definizioni delle proprietà di una verifica dell'integrità personalizzato.

|Proprietà di verifica| Descrizione|
|---|---|
| Nome | Nome della ricerca. Questo nome viene utilizzato per fare riferimento per la ricerca in impostazioni HTTP back-end. |
| Protocollo | Protocollo utilizzato per inviare la ricerca. La ricerca utilizzerà il protocollo definito nelle impostazioni HTTP back-end |
| Host |  Nome host per inviare la ricerca. Applicabile solo quando più siti è configurato nel Gateway di applicazioni, in caso contrario utilizzare '127.0.0.1'. Questa è la differenza tra nome host macchina virtuale. |
| Percorso | Percorso relativo della ricerca. Path valido inizia con "/". |
| Intervallo | Ricercare intervallo in secondi. Questo è l'intervallo di tempo tra due ricerche consecutive.|
| Timeout | Ricercare timeout in secondi. La ricerca viene contrassegnata come errore se non viene ricevuta una risposta valida durante questo periodo di timeout. |
| Soglia non corretti | Ricercare numero di tentativi. Il server di back-end verrà contrassegnato come inattivo dopo il numero di errori consecutivi verifica raggiunge la soglia non corretti. |

> [AZURE.IMPORTANT] Se l'applicazione di Gateway viene configurato per un singolo sito, per impostazione predefinita l'Host del nome deve essere specificato come '127.0.0.1', se non diversamente configurato in verifica personalizzato.
Per riferimento una ricerca personalizzata viene inviata a \<protocollo\>://\<host\>:\<porta\>\<percorso\>. La porta utilizzata sarà la stessa porta definita nelle impostazioni di HTTP back-end.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver approfondire il monitoraggio dell'integrità di Gateway di applicazioni, è possibile configurare una [verifica dell'integrità personalizzato](application-gateway-create-probe-portal.md) nel portale di Azure o una [verifica dell'integrità personalizzato](application-gateway-create-probe-ps.md) tramite PowerShell e il modello di distribuzione di Manager delle risorse di Azure.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png