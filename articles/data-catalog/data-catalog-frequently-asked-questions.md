<properties
   pageTitle="Domande frequenti su catalogo dati di Azure | Microsoft Azure"
   description="Domande frequenti su catalogo dati di Azure, incluse le funzionalità di individuazione di origine dati, le annotazioni e gestione."
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

# <a name="azure-data-catalog-frequently-asked-questions"></a>Domande frequenti su catalogo dati di Azure

In questo articolo vengono fornite le risposte a domande frequenti relativi al servizio Microsoft **Catalogo dati di Azure** .

## <a name="q-what-is-azure-data-catalog"></a>D: qual è il catalogo dati di Azure?

R: Microsoft Azure dati catalogo è un servizio completamente gestito ospitato nel cloud Microsoft Azure che funge da un sistema di registrazione e di sistema di individuazione per le origini dati dell'organizzazione. Catalogo dati di Azure offre funzionalità che consentono a qualsiasi utente – da analisti a scienziati dati per gli sviluppatori: la registrazione, individuare, comprendere e utilizzare le origini dati.

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>D: quali cliente richiede significa catalogo dati di Azure risolvere?

Catalogo dati di Azure indirizzi la necessità di individuazione di origine dati e "dati scuri" consentendo agli utenti di individuare e comprendere origini dati aziendali.

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>D: chi sono i destinatari del catalogo dati di Azure?

Catalogo dati di Azure offre funzionalità per gli utenti tecnici e non, tra cui:

- Gli sviluppatori di dati, BI e professionisti Analitica: chi è responsabile della creazione di contenuti di dati e analitica ad altri utenti di utilizzare
-   Degli amministratori dei dati: Gli utenti che conoscono sui dati, significato e come deve essere utilizzato e per quale scopo
- Dati consumatori: Gli utenti che devono avere la possibilità di individuare facilmente, informazioni e connettersi ai dati necessari per svolgere il proprio lavoro con lo strumento di propria scelta
- Centrale IT: quelli che necessitano rendere il centinaia di origini dati per gli utenti aziendali, e che è necessario mantenere la supervisione sulla modalità di utilizzo dei dati e l'autore

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>D: qual è la disponibilità di area Catalogo dati di Azure?

Servizi di catalogo dati Azure sono attualmente disponibili nei data center seguenti:

- Usa ovest
- Stati Uniti orientali
- Europa occidentale
- Europa Nord America
- Australia orientale
- Area Asia sudorientale

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>D: quali sono i limiti al numero di risorse di dati nel catalogo dati di Azure?

Edizione gratuita di Azure dati catalogo è limitato ai dati registrati 5.000.

La Standard Edition di Azure catalogo dati supporta fino a 100.000 risorse dati registrati.

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>D: quali sono i tipi di origine e di risorse di dati supportati?

Per l'elenco delle origini dati attualmente supportate, vedere [DSR catalogo dati](data-catalog-dsr.md) .

## <a name="q-how-do-i-request-support-for-another-data-source"></a>D: come è possibile richiedere il supporto per un'altra origine dati?

Nel [Catalogo dati di Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409), è possibile inviare richieste relative alle funzionalità e altri commenti e suggerimenti.

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>D: come iniziare con il catalogo dati di Azure

Il modo migliore per iniziare è seguendo le istruzioni nella [Guida introduttiva con il catalogo dati](data-catalog-get-started.md). In questo articolo è una panoramica-to-end delle funzionalità del servizio.

## <a name="q-how-do-i-register-my-data"></a>D: come è possibile registrare i dati?

Per registrare i dati nel catalogo dati di Azure, avviare lo strumento di registrazione del catalogo dati di Azure dall'area "Pubblica" del portale del catalogo dati di Azure. Nell'applicazione di pubblicazione del catalogo dati di Azure, accedere con le stesse credenziali che consente di accedere al portale di catalogo dati di Azure e quindi selezionare l'origine dati e i beni specifici che si desidera registrare.

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>D: quali proprietà vengono estratti per le risorse dati registrate?

Le proprietà specifiche sono diverso dall'origine dati all'origine dati, ma in genere il servizio di pubblicazione del catalogo dati di Azure verrà estratte le informazioni seguenti:

- Nome bene
- Tipo di risorsa
- Descrizione dei beni
- Nomi di colonna/attributo
- Tipi di dati attributo/colonna
- Attributo/colonna Descrizione

> [AZURE.IMPORTANT] Registrazione delle risorse di dati con il catalogo dati di Azure non spostare o copiare i dati nel cloud. Registrazione delle risorse da un'origine dati vengono copiate automaticamente i metadati i beni di Azure, ma i dati rimangono nel percorso di origine dati esistente. L'unica eccezione a questa regola è se un utente sceglie di caricare i record di anteprima o un profilo di dati durante la registrazione delle risorse. Quando tra un'anteprima, fino a 20 record verrà copiato da ogni risorsa e vengono memorizzati come snapshot nel catalogo dati di Azure. Quando si includono un profilo di dati, le informazioni di aggregazione (ad esempio le dimensioni di tabelle, i valori null percentuale per ogni colonna e i valori minimi, massimo e Media per le colonne) verranno calcolate e incluso nei metadati memorizzati nel catalogo.

<br/>

> [AZURE.NOTE] Per le origini dati, ad esempio SQL Server Analysis Services che dispongono di una costrutto proprietà **Descrizione** del catalogo dati di Azure pubblicazione applicazione verranno estratte il valore della proprietà. Per i database relazionali di SQL Server, che non dispongono di una costrutto proprietà **Descrizione** , l'applicazione di pubblicazione del catalogo dati di Azure verrà estratto il valore da ms_description proprietà per le colonne e oggetti estesa. Per ulteriori informazioni, vedere TechNet [Con proprietà estese gli oggetti di Database](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>D: come tempo necessario per risorse appena registrate visualizzare nel catalogo dati di Azure?

Dopo aver registrato beni con catalogo dati di Azure può essere un periodo di 5 a 10 secondi prima che vengano visualizzati nel portale di catalogo dati di Azure.

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>D: come aggiungere annotazioni e migliorare i metadati per le risorse dati registrato

Il modo più semplice per fornire i metadati per beni registrati consiste nel selezionare la risorsa nel portale del catalogo dati di Azure e quindi immettere i valori dei metadati nel riquadro proprietà o dello schema per l'oggetto selezionato.

È inoltre possibile fornire alcuni metadati, ad esempio esperti tag e, durante la registrazione. I valori forniti nel servizio di pubblicazione del catalogo dati di Azure vengono applicate a tutte le risorse da registrare in quel momento. Per visualizzare gli oggetti recente registrato nel portale per le annotazioni aggiuntive, selezionare il pulsante di **Visualizzazione portale** nella schermata finale dell'applicazione di pubblicazione del catalogo dati di Azure.

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>D: come si eliminano oggetti my dati registrato?

È possibile eliminare un oggetto dal catalogo dati di Azure selezionando l'oggetto nel portale e quindi fare clic sul pulsante **Elimina** . Verranno rimossi i metadati per l'oggetto dal catalogo dati di Azure, ma non viene influenzata dalle effettivo origine dati sottostante.

## <a name="q-what-is-an-expert"></a>D: che cos'è un esperto?

Un esperto è una persona che ha una prospettiva informata su un oggetto di dati. È possibile associare più esperti. Non è necessario che un esperto "proprietario" per un oggetto. l'esperto è sufficiente chi sa come sia possibile i dati e deve essere utilizzato.

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>D: come si condividono informazioni con il team del catalogo dati di Azure caso di problemi?

Utilizzare il catalogo dati di Azure per segnalare problemi, condividere le informazioni e porre domande. Visitare il forum http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

##<a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>D: catalogo dati di Azure funzionano con questa altra origine dati che sono interessato?
Stiamo lavorando attivamente sull'aggiunta di più origini dati nel catalogo dati di Azure. Se c'è un'origine dati che si vuole vedere supportati, vedere suggerite (o vocali al supporto se è già stato suggerito) nel [Catalogo dati di Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>D: come catalogo dati di Azure correlato al catalogo dati di Power BI per Office 365?

È possibile pensare del catalogo dati di Azure come un'evoluzione del catalogo dati. Catalogo dati di Azure offre funzionalità simili per la pubblicazione di origine dati e individuazione, ma è attivo in scenari più ampi e non dipende da Office 365. Subito dopo il catalogo dati di Azure diventa disponibile in genere due cataloghi consentono di unire in un unico servizio.

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>D: quali autorizzazioni è necessarie per un utente per registrare beni con catalogo dati di Azure?

L'utente che esegue lo strumento di registrazione del catalogo dati di Azure deve le autorizzazioni per l'origine dati che sarà in grado di leggere i metadati dall'origine. Se l'utente seleziona anche per includere un'anteprima, l'utente deve anche avere autorizzazioni che consentano in grado di leggere i dati dagli oggetti da registrare.

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Q: Azure catalogo dati saranno disponibile per la distribuzione in locale anche?

Catalogo dati di Azure è un servizio cloud compatibili con le origini dati cloud e in locale, l'esecuzione di una soluzione di individuazione ibrida origine dati. Non sono attualmente previste per la versione del servizio Catalogo dati di Azure che verrà eseguita in locale.

##<a name="q-can-we-extract-more--richer-metadata-from-the-data-sources-we-register"></a>Q: possiamo è estrarre i metadati ulteriori / più origini dati che si registra?

Stiamo lavorando attivamente per espandere le funzionalità del catalogo dati di Azure. Se c'è metadati aggiuntivi che si desidera visualizzare estratti dall'origine dati durante la registrazione, suggerire (oppure, se è già stato suggerito votare) nel [Catalogo dati di Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). In futuro è consentirà di terze parti aggiungere nuovi tipi di origini dati tramite un'API di estensibilità.

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>D: come limitare la visibilità dei beni dati registrato, in modo che solo determinate persone possibile scoprirla?

R: selezionare le risorse dati nel catalogo dei dati di Azure e fare clic sul pulsante "Diventa proprietario". I proprietari dei beni di dati nel catalogo dati di Azure è possono modificare le impostazioni di visibilità, per consentire tutti gli utenti di catalogo per individuare i beni secondari oppure per limitare la visibilità a utenti specifici.

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Q: come aggiornare la registrazione per una risorsa di dati per le modifiche nell'origine dati vengono riportate nel catalogo?

R: per aggiornare i metadati per le risorse dati già registrati nel catalogo, è sufficiente registrare nuovamente l'origine dati che contiene le risorse. Le modifiche nell'origine dati, ad esempio le colonne vengano aggiunti o rimossi da tabelle o viste, verranno aggiornate nel catalogo, ma le annotazioni inviate dagli utenti verranno mantenute.

## <a name="q-my-question-isnt-answered-here--what-should-i-do"></a>Q: una domanda non ha ricevuto risposta qui: cosa deve fare?

Testa sulla sopra al [Catalogo dati di Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Domande frequenti sono verranno introdotti qui.
