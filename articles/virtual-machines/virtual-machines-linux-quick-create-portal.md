<properties
    pageTitle="Creare una VM Linux tramite il portale di Azure | Microsoft Azure"
    description="Creare una VM Linux tramite il portale di Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Creare una VM Linux su Azure tramite il portale


In questo articolo viene illustrato come utilizzare il [portale di Azure](https://portal.azure.com/) per creare una macchina virtuale Linux.

I requisiti sono:

- [un account Azure](https://azure.microsoft.com/pricing/free-trial/)

- [File chiave SSH pubblici e privati](virtual-machines-linux-mac-create-ssh-keys.md)


1. Effettuato l'accesso al portale di Azure con l'identità account Azure, fare clic su **+ Nuovo** nell'angolo superiore sinistro:

    ![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. Fare clic su **macchine virtuali** in **Marketplace** quindi **Ubuntu Server 14.04 risultati** da **App in primo piano** immagini elenco.  Verificare nella parte inferiore che il modello di distribuzione `Resource Manager` e quindi fare clic su **Crea**.

    ![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. Nella pagina **Introduzione** immettere:
    - un nome per la macchina virtuale
    - un nome utente per l'utente di amministratore
    - impostare il tipo di autenticazione a **chiave pubblica SSH**
    - la chiave pubblica SSH come stringa (dal `~/.ssh/` directory)
    - una risorsa nome del gruppo o selezionare un gruppo esistente

    Fare clic su **OK** per continuare e scegliere le dimensioni di macchine Virtuali; che dovrebbe essere simile al seguente:

    ![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

4. Scegliere le dimensioni **DS1** che si installa Ubuntu in un SSD Premium e fare clic su **Seleziona** per configurare le impostazioni.

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

5. In **Impostazioni**, lasciare le impostazioni predefinite per i valori di spazio di archiviazione e di rete e fare clic su **OK** per visualizzare il riepilogo.  Si noti il tipo di disco sono stata impostata su Premium SSD scegliendo DS1, **S** notates SSD.

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

6. Confermare le impostazioni per il nuovo VM Ubuntu e fare clic su **OK**.

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

7. Aprire il portale di Dashboard e **le interfacce di rete** scegliere la scheda di rete

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

8. Aprire il menu di indirizzi IP pubblico in base alle impostazioni di rete

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

9. SSH nell'indirizzo IP pubblico utilizzando la chiave pubblica SSH

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Passaggi successivi

A questo punto è stata creata una VM Linux rapidamente da utilizzare per il testing o dimostrazione. Per creare una VM Linux personalizzate per l'infrastruttura, è possibile eseguire una delle informazioni in questi articoli.

- [Creare una VM Linux su Azure uso dei modelli](virtual-machines-linux-cli-deploy-templates.md)
- [Creare un SSH protetto in modo esclusivo Linux macchine Virtuali in Azure uso dei modelli](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Creare una VM Linux usa CLI Azure](virtual-machines-linux-create-cli-complete.md)
