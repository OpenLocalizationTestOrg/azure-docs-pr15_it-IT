<properties
    pageTitle="Gestire un account DocumentDB tramite il portale di Azure | Microsoft Azure"
    description="Informazioni su come gestire il proprio account DocumentDB tramite il portale di Azure. Trovare una Guida nel portale di Azure per visualizzare, copiare, eliminare e accedere agli account."
    keywords="Portale di Azure, documentdb, azure, Microsoft azure"
    services="documentdb"
    documentationCenter=""
    authors="kirillg"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="kirillg"/>

# <a name="how-to-manage-a-documentdb-account"></a>Come gestire un account DocumentDB

Informazioni su come impostare la coerenza globale, lavorare con i tasti ed eliminare un account DocumentDB nel portale di Azure.

## <a id="consistency"></a>Gestire le impostazioni di coerenza DocumentDB

Selezionare il livello di coerenza destra dipende la semantica dell'applicazione. È necessario acquisire familiarità con i livelli di coerenza disponibile in DocumentDB leggendo [livelli la coerenza tramite per ottimizzare la disponibilità e prestazioni ottimali in DocumentDB] [consistency]. DocumentDB offre la coerenza, disponibilità e prestazioni garanzie a ogni livello di coerenza disponibile per l'account di database. Configurare il proprio account di database con un livello di coerenza di sicuro richiede che i dati sono ristretto a una singola regione Azure e disponibile a livello globale. Invece, i livelli di coerenza Media - obsolescenza delimitata, session o Abilita finale è possibile associare qualsiasi numero di aree Azure con l'account di database. La semplice procedura seguente viene illustrato come selezionare il livello di coerenza predefinito per il proprio account di database. 

### <a name="to-specify-the-default-consistency-for-a-documentdb-account"></a>Per specificare la coerenza predefinito per un account DocumentDB

1. Nel [portale di Azure](https://portal.azure.com/), accedere all'account DocumentDB.
2. E il conto, fare clic su **predefinito coerenza**.
3. In e **l'Uniformità predefinita** , selezionare il nuovo livello di coerenza e fare clic su **Salva**.
    ![Sessione coerenza predefinita][5]

## <a id="keys"></a>Visualizzare, copiare e rigenera i tasti di scelta
Quando si crea un account DocumentDB, il servizio genera due master tasti che possono essere utilizzati per l'autenticazione quando si accede a account DocumentDB. Grazie a due tasti di scelta rapida, DocumentDB consente di rigenerare le chiavi senza interruzioni all'account DocumentDB. 

Nel [portale di Azure](https://portal.azure.com/), accedere e il **chiavi** dal menu della risorsa e il **DocumentDB account** per visualizzare, copiare e rigenera i tasti utilizzati per accedere all'account DocumentDB.

![Schermata di Azure portale, blade tasti](./media/documentdb-manage-account/keys.png)

> [AZURE.NOTE] E il **tasti** include anche le stringhe di connessione primario e secondario che possono essere utilizzate per connettersi all'account tramite lo [Strumento di migrazione di dati](documentdb-import-data.md).

Tasti di sola lettura sono inoltre disponibili in questo blade. Lettura e query operazioni di sola lettura, mentre crea, Elimina, e non si sostituisce.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Copiare un tasto di scelta nel portale di Azure

In e il **tasti** , fare clic sul pulsante **Copia** a destra della chiave che si desidera copiare.

![Visualizzazione e copia di un tasto di scelta nel portale di Azure, blade tasti](./media/documentdb-manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>Rigenerare i tasti di scelta

Al proprio account DocumentDB periodicamente allo scopo di mantenere le connessioni più sicura, è necessario modificare i tasti di scelta. Due tasti di scelta rapida assegnati agli consentono di gestire le connessioni all'account DocumentDB utilizzando un tasto di scelta rapida mentre si rigenera altri tasto di scelta rapida.

> [AZURE.WARNING] Rigenerare i tasti di scelta viene applicata a tutte le applicazioni che dipendono dai chiave corrente. Tutti i client che utilizzano il tasto di scelta per accedere all'account DocumentDB devono essere aggiornati per utilizzare la nuova chiave.

Se si dispone di applicazioni o servizi cloud usando l'account DocumentDB, si perderanno le connessioni se si rigenerare chiavi, a meno che non si ottengono le chiavi. La procedura seguente struttura il processo di ripristino dello stato delle chiavi.

1. Aggiornare il tasto di scelta nel codice dell'applicazione per fare riferimento il tasto di scelta secondario dell'account DocumentDB.
2. Rigenerare il tasto di scelta primario per il proprio account DocumentDB. Nel [Portale di Azure](https://portal.azure.com/), accedere all'account DocumentDB.
3. In e il **DocumentDB Account** , fare clic **sui tasti**.
4. Nella e **tasti** , fare clic sul pulsante rigenerato e quindi fare clic su **Ok** per confermare che si desidera generare una nuova chiave.
    ![Rigenerare i tasti di scelta](./media/documentdb-manage-account/regenerate-keys.png)

5. Dopo aver verificato che la nuova chiave sia disponibile per l'uso (circa 5 minuti dopo la rigenerazione), aggiornare il tasto di scelta nel codice dell'applicazione per fare riferimento alla nuova chiave primaria access.
6. Rigenerare il tasto di scelta secondario.

    ![Rigenerare i tasti di scelta](./media/documentdb-manage-account/regenerate-secondary-key.png)


> [AZURE.NOTE] È possibile richiedere alcuni minuti prima di una chiave generata può essere utilizzata per accedere all'account DocumentDB.

## <a name="get-the--connection-string"></a>Ottenere la stringa di connessione

Per recuperare la stringa di connessione, eseguire le operazioni seguenti: 

1. Nel [portale di Azure](https://portal.azure.com), accedere all'account DocumentDB.
2. Nel menu delle risorse, fare clic **sui tasti**.
3. Fare clic sul pulsante **Copia** accanto alla casella **Stringa di connessione principale** o **Secondario stringa di connessione** . 

Se si utilizza la stringa di connessione nello [Strumento di migrazione del Database DocumentDB](documentdb-import-data.md), aggiungere il nome del database alla fine della stringa di connessione. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a>Eliminare un account DocumentDB
Per rimuovere un account DocumentDB dal portale di Azure non è più in uso, scegliere il comando **Elimina Account** e il **DocumentDB account** .

![Come eliminare un account DocumentDB nel portale di Azure](./media/documentdb-manage-account/deleteaccount.png)


1. Nel [portale di Azure](https://portal.azure.com/), accedere all'account DocumentDB che si desidera eliminare.
2. Scegliere e il **DocumentDB account** , fare clic su **altro**e quindi fare clic su **Elimina conto**. Oppure, pulsante destro del mouse sul nome del database e fare clic su **Elimina conto**.
3. E il conferma risultante, digitare il nome dell'account DocumentDB per confermare che si desidera eliminare l'account.
4. Fare clic sul pulsante **Elimina** .

![Come eliminare un account DocumentDB nel portale di Azure](./media/documentdb-manage-account/delete-account-confirm.png)

## <a id="next"></a>Passaggi successivi

Informazioni su come [iniziare con l'account DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

Per ulteriori informazioni su DocumentDB, vedere la documentazione di Azure DocumentDB su [azure.com](http://go.microsoft.com/fwlink/?LinkID=402319&clcid=0x409).


<!--Image references-->
[1]: ./media/documentdb-manage-account/documentdb_add_region-1.png
[2]: ./media/documentdb-manage-account/documentdb_add_region-2.png
[3]: ./media/documentdb-manage-account/documentdb_change_write_region-1.png
[4]: ./media/documentdb-manage-account/documentdb_change_write_region-2.png
[5]: ./media/documentdb-manage-account/documentdb_change_consistency-1.png
[6]: ./media/documentdb-manage-account/chooseandsaveconsistency.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/
