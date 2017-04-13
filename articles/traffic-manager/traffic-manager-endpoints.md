<properties
   pageTitle="Gestire i punti finali di Azure il traffico Manager | Microsoft Azure"
   description="In questo articolo consente di aggiungere, rimuovere, abilitare e disabilitare i punti finali da Azure il traffico di gestione."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="add-disable-enable-or-delete-endpoints"></a>Aggiungere, disattivare, attivare o eliminare i punti finali

La caratteristica Web Apps nel servizio App Azure fornisce già failover e la funzionalità di routing il traffico circolari per i siti Web all'interno di un Data Center, indipendentemente dalla modalità del sito Web. Gestore del traffico Azure consente di specificare failover e il traffico circolari routing per i servizi di siti Web e cloud nei data center diverso. Il primo passaggio necessario fornire tale funzionalità consiste nell'aggiungere l'endpoint del servizio o un sito Web cloud per il traffico Manager.

>[AZURE.NOTE] È possibile aggiungere percorsi esterni o profili Manager il traffico come endpoint tramite il portale classico Azure. È necessario utilizzare l'API REST [Crea definizione](http://go.microsoft.com/fwlink/p/?LinkId=400772) o di Windows PowerShell [AzureTrafficManagerEndpoint Aggiungi](http://go.microsoft.com/fwlink/p/?LinkId=400774).

È anche possibile disabilitare singoli endpoint che fanno parte di un profilo di gestore del traffico. I punti finali includono servizi cloud e siti Web. La disattivazione di un estremo lascia come parte del profilo, ma il profilo che si comporta come se l'endpoint non è incluso al suo interno. Questa azione è molto utile per temporaneamente la rimozione di un estremo che si trova nella modalità di manutenzione o viene ridistribuito. Dopo il punto finale è in esecuzione nuovamente, può essere attivato.

>[AZURE.NOTE] La disattivazione di un estremo ha niente a che fare con lo stato di distribuzione di Azure. Un endpoint integro rimarrà su e grado di ricevere il traffico anche quando disabilitata in Gestione il traffico. Inoltre, la disattivazione di un estremo un profilo non ha alcun effetto il suo stato in un altro profilo.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Per aggiungere un endpoint del servizio o un sito Web cloud


1. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico che contiene le impostazioni di endpoint che si desidera modificare e quindi fare clic sulla freccia a destra del nome del profilo. Viene visualizzata la pagina di impostazioni per il profilo.
2. Nella parte superiore della pagina, fare clic su **endpoint** per visualizzare gli endpoint che fanno già parte della configurazione.
3. Nella parte inferiore della pagina, fare clic su **Aggiungi** per accedere alla pagina di **Aggiungere gli endpoint del servizio** . Per impostazione predefinita, la pagina sono elencati i servizi cloud in **Endpoint del servizio**.
4. Per i servizi cloud, selezionare i servizi cloud nell'elenco per consentire loro come endpoint per il profilo. Cancellare il nome del servizio cloud Rimuove dall'elenco dei punti finali.
5. Per i siti Web, fare clic sull'elenco a discesa **Tipo di servizio** e quindi selezionare **Web app**.
6. Selezionare i siti Web nell'elenco per aggiungerli come endpoint per il profilo. Cancellare il nome del sito Web Rimuove dall'elenco dei punti finali. Si noti che è possibile selezionare solo un unico sito Web per Azure Data Center (noto anche come area). Se si seleziona un sito Web in un Data Center che ospita più siti Web, quando si seleziona il sito Web, gli altri utenti in Data Center del stesso diventano disponibili per la selezione. Si noti inoltre che sono elencati i siti Web solo Standard.
7. Dopo aver selezionato gli endpoint per il profilo, fare clic sul segno di spunta in basso a destra per salvare le modifiche.

>[AZURE.NOTE] Se si utilizza il metodo di routing il traffico *Failover* , dopo l'aggiunta o rimozione di un estremo, assicurarsi di modificare l'elenco di priorità di Failover nella pagina configurazione per riflettere l'ordine di failover desiderato per la configurazione. Per ulteriori informazioni, vedere [routing del traffico configurare Failover](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Per disattivare un estremo

1. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico che contiene le impostazioni di endpoint che si desidera modificare e quindi fare clic sulla freccia a destra del nome del profilo. Viene visualizzata la pagina di impostazioni per il profilo.
2. Nella parte superiore della pagina, fare clic su **endpoint** per visualizzare i punti finali inclusi nella configurazione.
3. Fare clic sull'endpoint che si desidera disattivare e quindi fare clic su **Disattiva** nella parte inferiore della pagina.
4. Il traffico verrà interrotto che scorre all'endpoint in base al DNS per durata (TTL) configurato per il nome di dominio gestore del traffico. Nella pagina di configurazione del profilo di traffico Manager, è possibile modificare il valore TTL.

## <a name="to-enable-an-endpoint"></a>Per abilitare un estremo

1. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico che contiene le impostazioni di endpoint che si desidera modificare e quindi fare clic sulla freccia a destra del nome del profilo. Viene visualizzata la pagina di impostazioni per il profilo.
2. Nella parte superiore della pagina, fare clic su **endpoint** per visualizzare i punti finali inclusi nella configurazione.
3. Fare clic sull'endpoint che si desidera abilitare e quindi fare clic su **Attiva** nella parte inferiore della pagina.
4. Il traffico verrà avviato il flusso al servizio nuovamente come dettato dal profilo.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Per eliminare un endpoint del servizio o un sito Web cloud


1. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico che contiene le impostazioni di endpoint che si desidera modificare e quindi fare clic sulla freccia a destra del nome del profilo. Viene visualizzata la pagina di impostazioni per il profilo.
2. Nella parte superiore della pagina, fare clic su **endpoint** per visualizzare gli endpoint che fanno già parte della configurazione.
3. Nella pagina endpoint fare clic sul nome dell'endpoint che si desidera eliminare dal profilo.
4. Nella parte inferiore della pagina, fare clic su **Elimina**.

>[AZURE.NOTE] È possibile eliminare il traffico Gestione profili o percorsi esterni come endpoint tramite il portale classico Azure. È necessario utilizzare Windows PowerShell. Per ulteriori informazioni, vedere [Rimuovi AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## <a name="next-steps"></a>Passaggi successivi

- [Configurare failover metodo di routing](traffic-manager-configure-failover-routing-method.md)
- [Configurare metodo routing circolari](traffic-manager-configure-round-robin-routing-method.md)
- [Configurare metodo di routing delle prestazioni](traffic-manager-configure-performance-routing-method.md)
- [Risoluzione dei problemi gestore del traffico ridotto di stato](traffic-manager-troubleshooting-degraded.md)
- [Operazioni su Manager il traffico (riferimento all'API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)
