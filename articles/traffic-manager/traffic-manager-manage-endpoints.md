<properties
    pageTitle="Gestire i punti finali di Azure il traffico Manager | Microsoft Azure"
    description="In questo articolo consente di aggiungere, rimuovere, abilitare e disabilitare i punti finali da Azure il traffico di gestione."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="add-disable-enable-or-delete-endpoints"></a>Aggiungere, disattivare, attivare o eliminare i punti finali

La caratteristica Web Apps nel servizio App Azure fornisce già failover e la funzionalità di routing il traffico circolari per i siti Web all'interno di un Data Center, indipendentemente dalla modalità del sito Web. Gestore del traffico Azure consente di specificare failover e il traffico circolari routing per i servizi di siti Web e cloud nei data center diverso. Il primo passaggio necessario fornire tale funzionalità consiste nell'aggiungere l'endpoint del servizio o un sito Web cloud per il traffico Manager.

>[AZURE.NOTE]  In questo articolo viene spiegato come utilizzare il portale classico. Il portale classico Azure supporta solo la creazione e assegnazione di servizi cloud e Web apps come i punti finali. Il nuovo [portale Azure](https://portal.azure.com) è l'interfaccia preferito.

È anche possibile disabilitare singoli endpoint che fanno parte di un profilo di gestore del traffico. La disattivazione di un estremo lascia come parte del profilo, ma il profilo che si comporta come se l'endpoint non è incluso al suo interno. Questa azione è utile per temporaneamente la rimozione di un estremo che si trova nella modalità di manutenzione o viene ridistribuito. Dopo il punto finale è in esecuzione nuovamente, può essere attivato.

>[AZURE.NOTE] La disattivazione di un estremo ha niente a che fare con lo stato di distribuzione di Azure. Un endpoint integro rimane su e grado di ricevere il traffico anche quando disabilitata in Gestione il traffico. Inoltre, la disattivazione di un estremo un profilo non ha alcun effetto il suo stato in un altro profilo.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Per aggiungere un endpoint del servizio o un sito Web cloud

1. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico che contiene le impostazioni di endpoint che si desidera modificare. Per aprire la pagina Impostazioni, fare clic sulla freccia a destra del nome del profilo.
2. Nella parte superiore della pagina, fare clic su **endpoint** per visualizzare gli endpoint che fanno già parte della configurazione.
3. Nella parte inferiore della pagina, fare clic su **Aggiungi** per accedere alla pagina di **Aggiungere gli endpoint del servizio** . Per impostazione predefinita, la pagina sono elencati i servizi cloud in **Endpoint del servizio**.
4. Per i servizi cloud, selezionare i servizi cloud nell'elenco per aggiungerli come endpoint per il profilo. Cancellare il nome del servizio cloud Rimuove dall'elenco dei punti finali.
5. Per i siti Web, fare clic sull'elenco a discesa **Tipo di servizio** e quindi selezionare **Web app**.
6. Selezionare i siti Web nell'elenco per aggiungerli come endpoint per il profilo. Cancellare il nome del sito Web Rimuove dall'elenco dei punti finali. È possibile selezionare solo un sito Web per Azure Data Center (noto anche come area). Quando si seleziona il sito Web prima, altri siti Web in Data Center del stesso diventano disponibili per la selezione. Si noti inoltre che sono elencati i siti Web solo Standard.
7. Dopo aver selezionato gli endpoint per il profilo, fare clic sul segno di spunta in basso a destra per salvare le modifiche.

>[AZURE.NOTE] Dopo l'aggiunta o rimozione di un estremo da un profilo utilizzando il metodo di routing il traffico *Failover* , nell'elenco priorità failover potrebbe non essere ordinato aspetto desiderato. È possibile modificare l'ordine dell'elenco di priorità Failover nella pagina configurazione. Per ulteriori informazioni, vedere [routing del traffico configurare Failover](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Per disattivare un estremo

1. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico contenente le impostazioni di endpoint che si desidera modificare. Per aprire la pagina Impostazioni, fare clic sulla freccia a destra del nome del profilo.
2. Nella parte superiore della pagina, fare clic su **endpoint** per visualizzare i punti finali inclusi nella configurazione.
3. Fare clic sull'endpoint che si desidera disattivare e quindi fare clic su **Disattiva** nella parte inferiore della pagina.
4. Client continueranno a inviare il traffico all'endpoint per la durata del tempo-to-Live (TTL). È possibile modificare il valore TTL nella pagina Configurazione del profilo di traffico Manager.

## <a name="to-enable-an-endpoint"></a>Per abilitare un estremo

1. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico che contiene le impostazioni di endpoint che si desidera modificare. Per aprire la pagina Impostazioni, fare clic sulla freccia a destra del nome del profilo.
2. Nella parte superiore della pagina, fare clic su **endpoint** per visualizzare i punti finali inclusi nella configurazione.
3. Fare clic sull'endpoint che si desidera abilitare e quindi fare clic su **Attiva** nella parte inferiore della pagina.
4. Client vengono richiesto all'endpoint abilitato verranno visualizzati in base al profilo.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Per eliminare un endpoint del servizio o un sito Web cloud

1. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico che contiene le impostazioni di endpoint che si desidera modificare. Per aprire la pagina Impostazioni, fare clic sulla freccia a destra del nome del profilo.
2. Nella parte superiore della pagina, fare clic su **endpoint** per visualizzare gli endpoint che fanno già parte della configurazione.
3. Nella pagina endpoint fare clic sul nome dell'endpoint che si desidera eliminare dal profilo.
4. Nella parte inferiore della pagina, fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i profili di gestore del traffico](traffic-manager-manage-profiles.md)
* [Configurare i metodi di routing](traffic-manager-configure-routing-method.md)
* [Risoluzione dei problemi gestore del traffico ridotto di stato](traffic-manager-troubleshooting-degraded.md)
* [Considerazioni sulle prestazioni di gestore del traffico](traffic-manager-performance-considerations.md)
* [Operazioni su Manager il traffico (riferimento all'API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)
