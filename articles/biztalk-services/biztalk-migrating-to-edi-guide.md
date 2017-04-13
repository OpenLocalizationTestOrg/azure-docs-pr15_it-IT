<properties   
    pageTitle="La migrazione di soluzioni EDI BizTalk Server a BizTalk Services Guide tecniche | Microsoft Azure"
    description="Eseguire la migrazione EDI a MABS; Servizi di BizTalk Microsoft Azure"
    services="biztalk-services"
    documentationCenter="na"
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags 
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Eseguire la migrazione di soluzioni EDI BizTalk Server ai servizi BizTalk: Guida tecnica

Autore: Tim Wieman e Nitin Mehrotra

Revisori: Karthik Bharthy

Utilizzando: Microsoft Azure BizTalk Services: febbraio 2014 rilasciare.

## <a name="introduction"></a>Introduzione

EDI Electronic Data Interchange () corrisponde a uno degli strumenti più diffusi per i dati di exchange imprese in formato elettronico, detto anche come transazioni Business-to-Business o B2B. BizTalk Server ha supporto EDI per su dieci anni, rispetto alla versione server BizTalk iniziale. Con i servizi BizTalk, Microsoft continua il supporto per soluzioni EDI della piattaforma Microsoft Azure. Le transazioni B2B prevalentemente sono esterne all'organizzazione e quindi è facile implementare se è stata implementata una piattaforma cloud. Microsoft Azure fornisce questa funzionalità tramite i servizi BizTalk.

Durante alcuni clienti osservare servizi BizTalk come piattaforma "quale" per le nuove soluzioni EDI, molti clienti che attuali soluzioni BizTalk Server EDI che desiderano eseguire la migrazione a Azure. Poiché BizTalk servizi EDI è progettato in base alle stesse entità chiave degli architettura di BizTalk Server EDI (trading partner, entità, accordi), è possibile eseguire la migrazione di elementi di BizTalk Server EDI ai servizi BizTalk.

In questo documento vengono illustrate alcune delle differenze coinvolti nella migrazione elementi BizTalk Server EDI ai servizi BizTalk. In questo documento presuppone una conoscenza di elaborazione BizTalk Server EDI e Trading accordi Partner. Per ulteriori informazioni sull'EDI BizTalk Server, vedere [Trading Partner Management Using BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>La versione di elementi di BizTalk Server EDI può essere migrata ai servizi BizTalk?

Il modulo BizTalk Server EDI in modo significativo è stato migliorato per BizTalk Server 2010, quando è stata la rimodellazione degli includere partner, profili e contratti. Servizi BizTalk utilizza lo stesso modello per organizzare il partner e divisioni aziendali all'interno di tali partner. Di conseguenza, la migrazione di elementi EDI da BizTalk Server 2010 e versioni successive a BizTalk Services, è un processo molto più diretto. Per eseguire la migrazione gli elementi EDI associati a versioni precedenti a BizTalk Server 2010, è necessario prima eseguire l'aggiornamento a BizTalk Server 2010 e quindi eseguire la migrazione del elementi EDI BizTalk Services.

## <a name="scenariosmessage-flow"></a>Flusso di scenari/messaggi

Come BizTalk Server EDI l'elaborazione di servizi BizTalk è basata su una soluzione Trading Partner Management TPM (). Soluzione TPM è i seguenti componenti chiave:

- Partner, che rappresenta l'organizzazione in una transazione B2B.
- Profili, che rappresentano divisioni all'interno di un partner commerciale.
- Trading accordi partner (o accordi), che rappresentano il contratto di business tra due partner/profili.

Nella figura seguente viene illustrato analogie, nonché le differenze tra una soluzione BizTalk Server EDI e soluzione BizTalk servizi EDI:

![][EDImessageflow]

Le principali differenze e analogie tra un flusso di soluzione EDI in BizTalk Server e servizi BizTalk sono:

- Nello stesso modo in cui BizTalk Server utilizza una pipeline EDIReceive per ricevere un messaggio EDI e una pipeline EDISend per inviare un messaggio EDI, servizi BizTalk utilizza un bridge di trasmissione EDI per ricevere e bridge EDI Invia per inviare messaggi EDI. In BizTalk Server, le pipeline sono associate a un accordo con Invia o porte di ricezione. Servizi BizTalk, il contratto stesso indica invia o riceve bridge.
- In BizTalk Server dopo la pipeline EDIReceive elabora il messaggio EDI, il messaggio viene eseguito a un database di SQL Server. La pipeline EdiSend quindi recupera il messaggio da un database SQL Server, viene elaborato e quindi lo distribuisce al partner commerciale.

    Servizi BizTalk, dopo il EDI ricevuto bridge elabora il messaggio EDI, indirizza il messaggio a un processo esterno. Il processo esterno potrebbe essere in esecuzione in Microsoft Azure o locale. Il processo esterno deve inviare il messaggio al bridge invia EDI; bridge invia non estrae progettato per il messaggio. Dopo l'elaborazione del messaggio, il bridge invia EDI indirizza il messaggio per il partner commerciale.

Servizi BizTalk offre un'esperienza di configurazione da utilizzare per creare e distribuire un contratto B2B tra partner senza la configurazione di qualsiasi calcolo di Microsoft Azure istanze (ruoli Web o lavoro), qualsiasi database SQL di Microsoft Azure o tutti gli account di archiviazione di Microsoft Azure rapidamente. Scenari più complessi richiederà bloccare in flussi di lavoro o altre attività di elaborazione di servizio "intorno ai bordi" di un contratto Trading Partner, vale a dire prima o dopo l'elaborazione di bridge Trading EDI contratto per i Partner. In modo dettagliato, la sequenza di eventi seguente si verifica durante un messaggio EDI l'elaborazione di BizTalk Services.

1. Viene ricevuto un messaggio EDI dal partner, Fabrikam commerciale.  Per ricevere i messaggi EDI dal partner, servizi BizTalk supporta protocolli di trasporto, ad esempio FTP, SFTP, AS2 e HTTP/S.

2. L'elaborazione di ricevere lato contratto per i partner commerciali separa messaggio EDI in formato XML.  È possibile inviare il messaggio EDI disassemblato (in formato XML) all'endpoint servizio Bus, ad esempio un endpoint del servizio Bus inoltro, servizio Bus argomento, servizio Bus coda o un bridge di servizi BizTalk.

3. I messaggi XML disassemblati potrebbero quindi ricevuti da endpoint per un'ulteriore elaborazione personalizzata.  Questi endpoint possono essere elaborati, ad esempio da un componente locale o un'istanza di Microsoft Azure calcolare per elaborare ulteriormente il messaggio in un servizio Windows Workflow (WF) o Windows Communication Foundation (WCF).

4. "Elaborazione lato Invia" del partner commerciale contratto quindi assemblare messaggio XML in formato EDI e invia a partner commerciale, Contoso.  Per inviare messaggi EDI partner, servizi BizTalk supporta i protocolli di quelli utilizzati per la ricezione di messaggi EDI.

Ulteriormente questo documento vengono fornite indicazioni concettuale su alcuni dei diversi elementi di BizTalk Server EDI esecuzione della migrazione a servizi BizTalk.

## <a name="sendreceive-ports-to-trading-partners"></a>Invio/ricezione porte per partner

In BizTalk Server è impostare gli indirizzi di ricezione e porte di ricezione di ricevere messaggi EDI/XML dal partner e configurare le porte Invia per inviare messaggi EDI/XML a partner commerciale. È quindi possibile collegare le seguenti porte a un contratto per i partner commerciali utilizzando la console di amministrazione BizTalk Server. Servizi di BizTalk, le posizioni in cui si ricevono messaggi da partner commerciali e da cui inviare messaggi a partner commerciali sono configurati come parte del contratto di partner commerciali, stesso (come parte delle impostazioni di trasporto) nel portale servizi BizTalk.  Pertanto non è importante il concetto di "Invia porte" e "gli indirizzi di ricezione", per sé BizTalk Services. Per ulteriori informazioni, vedere [Creazione di contratti](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Pipeline (ponti)

In BizTalk Server EDI, tubazioni sono entità di elaborazione messaggio che possono essere inclusi anche la logica personalizzata per le funzionalità di elaborazione specifiche, come richiesto dall'applicazione. Per i servizi BizTalk, l'equivalente sarebbe un bridge EDI. Tuttavia servizi BizTalk, per il momento ponti EDI sono "chiuso".  Attività personalizzate, ovvero non è possibile aggiungere a un bridge EDI. Prima o dopo il messaggio viene ricevuto bridge configurato come parte del contratto di Trading Partner, è necessario eseguire qualsiasi elaborazione personalizzata all'esterno del sito EDI dell'applicazione. Ponti EAI offerta la possibilità di elaborazione personalizzata. Se si vuole elaborazione personalizzata, è possibile utilizzare ponti EAI prima o dopo il messaggio viene elaborato da bridge EDI. Per ulteriori informazioni, vedere [come includere codice personalizzato in Bridge](https://msdn.microsoft.com/library/azure/dn232389.aspx).

È possibile inserire un flusso di pubblicazione/sottoscrizione con codice personalizzato e/o con Bus di servizio di messaggistica code e gli argomenti della prima il contratto di partner commerciali riceve il messaggio o dopo il contratto elabora il messaggio e indirizza a un endpoint del servizio Bus.

Vedere **Flusso di scenari/messaggi** in questo argomento per il modello di flusso del messaggio.

## <a name="agreements"></a>Contratti

Se si ha familiarità con BizTalk Server 2010 Assistente Partner contratti usato per l'elaborazione di EDI, servizi BizTalk trading accordi partner aspetto molto familiari. La maggior parte delle impostazioni contratto sono le stesse e utilizzare la stessa terminologia. In alcuni casi, le impostazioni del contratto sono molto più semplici rispetto alle stesse impostazioni BizTalk Server. Servizi di Microsoft Azure BizTalk supporta X12, EDIFACT e AS2 trasporto.

Servizi di Microsoft Azure BizTalk vengono forniti anche uno strumento di **Migrazione di dati TPM** per eseguire la migrazione commerciali partner e contratti dal modulo entrambe Server Trading parti al portale di servizi BizTalk. Lo strumento di migrazione dei dati TPM è disponibile come parte di un pacchetto di strumenti, che può essere scaricato dal [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). Il pacchetto include anche un file Leggimi che fornisce istruzioni su come usare lo strumento e informazioni sulla risoluzione dei problemi di base per lo strumento.

## <a name="schemas"></a>Schemi

Servizi BizTalk fornisce schemi EDI che possono essere utilizzati in soluzioni BizTalk Services.  Inoltre, schemi BizTalk Server EDI utilizzabile anche con i servizi BizTalk perché il nodo principale dello schema EDI stesso tra BizTalk Server, nonché servizi BizTalk. In questo modo, sarà possibile eseguire gli schemi BizTalk Server EDI direttamente e usarle soluzioni EDI sviluppati mediante BizTalk Services. È anche possibile scaricare gli schemi da [SDK MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Mappe (trasformazioni)

Mappe in BizTalk Server sono denominate trasformazioni BizTalk Services. Migrazione di mappe da BizTalk Server ai servizi BizTalk potrebbero essere una delle operazioni più complesse per ottenere (a seconda della complessità della mappa). Lo strumento di mappatura utilizzato per i servizi BizTalk è diverso dal mapping BizTalk. Anche se il mapping degli prevalentemente simile, il formato della mappa sottostante è diverso. Anche i modelli di trasformazione (chiamato **Mappa operazioni** di BizTalk Services) disponibili per gli utenti sono diversi.  In effetti, è possibile utilizzare direttamente una mappa BizTalk in BizTalk Services. Inoltre, non tutti i modelli di trasformazione disponibili in BizTalk Server sono disponibili come operazioni di mappa in BizTalk Services.

### <a name="new-transform-operations"></a>Operazioni di trasformazione dei nuovi

Elenco operazioni di trasformazione mappa disponibili può sembrare piuttosto diverso dal mapping BizTalk Server, BizTalk Services trasforma sono disponibili nuove modalità per eseguire le stesse attività. Ad esempio, BizTalk Services trasforma hanno **Operazioni elenco** disponibile. Questa operazione non è disponibile nella mappatura BizTalk.  **Elenco operazioni** consentono di creare e utilizzare un "elenco", in un elenco è un set di elementi (noto anche come "righe") e ogni elemento può avere più membri (noto anche come "colonne").  È possibile ordinare l'elenco, selezionare gli elementi in base a una condizione e così via.

Un altro esempio di nuove funzionalità di BizTalk Services trasforma sono le **Operazioni di ciclo**.  È difficile creare cicli annidati nella mappatura BizTalk Server.  In questo modo, le operazioni di mappa ciclo vengono aggiunte per i servizi BizTalk Trasforma.

Ancora un altro esempio è l'operazione di mappa espressione **If-Then-Else** .  Eseguire un'operazione if-then-else è stato possibile nella mappatura BizTalk, ma è necessario più modelli di trasformazione da eseguire per completare un'attività apparentemente semplice.

### <a name="migrating-biztalk-server-maps"></a>Migrazione BizTalk Server mappe

Microsoft Azure BizTalk Services offre uno strumento per eseguire la migrazione BizTalk Server esegue il mapping a servizi BizTalk trasformazioni. **BTMMigrationTool** è disponibile come parte del pacchetto di **Strumenti** disponibili in [SDK dei servizi BizTalk scaricare](http://go.microsoft.com/fwlink/p/?LinkId=235057). Per ulteriori informazioni sullo strumento, vedere [conversione di una mappa BizTalk trasformare una BizTalk Services](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

È anche possibile visualizzare un campione da Sandro Pereira, MVP BizTalk, su come [eseguire la migrazione di mappe BizTalk Server per i servizi BizTalk trasformazioni](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orchestrazioni

Se è necessario eseguire la migrazione di orchestrazione BizTalk Server di Microsoft Azure, orchestrazioni dovrà essere sostituito perché Microsoft Azure non supporta orchestrazioni BizTalk Server in esecuzione.  Impossibile riscrittura funzionalità orchestrazione in un servizio di Windows Workflow Foundation 4.0 (WF4).  Poiché non è attualmente non migrazione da orchestrazioni BizTalk Server a WF4 dovrebbe essere una riscrittura completa. Ecco alcune risorse utili per flusso di lavoro di Windows:

- Informazioni su [*come integrare un servizio di flusso di lavoro WCF con code Bus di servizio e gli argomenti*](https://msdn.microsoft.com/library/azure/hh709041.aspx) da Paolo Salvatori. 

- [Sessione di *creazione di applicazioni in Windows Workflow Foundation e Azure* ](http://go.microsoft.com/fwlink/p/?LinkId=237314) dalla conferenza compilazione 2011.

- [*Centro per sviluppatori di Windows Workflow Foundation*](http://go.microsoft.com/fwlink/p/?LinkId=237315) su MSDN.

- [*Documentazione di Windows Workflow Foundation 4 (WF4)*](https://msdn.microsoft.com/library/dd489441.aspx) su MSDN.

## <a name="other-considerations"></a>Altre considerazioni

Ecco alcune considerazioni che è necessario eseguire durante l'uso di servizi BizTalk.

### <a name="fallback-agreements"></a>Contratti di fallback

L'elaborazione BizTalk Server EDI ha il concetto di "Fallback contratti".  BizTalk Services esegue **non** esiste il concetto di Fallback accordo finora.  Vedere la documentazione argomenti BizTalk [Ruolo dei contratti EDI elaborazione](http://go.microsoft.com/fwlink/p/?LinkId=237317) e [la configurazione globali o proprietà del contratto di Fallback](https://msdn.microsoft.com/library/bb245981.aspx) per informazioni sull'utilizzo di Fallback accordi BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>Distribuzione a più destinazioni

Ponti servizi BizTalk, nello stato corrente non supporta il routing dei messaggi a più destinazioni utilizzando la pubblicazione-sottoscrizione modello. Se, tuttavia si potrebbero indirizzare i messaggi provenienti da un bridge BizTalk servizi a un argomento Bus di servizio che possono quindi hanno più abbonamenti al punto più finale visualizzato il messaggio.

## <a name="conclusion"></a>Conclusioni

Servizi di Microsoft Azure BizTalk viene aggiornato in normali attività cardine per aggiungere altre caratteristiche e funzionalità. Ogni aggiornamento, ci auguriamo alle funzionalità avanzate per semplificare la creazione di soluzioni-to-end con BizTalk Services e altre tecnologie di Azure di supporto.

## <a name="see-also"></a>Vedere anche

[Sviluppo di applicazioni aziendali con Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
