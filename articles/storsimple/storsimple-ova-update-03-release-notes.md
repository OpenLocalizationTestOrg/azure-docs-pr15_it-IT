<properties 
   pageTitle="Note sulla versione gli aggiornamenti in forma di matrice virtuale StorSimple | Microsoft Azure"
   description="Descrive problemi aperti critici e risoluzioni per la matrice virtuale StorSimple eseguono aggiornamenti 0,3."
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
   ms.date="09/15/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-update-03-release-notes"></a>Note sulla versione 0,3 di aggiornamento in forma di matrice virtuale StorSimple

## <a name="overview"></a>Panoramica

Note sulla versione seguenti identificare i problemi critici aperti e problemi risolti per gli aggiornamenti di Microsoft Azure StorSimple virtuale in forma di matrice.

Note sulla versione vengono continuamente aggiornate e quando vengono rilevati problemi critici che richiedono una possibile soluzione, questi vengono aggiunti. Prima di distribuire la matrice virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

Aggiornamento 0,3 corrisponde alla versione di software **10.0.10288.0**.

> [AZURE.NOTE] Gli aggiornamenti eccedono e riavviare il dispositivo. Se i/o sono in corso, il dispositivo si verrà i tempi di inattività.


## <a name="whats-new-in-the-update-03"></a>Quali sono le novità nell'aggiornamento 0,3

Aggiornamento 0,3 è principalmente una compilazione di correzione dei bug. In questa versione sono stati risolti diversi bug risultante gli errori di backup nella versione precedente.

## <a name="issues-fixed-in-the-update-03"></a>Problemi risolti nell'aggiornamento 0,3

La tabella seguente contiene un riepilogo dei problemi risolti in questa versione.

| No.  | Caratteristica                              | Problema                                                                                                                                                                                                                                                                                                                           |
|------|--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1    | Esecuzione di backup                                |Un problema è stato visualizzato nella versione precedente nel punto in cui i backup sarebbe Impossibile completare per una condivisione file. Se si è verificato il problema, il processo di backup avrà esito negativo e il servizio di gestione StorSimple per informare l'utente è stato generato un avviso critico. Questo problema è stato non interessano i dati in azioni o accedere ai dati. La causa principale è stata identificata e corretta in questa versione. <br></br> Lo strumento Correggi non riguarda retroattivamente condivisioni già vedono il problema. Gli utenti che visualizzano il problema devono prima applicare aggiornamento 0,3, quindi contattare il supporto Microsoft per eseguire un backup completo del sistema per risolvere il problema. Invece di contattare il supporto Microsoft, clienti inoltre possono ripristinare in una condivisione di una copia di backup per le azioni interessate.                                                                                                                                                                                 |
| 2    | iSCSI                         | Un problema è stato visualizzato nella versione precedente nel punto in cui i volumi scompare quando si copiano dati in un volume nella matrice virtuale StorSimple. Questo problema è stato risolto in questa versione. <br></br> Le correzioni effettive solo via appena creato volumi. Le correzioni non si applicano retroattivamente a volumi che già vedono il problema. Si consiglia di portare volumi interessati online tramite il portale classico Azure, eseguire una copia di backup per questi volumi e ripristinare i volumi nuovi volumi.                                                               |


## <a name="known-issues-in-the-update-03"></a>Problemi noti di aggiornamento 0,3

Nella tabella seguente viene fornito un riepilogo dei problemi noti per la matrice virtuale StorSimple e include i problemi rilevati release da versioni precedenti. 


| No. | Caratteristica | Problema | Soluzione alternativa/commenti |
|-----|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1.** | Aggiornamenti | I dispositivi virtuali creati nella versione preview non possono essere aggiornati a una versione supportata di disponibilità generale. | Devono essere non è possibile questi dispositivi virtuali sopra per la versione di disponibilità generale utilizzando un flusso di lavoro di emergenza ripristino (). |
| **2.** | Disco dati provisioning | Dopo avere completato il provisioning un disco di dati di una certa dimensione specificata e creato il dispositivo virtuale StorSimple corrispondente, è necessario non espandere o ridurre il disco di dati. Il tentativo di eseguire i risultati di una perdita di tutti i dati nei livelli locali del dispositivo. |   |
| **3.** | Criteri di gruppo | Quando il dispositivo è dominio, l'applicazione dei criteri di gruppo può influire negativamente sull'operazione di dispositivo. | Assicurarsi che la matrice virtuale nella propria unità organizzativa (OU) per Active Directory e nessun gruppo oggetti criteri () vengono applicati a tale.|
| **4.** | Interfaccia utente web locale | In Internet Explorer (IE ESC) sono abilitate le caratteristiche di protezione avanzata, alcune pagine dell'interfaccia utente web locale, ad esempio risoluzione dei problemi o manutenzione potrebbero non funzionare correttamente. Pulsanti in queste pagine anche potrebbero non funzionare. | Disattivare le funzionalità di sicurezza avanzata in Internet Explorer.|
| **5.** | Interfaccia utente web locale | In una macchina virtuale Hyper-V, le interfacce di rete del Web dell'interfaccia utente vengono visualizzati come 10 GB/s interfacce. | Questo comportamento risulta un riflesso di Hyper-V. Hyper-V Mostra sempre 10 GB/s per schede di rete virtuale. |
| **6.** | Condivisioni o volumi a più livelli | Intervallo di byte blocco per le applicazioni compatibili con StorSimple volumi a più livelli non è supportato. Se il blocco di byte intervallo è attivato, StorSimple collegamento non funziona. | Misure consigliate seguenti: <br></br>Disattivare l'intervallo di byte blocco la logica dell'applicazione.<br></br>Scegliere di inserire i dati dell'applicazione in volumi localmente bloccati anziché volumi a più livelli.<br></br>*Avvertenza*: se utilizzando localmente aggiunte volumi e byte intervallo blocco è attivato, il volume localmente bloccato può essere online anche prima del ripristino. In questi casi, se il ripristino non è in corso, quindi è necessario attendere il ripristino completare. |
| **7.** | Azioni a più livelli | Utilizzo dei file di grandi dimensioni potrebbe causare lenta livello indietro. | Quando si lavora con file di grandi dimensioni, è consigliabile che il file più grande è minore di 3% delle dimensioni della condivisione. |
| **8.** | Utilizzata capacità per condivisioni | Può vedere condividere consumo quando non contiene dati presenti nella condivisione. In questo modo la capacità utilizzata per le azioni include i metadati. |   |
| **9.** | Ripristino di emergenza | È possibile eseguire solo il ripristino di un file server nello stesso dominio del dispositivo di origine. Ripristino di emergenza a un dispositivo di destinazione in un altro dominio non è supportato in questa versione. | Questo è implementato in una versione successiva. |
| **10.** | PowerShell Azure | Non è possibile gestire i dispositivi virtuali StorSimple tramite PowerShell Azure in questa versione. | Gestione dei dispositivi virtuale deve essere eseguita tramite il portale classico Azure e il web locale dell'interfaccia utente. |
| **11.** | Modifica della password | Console di dispositivo in forma di matrice virtuale accetta solo input nel formato di tastiera en-US. |   |
| **12.** | CHAP | Non è possibile rimuovere le credenziali CHAP dopo averlo create. Inoltre, se si modificano le credenziali CHAP, è necessario disconnettere i volumi e quindi visualizzarle online rendere effettiva la modifica. | Questo problema viene risolto in una versione successiva. |
| **13.** | server iSCSI  | La "lo spazio di archiviazione di usati" visualizzato per un volume iSCSI può essere diversi nel servizio StorSimple Manager e host iSCSI. | L'host iSCSI ha la visualizzazione di file System.<br></br>Il dispositivo vede blocchi allocati quando il volume è la dimensione massima.|
| **14.** | Server di file  | Se un file in una cartella ha un alternativo flusso di dati (annunci) è associato, gli annunci non backup o ripristino tramite il ripristino di emergenza, duplicato e il ripristino di livello di elemento.| |


## <a name="next-step"></a>Passaggio successivo

[Installare l'aggiornamento 0,3](storsimple-ova-install-update-01.md) nella matrice virtuale StorSimple.

## <a name="references"></a>Riferimenti

Si cerca una note sulla versione di versioni precedenti? Vai a: 

- [Note sulla versione di aggiornamento di matrice virtuale StorSimple 0,1 e 0,2](storsimple-ova-update-01-release-notes.md)

- [Note sulla versione disponibilità generale in forma di matrice virtuale StorSimple](storsimple-ova-pp-release-notes.md)
 

