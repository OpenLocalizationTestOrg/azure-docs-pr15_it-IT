<properties
   pageTitle="Automatizzare la distribuzione di applicazione con estensioni macchina virtuale | Microsoft Azure"
   description="Esercitazione DotNet Core Azure macchina virtuale"
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="neilpeterson"
   manager="timlt"
   editor="tysonn"
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/21/2016"
   ms.author="nepeters"/>

# <a name="application-deployment-with-azure-resource-manager-templates"></a>Distribuzione di applicazioni con i modelli di Manager delle risorse di Azure

Una volta tutti i requisiti infrastrutturali Azure sono stati identificati e convertiti in un modello di distribuzione, la distribuzione dell'applicazione effettivo deve essere affrontato. Distribuzione di applicazioni qui fa riferimento all'installazione di file binari di applicazione effettiva le risorse Azure del. Per l'esempio di archivio di musica, .net Core, NGINX e Supervisore devono essere installato e configurato in ogni macchina virtuale. I file binari musica Store è necessario installare nel computer virtuale e il database dell'archivio musica creato in precedenza.

In questo documento dettaglio come macchina virtuale estensioni possono automatizzare la distribuzione di applicazioni e configurazione di macchine virtuali di Azure. Tutte le dipendenze e configurazioni univoche vengono evidenziate. Per risultati ottimali, distribuire un'istanza della soluzione per l'abbonamento Azure e il lavoro con il modello di gestione di risorse Azure. Il modello completato sono disponibili qui: [Musica archivio distribuzione su Ubuntu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="configuration-script"></a>Script di configurazione

Estensioni macchina virtuale sono i programmi specializzati eseguire in macchine virtuali per fornire l'automazione configurazione. Estensioni sono disponibili per diversi scopi specifici, ad esempio antivirus, configurazione di registrazione e configurazione di Docker. Estensione di script personalizzati è utilizzabile per eseguire qualsiasi script una macchina virtuale. Con l'esempio di archivio di musica, in questo caso, l'estensione di script personalizzati per configurare le macchine virtuali Ubuntu e installare l'applicazione di archiviazione di musica.

Prima di dettaglio dichiarate come estensioni macchina virtuale in un modello di gestione risorse di Azure, esaminare lo script che viene eseguito. Questo script consente di configurare la macchina virtuale Ubuntu per ospitare l'applicazione dell'archiviazione musica. Quando si esegue, lo script consente di installare tutti necessari software, installare l'applicazione di archiviazione musica dal controllo origine e preparare il database. 

Per maggiori informazioni sull'hosting .net Core applicazione su Linux, vedere [pubblicazione in un ambiente di produzione Linux](https://docs.asp.net/en/latest/publishing/linuxproduction.html). 

> In questo esempio è dimostrativo.

```none
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>Estensione di Script macchine Virtuali

Estensioni macchine Virtuali può essere eseguite su una macchina virtuale in fase di compilazione includendo la risorsa estensione nel modello di gestione di risorse Azure. L'estensione può essere aggiunti con la procedura guidata Visual Studio Aggiungi risorsa oppure inserendo un JSON valido nel modello. La risorsa Script estensione è nidificata all'interno della risorsa macchina virtuale; può essere visualizzato nell'esempio seguente.

Seguire questo collegamento per visualizzare l'esempio JSON all'interno del modello di gestione risorse- [Macchine Virtuali Script estensione](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359). 

Si noti che nel sotto JSON che lo script viene salvato in GitHub. Questo script può anche essere archiviato in archiviazione Blob Azure. Inoltre, i modelli di gestione risorse Azure consentono la stringa di esecuzione di script impostata in modo che i valori dei parametri di modello possono essere utilizzati come parametri per l'esecuzione di script. In questo caso i dati sono disponibili per la distribuzione di modelli e questi valori possono quindi essere usati durante l'esecuzione di script.

```none
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Per ulteriori informazioni sull'utilizzo dell'estensione di script personalizzati, vedere [estensioni di script personalizzati con i modelli di Manager delle risorse](./virtual-machines-linux-extensions-customscript.md).

## <a name="next-step"></a>Passaggio successivo

<hr>

[Esplorare Azure più modelli di Manager delle risorse](https://github.com/Azure/azure-quickstart-templates)
