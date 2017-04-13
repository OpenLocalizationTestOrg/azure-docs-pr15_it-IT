<properties
   pageTitle="Disabilitare, abilitare o eliminare un profilo di gestore del traffico | Microsoft Azure"
   description="In questo articolo viene spiegato come lavorare con i profili di gestore del traffico."
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

# <a name="disable-enable-or-delete-a-profile"></a>Disabilitare, abilitare o eliminare un profilo


È possibile disattivare un profilo di gestore del traffico esistente, in modo che non riferirà richieste utente per l'endpoint configurati. Quando si disattiva un profilo di gestore del traffico, profilo stesso e le informazioni contenute nel profilo subiranno alcuna e può essere modificato nell'interfaccia di gestione di traffico. Quando si desidera abilitare nuovamente il profilo, è possibile eseguire facilmente in modo che nel Azure verranno ripresa portale e riferimenti. Quando si crea un profilo di gestore del traffico nel portale di Azure, viene attivata automaticamente.

## <a name="to-disable-a-profile"></a>Per disattivare un profilo

1. Modificare il record di risorse DNS sul server DNS Internet per utilizzare il tipo di record appropriato e puntatore a un altro nome o l'indirizzo IP di una posizione specifica su Internet. Modificare il record di risorse DNS nel server DNS Internet in altre parole, in modo che non utilizzi non è più un record di risorse CNAME che punti al nome di dominio del profilo gestore del traffico.
1. Il traffico verrà interrotto viene reindirizzato al endpoint tramite le impostazioni del profilo di gestore del traffico.
1. Selezionare il profilo che si desidera disattivare. Selezionare il profilo, nella pagina gestore del traffico, evidenziare il profilo facendo clic nella colonna accanto al nome del profilo. Non fare clic sul nome del profilo o sulla freccia accanto al nome, come verrà visualizzata la pagina di impostazioni per il profilo.
1. Dopo aver selezionato il profilo, fare clic su Disattiva nella parte inferiore della pagina.

## <a name="to-enable-a-profile"></a>Per abilitare un profilo

1. Selezionare il profilo che si desidera attivare. Selezionare il profilo, nella pagina gestore del traffico, evidenziare il profilo facendo clic nella colonna accanto al nome del profilo. Non fare clic sul nome del profilo o sulla freccia accanto al nome, come verrà visualizzata la pagina di impostazioni per il profilo.
1. Dopo aver selezionato il profilo, fare clic su attiva nella parte inferiore della pagina.
1. Modificare il record di risorse DNS sul server DNS Internet per utilizzare il tipo di record CNAME, che consente di associare il nome di dominio della società al nome di dominio del profilo gestore del traffico. Per ulteriori informazioni, vedere [punto di un dominio Internet della società a un dominio il traffico Manager](traffic-manager-point-internet-domain.md).
1. Verrà avviato il traffico vengano indirizzati nuovamente i punti finali.

## <a name="delete-a-profile"></a>Eliminare un profilo


1. Assicurarsi che il record di risorse DNS nel server DNS Internet non è più utilizzi un record di risorse CNAME che punti al nome di dominio del profilo gestore del traffico.
1. Selezionare il profilo che si desidera eliminare. Selezionare il profilo, nella pagina gestore del traffico, evidenziare il profilo come
1. facendo clic sulla colonna accanto al profilo. Non fare clic sul nome del profilo o sulla freccia accanto al nome, come verrà visualizzata la pagina di impostazioni per il profilo.
1. Dopo aver selezionato il profilo, fare clic su Elimina nella parte inferiore della pagina.

## <a name="next-steps"></a>Passaggi successivi

[Il traffico Manager - disabilitare o abilitare un estremo](disable-or-enable-an-endpoint.md)

[Configurare failover metodo di routing](traffic-manager-configure-failover-routing-method.md)

[Configurare metodo routing circolari](traffic-manager-configure-round-robin-routing-method.md)

[Configurare metodo di routing delle prestazioni](traffic-manager-configure-performance-routing-method.md)

[Risoluzione dei problemi gestore del traffico ridotto di stato](traffic-manager-troubleshooting-degraded.md)

