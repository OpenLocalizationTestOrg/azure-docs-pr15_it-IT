<properties
   pageTitle="Costruzione di stringhe di filtro per la progettazione di tabelle | Microsoft Azure"
   description="Costruzione di stringhe di filtro per la progettazione di tabelle"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="constructing-filter-strings-for-the-table-designer"></a>Costruzione di stringhe di filtro per la progettazione di tabelle

## <a name="overview"></a>Panoramica

Per filtrare i dati in una tabella di Azure che viene visualizzato nella finestra **Di progettazione tabelle**di Visual Studio, creare una stringa di filtro e immettere nel campo filtro. Nella sintassi della stringa filtro definita WCF Data Services ed è simile a una clausola WHERE SQL, ma viene inviata al servizio di tabella tramite una richiesta HTTP. **Progettazione tabelle** gestisce la codifica appropriata per l'utente, in modo da filtrare un valore di proprietà desiderata, è necessario immettere solo il nome della proprietà, operatore di confronto, criteri valore e facoltativamente Boolean operatore nel campo filtro. Non è necessario includere l'opzione di query $filter come se fosse creando un URL per la tabella tramite il [Riferimento all'API di spazio di archiviazione servizi resto](http://go.microsoft.com/fwlink/p/?LinkId=400447)della query.

WCF Data Services si basano [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Per informazioni dettagliate sull'opzione di query (**$filter**) sistema filtro, vedere la [specifica OData URI convenzioni](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Operatori di confronto

Gli operatori logici seguenti sono supportati per tutti i tipi di proprietà:

|Operatori logici|Descrizione|Stringa di filtro di esempio|
|---|---|---|
|EQ|Uguale|City eq 'Redmond'|
|gt|Maggiore|Prezzo gt 20|
|pagina|Maggiore o uguale a|Prezzo grandi 10|
|lt|Minore|Prezzo lt 20|
|le|Minore o uguale|Le prezzo 100|
|ne|Non è uguale a|Città ne "Londra"|
|e|E|Le prezzo 200 e prezzo gt 3.5|
|o|O|Le prezzo 3.5 o prezzo gt 200|
|non|Non|non isAvailable|

Quando si crea una stringa di filtro, sono importante le regole seguenti:

- Utilizzare gli operatori logici per confrontare una proprietà per un valore. Si noti che non è possibile confrontare una proprietà per un valore dinamico; un lato dell'espressione deve essere una costante.

- Tutte le parti della stringa di filtro vengono maiuscole e minuscole.

- Il valore della costante deve essere dello stesso tipo di dati come proprietà in ordine per il filtro per restituire i risultati validi. Per ulteriori informazioni sui tipi di proprietà supportati, vedere [informazioni sul modello di dati del servizio di tabella](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Filtro proprietà stringa

Quando applica un filtro nella proprietà stringa, racchiudere la costante stringa tra virgolette singole.

I filtri di esempio seguente nella proprietà **PartitionKey** e **RowKey** ; proprietà aggiuntive non chiave anche è stato aggiunto alla stringa di filtro:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

È possibile racchiudere ogni espressione di filtro tra parentesi, anche se non è necessario:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Si noti che il servizio di tabella non supporta le query con caratteri jolly e non supportati in Progettazione tabelle uno. Tuttavia, è possibile eseguire prefisso corrispondenti usando gli operatori di confronto sul prefisso desiderato. Nell'esempio seguente restituisce entità con una che proprietà cognome inizia con la lettera 'A':

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtro proprietà numerico

Per filtrare in un numero intero o un numero a virgola mobile, specificare il numero senza virgolette.

In questo esempio restituisce tutte le entità con una proprietà Age il cui valore è maggiore di 30:

    Age gt 30

In questo esempio restituisce tutte le entità con una proprietà ImportoDovuto il cui valore è minore o uguale a 100.25:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtro proprietà booleana

Per filtrare un valore Boolean, specificare **true** o **false** senza virgolette.

Nell'esempio seguente restituisce tutte le entità nel punto in cui la proprietà IsActive è impostata su **true**:

    IsActive eq true

È inoltre possibile scrivere questa espressione di filtro senza l'operatore logico. Nell'esempio seguente, il servizio di tabella restituirà anche tutte le entità nel punto in cui IsActive è **vera**:

    IsActive

Per restituire tutte le entità in IsActive è false, è possibile utilizzare non operatore:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Filtro proprietà DateTime

Per filtrare un valore DateTime, specificare la parola chiave **datetime** , seguita dalla costante di data/ora tra virgolette singole. La costante di data/ora deve essere in formato UTC combinato, come descritto nella [Formattazione dei valori di proprietà DateTime](http://go.microsoft.com/fwlink/p/?LinkId=400449).

Nell'esempio seguente restituisce entità quando la proprietà CustomerSince è uguale a 10 luglio 2008:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
