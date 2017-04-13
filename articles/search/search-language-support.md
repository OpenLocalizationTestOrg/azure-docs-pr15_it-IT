<properties
   pageTitle="Creare un indice per i documenti in più lingue nella ricerca Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
   description=" Ricerca Azure supporta 56 lingue, sfruttando analisi lingua grazie alla tecnologia Lucene e l'elaborazione di linguaggio naturale da Microsoft."
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="07/14/2016"
   ms.author="jlembicz"/>

# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Creare un indice per i documenti in più lingue nella ricerca Azure
> [AZURE.SELECTOR]
- [Portale](search-language-support.md)
- [RESTO](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

Potenza di analisi del linguaggio è semplice come impostazione di una proprietà a un campo di ricerca nella definizione di indice. A questo punto è possibile eseguire questo passaggio nel portale.

Di seguito sono schermate delle pale portale Azure per la ricerca di Azure che consentono agli utenti di definire uno schema di indice. Blade, gli utenti possono creare tutti i campi e impostare la proprietà analyzer per ognuno di essi.

> [AZURE.IMPORTANT] Come in possibile impostare un analizzatore lingua durante la definizione di campo, solo quando si crea un nuovo indice da zero di o quando si aggiunge un nuovo campo a un indice esistente. Verificare che siano completamente tutti gli attributi, tra cui l'analizzatore durante la creazione del campo. Non sarà possibile modificare gli attributi o modificare il tipo di analisi dopo il salvataggio delle modifiche.

## <a name="define-a-new-field-definition"></a>Definire una nuova definizione di campo

1. Accedere al [Portale di Azure](https://portal.azure.com) e aprire e il servizio di servizio di ricerca.
2. Fare clic su **Aggiungi indice** nella barra dei comandi nella parte superiore del dashboard del servizio per iniziare un nuovo indice o aprire un indice esistente per impostare un analizzatore nuovi campi da aggiungere a un indice esistente.
3. Viene visualizzata e il campi, contenente le opzioni per la definizione dello schema dell'indice, inclusa la scheda Analyzer utilizzata per la scelta di un analizzatore lingua.
4. Nei campi, iniziare una definizione di campo per fornire un nome, scegliere il tipo di dati e impostazione di attributi per contrassegnare il campo full-text che supportano le ricerche, recuperabili nei risultati della ricerca, utilizzabili nelle strutture di spostamento facet, ordinabile e così via. 
5. Prima di passare al campo successivo, aprire la scheda **analisi** . 

   
![][1]
*Per selezionare un analizzatore, selezionare la scheda di analisi e il campi*

## <a name="choose-an-analyzer"></a>Scegliere un analizzatore

6. Scorrere fino a trovare il campo che si definisce. 
7. Se non è stato contrassegnato il campo di ricerca, fare clic su casella di controllo per contrassegnare come **ricercabile**.
8. Fare clic sull'area di analisi per visualizzare l'elenco di analisi disponibili.
9. Scegliere analizzatore di cui che si desidera utilizzare.

![][2]
*Selezionare una delle analisi supportati per ogni campo*

Per impostazione predefinita, tutti i campi che supportano le ricerche utilizzano l' [analizzatore Lucene Standard](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) che è indipendente dalla lingua. Per visualizzare l'elenco completo delle analisi supportati, vedere [Supporto delle lingue in Azure ricerca](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Dopo aver selezionato l'analizzatore di lingua per un campo, verrà utilizzata con ogni richiesta di ricerca e indicizzazione per il campo. Quando viene inviata una query in base a più campi mediante analisi diversi, la query verrà elaborata in modo indipendente dal analisi appropriate per ogni campo.

Molte applicazioni web e dispositivi mobili servono utenti in tutto il mondo con lingue diverse. È possibile definire un indice per uno scenario … mediante la creazione di un campo per ogni lingua supportata.

![][3]
*Definizione di indice con un campo descrizione per ogni lingua supportata*

Se si conosce la lingua dell'agente di esecuzione di una query, una richiesta di ricerca può esistere a un campo specifico utilizzando il parametro di query **searchFields** . Solo con la descrizione in polacco verrà inviata la query seguente:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

È possibile richiedere l'indice dal portale di usare **Esplora ricerche** per incollare in una query simile a quello illustrato sopra. Soluzioni di ricerca sono disponibile nella barra dei comandi in e il servizio. Per informazioni dettagliate, vedere [l'indice di ricerca di Azure nel portale di Query](search-explorer.md) .

Può succedere che la lingua dell'agente di esecuzione di una query non è nota, nel qual caso la query può essere inviata a tutti i campi contemporaneamente. Se necessario, è possibile definire preferenza per risultati ottimali in una determinata lingua utilizzando [i profili di classificazione](https://msdn.microsoft.com/library/azure/dn798928.aspx). Nell'esempio seguente, corrispondenze trovate nella descrizione in lingua inglese verranno considerato equivalente superiore rispetto corrispondenze in francese e polacco:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

Se si è uno sviluppatore .NET, tenere presente che è possibile configurare analisi lingua utilizzando [Azure ricerca .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search). La versione più recente supporta anche analisi del linguaggio Microsoft.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
