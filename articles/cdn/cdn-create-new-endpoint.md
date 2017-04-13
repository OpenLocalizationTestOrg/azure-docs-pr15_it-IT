<properties
     pageTitle="Uso CDN Azure | Microsoft Azure"
     description="In questo argomento viene illustrato come abilitare il contenuto rete (CDN) per Azure. L'esercitazione illustrata la creazione di un nuovo profilo di rete CDN di endpoint."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="07/28/2016" 
     ms.author="casoper"/>

# <a name="using-azure-cdn"></a>Uso CDN Azure  

In questo argomento vengono esaminati attivazione CDN Azure mediante la creazione di un nuovo profilo CDN ed endpoint.

>[AZURE.IMPORTANT] Per un'introduzione alle funzionamento di rete CDN, e un elenco delle funzionalità, vedere la [Panoramica CDN](./cdn-overview.md).

## <a name="create-a-new-cdn-profile"></a>Creare un nuovo profilo di rete CDN

Un profilo CDN è un insieme di CDN.  Ogni profilo contiene uno o più endpoint CDN.  Si consiglia di utilizzare più profili per organizzare gli endpoint CDN dominio internet, applicazione web o ad altri criteri.

> [AZURE.NOTE] Per impostazione predefinita, una singola sottoscrizione Azure è limitata ai profili CDN otto. Ogni profilo CDN è limitata ai punti finali dieci CDN.
>
> Rete CDN prezzi viene applicato a livello di profilo CDN. Se si desidera utilizzare una combinazione dei livelli prezzi CDN Azure, sarà necessario più profili CDN.

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Creare un nuovo endpoint CDN

**Per creare un nuovo endpoint CDN**

1. Nel [Portale di Azure](https://portal.azure.com), passare al proprio profilo CDN.  Si può hanno bloccata dashboard nel passaggio precedente.  Se non si sarà facendo clic su **Sfoglia**, quindi **profili CDN**e facendo clic sul profilo di cui si intende aggiungere l'endpoint.

    Verrà visualizzata e il profilo CDN.

    ![Profilo CDN][cdn-profile-settings]

2. Fare clic sul pulsante **Aggiungi Endpoint** .

    ![Endpoint pulsante Aggiungi][cdn-new-endpoint-button]

    Verrà visualizzata e il **componente un endpoint** .

    ![Aggiungere blade endpoint][cdn-add-endpoint]

3. Immettere un **nome** per l'endpoint CDN.  Questo nome da utilizzare per accedere alle risorse di memorizzati nella cache del dominio `<endpointname>.azureedge.net`.

4. Nell'elenco a discesa **tipo di origine** , selezionare il tipo di origine.  Selezionare **lo spazio di archiviazione** per un account di archiviazione Azure, **servizio Cloud** per un servizio Cloud di Azure, **Web App** per un'App Web di Azure o **origine personalizzata** per qualsiasi altra origine di server web accessibile al pubblico (ospitata in Azure o in un' posizione).

    ![Tipo di origine CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
        
5. Nell'elenco a discesa **origine hostname** , selezionare o digitare il dominio di origine.  L'elenco a discesa verrà elencati tutte le origini diverse disponibili del tipo specificato nel passaggio 4.  Se si seleziona *origine personalizzata* come il **tipo di origine**, verrà digitato nel dominio dell'origine personalizzata.

6. Nella casella di testo **percorso di origine** , immettere il percorso per le risorse memorizzare nella cache o lasciare vuoto il campo a Consenti memorizzazione nella cache tutte le risorse del dominio specificato nel passaggio 5.

7. Nell' **intestazione di host di origine**, immettere l'intestazione host desiderata CDN per inviare a ogni richiesta o lasciare il valore predefinito.

    > [AZURE.WARNING] Alcuni tipi di origini diverse, ad esempio lo spazio di archiviazione di Azure e Web Apps, richiedono l'intestazione host in modo che corrispondano il dominio dell'origine. A meno che non si dispone di un'origine che richiede un'intestazione host diversa dal proprio dominio, è consigliabile lasciare il valore predefinito.

8. Per **protocollo** e **porta di origine**, specificare i protocolli e porte utilizzate per l'accesso all'origine.  Almeno un protocollo HTTP (HTTPS) deve essere selezionato.
    
    > [AZURE.NOTE] **Porta di origine** viene applicata solo a cosa porta ottimali endpoint per recuperare le informazioni dell'origine.  Endpoint stesso saranno disponibili solo per i clienti di fine in predefinito HTTP porte e HTTPS 80 e 443, indipendentemente dalla **porta di origine**.  
    >
    > I punti finali **CDN Azure da Akamai** non consentono l'intervallo di porte TCP completo di origini diverse.  Per un elenco delle porte di origine che non sono consentiti, vedere [CDN Azure da Akamai consentiti origine porte](https://msdn.microsoft.com/library/mt757337.aspx).  
    >
    > Accesso CDN contenuto utilizzando HTTPS è i seguenti vincoli:
    > 
    > - È necessario utilizzare il certificato SSL fornito tramite la rete CDN. I certificati di terze parti non sono supportati.
    > - È necessario usare il dominio forniti CDN (`<endpointname>.azureedge.net`) per accedere a contenuti HTTPS. Supporto HTTPS non è disponibile per i nomi di dominio personalizzato (CNAME) poiché la rete CDN non supporta i certificati personalizzati al momento.

9. Fare clic sul pulsante **Aggiungi** per creare il nuovo endpoint.

10. Dopo aver creato il punto finale, viene visualizzato in un elenco di endpoint per il profilo. La visualizzazione elenco Mostra l'URL da usare per accedere al contenuto della cache, come il dominio di origine.

    ![Endpoint CDN][cdn-endpoint-success]

    > [AZURE.IMPORTANT] L'endpoint non immediatamente saranno disponibile per l'utilizzo, come il tempo per la registrazione per la propagazione attraverso la rete CDN.  Per i profili di <b>Azure CDN da Akamai</b> , propagazione completeranno in genere all'interno di un minuto.  Per i profili di <b>Azure CDN da Verizon</b> , propagazione in genere completeranno all'interno di 90 minuti, ma in alcuni casi può impiegare più tempo.
    >    
    > Gli utenti che tentano di utilizzare il nome di dominio CDN prima che la configurazione dell'endpoint è propagato ai POP riceveranno codici di risposta HTTP 404.  Se è stato alcune ore dal momento che è stato creato l'endpoint e si ricevono ancora 404 risposte, vedere [risoluzione dei problemi di rete CDN endpoint restituzione 404 stati](cdn-troubleshoot-endpoint.md).


##<a name="see-also"></a>Vedere anche
- [Controllare il comportamento della cache di richieste con stringhe di query](cdn-query-string.md)
- [Come eseguire il mapping CDN contenuto a un dominio personalizzato](cdn-map-content-to-custom-domain.md)
- [Pre-caricare risorse per un endpoint di rete CDN di Azure](cdn-preload-endpoint.md)
- [Eliminare un Endpoint CDN Azure](cdn-purge-endpoint.md)
- [Risoluzione dei problemi endpoint CDN restituzione 404 stati](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
