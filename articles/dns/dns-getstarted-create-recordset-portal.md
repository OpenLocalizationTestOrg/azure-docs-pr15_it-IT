<properties
   pageTitle="Creare un set di record e record per una zona DNS tramite il portale di Azure | Microsoft Azure"
   description="Come creare record host per DNS Azure e creare set di record e record tramite il portale di Azure"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>



# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Creare record e set di record DNS tramite il portale di Azure


> [AZURE.SELECTOR]
- [Portale di Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI Azure](dns-getstarted-create-recordset-cli.md)


In questo articolo illustra il processo di creazione di record e i set di record tramite il portale di Azure. Dopo aver creato la zona DNS, aggiungere i record DNS per il dominio. A tale scopo, è innanzitutto necessario conoscere i record DNS e set di record.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## <a name="create-a-record-set-and-record"></a>Creare un set di record e record

Nell'esempio seguente viene illustrato il processo di creazione di un set di record e record tramite il portale di Azure. Si userà il tipo di record DNS "A".

1. Accedere al portale.

2. Passare a e il **DNS zone** in cui si desidera creare un set di record.

3. Nella parte superiore della stessa e **zona DNS** , selezionare **Imposta Record** per aprire e il **set di record aggiungere** .

    ![Nuovo set di record](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. Su e **Add record impostare** il nome del set di record. È ad esempio, il nome del set di record "**www**".

    ![Aggiungi set di record](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Selezionare il tipo di record che si desidera creare. Ad esempio, selezionare **oggetto**.

6. Impostare il **valore TTL**. La durata predefinito nel portale è un'ora.

7. Aggiungere gli indirizzi IP, un indirizzo IP per riga. Quando si utilizza il nome suggerito set di record e il tipo di record descritto in precedenza, aggiungere **il record a per il set di record www** indirizzi IPv4.

8. Dopo aver aggiunto gli indirizzi IP, selezionare **OK** nella parte inferiore della stessa e. Verrà creato il set di record DNS.


## <a name="next-steps"></a>Passaggi successivi

Per gestire il set di record e record, vedere [Gestione DNS record e record imposta tramite il portale di Azure](dns-operations-recordsets-portal.md).

Per ulteriori informazioni su Azure DNS, vedere la [Panoramica del DNS Azure](dns-overview.md).
