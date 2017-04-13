<properties
   pageTitle="Disabilitare o abilitare un endpoint di gestore del traffico | Microsoft Azure"
   description="In questo articolo viene spiegato disabilitare o abilitare gli endpoint di profilo di gestore del traffico."
   services="traffic-manager"
   documentationCenter="na"
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

# <a name="disable-or-enable-a-traffic-manager-endpoint"></a>Disabilitare o abilitare un Endpoint di gestore del traffico

È anche possibile disabilitare singoli endpoint che fanno parte di un profilo di gestore del traffico. I punti finali includono servizi cloud e siti Web. La disattivazione di un estremo lascia come parte del profilo, ma il profilo che si comporta come se l'endpoint non è incluso al suo interno. Questa azione è molto utile per temporaneamente la rimozione di un estremo che si trova nella modalità di manutenzione o viene ridistribuito. Dopo il punto finale è in esecuzione nuovamente, può essere attivato

>[AZURE.NOTE] **La disattivazione di un estremo ha niente a che fare con lo stato di distribuzione di Azure. Un endpoint integro rimarrà su e grado di ricevere il traffico anche quando disabilitata in Gestione il traffico. Inoltre, la disattivazione di un estremo un profilo non ha alcun effetto il suo stato in un altro profilo.**

## <a name="to-disable-an-endpoint"></a>Per disattivare un estremo

1. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico che contiene le impostazioni di endpoint che si desidera modificare e quindi fare clic sulla freccia a destra del nome del profilo. Viene visualizzata la pagina di impostazioni per il profilo.
1. Nella parte superiore della pagina, fare clic su **endpoint** per visualizzare i punti finali inclusi nella configurazione.
1. Fare clic sull'endpoint che si desidera disattivare e quindi fare clic su **Disattiva** nella parte inferiore della pagina.
1. Il traffico verrà interrotto che scorre all'endpoint in base al DNS per durata (TTL) configurato per il nome di dominio gestore del traffico. Nella pagina di configurazione del profilo di traffico Manager, è possibile modificare il valore TTL.

## <a name="to-enable-an-endpoint"></a>Per abilitare un estremo


1. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico che contiene le impostazioni di endpoint che si desidera modificare e quindi fare clic sulla freccia a destra del nome del profilo. Viene visualizzata la pagina di impostazioni per il profilo.
1. Nella parte superiore della pagina, fare clic su **endpoint** per visualizzare i punti finali inclusi nella configurazione.
1. Fare clic sull'endpoint che si desidera abilitare e quindi fare clic su **Attiva** nella parte inferiore della pagina.
1. Il traffico verrà avviato il flusso al servizio nuovamente come dettato dal profilo.

## <a name="next-steps"></a>Passaggi successivi

[Abilita Manager - disattiva, il traffico o eliminare un profilo](disable-enable-or-delete-a-profile.md)

[Risoluzione dei problemi gestore del traffico ridotto di stato](traffic-manager-troubleshooting-degraded.md)

[Considerazioni sulle prestazioni di gestore del traffico](traffic-manager-performance-considerations.md)