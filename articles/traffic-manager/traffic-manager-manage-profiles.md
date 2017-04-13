<properties
    pageTitle="Gestire i profili di Azure il traffico Manager | Microsoft Azure"
    description="In questo articolo consente di creare, disattivare, attivare, eliminare e visualizzare la cronologia di un profilo di Azure il traffico Manager."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="manage-an-azure-traffic-manager-profile"></a>Gestire un profilo di gestore del traffico Azure

I profili di gestore del traffico utilizzano routing del traffico metodi per controllare la distribuzione del traffico per i servizi cloud o endpoint sito Web. In questo articolo viene spiegato come creare e gestire i profili.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>Creare un profilo di gestore del traffico utilizzando Creazione rapida

È possibile creare rapidamente un profilo di gestore del traffico tramite Creazione rapida nel portale di classica Azure. Creazione rapida consente di creare profili con impostazioni di configurazione di base. Tuttavia, è possibile utilizzare Creazione rapida per accedere a impostazioni, ad esempio il set di punti finali (servizi cloud e siti Web), l'ordine di failover per il metodo di routing il traffico failover o monitoraggio delle impostazioni. Dopo aver creato il profilo personale, è possibile configurare queste impostazioni nel portale di classica Azure. Gestore del traffico supporta un massimo di 200 endpoint per ogni profilo. Tuttavia, la maggior parte dei scenari di utilizzo richiedono solo ad alcuni i punti finali.

### <a name="to-create-a-traffic-manager-profile"></a>Per creare un profilo di gestore del traffico

1. **Distribuire i servizi cloud e siti Web per l'ambiente di produzione.** Per ulteriori informazioni sui servizi cloud, vedere [Servizi Cloud](http://go.microsoft.com/fwlink/p/?LinkId=314074). Per ulteriori informazioni sui siti Web, vedere [siti Web](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Accedere al portale di classica Azure.** Fare clic su **Nuovo** in basso a sinistra del portale, fare clic su **servizi di rete > Gestione del traffico**e quindi fare clic su **Creazione rapida** per iniziare a configurare il profilo.
3. **Configurare il prefisso DNS.** Assegnare il profilo manager il traffico DNS univoco prefisso del nome. È possibile specificare solo il prefisso per un nome di dominio gestore del traffico.
4. **Selezionare l'abbonamento.** Selezionare l'abbonamento di Azure appropriato. Ogni profilo è associato a una singola sottoscrizione. Se si dispone di una sottoscrizione, questa opzione non viene visualizzato.
5. **Selezionare il metodo di routing il traffico.** Selezionare il metodo di routing il traffico in **traffico routing criteri**. Per ulteriori informazioni sui metodi instradare il traffico, vedere [metodi di routing il traffico di gestione del traffico](traffic-manager-routing-methods.md).
6. **Fare clic su "Crea" per creare il profilo**. Al termine della configurazione di profilo, è possibile individuare il profilo personale nel riquadro di gestore del traffico nel portale di classica Azure.
7. **Configurare le impostazioni aggiuntive, monitoraggio e i punti finali nel portale di classica Azure.** Utilizzando Create rapido solo Configura impostazioni di base. È necessario configurare ulteriori impostazioni, ad esempio l'elenco dei punti finali e l'ordine di failover endpoint.


## <a name="disable-enable-or-delete-a-profile"></a>Disabilitare, abilitare o eliminare un profilo

È possibile disattivare un profilo esistente, in modo tale Manager il traffico non fa riferimento richieste utente a endpoint configurati. Quando si disattiva un profilo di gestore del traffico, il profilo e le informazioni contenute nel profilo rimarrà invariate e possono essere modificati nell'interfaccia di gestione di traffico.  I riferimenti riprendere quando si riattiva il profilo. Quando si crea un profilo di gestore del traffico nel portale di classica Azure, viene attivata automaticamente. Se si decide di che un profilo non è più necessario, è possibile eliminarlo.

### <a name="to-disable-a-profile"></a>Per disattivare un profilo

1. Se si utilizza un nome di dominio personalizzato, modificare il record CNAME sul server DNS Internet, in modo che non è più punti al proprio profilo di gestore del traffico.
2. Il traffico interrompe vengano indirizzati a endpoint tramite le impostazioni del profilo di gestore del traffico.
3. Selezionare il profilo che si desidera disattivare. Nella pagina gestore del traffico, evidenziare il profilo facendo clic nella colonna accanto al nome del profilo. Nota, fare clic sul nome del profilo o sulla freccia accanto al nome verrà visualizzata la pagina di impostazioni per il profilo.
4. Dopo aver selezionato il profilo, fare clic su **Disattiva** nella parte inferiore della pagina.

### <a name="to-enable-a-profile"></a>Per abilitare un profilo

1. Selezionare il profilo che si desidera disattivare. Nella pagina gestore del traffico, evidenziare il profilo facendo clic nella colonna accanto al nome del profilo. Nota, fare clic sul nome del profilo o sulla freccia accanto al nome viene visualizzata la pagina di impostazioni per il profilo.
2. Dopo aver selezionato il profilo, fare clic su **Attiva** nella parte inferiore della pagina.
3. Se si utilizza un nome di dominio personalizzato, creare un record di risorse CNAME sul server DNS Internet per scegliere il nome di dominio del profilo gestore del traffico.
4. Il traffico è indirizzato ai punti finali nuovamente.

### <a name="to-delete-a-profile"></a>Per eliminare un profilo

1. Assicurarsi che il record di risorse DNS nel server DNS Internet non è più utilizzi un record di risorse CNAME che punti al nome di dominio del profilo gestore del traffico.
2. Selezionare il profilo che si desidera disattivare. Nella pagina gestore del traffico, evidenziare il profilo facendo clic nella colonna accanto al nome del profilo. Nota, fare clic sul nome del profilo o sulla freccia accanto al nome viene visualizzata la pagina di impostazioni per il profilo.
3. Dopo aver selezionato il profilo, fare clic su **Elimina** nella parte inferiore della pagina.

## <a name="view-traffic-manager-profile-change-history"></a>Cronologia delle modifiche di gestore del traffico Visualizza profilo

È possibile visualizzare la cronologia delle modifiche per il proprio profilo di gestore del traffico nel portale di classica Azure in servizi di gestione.

### <a name="to-view-your-traffic-manager-change-history"></a>Per visualizzare la cronologia delle modifiche di gestore del traffico

1. Nel riquadro a sinistra del portale di classica Azure, fare clic su **Servizi di gestione**.
2. Nella pagina Gestione servizi fare clic su **Registri operazioni**.
3. Nella pagina log operazione, è possibile filtrare per visualizzare la cronologia delle modifiche per il proprio profilo di gestore del traffico. Dopo aver selezionato le opzioni di filtro, fare clic sul segno di spunta per visualizzare i risultati.

   - Per visualizzare le modifiche per tutti i profili, selezionare l'abbonamento e l'intervallo di tempo e quindi selezionare **Il traffico gestione** dal menu di scelta rapida di **tipo** .
   - Per filtrare in base al nome del profilo, digitare il nome del profilo nel campo **Nome servizio** o selezionare dal menu di scelta rapida.
   - Per visualizzare i dettagli per ogni modifica singoli, selezionare la riga con le modifiche che si desidera visualizzare e quindi fare clic su **Dettagli** nella parte inferiore della pagina. Nella finestra di **Dettagli dell'operazione** , è possibile visualizzare la rappresentazione XML dell'oggetto API creato o aggiornato come parte dell'operazione.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiunta di un estremo](traffic-manager-endpoints.md)
- [Configurare failover metodo di routing](traffic-manager-configure-failover-routing-method.md)
- [Configurare metodo routing circolari](traffic-manager-configure-round-robin-routing-method.md)
- [Configurare metodo di routing delle prestazioni](traffic-manager-configure-performance-routing-method.md)
- [Scegliere un nome di dominio Manager il traffico di un dominio Internet della società](traffic-manager-point-internet-domain.md)
- [Risoluzione dei problemi gestore del traffico ridotto di stato](traffic-manager-troubleshooting-degraded.md)