<properties
   pageTitle="Come aggiungere note alle origini dati | Microsoft Azure"
   description="Articolo procedure evidenziazione come aggiungere note alle risorse di dati nel catalogo dati di Azure, tra cui esperti, tag, descrizioni e nomi."
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
   ms.date="09/21/2016"
   ms.author="maroche"/>


# <a name="how-to-annotate-data-sources"></a>Come aggiungere note alle origini dati

## <a name="introduction"></a>Introduzione
**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestita che funge da un sistema di registrazione e di sistema di individuazione per le origini dati dell'organizzazione. In altre parole, catalogo dati è basato sui consentire agli utenti alla scoperta di comprensione e utilizzare le origini dati e alle organizzazioni di risoluzione per ottenere il massimo dai dati esistenti. Quando un'origine dati è registrata con catalogo dati, viene copiato e indicizzato dal servizio metadati, ma la storia non finisce qui. Catalogo dati consente agli utenti di forniscono propri metadati descrittivo, ad esempio le descrizioni e categorie – per integrare i metadati estratti dall'origine dati e rendere più comprensibile l'origine dati di altre persone.

## <a name="annotation-and-crowdsourcing"></a>Le annotazioni e crowdsourcing
Tutti dispongono di un parere. Si tratta positiva.
Catalogo dati riconosce che gli utenti diversi presenti prospettive diverse origini dati aziendali e ognuna di queste prospettive può essere utile. Considerare le seguenti condizioni:

* L'amministratore di sistema conosca il contratto di servizio per i servizi o server che ospitano l'origine dati.
* L'amministratore del database sa la pianificazione di backup per ogni database e le finestre di elaborazione ETL consentiti.
* Il proprietario del sistema SA del processo per gli utenti dovranno richiedere l'accesso all'origine dati.
* Amministratore dei dati SA corrispondenze tra le attività e gli attributi nell'origine dati al modello di dati dell'organizzazione.
* Il analyst sa come i dati vengono utilizzati nel contesto di processi aziendali che supporta.

Ognuna di queste prospettive è valida e catalogo dati utilizza un approccio crowdsourcing ai metadati che consente di ciascuno di essi acquisite e usate per offrire un quadro completo di origini dati registrate. Tramite il portale di catalogo dati, ogni utente può aggiungere e modificare la propria annotazioni, la possibilità di visualizzare le annotazioni fornite da altri utenti.

## <a name="different-types-of-annotations"></a>Diversi tipi di annotazioni
Catalogo dati supporta i tipi di annotazioni seguenti:

| Annotazioni     | Note                                                                                                                                                                                                                                                                                                                                                           |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nome descrittivo  | Livello di dati bene, per rendere le risorse dati più facilmente comprensibile, è possono fornire nomi. Nomi sono particolarmente utili quando il nome dell'oggetto sottostante è difficile, abbreviato o in caso contrario non significativa agli utenti.                                                                                                                            |
| Descrizione    | Le risorse dati e attributo, è possono fornire descrizioni / livelli di colonna. Descrizione degli elementi sono annotazioni di testo breve in formato libero che descrivono l'utente prospettiva sulla bene dati o il suo utilizzo.                                                                                                                                                              |
| Tag (tag utente)          | Tag possono essere inseriti nelle risorse dati e degli attributi / livelli di colonna. Tag utente sono etichette definite dall'utente che possono essere utilizzate per suddividere in categorie di dati o gli attributi.                                                                                                                                                                                                    |
| Tag (tag glossario)          | Tag possono essere inseriti nelle risorse dati e degli attributi / livelli di colonna. Glossario tag sono definiti in modo centralizzato glossario che può essere usati per categorizzare risorse dati o gli attributi utilizzando una tassonomia business comuni. Per ulteriori informazioni vedere [come configurare il glossario Business per il Tagging regolata](data-catalog-how-to-business-glossary.md)                                                                                                                                                                                                    |
| Esperti        | Livello di risorse dei dati, è possono fornire esperti. Esperti di identificare gli utenti o gruppi con esperti prospettive sui dati e possono fungere da punti di contatto per gli utenti alla scoperta di origini dati registrate ed eventuali domande alle quali non ha ricevuto risposta per le annotazioni esistenti.  |
| Richiedere l'accesso | Al livello di risorsa dati, è possono fornire informazioni di accesso richiesta. Queste informazioni sono per gli utenti alla scoperta di un'origine dati che non si dispongono ancora di autorizzazioni di accesso. Gli utenti possono immettere l'indirizzo di posta elettronica dell'utente o gruppo chi concede l'accesso, l'URL del processo o strumento che gli utenti devono accedere, oppure immettere lo stesso processo come testo. |
| Documentazione | Livello di risorse dei dati, è possibile fornire la documentazione. Documentazione di risorse è possibile RTF informazioni che è possibile includere immagini e collegamenti e che può fornire le informazioni non trasferite tramite le descrizioni e categorie. |


## <a name="annotating-multiple-assets"></a>Inserire le note più risorse
Quando si selezionano più risorse di dati nel portale di catalogo dati, gli utenti possono annotare tutte le risorse selezionate in un'unica operazione. Annotazioni vengono applicate a tutte le risorse selezionate, rendendo più semplice selezionare e fornire una descrizione coerente e set di tag ed esperti per risorse dati correlati.

> [AZURE.NOTE] Tag ed esperti possono essere forniti anche quando la registrazione beni di dati tramite i dati del catalogo dati di origine strumento per la registrazione.

Quando la selezione di più tabelle e viste, solo le colonne tutti selezionati dati risorse in corrono verrà visualizzata nel portale di catalogo dati. In questo modo agli utenti di utilizzare tag e le descrizioni per tutte le colonne con lo stesso nome per tutte le attività selezionate.

## <a name="annotations-and-discovery"></a>Annotazioni e individuazione
Come i metadati estratti dall'origine dati durante la registrazione viene aggiunto all'indice di ricerca del catalogo dati, viene indicizzato anche metadati fornito dall'utente. Ciò significa che non solo le annotazioni rendono più semplice per gli utenti a comprendere i dati che sono individuare, annotazioni rendono più semplice per gli utenti individuare le risorse dati annotazioni mediante la ricerca con i termini indicati in base allo ad essi.

## <a name="summary"></a>Riepilogo
Registrazione di un'origine dati con il catalogo dati rende i dati individuabile copiando strutturali e descrittivo i metadati dall'origine dati al servizio di catalogo. Una volta registrata un'origine dati, gli utenti possono fornire le annotazioni per rendere più semplice individuare e comprendere all'interno del portale di catalogo dati.

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva a catalogo dati di Azure](data-catalog-get-started.md) esercitazione per informazioni dettagliate su come aggiungere note alle origini dati.
