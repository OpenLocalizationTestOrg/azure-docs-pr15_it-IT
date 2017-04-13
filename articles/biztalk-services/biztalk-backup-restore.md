<properties 
    pageTitle="Creare e ripristinare una copia di backup in servizi BizTalk | Microsoft Azure" 
    description="Servizi BizTalk include Backup e ripristino. Informazioni su come creare e ripristinare una copia di backup e determinare quali viene eseguito il backup. MABS, WABS" 
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


# <a name="biztalk-services-backup-and-restore"></a>Servizi di BizTalk: Eseguire il Backup e ripristino

Azure BizTalk Services include funzionalità di Backup e ripristino. In questo argomento viene descritto come eseguire il backup e ripristinare i servizi BizTalk tramite il portale classico Azure.

È anche possibile eseguire il backup BizTalk Services utilizzando l' [API REST di servizi BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584). 

> [AZURE.NOTE] Connessioni ibrido non il backup, indipendentemente dall'edizione. È necessario ricreare le connessioni ibrida.

## <a name="before-you-begin"></a>Prima di iniziare

- Eseguire il backup e ripristino potrebbe non essere disponibile per tutte le edizioni. Vedere [servizi BizTalk: edizioni grafico](biztalk-editions-feature-chart.md).

- Tramite il portale classico Azure, è possibile creare una copia di backup su richiesta o creare un backup pianificato. 

- Backup contenuto può essere ripristinato alla stessa BizTalk Service o a un nuovo BizTalk Service. Per ripristinare BizTalk Service utilizzando lo stesso nome, è necessario eliminare esistente BizTalk Service e il nome deve essere disponibile. Dopo aver eliminato un BizTalk Service, può richiedere più tempo del desiderato per lo stesso nome sia disponibile. Se non è possibile attendere lo stesso nome sia disponibile, quindi ripristinare in un nuovo BizTalk Service.

- Servizi BizTalk possono essere ripristinati la stessa edizione o un'edizione superiore. Ripristinare una versione inferiore servizi BizTalk, da quando è stato eseguito il backup, non è supportata.

    Ad esempio, una copia di backup con l'edizione di base può essere ripristinato per la versione Premium. Non è possibile ripristinare un backup con la versione Premium a Standard Edition.

- I numeri di controllo EDI backup per mantenere continuità dei numeri di controllo. Se i messaggi vengono elaborati dopo l'ultimo backup, ripristino contenuto backup può causare i numeri di controllo duplicati.

- Se un batch contiene messaggi attivi, elaborare il batch di **prima di** eseguire una copia di backup. Quando si crea una copia di backup (come necessari o programmato), i messaggi in batch mai sono archiviati. 

    **Se viene accettata una copia di backup con messaggi attivi in un batch, questi messaggi non è stato eseguito il backup e pertanto andranno persi.**

- Facoltativo: Nel portale di servizi BizTalk interrompere le operazioni di gestione.


## <a name="create-a-backup"></a>Creare una copia di backup

Una copia di backup in qualsiasi momento è possibile effettuare e completamente controllata dall'utente. In questa sezione viene illustrata la procedura per creare i backup tramite il portale classico Azure, tra cui:

[In backup richiesta](#backupnow)

[Pianificare un backup](#backupschedule)

#### <a name="backupnow"></a>In backup richiesta
1. Nel portale di Azure classico selezionare **Servizi BizTalk**e quindi selezionare BizTalk Service che si desidera eseguire il backup.
2. Nella scheda **Dashboard** , selezionare **il backup** nella parte inferiore della pagina.
3. Immettere un nome di backup. Ad esempio, immettere *myBizTalkService*IP*Data*.
4. Scegliere un account di archiviazione blob e selezionare il segno di spunta per avviare il backup.

Una volta completata la copia di backup, viene creato un contenitore con il nome del backup che immesso nella finestra account di archiviazione. In questo contenitore include la configurazione di backup BizTalk Service.

#### <a name="backupschedule"></a>Pianificare un backup

1. Nel portale di Azure classico selezionare **Servizi BizTalk**, selezionare il nome di BizTalk Service che si desidera pianificare il backup e quindi selezionare la scheda **Configura** .
2. Impostare lo **stato di Backup** **automatico**. 
3. Selezionare l' **Account di archiviazione** per archiviare il backup, immettere la **frequenza** di creazione di backup e la durata di mantenere i backup (**Giorni di conservazione**):

    ![][AutomaticBU]

    **Note**   
    - In **Giorni di conservazione**, il periodo di conservazione deve essere maggiore di frequenza di backup.
    - Selezionare **Mantieni sempre almeno un backup**, anche se si tratta oltre il periodo di conservazione.
    

4. Selezionare **Salva**.


Quando viene eseguito un processo di backup pianificato, viene creato un contenitore (per l'archiviazione dei dati di backup) nell'account di archiviazione che è stata immessa. Il nome del contenitore denominato *servizio BizTalk nome-formato data-ora*. 

Se il dashboard BizTalk Service mostra uno stato **non riuscito** :

![Ultimo pianificato stato backup][BackupStatus] 

Il collegamento viene aperto i registri di operazione gestione dei servizi per semplificare la soluzione. Vedere [servizi BizTalk: risolvere i problemi di utilizzo dei registri operazione](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## <a name="restore"></a>Ripristinare

È possibile ripristinare i backup dal portale di classica Azure o da [Ripristinare API REST BizTalk servizio](http://go.microsoft.com/fwlink/p/?LinkID=325582). In questa sezione viene illustrata la procedura per ripristinare tramite il portale classico.

#### <a name="before-restoring-a-backup"></a>Prima di ripristinare una copia di backup

- Nuova verifica, l'archiviazione e monitoraggio archivi possono essere immesse durante il ripristino BizTalk Service.

- Gli stessi dati EDI Runtime vengono ripristinati. Il backup di Runtime EDI archivia i numeri di controllo. I numeri di controllo ripristinato sono in sequenza dall'ora del backup. Se i messaggi vengono elaborati dopo l'ultimo backup, ripristino contenuto backup può causare i numeri di controllo duplicati.

#### <a name="restore-a-backup"></a>Ripristinare una copia di backup

1. Nel portale di Azure classico selezionare **Nuovo** > **Servizi App** > **Servizio BizTalk** > **ripristinare**:

    ![Ripristinare una copia di backup][Restore]

2. **URL di Backup**, selezionare l'icona di cartella ed espandere l'account di archiviazione Azure contenente il backup di configurazione BizTalk Service. Espandere il contenitore e nel riquadro destro selezionare il file con estensione txt di backup corrispondente. 
<br/><br/>
Scegliere **Apri**.

3. Nella pagina **Servizio BizTalk ripristinare** immettere un **Nome di servizio BizTalk** e verificare **l'URL del dominio**, **Edition**e **Opzioni internazionali** per ripristinato BizTalk Service. **Creare una nuova istanza di database SQL** per il database di verifica:

    ![][RestoreBizTalkService]

    Selezionare la freccia avanti.

4.  Verificare il nome del database SQL, immettere il server fisico in cui verrà creato il database SQL e un nome utente e password per il server.


    Se si desidera configurare l'edizione di database SQL, dimensioni e altre proprietà, selezionare **Configura impostazioni di Database avanzate**. 

    Selezionare la freccia avanti.

5. Creare un nuovo account di archiviazione o immettere un account di archiviazione esistente per BizTalk Service.

7. Selezionare il segno di spunta per avviare il ripristino.

Dopo il ripristino viene completato correttamente, un nuovo BizTalk Service viene elencato in uno stato di sospensione nella pagina servizi BizTalk nel portale di classica Azure.



### <a name="postrestore"></a>Dopo il ripristino di una copia di backup

BizTalk Service sempre vengono ripristinata in uno stato **sospeso** . In questo stato, è possibile apportare modifiche alla configurazione prima che il nuovo ambiente sia funzionale, tra cui:

- Se si creano le applicazioni di BizTalk Service con Azure BizTalk Services SDK, si potrebbe necessario aggiornare le credenziali di accesso controllo (ACS) in queste applicazioni per l'uso con l'ambiente ripristinato.

- Ripristinare un BizTalk Service per replicare un ambiente BizTalk Service esistente. In questo caso, se sono presenti contratti configurati nel portale di servizi BizTalk originale che utilizzano una cartella FTP di origine, si potrebbe essere necessario aggiornare i contratti nell'ambiente di appena ripristinato per usare una cartella FTP di origine diversi. In caso contrario, potrebbe essere due diversi contratti si tenta di estrarre lo stesso messaggio.

- Se è stato ripristinato per dispone di più ambienti BizTalk Service, assicurarsi che la destinazione è l'ambiente corretto nelle applicazioni Visual Studio, i cmdlet di PowerShell, API REST o Trading API OM di gestione dei Partner.

- È buona norma configurare backup automatici sull'ambiente BizTalk Service appena ripristinato.

Per avviare BizTalk Service nel portale di classica Azure, selezionare ripristinato BizTalk Service e quindi **curriculum** nella barra delle applicazioni. 



## <a name="what-gets-backed-up"></a>Cosa viene eseguito il backup

Quando si crea una copia di backup, gli elementi seguenti backup:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Eseguire il backup di elementi</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portale di servizi BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Configurazione e Runtime</td> 
<td>
<ul>
<li>Dettagli profilo e partner</li>
<li>Accordi partner</li>
<li>Assembly personalizzati distribuito</li>
<li>Ponti distribuiti</li>
<li>Certificati</li>
<li>Trasformazioni distribuite</li>
<li>Pipeline</li>
<li>Modelli creato e salvato nel portale servizi BizTalk</li>
<li>X12 i mapping ST01 e GS01</li>
<li>Numeri di controllo (EDI)</li>
<li>Valori AS2 messaggio microfono</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Servizio BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Certificato SSL</td> 
<td>
<ul>
<li>Dati del certificato SSL</li>
<li>Password del certificato SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Impostazioni del servizio BizTalk</td> 
<td>
<ul>
<li>Numero di unità di scala</li>
<li>Edition</li>
<li>Versione del prodotto</li>
<li>Regione/Data Center</li>
<li>Chiave e dello spazio dei nomi di access Control Service (ACS)</li>
<li>Stringa di connessione di database di verifica</li>
<li>Stringa di connessione di archiviazione archiviazione account</li>
<li>Stringa di connessione di monitoraggio dello spazio di archiviazione account</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Altri elementi</strong></td>
</tr> 
<tr>
<td>Database beni mobili</td> 
<td>Quando viene creata BizTalk Service, i dettagli di Database di rilevamento vengono immessi, tra cui il Server di Database SQL Azure e il nome del Database di verifica. Il Database di rilevamento non è automaticamente backup.
<br/><br/>
<strong>Importante</strong><br/>
Se viene eliminato il Database di rilevamento e ripristino le esigenze di database deve esistere una copia di backup precedente. Se non esiste una copia di backup, il Database di rilevamento e i relativi dati non sono recuperabili. In questo caso, creare un nuovo Database verifica con lo stesso nome del database. È consigliabile replica geografico.</td>
</tr> 
</table>

## <a name="next"></a>Successivo

Per creare servizi BizTalk Azure nel portale di classica Azure, passare a [servizi BizTalk: portale classica il Provisioning di Azure utilizzando](http://go.microsoft.com/fwlink/p/?LinkID=302280). Per avviare la creazione di applicazioni, passare a [Servizi BizTalk di Windows Azure](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Vedere anche
- [Servizio BizTalk backup](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Ripristinare il servizio di BizTalk dal Backup](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Servizi di BizTalk: Sviluppo, Basic, Standard e Premium edizioni grafico](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Servizi BizTalk: Portale classica di Azure utilizzando il Provisioning](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [BizTalk servizi: Grafico di stato di Provisioning](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk Services: Schede del Dashboard, monitorare e scala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk Services: Nome autorità che emette e chiave](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Come posso iniziare a usare Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 
