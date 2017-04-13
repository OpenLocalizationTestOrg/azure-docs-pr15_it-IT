

#<a name="metadata-for-azure-technical-articles"></a>Metadati di articoli tecnici Azure

Tutti gli articoli tecnici Azure contengano due sezioni di metadati - una sezione di proprietà e una sezione di tag. La sezione proprietà consente di alcuni automazione sito Web e oggetti SEO, mentre la sezione tag consente numerose segnalazione di contenuto interno. Entrambe le sezioni sono necessari.

- [Sintassi]
- [Uso]
- [Attributi e i valori per la sezione proprietà]
- [Attributi e i valori per la sezione tag]

##<a name="syntax"></a>Sintassi

Nella sezione proprietà viene utilizzata la sintassi seguente:

    <properties
       pageTitle="Page title that displays in search results and the browser tab | Microsoft Azure"
       description="Article description that will be displayed on landing pages and in most search results"
       services="service-name"
       documentationCenter="dev-center-name"
       authors="GitHub-alias-of-only-one-author"
       manager="manager-alias"
       editor=""
       tags="optional"
       keywords="For use by SEO champs only. Separate terms with commas. Check with your SEO champ before you change content in this article containing these terms."/>

Nella sezione tag viene utilizzata la sintassi seguente:

    <tags
       ms.service="required"
       ms.devlang="may be required"
       ms.topic="article"
       ms.tgt_pltfrm="may be required"
       ms.workload="na"
       ms.date="mm/dd/yyyy"
       ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

##<a name="usage"></a>Uso

- Il nome dell'elemento e i nomi degli attributi sono tra maiuscole e minuscole.
- Il <properties> sezione deve essere la prima riga del file.
- Lasciare una riga vuota dopo ogni sezione di metadati e prima il titolo della pagina per assicurarsi che il titolo della pagina correttamente viene convertito in HTML durante il processo di pubblicazione.

## <a name="attributes-and-values-for-the-properties-section"></a>Attributi e i valori per la sezione proprietà

![](./media/article-metadata/checkmark-small.png)**pageTitle**: obbligatorio; importante SEO. La scheda del browser e come il titolo di un risultato di ricerca, verrà visualizzato il testo per l'attributo. Usare i caratteri 55 60 inclusi gli spazi e l'identificatore del sito *, compresi | Microsoft Azure* (tipizzata come: in base allo spazio spazio barra verticale Microsoft Azure).  Il pageTitle deve essere diverse da H1.

![](./media/article-metadata/checkmark-small.png)**Descrizione**: obbligatorio; importante per SEO (pertinenza) e funzionalità del sito. La descrizione dovrebbe essere almeno 125 caratteri tempo a 155 caratteri massimo spazi inclusi. Descrivere lo scopo del contenuto in modo che i clienti saprà se si desidera scegliere da un elenco dei risultati della ricerca. Il valore è:

- Il testo visualizzato come la descrizione o un paragrafo astratto nei risultati della ricerca su Google.
- Questo testo viene visualizzato nei [risultati della indice articolo](https://azure.microsoft.com/documentation/articles/).

![](./media/article-metadata/checkmark-small.png)**servizi**: necessari per trovare articoli relativi a un servizio. Questo valore viene definito ofter "servizio indicazioni". Elencare tutti i servizi applicabili, separati da virgole. Il primo servizio che indicano determinerà la barra di navigazione spostamento per la pagina e barra di spostamento sinistra che viene visualizzata con la pagina.

Negli articoli che specificano un valore di servizi e un valore documentationCenter, il valore di servizi determinerà il percorso di navigazione. Valori aggiuntivi che si elenco verrà visualizzato come tag nell'articolo pubblicato. Valori:

- Active directory
- Active directory-b2c
- dominio Active directory Active directory
- App service\api
- gestione delle API
- servizio di App
- App servic\mobile
- App service\web
- App service\logic
- gateway di applicazioni
- informazioni dettagliate sui applicazione
- automazione
- portale di Azure
- Gestione risorse di Azure
- pila di Azure
- copia di backup
- batch
- procedure consigliate
- servizi di BizTalk
- cache
- rete CDN
- servizi cloud
- Catalogo dati
- factory di dati
- analitica di lake dati
- archivio di lake dati.
- laboratorio devtest
- DNS
- documentdb
- expressroute
- hub di evento
- hdinsight
- hub IOT
- archivio di chiave
- bilanciamento del carico
- apprendimento
- Marketplace
- servizi multimediali
- partecipazione attiva mobile
- servizi di dispositivi mobili
- autenticazione fattori
- hub di notifica
- Approfondimenti operativi
- gruppo di gestione delle operazioni
- powerapps
- gestione di ripristino
- redis cache
- RemoteApp
- Rights management.
- utilità di pianificazione
- ricerca
- Centro protezione
- bus di servizio
- servizio tessuti
- ripristino del sito
- database SQL
- SQL data warehouse
- creazione di report SQL
- spazio di archiviazione
- archivio
- storsimple
- flusso analitica
- gestore del traffico
- macchine virtuali
- rete virtuale
- Visual online studio
- gateway VPN
- siti Web

![](./media/article-metadata/checkmark-small.png)**documentationCenter**: necessario per incentrate sui dev articoli migliori in primo piano tramite un centro per sviluppatori. Specificare il centro per sviluppatori singola o la lingua che si applica all'articolo. Il valore che è elencare determinerà la barra di navigazione spostamento per la pagina. Negli articoli che specificano un valore di servizi e un valore documentationCenter, il valore di servizi determinerà il percorso di navigazione. Valori:

- **.NET**
- **nodejs**
- **Java**
- **PHP**
- **Python**
- **Trascrizione**
- **dispositivi mobili**: deprecata. Sostituire con piattaforma mobile specifica.
- **IOS**: Verifing il nuovo valore
- **Android**: verificare il nuovo valore
- **Windows**: verificare il nuovo valore
- **xamarin**: verificare il nuovo valore

![](./media/article-metadata/checkmark-small.png)**autori**: obbligatoria, un solo valore. L'account GitHub per l'autore principale o articolo PMI dell'elenco. Questo attributo unità l'autore su articolo pubblicato. Elencare sola nonostante il nome dell'attributo plurale.

![](./media/article-metadata/checkmark-small.png)**Manager**: richiesto se si è un collaboratore Microsoft. L'alias di posta elettronica del gestore di pubblicazione del contenuto per l'area di tecnologia dell'elenco. Se si è un collaboratore della community, includere l'attributo ma lasciare vuoto il campo in modo che è possibile compilare.

![](./media/article-metadata/checkmark-small.png)**Editor**: non utilizzati. Non utilizzarlo per altri scopi.

![](./media/article-metadata/checkmark-small.png)**tag**: facoltativo. Includere solo se si desidera abilitare un collegamento sotto la barra di navigazione articolo alla pagina di indice articolo (http://azure.microsoft.com/documentation/articles/) a un elenco di articoli che corrispondono a uno dei valori approvati prefiltered. Questi valori utilizzati per fornire un modo per raggruppare contenuto quando il raggruppamento contenuto non è specifico del servizio. Questi tag anche possibile etichette che indica la pila tecnologia a che l'articolo si applica. Questo valore **non** supporta i tag in formato libero o hashtags; i contrassegni che devono essere abilitati nel sito. È possibile fornire più valori di tag a un articolo, separati da virgole. I valori riconosciuti sono:

  - architettura
  - Gestione risorse di Azure
  - gestione dei servizi di Azure
  - fatturazione
  - MySQL

![](./media/article-metadata/checkmark-small.png)**parole chiave**: facoltativo. Per l'utilizzo da solo champs SEO. Termini separati da virgole. **Contattare il rubricista SEO prima di modificare o eliminare il contenuto in questo articolo che contiene i termini che seguono.** Questo attributo record parole chiave rubricista SEO è riservata e verifica per migliorare la classificazione di ricerca. Le parole chiave non visualizzano nell'HTML pubblicato. Convalida non richiede l'attributo.

## <a name="attributes-and-values-for-the-tags-section"></a>Attributi e i valori per la sezione tag

![](./media/article-metadata/checkmark-small.png)**ms.Service**: obbligatorio. Specifica il servizio di Azure, strumento o funzionalità che l'articolo si applica a. Un valore per ogni pagina.

 Se una pagina si applica a più servizi, scegliere il servizio in cui la maggior parte applicata direttamente; ad esempio, un articolo che utilizza un'app ospitata su siti web per dimostrare funzionalità Bus di servizio deve avere valore **bus di servizio** invece di **siti web**. Se una pagina si applica a più servizi in modo uniforme, scegliere **più**. Se una pagina non si applica a tutti i servizi (questo sarà raro), scegliere **ND**.

 - **Active directory**
 - **Active directory-b2c**
 - **dominio Active directory Active directory**
 - **gestione delle API**
 - **servizio di App**: si applica solo a materiale concettuale generale del servizio di App
 - **api del servizio di App**
 - **logica di servizio App**
 - **dispositivi mobili di servizio App**
 - **applicazione del servizio web**
 - **informazioni dettagliate sui applicazione**
 - **gateway di applicazioni**
 - **automazione**
 - **Gestione risorse di Azure**
 - **sicurezza di Windows Azure**
 - **pila di Azure**
 - **copia di backup**
 - **batch**
 - **procedure consigliate**
 - **servizi di BizTalk**
 - **fatturazione**
 - **cache**
 - **rete CDN**
 - **servizi cloud**
 - **Catalogo dati**
 - **archivio di lake dati.**
 - **analitica di lake dati**
 - **laboratorio devtest**
 - **expressroute**
 - **hdinsight**
 - **Internet di elementi**
 - **hub IOT**
 - **archivio di chiave**
 - **apprendimento**
 - **Marketplace**: articoli su Azure marketplace
 - **servizi multimediali**
 - **partecipazione attiva mobile**
 - **servizi di dispositivi mobili**
 - **autenticazione fattori**
 - **più**: la pagina si applica a più servizi identico
 - **ND**: la pagina non si applica a tutti i servizi (rari)
 - **hub di notifica**
 - **Approfondimenti operativi**
 - **powerapps**
 - **gestione di ripristino**
 - **redis cache**
 - **RemoteApp**
 - **Rights management.**
 - **utilità di pianificazione**
 - **Centro protezione**
 - **bus di servizio**
 - **servizio tessuti**
 - **il ripristino del sito**: servizi di recupero in precedenza
 - **database SQL**
 - **SQL data warehouse**
 - **creazione di report SQL**
 - **spazio di archiviazione**
 - **archiviare**: articoli relativi a servizi disponibili tramite l'archivio di Azure
 - **storsimple**
 - **gestore del traffico**
 - **macchine virtuali**
 - **rete virtuale**
 - **Visual online studio**
 - **gateway VPN**
 - **siti Web**

![](./media/article-metadata/checkmark-small.png)**ms.DevLang**: obbligatorio. Specifica il linguaggio di programmazione che l'articolo si applica a. Singolo valore per ogni pagina.

 Se una pagina è applicabile anche a due linguaggi di programmazione, scegliere **più**. Se una pagina è principalmente concettuale e il relativo contenuto è in genere applicabile a più linguaggi di programmazione, scegliere **più**. Se una pagina non è riservata agli sviluppatori e la programmazione applicabilità lingua non è rilevante, scegliere **ND**. Usare **l'api rest** per identificare gli argomenti della Guida di riferimento all'API REST.

 - **cpp**
 - **dotnet**
 - **Java**
 - **JavaScript**
 - **più**: la pagina si applica a più linguaggi di programmazione identico.
 - **ND**: la pagina non prevede gli sviluppatori e non è specifica di qualsiasi linguaggi di programmazione.
 - **nodejs**
 - **obiettivo-c**
 - **PHP**
 - **Python**
 - **api REST**
 - **Trascrizione**


![](./media/article-metadata/checkmark-small.png)**ms.topic**: obbligatorio. Digitare l'argomento di informazioni specifiche. La maggior parte delle nuove pagine create da parte di sostenitori sarà articolo o un riferimento.

 - **articolo**: un argomento concettuale, esercitazione, Guida caratteristica o altri articolo non di riferimento

 - **pagina della campagna**: solo Azure.com.  Una pagina in cui è progettata come una pagina di destinazione per le campagne esterne e non è incluso come parte del sito principale IA.  Non devono essere utilizzate per articoli documentazione o documento regolare le pagine di destinazione.  Esempi: azure.microsoft.com/develop/net/aspnet/; Azure.microsoft.com/Develop/mobile/IOS/

 - **DEV center-home page**: solo Azure.com.  Un dev center nella home page, ad esempio/sviluppare/net /

 - **articolo avviato Get**: assegnare ad articoli in primo piano nella sezione introduzione o una panoramica della barra di spostamento sinistra di un servizio.

 - **articolo eroe**: un'esercitazione "eroe" in cui è progettata per fornire un'introduzione a un servizio o funzionalità che ottiene i visitatori a utilizzare rapidamente il servizio e unità di prova gratuito iscrizioni e attivazioni MSDN. Assegnare il valore solo ad articoli in primo piano nella parte superiore della pagina documentazione per il servizio.

 - **home page**: home page di documentazione di livello superiore. Abbiamo solo due: azure.microsoft.com/documentation/ e msdn.microsoft.com/library/azure/

 - **pagina di indice**: secondo livello le pagine per la programmazione di lingue, servizi o le caratteristiche di destinazione. Questi sono distribuiti in Azure.com e la raccolta e vengono utilizzati come punti di ingresso per informazioni più specifiche, dell'ambito. Esempi: http://azure.microsoft.com/develop/mobile/resources-wp8/ http://msdn.microsoft.com/library/azure/jj673460.aspx, http://msdn.microsoft.com/library/azure/hh689864.aspx

 - **pagina infographic**: solo Azure.com. Una pagina in cui sono presenti un infographic visualizzabile o poster, ad esempio http://azure.microsoft.com/documentation/infographics/windows-azure/

 - **riferimento**: API di riferimento pagina (inclusi API REST) o PowerShell cmdlet di riferimento

 - **pagina iniziale del servizio**: solo Azure.com.  Documento servizio pagina iniziale, ad esempio /documentation/services/virtual-machines /

 - **pagina iniziale di sezione del sito**: Azure.com solo. "Home page" per un determinato tipo di contenuto azure.com esempi: http://azure.microsoft.com/documentation/infographics/, http://azure.microsoft.com/documentation/scripts/, http://azure.microsoft.com/documentation/videos/home/, http://azure.microsoft.com/downloads/

 - **pagina video**: solo Azure.com.  Una pagina in cui sono presenti un video, ad esempio http://azure.microsoft.com/documentation/videos/azure-webjobs-hosting-testing-net/

![](./media/article-metadata/checkmark-small.png)**ms.tgt_pltfrm**: obbligatorio. Specifica la piattaforma di destinazione, ad esempio Windows, Linux, Windows Phone, iOS, Android o piattaforme cache speciale. Un valore per ogni pagina. Questo valore deve essere **ND** per la maggior parte delle argomenti ad eccezione di dispositivi mobili e macchine virtuali.

 - **ruolo cache**
 - **più cache**
 - **cache redis**
 - **servizio di cache**
 - **cache condivisa**
 - **comando riga interfaccia**
 - **Ibiza**: il contenuto che utilizza il portale di Ibiza. Questa scheda solo nei casi in cui la caratteristica della discussione è disponibile attraverso il portale di Ibiza e il portale corrente.
 - **dispositivo mobile android**: Azure.com solo immediatamente
 - **html mobile**: Azure.com solo immediatamente
 - **dispositivi mobili ios**: Azure.com solo immediatamente
 - **dispositivo kindle mobile**: Azure.com solo immediatamente
 - **più dispositivi mobili**
 - **dispositivi mobili-nokia-x**: Azure.com solo immediatamente
 - **phonegap mobile**: Azure.com solo immediatamente
 - **sencha mobile**: Azure.com solo immediatamente
 - **windows mobile**: Azure.com solo immediatamente; Universale di Windows
 - **cellulare di windows**
 - **archivio di windows mobile**
 - **xamarin mobile**: Azure.com solo immediatamente; Xamarin tutte le piattaforme
 - **mobile xamarin android**: Azure.com solo immediatamente
 - **ios di xamarin mobile**: Azure.com solo immediatamente
 - **più**: la pagina si applica a più piattaforme identico
 - **ND**: uno specificatore di piattaforma non è applicabile per questa pagina
 - **PowerShell**
 - **macchine virtuali linux**
 - **più macchine virtuali**
 - **finestre SV**
 - **macchina virtuale windows sharepoint**
 - **macchine virtuali-windows-sql server**
 - **VS--introduzione**: identifica il gruppo di pagina Guida introduttiva VS. Aggiungere tag 1/12/14.
 - **VS-simulazione-relative**: identifica la pagina VS Guida introduttiva non più disponibile. Aggiungere tag 1/12/14.

![](./media/article-metadata/checkmark-small.png)**ms.Workload**: obbligatorio. Specifica il carico di lavoro Azure che la pagina si applica a. Un valore solo per ogni articolo.

**aggiornare 8/6/15** Il valore di ms.workload mappato da un file xls, non il valore nel file MD. Il valore di ms.workload è ancora necessario per la convalida fino a quando non è possibile aggiornare la caratteristica. Il lavoro a questo punto viene programmato in base.  Utilizzare **"na"** come valore per il momento.

![](./media/article-metadata/checkmark-small.png)**ms.date**: obbligatorio. Specifica la data che ultima revisione dell'articolo per pertinenza, precisione, schermate corretto e i collegamenti di lavoro. Immettere la data nel formato gg/mm/aaaa. Questa data verrà visualizzata anche l'articolo pubblicato come data dell'ultimo aggiornamento.

![](./media/article-metadata/checkmark-small.png)**ms.Author**: obbligatorio. Specifica degli autori associati all'argomento. Report interni (ad esempio validità) usare questo valore per associare degli autori destro dell'articolo. Per specificare più valori è necessario separarli con punti e virgola. Gli alias di Microsoft o gli indirizzi di posta elettronica completo sono accettabili. La lunghezza può essere non più di 200 caratteri.


###<a name="contributors-guide-links"></a>Collegamenti di Guida dei collaboratori

- [Articolo di panoramica](./../README.md)
- [Indice degli articoli di Guida](./contributor-guide-index.md)


<!--Anchors-->
[Sintassi]: #syntax
[Uso]: #usage
[Attributi e i valori per la sezione proprietà]: #attributes-and-values-for-the-properties-section
[Attributi e i valori per la sezione tag]: #attributes-and-values-for-the-tags-section
