
<properties
   pageTitle="Iniziare a creare un Internet affiancate di bilanciamento del carico nel modello di distribuzione classica tramite il portale classico Azure | Microsoft Azure"
   description="Informazioni su come creare un servizio di bilanciamento del carico nel modello di distribuzione classica tramite il portale classico Azure è connessa a Internet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-portal"></a>Iniziare a creare un Internet affiancate di bilanciamento del carico (classico) nel portale di classica Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione classica. È inoltre possibile [imparare a creare un servizio di bilanciamento del carico Gestione risorse di Azure è connessa a Internet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="set-up-an-internet-facing-load-balancer-for-virtual-machines"></a>Configurare un bilanciamento del carico esposto a Internet per macchine virtuali

Per caricare il traffico di rete saldo da Internet in macchine virtuali di un servizio cloud, è necessario creare un set di bilanciamento del carico. Questa procedura si presuppone che le macchine virtuali è già stato creato e che siano all'interno del servizio cloud stesso.

**Per configurare un set di bilanciamento del carico per macchine virtuali**

1. Nel portale di classica Azure, fare clic su **macchine virtuali**e quindi fare clic sul nome di una macchina virtuale nel set di bilanciamento del carico.

2. Fare clic sui **punti finali**e quindi fare clic su **Aggiungi**.

3. Nella pagina **Aggiungi un endpoint a una macchina virtuale** fare clic sulla freccia a destra.

4. Nella pagina **specificare i dettagli del punto finale** :

    * In **nome**digitare un nome per l'endpoint o selezionare il nome dall'elenco di endpoint predefiniti per i protocolli comuni.
    * **Protocollo**, selezionare il protocollo necessario per il tipo di endpoint TCP o UDP, in base alle esigenze.
    * In **porta privato e pubblico**, digitare i numeri di porta che si desidera la macchina virtuale da utilizzare, in base alle esigenze. È possibile utilizzare la porta privata e le regole del firewall sul computer virtuale per reindirizzare il traffico in modo appropriato per l'applicazione. Porta privata può essere diverso da quello di porta pubblica. Ad esempio, per un endpoint per il traffico web (HTTP), si potrebbero assegnare porte 80 alla porta pubblica e privata.

5. Selezionare **Crea un set di bilanciamento del carico**e quindi fare clic sulla freccia a destra.

6. Nella pagina **Configura il set di bilanciamento del carico** , digitare un nome per il set di bilanciamento del carico e quindi assegnare i valori per il comportamento di verifica del Azure caricare. Il servizio di bilanciamento del carico utilizza le ricerche per determinare se le macchine virtuali nel set di bilanciamento del carico sono disponibili a ricevere il traffico in ingresso.

7. Fare clic sul segno di spunta per creare l'endpoint di bilanciamento del carico. Verrà visualizzato **Sì** nella colonna **Nome set di bilanciamento del carico** della pagina **endpoint** per la macchina virtuale.

8. Nel portale, fare clic su **macchine virtuali**, fare clic sul nome di una macchina virtuale aggiuntiva nel set di bilanciamento del carico e quindi fare clic su **Aggiungi** **i punti finali**.

9. Nella pagina **Aggiungi un endpoint a una macchina virtuale** fare clic su **Aggiungi endpoint a un insieme di bilanciamento del carico esistente**, selezionare il nome del set di bilanciamento del carico e quindi fare clic sulla freccia a destra.

10. Nella pagina **specificare i dettagli dell'endpoint** , digitare un nome per l'endpoint e quindi fare clic sul segno di spunta.

Per altre macchine virtuali nel set di bilanciamento del carico, ripetere i passaggi 8-10.



## <a name="next-steps"></a>Passaggi successivi

[Per iniziare la configurazione di un servizio di bilanciamento del carico interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurare una modalità di distribuzione carico di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)

