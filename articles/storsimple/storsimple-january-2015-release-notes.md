<properties 
   pageTitle="Note sulla versione 0,2 di aggiornamento 8000 StorSimple | Microsoft Azure"
   description="Descrive le nuove funzionalità e le correzioni, problemi aperti e soluzioni alternative disponibili per gennaio 2015 sulla versione di Microsoft Azure StorSimple (aggiornamento 0,2)."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />


# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>Note sulla versione 0,2 di aggiornamento di serie 8000 StorSimple - gennaio 2015

## <a name="overview"></a>Panoramica

Note sulla versione seguenti identificare i problemi critici aperti per la versione di gennaio 2015 di Microsoft Azure StorSimple. Contengono anche un elenco degli aggiornamenti di software e firmware del StorSimple inclusi in questa versione. Questa è la versione seconda dopo la StorSimple 8000 serie versione è stata effettuata in genere disponibile in luglio 2014.
  
Questo aggiornamento non modifica il software o dispositivo fisico dall'aggiornamento ottobre. Continua a essere versione 6.3.9600.17312. In questa versione è stato modificato l'immagine da utilizzare per l'immagine del dispositivo virtuale. Di conseguenza, tutti i dispositivi virtuali nuovi creati dopo 20/1/2015 verranno visualizzata la versione come 6.3.9600.17361.  

Esaminare le seguenti informazioni contenute nelle note sulla versione per l'aggiornamento di gennaio 2015.

> [AZURE.IMPORTANT]  
>
>- Questo aggiornamento non è disponibile in Windows Update e non può essere installato ad altri aggiornamenti. Il dispositivo non riceveranno questo aggiornamento anche se è stato applicato gli aggiornamenti tramite il portale classico Azure. Questo aggiornamento verrà applicata solo ai dispositivi virtuali creati dopo il 20 gennaio 2015. 
> 
>- La versione di gennaio di StorSimple non contiene tutti gli aggiornamenti al dispositivo fisico StorSimple. È ancora possibile applicare gli aggiornamenti disponibili per Windows al dispositivo virtuale, inclusi correzioni recenti, ma non si vedranno una modifica nella versione per il dispositivo fisico StorSimple.

## <a name="whats-new-in-the-january-release"></a>Quali sono le novità della versione di gennaio

Questo aggiornamento contiene una correzione relativa ai volumi lavorare offline su dispositivo virtuale. Per vedere [i problemi risolti in questa versione](#issues-fixed-in-the-january-release).  

L'aggiornamento non contiene nuove caratteristiche o funzionalità.  

## <a name="issues-fixed-in-the-january-release"></a>Problemi risolti nella versione gennaio

Nella tabella seguente descrive il problema corretti in questo aggiornamento.

|No.|Caratteristica|Problema|Si applica a dispositivo fisico|Si applica a dispositivo virtuale 
|---|-------|-----|--------------------------|-------------------------
|1|Volumi passare alla modalità offline|Quando viene conservata latenza elevata cloud per alcuni minuti, i volumi periferica virtuale StorSimple accesso offline su host. Questa correzione aumenta la soglia per latenza cloud, riducendo le situazioni che potrebbero causare i volumi per l'accesso offline su host.|No|Sì  

## <a name="known-issues-in-the-january-release"></a>Problemi noti della versione di gennaio

La tabella seguente contiene un riepilogo dei problemi noti di questa versione.
 
|No.|Caratteristica|Problema|Commenti/soluzione|Si applica a dispositivo fisico|Si applica a dispositivo virtuale 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1| Reimposta factory|  In alcuni casi, quando si esegue il ripristino di factory, il dispositivo StorSimple potrebbe essere bloccato e visualizzare il messaggio seguente: **Reimposta factory è in corso (fase 8).** Si verifica questo evento se si preme CTRL + C, mentre il cmdlet è in corso.| Dopo aver iniziato la reimpostazione factory non premere CTRL + C. Se si ha già in questo stato, contattare il supporto Microsoft per la procedura successiva.|Sì|No|
|2|Base disco| In rari, se la maggior parte dei dischi in simbolo EBOD di un dispositivo 8600 disconnessi risultante in base Nessun disco, quindi pool di archiviazione sarà offline. Rimarrà non in linea anche se i dischi riconnessione.|È necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto Microsoft per la procedura successiva.|Sì |No
|3|Cloud snapshot errori|In alcuni casi l'uno snapshot cloud potrebbe non riuscire con l'errore **raggiunto il limite backup massimo**. Se si superano 255 duplicati online nello stesso dispositivo dallo stesso volume originale che è stato eliminato.||Sì|Sì
|4|ID controller non corretti|Quando viene eseguita la sostituzione controller, controller 0 vengano visualizzate come controller di 1. Durante la sostituzione dei controller, quando l'immagine viene caricata dal nodo peer ID controller può essere visualizzata inizialmente come ID. del controller peer  In alcuni casi il questo comportamento può essere visualizzato anche dopo il riavvio del sistema.|Non è necessario eseguire alcuna azione. Questa situazione risolverà stesso una volta completata la sostituzione controller.|Sì|No 
|5| Dispositivo monitoraggio grafici|Nel servizio di gestione StorSimple, i grafici di monitoraggio di dispositivo non funzionano quando base o autenticazione NTLM è attivata nella configurazione del server proxy per il dispositivo.|Modificare la configurazione del proxy web per il dispositivo sia registrato presso il servizio StorSimple Manager in modo che l'autenticazione è impostato su Nessuno. A tale scopo, eseguire il Windows PowerShell per cmdlet Set-HcsWebProxy StorSimple.|Sì|Sì
|6| Account di archiviazione|Utilizzo del servizio di archiviazione per eliminare l'account di archiviazione è uno scenario non supportato. Questa operazione comporta a situazioni in cui non è possibile recuperare dati utente.|| Sì |  Sì
|7|Dispositivo failover| Failover più di un contenitore di volume dalla stessa periferica di origine per diversi dispositivi non è supportato.| Il controllo da un unico dispositivo inattivo a più dispositivi consentono di rendere i contenitori volume il primo non è riuscita sul dispositivo perde la proprietà dei dati. Dopo un failover, questi contenitori volume verranno visualizzata o hanno un funzionamento diverso quando vengono visualizzati nel portale di classica Azure.|Sì|No
|8| Installazione|Durante la scheda StorSimple per l'installazione di SharePoint, è necessario specificare un indirizzo IP dispositivo affinché installa per completare correttamente.||Sì|No
|9| Proxy Web|Se la configurazione del proxy web include HTTPS come protocollo specificato, la comunicazione al servizio di dispositivi interessata e passa offline il dispositivo. Verranno generati anche pacchetti di supporto del processo di risorse significativa nel dispositivo.|Assicurarsi che l'URL del proxy web disponga HTTP come protocollo specificato. Vedere ulteriori informazioni su come [configurare proxy web per il dispositivo](storsimple-configure-web-proxy.md).|Sì |No
|10|Proxy Web|  Se si configura e Abilita proxy web in un dispositivo registrato, sarà necessario riavviare controller attivo nel dispositivo.|| Sì |No
|11|Latenza elevata cloud e carico di lavoro elevato i/o|Quando il dispositivo StorSimple rileva una combinazione di latenza elevata cloud (ordine di secondi) e il carico di lavoro i/o elevato, i volumi dispositivo passare a una riduzione e i / o potrebbe non riuscire con un errore di "dispositivo non pronto".|È necessario riavviare il controller di dispositivo o eseguire il controllo un dispositivo a risolvere questa situazione manualmente.|Sì|No

## <a name="physical-device-updates-in-the-january-release"></a>Dispositivo fisico aggiornamenti di gennaio release

Questo aggiornamento non contiene tutte le altre modifiche al dispositivo StorSimple.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Allegato seriale controller SCSI (SA) e firmware aggiornamenti di gennaio di rilascio

In questa versione non contiene tutti gli aggiornamenti a controller di SCSI (SA) allegato seriale o del firmware. L'aggiornamento del driver è stato ottobre, release 2014. 

## <a name="virtual-device-updates-in-the-january-release"></a>Dispositivo virtuale aggiornamenti di gennaio release

In questa versione sono un'immagine aggiornata per il dispositivo virtuale. Tutti i dispositivi virtuali creati dopo il 20 gennaio 2015 verranno visualizzata la versione del software come 6.3.9600.17361.

 
