<properties
    pageTitle="Verifica impostazioni di gestione di traffico | Microsoft Azure"
    description="In questo articolo viene spiegato come verificare le impostazioni di gestore del traffico"
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

# <a name="test-your-traffic-manager-settings"></a>Verificare le impostazioni del gestore del traffico

Per verificare le impostazioni del gestore del traffico, è necessario disporre di più client, in varie posizioni, da cui è possibile eseguire il test. Quindi, visualizzare i punti finali nel profilo di gestore del traffico verso il basso una alla volta.

* Impostare il valore TTL DNS bassa in modo che le modifiche vengano propagate rapidamente (ad esempio 30 secondi).
* Conoscere gli indirizzi IP dei servizi cloud Azure e siti Web nel profilo di cui che si sta verificando.
* Utilizzare gli strumenti che consentono di risolvere un nome DNS a un indirizzo IP e visualizzare tale indirizzo.

Sta verificando che che consente di risolvere i nomi DNS a indirizzi IP degli endpoint nel profilo. I nomi devono risolvere in modo coerente con il metodo di routing il traffico definito nel profilo di traffico Manager. È possibile utilizzare gli strumenti come **nslookup** o **analizzare** la risoluzione dei nomi DNS.

Negli esempi seguenti consentono di verificare il profilo di gestore del traffico.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Controllare il traffico Manager profilo usare nslookup e ipconfig in Windows

1. Aprire un comando o un prompt dei comandi PowerShell di Windows come amministratore.
2. Tipo di `ipconfig /flushdns` per svuotare la cache del Risolutore DNS.
3. Tipo di `nslookup <your Traffic Manager domain name>`. Ad esempio, il comando seguente controlla il nome di dominio con il prefisso *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Un risultato tipico Mostra le informazioni seguenti:

    * Il nome DNS e l'indirizzo IP del server DNS a cui si accede per risolvere questo nome di dominio gestore del traffico.
    * Il nome di dominio gestore del traffico è stato digitato nella riga di comando dopo "nslookup" e l'indirizzo IP a cui si risolve il dominio di gestore del traffico. Il secondo indirizzo IP è più importante per verificare. Deve corrispondere un indirizzo IP (VIP) virtuale pubblico per uno dei servizi cloud o siti Web nel profilo di traffico Manager che si stanno verificando.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Come verificare il metodo di routing il traffico failover

1. Uscire da tutti gli endpoint alto.
2. L'utilizzo di un singolo client, richiedere risoluzione DNS per il nome di dominio della società utilizzando nslookup o un'utilità analoga.
3. Assicurarsi che l'indirizzo IP risolto corrisponda all'endpoint principale.
4. Porta verso il basso il punto finale primario o rimuovere il file di monitoraggio in modo tale gestore del traffico ritiene che l'applicazione è verso il basso.
5. Attendere che il DNS ora-to-Live (TTL) del profilo di traffico Manager più ulteriori due minuti. Ad esempio, se il TTL DNS è 300 secondi (5 minuti), è necessario attendere sette minuti.
6. Scaricare il client cache e richiesta DNS risoluzione DNS utilizzo di nslookup. In Windows, è possibile cancellare la cache DNS con il comando ipconfig /flushdns.
7. Assicurarsi che l'indirizzo IP risolto corrisponda l'endpoint secondario.
8. Ripetere il processo, arrestare, a sua volta ogni endpoint. Verificare che il sistema DNS restituisca l'indirizzo IP dell'endpoint successivo nell'elenco. Dopo aver configurato tutti gli endpoint verso il basso, è necessario ottenere nuovamente l'indirizzo IP dell'endpoint principale.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Come verificare il metodo di routing il traffico Media ponderata

1. Uscire da tutti gli endpoint alto.
2. L'utilizzo di un singolo client, richiedere risoluzione DNS per il nome di dominio della società utilizzando nslookup o un'utilità analoga.
3. Assicurarsi che l'indirizzo IP risolto corrisponde a uno degli endpoint.
4. Svuotare la cache di client DNS e ripetere i passaggi 2 e 3 per ogni endpoint. Vedere indirizzi IP restituiti per ciascun gli endpoint.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Come verificare il metodo di routing il traffico di prestazioni

Per testare in modo efficace un metodo di instradare il traffico di prestazioni, è necessario disporre client situati in diverse parti del mondo. È possibile creare i clienti in diverse aree geografiche Azure che possono essere utilizzate per verificare il provider di servizi. Se si dispone di una rete globale, è possibile accedere a client in altre parti del mondo in remoto e i test da tale posizione.

In alternativa, esistono gratuito basato sul web ricerca DNS e analizzare i servizi disponibili. Alcuni di questi strumenti offrono la possibilità di controllare la risoluzione dei nomi DNS da diverse posizioni in tutto il mondo. Eseguire una ricerca su "Ricerca DNS" per gli esempi. Servizi di terze parti come compresi Gomez o note possono essere utilizzati per verificare che i profili distribuisce il traffico come previsto.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sui metodi di routing il traffico di gestore del traffico](traffic-manager-routing-methods.md)
* [Considerazioni sulle prestazioni di gestore del traffico](traffic-manager-performance-considerations.md)
* [Risoluzione dei problemi gestore del traffico ridotto di stato](traffic-manager-troubleshooting-degraded.md)




