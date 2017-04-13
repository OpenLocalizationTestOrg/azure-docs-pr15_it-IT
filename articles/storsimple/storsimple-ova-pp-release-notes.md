<properties 
   pageTitle="Note sulla versione matrice virtuale StorSimple | Microsoft Azure"
   description="Descrive problemi aperti critici e risoluzioni per la matrice virtuale StorSimple."
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
   ms.date="05/13/2016"
   ms.author="alkohli" />

# <a name="storsimple-virtual-array-release-notes"></a>Note sulla versione matrice virtuale StorSimple

## <a name="overview"></a>Panoramica

Note sulla versione seguenti identificare i problemi critici aperti per la versione di disponibilità generale (GA) marzo 2016 di Microsoft Azure StorSimple virtuale matrice (noto anche come il dispositivo virtuale locale StorSimple o il dispositivo virtuale StorSimple). In questa versione corrisponde alla versione del software 10.0.10271.0.

Note sulla versione vengono continuamente aggiornate e quando vengono rilevati problemi critici che richiedono una possibile soluzione, questi vengono aggiunti. Prima di distribuire il dispositivo virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione. 

La tabella seguente contiene un riepilogo dei problemi noti di questa versione.


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
| **12.** | CHAP | Non è possibile rimuovere le credenziali CHAP dopo averlo create. Inoltre, se si modificano le credenziali CHAP, è necessario disconnettere i volumi e quindi visualizzarle online rendere effettiva la modifica. | Questi verrà risolto in una versione successiva. |
| **13.** | server iSCSI  | La "lo spazio di archiviazione di usati" visualizzato per un volume iSCSI può essere diversi nel servizio StorSimple Manager e host iSCSI. | L'host iSCSI ha la visualizzazione di file System.<br></br>Il dispositivo vede blocchi allocati quando il volume è la dimensione massima.|
