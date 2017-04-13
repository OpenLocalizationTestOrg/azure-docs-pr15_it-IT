<properties
    pageTitle="Automatico ridimensionare un servizio cloud nel portale di | Microsoft Azure"
    description="(classica) Informazioni su come utilizzare il portale classico per configurare le regole di scala automatico per un ruolo web servizio cloud o un ruolo di lavoro in Azure."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>


# <a name="how-to-auto-scale-a-cloud-service"></a>Come un servizio cloud di adattamento automatico

> [AZURE.SELECTOR]
- [Portale di Azure](cloud-services-how-to-scale-portal.md)
- [Portale classica Azure](cloud-services-how-to-scale.md)

Nella pagina scala del portale di classica Azure, è possibile ridimensionare manualmente il ruolo web o lavoro oppure è possibile attivare il ridimensionamento automatico in base a una coda di messaggi o carico CPU.

>[AZURE.NOTE] In questo articolo è incentrato sui ruoli web e lavoro servizio Cloud. Quando si crea una macchina virtuale (classico) direttamente, questo viene ospitato in un servizio cloud. Alcune di queste informazioni si applica a questi tipi di macchine virtuali. Ridimensionamento di un set di disponibilità di macchine virtuali semplicemente arresto del loro attivato e disattivato in base alle regole di scala che configurare. Per ulteriori informazioni sulle macchine virtuali e insiemi di disponibilità, vedere [gestire la disponibilità di macchine virtuali](../virtual-machines/virtual-machines-windows-classic-configure-availability.md)

Prima di configurare proporzioni dei caratteri per l'applicazione, è necessario considerare le seguenti informazioni:

- Proporzioni dei caratteri viene influenzato dall'utilizzo di base. Maggiore istanze del ruolo usare più core. È possibile ridimensionare un'applicazione solo entro il limite di core per l'abbonamento. Ad esempio, se l'abbonamento ha un limite di venti core e si esegue un'applicazione con due medie dimensioni cloud services (un totale di quattro core), è possibile scalare solo le altre distribuzioni di servizio cloud nell'abbonamento di 16 core. Per ulteriori informazioni sulle dimensioni, vedere [Le dimensioni del servizio Cloud](cloud-services-sizes-specs.md) .

- È necessario creare una coda e associare a un ruolo prima che è possibile ridimensionare un'applicazione in base a una determinata soglia messaggio. Per ulteriori informazioni, vedere [come utilizzare il servizio di archiviazione coda](../storage/storage-dotnet-how-to-use-queues.md).

- È possibile ridimensionare le risorse che sono collegate al servizio cloud. Per ulteriori informazioni sul collegamento risorse, vedere [come: collegare una risorsa in un servizio cloud](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service).

- Per abilitare la disponibilità dell'applicazione, è necessario assicurarsi che venga distribuito con due o più istanze del ruolo. Per ulteriori informazioni, vedere [I contratti di servizio](https://azure.microsoft.com/support/legal/sla/).



## <a name="schedule-scaling"></a>Pianificare il ridimensionamento

Per impostazione predefinita, tutti i ruoli non seguono una pianificazione specifica. Di conseguenza, le impostazioni modificate applicano a tutti gli orari e tutti i giorni nel corso dell'anno. Se si desidera, è possibile impostare il ridimensionamento manuale o automatico per:

- Giorni lavorativi
- Fine settimana
- La notte
- Mattina settimana
- Date specifiche
- Intervalli di date specifico

Conigured nel [portale classica Azure](https://manage.windowsazure.com/) questa impostazione è attiva la  
**Servizi cloud** > **\[il servizio cloud\]** > **scala** > **\[produzione o in prova\] ** pagina.

Fare clic sul pulsante **impostare gli orari di programmazione** per ogni ruolo che si desidera modificare.

![Adattamento automatico in base alla programmazione di un servizio cloud][scale_schedules]



## <a name="manual-scale"></a>Scala manuale

Nella pagina **scala** manualmente aumentare o ridurre il numero di istanze in esecuzione in un servizio cloud. Questo è configurato per ogni pianificazione che è stata creata o all'ora tutti se non è stata creata una programmazione.

1. Nel [portale classica Azure](https://manage.windowsazure.com/), fare clic su **Servizi Cloud**e quindi fare clic sul nome del servizio cloud per aprire il dashboard.

    > [AZURE.TIP] Se non è presente il servizio cloud, potrebbe essere necessario modificare dalla **produzione** di **gestione temporanea** o viceversa.

2. Fare clic su **scala**.

3. Selezionare la pianificazione che si desidera modificare le opzioni di ridimensionamento per. Per impostazione predefinita *n orari* se non si dispone di alcun pianificazioni definite.

4. Individuare la sezione **scala dalla metrica** e selezionare **Nessuno**. Questo è l'impostazione predefinita per tutti i ruoli.

5. Ogni ruolo nel servizio cloud ha un dispositivo di scorrimento per la modifica del numero di istanze da utilizzare.

    ![Ridimensionare manualmente un ruolo di servizio cloud][manual_scale]

    Se sono necessarie più istanze, potrebbe essere necessario modificare la [dimensione macchina virtuale del servizio cloud](cloud-services-sizes-specs.md).

6. Fare clic su **Salva**.  
Istanze del ruolo verranno aggiunti o rimossi in base alle selezioni.

>[AZURE.TIP] Ogni volta che viene visualizzato ![][tip_icon] spostare il puntatore del mouse ed è possibile ottenere assistenza sulle indica che una determinata impostazione.


## <a name="automatic-scale---cpu"></a>Ridimensionamento automatico - CPU

Questa operazione ridimensiona se la percentuale media di utilizzo della CPU passa sopra o sotto soglie specificate; istanze del ruolo vengono create o eliminate.

1. Nel [portale classica Azure](https://manage.windowsazure.com/), fare clic su **Servizi Cloud**e quindi fare clic sul nome del servizio cloud per aprire il dashboard.

    > [AZURE.TIP] Se non è presente il servizio cloud, potrebbe essere necessario modificare dalla **produzione** di **gestione temporanea** o viceversa.

2. Fare clic su **scala**.

3. Selezionare la pianificazione che si desidera modificare le opzioni di ridimensionamento per. Per impostazione predefinita *n orari* se non si dispone di alcun pianificazioni definite.

4. Individuare la sezione **scala dalla metrica** e selezionare **CPU**.

5. A questo punto è possibile configurare un intervallo massimo e minimo di istanze di ruoli, la destinazione della CPU (per attivare una scala alto) e quante istanze scalare alto e verso il basso per.

![Ridimensionare un ruolo di servizio cloud di carico cpu][cpu_scale]

>[AZURE.TIP] Ogni volta che viene visualizzato ![][tip_icon] spostare il puntatore del mouse ed è possibile ottenere assistenza sulle indica che una determinata impostazione.





## <a name="automatic-scale---queue"></a>Ridimensionamento automatico - coda

Questa operazione ridimensiona automaticamente se il numero di messaggi in una coda passa sopra o sotto una determinata soglia; istanze del ruolo vengono create o eliminate.

1. Nel [portale classica Azure](https://manage.windowsazure.com/), fare clic su **Servizi Cloud**e quindi fare clic sul nome del servizio cloud per aprire il dashboard.

    > [AZURE.TIP] Se non è presente il servizio cloud, potrebbe essere necessario modificare dalla **produzione** di **gestione temporanea** o viceversa.

2. Fare clic su **scala**.

3. Individuare la sezione **scala dalla metrica** e selezionare **CPU**.

4. A questo punto è possibile configurare un intervallo massimo e minimo delle istanze di ruoli, la coda e la quantità di messaggi da elaborare per ogni istanza e il numero di istanze scalare alto e verso il basso per.

![Ridimensionare un ruolo di servizio cloud di una coda di messaggi][queue_scale]

>[AZURE.TIP] Ogni volta che viene visualizzato ![][tip_icon] spostare il puntatore del mouse ed è possibile ottenere assistenza sulle indica che una determinata impostazione.


## <a name="scale-linked-resources"></a>Ridimensionare le risorse collegate

Spesso durante il ridimensionamento di un ruolo, è utile ridimensionare il database che utilizza anche l'applicazione. Se il database si collega al servizio cloud, è possibile accedere le impostazioni di ridimensionamento per tale risorsa facendo clic sul collegamento appropriato.

1. Nel [portale classica Azure](https://manage.windowsazure.com/), fare clic su **Servizi Cloud**e quindi fare clic sul nome del servizio cloud per aprire il dashboard.

    > [AZURE.TIP] Se non è presente il servizio cloud, potrebbe essere necessario modificare dalla **produzione** di **gestione temporanea** o viceversa.

2. Fare clic su **scala**.

3. Individuare la sezione **risorse collegate** e fatto clic su **Gestisci scala per il database**.

    > [AZURE.NOTE] Se una sezione **risorse collegate** non è visibile, probabilmente non si dispone tutte le risorse collegate.

![][linked_resource]


[manual_scale]: ./media/cloud-services-how-to-scale/manual-scale.png
[queue_scale]: ./media/cloud-services-how-to-scale/queue-scale.png
[cpu_scale]: ./media/cloud-services-how-to-scale/cpu-scale.png
[tip_icon]: ./media/cloud-services-how-to-scale/tip.png
[scale_schedules]: ./media/cloud-services-how-to-scale/schedules.png
[scale_popup]: ./media/cloud-services-how-to-scale/schedules-dialog.png
[linked_resource]: ./media/cloud-services-how-to-scale/linked-resources.png
