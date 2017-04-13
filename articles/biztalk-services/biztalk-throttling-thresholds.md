<properties 
    pageTitle="Informazioni sulle limitazione in servizi BizTalk | Microsoft Azure" 
    description="Informazioni sulla limitazione soglie e risultante runtime comportamenti per i servizi BizTalk. Limitazione dipende l'utilizzo di memoria e il numero di messaggi. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
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





# <a name="biztalk-services-throttling"></a>Servizi BizTalk: limitazione

Azure BizTalk Services implementa servizio limitazione in base a due condizioni: utilizzo della memoria e il numero di messaggi simultanei di elaborazione. In questo argomento sono elencati i limiti limitazioni e viene descritto il comportamento di Runtime quando si verifica una condizione di limitazione.

## <a name="throttling-thresholds"></a>Soglie di limitazione

Nella tabella seguente sono elencate le soglie la limitazione di origine:

||Descrizione|Soglia inferiore|Soglia superiore|
|---|---|---|---|
|Memoria|% del totale del sistema memoria disponibile/PageFileBytes. <p><p>Totale PageFileBytes disponibile è circa 2 volte la RAM del sistema.|60%|70%|
|Elaborazione dei messaggi|Numero di messaggi di elaborazione contemporaneamente|40 * numero di core|100 * numero di core|

Quando viene raggiunta la soglia, servizi di Windows Azure BizTalk inizia a limitare. Limitazione si arresta quando viene raggiunta la soglia inferiore. Ad esempio, il servizio utilizza memoria 65%. In questo caso, il servizio non limitare. Il servizio ha inizio con memoria 70%. In questo caso, il servizio limita e continua limitare fino a quando il servizio utilizza la memoria di sistema di 60% (soglia inferiore).

Azure BizTalk Services tiene traccia lo stato di limitazione (stato normale e stato accelerato) e la durata di limitazione.


## <a name="runtime-behavior"></a>Comportamento di runtime

Quando servizi BizTalk Azure passa allo stato di limitazione, si verifica quanto segue:

- Limitazione è per ogni istanza del ruolo. Per esempio:<br/>
Limita RoleInstanceA. RoleInstanceB non è la limitazione. In questo caso, i messaggi in RoleInstanceB vengono elaborati come previsto. I messaggi in RoleInstanceA vengono eliminati e non riuscire con l'errore seguente:<br/><br/>
**Server è occupato. Riprovare.**<br/><br/>
- Tutte le origini pull non sondaggio o scaricare un messaggio. Per esempio:<br/>
Una pipeline estrae i messaggi da un'origine esterna FTP. L'istanza di ruolo eseguendo il pull ottiene in stato di limitazione. In questo caso, il gadget pipeline di interrompe il download dei messaggi aggiuntivi fino alla fine dell'istanza di ruolo limitazione.
- Viene inviata una risposta al client in modo che il client può inviare di nuovo il messaggio.
- È necessario attendere fino a ottenere la limitazione risolta. In particolare, è necessario attendere fino a raggiunta la soglia inferiore.

## <a name="important-notes"></a>Note importanti
- Limitazione non può essere disabilitata.
- Limitazione soglie non possono essere modificata.
- Limitazione è implementata a livello di sistema.
- Server di Database SQL Azure è installato anche limitazione incorporato.

## <a name="additional-azure-biztalk-services-topics"></a>Altri argomenti relativi ai servizi BizTalk Azure

-  [Installazione dei servizi di BizTalk Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Esercitazioni: Servizi di Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Come posso iniziare a usare Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Servizi BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Vedere anche
- [Servizi di BizTalk: Sviluppo, Basic, Standard e Premium edizioni grafico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servizi BizTalk: Portale classica di Azure utilizzando il Provisioning](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk servizi: Grafico di stato di Provisioning](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk Services: Schede del Dashboard, monitorare e scala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servizi di BizTalk: Eseguire il Backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk Services: Nome autorità che emette e chiave](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 
