<properties
   pageTitle="Informazioni introduttive sulla creazione di un servizio di bilanciamento del carico interno in Gestione risorse tramite il portale di Azure | Microsoft Azure"
   description="Informazioni su come creare un sistema di bilanciamento del carico interno in Gestione risorse tramite il portale di Azure"
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
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-in-the-azure-portal"></a>Creare un sistema di bilanciamento del carico interno nel portale di Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modello di distribuzione classica](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="get-started-creating-an-internal-load-balancer-using-azure-portal"></a>Iniziare a creare un portale Azure di bilanciamento del carico interno

Utilizzare la procedura seguente per creare un sistema di bilanciamento del carico interno dal portale di Azure.

1. Aprire un browser, passare al [portale di Azure](http://portal.azure.com)e accedere con l'account Azure.
2. Nel lato angolo superiore sinistro dello schermo, fare clic su **Nuovo** > **rete** > **di bilanciamento del carico**.
3. In e il **bilanciamento del carico crea** , immettere un **nome** per il servizio di bilanciamento del carico.
4. In **combinazione**, fare clic su **interno**.
5. Fare clic su **rete virtuale**e quindi selezionare la rete virtuale in cui si desidera creare il servizio di bilanciamento del carico.

    >[AZURE.NOTE] Non viene visualizzata la rete virtuale che si desidera utilizzare, **percorso** utilizzando per il servizio di bilanciamento del carico e modificare di conseguenza.

6. Fare clic su **Subnet**e quindi selezionare subnet in cui si desidera creare il servizio di bilanciamento del carico.
7. In **assegnazione di indirizzi IP**, fare clic su **dinamico** o **statico**, a seconda che si desideri l'indirizzo IP di bilanciamento del carico da correggere (statico) o meno.

    >[AZURE.NOTE] Se si sceglie di utilizzare un indirizzo IP statico, sarà necessario specificare un indirizzo per il servizio di bilanciamento del carico.

8. In **gruppo di risorse** specificare il nome del nuovo gruppo di risorse per bilanciamento del carico o fare clic su **Seleziona esistente** e selezionare un gruppo di risorse esistenti.
9. Fare clic su **Crea**.

## <a name="configure-load-balancing-rules"></a>Configurare regole di bilanciamento del carico

Dopo la creazione di bilanciamento del carico, passare alla risorsa bilanciamento carico per la configurazione.
È necessario configurare prima di tutto un pool di indirizzi di back-end e una ricerca prima di configurare una regola di bilanciamento del carico.

### <a name="step-1-configure-a-back-end-pool"></a>Passaggio 1: Configurare un pool di back-end

1. Nel portale di Azure, fare clic su **Sfoglia** > **servizi di bilanciamento del carico**, quindi scegliere il bilanciamento del carico creato in precedenza.
2. In e **l'Impostazioni** , fare clic su **pool di back-end**.
3. In e il **pool di indirizzi back-end** , fare clic su **Aggiungi**.
4. In e il **componente back-end pool** , immettere un **nome** per il pool di back-end e quindi fare clic su **OK**.

### <a name="step-2-configure-a-probe"></a>Passaggio 2: Configurare un sondaggio

1. Nel portale di Azure, fare clic su **Sfoglia** > **servizi di bilanciamento del carico**, quindi scegliere il bilanciamento del carico creato in precedenza.
2. In e **l'Impostazioni** , fare clic su **Analizza**.
3. In e **l'esegue la ricerca** , fare clic su **Aggiungi**.
4. In e il **componente verifica** , immettere un **nome** per la ricerca.
5. In **protocollo**, selezionare **HTTP** (per i siti web) o **TCP** (per le altre applicazioni basate sul protocollo TCP).
6. In **porta**, specificare la porta da utilizzare quando si accede alla ricerca.
7. In **percorso** (per ricerche solo HTTP), specificare il percorso da utilizzare come verifica.
8. In **intervallo** specificare la frequenza con cui esaminare l'applicazione.
9. In **soglia non corretti**, specificare il numero di tentativi devono verificarsi prima la macchina virtuale back-end è contrassegnata come non corretti.
10. Fare clic su **OK** per creare sondaggio.

### <a name="step-3-configure-load-balancing-rules"></a>Passaggio 3: Configurare le regole di bilanciamento del carico

1. Nel portale di Azure, fare clic su **Sfoglia** > **servizi di bilanciamento del carico**, quindi scegliere il bilanciamento del carico creato in precedenza.
2. Selezionare **le regole di bilanciamento del carico**e **l'Impostazioni** .
3. In e il **bilanciamento del carico regole** , fare clic su **Aggiungi**.
4. In e il **componente caricare regola bilanciamento del carico** , immettere un **nome** per la regola.
5. In **protocollo**, selezionare **HTTP** (per i siti web) o **TCP** (per le altre applicazioni basate sul protocollo TCP).
6. In **porta**, specificare che i client di porta di connettersi al servizio di bilanciamento del carico.
7. Nella casella **porta back-end**, specificare la porta da utilizzare nel pool di back-end (in genere, la porta di bilanciamento del carico e la porta back-end sono gli stessi).
8. Nel **pool di back-end**, selezionare il pool di back-end creato in precedenza.
9. In **persistenza sessione**, selezionare la modalità sessioni in modo permanente.
10. Nella sezione **tempo di inattività (minuti)**, specificare il tempo di inattività.
11. In **IP mobile (diretto server restituito)**, fare clic su **attivato**o **disattivato** .
12. Fare clic su **OK**.

## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione carico di bilanciamento del carico](load-balancer-distribution-mode.md)

[Configurare le impostazioni di timeout di inattività TCP per il servizio di bilanciamento del carico](load-balancer-tcp-idle-timeout.md)