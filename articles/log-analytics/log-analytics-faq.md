<properties
    pageTitle="Accedere Analitica domande frequenti su | Microsoft Azure"
    description="Risposte alle domande più frequenti relative al servizio Log Analitica."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="log-analytics-faq"></a>Registro Analitica domande frequenti

Questo FAQ Microsoft è riportato un elenco di domande frequenti su Analitica Log in Microsoft operazioni di gestione famiglia di prodotti (OMS). Se hai altre domande sulle Log Analitica, passare al [forum di discussione](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e rivolgere le domande. Un utente della community consente di ottenere le risposte. Se una domanda più frequenti, si verrà aggiungerla a questo articolo in modo che possano essere trovato in modo semplice e rapido.

## <a name="general"></a>Generale

**D. quali controlli vengono eseguiti da Active Directory e le soluzioni di valutazione di SQL?**

RISPOSTE. La query seguente mostra la descrizione di tutti i controlli di esecuzione di:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Quindi, è possibile esportare i risultati in Excel per rivedere.

* *Q: perché si vede il qualcosa di diverso da quello *OMS* in SCOM Administration? * *

R: in base quali aggiornamento cumulativo di SCOM è attiva, è possibile trovare un nodo per *System Center Advisor*, *Approfondimenti operative*o *Log Analitica*.

L'aggiornamento di stringa di testo a *OMS* è incluso in un management pack, che deve essere importato manualmente. Seguire le istruzioni nell'articolo SCOM aggiornamento cumulativo KB più recenti e aggiornare la console OMS per visualizzare gli aggiornamenti più recenti nel nodo *OMS* .

* *D: È previsto un *locale* versione di OMS? * *

R: No. Registro Analitica elabora e memorizza molto grandi quantità di dati. Come un servizio cloud, in grado di scalabilità se necessario ed evitare qualsiasi impatto sulle prestazioni all'ambiente di Log Analitica.

Inoltre, da un mezzo di servizio cloud non è necessario mantenere l'infrastruttura di Log Analitica e in esecuzione e può ricevere aggiornamenti caratteristica frequenti e le correzioni.

## <a name="configuration"></a>Configurazione
**D. è possibile modificare il nome del contenitore tabella/blob utilizzato per la lettura da Azure diagnostica (tampone)?**  

RISPOSTE.  No, questo attualmente non è possibile, ma è pianificato per una versione futura.

**D: cosa indirizzi come l'uso di servizi OMS? Come assicurarsi che il firewall consente solo il traffico ai servizi OMS?**  

RISPOSTE. Il servizio Analitica Log viene creato nella parte superiore di Azure e i punti finali ricevono indirizzi IP che sono in [Intervalli IP di Microsoft Azure Data Center](http://www.microsoft.com/download/details.aspx?id=41653).

Quando vengono apportate distribuzioni servizio, gli indirizzi IP effettivi dei servizi OMS cambiano. I nomi DNS per consentire attraverso il firewall vengono riportati in [configurare le impostazioni proxy e firewall nel registro Analitica](log-analytics-proxy-firewall.md).

**D utilizzo ExpressRoute per la connessione a Azure. Traffico Log Analitica userà la connessione ExpressRoute?**  

RISPOSTE. Nella [documentazione ExpressRoute](./expressroute/expressroute-faqs.md#supported-services)sono descritti i diversi tipi di traffico ExpressRoute.

Il traffico al Log Analitica utilizza il circuito di ExpressRoute peering pubblico.

**D. è disponibile un modo semplice e semplice per spostare un'area di lavoro esistente Analitica Log in un'altra sottoscrizione di un'area di lavoro/Azure Analitica Log?**  Abbiamo OMS aree di lavoro del cliente diverse che si stava test ed eseguito la sottoscrizione Azure e sono lo spostamento di produzione in modo che si desidera spostarli nel proprio abbonamento Azure/OMS.  

RISPOSTE. Il `Move-AzureRmResource` cmdlet consente di spostare un'area di lavoro Log Analitica, nonché un account di automazione da una sottoscrizione di Azure a altro. Per ulteriori informazioni, vedere [AzureRmResource Sposta](http://msdn.microsoft.com/library/mt652516.aspx).

Questa modifica può essere eseguita anche nel portale di Azure.

Impossibile spostare dati da un'area di lavoro Analitica Log o modificare l'area dati Analitica Log vengono archiviati in.

**D: come si aggiunge OMS a SCOM?**

R: aggiornamento per l'ultimo aggiornamento cumulativo e importazione di management pack consentono a cui connettersi SCOM Log Analitica.

Si noti che la connessione SCOM Analitica Log è disponibile solo per SCOM 2012 SP1 e versioni successive.

**D: come è possibile confermare che un agente in grado di comunicare con i Log Analitica?**

R: per assicurarsi che l'agente può comunicare OMS, passare a: pannello di controllo, sicurezza e le impostazioni, **Agente di monitoraggio di Microsoft**.

Nella scheda **Azure Log Analitica (OMS)** , cercare un segno di spunta verde. Un'icona di segno di spunta verde conferma che l'agente è possibile comunicare con il servizio Outlook Mobile.

Un'icona di avviso giallo indica che l'agente si verifica problemi di comunicazione con OMS. Uno dei motivi comuni è il servizio agente di monitoraggio di Microsoft è stata interrotta e deve essere riavviato.

**D: come è possibile interrompere un agente di comunicare con i Log Analitica?**

R: In SCOM, rimuovere il computer dall'elenco gestito OMS. Questa operazione si arresta tutte le comunicazioni tra SCOM per tale agente. Per gli agenti connessi al OMS direttamente, è possibile impedire loro di comunicare con OMS tramite: pannello di controllo, sicurezza e le impostazioni, **Agente di monitoraggio di Microsoft**.
In **Azure Log Analitica (OMS)**, rimuovere tutte le aree di lavoro nell'elenco.

## <a name="agent-data"></a>Dati dell'agente

**D. come quantità di dati che è possibile inviare tramite l'agente a Log Analitica? Esiste una maggior quantità di dati per ogni cliente?**  
RISPOSTE. Il piano libero imposta una terminazione giornaliera di 500 MB per ogni area di lavoro. I piani standard e premium non presentano limiti sulla quantità di dati che viene caricati. Come un servizio cloud, Analitica Log in OMS progettato per passare automaticamente handle del volume provenienti da un cliente, anche se si tratta terabyte al giorno.

Agente di Log Analitica è stata progettata per garantire ha un ingombro ridotto e compressione alcuni dati di base. Uno dei propri clienti scritti un blog su vengono effettuati contro il nostro agente e come si sono rimasti. Il volume di dati dipenderà le soluzioni consente ai clienti. È possibile trovare informazioni dettagliate sul volume di dati e visualizzare la suddivisione dalla soluzione in **uso** affiancato nella pagina Panoramica OMS.

Per ulteriori informazioni, è possibile leggere un [cliente blog](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) su ingombro ridotto dell'agente OMS.

**D. come banda di rete viene utilizzato da Microsoft Management agente (MMA) quando si inviano dati a Log Analitica?**

RISPOSTE. Larghezza di banda costituisce una funzione per la quantità di dati inviati. Dati viene compresso durante l'invio in rete.

**D. come quantità di dati che viene inviato per ciascun agente?**

RISPOSTE. Ciò dipende in gran parte:

- le soluzioni che è stata attivata
- il numero di contatori raccolti e registri
- il volume di dati nei log

Il livello di prezzo gratuito è un ottimo modo per incorporata server e indicatore del volume di dati in genere diverse. In generale l'uso verrà visualizzata nella pagina **utilizzo** .
Per i computer che sono in grado di eseguire l'agente di WireData, è possibile visualizzare la quantità di dati viene inviato tramite la query seguente:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva a Analitica Log](log-analytics-get-started.md) per informazioni su Analitica Log e get funzionanti in minuti.
