<properties
    pageTitle="Ridimensionare conteggio delle istanze manualmente o automaticamente | Microsoft Azure"
    description="Informazioni su come ridurre i servizi Azure."
    authors="rboucher"
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
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="scale-instance-count-manually-or-automatically"></a>Ridimensionare conteggio delle istanze manualmente o automaticamente

Nel [Portale di Azure](https://portal.azure.com/), è possibile impostare manualmente il conteggio delle istanze del servizio o, è possibile impostare i parametri in modo che automaticamente scala in base alla domanda. Questo viene in genere considerato *scalabilità orizzontale* o *scala in*.

Prima la scala basato sul conteggio delle istanze, è necessario che il ridimensionamento dipende dal **livello prezzi** oltre al numero di istanza. Diversi livelli prezzi possono avere memoria e numeri diversi core e pertanto hanno prestazioni migliori per lo stesso numero di istanze (ossia *scalabilità* o *scala verso il basso*). In questo articolo è descritta in particolare in *scala in* e in *uscita*.

È possibile ridimensionare nel portale ed è anche possibile utilizzare [l'API REST](https://msdn.microsoft.com/library/azure/dn931953.aspx) o [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) per regolare la scala manualmente o automaticamente.

> [AZURE.NOTE] In questo articolo viene descritto come creare un'impostazione autoscale nel portale all'indirizzo [http://portal.azure.com](http://portal.azure.com). Impostazioni AutoScale create in questo portale non possono essere modificato il portale classico ([http://manage.windowsazure.com](http://manage.windowsazure.com)).

## <a name="scaling-manually"></a>Ridimensionamento manualmente

1. Nel [Portale di Azure](https://portal.azure.com/), fare clic su **Sfoglia**, quindi passare alla risorsa che si desidera ridimensionare, ad esempio un **piano di servizio di App**.

2. Il riquadro **scala** nelle **operazioni** che indica lo stato di ridimensionamento: **disattivare** per quando si è il ridimensionamento manualmente, **in** per quando scala da uno o più metriche di prestazioni.
    ![Riquadro di scala](./media/insights-how-to-scale/Insights_UsageLens.png)

3. Fare clic sul riquadro si tornerà e il **ridimensionamento** . Nella parte superiore e il ridimensionamento è possibile visualizzare la cronologia delle azioni autoscale il servizio.  
    ![Blade di scala](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)

>[AZURE.NOTE] Solo le azioni eseguite da autoscale appariranno nel grafico. Se si modifica manualmente il conteggio delle istanze, la modifica non verranno applicata nel grafico.

4. È possibile regolare manualmente le **istanze** di numero con il dispositivo di scorrimento.
5. Fare clic sul comando **Salva** e verrà essere ridimensionato immediatamente a tale numero di istanze.

## <a name="scaling-based-on-a-pre-set-metric"></a>Il ridimensionamento in base a una metrica prestabilita

Se si desidera il numero di istanze per regolare automaticamente in base a una metrica, selezionare l'unità di misura metriche desiderato nell'elenco a discesa **scala da** . Ad esempio, per un **piano di servizio App** è possibile ridimensionare percentuale di **CPU**.

1. Quando si seleziona una metrica viene visualizzato un dispositivo di scorrimento e/o, caselle di testo per immettere il numero di istanze che si desidera ridimensionare tra:

    ![Blade scala con percentuale di CPU](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)

    AutoScale non richiederà mai il servizio sotto o sopra i limiti impostati, indipendentemente dalla propria il caricamento.

2. In secondo luogo, si sceglie l'intervallo di destinazione per le unità di misura metriche. Ad esempio, se si è scelto di **percentuale di CPU**, è possibile impostare un valore di destinazione per la CPU media tra tutte le istanze nel servizio di posta. Una scala fuori verrà eseguita quando la CPU Media supera il limite massimo che è possibile definire, allo stesso modo, si verificherà una scala in ogni volta che la media della CPU è inferiore al valore minimo.

3. Fare clic sul comando **Salva** . AutoScale controllerà alcuni minuti per assicurarsi che siano nell'intervallo istanza di destinazione per l'unità di misura. Quando il servizio riceve un aumento del traffico, si otterrà più istanze non esegue alcuna operazione.

## <a name="scale-based-on-other-metrics"></a>Scala in base alle altre metriche

È possibile ridimensionare in base alle metriche diversa da quella predefiniti che vengono visualizzati nell'elenco a discesa **scalare di** anche impostato un complesse scalabilità e ridurre in regole.

### <a name="adding-or-changing-a-rule"></a>Aggiungere o modificare una regola

1. Scegliere **regole pianificazione e le prestazioni** nell'elenco a discesa **scala da** : ![regole delle prestazioni](./media/insights-how-to-scale/Insights_PerformanceRules.png)

2. Se in precedenza erano autoscale, nel verrà visualizzato una visualizzazione di informazioni sulle regole esatte che erano presenti.

3. Per ridimensionare in base a un altro fare clic su metriche della riga di **Aggiungi regola** . È anche possibile fare clic su una delle righe esistente per modificare l'unità di misura metriche in precedenza erano compito metrica che si desidera ridimensionare da.
![Aggiungere una regola](./media/insights-how-to-scale/Insights_AddRule.png)

4. A questo punto è necessario selezionare le unità di misura metriche da scalare da. Quando si sceglie una metrica sono disponibili alcuni aspetti da considerare:
    * La *risorsa* l'unità di misura metriche proviene da. In genere questo saranno gli stessi la risorsa che ridimensionamento. Tuttavia, se si desidera ridimensionare in base alla profondità di una coda di spazio di archiviazione, la risorsa è coda che si desidera ridimensionare da.
    * Il *nome metriche* stesso.
    * L' *aggregazione temporale* della metrica. Si tratta come i dati sono la combinazione per la *durata*.

5. Dopo aver selezionato l'unità di misura è scegliere la soglia per l'unità di misura metriche e dell'operatore. Ad esempio, è possibile pronunciare **maggiore di** **80%**.

6. Scegliere l'azione che si desidera eseguire. Esistono un paio di tipo diverso di azioni:
    * Aumentare o diminuire, questo verrà aggiungere o rimuovere il numero di **valore** delle istanze che è possibile definire
    * Aumentare o diminuire percentuale: questa operazione modificherà il conteggio delle istanze da una percentuale. Ad esempio, è possibile inserire 25 nel campo **valore** e se è stato attualmente 8 varianti, è necessario aggiungere 2.
    * Aumentare o ridurre a, questa verrà impostata il conteggio delle istanze sul **valore** è possibile definire.

7. Infine, è possibile scegliere interessante verso il basso - il tempo di attesa questa regola dopo l'azione precedente scala nuovamente le dimensioni.

8. Dopo aver configurato la regola scegliere **OK**.

9. Dopo aver configurato tutte le regole desiderato, assicurarsi di raggiunto il comando **Salva** .

### <a name="scaling-with-multiple-steps"></a>Ridimensionamento con più passaggi

Gli esempi riportati sopra le applicazioni standard. Tuttavia, se si desidera utilizzare maggiormente sul ridimensionamento su (o verso il basso), è possibile aggiungere anche più regole scala per la stessa metrica. Ad esempio, è possibile definire due regole di scala sulla percentuale di CPU:

1. Scalabilità da 1 istanza se percentuale di CPU è superiore al 60%
2. Scalabilità da 3 istanze se percentuale di CPU è superiore a 85%

![Più regole di scala](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Con questa regola aggiuntiva, se il carico supera 85% prima di un'azione scala, si otterrà due istanze aggiuntive anziché uno.

## <a name="scale-based-on-a-schedule"></a>Scala in base a una programmazione


Per impostazione predefinita, quando si crea una regola di scala viene applicata sempre. È possibile vedere che quando si fa clic sull'intestazione di profilo:

![Profilo](./media/insights-how-to-scale/Insights_Profile.png)

Tuttavia, potrebbe essere necessario maggiormente proporzione durante il giorno o settimana, rispetto a quella durante il fine settimana. È anche possibile arrestare il servizio di fuori orario di lavoro.

1. A tale scopo, sul profilo di che avere, selezionare **ricorrenza** anziché **sempre** e scegliere le volte che si desidera che il profilo da applicare.

2. Ad esempio, per un profilo che si applica durante la settimana, nell'elenco a discesa **giorni** deselezionare **sabato** e **domenica**.

3. Per un profilo che si applica durante il giorno, impostare l' **ora di inizio** all'ora del giorno in cui si desidera iniziare dal.
    ![Ricorrenza predefinita](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)

4. Fare clic su **OK**.

5. Successivamente, sarà necessario aggiungere il profilo che si desidera applicare in altri casi. Fare clic sulla riga **Aggiungere profilo** .
    ![Non al lavoro](./media/insights-how-to-scale/Insights_ProfileOffWork.png)

6. Denominare il nuovo versione, secondo, profilo, ad esempio possibile chiamare **il lavoro**.

7. Selezionare di nuovo **ricorrenza** , quindi scegliere l'intervallo di conteggio istanza desiderato durante questo periodo.

8. Come con il profilo predefinito, scegliere i **giorni** da assegnare questo profilo da applicare a e l' **ora di inizio** della giornata.

>[AZURE.NOTE] AutoScale utilizzerà le regole relative all'ora legale per il **fuso orario** selezionato. Tuttavia, durante l'ora legale fuso orario di riferimento verrà visualizzato l'offset di base fuso orario, non il offset risparmi UTC dell'ora legale.

9. Fare clic su **OK**.

10. A questo punto, sarà necessario aggiungere le regole da applicare durante il secondo profilo. Fare clic su **Aggiungi regola**e quindi è possibile creare la stessa regola che durante il profilo predefinito.
    ![Aggiungere regole per disattivare la funzione lavoro](./media/insights-how-to-scale/Insights_RuleOffWork.png)

11. Assicurarsi di creare sia una regola per scalabilità e scala in oppure durante il profilo il conteggio delle istanze solo Ingrandisci (o ridurre).

12. Infine, fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

* [Metrica servizio monitor](insights-how-to-customize-monitoring.md) per verificare che il servizio è disponibile e rispondere.
* [Abilitare il monitoraggio e diagnostica](insights-how-to-use-diagnostics.md) per raccogliere metriche ad alta frequenza dettagliate del servizio.
* [Ricevere notifiche di avviso](insights-receive-alert-notifications.md) ogni volta che si verificano gli eventi operativi o metriche incrociato una determinata soglia.
* [Monitorare le prestazioni dell'applicazione](../application-insights/app-insights-azure-web-apps.md) se si vuole conoscere esattamente come l'esecuzione del codice nel cloud.
* [Visualizzare gli eventi e registri di controllo](insights-debugging-with-events.md) per informazioni su tutti gli elementi che è avvenuto nel servizio di posta.
* [Disponibilità di monitoraggio e la velocità di risposta di qualsiasi pagina web](../application-insights/app-insights-monitor-web-app-availability.md) con informazioni dettagliate sui applicazione in modo che è possibile scoprire se la pagina è verso il basso.
