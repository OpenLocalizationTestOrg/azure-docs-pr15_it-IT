<properties
   pageTitle="Distribuire un cluster di Azure contenitore servizio | Microsoft Azure"
   description="Distribuire un cluster di Azure contenitore servizio tramite il portale di Azure, CLI Azure o PowerShell."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenitori, Micro-servizi, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# <a name="deploy-an-azure-container-service-cluster"></a>Distribuire un cluster di servizio contenitore Azure

Servizio contenitore Azure fornisce distribuzione rapida del contenitore Apri origine popolari soluzioni cluster e orchestrazione. Tramite il servizio contenitore di Azure, è possibile distribuire controller di dominio/OS e Particle Swarm Docker cluster con i modelli di gestione di risorse Azure o il portale di Azure. Si distribuiscono questi cluster utilizzando Azure macchina virtuale scala set e i cluster vantaggi offerti da Azure offerte di rete e lo spazio di archiviazione. Per accedere al servizio contenitore Azure, è necessario un abbonamento a Azure. Se non si dispone di uno, è possibile accedere per una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935).

Questo documento viene illustrata la distribuzione di un cluster di Azure contenitore servizio tramite il [portale di Azure](#creating-a-service-using-the-azure-portal), [Azure interfaccia riga di comando (CLI)](#creating-a-service-using-the-azure-cli)e il [modulo di Azure PowerShell](#creating-a-service-using-powershell).  

## <a name="create-a-service-by-using-the-azure-portal"></a>Creare un servizio tramite il portale di Azure

Accedere al portale di Azure, selezionare **Nuovo**e cercare **Servizio contenitore Azure**Azure Marketplace.

![Creare distribuzione 1](media/acs-portal1.png)  <br />

Selezionare **Il servizio contenitore Azure**e fare clic su **Crea**.

![Creare distribuzione 2](media/acs-portal2.png)  <br />

Immettere le informazioni seguenti:

- **Nome utente**: si tratta del nome utente che verrà utilizzato per un account su ciascuna delle macchine virtuali e scala macchina virtuale imposta cluster Azure contenitore servizio.
- **Abbonamento**: selezionare un abbonamento a Azure.
- **Gruppo risorse**: selezionare un gruppo di risorse esistente o crearne uno nuovo.
- **Posizione**: selezionare un'area di Azure per la distribuzione di Azure contenitore servizio.
- **Chiave pubblica SSH**: aggiungere la chiave pubblica che verrà utilizzata per l'autenticazione rispetto a macchine virtuali di Azure contenitore servizio. È importante che questa chiave non contiene interruzioni di riga e che include il prefisso 'ssh rsa' e 'username@domain' un suffisso. Dovrebbe essere simile alla seguente: **AAAAB3Nz ssh rsa … <>...... UcyupgH azureuser@linuxvm **. Per ulteriori informazioni sulla creazione di chiavi Secure Shell (SSH), vedere gli articoli [Linux]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) e [Windows]( https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/) .

Quando si è pronti per continuare, fare clic su **OK** .

![Creare distribuzione 3](media/acs-portal3.png)  <br />

Selezionare un tipo di orchestrazione. Le opzioni sono:

- **Cc/OS**: distribuisce un cluster di controller di dominio/OS.
- **Particle Swarm**: distribuisce un cluster Particle Swarm Docker.

Quando si è pronti per continuare, fare clic su **OK** .

![Creare distribuzione 4](media/acs-portal4.png)  <br />

Immettere le informazioni seguenti:

- **Contare schema**: il numero di schemi del cluster.
- **Conteggio agente**: per Particle Swarm Docker, questo sarà il numero iniziale di agenti nel set di scala agente. Per controller di dominio o il sistema operativo, si sarà il numero iniziale di agenti in un set di scala privato. Inoltre, viene creato un set di scala pubblico, che contiene un numero prestabilito di agenti. Il numero di agenti di questo set di scala pubblica è determinato dai master quanti sono stati creati in cluster--un agente pubblico per uno schema e due agenti pubblici per tre o cinque schemi.
- **Dimensioni del macchina virtuale dell'agente**: le dimensioni delle macchine virtuali agente.
- **Prefisso DNS**: un nome univoco del mondo che verrà utilizzato per prefisso parti principali dei nomi di dominio completo per il servizio.

Quando si è pronti per continuare, fare clic su **OK** .

![Creare distribuzione 5](media/acs-portal5.png)  <br />

Al termine della convalida dei servizi, fare clic su **OK** .

![Creare distribuzione 6](media/acs-portal6.png)  <br />

Fare clic su **Crea** per avviare il processo di distribuzione.

![Creare distribuzione 7](media/acs-portal7.png)  <br />

Se si è scelto di bloccare la distribuzione al portale di Azure, è possibile visualizzare lo stato di distribuzione.

![Creare distribuzione 8](media/acs-portal8.png)  <br />

Al termine di distribuzione, il cluster di Azure contenitore servizio è pronto per l'utilizzo.

## <a name="create-a-service-by-using-the-azure-cli"></a>Creare un servizio tramite CLI Azure

Per creare un'istanza di Azure contenitore servizio tramite la riga di comando, è necessario un abbonamento a Azure. Se non si dispone di uno, è possibile accedere per una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). Inoltre, è necessario avere [installato](../xplat-cli-install.md) e [configurato](../xplat-cli-connect.md) CLI Azure.

Per distribuire un controller di dominio/OS o Particle Swarm Docker cluster, selezionare uno dei modelli di esempio GitHub. Si noti che entrambi i modelli sono gli stessi, fatta eccezione per la selezione di orchestrator predefinita.

* [Modello di controller di dominio/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Modello sciame](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Poi, assicurarsi che CLI Azure sia stata collegata a un abbonamento a Azure. È possibile eseguire questa operazione utilizzando il comando seguente:

```bash
azure account show
```
Se non viene restituito un account Azure, utilizzare il comando seguente per accedere CLI su Azure.

```bash
azure login -u user@domain.com
```

Quindi configurare gli strumenti di Azure CLI per utilizzare Gestione risorse di Azure.

```bash
azure config mode arm
```

Creare un gruppo di risorse Azure e il contenitore cluster con il comando seguente, in cui:

- **RESOURCE_GROUP** è il nome del gruppo di risorse che si desidera utilizzare per il servizio.
- **Posizione** è l'area Azure in cui verrà creati il gruppo di risorse e la distribuzione di Azure contenitore servizio.
- **TEMPLATE_URI** è il percorso del file di distribuzione. Si noti che deve trattarsi di file non elaborato, non un puntatore alla UI GitHub. Per trovare questo URL, selezionare il file azuredeploy.json in GitHub e fare clic sul pulsante **materie** .

> [AZURE.NOTE] Quando si esegue questo comando, la shell verrà chiesto di valori dei parametri di distribuzione.

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Fornire i parametri del modello

Questa versione del comando è necessario definire i parametri in modo interattivo. Se si desidera fornire parametri, ad esempio una stringa di formato JSON, è possibile eseguire questa operazione utilizzando la `-p` passare. Per esempio:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

In alternativa, è possibile fornire un file in formato JSON parametri utilizzando il `-e` passare:

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

Per visualizzare un file di parametri di esempio denominato `azuredeploy.parameters.json`, cercare con i modelli di servizio contenitore Azure in GitHub.

## <a name="create-a-service-by-using-powershell"></a>Creare un servizio tramite PowerShell

È inoltre possibile distribuire un cluster di Azure contenitore servizio con PowerShell. In questo documento è basato sulla versione 1.0 [modulo PowerShell per Azure](https://azure.microsoft.com/blog/azps-1-0/).

Per distribuire un controller di dominio/OS o Particle Swarm Docker cluster, selezionare uno dei modelli di esempio. Si noti che entrambi i modelli sono gli stessi, fatta eccezione per la selezione di orchestrator predefinita.

* [Modello di controller di dominio/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Modello sciame](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Prima di creare un cluster di abbonamento Azure, verificare che la sessione di PowerShell sia stata firmata Azure. È possibile eseguire questa operazione con la `Get-AzureRMSubscription` comando:

```powershell
Get-AzureRmSubscription
```

Se è necessario effettuare l'accesso a Azure, utilizzare la `Login-AzureRMAccount` comando:

```powershell
Login-AzureRmAccount
```

Se si distribuisce a un nuovo gruppo di risorse, è innanzitutto necessario creare il gruppo di risorse. Per creare un nuovo gruppo di risorse, utilizzare la `New-AzureRmResourceGroup` comando e specificare un'area nome e la destinazione del gruppo risorse:

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

Dopo aver creato un gruppo di risorse, è possibile creare il cluster con il comando seguente. URI del modello desiderato verrà specificato per il `-TemplateUri` parametro. Quando si esegue questo comando, PowerShell verrà chiesto di valori dei parametri di distribuzione.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>Fornire i parametri del modello

Se si ha familiarità con PowerShell, è possibile sapere che è possibile scorrere i parametri disponibili per un cmdlet digitando un segno meno (-) e quindi premendo il tasto TAB. La stessa funzionalità funziona anche con i parametri definiti nel modello scelto. Non appena si digita il nome del modello, il cmdlet recupera il modello, analizza i parametri e i parametri del modello viene aggiunta al comando in modo dinamico. In questo modo molto semplice specificare i valori dei parametri di modello. E, se si dimentica un valore di parametro obbligatorio, viene richiesto per il valore di PowerShell.

Di seguito è il comando completa, con i parametri inclusi. È possibile fornire i valori corrispondenti ai nomi delle risorse.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un cluster funziona, vedere questi documenti per i dettagli di gestione e la connessione:

- [Connettersi a un cluster di servizio contenitore Azure](container-service-connect.md)
- [Lavorare con il servizio contenitore Azure e controller di dominio/OS](container-service-mesos-marathon-rest.md)
- [Lavorare con il servizio contenitore Azure e sciame Docker](container-service-docker-swarm.md)
