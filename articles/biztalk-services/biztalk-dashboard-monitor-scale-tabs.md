<properties 
    pageTitle="Dashboard, Monitor, scala, configurare e connessioni ibrido dei servizi BizTalk | Microsoft Azure" 
    description="Informazioni sui controlli e il monitoraggio delle prestazioni delle schede portale classico per i servizi BizTalk: Dashboard, Monitor, scala, configurare e connessioni ibrido. MABS, WABS" 
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
    ms.date="08/23/2016" 
    ms.author="mandia"/>




# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Esaminare le schede del Dashboard, Monitor, scala, configurare e connessione ibrido

Dopo aver creato il BizTalk Service e distribuire l'applicazione, è possibile modificare alcune impostazioni BizTalk Service e monitorare le prestazioni dell'applicazione. 

Quando si apre il portale di classico Azure, vengono inseriti automaticamente la scheda **Tutti gli elementi** . Per visualizzare il BizTalk Service, selezionare il BizTalk Service nella scheda **Tutti gli elementi** oppure selezionare la scheda **Servizi BIZTALK** ; e quindi selezionare il proprio nome BizTalk Service.

Verrà aperta una nuova finestra con le schede seguenti. In questo argomento descrive queste schede.

## <a name="quick-start-quick-startquickstart"></a>Guida introduttiva (![Guida introduttiva][QuickStart])
A seconda dell'edizione di servizi BizTalk, tutte le opzioni nell'elenco potrebbero non essere disponibile. 
<table border="1">
    <tr>
        <td><strong>Tutti gli strumenti</strong></td>
        <td>Scaricare BizTalk Services SDK per installare i modelli di progetto di Visual Studio nel computer di sviluppo locale. Questi modelli creano i <strong>Servizi BizTalk</strong> (bridge) e i progetti di Visual Studio <strong>Elementi dei servizi BizTalk</strong> (trasformazione) distribuiti nel BizTalk Service.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Come posso iniziare a usare Azure BizTalk Services SDK</a> e <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">l'installazione di Azure BizTalk Services SDK</a> Elenca i passaggi per iniziare.
        </td>
    </tr>
    <tr>
        <td><strong>Creare i contratti di partner</strong></td>
        <td>Apre il portale di servizi BizTalk Azure ospitati in Azure in cui aggiungere partner e creare X12, AS2 e i contratti di EDIFACT EDI.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurazione di componenti per la messaggistica EDI nel portale di servizi BizTalk</a> Elenca i passaggi per iniziare.
        </td>
    </tr>

<tr>
        <td><strong>Ulteriori informazioni sui servizi BizTalk</strong></td>
        <td>Passare al <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">Centro di formazione</a> per ulteriori informazioni sui servizi BizTalk Azure.</td>
</tr>
</table>


Nella barra attività nella parte inferiore, è possibile:

<table border="1">

<tr>
<td><strong>Gestire</strong> la distribuzione dell'applicazione</td>
<td>Apre il portale di servizi di Windows Azure BizTalk. Il portale di servizi BizTalk è entrata alla configurazione EDI, incluse l'aggiunta di partner e creazione di X12, AS2 e i contratti di EDIFACT.
<br/><br/>
Questo è diverso da quello di <strong>accordi partner crea</strong> nella scheda <strong>Quick Start</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurazione di componenti per la messaggistica EDI nel portale di servizi BizTalk</a> fornisce altre informazioni nel portale di servizi di BizTalk.</td>
</tr>

<tr>
<td><strong>Informazioni sulla connessione</strong> di Namespace di controllo accesso</td>
<td>Quando si seleziona informazioni di connessione, vengono visualizzati i Namespace di controllo di accesso, emittente predefinito e chiave predefinita. È possibile copiare questi valori.
<br/><br/>
È anche possibile aprire il portale di controllo di accesso. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Creare un controllo dell'accesso Namespace</a> fornisce altre informazioni nel portale di controllo di accesso.</td>
</tr>

<tr>
<td><strong>Sincronizzare le chiavi</strong> nell'Account di archiviazione</td>
<td>Quando si crea un account di archiviazione, vengono creati automaticamente una chiave primaria e chiave secondaria. Questi tasti di crittografia controllano l'accesso al proprio Account di archiviazione. Il BizTalk Service usa automaticamente la chiave primaria. <strong>Tasti di sincronizzazione</strong> consentono agli utenti per spostarsi tra la chiave primaria e chiave secondaria senza interrompere BizTalk Service.
<br/><br/>
Ad esempio, si desidera BizTalk Service da utilizzare una nuova chiave primaria per l'Account di archiviazione. Per procedere come segue:
<br/><br/>
<ol>
<li>Selezionare il BizTalk Service e selezionare <strong>I tasti di sincronizzazione</strong>. Selezionare la chiave secondaria. Quando si esegue questa operazione, verrà avviato BizTalk Service mediante la chiave secondaria.</li>
<li>Nel portale di classica Azure, selezionare il proprio account di archiviazione e rigenerare la chiave primaria. Tenere presente che il BizTalk Service utilizza la chiave secondaria.</li>
<li>Selezionare il BizTalk Service e selezionare <strong>I tasti di sincronizzazione</strong>. A questo punto, selezionare la chiave primaria. Questa è la nuova chiave primaria rigenerare.</li>
<li>Nel portale di classica Azure, selezionare il proprio account di archiviazione e rigenerare la chiave secondaria.</li>
</ol>
<br/>
Questo processo è denominato "attivazione tasti visualizzati". Lo scopo è per consentire agli utenti per spostarsi tra la chiave primaria e chiave secondaria senza interrompere BizTalk Service.</td>
</tr>

<tr>
<td><strong>Eliminare</strong> l'applicazione</td>
<td>Quando si seleziona eliminare il BizTalk Service e vengono rimossi tutti gli elementi in uso.</td>
</tr>
</table>


## <a name="dashboard"></a>Dashboard
A seconda dell'edizione di servizi BizTalk, tutte le opzioni nell'elenco potrebbero non essere disponibile. 

Quando si seleziona il nome BizTalk Service, viene visualizzata la scheda Dashboard. Nel Dashboard, è possibile:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Panoramica di utilizzo: Mostra il numero di connessioni utilizzate ibrido
Viene visualizzata anche l'utilizzo di dati in GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Grafico metrica: Mostra un elenco fisso misurazione delle prestazioni
Queste metriche forniscono i valori in tempo reale relative allo stato del BizTalk Service. È anche possibile scegliere i valori **relativi** o **assoluti** e dell'intervallo di tempo **dell'intervallo** dei criteri di misurazione che vengono visualizzati nel grafico. 

Per una descrizione di questi indicatori di prestazioni, passare a [Metriche disponibili](#Metrics) in questo argomento.


##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Visualizzazione rapida: Elenca le proprietà BizTalk Service

<table border="1">

<tr>
<td><strong>Aggiornare le credenziali di Database di rilevamento</strong></td>
<td>Viene modificato il nome utente e la password per accedere al Database di rilevamento.</td>
</tr>
<tr>
<td><strong>Aggiornare il certificato SSL</strong></td>
<td>Possibile aggiornare BizTalk Service per l'utilizzo di un certificato SSL diverso. Un certificato SSL autofirmato viene creato automaticamente quando si <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Crea il servizio BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Scaricare certificato</strong></td>
<td>È possibile scaricare il certificato SSL utilizzato per il BizTalk Service a un computer locale.</td>
</tr>
<tr>
<td><strong>Stato</strong></td>
<td>Consente di visualizzare lo stato corrente del BizTalk Service. Vedere <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">servizi BizTalk: diagramma di stato del servizio</a>. </td>
</tr>
<tr>
<td><strong>URL del servizio</strong></td>
<td>L'URL per il servizio BizTalk. Questo è lo stesso come l' <strong>URL del dominio</strong> immesso quando viene creato il BizTalk Service.</td>
</tr>
<tr>
<td><strong>Indirizzo IP pubblico virtuale (VIP)</strong></td>
<td>L'indirizzo IP assegnato per il BizTalk Service. Viene utilizzato per tutti gli endpoint di input e l'indirizzo di origine per il traffico in uscita. L'indirizzo IP appartiene al BizTalk Service come che è stata creata. Se si elimina BizTalk Service, l'indirizzo IP è assegnata a un'altra BizTalk Service.</td>
</tr>
<tr>
<td><strong>Namespace ACS</strong></td>
<td>Autentica con il servizio BizTalk.</td>
</tr>
<tr>
<td><strong>Edition</strong></td>
<td>Elenchi l'edizione immesso quando si crea BizTalk Service.</td>
</tr>
<tr>
<td><strong>Posizione</strong></td>
<td>Consente di visualizzare l'area geografica che ospita i BizTalk Service.</td>
</tr>
<tr>
<td><strong>Creato</strong></td>
<td>Visualizza la data e ora di che creazione BizTalk Service.</td>
</tr>
<tr>
<td><strong>Database beni mobili</strong></td>
<td>Nome Database SQL Azure contenente le tabelle di rilevamento utilizzate per il BizTalk Service. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisiti di spiegazione</a> vengono fornite informazioni dettagliate sul Database di rilevamento.</td>
</tr>
<tr>
<td><strong>Archiviazione di monitoraggio e archiviazione</strong></td>
<td>Il nome dell'account Azure lo spazio di archiviazione che archivia l'output di monitoraggio del BizTalk Service.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Requisiti di spiegazione</a> fornisce dettagli sull'account di archiviazione.</td>
</tr>
<tr>
<td><strong>Nome dell'abbonamento</strong></td>
<td>Elenca l'abbonamento che ospita i BizTalk Service. L'abbonamento regola l'accesso al portale di classica Azure.</td>
</tr>
<tr>
<td><strong>ID abbonamento</strong></td>
<td>Dopo avere creata una sottoscrizione, viene generato automaticamente un ID abbonamento. Quando si utilizzano API REST, potrebbe essere necessario immettere l'ID di sottoscrizione.</td>
</tr>
</table>

[Servizi BizTalk: portale classica di Provisioning con Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280) viene illustrata la procedura per creare un BizTalk Service.


##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Gestire le informazioni di connessione, le chiavi di sincronizzazione ed eliminare nella barra delle attività:

<table border="1">

<tr>
<td><strong>Gestire</strong> la distribuzione dell'applicazione</td>
<td>Apre il portale di servizi BizTalk Azure. Il portale di servizi BizTalk è entrata alla configurazione EDI, incluse l'aggiunta di partner e creazione di X12, AS2 e i contratti di EDIFACT.
<br/><br/>
Questo è diverso da quello di <strong>accordi partner crea</strong> nella scheda <strong>Quick Start</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurazione di componenti per la messaggistica EDI nel portale di servizi BizTalk</a> fornisce altre informazioni nel portale di servizi di BizTalk.</td>
</tr>
<tr>
<td><strong>Informazioni sulla connessione</strong> di Namespace di controllo accesso</td>
<td>Visualizza Namespace controllo di accesso, emittente predefiniti e valori predefiniti della chiave; che possono essere copiati.
<br/><br/>
È anche possibile aprire il portale di controllo di accesso. Il portale di controllo di accesso è diverso da quello di usare l'opzione di Active Directory nel riquadro di spostamento sinistro.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gestione di ACS Namespace</a> fornisce altre informazioni nel portale di controllo di accesso.</td>
</tr>
<tr>
<td><strong>Sincronizzare le chiavi</strong> nell'Account di archiviazione</td>
<td>Quando si crea un account di archiviazione, vengono creati automaticamente una chiave primaria e chiave secondaria. Questi tasti di crittografia controllano l'accesso al proprio Account di archiviazione. Il BizTalk Service usa automaticamente la chiave primaria. <strong>Tasti di sincronizzazione</strong> consentono agli utenti per spostarsi tra la chiave primaria e chiave secondaria senza interrompere BizTalk Service.
<br/><br/>
Ad esempio, si desidera BizTalk Service da utilizzare una nuova chiave primaria per l'Account di archiviazione. Per procedere come segue:
<br/><br/>
<ol>
<li>Selezionare il BizTalk Service e selezionare <strong>I tasti di sincronizzazione</strong>. Selezionare la chiave secondaria. Quando si esegue questa operazione, verrà avviato BizTalk Service mediante la chiave secondaria.</li>
<li>Nel portale di classica Azure, selezionare il proprio account di archiviazione e rigenerare la chiave primaria. Tenere presente che il BizTalk Service utilizza la chiave secondaria.</li>
<li>Selezionare il BizTalk Service e selezionare <strong>I tasti di sincronizzazione</strong>. A questo punto, selezionare la chiave primaria. Questa è la nuova chiave primaria rigenerare.</li>
<li>Nel portale di classica Azure, selezionare il proprio account di archiviazione e rigenerare la chiave secondaria.</li>
</ol>
<br/>
Questo processo è denominato "attivazione tasti visualizzati". Lo scopo è per consentire agli utenti per spostarsi tra la chiave primaria e chiave secondaria senza interrompere BizTalk Service.</td>
</tr>

<tr>
<td><strong>Eliminare</strong> l'applicazione</td>
<td>Il BizTalk Service e tutti gli elementi in uso verranno rimossi.</td>
</tr>
</table>


## <a name="monitor"></a>Monitor
Non è valida per l'edizione gratuita.

Quando si seleziona il nome BizTalk Service, scheda Monitor è disponibile e viene visualizzato quanto segue:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Grafico metrica: Visualizza le metriche prestazioni selezionata
Queste metriche forniscono i valori in tempo reale relativo stato di integrità di BizTalk Service. Scegliere quali criteri di misurazione vengono visualizzati. Un massimo di sei misurazione delle prestazioni può essere visualizzato contemporaneamente. 

È anche possibile scegliere i valori **relativi** o **assoluti** e dell'intervallo di tempo **dell'intervallo** dei criteri di misurazione vengono visualizzate. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Per rimuovere o visualizzare metriche nel grafico:
1. Selezionare la scheda **Monitor** .
2. Selezionare **Aggiungi metriche** nella barra delle attività:  
![Selezionare Aggiungi metriche][AddMetrics]
3. Verificare le metriche di prestazioni che si desidera visualizzare.
4. Selezionare il segno di spunta per tornare alla scheda **Monitor** .
5. Selezionare il cerchio accanto a unità di misura metriche per visualizzare il valore di metrica nel grafico.  

    Ad esempio, l'unità di misura metriche di **Utilizzo della CPU** è inattivo; l'output non viene visualizzata nel grafico:  
![Unità di misura metriche utilizzo della CPU è disattivato][GrayedMetric]  

    Selezionare il disabilitata fuori cerchio per abilitare l'unità di misura metriche **Utilizzo della CPU** visualizzare l'output del grafico:  
![Unità di misura metriche utilizzo della CPU è abilitata][EnabledMetric]

6. Per rimuovere una metrica dal grafico visualizzato e l'elenco, selezionare **Elimina metrica** nella barra delle applicazioni. Per aggiungere indietro metrica all'elenco, selezionare **Aggiungi metriche** nella barra delle applicazioni, selezionare l'unità di misura metriche e selezionare il segno di spunta per tornare alla scheda **Monitor** . Selezionare il grigio fuori cerchio per abilitare l'unità di misura metriche.

## <a name="Metrics"></a>Statistiche disponibili
Prestazioni contatori/quelle riportate di seguito sono disponibili:

<table border="1">

<tr>
<td><strong>Latenza RountdTrip</strong></td>
<td>Visualizza tempo medio in millisecondi (ms) per l'elaborazione di un messaggio dal momento in cui che si riceve il messaggio fino a quando il messaggio viene elaborato completamente da BizTalk Service in Bridge tutti. Vengono contati solo i messaggi elaborati correttamente.<br/><br/>
Quando si verificano gli eventi seguenti, viene creato un timestamp:
<ul>
<li>Il gateway viene inserito un messaggio</li>
<li>Messaggio viene indirizzato alla destinazione</li>
<li>Viene ricevuta risposta di destinazione</li>
<li>Risposta di conferma destinazione inviata al gateway</li>
</ul>
<br/>
Questa metrica Mostra il risultato del calcolo seguente:
<br/><br/>
[Destinazione conferma risposta inviata al gateway] - [messaggio viene inserito il gateway]</td>
</tr>
<tr>
<td><strong>Errori di origine</strong></td>
<td>Visualizza il numero totale di messaggi che non è riuscita, BizTalk Service quando il pull e i messaggi provenienti da endpoint di origine.</td>
</tr>
<tr>
<td><strong>Utilizzo della CPU</strong></td>
<td>Elenchi di % tempo processore Media di tutte le istanze di ruolo.</td>
</tr>
<tr>
<td><strong>Latenza di elaborazione</strong></td>
<td>Visualizza il tempo medio In millisecondi (ms) per l'elaborazione di un messaggio da BizTalk Service in tutti i ponti, escluso il tempo trascorso in destinazioni. Vengono contati solo i messaggi elaborati correttamente.<br/><br/>
Quando si verificano tutti gli eventi seguenti, viene creato un timestamp:

<ul>
<li>Il gateway viene inserito un messaggio</li>
<li>Messaggio viene indirizzato alla destinazione</li>
<li>Viene ricevuta risposta di destinazione</li>
<li>Risposta di conferma di destinazione inviata al gateway</li>
</ul>
<br/>Questa metrica Mostra il risultato del calcolo seguente:<br/><br/>
[Destinazione conferma risposta inviata al gateway] - [messaggio viene inserito il gateway] - [destinazione risposte] + [messaggio indirizzato alla destinazione]</td>
</tr>
<tr>
<td><strong>Errori di processo</strong></td>
<td>Visualizza il numero totale di messaggi che non è riuscita durante l'elaborazione da BizTalk Service in tutti i ponti all'interno di un intervallo di tempo.</td>
</tr>
<tr>
<td><strong>Messaggi inviati</strong></td>
<td>Visualizza il numero totale di messaggi inviati dal BizTalk Service attraverso ponti tutti all'interno di un intervallo di tempo. Questa metrica viene incrementata quando un messaggio inviato da una pipeline raggiunge la destinazione del percorso. Questa metrica non indica che un messaggio sia elaborato correttamente.<br/><br/>
In uno scenario di risposta convocazione, l'unità di misura metriche viene incrementato quando route destinazione invia una conferma di ricezione alla pipeline.</td>
</tr>
<tr>
<td><strong>Messaggi ricevuti</strong></td>
<td>Visualizza il numero totale di messaggi ricevuti dal BizTalk Service attraverso ponti tutti all'interno di un intervallo di tempo. Questa metrica viene incrementata quando si riceve un nuovo messaggio dalla pipeline di.</td>
</tr>
<tr>
<td><strong>Messaggi di processo</strong></td>
<td>Visualizza il numero totale di messaggi attualmente elaborati da BizTalk Service all'interno di un intervallo di tempo.</td>
</tr>
<tr>
<td><strong>Messaggi elaborati</strong></td>
<td>Visualizza il numero totale di messaggi correttamente elaborati da BizTalk Service attraverso ponti tutti all'interno di un intervallo di tempo. Questa metrica viene incrementata quando un messaggio viene ricevuto dalla pipeline di instradati correttamente alla destinazione.</td>
</tr>
</table>


## <a name="scale"></a>Scala
Nella scheda scala, è possibile aggiungere o sottrarre il numero di unità utilizzato per il BizTalk Service. Per impostazione predefinita, esiste una unità configurata. Unità aggiuntive possono essere aggiunti per ridimensionare il BizTalk Service. Quando si aumenta la scala, aumenta la velocità di trasmissione. La quantità di risorse aumenta anche, tra cui ponti distribuiti, contratti, le connessioni Line e la potenza di elaborazione. Ad esempio, si aumenta la scala da 1 unità a 2 unità. In questo caso, è possibile distribuire doppio del numero di ponti, doppio i contratti, doppio connessioni Line ed doppio la potenza di elaborazione.

Alcune versioni di BizTalk non offrono un'opzione scala. In questo caso, un'unità è consentita. Per determinare il numero di unità è possibile ridimensionare l'edizione, fare riferimento a [servizi BizTalk: edizioni grafico](biztalk-editions-feature-chart.md).

Aumento del numero di unità potrebbe avere un impatto prezzi. Se si aumenta l'unità, selezionando **Salva** viene visualizzato un messaggio che indica se la fatturazione è interessata. Quindi possibile scegliere di continuare. Quando si aumenta il numero di unità, le modifiche di stato BizTalk Service da attivo a aggiornamento. Nello stato aggiornamento il BizTalk Service continua a funzionare.

[Servizi BizTalk: edizioni grafico](biztalk-editions-feature-chart.md) definisce "Unità".


## <a name="configure"></a>Configurare
Non si applica alle connessioni ibrida.

Imposta lo stato di Backup su Nessuno o automatico. Se è impostato su Nessuno, non vengono creati automaticamente. Se è impostato su automatico, configurare il percorso di backup, la frequenza di backup e il periodo di tempo per mantenere i file di backup. 

[Servizi BizTalk: Backup e ripristino](biztalk-backup-restore.md) fornisce i dettagli. 


## <a name="HybridConnections"></a>Connessioni ibrido
Un'applicazione di Azure, ad esempio Web Apps o App Mobile nel servizio App Azure ibrido connessioni a una risorsa locale che utilizza una porta TCP statica, ad esempio SQL Server, MySQL, API Web HTTP e la maggior parte dei servizi Web personalizzati. Connessioni ibrido vengono gestite in servizi BizTalk nel portale di classica Azure.

Per creare connessioni ibrido in Azure App servizio, vedere [accesso locale risorse con le connessioni ibrido in Azure App servizio](../app-service-web/web-sites-hybrid-connection-get-started.md).

Per creare o gestire le connessioni ibrido in servizi di BizTalk Windows Azure, vedere [Distribuzione ibrida connessioni](integration-hybrid-connection-overview.md).



## <a name="next"></a>Successivo
Ora che si ha familiarità con le schede diverse, per ulteriori informazioni sulle funzionalità di servizi di Windows Azure BizTalk:

- [Servizi BizTalk: limitazione](biztalk-throttling-thresholds.md)  
- [Servizi di BizTalk: Nome autorità che emette e chiave](biztalk-issuer-name-issuer-key.md)  
- [Servizi di BizTalk: Eseguire il Backup e ripristino](biztalk-backup-restore.md)

## <a name="see-also"></a>Vedere anche
- [Connessioni ibrido](integration-hybrid-connection-overview.md)  
- [Servizi di BizTalk: Sviluppo, Basic, Standard e Premium edizioni grafico](biztalk-editions-feature-chart.md)  
- [Servizi BizTalk: Portale classica di Azure utilizzando il Provisioning](biztalk-provision-services.md)  
- [BizTalk servizi: Grafico di stato del servizio BizTalk](biztalk-service-state-chart.md)  
- [Come posso iniziare a usare Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 
