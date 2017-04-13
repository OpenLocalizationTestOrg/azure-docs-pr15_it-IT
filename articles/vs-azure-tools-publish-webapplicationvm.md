<properties
   pageTitle="Pubblicare WebApplicationVM | Microsoft Azure"
   description="Informazioni su come distribuire un'applicazione web a una macchina virtuale. Questo script crea le risorse necessarie nell'abbonamento Azure se non sono presenti."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publish-webapplicationvm-windows-powershell-script"></a>Pubblicare-WebApplicationVM (script di Windows PowerShell)

Consente di distribuire un'applicazione web a una macchina virtuale. Lo script crea le risorse necessarie nell'abbonamento Azure se non sono presenti.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Configurazione

Il percorso del file di configurazione JSON che descrive i dettagli della distribuzione.

|Alias|Nessuno|
|---|---|
|Obbligatorio?|vero|
|Posizione|denominata|
|Valore predefinito|Nessuno|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

### <a name="subscriptionname"></a>SubscriptionName

Il nome della sottoscrizione Azure in cui si desidera creare la macchina virtuale.

|Alias|Nessuno|
|---|---|
|Obbligatorio?|FALSO|
|Posizione|denominata|
|Valore predefinito|Utilizza l'abbonamento prima del file di abbonamento|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

### <a name="webdeploypackage"></a>WebDeployPackage

Il percorso per il pacchetto di distribuzione web per pubblicare la macchina virtuale. È possibile creare il pacchetto utilizzando la creazione guidata pubblicazione Web in Visual Studio. Vedere [procedura: creare un pacchetto di distribuzione Web in Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

|Alias|Nessuno|
|---|---|
|Obbligatorio?|FALSO|
|Posizione|denominata|
|Valore predefinito|Nessuno|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

### <a name="allowuntrusted"></a>AllowUntrusted

Se true, consente l'uso dei certificati non firmato da una fonte attendibile.

|Alias|Nessuno|
|---|---|
|Obbligatorio?|FALSO|
|Posizione|denominata|
|Valore predefinito|FALSO|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

### <a name="vmpassword"></a>VMPassword

Le credenziali dell'account macchina virtuale. Esempio: - VMPassword @{Name = "admin"; Password = "password"}

|Alias|Nessuno|
|---|---|
|Obbligatorio?|FALSO|
|Posizione|denominata|
|Valore predefinito|Nessuno|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

### <a name="databaseserverpassword"></a>DatabaseServerPassword

Le credenziali per il database di SQL Azure. Esempio: - DatabaseServerPassword @{Name = "admin"; Password = "password"}

|Alias|Nessuno|
|---|---|
|Obbligatorio?|FALSO|
|Posizione|denominata|
|Valore predefinito|Nessuno|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Se true, stampa messaggi dello script per il flusso di output.

|Alias|Nessuno|
|---|---|
|Obbligatorio?|FALSO|
|Posizione|denominata|
|Valore predefinito|FALSO|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

## <a name="remarks"></a>Note

Per una spiegazione completa di come utilizzare lo script per creare ambienti di sviluppo e Test, vedere [Usando gli script di PowerShell di Windows per la pubblicazione di sviluppo e ambienti di Test](vs-azure-tools-publishing-using-powershell-scripts.md).

File di configurazione JSON specifica i dettagli del contenuto per la distribuzione. Include le informazioni specificate durante la creazione del progetto, ad esempio nome, gruppo affinità, immagine del disco rigido virtuale e dimensioni della macchina virtuale. Inoltre include gli endpoint del computer virtuali, i database per effettuare il provisioning, se presenti e web parametri di distribuzione. Il codice seguente mostra un file di configurazione JSON di esempio:

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

È possibile modificare il file di configurazione JSON per modificare cosa viene eseguito il provisioning. Sono necessari una macchina virtuale e un servizio cloud, ma la sezione database è facoltativa.
