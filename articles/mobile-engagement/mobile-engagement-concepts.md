<properties
    pageTitle="Concetti di impegno mobile | Microsoft Azure"
    description="Azure concetti coinvolgimento Mobile"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-concepts"></a>Azure concetti coinvolgimento Mobile

Coinvolgimento mobile definisce alcuni concetti comuni a tutte le piattaforme supportate. In questo articolo vengono illustrati brevemente i concetti.

In questo articolo è una buona soluzione se si ha familiarità con coinvolgimento di dispositivi mobili. Assicurarsi di leggere la documentazione specifica della piattaforma in uso, come che verrà perfezionare i concetti descritti in questo articolo con ulteriori dettagli ed esempi, nonché eventuali restrizioni.

## <a name="devices-and-users"></a>Dispositivi e i relativi utenti
Coinvolgimento mobile Identifica utenti generando un identificatore univoco per ogni dispositivo. Questo identificatore viene definito l'identificatore di dispositivo (o `deviceid`). Viene generato in modo che tutte le applicazioni esecuzione dello stesso dispositivo condividere lo stesso identificatore di dispositivo.

In modo implicito, significa che Mobile coinvolgimento vengono tenuti in considerazione un dispositivo in cui inserire un solo utente, e pertanto gli utenti e i dispositivi sono concetti equivalenti.

## <a name="sessions-and-activities"></a>Sessioni e attività
Una sessione di un utilizzo dell'applicazione eseguita da un utente, dal momento in cui si inizia il suo utilizzo, fino a quando non si interrompe l'utente.

Un'attività è un utilizzo di una determinata parte secondaria dell'applicazione eseguita da un utente (in genere uno schermo, ma può essere nulla adatto all'applicazione).

Un utente può eseguire solo un'attività alla volta.

Un'attività è identificata da un nome (limitato a 64 caratteri) e facoltativamente possibile incorporare alcuni dati aggiuntivi (entro il limite di 1024 byte).

Sessioni vengono calcolate automaticamente dalla sequenza delle attività eseguite dagli utenti. Avviare una sessione quando l'utente verrà avviata la propria attività prima e si arresta quando avrà terminato il suo ultima attività. Questo errore indica che una sessione non è necessario in modo esplicito da avviare o interrompere. Se, tuttavia, le attività in modo esplicito avvio o interruzione. Se non viene segnalata alcuna attività, non viene segnalata alcuna sessione.

## <a name="events"></a>Eventi
Gli eventi vengono utilizzati per segnalare azioni immediate (ad esempio la pressione del pulsante o articoli letti dagli utenti).

Un evento può essere correlato alla sessione corrente, a un processo in esecuzione, oppure un evento di autonomo.

Un evento è identificato da un nome (limitato a 64 caratteri) e facoltativamente possibile incorporare alcuni dati aggiuntivi (entro il limite di 1024 byte).

## <a name="error"></a>Errore
Gli errori vengono utilizzati per segnalare i problemi rilevati correttamente l'applicazione (ad esempio non corretti o azioni dell'utente, gli errori di chiamata API).

Un errore può essere correlato alla sessione corrente, a un processo in esecuzione, oppure può trattarsi di un errore autonomo.

Un messaggio di errore è identificato da un nome (limitato a 64 caratteri) e facoltativamente possibile incorporare alcuni dati aggiuntivi (entro il limite di 1024 byte).

## <a name="job"></a>Processo
Processi vengono utilizzati per segnalare azioni con una durata (ad esempio durata delle chiamate API, visualizzare l'orario di annunci, le attività in background o durata delle azioni utente).

Un processo non è correlato a una sessione, un'attività può essere eseguita in background, senza interazione dell'utente.

Un processo è identificato da un nome (limitato a 64 caratteri) e facoltativamente possibile incorporare alcuni dati aggiuntivi (entro il limite di 1024 byte).

## <a name="crash"></a>Arresto anomalo
Anomalo vengono rilasciati automaticamente da Mobile coinvolgimento SDK per inviare una segnalazione errori dell'applicazione in cui problemi non rilevati dall'applicazione rendono blocco.

## <a name="application-information"></a>Informazioni sull'applicazione
Informazioni sull'applicazione (o informazioni app) consente di contrassegnare gli utenti, vale a dire per associare alcuni dati agli utenti di un'applicazione (è simile a cookie web, ad eccezione del fatto che informazioni app archiviata sul lato server piattaforma Azure Mobile coinvolgimento).

È possibile registrare informazioni App tramite l'API di SDK coinvolgimento Mobile oppure usando il dispositivo API della piattaforma di coinvolgimento Mobile.

Informazioni App sono una coppia di chiave/valore associata a un dispositivo. La chiave è il nome delle informazioni di app (limitato a 64 ASCII lettere [a-zA-Z], [0-9] numeri e caratteri di sottolineatura [_]). Valore (limitato a 1024 caratteri) può essere qualsiasi stringa, integer, data (AAAA-MM-dd) o un valore Boolean (true o false).

Qualsiasi numero di informazioni app può essere associata a un dispositivo, entro i limiti definita dai termini prezzi coinvolgimento Mobile. Per una determinata chiave, coinvolgimento Mobile solo tiene traccia del più recente valore impostato (nessuno). Impostazione o modifica il valore di informazioni sull'app forza coinvolgimento Mobile per valutare nuovamente pubblico set di criteri in queste informazioni app (se presente) indicare info app può essere utilizzato per impostare un trigger push in tempo reale.

## <a name="extra-data"></a>Dati aggiuntivi
Dati aggiuntivi (o extra) sono alcuni dati non autorizzati che possono essere collegate a processi, gli errori, attività ed eventi.

Funzionalità aggiuntive sono strutturate in modo analogo a oggetti JSON: essi sono costituiti da una struttura di coppie di parole chiave/valore. Chiavi sono limitate a 64 lettere ASCII [a-zA-Z], [0-9] numeri e caratteri di sottolineatura [_]) e le dimensioni totali di extra sono limitata a 1024 caratteri (una sola volta codificati JSON da Mobile coinvolgimento SDK).

L'intera struttura coppie di parole chiave/valore viene memorizzato come oggetto JSON. Tuttavia, il primo livello di chiavi/valori è scomposto siano direttamente accessibili per alcune funzionalità avanzate come segmenti (ad esempio, è possibile facilmente definire un segmento denominato "SciFi ventole" produttrice di tutti gli utenti avere inviato almeno 10 volte l'evento denominato "content_viewed" con la Content_Types chiave"" e di impostare il valore "scifi" nell'ultimo mese). In questo modo si consiglia di inviare solo extra è costituito da elenchi semplici coppie chiave/valore con valori scalari (ad esempio stringhe, date, numeri interi o Boolean).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Windows SDK universale per la partecipazione attiva Mobile Azure](mobile-engagement-windows-store-sdk-overview.md)
- [Panoramica di Windows Phone Silverlight SDK per la partecipazione attiva Mobile Azure](mobile-engagement-windows-phone-sdk-overview.md)
- [iOS SDK per Azure Mobile coinvolgimento](mobile-engagement-ios-sdk-overview.md)
- [Android SDK per Azure impegno per dispositivi mobili](mobile-engagement-android-sdk-overview.md)
