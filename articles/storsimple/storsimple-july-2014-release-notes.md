<properties 
   pageTitle="Note sulla versione di versione di rilascio 8000 StorSimple | Microsoft Azure"
   description="Descrive le nuove funzionalità, problemi aperti e soluzioni alternative disponibili per luglio 2014 sulla versione di Microsoft Azure StorSimple."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>Note sulla versione StorSimple 8000 serie versione - luglio 2014 

## <a name="overview"></a>Panoramica

Le note sulla versione di completamento identificare i problemi critici aperti per la serie di 8000 StorSimple luglio 2014 disponibilità generale (GA) della versione di Microsoft Azure StorSimple. In questa versione corrisponde alla versione del software 6.3.9600.17215.  

Se non diversamente specificato, queste note sulla versione si applicano a tutti i modelli di dispositivo StorSimple. Note sulla versione vengono continuamente aggiornate; Quando vengono rilevati problemi critici che richiedono una possibile soluzione, questi vengono aggiunti. Prima di distribuire la soluzione Microsoft Azure StorSimple, considerare le seguenti informazioni.  

## <a name="known-issues-in-this-release"></a>Problemi noti di questa versione
La tabella seguente contiene un riepilogo dei problemi noti di questa versione.  
 
| No. | Caratteristica | Problema | Commenti/soluzione | Si applica a dispositivo fisico | Si applica a dispositivo virtuale |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Reimposta factory | In alcuni casi, quando si esegue il ripristino di factory, il dispositivo StorSimple potrebbe essere bloccato e visualizzare il messaggio seguente: **Reimposta su factory è in corso (fase 8)**. Si verifica questo evento se si preme CTRL + C, mentre il cmdlet è in corso. | Dopo aver iniziato la reimpostazione factory non premere CTRL + C. Se si ha già in questo stato, contattare il supporto Microsoft per la procedura successiva. | Sì | No |
| 2 | Base disco | In rari, se la maggior parte dei dischi in simbolo EBOD di un dispositivo 8600 disconnessi risultante in base Nessun disco, quindi pool di archiviazione sarà offline. Rimarrà non in linea anche se i dischi riconnessione. | È necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto Microsoft per la procedura successiva. | Sì | No |
| 3 | Cloud snapshot errori | In alcuni casi l'uno snapshot cloud potrebbe non riuscire con l'errore **raggiunto il limite backup massimo**. Se si superano 255 duplicati online nello stesso dispositivo dallo stesso volume originale che è stato eliminato. | | Sì | Sì |
| 4 | ID controller non corretti | Quando viene eseguita la sostituzione controller, controller 0 vengano visualizzate come controller di 1. Durante la sostituzione dei controller, quando l'immagine viene caricata dal nodo peer ID controller può essere visualizzata inizialmente come ID. del controller peer In alcuni casi il questo comportamento può essere visualizzato anche dopo il riavvio del sistema. | Non è necessario eseguire alcuna azione. Questa situazione risolverà stesso una volta completata la sostituzione controller. | Sì | No |
| 5 | Dispositivo monitoraggio grafici | Nel servizio di gestione StorSimple, i grafici di monitoraggio di dispositivo non funzionano quando base o autenticazione NTLM è attivata nella configurazione del server proxy per il dispositivo. | Modificare la configurazione del proxy web per il dispositivo sia registrato presso il servizio StorSimple Manager in modo che l'autenticazione è impostato su Nessuno. A tale scopo, eseguire il Windows PowerShell per cmdlet Set-HcsWebProxy StorSimple. | Sì | Sì |
| 6 | Account di archiviazione | Utilizzo del servizio di archiviazione per eliminare l'account di archiviazione è uno scenario non supportato. Questa operazione comporta a situazioni in cui non è possibile recuperare dati utente. | | Sì | Sì |
| 7 | Failback | Failback entro 24 ore di emergenza (DR) non è supportato. | | Sì | No |
| 8 | Dispositivo failover | Failover più di un contenitore di volume dalla stessa periferica di origine per diversi dispositivi non è supportato. Il controllo da un unico dispositivo inattivo a più dispositivi consentono di rendere i contenitori volume il primo non è riuscita sul dispositivo perde la proprietà dei dati. Dopo un failover, questi contenitori volume verranno visualizzata o hanno un funzionamento diverso quando vengono visualizzati nel portale di classica Azure. | | Sì | No |
| 9 | Installazione | Durante la scheda StorSimple per l'installazione di SharePoint, è necessario specificare un indirizzo IP dispositivo per l'installazione completare correttamente. | | Sì | No |
| 10 | Interfacce di rete | Interfacce di rete dati 2 e 3 dati sono state scambiate nel software. | Contattare il supporto Microsoft se è necessario configurare queste interfacce. | Sì | No |


 
