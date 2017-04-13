<properties 
   pageTitle="Distribuire una macchina virtuale con un indirizzo IP pubblico statico tramite il portale di Azure in Gestione risorse | Microsoft Azure"
   description="Informazioni su come distribuire macchine virtuali con un indirizzo IP pubblico statico tramite il portale di zure in Gestione risorse"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-the-azure-portal"></a>Distribuire una macchina virtuale con un indirizzo IP pubblico statico tramite il portale di Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Creare una macchina virtuale con un indirizzo IP pubblico statico 

Per creare una macchina virtuale con un indirizzo IP pubblico statico nel portale di Azure, seguire la procedura seguente.

1. Da un browser passare al [portale di Azure](https://portal.azure.com) e, se necessario, accedere con l'account Azure.
2. Nell'angolo superiore sinistro del portale, fare clic su **Nuovo**>>**calcolare**>**Data Center di Windows Server 2012 R2**.
3. Nell'elenco **Selezionare un modello di distribuzione** selezionare **Manager delle risorse** e fare clic su **Crea**.
4. In e il **Nozioni di base** , immettere le informazioni di macchine Virtuali, come illustrato di seguito e quindi fare clic su **OK**.

    ![Portale di Azure - concetti di base](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)

5. In e lo **scegliere una dimensione** , fare clic su **A1 Standard** come illustrato di seguito e quindi fare clic su **Seleziona**.

    ![Portale di Azure - scegliere una dimensione](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)

6. In e **l'Impostazioni** , fare clic su **indirizzo IP pubblico**, quindi in e **l'indirizzo IP pubblico crea** in **assegnazione** **statica** come illustrato di seguito. E quindi fare clic su **OK**.

    ![Portale di Azure - creare indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)

7. In e **l'Impostazioni** , fare clic su **OK**.
8. Esaminare e il **Riepilogo** , come illustrato di seguito e quindi fare clic su **OK**.

    ![Portale di Azure - creare indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)

9. Si noti la nuova sezione nel dashboard.

    ![Portale di Azure - creare indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)

10. Dopo aver creata la macchina virtuale, e **l'Impostazioni** verrà visualizzato come illustrato di seguito

    ![Portale di Azure - creare indirizzo IP pubblico](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)