<properties
   pageTitle="Pubblicare-WebApplicationWebSite (script di Windows PowerShell) | Microsoft Azure"
   description="Informazioni su come pubblicare un progetto web in un sito Web di Azure. Questo script crea le risorse necessarie nell'abbonamento Azure se non sono presenti."
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

# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Pubblicare-WebApplicationWebSite (script di Windows PowerShell)

##<a name="syntax"></a>Sintassi

Pubblica un progetto web in un sito Web di Azure. Lo script crea le risorse necessarie nell'abbonamento Azure se non sono presenti.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Configurazione

Il percorso del file di configurazione JSON che descrive i dettagli della distribuzione.

|Parametro|Valore predefinito|
|---|---|
|Alias|Nessuno|
|Obbligatorio?|vero|
|Posizione|denominata|
|Valore predefinito|Nessuno|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

## <a name="subscriptionname"></a>SubscriptionName

Il nome della sottoscrizione Azure che si desidera creare il sito Web di.

|Parametro|Valore predefinito|
|---|---|
|Alias|Nessuno|
|Obbligatorio?|FALSO|
|Posizione|denominata|
|Valore predefinito|Nessuno|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

## <a name="webdeploypackage"></a>WebDeployPackage

Il percorso per il pacchetto di distribuzione web per pubblicare il sito Web. È possibile creare il pacchetto utilizzando la creazione guidata pubblicazione Web in Visual Studio. Per ulteriori informazioni, vedere [Introduzione a servizi Cloud Windows Azure e ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

|Parametro|Valore predefinito|
|---|---|
|Alias|Nessuno|
|Obbligatorio?|FALSO|
|Posizione|denominata|
|Valore predefinito|Nessuno|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

## <a name="databaseserverpassword"></a>DatabaseServerPassword

Nome utente e password per il database di SQL Azure.

|Parametro|Valore predefinito|
|---|---|
|Alias|Nessuno|
|Obbligatorio?|FALSO|
|Posizione|denominata|
|Valore predefinito|Nessuno|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Se true, stampa messaggi dello script per il flusso di output.

|Parametro|Valore predefinito|
|---|---|
|Alias|Nessuno|
|Obbligatorio?|FALSO|
|Posizione|denominata|
|Valore predefinito|FALSO|
|Accettare input della pipeline?|FALSO|
|Accettare i caratteri jolly?|FALSO|

## <a name="remarks"></a>Note

Per una spiegazione completa di come utilizzare lo script per creare ambienti di sviluppo e Test, vedere [Usando gli script di PowerShell di Windows per la pubblicazione di sviluppo e ambienti di Test](vs-azure-tools-publishing-using-powershell-scripts.md).

File di configurazione JSON specifica i dettagli del contenuto per la distribuzione. Include le informazioni specificate al momento della creazione del progetto, ad esempio il nome e il nome utente per il sito Web. Include inoltre il database a disposizione, se presenti. Il codice seguente mostra un file di configurazione JSON di esempio:

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

È possibile modificare il file di configurazione JSON per modificare cosa viene distribuito. È necessaria una sezione sito Web, ma la sezione database è facoltativa.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [Pubblicazione-WebApplicationVM (script di Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)
