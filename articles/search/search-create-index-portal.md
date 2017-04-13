<properties
    pageTitle="Creare un indice di ricerca di Azure tramite il portale di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Creare un indice tramite il portale di Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Creare un indice di ricerca di Azure tramite il portale di Azure
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-an-index.md)
- [Portale](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTO](search-create-index-rest-api.md)

In questo articolo verrà descritto il processo di creazione di un [indice](search-what-is-an-index.md) di ricerca di Azure tramite il portale di Azure.

Prima di seguire questa Guida e creare un indice analitico, è necessario avere già [creato un servizio di ricerca di Azure](search-create-service-portal.md).


## <a name="i-go-to-your-azure-search-blade"></a>I. Passare a blade la ricerca di Azure
1. Fare clic su "Tutte le risorse" nel menu sul lato sinistro del [Portale di Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Selezionare il servizio di ricerca di Azure

## <a name="ii-add-and-name-your-index"></a>II. Aggiungere e assegnare un nome l'indice
1. Fare clic sul pulsante "Aggiungi indice"
2. Assegnare un nome indice di ricerca di Azure. Poiché viene creata un indice per cercare alberghi in questa Guida, abbiamo denominata nostro indice "alberghi".
  * Il nome dell'indice deve iniziare con una lettera e contengono solo lettere minuscole, cifre o trattini ("-").
  * Simile al nome del servizio, il nome dell'indice che è selezionare anche faranno parte dell'URL endpoint nel punto in cui si invierà le richieste per l'API di ricerca di Azure
3. Fare clic sulla voce "Campi" per aprire una nuova pala

![](./media/search-create-index-portal/add-index.png)


## <a name="iii-create-and-define-the-fields-of-your-index"></a>III. Creare e definire i campi dell'indice
1. Se si seleziona la voce "Campi", verrà aperta una nuova pala con un modulo per immettere la definizione di indice.
2. Aggiungere campi per l'indice utilizzando il modulo.

  * Un campo *chiave* di tipo Edm.String è obbligatorio per ogni indice di ricerca di Azure. In questo campo chiave viene creato con il campo nome "" id per impostazione predefinita. Sono stati modificati per "hotelId" nel nostro indice.
  * Alcune proprietà dello schema di indice è possibile impostare solo una volta e non possono essere aggiornati in futuro. Per questi motivi, tutti gli aggiornamenti dello schema che richiedono la reindicizzazione ad esempio la modifica di tipi di campo non sono attualmente possibili dopo la configurazione iniziale.
  * Avendo scelto con attenzione i valori di proprietà per ogni campo in base a come si ritiene che verranno utilizzati in un'applicazione. Quando si progetta l'indice di ogni campo deve essere assegnata la [proprietà appropriate](https://msdn.microsoft.com/library/azure/dn798941.aspx), tenere presente la ricerca utente esperienza e le esigenze aziendali. Queste proprietà controllo quale ricerca caratteristiche (filtro, faceting, ordinamento, ricerca full-text, e così via) applicare ai campi. Ad esempio, è probabile che gli utenti cercare alberghi saranno interessati corrispondenze di parole chiave per il campo "descrizione", in modo che si attiva ricerca full-text per il campo impostando la proprietà di "Ricercabile".
    * Impostare l' [analisi di lingua](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) per ogni campo facendo clic sulla scheda "Analyzer" nella parte superiore e il. È possibile vedere di seguito che è stato selezionato un analizzatore francese per un campo nel nostro indice destinato testo francese.

3. Fare clic su **OK** nella e "Campi" per confermare le definizioni di campo
4. Fare clic su **OK** nella e "Aggiungi indice" per salvare e creare l'indice che appena definito.

In schermate riportata di seguito, è possibile vedere come abbiamo denominate e i campi definiti per l'indice "alberghi".

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next"></a>Successivo
Dopo aver creato un indice di ricerca di Azure, sarà possibile caricare [il contenuto in corrispondenza dell'indice](search-what-is-data-import.md) in modo che è possibile avviare la ricerca dei dati.
