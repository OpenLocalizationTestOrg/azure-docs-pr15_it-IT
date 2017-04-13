<properties
   pageTitle="Gestire i modelli di larghezza di banda StorSimple | Microsoft Azure"
   description="Descrive come gestire i modelli di larghezza di banda StorSimple, che consentono di controllare la larghezza di banda."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>Utilizzare il servizio di gestione StorSimple per gestire i modelli di larghezza di banda StorSimple

## <a name="overview"></a>Panoramica

Modelli di larghezza di banda consentono di configurare l'utilizzo della larghezza di banda di rete tra più pianificazioni ora del giorno per i dati dal dispositivo StorSimple nel cloud a più livelli.

Limitazione le pianificazioni di larghezza di banda consente di:

- Specificare le pianificazioni di larghezza di banda personalizzate a seconda di utilizzi di rete di carico di lavoro.

- Centralizzare la gestione e riutilizzare le pianificazioni in più dispositivi in modo semplice e agevole.

> [AZURE.NOTE] Questa caratteristica è disponibile solo per i dispositivi fisici StorSimple e non per i dispositivi virtuali.

Tutti i modelli di larghezza di banda per il servizio vengono visualizzati in formato tabulare e contengono le informazioni seguenti:

- **Nome** : un nome univoco assegnato al modello di larghezza di banda quando è stata creata.

- **Pianificazione** : il numero delle pianificazioni contenuti in un modello di larghezza di banda specificato.

- **Riferimento** : il numero di volumi utilizzando i modelli di larghezza di banda.

Utilizzare la pagina di **configurazione** del servizio StorSimple Manager nel portale di classica Azure per gestire i modelli di larghezza di banda.

È anche possibile trovare informazioni aggiuntive per la configurazione di modelli di larghezza di banda in:

- Domande e risposte sui modelli di larghezza di banda
- Procedure consigliate per i modelli di larghezza di banda

## <a name="add-a-bandwidth-template"></a>Aggiungere un modello di larghezza di banda

Eseguire la procedura seguente per creare un nuovo modello di larghezza di banda.

#### <a name="to-add-a-bandwidth-template"></a>Per aggiungere un modello di larghezza di banda

1. Nella pagina **Configura** servizio StorSimple Manager, fare clic su **Aggiungi/Modifica modello di larghezza di banda**.

2. Nella finestra di dialogo **Aggiungi/modifica della larghezza di banda modello** :

   1. Dall'elenco a discesa **modello** selezionare **Crea nuovo** per aggiungere un nuovo modello di larghezza di banda.
   2. Specificare un nome univoco per il modello di larghezza di banda.

3. Definire una **pianificazione della larghezza di banda**. Per creare una pianificazione:

   1. Scegliere i giorni della settimana che la programmazione è configurata per l'elenco a discesa. È possibile selezionare più giorni selezionando le caselle di controllo posizionate prima i rispettivi giorni nell'elenco.
   2. Selezionare l'opzione **Tutto il giorno** se la programmazione è attivata per l'intera giornata. Quando questa opzione è selezionata, non è possibile specificare un' **Ora di inizio** o un' **Ora di fine**. L'esecuzione della pianificazione dall'ora 00.00 a 11:59 PM.
   3. Dall'elenco a discesa selezionare un' **Ora di inizio**. Si tratta quando verrà avviata la pianificazione.
   4. Dall'elenco a discesa selezionare un' **Ora di fine**. Si tratta quando la programmazione verrà interrotto.

         > [AZURE.NOTE] Le pianificazioni sovrapposte non consentite. Se l'ora di inizio e fine genereranno una pianificazione di sovrapposizione, si verrà visualizzato un messaggio di errore in tal senso.

   5. Specificare la **frequenza di larghezza di banda**. Verrà visualizzata la larghezza di banda in megabit al secondo (Mbps) utilizzato dal dispositivo StorSimple nelle operazioni che comportano nel cloud (caricamento e download). Specificare un numero compreso tra 1 e 1.000 per questo campo.

   6. Fare clic sull'icona di controllo ![icona controllo](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Il modello è stato creato verrà aggiunti all'elenco dei modelli di larghezza di banda nella pagina **Configura** il servizio.

    ![Crea nuovo modello di larghezza di banda](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)

4. Fare clic su **Salva** nella parte inferiore della pagina e quindi fare clic su **Sì** quando viene richiesto di confermare l'operazione. Verranno salvate le modifiche di configurazione che sono state.

## <a name="edit-a-bandwidth-template"></a>Modificare un modello di larghezza di banda

Per modificare un modello di larghezza di banda, procedere come segue.

### <a name="to-edit-a-bandwidth-template"></a>Per modificare un modello di larghezza di banda

1. Scegliere **Aggiungi/modifica della larghezza di banda modello**.

2. Nella finestra di dialogo **Aggiungi/modifica della larghezza di banda modello** :

   1. Dall'elenco a discesa **modello** scegliere un modello della larghezza di banda esistente che si desidera modificare.
   2. Completare le modifiche. (È possibile modificare le impostazioni esistenti.)
   3. Fare clic sull'icona di controllo ![Icona del controllo](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Il modello modificato nell'elenco dei modelli di larghezza di banda verranno visualizzati nella pagina Configura il servizio.

3. Per salvare le modifiche, fare clic su **Salva** nella parte inferiore della pagina. Fare clic su **Sì** quando viene richiesto di confermare l'operazione.

> [AZURE.NOTE] Non sarà possibile salvare le modifiche se la programmazione modificata si sovrappone a una pianificazione esistente nel modello di larghezza di banda che si desidera modificare.

## <a name="delete-a-bandwidth-template"></a>Eliminare un modello di larghezza di banda

Per eliminare un modello di larghezza di banda, procedere come segue.

#### <a name="to-delete-a-bandwidth-template"></a>Per eliminare un modello di larghezza di banda

1. Nell'elenco tabulare dei modelli di larghezza di banda per il servizio, selezionare il modello che si desidera eliminare. Per l'estrema destra del modello selezionato verrà visualizzata un'icona di eliminazione (**x**). Fare clic sull'icona di **x** per eliminare il modello.

2. Verrà richiesto di confermare l'operazione. Fare clic su **OK** per continuare.

Se il modello è in uso da qualsiasi volumi, non sarà possibile per eliminarla. Verrà visualizzato un messaggio di errore che indica che il modello è in uso. Verrà visualizzato nella finestra di dialogo di messaggio di errore in cui verrà suggerito che devono essere rimossi tutti i riferimenti al modello.

È possibile eliminare tutti i riferimenti al modello di accedere alla pagina di **Contenitori Volume** e modificando i contenitori di volume di utilizzano questo modello in modo che utilizzano un altro modello o utilizzare l'impostazione della larghezza di banda personalizzato o un numero illimitato. Quando sono stati rimossi tutti i riferimenti, è possibile eliminare il modello.

## <a name="use-a-default-bandwidth-template"></a>Utilizzare un modello di larghezza di banda predefinito

Un modello di larghezza di banda predefinito viene fornito ed è utilizzato da contenitori volume per impostazione predefinita per attivare i controlli della larghezza di banda durante l'accesso cloud. Il modello predefinito funge anche da un riferimento pronto per gli utenti che creano i propri modelli. I dettagli del modello predefinito sono:

- **Nome** : illimitati notte e i fine settimana

- **Pianificazione** : un'unica pianificazione dal lunedì al venerdì che si applica un tasso di larghezza di banda di 1 MB/s tra 8 AM e PM 5 dispositivo ora. La larghezza di banda viene impostato su illimitata per il resto della settimana.

È possibile modificare il modello predefinito. L'utilizzo di questo modello (comprese le versioni modificate) viene controllato.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Creare un modello della larghezza di banda giornata intera che inizia in un momento specificato

Seguire questa procedura per creare una pianificazione che inizia in un momento specificato ed esegue giornata intera. Nell'esempio, la programmazione inizia 9: 00 di mattina e viene eseguito fino a 9 del Mattino successivo. È importante tenere presente che l'ora di inizio e fine di un impiego specificato devono essere contenuti entrambi nella stessa pianificazione di 24 ore e non possono essere estesi più giorni. Se è necessario configurare modelli di larghezza di banda che si estendono su più giorni, sarà necessario utilizzare più pianificazioni (come illustrato nell'esempio).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Per creare un modello di larghezza di banda giornata intera

1. Creare una pianificazione che inizia con 9: 00 di mattina e viene eseguito fino a mezzanotte.

2. Aggiungere un'altra pianificazione. Configurare la pianificazione seconda Esegui da mezzanotte fino a 9: 00 di mattina.

3. Salvare il modello di larghezza di banda.

Pianificazione composta verrà quindi avviare in un momento della scelta ed eseguire giornata intera.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Domande e risposte sui modelli di larghezza di banda

**Q**. Cosa succede ai controlli della larghezza di banda quando ci si trova tra le pianificazioni? (È terminato una pianificazione e un altro non è ancora stata avviata).

**A**. In tal caso, non verranno utilizzati controlli della larghezza di banda. Ciò significa che il dispositivo può utilizzare della larghezza di banda illimitato quando il collegamento a livelli nel cloud.

**Q**. È possibile modificare i modelli di larghezza di banda in un dispositivo offline?

**A**. Non sarà possibile modificare i modelli di larghezza di banda sui contenitori di volumi se il dispositivo corrispondente è offline.

**Q**. Modificare un modello di larghezza di banda associato a un contenitore di volume quando i volumi associati sono offline?

**A**. È possibile modificare un modello di larghezza di banda associato a un contenitore di volume con volumi in modalità offline. Si noti che in volumi in linea, dati non verranno livelli dal dispositivo nel cloud.

**Q**. È possibile eliminare un modello predefinito?

**A**. Sebbene sia possibile eliminare un modello predefinito, non è consigliabile farlo. L'utilizzo di un modello predefinito, comprese le versioni modificate, viene controllato. I dati di rilevamento vengono analizzati e nel corso del tempo, viene utilizzati per migliorare il modello predefinito.

**Q**. Come si determina che è necessario modificare i modelli di larghezza di banda

**A**. Uno dei segni di che è necessario modificare i modelli di larghezza di banda è quando si avvia vedono la rete rallentare o riduzione più volte in un giorno. In questo caso, monitorare la rete lo spazio di archiviazione e l'uso esaminando grafici prestazioni i/o e velocità di rete.

Identificare l'ora del giorno e i contenitori di volume in cui si verifica bottiglia rete dai dati di velocità di rete. Se si verifica questo evento quando sono vengano a più livelli di dati nel cloud (get queste informazioni prestazioni i/o per tutti i contenitori di volume per dispositivo nel cloud), quindi è necessario modificare i modelli di larghezza di banda associati i contenitori di volume.

Dopo aver modificati modelli in uso, sarà necessario monitorare la rete nuovamente per latenza significativa. Se questi sono ancora presenti, sarà necessario rivedere i modelli di larghezza di banda.

**Q**. Cosa accade se dispongono di più contenitori volume sul dispositivo pianifica tale sovrapposizione ma diversi limiti si applicano a ogni?

**A**. Si supponga di disporre di un dispositivo con 3 contenitori volume. Sovrapposizione delle pianificazioni associate a questi contenitori completamente. Per ciascuno dei contenitori, i limiti della larghezza di banda utilizzati sono 5, 10 e 15 Mbps. Quando si verificano su tutti i contenitori i/o nello stesso momento, può essere applicato il valore minimo dei limiti della larghezza di banda 3: in questo caso, 5 Mbps come questi in uscita delle richieste di condividere la stessa coda.

## <a name="best-practices-for-bandwidth-templates"></a>Procedure consigliate per i modelli di larghezza di banda

Eseguire queste procedure consigliate per il dispositivo StorSimple seguenti:

- Configurare modelli di larghezza di banda in un dispositivo per attivare variabile la limitazione di velocità di rete dal dispositivo orari del giorno. Questi modelli di larghezza di banda se utilizzata con le pianificazioni di backup in modo efficace possono sfruttare le della larghezza di banda di rete aggiuntive per le operazioni di cloud durante le ore.

- Calcolare la larghezza di banda necessario per una specifica distribuzione in base alle dimensioni di distribuzione e l'obiettivo di tempo richiesta di ripristino (RTO).

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni [sull'utilizzo del servizio di gestione di StorSimple per amministrare il dispositivo StorSimple](storsimple-manager-service-administration.md).
