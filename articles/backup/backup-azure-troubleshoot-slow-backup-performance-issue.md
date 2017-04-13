<properties
   pageTitle="Risolvere i problemi di lento backup dei file e cartelle in Azure Backup | Microsoft Azure"
   description="Fornisce indicazioni per la risoluzione dei problemi per diagnosticare la causa di problemi di prestazioni di Backup di Azure"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jimpark"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="genli"/>

# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Risolvere i problemi di lento backup dei file e cartelle in Azure Backup

Questo articolo fornisce indicazioni per la risoluzione dei problemi per diagnosticare la causa del lentezza nelle prestazioni di backup per file e cartelle quando si usa Azure Backup. Quando si utilizza l'agente di Azure Backup per eseguire il backup dei file, il processo di backup può richiedere più tempo del previsto. Il ritardo potrebbe essere causato da uno o più delle operazioni seguenti:

-   [Esistono problemi di prestazioni nel computer in cui viene eseguito il backup.](#cause1)
-   [Un altro processo o il software antivirus interferisce con il processo di Backup di Azure.](#cause2)
-   [L'agente di Backup è in esecuzione su una Azure macchine ().](#cause3)  
-   [Si sta il backup di un numero elevato (milioni) dei file.](#cause4)

Prima di iniziare la risoluzione dei problemi, è consigliabile scaricare e installare l' [ultima agente di Backup di Azure](http://aka.ms/azurebackup_agent). Abbiamo effettuare aggiornamenti frequenti per l'agente di Backup per risolvere i problemi di vari, aggiungere funzionalità e migliorare le prestazioni.

È anche consigliabile leggere le [domande frequenti su servizio di Backup di Azure](backup-azure-backup-faq.md) per assicurarsi che non si verificano i comuni problemi di configurazione.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>
## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: Problemi di prestazioni il computer

Bottiglia nel computer in cui viene eseguito il backup può causare ritardi. Ad esempio, la capacità del computer per leggere o scrivere su disco o larghezza di banda per inviare dati tramite la rete, può causare bottiglia.

Windows offre uno strumento incorporato che è chiamato [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) per rilevare questi rallentamenti.

Ecco alcuni contatori e intervalli che possono essere utili per la diagnosi bottiglia per i backup ottimali.

| Contatore  | Stato  |
|---|---|
|Disco logico (disco fisico)-% inattive   | • 100% idle al 50% inattivo = integro</br>• 49% idle al 20% inattivo = avviso o il Monitor</br>• 19% idle su 0% inattivo = critica o fuori specifiche|
|  Disco logico (disco fisico) - medio % Disco sec o scrittura |  • 0,001 ms per ms 0.015 = integro</br>• 0.015 ms per ms 0.025 = avviso o il Monitor</br>• ms 0.026 o più = critica o fuori specifiche|
|  Disco logico (disco fisico) - lunghezza coda disco corrente (per tutte le istanze) | 80 richieste per più di 6 minuti |
| Memoria: Non di paging byte|• Minore di 60% del pool consumate = integro<br>• 61-80% del pool consumate = avviso o il Monitor</br>• Maggiore pool 80% consumate = critica o fuori specifiche|
| Memoria: Pool di paging |• Minore di 60% del pool consumate = integro</br>• 61-80% del pool consumate = avviso o il Monitor</br>• Maggiore pool 80% consumate = critica o fuori specifiche|
| Memoria: megabyte disponibili| • 50% di memoria o più = integro</br>• 25% della memoria = Monitor</br>• 10% della memoria = avviso</br>• Meno di 100 MB o 5% di memoria disponibile = critica o fuori specifiche|
|Processore -\%tempo processore (tutte le istanze)|• Minore di 60% consumate = integro</br>• 61 a 90% consumate = Monitor o attenzione</br>• 91 al 100% consumate = critica|


> [AZURE.NOTE] Se si determina che l'infrastruttura sia la causa del problema, è consigliabile deframmentare dischi regolarmente per migliorare le prestazioni.

<a id="cause2"></a>
## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: Un altro processo o il software antivirus interferire con Azure Backup

Abbiamo visto più istanze in cui altri processi nel sistema di Windows hanno inciso negativamente le prestazioni del processo di agente di Backup di Azure. Ad esempio, se si utilizza l'agente di Backup di Azure e un altro programma per eseguire il backup dei dati o se il software antivirus è in esecuzione e ha un blocco file di backup, al multiplo blocca nel file potrebbero causare un conflitto. In questo caso, il backup potrebbe non riuscire o il processo può richiedere più tempo del previsto.

In questo scenario consigliato consiste nel disattivare l'altro programma backup per verificare se il tempo di backup per l'agente di Azure Backup cambia. In genere, è sufficiente per impedire che influisce negativamente sulla loro assicurandosi che più processi di backup non è in esecuzione nello stesso momento.

Per le applicazioni antivirus, è consigliabile escludere i seguenti file e le posizioni:

- C:\Programmi\Microsoft c:\Programmi\Microsoft Azure ripristino servizi Agent\bin\cbengine.exe come un processo
- C:\Programmi\Microsoft c:\Programmi\Microsoft Azure ripristino servizi Agent\ cartelle
- Spazio di posizione (se non si usa il percorso standard)

<a id="cause3"></a>
## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: Backup agent installato in un computer virtuale Azure

Se si esegue l'agente di Backup in una macchina virtuale, prestazioni sarà inferiore quando si esegue in un computer fisico. È previsto a causa di limitazioni IOPS.  Tuttavia, è possibile ottimizzare le prestazioni passando le unità di dati che viene eseguito il per lo spazio di archiviazione di Azure Premium. Ci stiamo lavorando correggere il problema e lo strumento Correggi saranno disponibile nelle versioni future.

<a id="cause4"></a>
## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: Backup di un numero elevato (milioni) dei file

Spostamento di una grande quantità di dati richiede più di spostamento di dimensioni inferiori di dati. In alcuni casi, il tempo di backup è correlato a non solo le dimensioni dei dati, ma, anche il numero di file o cartelle. Si tratta soprattutto quando viene eseguito il milioni di file di dimensioni ridotte (alcuni byte a pochi KB).

Questo comportamento è dovuto al fatto che mentre si sta backup dei dati e spostarlo in Azure, Azure contemporaneamente la catalogazione dei file. In alcuni casi rari mediante questa operazione può richiedere più tempo del previsto.

I seguenti indicatori consentono di comprendere la bottiglia e di conseguenza lavorare nella procedura seguente:

- **Interfaccia utente viene visualizzato lo stato di avanzamento per il trasferimento di dati**. I dati vengono comunque vengano trasferiti. La larghezza di banda di rete o le dimensioni dei dati possono causare ritardi.

- **Interfaccia utente non viene visualizzato lo stato di avanzamento per il trasferimento di dati**. Aprire i registri che si trova in C:\Microsoft Azure ripristino servizi Agent\Temp e quindi cercare la voce FileProvider::EndData nei log. Questa voce indica che il trasferimento dei dati completato e avviene mediante questa operazione. Non annullare i processi di backup. Se, tuttavia, attendere leggermente più l'operazione di catalogo da completare. Se il problema persiste, contattare il [supporto di Azure](https://portal.azure.com/#create/Microsoft.Support).
