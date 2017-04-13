<properties 
    pageTitle="Attività consentite in diversi stati o stati in servizi BizTalk | Microsoft Azure" 
    description="Le azioni/operazioni consentite in stato MABS diverso: interrompere, avviare, riavviare, sospendere, riprendere, eliminare, ridimensionare, aggiornare la configurazione e supporto" 
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



# <a name="biztalk-services-service-state-chart"></a>BizTalk Services: Diagramma di stato del servizio
A seconda lo stato corrente del servizio di BizTalk, esistono operazioni che è possibile o non è possibile eseguire il servizio di BizTalk.

Ad esempio, si effettua il provisioning di un nuovo servizio BizTalk nel portale di classica Azure. Quando è stata completata correttamente, il servizio BizTalk è stato attivo. Quando è attiva, è possibile interrompere il servizio BizTalk. Se Interrompi ha esito positivo, il servizio BizTalk passa a uno stato interrotto. Se Interrompi non riesce, il servizio BizTalk passa a uno stato StopFailed. Stato StopFailed, è possibile riavviare il servizio BizTalk. Se si tenta di un'operazione non è consentita, ad esempio curriculum servizio BizTalk il seguente errore si verifica:

**Operazione non consentita**

Per eseguire il provisioning BizTalk Service, passare a [servizi BizTalk: portale classica il Provisioning di Azure utilizzando](http://go.microsoft.com/fwlink/p/?LinkID=302280).

Le tabelle seguenti elencano le operazioni o le azioni che possono essere completate quando BizTalk Service si trova in uno stato specifico. Un ✔ indica che l'operazione è consentita nello stato. Uno spazio vuoto indica che non è possibile eseguire l'operazione nello stato.

## <a name="start-stop-restart-suspend-resume-and-delete-operations"></a>Iniziare, interrompere, riavviare, sospendere, riprendere e le operazioni di eliminazione
<table border="1">
<tr>
        <th colspan="15">Operazione o azione</th>
</tr>

<tr>
        <th rowspan="18">Stato del servizio BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Inizio</th>
        <th>Interrompi</th>
        <th>Riavviare</th>
        <th>Sospensione</th>
        <th>Curriculum</th>
        <th>Elimina</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Attiva</b></td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Disattivato</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Sospesa</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Arrestato</b></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Aggiornamento del servizio non è riuscito</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
</table>
<br/>

## <a name="scale-update-configuration-and-backup-operations"></a>Scala, configurazione di aggiornamento e le operazioni di Backup
<table border="1">
<tr>
        <th colspan="15">Operazione o azione</th>
</tr>

<tr>
        <th rowspan="18">Stato del servizio BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Scala</th>
        <th>Configurazione di aggiornamento</th>
        <th>Copia di backup</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Attiva</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Disattivato</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Sospesa</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Arrestato</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Aggiornamento del servizio non è riuscito</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## <a name="see-also"></a>Vedere anche
- [Servizi BizTalk: Portale classica di Azure utilizzando il Provisioning](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk Services: Schede del Dashboard, monitorare e scala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servizi di BizTalk: Sviluppo, Basic, Standard e Premium edizioni grafico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servizi di BizTalk: Eseguire il Backup e ripristino](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servizi BizTalk: limitazione](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk Services: Nome autorità che emette e chiave](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Come posso iniziare a usare Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)


 
