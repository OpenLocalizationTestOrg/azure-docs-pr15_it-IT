<properties 
   pageTitle="Note sulla versione 0,3 di aggiornamento 8000 StorSimple | Microsoft Azure"
   description="Descrive le nuove funzionalità e le correzioni, problemi aperti e soluzioni alternative disponibili per febbraio 2015 sulla versione di Microsoft Azure StorSimple (aggiornamento 0,3)."
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

# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>Note sulla versione 0,3 di aggiornamento di serie 8000 StorSimple - febbraio 2015

## <a name="overview"></a>Panoramica

Note sulla versione seguenti identificare i problemi di Apri critici StorSimple 8000 serie aggiornamento 0,3 rilasciato nel febbraio 2015. Contengono anche un elenco degli aggiornamenti di software e firmware del StorSimple inclusi in questa versione. Questa è la terza versione dopo la StorSimple 8000 serie versione è stata effettuata in genere disponibile in luglio 2014.
  
Questo aggiornamento non modifica il software o dispositivo dall'aggiornamento gennaio. Continua a essere versione 6.3.9600.17312. È possibile verificare che l'aggiornamento è stato installato controllando la data **Ultimo aggiornamento** . Se la data 2/10/2015 o versione successiva, quindi installato l'aggiornamento.  

È consigliabile cercare e applicare gli aggiornamenti disponibili subito dopo l'installazione del dispositivo StorSimple. È anche possibile attivare gli aggiornamenti automatici per scaricare e installare gli aggiornamenti priorità alta da Microsoft, non appena sono disponibili. Per ulteriori informazioni, vedere [aggiornare il dispositivo StorSimple](storsimple-update-device.md).  

Esaminare le informazioni contenute nelle note sulla versione prima di distribuire l'aggiornamento della soluzione StorSimple.  

>[AZURE.IMPORTANT]   
>
> - Utilizzare il servizio di gestione StorSimple e non Windows PowerShell per StorSimple per installare l'aggiornamento di febbraio.   
> - Bastano circa un'ora per installare l'aggiornamento. Tuttavia, se si installa aggiornamenti cumulativi, il processo può richiedere circa 3 ore per completare.  
> - La versione di febbraio di StorSimple non contiene tutti gli aggiornamenti al dispositivo virtuale StorSimple. È ancora possibile applicare gli aggiornamenti disponibili per Windows al dispositivo virtuale, inclusi correzioni recenti, ma non si vedranno una modifica nella versione per il dispositivo virtuale.  

Assicurarsi che siano soddisfatti i prerequisiti seguenti prima dell'aggiornamento del dispositivo StorSimple.  

- Verificare che entrambi i controller di dispositivo sono in esecuzione prima che la disponibilità di aggiornamenti. Se uno dei due controller non è in esecuzione, l'elemento digitalizzato. Per verificare che i controller funzionante, passare allo **Stato di Hardware** sotto la pagina **manutenzione** . Se sono presenti componenti che **necessità attenzione**, contattare il supporto Microsoft prima di continuare.
- Assicurarsi che IP fissi per controller 0 e 1 controller sono routing e connettersi a Internet utilizzati per la manutenzione gli aggiornamenti al dispositivo. È possibile utilizzare il [cmdlet Test connessione](https://technet.microsoft.com/library/hh849808.aspx) per effettuare il ping indirizzo noto all'esterno della rete, ad esempio outlook.com, per verificare che il controller disponga di connettività di rete esterna.
- Assicurarsi che le porte 80 e 443 siano disponibili in un dispositivo StorSimple per le comunicazioni in uscita. Per ulteriori informazioni, vedere [requisiti per il dispositivo di StorSimple di rete](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
- Se il software o dispositivo è meno recente 6.3.9600.17312 (aggiornamento di ottobre 2014), disattivare le porte dati 2 e 3 dati se abilitata, prima di iniziare l'aggiornamento. Lasciando i dati 2 o 3 dati porte abilitate quando si applica l'aggiornamento potrebbe causare controller dispositivo passa alla modalità di ripristino. Si noti che, quando si disattivano le interfacce di rete, tutti i volumi associati impostare la modalità offline e i / o verrà interrotto per la durata dell'aggiornamento.  
  
## <a name="whats-new-in-the-february-release"></a>Quali sono le novità della versione di febbraio

Questo aggiornamento contiene una correzione per la factory Reimposta problema che si sono verificati in dispositivi aggiornabili da il GA rilasciare la versione di ottobre 2014. Per ulteriori informazioni, vedere [problemi risolti in questa versione](#issues-fixed-in-the-february-release).   

Aggiornamento non include nuove caratteristiche o funzionalità.  

## <a name="issues-fixed-in-the-february-release"></a>Problemi risolti nella versione febbraio

Nella tabella seguente descrive il problema corretti in questo aggiornamento.  
 
| No. | Caratteristica | Problema | Si applica a dispositivo fisico | Si applica a dispositivo virtuale |
|-----|---------|-------|---------------------------------|-------------------------------|
| 1 | Reimposta factory | Si tenta di eseguire una factory Reimposta in un dispositivo originariamente contiene la versione GA (versione 6.3.9600.17215) installata ma è stato aggiornato a di ottobre versione (6.3.9600.17312). La factory Reimposta ha esito negativo e il dispositivo diventa instabile. | Sì | No |


## <a name="known-issues-in-the-february-release"></a>Problemi noti della versione di febbraio

La tabella seguente contiene un riepilogo dei problemi noti di questa versione.
 
| No. | Caratteristica | Problema | Commenti/soluzione | Si applica a dispositivo fisico  | Si applica a dispositivo virtuale |
|-----|---------|-------|----------------------------|-----------------------------|--------------------------|
| 1 | Reimposta factory | In alcuni casi, quando si esegue il ripristino di factory, il dispositivo StorSimple potrebbe essere bloccato e visualizzare il messaggio seguente: **reimpostare su factory è in corso (fase 8)**. Questo accade se si preme CTRL + C, mentre il cmdlet è in corso. | Dopo aver iniziato la reimpostazione factory non premere CTRL + C. Se si ha già in questo stato, contattare il supporto Microsoft per la procedura successiva. | Sì | No |
| 2 | Base disco | In rari, se la maggior parte dei dischi in simbolo EBOD di un 8600device disconnessi risultante in base Nessun disco, quindi pool di archiviazione sarà offline. Rimarrà offline anche se i dischi riconnessione. | È necessario riavviare il dispositivo. Se il problema persiste, contattare il supporto Microsoft per la procedura successiva. | Sì | No |
| 3 | Errori snapshot cloud | In alcuni casi l'uno snapshot cloud potrebbe non riuscire con l'errore **raggiunto il limite backup massimo**. Se si superano 255 duplicati online nello stesso dispositivo dallo stesso volume originale che è stato eliminato. |  | Sì | Sì |
| 4 | ID controller non corretti | Quando viene eseguita la sostituzione controller, controller 0 vengano visualizzate come controller di 1. Durante la sostituzione dei controller, quando l'immagine viene caricata dal nodo peer ID controller può essere visualizzata inizialmente come ID. del controller peer In alcuni casi il questo comportamento può essere visualizzato anche dopo il riavvio del sistema. | Non è necessario eseguire alcuna azione. Questa situazione risolverà stesso una volta completata la sostituzione controller. | Sì | No |
| 5 | Dispositivo monitoraggio grafici | Nel servizio di gestione StorSimple, i grafici di monitoraggio di dispositivo non funzionano quando base o autenticazione NTLM è attivata nella configurazione del server proxy per il dispositivo. | Modificare la configurazione del proxy web per il dispositivo sia registrato presso il servizio StorSimple Manager in modo che l'autenticazione è impostato su Nessuno. A tale scopo, eseguire il Windows PowerShell per cmdlet Set-HcsWebProxy StorSimple. | Sì | Sì |
| 6 | Account di archiviazione | Utilizzo del servizio di archiviazione per eliminare l'account di archiviazione è uno scenario non supportato. Questa operazione comporta a situazioni in cui non è possibile recuperare dati utente. |  | Sì | Sì |
| 7 | Dispositivo failover | Failover più di un contenitore di volume dalla stessa periferica di origine per diversi dispositivi non è supportato.  Il controllo da un unico dispositivo inattivo a più dispositivi consentono di rendere i contenitori volume il primo non è riuscita sul dispositivo perde la proprietà dei dati. Dopo un failover, questi contenitori volume verranno visualizzata o hanno un funzionamento diverso quando vengono visualizzati nel portale di classica Azure. |   | Sì | No |
| 8 | Installazione | Durante la scheda StorSimple per l'installazione di SharePoint, è necessario specificare un indirizzo IP dispositivo affinché installa per completare correttamente. |  | Sì | No |
| 9 | Proxy Web | Se la configurazione del proxy web include HTTPS come protocollo specificato, la comunicazione dispositivo-servizio avrà effetto sul e passa offline il dispositivo. Verranno generati anche pacchetti di supporto del processo di risorse significativa nel dispositivo. | Assicurarsi che l'URL del proxy web disponga HTTP come protocollo specificato. Ulteriori informazioni su come [configurare proxy web per il dispositivo](storsimple-configure-web-proxy.md). | Sì | No |
| 10 | Proxy Web | Se si configura e Abilita proxy web in un dispositivo registrato, sarà necessario riavviare il controller attivo nel dispositivo. |  | Sì | No |
| 11 | Latenza elevata cloud e carico di lavoro elevato i/o | Quando il dispositivo StorSimple rileva una combinazione di latenza elevata cloud (ordine di secondi) e il carico di lavoro i/o elevato, i volumi dispositivo passare a una riduzione e i / o potrebbe non riuscire con un errore di "dispositivo non pronto". | È necessario riavviare controller dispositivo o eseguire il controllo un dispositivo a risolvere questa situazione manualmente. | Sì | No |

## <a name="physical-device-updates-in-the-february-release"></a>Dispositivo fisico aggiornamenti di febbraio release

Che risolve il problema di reimpostazione factory che si sono verificati in dispositivi aggiornabili da il GA rilasciare la versione di ottobre 2014. Non contiene altri aggiornamenti per il dispositivo StorSimple.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Allegato seriale controller SCSI (SA) e firmware aggiornamenti di febbraio di rilascio

In questa versione non contiene tutti gli aggiornamenti a controller di SCSI (SA) allegato seriale o del firmware. L'aggiornamento del driver è stato ottobre, release 2014.  

## <a name="virtual-device-updates-in-the-february-release"></a>Dispositivo virtuale aggiornamenti di febbraio release

In questa versione non contiene gli aggiornamenti disponibili per il dispositivo virtuale. Applicazione dell'aggiornamento, la versione del software di un dispositivo virtuale rimarrà invariato.
 
