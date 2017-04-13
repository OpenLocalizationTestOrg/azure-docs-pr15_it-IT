<properties 
   pageTitle="Come creare NSGs in modalità ARM tramite il portale di Azure | Microsoft Azure"
   description="Informazioni su come creare e distribuire NSGs in ARM tramite il portale di Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
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

# <a name="how-to-manage-nsgs-using-the-azure-portal"></a>Come gestire NSGs tramite il portale di Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione di Manager delle risorse. È anche possibile [creare NSGs nel modello di distribuzione classica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Nell'esempio PowerShell comandi seguenti richiedono un ambiente semplice già stato creato in base allo scenario precedente. Se si desidera eseguire i comandi così come sono visualizzati nel documento, prima di tutto creare l'ambiente di testing distribuendo [questo modello](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), fare clic su **Distribuisci in Azure**, sostituire i valori di parametro predefiniti, se necessario e seguire le istruzioni nel portale. La procedura seguente usa **RG NSG** come nome del gruppo di risorse che il modello è stato distribuito.

## <a name="create-the-nsg-frontend-nsg"></a>Creare NSG front-end NSG

Per creare **Front-end NSG** NSG come illustrato in uno scenario sopra, seguire la procedura seguente.

1. In un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **Sfoglia >** > **gruppi di sicurezza di rete**.

    ![Portale di Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)

3. In e il **gruppi di sicurezza di rete** , fare clic su **Aggiungi**.
  
    ![Portale di Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)

4. In e il **gruppo di sicurezza crea rete** , creare un NSG denominato *Front-end NSG* nel gruppo di risorse *RG NSG* e quindi fare clic su **Crea**.

    ![Portale di Azure - NSGs](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Creare regole in un NSG esistente

Per creare le regole in un NSG esistente dal portale di Azure, seguire la procedura seguente.

2. Fare clic su **Sfoglia >** > **gruppi di sicurezza di rete**.

3. Nell'elenco dei NSGs, fare clic su **Front-end NSG** > **regole di protezione in ingresso**

    ![Portale di Azure - front-end NSG](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)

4. Nell'elenco delle **regole di protezione in ingresso**, fare clic su **Aggiungi**.

    ![Portale di Azure - Aggiungi regola](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)

5. In e **l'Aggiungi regola di protezione in ingresso** , creare una regola denominata *regola web* con priorità *200* che consente l'accesso tramite *TCP* porte *80* e qualsiasi macchina virtuale da qualsiasi origine e quindi fare clic su **OK**. Si noti che la maggior parte di queste impostazioni siano già valori predefiniti.

    ![Portale di Azure - impostazioni delle regole](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)

6. Dopo alcuni secondi verrà visualizzata la nuova regola di NSG.

    ![Portale di Azure - nuova regola](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)

7. Ripetere i passaggi a 6 per creare una regola in entrata denominata *rdp regola* con una priorità più di *250* che consente l'accesso tramite *TCP* porta *3389* in qualsiasi macchine Virtuali da qualsiasi origine.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associare NSG alla subnet front-end

1. Fare clic su **Sfoglia >** > **gruppi di risorse** > **RG NSG**.
2. Selezionare **…** e il **RG NSG**  >  **TestVNet**.

    ![Portale di Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)

3. In e **l'Impostazioni** , fare clic su **subnet** > **FrontEnd** > **gruppo di sicurezza di rete** > **NSG FrontEnd**.

    ![Portale di Azure - impostazioni Subnet](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)

4. In e il **front-end** , fare clic su **Salva**.

    ![Portale di Azure - impostazioni Subnet](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Creare NSG back-end NSG

Per creare **Back-end NSG** NSG e associarla alla subnet **back-end** , seguire la procedura seguente.

1. Ripetere i passaggi descritti in [creare NSG front-end NSG](#Create-the-NSG-FrontEnd-NSG) per creare un NSG denominato *Back-end NSG*
2. Ripetere i passaggi in [creazione di regole in un NSG esistente](#Create-rules-in-an-existing-NSG) per creare le regole **in entrata** nella tabella seguente.

  	|Regola in entrata|Regola in uscita|
  	|---|---|
  	|![Portale di Azure - regola in entrata](./media/virtual-networks-create-nsg-arm-pportal/figure17.png)|![Portale di Azure - regola in uscita](./media/virtual-networks-create-nsg-arm-pportal/figure18.png)|

3. Ripetere i passaggi descritti in [associare NSG alla subnet front-end](#Associate-the-NSG-to-the-FrontEnd-subnet) da associare **Back-end NSG** NSG alla subnet **back-end** .

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire NSGs esistente](virtual-network-manage-nsg-arm-portal.md)
- [Attivare la registrazione](virtual-network-nsg-manage-log.md) per NSGs.