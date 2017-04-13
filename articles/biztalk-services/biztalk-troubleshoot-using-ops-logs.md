<properties 
    pageTitle="Risolvere i problemi di servizi di BizTalk utilizzando registri operazioni | Microsoft Azure" 
    description="Risolvere i problemi di utilizzo dei registri operazione BizTalk Services. MABS, WABS" 
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


# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>Servizi BizTalk: Risolvere i problemi di utilizzo dei registri operazione

## <a name="what-are-the-operation-logs"></a>Quali sono i registri di operazione
Registri operazioni è una funzionalità di gestione dei servizi disponibile nel portale di classica Azure che consente di visualizzare i registri nella cronologia delle operazioni eseguite sui servizi Azure, inclusi quelli BizTalk. In questo modo è possibile visualizzare i dati cronologici relativi alle operazioni di gestione nell'abbonamento a BizTalk Service fino alla versione di 180 giorni.

> [AZURE.NOTE]Questa caratteristica acquisisce solo i registri per le operazioni di gestione su BizTalk Services, ad esempio quando è stato avviato il servizio, sottoposta verso l'alto, e così via. Tali operazioni vengono rilevate indipendentemente dal fatto che vengono eseguiti dal portale di classica Azure o tramite le [API REST servizio BizTalk](http://msdn.microsoft.com/library/azure/dn232347.aspx). Per un elenco completo delle operazioni che vengono rilevate tramite servizi di gestione, vedere [Operazioni rilevate tramite Gestione dei servizi di Windows Azure](#bizops).<br/><br/>
Questa operazione non acquisire i registri per attività correlate al runtime BizTalk Service (ad esempio messaggio elaborato dal ponti e così via). Per visualizzare questi file di log, utilizzare la visualizzazione di verifica dal portale di servizi BizTalk. Per ulteriori informazioni, vedere [Gestione dei messaggi](http://msdn.microsoft.com/library/azure/hh949805.aspx).

## <a name="view-biztalk-services-operation-logs"></a>Visualizzazione BizTalk Services registri operazioni
1. Nel portale di Azure classico selezionare **Servizi di gestione**e quindi selezionare la scheda **Registri operazioni** .
2. È possibile filtrare i log in base ai parametri diversi come abbonamento, intervallo di date, il tipo di servizio (ad esempio BizTalk Services), il nome del servizio o lo stato dell'operazione (completata, non riuscito).
3. Selezionare il segno di spunta per visualizzare l'elenco filtrato. L'immagine seguente illustra le attività correlate a testbiztalkservice:  ![visualizzare registri operazioni][ViewLogs] 
4. Per visualizzare più su un'operazione specifica, selezionare la riga e fare clic su **Dettagli** nella barra attività nella parte inferiore.


## <a name="bizops"></a>Operazioni revisioni con servizi di gestione di Azure
Nella tabella seguente sono elencate le operazioni che vengono rilevate uso dei servizi di gestione di Azure:

Nome dell'operazione | Attività
--- | ---
CreateBizTalkService | Operazione per creare un nuovo BizTalk Service
DeleteBizTalkService | Operazione di eliminazione BizTalk Service
RestartBizTalkService | Operazione per riavviare BizTalk Service
StartBizTalkService | Operazione per avviare BizTalk Service
StopBizTalkService | Operazione interrompere BizTalk Service
DisableBizTalkService | Operazione per disabilitare BizTalk Service
EnableBizTalkService | Operazione da abilitare BizTalk Service
BackupBizTalkService | Operazione da eseguire il backup BizTalk Service
RestoreBizTalkService | Ripristinare un BizTalk Service dal backup specificato
SuspendBizTalkService | Operazione per sospendere BizTalk Service
ResumeBizTalkService | Operazione da riprendere BizTalk Service
ScaleBizTalkService | Operazione scalare BizTalk Service verso l'alto o verso il basso
ConfigUpdateBizTalkService | Operazione per aggiornare la configurazione di BizTalk Service
ServiceUpdateBizTalkService | Operazione da eseguire l'aggiornamento o eseguire il downgrade un BizTalk Service a una versione diversa
PurgeBackupBizTalkService | Operazione da eliminare backup di BizTalk Service di fuori del periodo di conservazione


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

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 
