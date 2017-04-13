<properties
   pageTitle="Configurare le prestazioni del traffico routing metodo | Microsoft Azure"
   description="In questo articolo consente di configurare metodo instradare il traffico delle prestazioni in gestore del traffico"
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

# <a name="configure-performance-traffic-routing-method"></a>Configurare le prestazioni del traffico routing metodo

Per indirizzare il traffico per servizi cloud e siti Web (endpoint) che si trovano nei data center diversi in tutto il mondo (noto anche come aree geografiche), è possibile indirizzare il traffico in ingresso all'endpoint con latenza minima dal client richiedente. In genere, Data Center con latenza più bassa corrisponde al più vicino distanza geografica. Il metodo di routing il traffico prestazioni consentirà la distribuzione in base a più bassa latenza, ma non è possibile prendere in considerazione le variazioni in tempo reale in configurazione di rete o per caricarli. Per ulteriori informazioni sui metodi routing traffico diverso che fornisce Azure il traffico Manager, vedere [il traffico di gestione del traffico routing metodi](traffic-manager-routing-methods.md).

## <a name="route-traffic-based-on-lowest-latency-across-a-set-of-endpoints"></a>Instradare il traffico in base a latenza più bassa in un set di punti finali:

1. Nel portale di classica Azure, nel riquadro sinistro fare clic sull'icona di **Gestore del traffico** per aprire il riquadro di gestore del traffico. Se non è ancora stato creato il profilo il traffico Manager, vedere [Gestire i profili di gestore del traffico](traffic-manager-manage-profiles.md) per la procedura per creare un profilo di gestore del traffico base.
2. Nel portale di classica Azure, nel riquadro di traffico Manager, individuare il profilo di gestore del traffico che contiene le impostazioni che si desidera modificare e quindi fare clic sulla freccia a destra del nome del profilo. Viene visualizzata la pagina di impostazioni per il profilo.
3. Nella pagina per il proprio profilo, fare clic su **endpoint** nella parte superiore della pagina e verificare che siano presenti gli endpoint del servizio che si desidera includere nella configurazione. Per la procedura aggiungere o rimuovere i punti finali dal proprio profilo, vedere [Gestire i punti finali di traffico Manager](traffic-manager-endpoints.md).
4. Nella pagina per il proprio profilo, fare clic su **Configura** nella parte superiore per aprire la pagina di configurazione.
5. Per **le impostazioni del metodo instradare il traffico**, verificare che il metodo di routing il traffico * *prestazioni*. In caso contrario, fare clic su * *prestazioni** nell'elenco a discesa.
6. Verificare che le **Impostazioni di monitoraggio** sono configurate in modo appropriato. Monitoraggio garantisce che gli endpoint attualmente offline non vengono inviati il traffico. Per controllare i punti finali, è necessario specificare un percorso e il nome. Si noti che una barra "/" è un valore valido per il relativo percorso e implica che il file sia nella directory principale (impostazione predefinita). Per ulteriori informazioni sul monitoraggio, vedere [Su Monitoraggio gestore del traffico](traffic-manager-monitoring.md).
7. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
8. Verificare le modifiche nella configurazione. Per ulteriori informazioni, vedere [Impostazioni di gestione di traffico test](traffic-manager-testing-settings.md).
9. Una volta il programma di installazione e utilizzo del profilo il traffico Manager, modificare il record DNS per il server DNS autorevole in modo che puntino il nome di dominio della società al nome di dominio gestore del traffico. Per ulteriori informazioni, vedere [punto di un dominio Internet della società a un dominio il traffico Manager](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Passaggi successivi


[Scegliere un dominio Internet della società a un dominio il traffico Manager](traffic-manager-point-internet-domain.md)

[Metodi di routing il traffico Manager](traffic-manager-routing-methods.md)

[Configurare failover metodo di routing](traffic-manager-configure-failover-routing-method.md)

[Configurare metodo routing circolari](traffic-manager-configure-round-robin-routing-method.md)

[Risoluzione dei problemi gestore del traffico ridotto di stato](traffic-manager-troubleshooting-degraded.md)

[Abilita Manager - disattiva, il traffico o eliminare un profilo](disable-enable-or-delete-a-profile.md)

[Il traffico Manager - disabilitare o abilitare un estremo](disable-or-enable-an-endpoint.md)

