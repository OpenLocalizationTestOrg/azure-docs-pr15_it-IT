<properties 
    pageTitle="I criteri di gestione delle API Azure | Microsoft Azure" 
    description="Informazioni su come creare, modificare e configurare i criteri di gestione delle API." 
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


#<a name="policies-in-azure-api-management"></a>Criteri di gestione delle API Azure

In Gestione API Azure criteri sono una funzionalità potente del sistema che consentono l'autore che si desidera modificare il comportamento dell'API tramite la configurazione. I criteri sono un insieme di istruzioni che vengono eseguite in sequenza nella richiesta o risposta di un'API. Istruzioni popolari includono conversione del formato XML in JSON e tasso la limitazione per limitare la quantità di chiamate in arrivo da uno sviluppatore di chiamata. Molti altri criteri sono disponibili all'esterno della casella.

Vedere [Guida di riferimento dei criteri][] per un elenco completo delle istruzioni di criteri e le relative impostazioni.

I criteri vengono applicati all'interno del gateway che si trova tra consumer API e l'API gestita. Il gateway riceve tutte le richieste e in genere inoltra senza modifiche all'API sottostante. È tuttavia possibile applicare un criterio modifiche per la richiesta in entrata e di risposta in uscita.

Espressioni di criteri possono essere utilizzate come valori degli attributi o valori di testo in uno dei criteri di gestione delle API, a meno che il criterio specifica in caso contrario. Alcuni criteri, ad esempio i criteri di [flusso di controllo][] e [impostare variabile][] seguono si basano sulle espressioni di criteri. Per ulteriori informazioni, vedere [Avanzate criteri][] e [le espressioni di criteri][].

## <a name="scopes"> </a>Come configurare i criteri
Possono essere configurati globalmente o nell'ambito di un [prodotto][], [API][] o [un'operazione][]. Per configurare un criterio, passare all'editor di criteri nel portale di publisher.

![Menu di criteri][policies-menu]

Editor criteri è costituito da tre sezioni principali: l'ambito di criteri (in alto), la definizione di criteri in cui i criteri vengono modificati (a sinistra) e le istruzioni nell'elenco (a destra):

![Editor Criteri][policies-editor]

Per iniziare la configurazione di un criterio selezionare l'ambito applicano i criteri. Nella schermata seguente **Starter** prodotto selezionato. Si noti che il simbolo piedi accanto al nome del criterio indica che questo livello è già applicato un criterio.

![Ambito][policies-scope]

Dal momento che è già stato applicato un criterio, la configurazione è illustrata nella visualizzazione scheda definizione.

![Configurare][policies-configure]

Il criterio viene visualizzato sola lettura inizialmente. Per modificare la definizione fare clic sull'azione **Configurare i criteri** .

![Modifica][policies-edit]

La definizione di criteri è un semplice documento XML che descrive una sequenza di istruzioni in ingresso e in uscita. Il codice XML può essere modificato direttamente nella finestra definizione. Viene fornito un elenco di istruzioni a destra e istruzioni applicabili all'ambito corrente sono abilitate ed evidenziate. come illustrato dall'istruzione **Limite chiamare tasso** nella schermata precedente.

Fare clic su un'istruzione abilitata aggiungerà il codice XML in corrispondenza della posizione del cursore nella visualizzazione scheda definizione. 

>[AZURE.NOTE] Se il criterio che si desidera aggiungere non è attivato, assicurarsi che ci si trova nell'ambito corretto per tale criterio. Ogni informativa deve essere utilizzato in determinati ambiti e sezioni dei criteri. Per esaminare le sezioni dei criteri e gli ambiti per un criterio, controllare la sezione **utilizzo** dei criteri correlate nel [Riferimento di criteri][].

Un elenco completo delle istruzioni di criteri e le relative impostazioni sono disponibili nella [Guida di riferimento dei criteri][].

Ad esempio, per aggiungere una nuova istruzione per limitare le richieste in arrivo a indirizzi IP specificati, posizionare il cursore all'interno il contenuto della `inbound` elemento XML e fare clic su istruzione **chiamante limita IP** .

![Criteri di restrizione][policies-restrict]

Verrà aggiunto un frammento di codice XML per il `inbound` elemento che fornisce indicazioni su come configurare l'istruzione.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

Per limitare le richieste in ingresso e accettare che solo quelli da un indirizzo IP di 1.2.3.4 modificare il codice XML come indicato di seguito:

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Salva][policies-save]

Quando completa configurazione le istruzioni per il criterio, fare clic su **Salva** e le modifiche verranno propagate a gateway di gestione dell'API immediatamente.

##<a name="sections"> </a>Configurazione dei criteri di informazioni

Un criterio è una serie di istruzioni che vengono eseguite in ordine per una richiesta e risposta. La configurazione è diviso in modo appropriato in `inbound`, `backend`, `outbound`, e `on-error` sections come illustrato nella configurazione seguente.

    <policies>
      <inbound>
        <!-- statements to be applied to the request go here -->
      </inbound>
      <backend>
        <!-- statements to be applied before the request is forwarded to 
             the backend service go here -->
      </backend>
      <outbound>
        <!-- statements to be applied to the response go here -->
      </outbound>
      <on-error>
        <!-- statements to be applied if there is an error condition go here -->
      </on-error>
    </policies> 

Se si verifica un errore durante l'elaborazione di una richiesta, qualsiasi rimanente passaggi da eseguire nel `inbound`, `backend`, o `outbound` vengono ignorate le sezioni e l'esecuzione prosegue con le istruzioni nel `on-error` sezione. Inserendo istruzioni di criteri nel `on-error` sezione è possibile esaminare l'errore usando il `context.LastError` proprietà, esaminare e personalizzare la risposta di errore utilizzando il `set-body` criterio e configurare cosa succede se si verifica un errore. Sono disponibili i codici di errore per la procedura incorporato e gli errori che possono verificarsi durante l'elaborazione di istruzioni di criteri. Per ulteriori informazioni, vedere [gestione degli errori in Criteri di gestione delle API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

Dal momento che è possibile specificare criteri diversi livelli (globale, prodotto, api e operazione) la configurazione consente di specificare l'ordine in cui eseguire le istruzioni della definizione dei criteri per quanto riguarda il criterio padre. 

Ambiti di criteri vengono valutati nell'ordine seguente.

1. Ambito globale
2. Obiettivi globali del prodotto
3. Ambito API
4. Ambito dell'operazione

Le istruzioni al loro interno vengono valutate in base alla posizione del `base` elemento, se presenta.

Ad esempio, se si dispone di un criterio a livello globale e configurato un criterio per un'API, quindi ogni volta che viene utilizzata l'API specifica entrambi i criteri verranno applicati. Gestione dell'API consente per l'ordinamento deterministica delle istruzioni di criteri combinati mediante l'elemento di base. 

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

Nella definizione di criteri di esempio precedente, il `cross-domain` istruzione da eseguire prima tutti i criteri superiori che avviene in attiva, verranno seguiti dal `find-and-replace` criteri.

Se lo stesso criterio compare due volte nell'istruzione di criteri, i criteri di valutati più di recente. È possibile utilizzare questa sovrascrivere criteri definiti in un ambito superiore. Per visualizzare i criteri nell'ambito corrente nell'editor criteri, fare clic su **Ricalcola criterio effettivo per ambito selezionato**.

Si noti che criterio globale non ha alcun criterio padre e l'utilizzo di `<base>` elemento in essa non ha alcun effetto. 

## <a name="next-steps"></a>Passaggi successivi

Controllare i seguenti video su espressioni di criteri.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Guida di riferimento dei criteri]: api-management-policy-reference.md
[Prodotto]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Operazione]: api-management-howto-add-operations.md

[Criteri avanzati]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flusso di controllo]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Imposta variabile]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Espressioni di criteri]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png
