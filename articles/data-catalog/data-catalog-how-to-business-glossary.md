<properties
    pageTitle="Come configurare il glossario Business per disciplinata tagging | Microsoft Azure"
    description="Articolo procedure evidenziazione glossario business nel catalogo dati di Azure per definire e utilizzare un comuni terminologia aziendale a tag registrato risorse dati."
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

# <a name="how-to-set-up-the-business-glossary-for-governed-tagging"></a>Come configurare il glossario Business per il Tagging regolata

## <a name="introduction"></a>Introduzione

Catalogo dati di Azure offre funzionalità per l'individuazione di origine dati, consentendo agli utenti di individuare facilmente e comprendere le origini dati che è necessario eseguire l'analisi e prendere decisioni. Queste funzionalità di individuazione di verificare l'impatto più significativo quando gli utenti possono trovare informazioni più ampia gamma di origini dati disponibili.

Una funzionalità del catalogo dati che si promuove migliore comprensione dei dati di risorse è tagging. Tagging consente agli utenti di associare parole chiave a una risorsa o a una colonna, che a sua volta, è facile scoprire bene tramite ricerca o esplorazione, e consente agli utenti di capire più facilmente le contesto e lo scopo del bene.

Tuttavia, tagging in alcuni casi può causare problemi di una propria. Alcuni esempi dei problemi che possono essere introdotti dalla tagging sono:

1.  Utenti che utilizzano le abbreviazioni su alcune attività e testo espanso su altri durante il tagging. Questa differenza ostacola l'individuazione dei beni, anche se è stato lo scopo di contrassegnare le attività con lo stesso tag.
2.  Tag che si riferiscono ad elementi diversi in contesti diversi. Ad esempio un tag denominato "Ricavi" in un set di dati dei clienti potrebbe ricavi del cliente, ma lo stesso tag in un set di dati delle vendite trimestrali Impossibile indicano ricavi trimestrali per la società.  

Per informazioni su come risolvere questi e altri problemi simili, catalogo dati include un glossario Business.

Glossario di Business catalogo dati consente alle organizzazioni di condizioni aziendali cruciali per documenti e le relative definizioni per creare una terminologia business comuni. Questo governance consente la coerenza nell'utilizzo di dati in tutta l'organizzazione. Una volta termini sono definiti nel glossario business, può essere assegnati alle risorse di dati nel catalogo, con lo stesso approccio come social tagging, consentendo _disciplinata tagging_.

> [AZURE.NOTE] Le funzionalità descritte in questo articolo sono disponibili solo nel Standard Edition di Azure catalogo dati. La versione gratuita non sono disponibili funzionalità per il tagging gestite o un glossario business.

## <a name="glossary-availability-and-privileges"></a>Privilegi e la disponibilità di glossario

*Glossario business è disponibile nel Standard Edition di Azure catalogo dati. Dati catalogo gratis Edition non include un glossario.*

Glossario business accessibili tramite l'opzione "Glossario" nel menu di spostamento del portale catalogo dati.  

![Accesso glossario business](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)


Gli amministratori di catalogo dei dati e i membri del ruolo Administrators glossario possono creare, modificare ed eliminare glossario nel glossario business. Tutti gli utenti del catalogo dati consente di visualizzare le definizioni di termini e applicare i tag beni con i termini di glossario.

![Aggiunta di un nuovo termine di glossario](./media/data-catalog-how-to-business-glossary/02-new-term.png)


## <a name="creating-glossary-terms"></a>Creazione di glossario

Gli amministratori di catalogo dati e glossario creare nuovi termini glossario facendo clic su nuovo termine ' pulsante per creare glossario con i campi seguenti:

* Una definizione di business per il termine
* Una descrizione che acquisisce pensato o le regole di business per la risorsa/colonna
* Un elenco delle parti interessate che conoscono il massimo relative al termine
* Il termine padre, che definisce la gerarchia in cui è organizzato il termine


## <a name="glossary-term-hierarchies"></a>Gerarchie termini glossario

Glossario catalogo dati business offre la possibilità di descrivere la terminologia di business come una gerarchia di termini. Questa sezione consente alle organizzazioni di creare una classificazione di termini che meglio rappresenta la propria tassonomia business.

Il nome di un termine deve essere univoco in un determinato livello della gerarchia, non sono consentiti nomi duplicati. Illimitato al numero di livelli in una gerarchia, ma una gerarchia spesso più facile comprensione quando sono presenti tre livelli o meno.

Uso delle gerarchie nel glossario business è facoltativo. Lasciare l'elemento padre termini campo vuoto per glossario creerà un elenco di strutturati (non gerarchici) di termini nel glossario.  

## <a name="tagging-assets-with-glossary-terms"></a>Tagging risorse con il glossario

Dopo aver definito glossario all'interno del catalogo, l'esperienza di tagging risorse ottimizzata per cercare il glossario quando l'utente digita i tag. Portale del catalogo dati visualizza un elenco di corrispondenti glossario per l'utente tra cui scegliere. Se l'utente seleziona un termine glossario dall'elenco verrà aggiunto al bene come tag (noto anche tag di glossario). L'utente può anche scegliere di creare un nuovo tag digitando un termine non inclusa nel glossario (noto anche tag di utente).

![Risorse dati contrassegnate con due tag di glossario e tag di un utente](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [AZURE.NOTE] Tag utente sono l'unico tipo di tag supportato in Edition gratuita del catalogo dati.

### <a name="hover-behavior-on-tags"></a>Comportamento di Hover tag
Nel portale di catalogo dati i due tipi di tag sono visivamente distinti, con comportamento diverso al passaggio del mouse. Quando l'utente viene posizionato su un tag utente possono vedere il testo del tag e uno o più utenti che hanno aggiunto il contrassegno. Quando l'utente passa su un tag di glossario, vedono la definizione del termine del glossario e un collegamento per aprire il glossario business per visualizzare la definizione completa del termine.

### <a name="search-filters-for-tags"></a>Filtri di ricerca per categorie
Tag di glossario e tag utente sono che supportano le ricerche e possono essere applicate come filtri di una ricerca.

## <a name="summary"></a>Riepilogo
Glossario di business in catalogo dati di Azure e tagging gestite consente, consentire attività dati identificato, gestire e individuato in modo coerente. Glossario business può promuovere risorse della terminologia business tra gli utenti dell'organizzazione e supporta significativo per i metadati da acquisire, esecuzione individuazione e la comprensione semplicissimo.

## <a name="see-also"></a>Vedere anche

- [Documentazione API REST operazioni glossario aziendali](https://msdn.microsoft.com/library/mt708855.aspx)
