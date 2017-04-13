<properties 
   pageTitle="Gestire NSGs tramite il portale di anteprima in Gestione risorse | Microsoft Azure"
   description="Informazioni su come gestire esistente NSGs tramite il portale di anteprima in Gestione risorse"
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
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-the-preview-portal"></a>Gestire NSGs tramite il portale di anteprima

> [AZURE.SELECTOR]
- [Portale](virtual-network-manage-nsg-arm-portal.md)
- [PowerShell](virtual-network-manage-nsg-arm-ps.md)
- [CLI Azure](virtual-network-manage-nsg-arm-cli.md)

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Recuperare le informazioni

È possibile visualizzare il NSGs esistente, recuperare le regole per un NSG esistente e scoprire quali risorse è associato a un NSG.

### <a name="view-existing-nsgs"></a>Visualizzare NSGs esistente
Per visualizzare tutti i NSGs esistenti in una sottoscrizione, seguire la procedura seguente.

1. In un browser, passare a http://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Fare clic su **Sfoglia >** > **gruppi di sicurezza di rete**.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Controllare l'elenco di NSGs e il **gruppi di sicurezza di rete** .

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

Per visualizzare l'elenco dei NSGs nel gruppo di risorse **RG NSG** , seguire la procedura seguente. 

1. Fare clic su **gruppi di risorse >** > **RG NSG** > **…**.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Nell'elenco delle risorse, cercare gli elementi visualizzati l'icona NSG, come illustrato di seguito e il **risorse** .

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure4.png)
         
### <a name="list-all-rules-for-an-nsg"></a>Elenco di tutte le regole per un NSG

Per visualizzare le regole di un NSG denominato **NSG FrontEnd**, seguire la procedura seguente. 

1. Da e il **gruppi di sicurezza di rete** o e il **risorse** illustrato sopra, fare clic su **Front-end NSG**.
2. Nella scheda **Impostazioni** fare clic su **regole di protezione in ingresso**.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. E **l'in ingresso di regole di protezione** viene visualizzato come illustrato di seguito.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. Nella scheda **Impostazioni** fare clic su **regole di protezione in uscita** per visualizzare le regole in uscita.

>[AZURE.NOTE] Per visualizzare le regole predefinite, fare clic sull'icona di **regole predefinite** nella parte superiore della stessa e che vengono visualizzate le regole.

### <a name="view-nsgs-associations"></a>Visualizzare le associazioni NSGs

Per visualizzare ciò che risorse che NSG **Front-end NSG** è associato a, seguire la procedura seguente.

1. Da e il **gruppi di sicurezza di rete** o e il **risorse** illustrato sopra, fare clic su **Front-end NSG**.
2. Nella scheda **Impostazioni** fare clic su **subnet** per vedere quali subnet sono associate le NSG.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. Nella scheda **Impostazioni** fare clic su **interfacce di rete** per visualizzare le quali sono associate le NSG NIC.

## <a name="manage-rules"></a>Gestire le regole

È possibile aggiungere regole per un NSG esistente, modificare le regole esistenti e rimuovere le regole.

### <a name="add-a-rule"></a>Aggiungere una regola

Per aggiungere una regola che consenta il traffico **in ingresso** alla porta **443** da qualsiasi computer a **Front-end NSG** NSG, seguire la procedura seguente.

1. Da e il **gruppi di sicurezza di rete** o e il **risorse** illustrato sopra, fare clic su **Front-end NSG**.
2. Nella scheda **Impostazioni** fare clic su **regole di protezione in ingresso**.
3. In e il testo **in ingresso di regole di protezione** fare clic su **Aggiungi**. In e **l'Aggiungi regola di protezione in ingresso** , inserire i valori, come illustrato di seguito e quindi fare clic su **OK**.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

4. Dopo alcuni secondi, si noterà la nuova regola e il **regole di protezione in ingresso** .

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Modificare una regola

Per modificare la regola creata in precedenza per consentire il traffico in ingresso da **Internet** solo, seguire la procedura seguente.

1. Da e il **gruppi di sicurezza di rete** o e il **risorse** illustrato sopra, fare clic su **Front-end NSG**.
2. Nella scheda **Impostazioni** fare clic sulla regola creata in precedenza.
3. In e il **https Consenti** , modificare la proprietà **origine** , come illustrato di seguito e quindi fare clic su **Salva**.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Eliminare una regola

Per eliminare la regola creata in precedenza, seguire la procedura seguente.

1. Da e il **gruppi di sicurezza di rete** o e il **risorse** illustrato sopra, fare clic su **Front-end NSG**.
2. Nella scheda **Impostazioni** fare clic sulla regola creata in precedenza.
3. In e il **https Consenti** , fare clic su **Elimina**e quindi fare clic su **Sì**.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Gestire le associazioni

È possibile associare un NSG a subnet e schede di rete. È possibile annullare l'associazione anche tra un NSG da una risorsa a che è associata.

### <a name="associate-an-nsg-to-a-nic"></a>Associare un NSG a una scheda

Per associare **Front-end NSG** NSG a **TestNICWeb1** NIC, seguire la procedura seguente.

1. Da e il **gruppi di sicurezza di rete** o e il **risorse** illustrato sopra, fare clic su **Front-end NSG**.
2. Nella scheda **Impostazioni** fare clic su **interfacce di rete** > **associare** > **TestNICWeb1**.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Annullare l'associazione tra un NSG da una scheda di rete

Per annullare l'associazione tra **Front-end NSG** NSG da **TestNICWeb1** NIC, seguire la procedura seguente.

1. Dal portale di Azure, fare clic su **gruppi di risorse >** > **RG NSG** > **…**  >  **TestNICWeb1**.
2. In e il **TestNICWeb1** , fare clic su **Modifica protezione...**  > **None**.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

>[AZURE.NOTE] Utilizzare questo blade per associare la scheda NIC per qualsiasi NSG esistente.

### <a name="dissociate-an-nsg-from-a-subnet"></a>Annullare l'associazione tra un NSG da una subnet

Per annullare l'associazione tra **Front-end NSG** NSG dalla subnet **front-end** , seguire la procedura seguente.

1. Dal portale di Azure, fare clic su **gruppi di risorse >** > **RG NSG** > **…**  >  **TestVNet**.
2. In e **l'Impostazioni** , fare clic su **subnet** > **FrontEnd** > **gruppo di sicurezza di rete** > **Nessuno**.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. In e il **front-end** , fare clic su **Salva**.

![Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Associare un NSG a una subnet

Per associare nuovamente **Front-end NSG** NSG alla subnet **FronEnd** , seguire la procedura seguente.

1. Dal portale di Azure, fare clic su **gruppi di risorse >** > **RG NSG** > **…**  >  **TestVNet**.
2. In e **l'Impostazioni** , fare clic su **subnet** > **FrontEnd** > **gruppo di sicurezza di rete** > **NSG FrontEnd**.
3. In e il **front-end** , fare clic su **Salva**.

>[AZURE.NOTE] È inoltre possibile associare un NSG a una subnet da blade di **Impostazioni** del NSG thh.

## <a name="delete-an-nsg"></a>Eliminare un NSG

È possibile eliminare un NSG solo se non è associata a tutte le risorse. Per eliminare un NSG, seguire la procedura seguente.

1. Dal portale di Azure, fare clic su **gruppi di risorse >** > **RG NSG** > **…**  >  **NSG FrontEnd**.
2. Selezionare **le interfacce di rete**e **l'Impostazioni** .
3. Se sono presenti le schede nell'elenco, scegliere la scheda di rete e seguire il passaggio 2 in [Dissociate un NSG da una scheda di rete](#Dissociate-an-NSG-from-a-NIC).
4. Ripetere il passaggio 3 per ogni scheda di rete.
5. In e **l'Impostazioni** , fare clic su **subnet**.
6. Se sono presenti qualsiasi subnet elencate, fare clic su subnet e seguire i passaggi 2 e 3 [Dissociate un NSG da una subnet](#Dissociate-an-NSG-from-a-subnet).
7. Scorrere a sinistra per e il **Front-end NSG** , quindi fare clic su **Elimina** > **Sì**.

[Portale di Azure - NSGs](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Passaggi successivi

- [Attivare la registrazione](virtual-network-nsg-manage-log.md) per NSGs.
