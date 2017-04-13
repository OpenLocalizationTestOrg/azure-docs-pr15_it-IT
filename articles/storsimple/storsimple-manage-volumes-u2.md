<properties
   pageTitle="La gestione dei volumi StorSimple (U2) | Microsoft Azure"
   description="Viene illustrato come aggiungere, modificare, monitorare ed eliminare volumi StorSimple e come terzi offline se necessario."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/28/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-volumes-update-2"></a>Utilizzare il servizio di gestione StorSimple per gestire i volumi (aggiornamento 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Panoramica

In questa esercitazione viene illustrato come utilizzare il servizio di gestione StorSimple per creare e gestire volumi sul dispositivo StorSimple e StorSimple dispositivo virtuale con 2 Update installato.

Il servizio di gestione di StorSimple è un'estensione nel portale di classica Azure che consente di gestire la soluzione StorSimple da un'interfaccia web singola. Oltre a gestire i volumi, è possibile utilizzare il servizio di gestione di StorSimple per creare e gestire servizi StorSimple, visualizzare e gestire i dispositivi, visualizzare gli avvisi e consente di visualizzare e gestire i criteri di backup e il catalogo di backup.

## <a name="volume-types"></a>Tipi di volume

Possono essere StorSimple volumi:

- **In locale aggiunte volumi**: dati in questi volumi rimarranno sul dispositivo StorSimple locale in qualsiasi momento.
- **Volumi Tiered**: dati in questi volumi possano soppressione nel cloud.

Un volume di archiviazione è un tipo di volume a più livelli. La dimensione maggiore per il blocco deduplication utilizzata per indicare i volumi archiviazione consente il dispositivo per trasferire più grandi segmenti di dati nel cloud. 

Se necessario, è possibile modificare il volume tipo da locale a più livelli o da a più livelli a locale. Per ulteriori informazioni, passare alla sezione [Modifica il tipo di volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volumi localmente bloccati

Locale bloccati volumi sono interamente provisioning volumi che non livello dati nel cloud, in modo da assicurare locale garantisce per i dati principali, indipendentemente dalla connettività cloud. I dati in volumi localmente bloccati non deduplicated ed è compresso. Tuttavia, vengono deduplicated snapshot di volumi localmente bloccati. 

Completamente il provisioning di volumi localmente bloccati; di conseguenza, è necessario disporre di spazio sufficiente sul dispositivo quando si crea. È possibile eseguire il provisioning di volumi localmente aggiunto tutte le dimensioni massime di 8 TB sul dispositivo StorSimple 8100 e 20 TB sul dispositivo 8600. StorSimple riserva lo spazio rimanente locale nel dispositivo, per istantanee, metadati ed elaborazione dei dati. È possibile aumentare le dimensioni di un volume locale bloccata per lo spazio disponibile, ma non è possibile ridurre le dimensioni di un volume dopo averlo creato.

Quando si crea un volume locale bloccato, viene ridotta dello spazio disponibile per la creazione di volumi a più livelli. Anche l'operazione true: se si dispone di volumi a più livelli esistenti, lo spazio disponibile per la creazione di localmente aggiunte volumi sarà inferiore a quello massimo indicato in precedenza. Per ulteriori informazioni su volumi locali, consultare le [domande frequenti su volumi localmente bloccati](storsimple-local-volume-faq.md).   

### <a name="tiered-volumes"></a>Volumi a più livelli

Volumi a più livelli sono volumi eseguono provisioning in cui i dati di frequente rimangono locali nel dispositivo e meno dati utilizzati di frequente automaticamente a livelli nel cloud. Il provisioning sottile è una tecnologia di virtualizzazione in cui lo spazio di archiviazione disponibile sembra superano risorse fisiche. Anziché la prenotazione di sufficiente spazio di archiviazione in anticipo, StorSimple utilizza il provisioning sottile allocare sufficiente spazio per soddisfare requisiti corrente. Natura flessibile di spazio di archiviazione cloud semplifica l'esecuzione di questo approccio perché StorSimple possono aumentare o diminuire lo spazio di archiviazione cloud per soddisfare le esigenze.

Se si utilizza il volume a più livelli di archiviazione dati, selezionare la casella di controllo **Usa il volume per meno frequente dati archiviazione** diventa la dimensione del blocco deduplication per il volume 512 KB. Se non si seleziona questa opzione, il volume corrispondente a più livelli utilizzerà una dimensione di blocco di 64 KB. Una dimensione maggiore per il blocco deduplication consente il dispositivo per accelerare il trasferimento di dati di archiviazione di grandi dimensioni nel cloud.

>[AZURE.NOTE] Archiviazione volumi creati con una versione 2 pre-aggiornamento di StorSimple saranno importati nel modo a più livelli con la casella di controllo archiviazione selezionata.

### <a name="provisioned-capacity"></a>Capacità di provisioning

Fare riferimento alla tabella seguente per capacità di provisioning massima per ogni tipo di dispositivo e volume. Si noti che i volumi localmente bloccati non sono disponibili su un dispositivo virtuale.

|             | Dimensione massima a più livelli del volume | Massimo localmente aggiunte dimensione del volume |
|-------------|----------------------------|------------------------------------|
| **Dispositivi fisici** |       |       |
| 8100                 | 64 TB | 8 TB |
| 8600                 | 64 TB | 20 TB |
| **Dispositivi virtuali**  |       |       |
| 8010                | 30 TB | N/D   |
| 8020               | 64 TB | N/D   |

## <a name="the-volumes-page"></a>La pagina di volumi

La pagina di **volumi** consente di gestire i volumi di archiviazione che vengono effettuato il provisioning nel dispositivo, Microsoft Azure StorSimple per gli iniziatori (server). Visualizza l'elenco dei volumi nel dispositivo StorSimple.

 ![Pagina di volumi](./media/storsimple-manage-volumes-u2/VolumePage.png)

Un volume è costituito da una serie di attributi:

- **Il nome del volume** : un nome descrittivo che deve essere univoco e consente di identificare il volume. Questo nome viene usato anche in rapporti di monitoring quando applica un filtro in un volume specifico.

- **Stato** : può essere online o offline. Se un volume è offline, non è visibile ai iniziatori (server) sono autorizzati ad accedere a utilizzare il volume.

- **Capacità** : specifica la quantità totale di dati che possono essere memorizzati dall'iniziatore (server). Aggiunte localmente volumi completamente viene completato il provisioning e si trovano sul dispositivo StorSimple. Eseguono viene completato il provisioning volumi a più livelli e i dati sono deduplicated. Con i volumi eseguono provisioning, il dispositivo non pre-allocazione capacità di archiviazione fisica internamente o nel cloud in base alle capacità del volume configurato. La capacità del volume è allocata e utilizzata su richiesta.

- **Tipo** : indica se il volume è **Tiered** (impostazione predefinita) o **aggiunte in locale**.

- **Backup** – indica se è presente un criterio di backup predefinito per il volume.

- **Access** -specifica iniziatori (server) sono autorizzati ad accedere a questo volume. Iniziatori che non appartengono a record di controllo di accesso (ACR) associato il volume non verranno visualizzato il volume.

- **Monitoraggio** – specifica o meno da monitorare un volume. Un volume avrà monitoraggio attivata per impostazione predefinita quando viene creato. Monitoraggio verrà, tuttavia, essere disabilitata per un duplicato volume. Per attivare il monitoraggio per un volume, seguire le istruzioni in [Monitor di un volume](#monitor-a-volume). 

Attenersi alle istruzioni in questa esercitazione per eseguire le operazioni seguenti:

- Aggiungere un volume 
- Modificare un volume 
- Modificare il tipo di volume
- Eliminare un volume 
- Disconnettere un volume 
- Monitorare un volume 

## <a name="add-a-volume"></a>Aggiungere un volume

Durante la distribuzione della soluzione StorSimple è stato [creato un volume](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) . Aggiunta di un volume è una procedura simile.

#### <a name="to-add-a-volume"></a>Per aggiungere un volume

1. Nella pagina **dispositivi** selezionare il dispositivo, fare doppio clic su esso e quindi fare clic sulla scheda **Contenitori Volume** .

2. Selezionare un contenitore volume dall'elenco e fare doppio clic su esso per accedere a volumi associati al contenitore.

3. Fare clic su **Aggiungi** nella parte inferiore della pagina. Aggiungere un volume viene avviata.

     ![Volume installazione guidata impostazioni di base](./media/storsimple-manage-volumes-u2/TieredVolEx.png)

4. Nella casella Aggiungi una procedura guidata volume, in **Impostazioni di base**, eseguire le operazioni seguenti:

  1. Specificare un **nome** per il volume.
  2. Selezionare un **Tipo di utilizzo** dall'elenco a discesa. Per carichi di lavoro che richiede dei dati disponibili in locale nel dispositivo in qualsiasi momento, selezionare **Bloccato in locale**. Per tutti gli altri tipi di dati, selezionare **Tiered**. (**Tiered** è l'impostazione predefinita).
  3. Se si seleziona **Tiered** nel passaggio 2, è possibile selezionare la casella di controllo **utilizzare questo volume per meno frequente dati di archiviazione** per configurare un volume di archiviazione.
  4. Immettere la **Capacità viene completato il provisioning** per il volume in GB o TB. Vedere [Provisioned capacità](#provisioned-capacity) di dimensioni massime per ogni tipo di dispositivo e volume. Osservare la **Capacità disponibile** per determinare quanto spazio di archiviazione è effettivamente disponibile nel dispositivo.

5. Fare clic sull'icona freccia![Icona freccia](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Se si sta configurando un volume locale bloccato, si verrà visualizzato il messaggio seguente.

    ![Modificare il messaggio di tipo Volume](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
   
5. Fare clic sull'icona freccia ![icona freccia](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)nuovamente per passare alla pagina **Impostazioni aggiuntive** .

    ![Aggiungere ulteriori impostazioni guidata Volume](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>

6. In **Impostazioni aggiuntive**, aggiungere un nuovo record di controllo di accesso (ACR):
  
  1. Selezionare un record di controllo di accesso (ACR) dall'elenco a discesa. In alternativa, è possibile aggiungere un nuovo ACR. ACRs determinano quali host può accedere ai volumi mediante corrispondente all'host IQN con quello elencato nel record. Se non si specifica un ACR, si verrà visualizzato il messaggio seguente.

        ![Specificare ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)

  2. È consigliabile selezionare la casella di controllo **attiva una copia di backup predefinito per il volume** .
  3. Fare clic sull'icona di controllo ![Icona del controllo](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) Per creare il volume con le impostazioni specificate.

È ora possibile utilizzare il nuovo volume.

>[AZURE.NOTE] Se si crea un volume locale bloccato e quindi creare un'altra localmente bloccato volume immediatamente in un secondo momento, la creazione del volume processi eseguiti in sequenza. Deve finire il processo di creazione primo volume prima di poter iniziare il processo di creazione volume successivo.

## <a name="modify-a-volume"></a>Modificare un volume

Modificare un volume quando è necessario per espanderla o modificare gli host che accedere al volume.

> [AZURE.IMPORTANT] 
>
> - Se si modifica la dimensione del volume sul dispositivo, la dimensione del volume deve essere modificata nell'host anche. 
> - Lato host procedure descritte di seguito sono per Windows Server 2012 (2012R2). Procedure per Linux o altri sistemi operativi host sono diverse. Quando si modifica il volume su un host con altri sistemi operativi, consultare le istruzioni del sistema operativo host. 

#### <a name="to-modify-a-volume"></a>Per modificare un volume

1. Nella pagina **dispositivi** selezionare il dispositivo, fare doppio clic su esso e quindi fare clic sulla scheda **Contenitori Volume** .

2. Selezionare un contenitore volume dall'elenco e fare doppio clic su esso per visualizzare i volumi associati al contenitore.

3. Selezionare un volume e nella parte inferiore della pagina, fare clic su **Modifica**. Verrà avviata la creazione guidata volume di modifica.

4. Nella creazione guidata volume modifica, in **Impostazioni di base**, è possibile eseguire le operazioni seguenti:

  - Modificare il **nome**.
  - Convertire il **Tipo di utilizzo** da localmente aggiunte a più livelli o da a più livelli a localmente aggiunte (per ulteriori informazioni, vedere [modificare il tipo di volume](#change-the-volume-type) ).
  - Aumentare la **capacità di provisioning**. Solo è possibile aumentare la **Capacità viene completato il provisioning** . È possibile ridurre un volume dopo averlo creato.

5. In **Impostazioni aggiuntive**, è possibile modificare ACR, a condizione che il volume sia offline. Se il volume è online, è necessario disconnettere prima di tutto. Prima di modificare il ACR, consultare la procedura descritta in [eseguire un volume offline](#take-a-volume-offline) .

    > [AZURE.NOTE] Non è possibile modificare l'opzione **attiva una copia di backup predefinito** per il volume.

6. Salvare le modifiche facendo clic sull'icona di controllo ![icona di controllo](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). Il portale classico Azure verrà visualizzato un messaggio di volume aggiornamento. È verrà visualizzato un messaggio di conferma quando il volume è stato aggiornato.

7. Se si espandono un volume, completare la procedura seguente sul computer host Windows:

   1. Passare a **Gestione Computer** ->**Gestione disco**.
   2. Il pulsante destro **Del disco** e selezionare **Ripeti analisi dischi**.
   3. Nell'elenco dei dischi, selezionare il volume è aggiornato, pulsante destro del mouse e quindi selezionare **Estendi Volume**. Verrà avviata la procedura guidata Estendi Volume. Fare clic su **Avanti**.
   4. Completare la procedura guidata accettare i valori predefiniti. Al termine della procedura, il volume deve essere visualizzata l'aumento delle dimensioni.

    >[AZURE.NOTE] Se si espande un volume locale bloccato e quindi espandere un altro localmente aggiunte volume immediatamente in un secondo momento, i processi di espansione volume eseguite in sequenza. Deve finire il processo di espansione primo volume prima di poter iniziare il processo di espansione volume successivo.

![Video disponibile](./media/storsimple-manage-volumes-u2/Video_icon.png) **Video disponibile**

Per guardare un video che illustra come espandere un volume, fare clic [qui](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="change-the-volume-type"></a>Modificare il tipo di volume

È possibile modificare il tipo di volume da a più livelli a localmente aggiunte o da localmente aggiunte a più livelli. Questa conversione deve essere una frequenza. Ecco alcuni motivi per la conversione di un volume da a più livelli a localmente aggiunte sono:

- Locale garanzie relative alle prestazioni e la disponibilità dei dati
- Eliminazione di latenza cloud e problemi di connettività del cloud.

In genere, questi sono piccoli volumi esistenti che si desidera accedere spesso. Un volume locale bloccato completamente viene eseguito il provisioning quando viene creato. Se si desidera convertire un volume a più livelli in un volume locale bloccato, StorSimple consente di verificare di disporre di spazio sufficiente sul dispositivo prima di iniziare la conversione. Se si dispone di spazio sufficiente, si riceverà un messaggio di errore e viene annullato l'operazione. 

> [AZURE.NOTE] Prima di iniziare una conversione da a più livelli di aggiunte in locale, assicurarsi di considerare gli altri carichi di lavoro ai requisiti di spazio. 

È consigliabile modificare un volume locale bloccato in un volume a più livelli, se è necessario spazio aggiuntivo per eseguire il provisioning altri volumi. Quando si converte il volume localmente aggiunto a più livelli, la capacità disponibile in aumenta il dispositivo per la dimensione della capacità rilasciata. Se i problemi di connettività impediscono la conversione di un volume da un tipo di locali per il tipo di a più livelli, volume locale assumerà proprietà di un volume a più livelli fino a quando non viene completata la conversione. In questo modo alcuni dati potrebbero siano stati inseriti vuoti nel cloud. Questi dati espanso continuerà a occupare spazio locale nel dispositivo non è possibile liberare fino a quando non si riavvia e completata l'operazione.

>[AZURE.NOTE] Conversione di un volume può richiedere del tempo e non è possibile annullare una conversione dopo l'avvio. Il volume online durante la conversione e che è possibile eseguire backup, che però non è possibile espandere o ripristinare il volume mentre viene eseguita la conversione.  

Conversione da una a più livelli in un volume locale bloccata può influire negativamente sulle prestazioni dispositivo. Inoltre, i fattori seguenti potrebbero aumentare il tempo che necessario per completare la conversione:

- Non c'è della larghezza di banda insufficiente.

- Non esiste alcun backup corrente.

Per ridurre al minimo gli effetti che possono avere fattori seguenti:

- Esaminare la limitazione dei criteri di larghezza di banda e assicurarsi che sia disponibile un dedicato 40 Mbps della larghezza di banda.
- Pianificare la conversione per le ore.
- Creare uno snapshot cloud prima di iniziare la conversione.

Se si desidera convertire più volumi (che supporta diversi carichi di lavoro), quindi si deve assegnare priorità conversione del volume in modo che i volumi priorità più elevati vengano convertiti prima di tutto. Ad esempio, è necessario convertire volumi che ospitano macchine () o volumi con carichi di lavoro SQL prima di convertire volumi con carichi di lavoro Condivisione file.

#### <a name="to-change-the-volume-type"></a>Per modificare il tipo di volume

1. Nella pagina **dispositivi** selezionare il dispositivo, fare doppio clic su esso e quindi fare clic sulla scheda **Contenitori Volume** .

2. Selezionare un contenitore volume dall'elenco e fare doppio clic su esso per visualizzare i volumi associati al contenitore.

3. Selezionare un volume e nella parte inferiore della pagina, fare clic su **Modifica**. Verrà avviata la creazione guidata volume di modifica.

4. Nella pagina **Impostazioni di base** , modificare il tipo di utilizzo selezionando il nuovo tipo di dall'elenco a discesa **Tipo di utilizzo** .

    - Se si modifica il tipo di a **localmente aggiunte**, StorSimple controllerà per verificare se è sufficiente.
    - Se si modifica il tipo di **Tiered** e il volume verrà utilizzato per i dati di archiviazione, selezionare la casella di controllo **Usa il volume per meno frequente dati di archiviazione** .

        ![Casella di controllo archivio](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)

5. Fare clic sull'icona freccia ![icona freccia](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) per passare alla pagina **Impostazioni aggiuntive** . Se si sta configurando un volume locale bloccato, verrà visualizzato il seguente messaggio.

    ![Modificare il messaggio di tipo Volume](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)

6. Fare clic sull'icona freccia ![icona freccia](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) Per continuare.

7. Fare clic sull'icona di controllo ![Icona del controllo](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) Per avviare il processo di conversione. Portale di Azure verrà visualizzato un messaggio di volume aggiornamento. È verrà visualizzato un messaggio di conferma quando il volume è stato aggiornato.

## <a name="take-a-volume-offline"></a>Disconnettere un volume

Potrebbe essere necessario disconnettere un volume quando si prevede di modificarlo o eliminarlo. Quando un volume è offline, non è disponibile per l'accesso in lettura / scrittura. È necessario eseguire il volume non in linea nell'host oltre che nel dispositivo. 

#### <a name="to-take-a-volume-offline"></a>Per disconnettere un volume

1. Assicurarsi che il volume in questione non sia in uso prima di portare non in linea.

2. Richiedere il volume non in linea nell'host prima. Per evitare qualsiasi rischio di danneggiamento dei dati del volume. Per istruzioni specifiche, vedere le istruzioni per il sistema operativo host.

3. Dopo l'host è offline, richiedere il volume sul dispositivo non in linea eseguendo la procedura seguente:

  1. Nella pagina **dispositivi** selezionare il dispositivo, fare doppio clic su esso e quindi fare clic sulla scheda **Contenitori Volume** . Nella scheda **Volume contenitori** sono elencati sotto forma di tutti i contenitori volume associati al dispositivo.
  2. Selezionare un contenitore di volume e fare clic su esso per visualizzare l'elenco di tutti i volumi all'interno del contenitore.
  3. Selezionare un volume e fare clic su **non in linea**.
  4. Quando viene richiesto di confermare l'operazione, fare clic su **Sì**. Il volume dovrebbe ora essere offline.

    Dopo avere inserito un volume non in linea, diventa disponibile l'opzione **In linea** .

> [AZURE.NOTE] Il comando **Non in linea** invia una richiesta al dispositivo per disconnettere il volume. Se host siano ancora usando il volume, il risultato è interrotta connessioni, ma disconnettere il volume non avrà esito negativo. 

## <a name="delete-a-volume"></a>Eliminare un volume

> [AZURE.IMPORTANT] È possibile eliminare un volume solo se è in linea.

Completare la procedura seguente per eliminare un volume.

#### <a name="to-delete-a-volume"></a>Per eliminare un volume

1. Nella pagina **dispositivi** selezionare il dispositivo, fare doppio clic su esso e quindi fare clic sulla scheda **Contenitori Volume** .

2. Selezionare il contenitore volume che contiene il volume che si desidera eliminare. Fare clic sul contenitore di volume per accedere alla pagina di **volumi** .

3. Tutti i volumi associati a questo contenitore vengono visualizzati in formato tabulare. Controllare lo stato del volume che si desidera eliminare. Se il volume che si desidera eliminare non è offline, disconnettere prima di tutto, seguire la procedura descritta in [eseguire un volume offline](#take-a-volume-offline).

4. Dopo avere inserito il volume non in linea, fare clic su **Elimina** nella parte inferiore della pagina.

5. Quando viene richiesto di confermare l'operazione, fare clic su **Sì**. Verrà eliminato il volume e la pagina di **volumi** verrà visualizzato l'elenco aggiornato dei volumi all'interno del contenitore.

    >[AZURE.NOTE]Se si elimina un volume locale bloccato, lo spazio disponibile per i nuovi volumi potrebbe non essere aggiornato immediatamente. Il servizio di gestione StorSimple Aggiorna periodicamente locale allo spazio disponibile. È consigliabile che attendere alcuni minuti prima di provare a creare il nuovo volume.<br> Inoltre, se si elimina un volume locale bloccato e quindi eliminarla un'altra localmente aggiunte volume immediatamente in un secondo momento, l'eliminazione di volume processi eseguiti in sequenza. Deve finire il processo di eliminazione primo volume prima di poter iniziare il processo di eliminazione volume successivo.
 
## <a name="monitor-a-volume"></a>Monitorare un volume

Monitoraggio dei volumi consente di raccogliere dati statistici relativi ai / O per un volume. Il monitoraggio è attivato per impostazione predefinita per i primi 32 volumi creati. Monitoraggio di volumi aggiuntivi è disattivato per impostazione predefinita. Monitoraggio dei volumi duplicati è disattivato per impostazione predefinita.

Eseguire la procedura seguente per attivare o disattivare il monitoraggio di un volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Per abilitare o disabilitare il monitoraggio volume

1. Nella pagina **dispositivi** selezionare il dispositivo, fare doppio clic su esso e quindi fare clic sulla scheda **Contenitori Volume** .

2. Selezionare il contenitore volume in cui si trova il volume e quindi fare clic sul contenitore volume per accedere alla pagina di **volumi** .

3. Nella visualizzazione tabella sono elencati tutti i volumi associati a questo contenitore. Fare clic su e selezionare il volume o duplicato volume.

4. Nella parte inferiore della pagina, fare clic su **Modifica**.

5. Della procedura guidata Modifica Volume, in **Impostazioni di base**, selezionare **Attiva** o **Disattiva** dall'elenco a discesa **monitoraggio** .

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [un volume StorSimple duplicato](storsimple-clone-volume.md).

- Informazioni su come [usare il servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).

 
