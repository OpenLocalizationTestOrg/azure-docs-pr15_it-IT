<properties
   pageTitle="Creare collegamenti negli articoli delle vendite promozionali" description="In questo articolo viene spiegato come codice collegamenti incrociati in vendite promozionali." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="02/03/2015" ms.author="tysonn" />

# <a name="linking-guidance-for-azure-technical-content"></a>Collegamento Guida per il contenuto tecnico Azure

### <a name="links-from-one-acom-article-to-another"></a>Collegamenti dall'articolo ACOM uno a altro

Per creare un collegamento all'interno del testo da un articolo tecnico ACOM a un altro articolo tecnico ACOM, utilizzare la sintassi di collegamento seguente:  

- Un articolo in un collegamenti al servizio directory a un altro articolo nella stessa directory di servizio:

  `[link text](article-name.md)`

- Un articolo fornisce collegamenti da una sottodirectory servizio a un articolo nella directory principale:

  `[link text](../article-name.md)`

- Un articolo nei collegamenti di directory radice a un articolo in una sottodirectory di servizio: 

  `[link text](./service-directory/article-name.md)`

- Un articolo in un collegamenti sottodirectory servizio a un articolo in un'altra sottodirectory di servizio:

  `[link text](../service-directory/article-name.md)`
 

## <a name="links-to-anchors"></a>Collegamenti per gli ancoraggi

Non è necessario creare gli ancoraggi - vengono generate automaticamente all'ora per tutti i titoli H2 di pubblicazione. L'unica cosa che è necessario effettuare è creare collegamenti alle sezioni H2.

- Per creare un collegamento a un'intestazione all'interno dell'articolo stesso:

  `[link](#the-text-of-the-H2-section-separated-by-hyphens)`  
  `[Create cache](#create-cache)`

- Per creare un collegamento a un ancoraggio in un altro articolo nella stessa sottodirectory:

  `[link text](article-name.md#anchor-name)`
  `[Configure your profile](media-services-create-account.md#configure-your-profile)`

- Per creare un collegamento a un ancoraggio in un'altra sottodirectory di servizio:

  `[link text](../service-directory/article-name.md#anchor-name)`
  `[Configure your profile](../service-directory/media-services-create-account.md#configure-your-profile)`

Per automatizzare il processo di creazione di collegamenti negli articoli ai collegamenti ancoraggio generato automaticamente è [MarkdownAnchorLinkGenerator - uno strumento per generare i collegamenti di ancoraggio per ACOM nel formato corretto](https://github.com/Azure/Azure-CSI-Content-Tools/tree/master/Tools/ACOMMarkdownAnchorLinkGenerator).

## <a name="links-from-includes"></a>Includono collegamenti da

Poiché includere file si trovano in un'altra directory, è necessario utilizzare più percorsi relativi, come illustrato di seguito. Per creare un collegamento a un articolo da un file di inclusione, utilizzare questo formato:

    [link text](../articles/service-folder/article-name.md)
    
Ulteriori informazioni sull'utilizzo di un file di inclusione [alle linee guida estensioni delle vendite promozionali personalizzata](custom-markdown-extensions.md#includes).

## <a name="links-in-selectors"></a>Collegamenti nelle selettori

Se si dispone di selettori incorporati in un Includi, utilizzare questo tipo di collegamento: 

    > [AZURE. ELENCO Selettore (Dropdown1 | Dropdown2)]     -  [(Testo1 | Esempio1)](../articles/service-folder/article-name1.md)
    - [(Testo1 | Example2)](../articles/service-folder/article-name2.md)
    - [(Testo2 | Example3)](../articles/service-folder/article-name3.md)
    - [(Testo2 | Example4)](../articles/service-folder/article-name4.md)


## <a name="reference-style-links"></a>Collegamenti di stile di riferimento

È possibile utilizzare i collegamenti di stile di riferimento per semplificare la lettura del contenuto di origine. I collegamenti di stile di riferimento sostituiscono la sintassi di collegamento all'interno del testo con sintassi semplificata che consente di spostare gli URL lunghi alla fine dell'articolo. Di seguito è riportato di coraggio Fireball:

Testo all'interno del testo:

    I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3].

Creare un collegamento riferimenti alla fine dell'articolo:

    <!--Reference links in article-->
    [1]: http://google.com/
    [2]: http://search.yahoo.com/  
    [3]: http://search.msn.com/

Assicurarsi di includere lo spazio dopo i due punti, prima del collegamento. Quando si collega ad altri articoli tecnici, se si dimentica di includere lo spazio, il collegamento verrà suddiviso in articolo pubblicato. 

## <a name="link-to-acom-pages-that-are-not-part-of-the-technical-documentation-set"></a>Creare un collegamento a pagine ACOM che non fanno parte del set di documentazione tecnica

Per creare un collegamento a una pagina in ACOM (ad esempio una pagina prezzo, pagina Contratto di servizio o qualsiasi altro elemento che non è un articolo documentazione), utilizzare un URL assoluto, ma omettere le impostazioni locali. L'obiettivo è compatibili collegamenti in GitHub e nel sito di rendering:

    [link text](http://azure.microsoft.com/pricing/details/virtual-machines/)


## <a name="link-to-msdn-or-technet"></a>Collegamento al sito Web MSDN o TechNet

Quando è necessario creare un collegamento a MSDN o TechNet, utilizzare il collegamento completo per l'argomento e rimuovere il en-us lingua dal collegamento. 

### <a name="use-friendly-link-text-for-all-links"></a>Usare un testo descrittivo collegamento per tutti i collegamenti

Le parole che includere un collegamento devono essere descrittivo: in altre parole, deve essere normale parole inglesi o il titolo della pagina da collegare. Non utilizzare "fare clic qui". È errato per SEO e non per descrivere adeguatamente di destinazione.

**Correggere:**

- `For more information, see the [contributor guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`

- `For more details, see the [SET TRANSACTION ISOLATION LEVEL](https://msdn.microsoft.com/library/ms173763.aspx) reference.`

**Non corretti:**

- `For more details, see [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).`

- `For more information, click [here](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`


## <a name="fwlinks"></a>Fwlink

Evitare fwlink (il sistema di reindirizzamento) azure.microsoft.com contenuto. Si dovrebbe essere usate solo come ultima risorsa quando è necessario creare un collegamento per una pagina il cui URL non si conosce. Non è quasi mai effettivamente sono necessari. Per ACOM, definire il nome del file, per sapere cosa occorre anticipo. Per l'argomento di una raccolta non ancora pubblicata, è possibile creare un collegamento che utilizza l'argomento GUID in modo che non è necessario utilizzare un collegamento.

Se è necessario utilizzare un collegamento in una pagina web, includere il parametro P per rendere un reindirizzamento permanente:

    http://go.microsoft.com/fwlink/p/?LinkId=389595

Quando si incolla l'URL di destinazione nello strumento di collegamento, ricordarsi di rimuovere le impostazioni locali se il collegamento di destinazione è una raccolta ACOM, o il sito Web MSDN o TechNet.

## <a name="remember-the-azure-library-chrome"></a>Ricordare chrome raccolta Azure.
Se si desidera creare un collegamento a un argomento di Azure raccolta che si trova in [questo nodo](https://msdn.microsoft.com/library/azure), non dimenticare di specificare il Azure chrome nella casella collegamento (azure /). Chrome Azure condivide le opzioni di spostamento ACOM e viene visualizzato solo il contenuto di MSDN library Azure. Un collegamento in modo corretto dell'ambito è simile alla seguente:

    http://msdn.microsoft.com/library/azure/dd163896.aspx

In caso contrario, verrà visualizzata la pagina nella visualizzazione MSDN standard, con l'intera struttura MSDN visualizzato.

### <a name="contributors-guide-links"></a>Collegamenti di Guida dei collaboratori

- [Articolo di panoramica](./../README.md)
- [Indice degli articoli di Guida](./contributor-guide-index.md)

<!--image references-->
[1]: ./media/create-tables-markdown/table-markdown.png
[2]: ./media/create-tables-markdown/break-tables.png
