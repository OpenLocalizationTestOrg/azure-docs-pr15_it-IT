<properties
pageTitle="Come aggiornare un servizio cloud | Microsoft Azure"
description="Informazioni su come aggiornare servizi cloud in Azure. Informazioni su come un aggiornamento su un servizio cloud procede per assicurare la disponibilità."
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
ms.date="08/10/2016"
ms.author="adegeo"/>

# <a name="how-to-update-a-cloud-service"></a>Come aggiornare un servizio cloud

## <a name="overview"></a>Panoramica
10.000 piedi, l'aggiornamento di un servizio cloud, inclusi i ruoli e sistema operativo, guest è tre passaggi. Prima di tutto necessario caricati i file binari e i file di configurazione per il nuovo servizio cloud o una versione del sistema operativo. Successivamente, Azure riserva risorse di calcolo e di rete per il servizio cloud in base ai requisiti della nuova versione di servizio cloud. Infine, Azure esegue un aggiornamento per aggiornare in modo incrementale tenant alla nuova versione o guest OS, mantenendo la propria disponibilità. In questo articolo vengono illustrati i dettagli di quest'ultimo passaggio, l'aggiornamento in sequenza.

## <a name="update-an-azure-service"></a>Aggiornare un servizio di Azure

Azure consente di organizzare le istanze del ruolo in raggruppamenti logici definiti domini aggiornamento (UD). Aggiornamento domini (UD) sono insiemi logici di istanze del ruolo che vengono aggiornate come gruppo.  Azure aggiornamenti al cloud service uno UD alla volta, consentendo agli altri UDs per continuare a esercitare il traffico istanze.

Il numero predefinito di domini aggiornamento è "5". È possibile specificare un numero differente di domini aggiornamento includendo l'attributo upgradeDomainCount nel file di definizione del servizio (.csdef). Per ulteriori informazioni sull'attributo upgradeDomainCount, vedere [WebRole Schema](https://msdn.microsoft.com/library/azure/gg557553.aspx) o [WorkerRole Schema](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Quando si esegue un aggiornamento sul posto di uno o più ruoli nel servizio di posta, Azure Aggiorna insiemi di istanze del ruolo in base alla quale appartengono l'aggiornamento del dominio. Aggiornamenti Azure tutte le istanze in un determinato dominio aggiornamento – per evitare, aggiornarli, caricarli loro di eseguire il backup, passa quindi il dominio successivo. Per arrestare solo le istanze in esecuzione nel dominio di aggiornamento corrente, Azure garantisce che un aggiornamento si verifica con un impatto minimo per il servizio in esecuzione. Per ulteriori informazioni, vedere [come procede l'aggiornamento](#howanupgradeproceeds) più avanti in questo articolo.

> [AZURE.NOTE] Mentre le condizioni per l' **aggiornamento** e **all'aggiornamento** hanno un significato leggermente diverso nel contesto di Azure, possono essere utilizzati in modo intercambiabile per i processi e descrizioni delle caratteristiche di questo documento.

Il servizio necessario definire almeno due istanze di un ruolo per tale ruolo per l'aggiornamento sul posto senza tempi di inattività. Se il servizio include una sola istanza di un ruolo, il servizio non sarà disponibile fino al completamento dell'aggiornamento sul posto.

In questo argomento illustra le seguenti informazioni sugli aggiornamenti Azure:

-   [Le modifiche del servizio è consentito durante un aggiornamento](#AllowedChanges)
-   [Come procede un aggiornamento](#howanupgradeproceeds)
-   [Ripristino di un aggiornamento](#RollbackofanUpdate)
-   [Avvio di più operazioni mutating in una distribuzione in corso](#multiplemutatingoperations)
-   [Distribuzione dei ruoli in domini di aggiornamento](#distributiondfroles)

<a name="AllowedChanges"></a>
## <a name="allowed-service-changes-during-an-update"></a>Le modifiche del servizio è consentito durante un aggiornamento
Nella tabella seguente mostra le modifiche consentite a un servizio durante un aggiornamento:

|Modifiche consentite a hosting, servizi e ruoli|Aggiornamento sul posto|A fasi (scambio VIP)|Elimina e ridistribuire|
|---|---|---|---|
|Versione del sistema operativo|Sì|Sì|Sì
|Livello di protezione .NET|Sì|Sì|Sì|
|Macchina virtuale dimensioni<sup>1</sup>|Sì,<sup>2</sup>|Sì|Sì|
|Impostazioni di archiviazione locale|Aumentare solo<sup>2</sup>|Sì|Sì|
|Aggiungere o rimuovere i ruoli in un servizio|Sì|Sì|Sì|
|Numero di istanze di un particolare ruolo|Sì|Sì|Sì|
|Numero o un tipo di endpoint di un servizio|Sì,<sup>2</sup>|No|Sì|
|Nomi e i valori delle impostazioni di configurazione|Sì|Sì|Sì|
|I valori (ma non nomi) delle impostazioni di configurazione|Sì|Sì|Sì|
|Aggiungere nuovi certificati|Sì|Sì|Sì|
|Modificare i certificati esistenti|Sì|Sì|Sì|
|Distribuire nuovo codice|Sì|Sì|Sì|
<sup>1</sup> Modifica della dimensione limitata il subset di dimensioni disponibili per il servizio cloud.

<sup>2</sup> Richiede Azure SDK 1.5 o versioni successive.

> [AZURE.WARNING] Modificare la dimensione macchina virtuale comporta l'eliminazione di dati locali.


Gli elementi seguenti non sono supportati durante un aggiornamento:

-   Modifica del nome di un ruolo. Rimuovere e quindi aggiungere il ruolo con il nuovo nome.
-   Modifica del conteggio aggiornamento del dominio.
-   Ridurre le dimensioni delle risorse locali.

Se invece si creano altri aggiornamenti alla definizione del servizio, ad esempio riducendo le dimensioni della risorsa locale, è necessario eseguire un aggiornamento di scambio VIP invece. Per ulteriori informazioni, vedere [Scambio di distribuzione](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>
## <a name="how-an-upgrade-proceeds"></a>Come procede un aggiornamento
È possibile decidere se si desidera aggiornare tutti i ruoli del servizio o un singolo ruolo nel servizio. In entrambi i casi, tutte le istanze di ogni ruolo in corso l'aggiornamento e appartengono al primo dominio aggiornamento sono arrestate aggiornate e riconnessione. Una volta in linea, le istanze del secondo dominio aggiornamento arrestate, aggiornate e riconnessione. Un servizio cloud può includere al massimo un aggiornamento contemporaneamente. L'aggiornamento viene eseguito sempre la versione più recente del servizio cloud.

Il diagramma seguente illustra come l'aggiornamento procede se si esegue l'aggiornamento a tutti i ruoli nel servizio:

![Eseguire l'aggiornamento del servizio] (media/cloud-services-update-azure-service/IC345879.png "Eseguire l'aggiornamento del servizio")

Il diagramma seguente illustra come l'aggiornamento procede se si sta aggiornando un singolo ruolo:

![Ruolo di aggiornamento] (media/cloud-services-update-azure-service/IC345880.png "Ruolo di aggiornamento")  

> [AZURE.NOTE] Durante l'aggiornamento di un servizio da una singola istanza a più istanze del servizio verrà inserito verso il basso durante l'aggiornamento viene eseguita a causa di Azure aggiornamenti dei servizi. La disponibilità di servizio servizio garante contratto solo per i servizi distribuiti con più di un'istanza. L'elenco seguente descrive i dati in ogni unità effetto su ogni scenario di aggiornamento del servizio Azure:
>
>Riavviare il computer macchine Virtuali:
>
-   C: mantenuti
-   D: mantenuti
-   E: mantenuti
>
>Riavviare il computer portale:
>
-   C: mantenuti
-   D: mantenuti
-   E: eliminazione
>
>Portale creare una nuova immagine:
>
-   C: mantenuti
-   D: eliminato
-   E: eliminazione

>Aggiornamento sul posto:
>
-   C: mantenuti
-   D: mantenuti
-   E: eliminazione
>
>Migrazione di nodo:
>
-   C: eliminato
-   D: eliminato
-   E: eliminazione

>Si noti che, nell'elenco precedente, l'unità e: rappresenta unità principale del ruolo e non deve essere hardcoded. Utilizzare invece la variabile di ambiente % RoleRoot % per rappresentare le unità.

>Per ridurre al minimo il tempo di inattività durante l'aggiornamento di un servizio singola istanza, distribuire un nuovo servizio di più istanze al server di gestione temporaneo ed eseguire scambio VIP.

Durante l'aggiornamento automatico, il Controller dell'infrastruttura di Azure valuta periodicamente lo stato del servizio cloud per determinare quando è consigliabile esaminare UD successivo. La valutazione dell'integrità viene eseguita in base al ruolo e vengono tenuti in considerazione solo le istanze nella versione più recente (ad esempio le istanze da UDs sono già state scorrere). Consente di verificare che un numero minimo di istanze del ruolo, per ogni ruolo, hanno ottenuto un buono stato terminal.

### <a name="role-instance-start-timeout"></a>Timeout Start istanza ruolo
Il Controller dell'infrastruttura di attesa 30 minuti per ogni istanza del ruolo raggiungere uno stato avviato. Se l'intervallo di timeout, il Controller dell'infrastruttura continuerà alcuni all'istanza successiva di ruolo.

<a name="RollbackofanUpdate"></a>
## <a name="rollback-of-an-update"></a>Ripristino di un aggiornamento
Azure offre flessibilità nella gestione dei servizi durante un aggiornamento grazie alla possibilità di avviare operazioni aggiuntive su un servizio, dopo la richiesta di aggiornamento iniziale viene accettata per il Controller dell'infrastruttura di Azure. È possibile eseguire un ripristino solo quando un aggiornamento (modifica della configurazione) o aggiornamento è stato **in corso** sulla distribuzione. Un aggiornamento o l'aggiornamento viene considerato in corso, purché sia presente almeno un'istanza del servizio che non è ancora stato aggiornato alla nuova versione. Per verificare se è consentito un ripristino, controllare il valore del contrassegno RollbackAllowed restituito da operazioni di [Distribuzione accedere](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Ottenere le proprietà delle servizio Cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx) , è impostato su true.

> [AZURE.NOTE] Solo opportuno chiamare ripristino di un aggiornamento **sul posto** o aggiornare perché gli aggiornamenti di scambio di VIP prevedere la sostituzione di un'intera istanza di esecuzione del servizio con un altro.

Ripristino di un aggiornamento in corso è i seguenti effetti sulla distribuzione:

-   Tutte le istanze di ruolo che avevano non ancora aggiornate o aggiornate alla nuova versione non aggiornate o aggiornate, in quanto tali istanze già in esecuzione la versione di destinazione del servizio.
-   Tutte le istanze di ruolo ricoperto dai partecipanti che erano già stato o aggiornare alla nuova versione del pacchetto service (\*cspkg) file o la configurazione del servizio (\*cscfg) file (o entrambi i file) vengono annullati alla versione pre-aggiornamento di questi file.

Questa funzionalità viene fornita dalle caratteristiche seguenti:

-   L'operazione [Ripristino aggiornare o eseguire l'aggiornamento](https://msdn.microsoft.com/library/azure/hh403977.aspx) , che può essere chiamato su un aggiornamento della configurazione (attivato dalla chiamata [Configurazione di distribuzione di modifica](https://msdn.microsoft.com/library/azure/ee460809.aspx)) o un aggiornamento (attivato dalla chiamata [Distribuzione aggiornamento](https://msdn.microsoft.com/library/azure/ee460793.aspx)), purché sia presente almeno un'istanza nel servizio che non è ancora stato aggiornato alla nuova versione.
-   L'elemento bloccata e l'elemento RollbackAllowed, che vengono restituiti come parte del corpo della risposta delle operazioni di [Distribuzione accedere](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Ottenere le proprietà delle servizio Cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx) :
    1.  L'elemento bloccata consente di rilevare quando un'operazione di mutating può essere richiamata su una determinata distribuzione.
    2.  L'elemento RollbackAllowed consente di rilevare quando l'operazione di [Ripristino Update o l'aggiornamento](https://msdn.microsoft.com/library/azure/hh403977.aspx) può essere chiamato in una distribuzione specificata.

    Per eseguire un ripristino dello stato precedente, non è necessario verificare il bloccata e gli elementi RollbackAllowed. È sufficiente per verificare che RollbackAllowed sia impostato su true. Questi elementi vengono restituiti solo se questi metodi vengono richiamati mediante l'intestazione della richiesta impostata su "x-ms-versione: 2011-10-01" o versione successiva. Per ulteriori informazioni sulle intestazioni di controllo delle versioni, vedere [Servizio di gestione delle versioni](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Esistono alcune situazioni in cui un ripristino di un aggiornamento o aggiornamento non è supportato, questi sono i seguenti:

-   Riduzione delle risorse locali - se l'aggiornamento aumenta le risorse locali per un ruolo di piattaforma Azure non consente di eseguire nuovamente. 
-   Limiti di quota - se l'aggiornamento è una scala verso il basso operazione potrebbe non dispone di sufficienti quota di calcolo per completare l'operazione di ripristino. Ogni abbonamento Azure ha una quota associata che specifica il numero massimo di core che può essere utilizzato da tutti i servizi di hosting che appartengono a tale abbonamento. Eseguire un ripristino di un aggiornamento specificato da posizionare l'abbonamento quota quindi che un ripristino non verrà attivato.
-   Condizione di competizione - se è stata completata l'aggiornamento iniziale, un ripristino non è possibile.

Esempio di quando il ripristino di un aggiornamento può essere utile è se si utilizza l'operazione di [Aggiornamento distribuzione](https://msdn.microsoft.com/library/azure/ee460793.aspx) in modalità manuale per controllare la frequenza con cui un aggiornamento sul posto principale per il Azure ospitato servizio è stata implementata.

Durante l'implementazione dell'aggiornamento si chiama [Distribuzione l'aggiornamento](https://msdn.microsoft.com/library/azure/ee460793.aspx) in modalità manuale e iniziare a eseguire l'aggiornamento di domini. Se un certo punto, come si monitorare l'aggiornamento, prendere nota alcune istanze del ruolo dei domini di aggiornamento prima che si esamina dispongano di rispondere, è possibile chiamare l'operazione di [Ripristino aggiornamento o aggiornato](https://msdn.microsoft.com/library/azure/hh403977.aspx) nella distribuzione, che verrà lasciare invariate le istanze che non ha ancora aggiornate e le istanze di ripristino che erano state aggiornate per il pacchetto di servizio precedente e la configurazione.

<a name="multiplemutatingoperations"></a>
## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Avvio di più operazioni mutating in una distribuzione in corso
In alcuni casi può essere necessario avviare più operazioni mutating simultanee in una distribuzione in corso. Ad esempio, è possibile eseguire un aggiornamento di servizio e, durante l'aggiornamento software attraverso il servizio, si desidera apportare qualche modifica, ad esempio, per distribuire nuovamente l'aggiornamento, applicare un aggiornamento diverso o eliminare la distribuzione. Un caso in cui ciò potrebbe essere necessario è se un aggiornamento del servizio contiene codice anomalo che causa un'istanza di ruolo aggiornato in modo anomalo più volte. In questo caso, il Controller dell'infrastruttura di Azure non sarà possibile verificare lo stato di avanzamento per l'applicazione che l'aggiornamento poiché un numero di istanze nel dominio aggiornato insufficiente è corretto. Questo stato viene definito una *distribuzione bloccato*. È possibile sbloccare la distribuzione ripristino dell'aggiornamento o applicandovi un aggiornamento aggiornato sopra l'errore uno.

Dopo la richiesta iniziale per aggiornare o aggiornare il servizio è stata ricevuta tramite il Controller dell'infrastruttura di Azure, è possibile iniziare le operazioni di modifica successive. Ovvero non è necessario attendere il completamento prima di poter iniziare un'altra operazione mutating dell'operazione iniziale.

Avvio di un'operazione di aggiornamento seconda mentre è in corso l'aggiornamento prima verrà eseguita automaticamente simile all'operazione di ripristino. Se il secondo aggiornamento è in modalità automatica, l'aggiornamento del primo dominio verrà aggiornata immediatamente, causando in istanze di da più domini di aggiornamento in linea nello stesso punto nel tempo.

Operazioni mutating sono i seguenti: [Configurazione di distribuzione di modifica](https://msdn.microsoft.com/library/azure/ee460809.aspx), [L'aggiornamento distribuzione](https://msdn.microsoft.com/library/azure/ee460793.aspx), [Lo stato di distribuzione](https://msdn.microsoft.com/library/azure/ee460808.aspx), [Eliminare distribuzione](https://msdn.microsoft.com/library/azure/ee460815.aspx)e [Ripristino Update o l'aggiornamento](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Due operazioni, [Distribuzione accedere](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Ottenere le proprietà delle servizio Cloud](https://msdn.microsoft.com/library/azure/ee460806.aspx), restituiscono il flag bloccata che può essere esaminato per determinare se è possibile chiamare un'operazione di mutating in una distribuzione specificata.

Per chiamare la versione di questi metodi che restituisce il contrassegno bloccato, è necessario impostare intestazione richiesta "x-ms-versione: 2011-10-01" o una versione successiva. Per ulteriori informazioni sulle intestazioni di controllo delle versioni, vedere [Servizio di gestione delle versioni](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>
## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuzione dei ruoli in domini di aggiornamento
Azure consente di distribuire le istanze di un ruolo uniformemente un numero prestabilito di domini aggiornamento, che può essere configurato come parte del file di definizione (.csdef) del servizio. Il numero massimo di domini aggiornamento è 20 e il valore predefinito è 5. Per ulteriori informazioni su come modificare il file di definizione del servizio, vedere [Schema di definizione del servizio di Azure (.csdef File)](cloud-services-model-and-package.md#csdef).

Ad esempio, se il proprio ruolo dispone dieci istanze, per impostazione predefinita ogni aggiornamento del dominio contiene due istanze. Se il proprio ruolo dispone di 14 varianti, quindi quattro domini aggiornamento contengono tre istanze e un dominio quinto contiene due.

Aggiornamento domini sono contrassegnati da zero: il dominio prima di eseguire l'aggiornamento ha un ID pari a 0 e l'aggiornamento del dominio secondo ha un ID 1 e così via.

Il diagramma seguente illustra la distribuzione di un servizio che contiene due ruoli quando il servizio definisce aggiornamento domini. Il servizio è in esecuzione otto istanze del ruolo web e nove istanze del ruolo di lavoro.

![Distribuzione di domini aggiornamento] (media/cloud-services-update-azure-service/IC345533.png "Distribuzione di domini aggiornamento")

> [AZURE.NOTE] Si noti che Azure controlli assegnazione istanze domini aggiornamento. Non è possibile specificare quali istanze sono allocata per il dominio.

## <a name="next-steps"></a>Passaggi successivi
[Come gestire servizi Cloud](cloud-services-how-to-manage.md)  
[Come monitorare i servizi Cloud](cloud-services-how-to-monitor.md)  
[Come configurare i servizi Cloud](cloud-services-how-to-configure.md)  
