<properties
    pageTitle="Considerazioni sulle prestazioni di Azure il traffico Manager | Microsoft Azure"
    description="Comprendere le prestazioni di gestore del traffico e come verificare le prestazioni del sito Web quando si utilizza il traffico Manager"
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

# <a name="performance-considerations-for-traffic-manager"></a>Considerazioni sulle prestazioni per il traffico Manager

Questa pagina vengono illustrati i problemi di prestazioni con il traffico Manager. Considerare le seguenti condizioni:

Si dispone di istanze del sito Web nelle aree WestUS e asiatico. Una delle istanze non riesce la verifica dell'integrità per la ricerca di gestione del traffico. Il traffico di applicazione è indirizzato all'area integro. È previsto il failover ma prestazioni possono essere un problema in base a latenza del traffico in viaggio ora a un'area distante.

## <a name="how-traffic-manager-works"></a>Come funziona il gestore del traffico

L'impatto sulle prestazioni solo contenenti il traffico di gestione del sito Web è la ricerca DNS iniziale. Una richiesta DNS per il nome del proprio profilo di gestore del traffico viene gestita dal server DNS Microsoft principale che ospita la zona trafficmanager.net. Gestore del traffico popola e periodicamente aggiornato, il server DNS Microsoft radice in base al criterio di gestione di traffico e i risultati di ricerca. In modo che anche durante la ricerca DNS iniziale, nessuna query DNS vengono inviata al gestore del traffico.

Gestore del traffico è costituito da diversi componenti: assegnare un nome DNS server, un servizio di API, il livello di archiviazione e un endpoint del servizio di monitoraggio. Se un componente del servizio di gestione di traffico non riesce, non esiste alcun effetto sul nome del DNS associato il profilo di gestore del traffico. I record dei server DNS Microsoft rimangono invariati. Tuttavia, non vengono eseguiti endpoint monitoraggio e aggiornamento di DNS. Di conseguenza, il traffico Manager non è in grado aggiornare il DNS in modo che punti al sito failover quando il sito principale non è raggiungibile.

Risoluzione dei nomi DNS è veloce e risultati sono memorizzati nella cache. La velocità della ricerca DNS iniziale dipende dal server DNS che il client utilizza per la risoluzione dei nomi. In genere, un client può eseguire una ricerca DNS all'interno di ms ~ 50. I risultati della ricerca vengono memorizzati nella cache per la durata del DNS per durata (TTL). Il valore TTL per il traffico Manager predefinito è 300 secondi.

Tramite Gestione il traffico non traffico. Una volta completata la ricerca DNS, il client ha un indirizzo IP per un'istanza del sito web. Il client si connette direttamente all'indirizzo e non vengono passati tramite il traffico Manager. Il criterio di gestione di traffico che scelto non influisce sulle prestazioni DNS. Un metodo routing prestazioni possa compromettere l'esperienza dell'applicazione. Ad esempio, se il criterio reindirizza il traffico da Nord America a un'istanza ospitato in Asia, la latenza di rete per le sessioni potrebbe essere un problema di prestazioni.

## <a name="measuring-traffic-manager-performance"></a>La misurazione delle prestazioni gestore del traffico

Esistono diversi siti Web che è possibile utilizzare per comprendere le prestazioni e il comportamento di un profilo di gestore del traffico. Molti di questi siti sono gratuite anche alcune limitazioni. Alcuni siti offrono il monitoraggio e la segnalazione di una tariffa avanzata.

Gli strumenti di questi siti misura DNS latenza e visualizzare i risolto indirizzi IP per percorsi client nel mondo. La maggior parte di questi strumenti non memorizzare nella cache i risultati DNS. Pertanto, gli strumenti di mostrano la ricerca DNS completa ogni volta che viene eseguito un test. Quando si verifica dal proprio client, si verificano solo le prestazioni di ricerca DNS completa una volta durante la durata TTL.

## <a name="sample-tools-to-measure-dns-performance"></a>Strumenti di esempio per misurare le prestazioni del DNS

- [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS offre diversi strumenti di prestazioni. Lo strumento di confronto DNS consente di visualizzare il tempo impiegato per risolvere il nome del DNS e come che confronta con altri provider di servizi DNS.

- [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Uno degli strumenti più semplici è WebSitePulse. Immettere l'URL per visualizzare il tempo di risoluzione DNS, primo Byte, ultimo Byte e altre statistiche relative alle prestazioni. È possibile scegliere tra tre posizioni di test diverso. In questo esempio, si noterà che la prima esecuzione è indicato che ricerca DNS accetta 0.204 sec.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Perché i risultati vengono memorizzati nella cache, il secondo test per l'endpoint di gestore del traffico stesso la ricerca DNS diventa 0.002 sec.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

- [Autorità di certificazione App sintetici Monitor](https://asm.ca.com/en/checkit.php)

    In precedenza noto come lo strumento di sito Web di controllo Watchmouse, questo sito illustrano il tempo di risoluzione DNS da più aree geografiche contemporaneamente. Immettere l'URL per visualizzare il tempo di risoluzione DNS, momento della connessione e la velocità da diverse aree geografiche. Utilizzare questo test per visualizzare il servizio di hosting viene restituito per diverse posizioni in tutto il mondo.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

- [Pingdom](http://tools.pingdom.com/)

    Questo strumento fornisce statistiche sulle prestazioni per ogni elemento di una pagina web. Scheda pagina analisi viene visualizzata la percentuale di tempo nella ricerca DNS.

- [Che cos'è il DNS?](http://www.whatsmydns.net/)

    Il sito esegue una ricerca DNS 20 origini diverse e i risultati vengono visualizzati su una mappa.

- [Attività Scavare interfaccia Web](http://www.digwebinterface.com)

    Questo sito mostra che ulteriori informazioni DNS nonché un record CNAME. Verificare che controllare il "Colorizza output" e 'Stat' in opzioni e selezionare "All" in server dei nomi.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sui metodi di routing il traffico di gestore del traffico](traffic-manager-routing-methods.md)

[Verificare le impostazioni del gestore del traffico](traffic-manager-testing-settings.md)

[Operazioni su Manager il traffico (riferimento all'API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlet di gestore del traffico Azure](http://go.microsoft.com/fwlink/p/?LinkId=400769)
