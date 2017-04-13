<properties
    pageTitle="Configurare l'integrazione di Azure archivio chiave per SQL Server in macchine virtuali Azure (Manager delle risorse)"
    description="Informazioni su come automatizzare la configurazione di crittografia di SQL Server per l'utilizzo con Azure chiave archivio. In questo argomento viene illustrato come utilizzare l'integrazione di archivio di Azure chiave con macchine virtuali di SQL Server create con Gestione risorse."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Configurare l'integrazione di Azure archivio chiave per SQL Server in macchine virtuali Azure (Manager delle risorse)

> [AZURE.SELECTOR]
- [Manager delle risorse](virtual-machines-windows-ps-sql-keyvault.md)
- [Classica](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Panoramica
Sono disponibili più funzioni di crittografia di SQL Server, ad esempio [la crittografia dei dati trasparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [crittografia a livello di colonna (Cancella)](https://msdn.microsoft.com/library/ms173744.aspx)e [crittografia di backup](https://msdn.microsoft.com/library/dn449489.aspx). Questi tipi di crittografia è necessario gestire e memorizzare la crittografia che utilizzare per la crittografia. Il servizio di Azure chiave archivio (AKV) è progettato per migliorare la sicurezza e la gestione di questi tasti in un percorso sicuro e disponibilità. Il [Connettore di SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) consente a SQL Server per utilizzare questi tasti dall'archivio chiave Azure.

Se si esegue SQL Server con locale macchine, sono disponibili [procedure per accedere a Azure chiave archivio dal computer locale di SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Ma per SQL Server in macchine virtuali di Azure, è possibile risparmiare tempo utilizzando la funzionalità di *Integrazione di archivio di Azure chiave* .

Quando è attivata, automaticamente installa il connettore di SQL Server, Configura provider EKM per accedere a Azure chiave archivio e crea le credenziali per consentire l'insieme di credenziali di accesso. Se attiva la procedura descritta nella documentazione locale descritto in precedenza, è possibile vedere questa caratteristica consente di automatizzare i passaggi 2 e 3. L'unica che è comunque necessario eseguire manualmente consiste nel creare l'archivio chiave e chiavi. A questo punto, l'intera impostazione le VM SQL è automatica. Quando questa funzionalità viene completato il programma di installazione, è possibile eseguire istruzioni Transact-SQL per iniziare la crittografia del database o backup come di consueto.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Attivazione e configurazione dell'integrazione AKV
È possibile attivare l'integrazione di AKV durante il provisioning o configurare per le macchine virtuali esistenti.

### <a name="new-vms"></a>Nuove macchine virtuali
Se si il provisioning di un nuovo computer virtuale SQL Server con Gestione risorse, il portale di Azure rappresenta un passaggio per attivare l'integrazione di Azure chiave archivio. La caratteristica di archivio di chiave Azure è disponibile solo per le aziende, sviluppo e valutazione edizioni di SQL Server.

![Integrazione di SQL Azure archivio chiave](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Per una descrizione dettagliata di provisioning, vedere [disposizione una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Macchine virtuali esistenti
Per macchine virtuali di SQL Server esistenti, selezionare la macchina virtuale di SQL Server. Quindi selezionare la sezione **configurazione SQL Server** della stessa e **Impostazioni** .

![Integrazione di AKV SQL per macchine virtuali esistenti](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Selezionare il pulsante **Modifica** nella sezione integrazione automatica archivio chiave e il **configurazione SQL Server** .

![Configurare l'integrazione di AKV SQL per macchine virtuali esistenti](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Al termine, fare clic sul pulsante **OK** nella parte inferiore della stessa e **configurazione di SQL Server** per salvare le modifiche.

>[AZURE.NOTE] È inoltre possibile configurare l'integrazione di AKV utilizzando un modello. Per ulteriori informazioni, vedere [Guida introduttiva di Azure modello per l'integrazione di Azure chiave archivio](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
