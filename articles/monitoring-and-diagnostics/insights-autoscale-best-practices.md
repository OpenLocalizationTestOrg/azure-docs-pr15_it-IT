<properties
    pageTitle="Procedure consigliate per il ridimensionamento automatico Azure Monitor. | Microsoft Azure"
    description="Informazioni su principi per utilizzare in modo efficiente il ridimensionamento automatico in Azure Monitor."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="best-practices-for-azure-monitor-autoscaling"></a>Procedure consigliate per il ridimensionamento automatico Monitor Azure

Nelle sezioni seguenti sono in questo documento utili per comprendere le procedure consigliate per Azure di autoscale. Dopo aver esaminato queste informazioni, sarà più possibile per l'utilizzo efficiente autoscale dell'infrastruttura di Azure.

## <a name="autoscale-concepts"></a>Concetti AutoScale

- Una risorsa può avere solo ** un'impostazione autoscale
- Un'impostazione autoscale può avere una o più profili e ogni profilo possono avere una o più regole autoscale.
- Un'impostazione autoscale scale istanze orizzontalmente, ovvero *le* aumentando *le istanze e* riducendo il numero di istanze.
 Un'impostazione autoscale ha un massimo, minimo e valore predefinito delle istanze.
- Un processo autoscale legge sempre la metrica associata scalare per controllare se ha incrociate soglia configurata per scalabilità orizzontale o scala in. È possibile visualizzare un elenco di metriche che autoscale possibile scalare di al [Monitor di Azure il ridimensionamento automatico comuni metriche](insights-autoscale-common-metrics.md).
- Le soglie vengono calcolate un livello di istanza. Ad esempio, "scala in uscita da 1 istanza quando calcolare la media di CPU > 80% quando conteggio delle istanze 2", significa scalabilità quando la CPU Media in tutte le istanze è superiore all'80%.
- Sempre possibile ricevere notifiche di errore tramite posta elettronica. In particolare, il proprietario, collaboratori e utilità per la lettura della risorsa di destinazione ricevere posta elettronica. Un messaggio di posta elettronica *ripristino* viene sempre visualizzato quando autoscale recupera da un errore e viene avviata funziona correttamente.
- È possibile scegliere aggiuntivo per ricevere una notifica di azione scala completato tramite posta elettronica e webhooks.

## <a name="autoscale-best-practices"></a>Procedure consigliate AutoScale

Usare le procedure consigliate seguenti quando si usa autoscale.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Assicurarsi che i valori minimi e massimo sono diversi e dispongono di un margine adeguato tra di esse
Se si dispone di un'impostazione che contiene i valori minimo = 2, massimo = 2 e corrente conteggio delle istanze è 2, non può verificarsi alcuna azione scala. Mantenere un margine adeguato tra i conteggi istanza minimo e massimo, sono inclusi. AutoScale scale sempre tra questi limiti.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Adattamento manuale viene ripristinato da max e min autoscale
Se si aggiorna manualmente il conteggio delle istanze a un valore sopra o sotto il valore massimo, il motore autoscale ridimensionato automaticamente al minimo (se sotto) o il numero massimo (se in precedenza). Ad esempio, impostare l'intervallo tra 3 e 6. Se si dispone di una sola istanza in esecuzione, il motore autoscale scala a 3 istanze la successiva esecuzione. Allo stesso modo, preferisce scala aggiuntivo 8 varianti di nuovo in 6 la successiva esecuzione.  Ridimensionamento manuale è molto temporaneo a meno che non è reimpostare anche le regole autoscale.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Usa sempre una combinazione di regola scalabilità e scala aggiuntivo che esegue un aumento e riduzione
Se si utilizza solo una parte della combinazione, autoscale scala aggiuntivo che è possibile applicare singola out o, finché il valore massimo o minimo, viene raggiunta.

### <a name="do-not-switch-between-the-azure-portal-and-the-azure-classic-portal-when-managing-autoscale"></a>Non alternare il portale di Azure e il portale classico Azure quando si gestiscono Autoscale
Per i servizi Cloud e servizi App (applicazioni Web), utilizzare il portale Azure (portal.azure.com) per creare e gestire le impostazioni di autoscale. Per i set di scala macchina virtuale usare PoSH, CLI o API REST per creare e gestire le impostazioni di autoscale. Non passare al portale classico Azure (manage.windowsazure.com) e il portale Azure (portal.azure.com) quando si gestiscono le configurazioni autoscale. Il portale classico Azure e il relativo back-end sottostante presenta limitazioni. Passare al portale di Azure per gestire autoscale mediante un'interfaccia utente grafica. Le opzioni sono usare autoscale PowerShell, CLI o API REST (tramite Esplora risorse Azure).

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Scegliere la statistica appropriata per l'unità di misura di diagnostica
Per le metriche di diagnostica, è possibile scegliere tra *Media*, *minimo*, *massimo* e *totale* come metrica scalare da. La statistica più comune è *Media*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Scegliere le soglie con attenzione per tutti i tipi di metriche
È consigliabile scegliere con attenzione soglie diverse per scalabilità e scala aggiuntivo in base a situazioni pratiche.

È *consigliabile non* autoscale impostazioni come gli esempi riportati di seguito con i valori soglia uguali o molto simili per ritirare e condizioni:

- Aumentare le istanze di 1 contare quando conteggio Thread < = 600
- Ridurre le istanze di 1 contare quando conteggio Thread > = 600

Esaminiamo un esempio di cosa può comportare un'operazione che può sembrare confusione. Cosider la sequenza di seguito.

1. Si supponga esistono istanze di 2 per iniziare e quindi il numero medio di thread per ogni istanza aumenta a 625.
2. AutoScale scale all'aggiunta di un'istanza di 3 °.
3. Si supponga che il numero di thread Media in istanze rientri a 575.
4. Prima di ridimensionamento verso il basso, autoscale tentativi per stimare stato finale sarà se ridimensionata. Ad esempio, 575 x 3 (conteggio corrente delle istanze) = 1,725 / 2 (numero finale delle istanze quando ridotta) = 862.5 thread. Questo significa autoscale avrebbero dovuto immediatamente scalabilità nuovamente anche dopo che architetture, se il numero di thread medio rimane inalterato o persino rientra solo una piccola quantità. Tuttavia, se architetture di nuovo l'intero processo ripetere, causando un ciclo infinito.
5. Per evitare questa situazione (indicata "Ali"), autoscale non scalare verso il basso tutto. Se, tuttavia, Ignora e Rivaluta la condizione nuovamente la volta successiva che viene eseguito il processo del servizio. Questa operazione potrebbe confondere molte persone perché autoscale non sembra funzionare quando il numero di thread Media è stata 575.

Stima durante il scala è destinata a evitare situazioni "flappy". Questo comportamento da tenere in considerazione quando si sceglie la stessa soglia per scalabilità e.

È consigliabile scegliere un margine adeguato tra le proporzioni e le soglie. Ad esempio, prendere in considerazione la seguente combinazione regola migliore.

- Aumentare le istanze di 1 contare quando % CPU > = 80
- Ridurre le istanze di 1 contare quando % CPU < = 60

In questo caso  

1. Si supponga che sono disponibili per iniziare con 2 istanze.
2. Se la percentuale di CPU Media nelle varie istanze pari a 80, autoscale scale all'aggiunta di una terza istanza.
3. Si supponga che nel tempo la percentuale di CPU rientri a 60.
4. Regola di scala dell'AutoScale stima lo stato finale se fosse in scala. Ad esempio 60 x 3 (conteggio corrente delle istanze) = 180 / 2 (numero finale delle istanze quando ridotta) = 90. In modo autoscale non scala aggiuntivo perché avrebbe dovuto scalabilità nuovamente immediatamente. Se, tuttavia, ignora il ridimensionamento in basso.
5. I controlli successiva autoscale di tempo, la CPU continua a compresi su 50. Restituisce una stima nuovamente - 50 x 3 istanza = 150 / 2 istanze = 75; è inferiore al limite di scalabilità di 80, in modo che viene modificato in scala correttamente a 2 istanze.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Considerazioni per la scala di valori soglia per metriche speciali
 Per le metriche speciali, ad esempio lo spazio di archiviazione o servizio Bus coda unità di misura metriche lunghezza, la soglia è il numero medio di messaggi disponibili in base al numero corrente delle istanze. Scegliere con attenzione scegliere il valore di soglia per la metrica.

Di seguito illustrato con un esempio per assicurarsi di comprendere il comportamento migliore.

- Aumentare le istanze di 1 al numero di messaggi coda lo spazio di archiviazione > = 50
- Ridurre le istanze di 1 al numero di messaggi di spazio di archiviazione coda < = 10

Considerare la sequenza di seguito:

1. Esistono 2 istanze di coda di spazio di archiviazione.
2. Continuare a essere recapitata messaggi e revisionando coda lo spazio di archiviazione totale legge 50. Si potrebbe presuppongono che autoscale dovrebbe iniziare un'azione scalabilità. Tuttavia, si noti che è ancora 50/2 = 25 messaggi per ogni istanza. Pertanto, scalabilità non è disponibile. Per la prima scalabilità verificarsi, il numero totale dei messaggi nella coda di spazio di archiviazione deve essere 100.
3. Si supponga che il numero totale dei messaggi raggiunge 100.
4. A causa di un'azione scalabilità viene aggiunta un'istanza di coda lo spazio di archiviazione 3 °.  L'azione successiva scalabilità non si verificherà fino a quando il numero totale dei messaggi nella coda raggiunge 150 perché 150/3 = 50.
5. A questo punto, il numero di messaggi nella coda vengono ridotte. Con 3 istanze, la prima azione di scala accade quando il totale dei messaggi in tutte le code aggiungere fino a 30 perché 30/3 = 10 messaggi per ogni istanza, ossia la soglia di scala.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Considerazioni per il ridimensionamento quando in un'impostazione autoscale sono configurati più profili

In un'impostazione autoscale, è possibile scegliere un profilo predefinito, viene applicato sempre senza nella pianificazione o un'ora, oppure è possibile scegliere un profilo ricorrente o un profilo per un periodo fisso con un intervallo di data e ora.

Quando servizio autoscale li elabora, controlla sempre nell'ordine seguente:

1. Profilo data fisso
2. Profilo ricorrente
3. ("Sempre") profilo predefinito

Se viene soddisfatta una condizione di profilo, autoscale non controlla la condizione profilo successiva sotto di esso. AutoScale elabora solo un profilo alla volta. Di conseguenza, che se si desidera includere anche una condizione di elaborazione da un profilo di livello inferiore, è necessario includere anche le regole nel profilo corrente.

Esaminiamo questo utilizzando un esempio:

Nell'immagine seguente mostra un'impostazione autoscale con un profilo predefinito delle istanze minime = 2 e massime istanze = 10. In questo esempio, le regole sono configurate per scalabilità quando il numero di messaggi nella coda è maggiore di 10 e scala aggiuntivo quando il numero di messaggi nella coda è minore di 3. A questo punto la risorsa può scalare tra 2 e 10 istanze.

Inoltre, non esiste un profilo ricorrente per lunedì. Viene impostato per istanze minime = 2 e massime istanze = 12. Di conseguenza, lunedì, il primo autoscale ora consente di verifica questa condizione, se il conteggio delle istanze è 2, viene modificato in scala per il nuovo valore minimo di 3. Come autoscale continua a questa condizione di profilo di trovare una corrispondenza (lunedì), elabora solo la CPU regole basate su scala esterna/aggiuntivo configurate per il profilo. Al momento non controlla la lunghezza di coda. Tuttavia, se si desidera anche la condizione di lunghezza coda da verificare, è necessario includere tali regole dal profilo predefinito anche nel profilo di lunedì.

Analogamente, quando autoscale torna al profilo predefinito, verifica innanzitutto se vengono soddisfatte le condizioni minime e massime. Se il numero di istanze al momento è 12, viene modificato in scala a 10, il valore massimo consentito per il profilo predefinito.

![impostazioni AutoScale](./media/insights-autoscale-best-practices/insights-autoscale-best-practices.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Considerazioni per il ridimensionamento quando più regole sono configurate in un profilo
Esistono casi in cui è necessario impostare più regole in un profilo. Le seguenti regole autoscale vengono utilizzati per i servizi quando più regole sono impostate.

Nella *scalabilità*autoscale viene eseguito se viene soddisfatta una regola.
*Scala aggiuntivo*, autoscale richiedono tutte le regole che venga rispettata.

Per illustrare, si supponga di disporre le seguenti regole autoscale 4:

- Se CPU < 30%, scala aggiuntivo da 1
- Se memoria < 50%, scala aggiuntivo da 1
- Se CPU > 75%, scalabilità da 1
- Se memoria > 75% scalabilità da 1

Si verifica il seguente:

- Se la CPU è 76% e della memoria è 50%, abbiamo scalabilità.
- Se CPU 50% e della memoria 76% è scalabilità.

Mano, se CPU 25% e della memoria 51% autoscale non **non** scala aggiuntivo. Per poter scala in CPU deve essere % 29 e della memoria 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Selezionare sempre un conteggio delle istanze predefinito attendibili
Il conteggio delle istanze predefinito è importante autoscale scale il servizio per tale conteggio quando metriche non sono disponibili. Pertanto, selezionare un conteggio delle istanze predefinito sicuro per i carichi di lavoro.

### <a name="configure-autoscale-notifications"></a>Configurare le notifiche autoscale
AutoScale informa gli amministratori e i collaboratori della risorsa tramite posta elettronica se si verifica una delle condizioni seguenti:

- servizio AutoScale ha esito negativo eseguire un'azione.
- Metrica non è disponibile per il servizio autoscale prendere una decisione scala.
- Sono disponibile (ripristino) per prendere una decisione scala.
Oltre alle condizioni sopra, è possibile configurare le notifiche di posta elettronica o webhook per ricevere una notifica per le azioni scala esito negativo.
