<properties
    pageTitle="Eseguire la migrazione di risorse IaaS da classico per gestione di risorse di Azure mediante CLI Azure | Microsoft Azure"
    description="Questo articolo sono illustrati migrazione piattaforma supportata di risorse da classico per gestione di risorse di Azure tramite CLI Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Eseguire la migrazione di risorse IaaS da classico per gestione di risorse di Azure mediante CLI Azure

Questa procedura viene illustrato come utilizzare i comandi di Azure interfaccia riga di comando (CLI) per eseguire la migrazione dell'infrastruttura come una risorsa di servizio (IaaS) dal modello di distribuzione classico per il modello di distribuzione di Manager delle risorse di Azure. L'articolo richiede [CLI Azure](../xplat-cli-install.md).

>[AZURE.NOTE] Tutte le operazioni descritte di seguito sono idempotenti. Se si dispone di un problema diverso da una caratteristica non supportata o un errore di configurazione, è consigliabile riprovare Prepara, interrompere o eseguire il commit operazione. La piattaforma tenterà quindi l'azione.

## <a name="step-1-prepare-for-migration"></a>Passaggio 1: Preparazione per la migrazione

Ecco alcune procedure che è consigliabile durante la valutazione della migrazione IaaS delle risorse da classica di Manager delle risorse:

- Leggere l' [elenco di configurazioni non supportate o funzionalità](virtual-machines-windows-migration-classic-resource-manager.md). Se si dispone di macchine virtuali che utilizzano configurazioni non supportate o funzionalità, è consigliabile attendere il supporto di configurazione delle funzionalità da presentare. In alternativa, è possibile rimuovere tale funzionalità o spostare fuori la configurazione per abilitare la migrazione, se adatto alle proprie esigenze.
-   Se si dispone di automatizzato script per distribuire l'infrastruttura e applicazioni oggi, provare a creare una configurazione di test simili usando gli script per la migrazione. In alternativa, è possibile impostare ambienti di esempio tramite il portale di Azure.

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Passaggio 2: Configurare l'abbonamento e registrare il provider

Per gli scenari di migrazione, è necessario configurare l'ambiente per entrambi classica e Manager delle risorse. [Installare CLI Azure](../xplat-cli-install.md) e [Selezionare l'abbonamento](../xplat-cli-connect.md).

Effettuare l'accesso al proprio account.
    
    azure login

Selezionare l'abbonamento Azure tramite il comando seguente.

    azure account set "<azure-subscription-name>"

>[AZURE.NOTE] La registrazione è uno fase ma deve essere eseguita una volta prima di eseguire la migrazione. Senza eseguire la registrazione verrà visualizzato il seguente messaggio di errore 

>   *BadRequest: Sottoscrizione non è registrato per la migrazione.* 

Registrare con il provider di risorse di migrazione utilizzando il comando seguente. Si noti che in alcuni casi, il timeout del comando. Tuttavia, la registrazione verrà eseguita correttamente.

    azure provider register Microsoft.ClassicInfrastructureMigrate

Attendere cinque minuti per la registrazione completare. È possibile verificare lo stato dell'approvazione utilizzando il comando seguente. Verificare che sia RegistrationState `Registered` prima di procedere.

    azure provider show Microsoft.ClassicInfrastructureMigrate

Passare a questo punto CLI per il `asm` modalità.

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Passaggio 3: Verificare che sia sufficiente core macchina virtuale di Azure Manager delle risorse nell'area Azure della distribuzione corrente o VNET

Per eseguire questa operazione è necessario passare a `arm` modalità. Ripetere l'operazione con il comando seguente.

```
azure config mode arm
```

È possibile utilizzare il comando CLI seguente per controllare la quantità di core che nel gestore di risorse di Azure. Per ulteriori informazioni sugli obiettivi di base, vedere [limiti e la gestione di risorse di Azure](../articles/azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Dopo aver verificato questo passaggio, è possibile tornare alla `asm` modalità.

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Passaggio 4: Opzione 1: eseguire la migrazione di macchine virtuali in un servizio cloud 

Ottenere l'elenco dei servizi cloud usando il comando seguente e quindi selezionare il servizio cloud che si desidera eseguire la migrazione. Si noti che se macchine virtuali nel servizio cloud impegnato in una rete virtuale oppure se hanno ruoli di web/lavoro, verrà visualizzato un messaggio di errore.

    azure service list

Eseguire il seguente comando per ottenere il nome di distribuzione per il servizio cloud dall'output dettagliato. Nella maggior parte dei casi, il nome di distribuzione è lo stesso nome servizio cloud.

    azure service show <serviceName> -vv

Preparare le macchine virtuali nel servizio cloud per la migrazione. Sono disponibili due opzioni tra cui scegliere.

Se si desidera eseguire la migrazione di macchine virtuali a una rete virtuale creata piattaforma, usare il comando seguente.

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Se si desidera eseguire la migrazione a una rete virtuale esistente nel modello di distribuzione Manager delle risorse, utilizzare il comando seguente.

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

Una volta completato l'operazione di preparazione, è possibile esaminare output dettagliato per ottenere lo stato della migrazione di macchine virtuali e assicurarsi che siano nel `Prepared` stato.

    azure vm show <vmName> -vv

Verificare la configurazione per le risorse preparate utilizzando CLI o il portale di Azure. Se non si è pronti per la migrazione e si desidera tornare allo stato precedente, usare il comando seguente.

    azure service deployment abort-migration <serviceName> <deploymentName>

Se la configurazione preparata è soddisfacente, è possibile spostarsi in avanti ed eseguire il commit le risorse usando il comando seguente.

    azure service deployment commit-migration <serviceName> <deploymentName>


    
## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Passaggio 4: Opzione 2: eseguire la migrazione di macchine virtuali in una rete virtuale

Selezionare la rete virtuale che si desidera eseguire la migrazione. Nota Se la rete virtuale contiene web/lavoro ruoli o macchine virtuali con configurazioni non supportate, verrà visualizzato un messaggio di errore di convalida.

È possibile ottenere tutte le reti virtuali nella sottoscrizione tramite il comando seguente.

    azure network vnet list
    
L'output un aspetto simile al seguente:

![Schermata della riga di comando con il nome intero virtuali evidenziato.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

Nell'esempio precedente **virtualNetworkName** è il nome intero **"Gruppo classicubuntu16 classicubuntu16"**.

Preparare la rete virtuale preferito per la migrazione utilizzando il comando seguente.

    azure network vnet prepare-migration <virtualNetworkName>

Verificare la configurazione per le macchine virtuali preparate utilizzando CLI o il portale di Azure. Se non si è pronti per la migrazione e si desidera tornare allo stato precedente, usare il comando seguente.

    azure network vnet abort-migration <virtualNetworkName>

Se la configurazione preparata è soddisfacente, è possibile spostarsi in avanti ed eseguire il commit le risorse usando il comando seguente.

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>Passaggio 5: Eseguire la migrazione di un account di archiviazione

Dopo aver completato la migrazione di macchine virtuali, è consigliabile eseguire la migrazione account di archiviazione.

Preparare l'account di archiviazione per la migrazione utilizzando il comando seguente

    azure storage account prepare-migration <storageAccountName>

Verificare la configurazione dell'account di archiviazione preparato utilizzando CLI o il portale di Azure. Se non si è pronti per la migrazione e si desidera tornare allo stato precedente, usare il comando seguente.

    azure storage account abort-migration <storageAccountName>

Se la configurazione preparata è soddisfacente, è possibile spostarsi in avanti ed eseguire il commit le risorse usando il comando seguente.

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>Passaggi successivi

- [Piattaforma supportata la migrazione delle risorse IaaS da classico a Gestione risorse](virtual-machines-windows-migration-classic-resource-manager.md)
- [Tecnico approfondimento su migrazione piattaforma supportata da classico a Gestione risorse](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
