<properties
   pageTitle="Configurare il traffico gestione arrotondare Marco il traffico routing metodo | Microsoft Azure"
   description="In questo articolo consente di configurare circolari bilanciamento del carico per gli endpoint gestore del traffico."
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

# <a name="configure-round-robin-routing-method"></a>Metodo di routing Configura circolari

Un modello di metodo instradare il traffico comune consiste nel fornire un insieme di endpoint identici, che includono servizi cloud e siti Web, e inviare il traffico a ognuna in una circolare. La procedura seguente viene illustrato come configurare il traffico Manager per eseguire questo tipo di metodo instradare il traffico. Per ulteriori informazioni sui metodi instradare il traffico diverso, vedere [metodi di routing il traffico di gestione del traffico](traffic-manager-routing-methods.md).

>[AZURE.NOTE] Siti Web Azure fornisce già circolari bilanciamento del carico funzionalità per i siti Web all'interno di un Data Center (noto anche come area). Gestore del traffico consente di specificare il traffico circolari metodo routing per i siti Web nei data center diversi.

## <a name="routing-traffic-equally-round-robin-across-a-set-of-endpoints"></a>Un set di punti finali di routing il traffico identico (eseguire funzioni circolari):

1. Nel portale di classica Azure, nel riquadro sinistro fare clic sull'icona di **Gestore del traffico** per aprire il riquadro di gestore del traffico. Se non è ancora stato creato il profilo il traffico Manager, vedere [Gestire i profili di gestore del traffico](traffic-manager-manage-profiles.md) per la procedura per creare un profilo di gestore del traffico base.
2. Nel portale di classica Azure, nel riquadro di traffico Manager, individuare il profilo di gestore del traffico che contiene le impostazioni che si desidera modificare e quindi fare clic sulla freccia a destra del nome del profilo. Viene visualizzata la pagina di impostazioni per il profilo.
3. Nella pagina per il proprio profilo, fare clic su **endpoint** nella parte superiore della pagina e verificare che siano presenti gli endpoint del servizio che si desidera includere nella configurazione. Per la procedura aggiungere o rimuovere i punti finali, vedere [Gestire i punti finali di traffico Manager](traffic-manager-endpoints.md).
4. Nella pagina del profilo, fare clic su **Configura** nella parte superiore per aprire la pagina di configurazione.
5. Per **il traffico routing metodo impostazioni**, verificare che il metodo di routing il traffico **circolari**. In caso contrario, fare clic su **circolari** nell'elenco a discesa.
6. Verificare che le **Impostazioni di monitoraggio** sono configurate in modo appropriato. Monitoraggio garantisce che gli endpoint attualmente offline non vengono inviati il traffico. Per controllare i punti finali, è necessario specificare un percorso e il nome. Si noti che una barra "/" è un valore valido per il relativo percorso e implica che il file sia nella directory principale (impostazione predefinita). Per ulteriori informazioni sul monitoraggio, vedere [Su Monitoraggio gestore del traffico](traffic-manager-monitoring.md).
7. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
8. Verificare le modifiche nella configurazione. Per ulteriori informazioni, vedere [Impostazioni di gestione di traffico test](traffic-manager-testing-settings.md).
9. Una volta il programma di installazione e utilizzo del profilo il traffico Manager, modificare il record DNS per il server DNS autorevole in modo che puntino il nome di dominio della società al nome di dominio gestore del traffico. Per ulteriori informazioni, vedere [punto di un dominio Internet della società a un dominio il traffico Manager](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Passaggi successivi


[Scegliere un dominio Internet della società a un dominio il traffico Manager](traffic-manager-point-internet-domain.md)

[Metodi di routing il traffico Manager](traffic-manager-routing-methods.md)

[Configurare failover metodo di routing](traffic-manager-configure-failover-routing-method.md)

[Configurare metodo di routing delle prestazioni](traffic-manager-configure-performance-routing-method.md)

[Risoluzione dei problemi gestore del traffico ridotto di stato](traffic-manager-troubleshooting-degraded.md)

[Abilita Manager - disattiva, il traffico o eliminare un profilo](disable-enable-or-delete-a-profile.md)

[Il traffico Manager - disabilitare o abilitare un estremo](disable-or-enable-an-endpoint.md)

