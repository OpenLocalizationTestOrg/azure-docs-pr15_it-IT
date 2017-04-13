<properties
   pageTitle="Configurare il traffico Manager failover il traffico routing metodo | Microsoft Azure"
   description="In questo articolo consente di configurare failover metodo di routing il traffico in gestore del traffico"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-failover-routing-method"></a>Configurare Failover metodo di routing

Spesso un'organizzazione desidera fornire l'affidabilità per i servizi. A tale scopo, fornire servizi di backup nel caso in cui il servizio principale non è raggiungibile. Un modello comune per failover del servizio è fornire un insieme di servizi identici e inviare il traffico a un servizio principale, mantenendo un elenco configurato di uno o più servizi di backup. È possibile configurare questo tipo di backup con servizi cloud Azure e siti Web seguendo le procedure descritte di seguito.

Si noti che siti Web di Azure fornisce già il traffico failover routing funzionalità metodo per i siti Web all'interno di un Data Center (noto anche come area), indipendentemente dalla modalità del sito Web. Gestore del traffico consente di specificare failover il traffico routing metodo per i siti Web nei data center diversi.

## <a name="to-configure-failover-traffic-routing-method"></a>Per configurare failover il traffico routing metodo:

1. Nel portale di classica Azure, nel riquadro sinistro fare clic sull'icona di **Gestore del traffico** per aprire il riquadro di gestore del traffico. Se non è ancora stato creato il profilo il traffico Manager, vedere [Gestire i profili di gestore del traffico](traffic-manager-manage-profiles.md) per la procedura per creare un profilo di gestore del traffico base.
2. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico che contiene le impostazioni che si desidera modificare e quindi fare clic sulla freccia a destra del nome del profilo. Viene visualizzata la pagina di impostazioni per il profilo.
3. Nella pagina del profilo, fare clic su **endpoint** nella parte superiore della pagina e verificare che entrambi i servizi cloud e siti Web (endpoint) che si desidera includere nella configurazione sono presenti. Per la procedura aggiungere o rimuovere i punti finali, vedere [Gestire i punti finali di traffico Manager](traffic-manager-endpoints.md).
4. Nella pagina del profilo, fare clic su **Configura** nella parte superiore per aprire la pagina di configurazione.
5. Per **le impostazioni del metodo instradare il traffico**, verificare che il metodo di routing il traffico **Failover**. In caso contrario, fare clic su **Failover** nell'elenco a discesa.
6. Per **Nell'elenco priorità Failover**, modificare l'ordine di failover per gli endpoint. Quando si seleziona il metodo di routing il traffico **Failover** , è importante l'ordine di endpoint selezionato. L'endpoint principale è in primo piano. Utilizzare la freccia su e freccia giù per cambiare l'ordine in base alle esigenze. Per informazioni su come impostare la priorità di failover mediante Windows PowerShell, vedere [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Verificare che le **Impostazioni di monitoraggio** sono configurate in modo appropriato. Monitoraggio garantisce che gli endpoint attualmente offline non vengono inviati il traffico. Per controllare i punti finali, è necessario specificare un percorso e il nome. Si noti che una barra "/" è un valore valido per il relativo percorso e implica che il file sia nella directory principale (impostazione predefinita). Per ulteriori informazioni sul monitoraggio, vedere [Monitoraggio di gestore del traffico](traffic-manager-monitoring.md).
8. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
9. Verificare le modifiche nella configurazione. Per ulteriori informazioni, vedere [Impostazioni di gestione di traffico test](traffic-manager-testing-settings.md) .
10. Una volta il programma di installazione e utilizzo del profilo il traffico Manager, modificare il record DNS per il server DNS autorevole in modo che puntino il nome di dominio della società al nome di dominio gestore del traffico. Per ulteriori informazioni, vedere [punto di un dominio Internet della società a un dominio il traffico Manager](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Passaggi successivi

[Scegliere un dominio Internet della società a un dominio il traffico Manager](traffic-manager-point-internet-domain.md)

[Metodi di routing il traffico Manager](traffic-manager-routing-methods.md)

[Configurare metodo routing circolari](traffic-manager-configure-round-robin-routing-method.md)

[Configurare metodo di routing delle prestazioni](traffic-manager-configure-performance-routing-method.md)

[Risoluzione dei problemi gestore del traffico ridotto di stato](traffic-manager-troubleshooting-degraded.md)

[Abilita Manager - disattiva, il traffico o eliminare un profilo](disable-enable-or-delete-a-profile.md)

[Il traffico Manager - disabilitare o abilitare un estremo](disable-or-enable-an-endpoint.md)

