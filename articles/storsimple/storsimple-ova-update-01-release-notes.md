<properties 
   pageTitle="Note sulla versione di aggiornamenti in forma di matrice virtuale StorSimple | Microsoft Azure"
   description="Descrive problemi aperti critici e risoluzioni per la matrice virtuale StorSimple eseguono aggiornamenti 0,2 e 0,1."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/16/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>Note sulla versione virtuale matrice Update 0,2 0,1 StorSimple

## <a name="overview"></a>Panoramica

Note sulla versione seguenti identificare i problemi critici aperti e problemi risolti per gli aggiornamenti di Microsoft Azure StorSimple virtuale in forma di matrice. (Matrice virtuale di Microsoft Azure StorSimple è noto anche come il dispositivo virtuale locale StorSimple o il dispositivo virtuale StorSimple) 

Note sulla versione vengono continuamente aggiornate e quando vengono rilevati problemi critici che richiedono una possibile soluzione, questi vengono aggiunti. Prima di distribuire il dispositivo virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

Aggiornamento 0,2 corrisponde alla versione del software **10.0.10280.0**; Aggiornamento 0,1 è versione **10.0.10279.0**. Nelle sezioni seguenti elencano le modifiche apportate per ogni aggiornamento. 

> [AZURE.NOTE] Gli aggiornamenti eccedono e riavvio del dispositivo. Se i/o sono in corso, il dispositivo causeranno il tempo di inattività.

## <a name="issues-fixed-in-the-update-02"></a>Problemi risolti nell'aggiornamento 0,2
Aggiornamento 0,2 include tutte le modifiche apportate da Update 0,1 oltre la correzione descritta nella tabella riportata di seguito:

Caratteristica                              | Problema                                                                                                                                                                                                                                                                                                                           |
--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
Aggiornamenti                                 | Nella versione precedente, non sono state rilevati aggiornamenti automaticamente nel portale di classica Azure, in modo che era necessario utilizzare l'interfaccia utente Web locale per installare gli aggiornamenti. Questo problema viene risolto in questa versione. Dopo aver installato l'aggiornamento 0,2, è possibile installare aggiornamenti futuri tramite il portale classico Azure.                       

## <a name="whats-new-in-the-update-01"></a>Quali sono le novità nell'aggiornamento 0,1

Aggiornamento 0,1 contiene le correzioni di bug e i miglioramenti seguenti. 

- **Maggiore flessibilità per le interruzioni cloud**: in questa versione è state apportate diverse correzioni all'interno di emergenza, backup, ripristino e livelli diversi in caso di un'interruzione di integrazione applicativa cloud. 

- **Miglioramenti ripristinare le prestazioni**: in questa versione è correzioni di bug notevolmente ridotta l'ora di completamento dei processi di ripristino.

- **Ottimizzazione di recupero automatico dello spazio**: quando i dati vengono eliminati volumi eseguono provisioning, è necessario essere recuperata blocchi inutilizzata. In questa versione è state migliorate il processo di rigenerazione spazio dal cloud risultante nello spazio inutilizzato diventi disponibile più velocemente rispetto alle versioni precedenti.

- **Nuove immagini virtuale disco**: nuovo disco rigido virtuale, VHDX e VMDK sono ora disponibili tramite il portale classico Azure. È possibile scaricare le immagini per effettuare il provisioning di nuovi dispositivi aggiornamento 0,1.

- **Migliorare la precisione di stato dei processi nel portale**: nella versione precedente del software, lo stato dei processi dei report nel portale non granulare. Questo problema viene risolto in questa versione.

- **Esperienza utente di aggiunta al dominio**: correzioni di Bug correlati a partecipare a dominio e la ridenominazione del dispositivo.


## <a name="issues-fixed-in-the-update-01"></a>Problemi risolti nell'aggiornamento 0,1

La tabella seguente contiene un riepilogo dei problemi risolti in questa versione.

| No.  | Caratteristica                              | Problema                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | VMDK                                 | In alcune versioni di VMware, il disco del sistema operativo è stato considerato come sparse causa avvisi e interrompere il normale funzionamento. Questo è stato risolto in questa versione.                                                                                                                                                                                    |
| 2    | server iSCSI                         | Nella versione precedente, l'utente è stato richiesto di specificare un gateway per ogni interfaccia di rete del dispositivo virtuale StorSimple. Questo problema è stato modificato in questa versione è necessario configurare almeno un gateway per tutte le interfacce di rete dell'utente.                                                                              |
| 3    | Pacchetto di supporto                      | Nella versione precedente del software, insieme pacchetto di supporto non riuscita quando le dimensioni di pacchetto erano superiori a 1 GB. Questo problema viene risolto in questa versione.                                                                                                                                                                               |
| 4    | Accesso cloud                         |  Nella versione precedente, se la matrice virtuale StorSimple non dispone la connettività di rete ed è stata riavviata, l'interfaccia utente locale avrebbe problemi di connettività. Questo problema viene risolto in questa versione.                                                                                                                            |
| 5    | Grafici di monitoraggio                    | Nella versione precedente, successive all'errore di dispositivo, i grafici di utilizzo della capacità cloud visualizzati valori non corretti nel portale di classica Azure. Questo problema viene risolto nella versione corrente.                                                                                                                          |



## <a name="known-issues-in-the-update-01"></a>Problemi noti di aggiornamento 0,1

Nella tabella seguente viene fornito un riepilogo dei problemi noti per la matrice virtuale StorSimple e include i problemi rilevati release da versioni precedenti. **La versione di problemi indicata in questa versione sono contrassegnati con un asterisco. Quasi tutti i problemi di questo elenco sono trasferita dalla versione GA di matrice virtuale StorSimple.**


| No. | Caratteristica | Problema | Soluzione alternativa/commenti |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Aggiornamenti | I dispositivi virtuali creati nella versione preview non possono essere aggiornati a una versione supportata di disponibilità generale. | Devono essere non è possibile questi dispositivi virtuali sopra per la versione di disponibilità generale utilizzando un flusso di lavoro di emergenza ripristino (). |
| **2.** | Disco dati provisioning | Dopo avere completato il provisioning un disco di dati di una certa dimensione specificata e creato il dispositivo virtuale StorSimple corrispondente, è necessario non espandere o ridurre il disco di dati. Se si tenta di farlo genereranno una perdita di tutti i dati nei livelli locali del dispositivo. |   |
| **3.** | Criteri di gruppo | Quando il dispositivo è dominio, l'applicazione dei criteri di gruppo può influire negativamente sull'operazione di dispositivo. | Assicurarsi che la matrice virtuale nella propria unità organizzativa (OU) per Active Directory e non dei criteri di gruppo (gruppo) vengono applicati a tale.|
| **4.** | Interfaccia utente web locale | In Internet Explorer (IE ESC) sono abilitate le caratteristiche di protezione avanzata, alcune pagine dell'interfaccia utente web locale, ad esempio risoluzione dei problemi o manutenzione potrebbero non funzionare correttamente. Pulsanti in queste pagine anche potrebbero non funzionare. | Disattivare le funzionalità di sicurezza avanzata in Internet Explorer.|
| **5.** | Interfaccia utente web locale | In una macchina virtuale Hyper-V, le interfacce di rete del Web dell'interfaccia utente vengono visualizzati come 10 GB/s interfacce. | Questo comportamento risulta un riflesso di Hyper-V. Hyper-V Mostra sempre 10 GB/s per schede di rete virtuale. |
| **6.** | Condivisioni o volumi a più livelli | Intervallo di byte blocco per le applicazioni compatibili con StorSimple volumi a più livelli non è supportato. Se il blocco di byte intervallo è attivato, StorSimple collegamento non funzionerà. | Misure consigliate seguenti: <br></br>Disattivare l'intervallo di byte blocco la logica dell'applicazione.<br></br>Scegliere di inserire i dati dell'applicazione in volumi localmente bloccati anziché volumi a più livelli.<br></br>*Avvertenza*: se utilizzando localmente aggiunte volumi e il blocco di byte intervallo è attivato, tenere presente che il volume locale bloccato possa essere online anche prima del ripristino. In questi casi, se il ripristino non è in corso, quindi è necessario attendere il ripristino completare. |
| **7.** | Azioni a più livelli | Utilizzo dei file di grandi dimensioni potrebbe causare lenta livello indietro. | Quando si lavora con file di grandi dimensioni, è consigliabile che il file più grande è minore di 3% delle dimensioni della condivisione. |
| **8.** | Utilizzata capacità per condivisioni | Può vedere condividere consumo in assenza di tutti i dati presenti nella condivisione. In questo modo la capacità usata per azioni include i metadati. |   |
| **9.** | Ripristino di emergenza | È possibile eseguire solo il ripristino di un file server nello stesso dominio del dispositivo di origine. Ripristino di emergenza a un dispositivo di destinazione in un altro dominio non è supportato in questa versione. | Questo sarà implementato in una versione successiva. |
| **10.** | PowerShell Azure | Non è possibile gestire i dispositivi virtuali StorSimple tramite PowerShell Azure in questa versione. | Gestione dei dispositivi virtuale deve essere eseguita tramite il portale classico Azure e il web locale dell'interfaccia utente. |
| **11.** | Modifica della password | Console di dispositivo in forma di matrice virtuale accetta solo input nel formato di tastiera en-US. |   |
| **12.** | CHAP | Non è possibile rimuovere le credenziali CHAP dopo averlo create. Inoltre, se si modificano le credenziali CHAP, sarà necessario utilizzare i volumi non in linea e quindi visualizzarle online rendere effettiva la modifica. | Questi verrà risolto in una versione successiva. |
| **13.** | server iSCSI  | La "lo spazio di archiviazione di usati" visualizzato per un volume iSCSI può essere diversi nel servizio StorSimple Manager e host iSCSI. | L'host iSCSI ha la visualizzazione di file System.<br></br>Il dispositivo vede blocchi allocati quando il volume è la dimensione massima.|
| **14.** | File server *  | Se un file in una cartella ha un alternativo flusso di dati (annunci) è associato, gli annunci non backup o ripristino tramite il ripristino di emergenza, duplicato e il ripristino di livello di elemento.| |


## <a name="next-step"></a>Passaggio successivo

[Installare gli aggiornamenti](storsimple-ova-install-update-01.md) nella matrice virtuale StorSimple.
