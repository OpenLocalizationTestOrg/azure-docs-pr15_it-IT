<properties
   pageTitle="Come registrare le origini dati | Microsoft Azure"
   description="Articolo procedure evidenziazione come registrare le origini dati con il catalogo dati di Azure, inclusi i campi di metadati estratti durante la registrazione."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-register-data-sources"></a>Come registrare le origini dati

## <a name="introduction"></a>Introduzione
**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestita che funge da un sistema di registrazione e di sistema di individuazione per le origini dati dell'organizzazione. In altre parole, **Catalogo dati di Azure** è basato sui consentire agli utenti alla scoperta di comprensione e utilizzare le origini dati e alle organizzazioni di risoluzione per ottenere il massimo dai dati esistenti. È il primo passaggio per rendere individuabile tramite **Catalogo dati di Azure** un'origine dati per registrare l'origine dati.
## <a name="registering-data-sources"></a>Registrazione di origini dati
La registrazione è il processo di estrazione dei metadati dall'origine dati e copiare i dati nel servizio **Catalogo dati di Azure** . I dati rimarranno nel punto in cui attualmente risiede e rimane sotto il controllo degli amministratori e i criteri del sistema corrente.

Per registrare un'origine dati, è sufficiente avviare lo strumento di registrazione **Del catalogo dati di Azure** origine dati dal portale di **Catalogo dati di Azure** . Accedere con il lavoro o account dell'istituto di istruzione (le stesse Azure Active Directory credenziali utilizzare agli utenti di accedere al portale) e quindi selezionare l'origine dati che si desidera registrare.
Vedere l'esercitazione [Introduzione catalogo dati di Azure](data-catalog-get-started.md) per altre informazioni dettagliate.

Una volta registrata l'origine dati, il catalogo tiene traccia nella posizione e indicizza i metadati, in modo che gli utenti possono eseguire una ricerca, Sfoglia e individuare l'origine dati e quindi utilizzare la posizione per la connessione tramite l'applicazione o lo strumento di propria scelta.

## <a name="sources-supported"></a>Origini supportate
Per l'elenco delle origini dati attualmente supportate, vedere [DSR catalogo dati](data-catalog-dsr.md) .
<br/>


## <a name="structural-metadata"></a>Metadati strutturali
Quando si sta registrando un'origine dati, lo strumento di registrazione verrà estratte informazioni sulla struttura di oggetti selezionati: si tratta di metadati previste come strutturale.

Per tutti gli oggetti metadati strutturali conterrà la posizione dell'oggetto, in modo che gli utenti individuare i dati possono utilizzare queste informazioni per connettersi all'oggetto in strumenti client di propria scelta. Altri metadati strutturali includono tipo e il nome di oggetto e digitare il nome di colonna/attributo e i dati.

## <a name="descriptive-metadata"></a>Metadati descrittivi
Oltre ai metadati di base strutturali estratti dall'origine dati, lo strumento di registrazione di origine dati verrà anche estratto anche metadati descrittivi. Per SQL Server Analysis Services e SQL Server Reporting Services, questo viene accettato dalle proprietà Descrizione esposta da questi servizi. Per SQL Server, verranno estratti valori forniti con ms_description proprietà estesa. Per Database Oracle, lo strumento di registrazione di origine dati verrà estratto nella colonna commenti dalla visualizzazione ALL_TAB_COMMENTS.

Oltre ai metadati descrittivi estratti dall'origine dati, gli utenti possono immettere anche metadati descrittivi utilizzando lo strumento di registrazione dell'origine dati. Gli utenti possono aggiungere contrassegni e identificano esperti per gli oggetti da registrare. Tutti i metadati descrittivo viene copiata al servizio **Catalogo dati di Azure** insieme i metadati strutturali.

## <a name="including-previews"></a>Incluse le anteprime

Per impostazione predefinita, solo i metadati sono estratti dalle origini dati e copiato al servizio **Catalogo dati di Azure** , ma la comprensione che un'origine dati è stata semplificata spesso visualizzando un campione dei dati che contiene.

Strumento di registrazione origine dati **Del catalogo dati di Azure** consente agli utenti di includere un'anteprima snapshot dei dati in ogni tabella e visualizzare registrato. Se l'utente sceglie aggiuntivo per includere le anteprime durante la registrazione, lo strumento di registrazione includerà un massimo di 20 record di ogni tabella e visualizzazione. Questo snapshot viene quindi copiato nel catalogo insieme i metadati strutturali e descrittivo.


> [AZURE.NOTE]  Tabelle di grandi dimensioni con un numero elevato di colonne, potrebbe essere minore di 20 record inclusi nel loro anteprima.


## <a name="including-data-profiles"></a>Tra i profili dei dati

Come anteprime incluse possono fornire contesto importante per gli utenti di cercare le origini dati nel **Catalogo dati di Azure**, incluso un profilo di dati possibile rendono più semplice da comprendere le origini dati rilevati.

Strumento di registrazione origine dati **Del catalogo dati di Azure** consente agli utenti di includere un profilo di dati per ogni tabella o visualizzazione registrato. Se si sceglie di includere un profilo di dati durante la registrazione, lo strumento di registrazione includerà aggregazione statistiche relative ai dati in ogni tabella e visualizzare, tra cui:

* Il numero di righe e le dimensioni dei dati nell'oggetto
* La data per l'aggiornamento più recente dei dati e lo schema di oggetto
* Il numero di valori distinti per le colonne e i record null
* I valori minimo, massimo, Media e deviazione standard per le colonne

Queste statistiche vengono quindi copiate nel catalogo insieme i metadati strutturali e descrittivo.

> [AZURE.NOTE]  Colonne di testo e data non verranno inclusi statistiche Media o deviazione standard nel proprio profilo di dati.

## <a name="updating-registrations"></a>Aggiornare le registrazioni

Registrazione di un'origine dati renderà individuabile nel **Catalogo dati di Azure** utilizzando i metadati e l'anteprima facoltativo estratte durante la registrazione. Se l'origine dati deve essere aggiornata nel catalogo (ad esempio, se lo schema di un oggetto è stato modificato, o tabelle originariamente escluse devono essere incluse o si desidera aggiornare i dati inclusi nelle anteprime) è possibile eseguire nuovamente lo strumento di registrazione di origine dati.

Ripetere la registrazione di un'origine dati già registrato esegue un'operazione di stampa unione "upsert": gli oggetti esistenti verranno aggiornati, mentre verranno creati nuovi oggetti. I metadati inviati dagli utenti tramite il portale di **Catalogo dati di Azure** verranno mantenuti.

## <a name="summary"></a>Riepilogo
Registrazione di un'origine dati con **Il catalogo dati di Azure** semplifica l'origine dati individuare e comprendere copiando strutturali e descrittivo i metadati dall'origine dati al servizio di catalogo. Una volta registrata un'origine dati, si può quindi essere aggiunto, gestita e individuato tramite il portale di **Catalogo dati di Azure** .

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva a catalogo dati di Azure](data-catalog-get-started.md) esercitazione per informazioni dettagliate su come registrare le origini dati.
