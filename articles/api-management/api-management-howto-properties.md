<properties 
    pageTitle="Come usare le proprietà in Criteri di gestione delle API di Azure" 
    description="Informazioni su come utilizzare le proprietà in Criteri di gestione delle API di Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Come usare le proprietà in Criteri di gestione delle API di Azure

Criteri di gestione delle API sono una funzionalità potente del sistema che consentono l'autore che si desidera modificare il comportamento dell'API tramite la configurazione. I criteri sono un insieme di istruzioni che vengono eseguite in sequenza nella richiesta o risposta di un'API. È possibile creare istruzioni di criteri utilizzando i valori di testo letterale, le espressioni di criteri e le proprietà. 

Ogni istanza del servizio di gestione delle API dispone di un insieme di proprietà di coppie chiave/valore valide per l'istanza del servizio. Queste proprietà possono essere usate per gestire i valori stringa costante in tutti i criteri e le API configuration. Ogni proprietà presenta gli attributi seguenti.


| Attributo | Tipo            | Descrizione                                                                                             |
|-----------|-----------------|---------------------------------------------------------------------------------------------------------|
| Nome      | stringa          | Il nome della proprietà. Possono contenere solo lettere, cifre, periodo, trattino e caratteri di sottolineatura. |
| Valore     | stringa          | Il valore della proprietà. Potrebbe non essere vuota o essere costituito solo da uno spazio vuoto.                           |
| Segreto    | valore booleano         | Determina se il valore è un segreto e deve essere crittografato o meno.                                |
| Tag      | Matrice di stringa | Tag facoltativo che quando viene fornito può essere utilizzato per filtrare l'elenco di proprietà.                               |

Proprietà sono configurate nel portale di publisher nella scheda **proprietà** . Nell'esempio seguente vengono configurate tre proprietà.

![Proprietà][api-management-properties]

Valori di proprietà possono contenere stringhe letterali ed [espressioni di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx). Nella tabella seguente mostra le proprietà di tre esempio precedente e i relativi attributi. Il valore di `ExpressionProperty` è un'espressione criterio che restituisce una stringa contenente la data e ora correnti. La proprietà `ContosoHeaderValue` è contrassegnato come un segreto, in modo che non è visualizzato il relativo valore.

| Nome               | Valore                      | Segreto | Tag    |
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | TrackingId                 | FALSO  | Contoso |
| ContosoHeaderValue | ••••••••••••••••••••••     | Vero   | Contoso |
| ExpressionProperty | @(DateTime.Now.ToString()) | FALSO  |         |

## <a name="to-use-a-property"></a>Usare una proprietà

Per utilizzare una proprietà in un criterio, inserire il nome della proprietà all'interno di una doppia coppia di parentesi graffe come `{{ContosoHeader}}`, come illustrato nell'esempio seguente.

    <set-header name="{{ContosoHeader}}" exists-action="override">
      <value>{{ContosoHeaderValue}}</value>
    </set-header>

In questo esempio, `ContosoHeader` viene utilizzato come il nome di intestazione in un `set-header` criterio, e `ContosoHeaderValue` viene utilizzato come valore dell'intestazione di tale. Quando il criterio viene valutato nel corso di una richiesta o risposta a gateway di gestione dell'API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` vengono sostituiti con i rispettivi valori di proprietà corrispondenti.

Proprietà possono essere utilizzate come attributi completo o valori di elementi, come illustrato nell'esempio precedente, ma anche possono essere inseriti in o combinati con parte di un'espressione di testo come illustrato nell'esempio seguente:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Proprietà possono inoltre contenere espressioni di criteri. Nell'esempio seguente la `ExpressionProperty` viene utilizzato.

    <set-header name="CustomHeader" exists-action="override">
        <value>{{ExpressionProperty}}</value>
    </set-header>

Quando viene valutato il criterio, `{{ExpressionProperty}}` sostituito con il rispettivo valore: `@(DateTime.Now.ToString())`. Poiché il valore è un'espressione di criteri, viene valutata l'espressione e i criteri procede con l'esecuzione.

È possibile verificare questo esterna nel portale per sviluppatori chiamando un'operazione che ha un criterio con le proprietà nell'ambito. Nell'esempio seguente viene chiamata un'operazione con dell'esempio precedente due `set-header` criteri con le proprietà. Si noti che la risposta contiene due intestazioni personalizzate configurato per l'utilizzo dei criteri con proprietà.

![Portale per sviluppatori][api-management-send-results]

Se osserva la [traccia di controllo API](api-management-howto-api-inspector.md) per una chiamata che include due criteri di esempio precedente con il comando proprietà, è possibile visualizzare i due `set-header` criteri con i valori di proprietà inseriti dell'utente e la valutazione dell'espressione dei criteri per la proprietà che conteneva l'espressione criterio.

![Traccia API controllo][api-management-api-inspector-trace]

Si noti che mentre i valori di proprietà possono contenere espressioni di criteri, i valori di proprietà non possono contenere altre proprietà. Se il testo che contiene un riferimento di proprietà viene utilizzato per un valore di proprietà, ad esempio `Property value text {{MyProperty}}`, che fanno riferimento proprietà non è possibile sostituire e sarà incluso come parte del valore della proprietà.

## <a name="to-create-a-property"></a>Per creare una proprietà

Per creare una proprietà, fare clic su **Aggiungi proprietà** nella scheda **proprietà** .

![Aggiungere proprietà][api-management-properties-add-property-menu]

**Nome** e il **valore** sono valori obbligatori. Se il valore della proprietà non è un segreto, selezionare la casella di controllo **questo è un segreto** . Immettere uno o più tag facoltativo per agevolare la organizzare le proprietà e fare clic su **Salva**.

![Aggiungere proprietà][api-management-properties-add-property]

Quando si salva una nuova proprietà, la casella di testo di **proprietà di ricerca** viene popolata con il nome della nuova proprietà e viene visualizzata la nuova proprietà. Per visualizzare tutte le proprietà, deselezionare la casella di testo di **proprietà di ricerca** e premere INVIO.

![Proprietà][api-management-properties-property-saved]

Per informazioni sulla creazione di una proprietà utilizzando l'API REST, vedere [creare una proprietà utilizzando l'API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Per modificare una proprietà

Per modificare una proprietà, fare clic su **Modifica** accanto alla proprietà da modificare.

![Modifica proprietà][api-management-properties-edit]

Apportare le modifiche desiderate e fare clic su **Salva**. Se si cambia il nome della proprietà, tutti i criteri che fanno riferimento a tale proprietà vengono automaticamente aggiornati per utilizzare il nuovo nome.

![Modifica proprietà][api-management-properties-edit-property]

Per informazioni sulla modifica di una proprietà utilizzando l'API REST, vedere [modificare una proprietà tramite l'API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Per eliminare una proprietà

Per eliminare una proprietà, fare clic su **Elimina** accanto a proprietà da eliminare.

![Eliminazione di proprietà][api-management-properties-delete]

Fare clic su **Sì, Elimina** per confermare.

![Conferma eliminazione][api-management-delete-confirm]

>[AZURE.IMPORTANT] Se la proprietà fa riferimento tutti i criteri, non sarà possibile eliminare completata fino a quando la proprietà per rimuovere tutti i criteri che usarla.

Per informazioni sull'eliminazione di una proprietà tramite l'API REST, vedere [eliminare una proprietà utilizzando l'API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Ricerca e filtro proprietà

La scheda **proprietà** include caratteristiche che consentono di gestire le proprietà di filtro e la ricerca. Per filtrare l'elenco delle proprietà in base al nome di proprietà, immettere un termine di ricerca nella casella di testo **proprietà di ricerca** . Per visualizzare tutte le proprietà, deselezionare la casella di testo di **proprietà di ricerca** e premere INVIO.

![Ricerca][api-management-properties-search]

Per filtrare l'elenco delle proprietà per i valori di tag, immettere uno o più tag nella casella di testo **filtro in base a categorie** . Per visualizzare tutte le proprietà, deselezionare la casella di testo **filtrare in base al tag** e premere INVIO.

![Filtro][api-management-properties-filter]

## <a name="next-steps"></a>Passaggi successivi

-   Altre informazioni sull'uso dei criteri
    -   [Criteri di gestione delle API](api-management-howto-policies.md)
    -   [Guida di riferimento dei criteri](https://msdn.microsoft.com/library/azure/dn894081.aspx)
    -   [Espressioni di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Guardare un video introduttivo

> [AZURE.VIDEO use-properties-in-policies]

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

