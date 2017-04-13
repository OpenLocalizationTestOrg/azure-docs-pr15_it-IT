<properties
    pageTitle="Archivio di flusso Analitica dati Lake Output | Microsoft Azure"
    description="Configurazione di autenticazione e autorizzazione di un archivio di Lake Azure dati in un processo di flusso Analitica"
    keywords=""
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="stream-analytics-data-lake-store-output"></a>Output flusso Analitica dati Lake archivio

Processi Analitica flusso supportano diversi metodi di output, uno da un [Archivio Lake dati di Azure](https://azure.microsoft.com/services/data-lake-store/). Azure Lake archivio in un archivio di hyper scala aziendale per carichi di lavoro analitico di dati. Archivio Lake dati consente di archiviare i dati di qualsiasi dimensione, tipo e acquisizione velocità per operativi ed esplorativo analitica.

## <a name="authorize-a-data-lake-store-account"></a>Autorizzare un account di archivio di dati Lake

1.  Archivio dati Lake selezionato come un output nel portale di gestione di Azure, verrà richiesto di autorizzare l'uso dell'archivio Lake dati esistenti o per richiedere l'accesso all'anteprima di archivio dati Lake tramite il portale classica Azure.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  

2.  Se si dispone già di accesso all'archivio Lake dati, fare clic su "Ora autorizzare" e per brevi periodi una pagina verrà visualizzata che indica "Reindirizzamento a autorizzazione".... La pagina verrà chiusa automaticamente e verrà visualizzata la pagina che consentirebbe di configurare l'output archivio Lake dati.

Se dispone non iscritti anteprima archivio Lake dati, è possibile fare clic sul collegamento "Per iscriversi" per avviare la richiesta o attenersi alle [istruzioni avviato](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Configurare le proprietà di output archivio Lake dati.

Dopo avere inserito l'account di archivio di dati Lake autenticato, è possibile configurare le proprietà per l'output archivio Lake dati. Nella tabella riportata di seguito è l'elenco dei nomi di proprietà e la relativa descrizione per configurare l'output archivio Lake dati.

<table>
<tbody>
<tr>
<td><B>NOME DELLA PROPRIETÀ</B></td>
<td><B>DESCRIZIONE</B></td>
</tr>
<tr>
<td>Alias di output</td>
<td>Si tratta di un nome descrittivo utilizzato nelle query per indirizzare l'output della query all'archivio Lake dati.</td>
</tr>
<tr>
<td>Archivio dati Lake Account</td>
<td>Il nome dell'account di archiviazione in cui si intende inviare l'output. Verrà visualizzato un elenco degli account archivio Lake dati a cui l'utente ha eseguito l'accesso al portale di ha accesso a discesa.</td>
</tr>
<tr>
<td>Percorso prefisso motivo [<I>facoltativo</I>]</td>
<td>Il percorso del file utilizzato per scrivere i file all'interno di dall'Account archivio Lake dati specificato. <BR>{date}, {time}<BR>Esempio 1: Cartella1/log / {date} / {ora}<BR>Esempio 2: Cartella1/log / {date}</td>
</tr>
<tr>
<td>Formato di data [<I>facoltativo</I>]</td>
<td>Se nel percorso prefisso viene utilizzato il token di data, è possibile selezionare il formato di data in cui sono organizzati i file. Esempio: Gg/MM/aaaa</td>
</tr>
<tr>
<td>Formato ora [<I>facoltativo</I>]</td>
<td>Se nel percorso prefisso viene utilizzato il token di tempo, specificare il formato di ora in cui sono organizzati i file. L'unico valore supportato è al momento HH.</td>
</tr>
<tr>
<td>Formato di serializzazione evento</td>
<td>Formato di serializzazione per i dati di output. JSON, CSV e Avro sono supportate.</td>
</tr>
<tr>
<td>Codifica</td>
<td>Se in formato CSV o JSON, è necessario specificare una codifica. UTF-8 è il formato di codifica supportato solo in questo momento.</td>
</tr>
<tr>
<td>Delimitatore</td>
<td>Applicabile solo per la serializzazione CSV. Flusso Analitica supporta un numero di delimitatori comuni per serializzare dati CSV. Valori supportati sono punto e virgola, punto e virgola, spazio, tabulazione e barra verticale.</td>
</tr>
<tr>
<td>Formato</td>
<td>Applicabile solo per la serializzazione JSON. Riga di separazione specifica che verrà formattata l'output mediante la ricezione di ogni oggetto JSON separato da una nuova riga. Matrice indica che l'output verrà formattata come una matrice di oggetti JSON.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Rinnovare autorizzazione archivio Lake dati.

Attualmente non è presente una limitazione nel punto in cui il token di autenticazione deve essere aggiornati manualmente ogni 90 giorni per tutti i processi con output archivio Lake dati. Sarà anche necessario ripetere l'autenticazione account archivio Lake dati se è stata modificata la password poiché il lavoro è stato creato o dell'ultima autenticazione. Un indicatore di questo problema è alcun output di processo e un errore nei registri di operazione che indica necessità di autorizzazione di modifica.

Per risolvere il problema, interrompere il lavoro in esecuzione e passare all'output archivio Lake dati. Fare clic sul collegamento "Rinnovo autorizzazione" e per brevi periodi una pagina verrà visualizzata che indica "Reindirizzamento a autorizzazione".... La pagina verrà chiusa automaticamente e in caso contrario, verrà indicato "Autorizzazione è stata aggiornata". È quindi necessario fare clic su "Salva" nella parte inferiore della pagina e procedere riavviando il processo dall'ultima volta arrestato per evitare perdite di dati.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)
