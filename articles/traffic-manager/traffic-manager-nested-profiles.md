<properties
    pageTitle="Annidate profili il traffico Manager | Microsoft Azure"
    description="Questo articolo illustra la funzionalità di 'Annidate profili' di Azure il traffico Manager"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="nested-traffic-manager-profiles"></a>Profili di gestore del traffico annidate

Gestore del traffico offre una gamma di metodi routing del traffico che consentono di controllare come gestore del traffico sceglie quale endpoint deve ricevere il traffico da ogni utente finale. Per ulteriori informazioni, vedere [gestione di traffico routing del traffico metodi](traffic-manager-routing-methods.md).

Ogni profilo di gestore del traffico consente di specificare un unico metodo di routing del traffico. Esistono tuttavia scenari che richiedono più sofisticati il traffico routing che il routing forniti da un singolo profilo di gestore del traffico. È possibile annidare i profili di gestore del traffico per combinare i vantaggi di più di un metodo di routing del traffico. Profili annidati consentono di ignorare il comportamento di gestore del traffico predefinito per supportare più grandi e installazioni applicazione più complesse.

Negli esempi seguenti viene illustrato come utilizzare profili di gestore del traffico annidate in diversi scenari.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Esempio 1: Combinazione "Prestazioni" e 'Weighted' routing del traffico

Si supponga distribuito un'applicazione nei seguenti paesi Azure: ovest degli Stati Uniti, Europa occidentale e Asia orientale. Utilizzare il traffico Manager 'Prestazioni' routing del traffico metodo per distribuire il traffico all'area vicina all'utente.

![Profilo singolo il traffico Manager][1]

Si supponga ora che si desidera verificare un aggiornamento al servizio prima di distribuirlo più ampiamente. Si desidera utilizzare il metodo di routing del traffico 'ponderato' per indirizzare una piccola percentuale di traffico per la distribuzione di prova. Impostare la distribuzione di test insieme alla distribuzione di produzione esistente in Europa occidentale.

Non è possibile combinare entrambi 'Weighted"e" prestazioni routing del traffico a un unico profilo. Per supportare questo scenario, si crea un profilo di gestore del traffico utilizzando i due punti finali Europa occidentale e il metodo di routing del traffico 'Weighted'. Aggiungere quindi il profilo 'figlio' come endpoint al profilo 'parent'. Il profilo padre ancora utilizzato il metodo di routing del traffico prestazioni e sono presenti altre distribuzioni globali come endpoint.

In questo esempio è illustrato nel diagramma seguente:

![Profili di gestore del traffico annidate][2]

In questa configurazione, il traffico diretto tramite il profilo padre distribuisce il traffico tra le aree normalmente. In Europa occidentale, il profilo annidato distribuisce il traffico per gli endpoint di produzione e test in base alle spessori assegnati.

Quando il profilo padre utilizza il metodo di routing del traffico 'Prestazioni', ogni endpoint deve essere assegnata una posizione. Quando si configura il punto finale, viene assegnato il percorso. Selezionare il paese Azure più vicino alla distribuzione. Le aree Azure sono i valori di posizione supportati dalla tabella latenza Internet. Per ulteriori informazioni, vedere [gestore del traffico 'Prestazioni' routing del traffico metodo](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Esempio 2: Monitoraggio Endpoint nei profili annidate

Gestore del traffico controlla attivamente lo stato di ogni endpoint del servizio. Se un endpoint è danneggiato, il traffico Manager indirizza agli utenti di endpoint alternativo per mantenere la disponibilità del servizio. Questo comportamento di failover e il monitoraggio dell'endpoint si applica a tutti i metodi di routing del traffico. Per ulteriori informazioni, vedere [Gestione di traffico Endpoint monitoraggio](traffic-manager-monitoring.md). Endpoint monitoraggio alcune differenze sostanziali per i profili annidati. Con i profili annidati, il profilo padre non esegue verifiche integrità sull'elemento figlio direttamente. Se, tuttavia, lo stato dell'endpoint del profilo figlio viene utilizzato per calcolare le condizioni generali del profilo figlio. Queste informazioni di stato viene propagate gerarchia profilo annidate. Il profilo padre questo aggregato integrità per determinare se per il traffico diretto al profilo figlio. Vedere la sezione [domande frequenti](#faq) di questo articolo per informazioni dettagliate sul monitoraggio dei profili annidati.

Tornare dell'esempio precedente, si supponga che la distribuzione di produzione in Europa occidentale ha esito negativo. Per impostazione predefinita, il profilo 'figlio' indirizza tutto il traffico alla distribuzione di prova. Se la distribuzione di test anche non riesce, il profilo padre determina che il profilo figlio non deve ricevere il traffico dal momento che tutti gli endpoint figlio siano danneggiati. Il profilo padre distribuisce il traffico alle altre aree.

![Annidata profilo failover (impostazione predefinita)][3]

Potrebbe essere soddisfatti questa disposizione. O potrebbe essere in questione che tutto il traffico per Europa occidentale sta per la distribuzione di test anziché il traffico di una versione ridotta. Indipendentemente dallo stato della distribuzione di prova, che si desidera esito negativo alle altre aree quando la distribuzione di produzione in Europa occidentale ha esito negativo. Per abilitare questa failover, è possibile specificare il parametro 'MinChildEndpoints' quando si configura il profilo figlio come endpoint nel profilo padre. Il parametro determina il numero minimo di endpoint disponibili nel profilo figlio. Il valore predefinito è "1". Per questo scenario, impostare il valore di MinChildEndpoints 2. Soglia, il profilo padre considera il profilo intero figlio da non essere disponibili e indirizza il traffico al altri endpoint.

La figura seguente illustra la configurazione:

![Annidate failover profilo con 'MinChildEndpoints' = 2][4]

>[AZURE.NOTE]
>Il metodo di routing del traffico 'Priority' distribuisce tutto il traffico a un unico endpoint. In questo modo è molto utile in un'impostazione di MinChildEndpoints diverso da "1" per un profilo figlio.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Esempio 3: Failover priorità aree nel routing del traffico 'Prestazioni'

Il comportamento predefinito per il metodo di routing del traffico 'Prestazioni' è progettato per evitare eccessivo caricamento successivo più vicino endpoint e causa una serie di errori a cascata. Quando un endpoint non riesce, tutto il traffico da indirizzati a tale endpoint viene distribuito uniformemente agli altri endpoint tra tutte le aree.

![Traffico 'Prestazioni' routing con failover predefinito][5]

Si supponga, tuttavia, si preferisce failover di traffico Europa occidentale su US ovest e solo indirizzare il traffico alle altre aree quando entrambi gli endpoint non sono disponibili. È possibile creare questa soluzione utilizzando un profilo figlio con il metodo di routing del traffico 'Priority'.

![Traffico 'Prestazioni' routing con failover preferenziale][6]

Poiché l'endpoint Europa occidentale ha priorità più alta endpoint ovest degli Stati Uniti, tutto il traffico viene inviato all'endpoint Europa occidentale quando entrambi gli endpoint siano in linea. Se Europe ovest non riesce, il traffico viene indirizzato alla US ovest. Con il profilo annidato, il traffico è indirizzato al Asia orientale solo quando Europa ovest e Ovest US hanno esito negativo.

È possibile ripetere questo modello per tutte le aree. Sostituire tutti gli tre endpoint nel profilo padre con tre profili figlio, ognuno dei quali fornisce una sequenza di failover priorità.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Esempio 4: Controllo 'Prestazioni' routing del traffico tra più endpoint nella stessa regione

Si supponga che le prestazioni routing del traffico metodo viene utilizzato in un profilo che contiene più di un endpoint in un'area specifica. Per impostazione predefinita, il traffico diretto a tale area venga distribuito equamente tra tutti gli endpoint disponibili in tale area.

![Traffico 'Prestazioni' routing distribuzione il traffico nell'area (impostazione predefinita)][7]

Invece di aggiungere più endpoint in Europa occidentale, gli endpoint sono racchiusi tra un profilo figlio separata. Il profilo figlio viene aggiunto all'elemento padre come endpoint solo in Europa occidentale. Le impostazioni del profilo figlio possono controllare la distribuzione di traffico con Europa occidentale abilitando il traffico basato sulle priorità o ponderato il routing all'interno di tale area.

![Traffico 'Prestazioni' routing alla distribuzione di traffico nell'area personalizzato][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Esempio 5: Impostazioni di monitoraggio Per endpoint

Si supponga che si utilizza il traffico Manager per eseguire la migrazione di consentire il traffico da un legacy locale sito web a una nuova versione basata su Cloud ospitato in Azure. Per il sito legacy, si desidera utilizzare la home page URI per monitorare l'integrità del sito. Ma per la nuova versione basata su Cloud, l'implementazione di una pagina di monitoraggio personalizzata (percorso ' / monitor.aspx') che include altri controlli.

![Gestore del traffico endpoint monitoraggio (impostazione predefinita)][9]

Le impostazioni di monitoraggio in un profilo di gestore del traffico si applicano a tutti gli endpoint all'interno di un singolo profilo. Con i profili annidati, si utilizza un profilo figlio diversi per ogni sito per definire le impostazioni di monitoraggio diversi.

![Gestore del traffico endpoint monitoraggio le impostazioni per ogni endpoint][10]

## <a name="faq"></a>Domande frequenti

### <a name="how-do-i-configure-nested-profiles"></a>Configurazione di profili annidati

Profili di gestore del traffico annidate possono essere configurati utilizzando la gestione di risorse di Azure e le API REST Azure classica i cmdlet di PowerShell Azure e i comandi CLI Azure multipiattaforma. Sono supportati anche tramite il portale di Azure nuovo. Non supportate nel portale di classica.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Il numero di livelli di annidamento non Manager il traffico supporta?

È possibile annidare profili fino a 10 livelli di annidamento. 'Cicli' non sono consentiti.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>È possibile combinare altri tipi di endpoint con i profili di figlio nidificate, nello stesso profilo di gestore del traffico?

Sì. Non esistono restrizioni su come combinare gli endpoint dei diversi tipi all'interno di un profilo.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Come applicato il modello fatturazione per i profili annidate

Non c'è non negativo prezzi impatto dell'utilizzo di profili annidati.

Gestore del traffico fatturazione include due componenti: verifiche integrità endpoint e milioni di query DNS

- Verifiche integrità endpoint: è gratuito per un profilo figlio quando configurato come un endpoint in un profilo padre. Monitoraggio dei punti finali nel profilo figlio vengono addebitate nel modo consueto.
- Query DNS: ogni query è conteggiate una sola volta. Una query su un profilo padre che restituisce un endpoint da un profilo figlio viene conteggiata in base al profilo padre solo.

Per informazioni dettagliate, vedere la [pagina prezzi gestore del traffico](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Esiste un impatto sulle prestazioni per i profili annidati?

No. Non esiste alcun impatto sulle prestazioni sostenuto quando si utilizza annidate.

I server dei nomi di gestore del traffico scorrere la gerarchia profilo internamente durante l'elaborazione di ogni query DNS. Una query DNS per un profilo padre può ricevere una risposta DNS con un endpoint da un profilo figlio. Se si utilizza un unico profilo o annidati profili, viene utilizzato un singolo record CNAME. Non è necessario per creare un record CNAME per ogni profilo nella gerarchia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Come gestore del traffico calcolare lo stato di un endpoint annidato in un profilo padre?

Il profilo padre non esegue verifiche integrità sull'elemento figlio direttamente. Se, tuttavia, lo stato dell'endpoint del profilo figlio vengono utilizzati per calcolare le condizioni generali del profilo figlio. Queste informazioni viene propagate gerarchia profilo annidata per determinare lo stato dell'endpoint annidate. Il profilo padre Usa questo stato aggregato per determinare se il traffico può essere indirizzato al figlio.

Nella tabella seguente viene descritto il comportamento di gestione del traffico integrità verifica la presenza di un endpoint annidato.

|Stato figlio Profile Monitor|Stato Endpoint Monitor principale|Note|
|---|---|---|
|Disattivato. Il profilo figlio è stato disattivato.|Arrestato|Lo stato dell'endpoint padre viene interrotto, non sia disattivato. Stato disabilitato riservato per indicare che è stata disabilitata l'endpoint nel profilo padre.|
|Ridotto. Almeno un elemento figlio profilo endpoint è in uno stato danneggiato.| Online: il numero di endpoint Online nel profilo figlio almeno è il valore di MinChildEndpoints.<BR>CheckingEndpoint: il numero di endpoint Online più CheckingEndpoint nel profilo figlio almeno è il valore di MinChildEndpoints.<BR>Ridotto: in caso contrario.|Il traffico viene inviato a un punto finale di stato CheckingEndpoint. Se MinChildEndpoints è troppo alto, l'endpoint è sempre ridotto.|
|In linea. Almeno un elemento figlio profilo endpoint è stato Online. Nessun endpoint è nello stato danneggiato.|Vedere sopra.||
|CheckingEndpoints. Almeno un elemento figlio profilo endpoint è 'CheckingEndpoint'. Nessun endpoint è 'In linea' o 'Ridotto'|Come sopra.||
|Inattivo. Tutti gli endpoint profilo figlio sono disattivato o arrestato questo profilo non è alcun endpoint.|Arrestato||


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [funzionamento del gestore del traffico](traffic-manager-how-traffic-manager-works.md)

Informazioni su come [creare un profilo di gestore del traffico](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png

