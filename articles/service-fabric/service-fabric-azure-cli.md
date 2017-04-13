<properties
   pageTitle="Interazione con i cluster di servizio tessuti usa CLI | Microsoft Azure"
   description="Come usare Azure CLI per interagire con un cluster di tessuti di servizio"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>Utilizzo di CLI Azure per interagire con un Cluster di tessuti servizio

È possibile interagire con i cluster tessuti servizio dal computer Linux con Azure CLI su Linux.

Il primo passaggio è ottenere l'ultima versione dalla rappresentazione fra CLI e configurare il percorso utilizzando i comandi seguenti:

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

Per ogni comando supporta, è possibile digitare il nome del comando per ottenere la Guida per tale comando. Completamento automatico è supportato per i comandi. Ad esempio, il comando seguente offre assistenza per tutti i comandi dell'applicazione. 

```sh
 azure servicefabric application 
```

È possibile filtrare ulteriormente la Guida per un determinato comando, come illustrato nell'esempio seguente:

```sh
 azure servicefabric application  create
```

Per attivare il completamento automatico in CLI, eseguire i comandi seguenti:

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

I comandi seguenti connettersi al cluster e mostrano i nodi del cluster:

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Per utilizzare parametri denominati e cosa sono, è possibile digitare - Guida dopo un comando. Per esempio:

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Quando ci si connette a un cluster più computer da un computer **che non fanno parte del cluster**, utilizzare il comando seguente:

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Sostituire il tag PublicIPorFQDN con il reale IP o il nome di dominio completo in base alle esigenze. Quando ci si connette a un cluster più computer da un computer **che fanno parte del cluster**, utilizzare il comando seguente:

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

È possibile utilizzare PowerShell o CLI per interagire con i Cluster di tessuti servizio Linux creato tramite il portale di Azure. 

**Attenzione:** Questi cluster non sono protetti, pertanto, potrebbe essere aprendo la casella di uno aggiungendo l'indirizzo IP pubblico nel manifesto cluster.



## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>Utilizzo di CLI Azure per connettersi a un Cluster di tessuti servizio

I comandi di Azure CLI seguenti viene descritto come connettersi a un cluster protetto. I dettagli del certificato devono corrispondere al certificato nei nodi del cluster.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```
 
Se il certificato ha autorità di certificazione (CA), è necessario aggiungere il parametro del percorso di certificato - ca analogo al seguente: 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Se si dispone di più CA, utilizzare la virgola come delimitatore.
 
Se il nome comune del certificato non corrisponde all'endpoint di connessione, è possibile utilizzare il parametro `--strict-ssl` di ignorare la verifica, come illustrato nel comando seguente: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```
 
Se si desidera ignorare la verifica CA, è possibile aggiungere--parametro non autorizzato Rifiuta come riportato nel comando seguente: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```
 
Dopo la connessione, dovrebbe essere possibile eseguire altri comandi CLI per interagire con i cluster. 

## <a name="deploying-your-service-fabric-application"></a>Distribuire l'applicazione di servizio tessuti

Eseguire i comandi seguenti per copiare, eseguire la registrazione e avviare l'applicazione del servizio tessuti:

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>L'aggiornamento dell'applicazione

Il processo è simile al [processo in Windows](service-fabric-application-upgrade-tutorial-powershell.md)).

Creare, copiare, eseguire la registrazione e creare l'applicazione dalla directory radice del progetto. Se l'istanza dell'applicazione è denominata tessuti: / MySFApp e il tipo di MySFApp, i comandi sarà il seguente:

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Apportare le modifiche all'applicazione e ricreerà il servizio modificato.  Aggiornare file manifesto del servizio modificate (ServiceManifest.xml) con le versioni più recenti per il servizio (e codice o configurazione o dati in base alle esigenze). Modificare anche manifesto dell'applicazione (ApplicationManifest.xml) con il numero di versione aggiornata per l'applicazione e il servizio modificato.  A questo punto, copiare e registrare l'applicazione aggiornata con i comandi seguenti:

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

A questo punto, è possibile avviare l'aggiornamento dell'applicazione con il comando seguente:

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

È ora possibile controllare l'aggiornamento dell'applicazione tramite SFX. In pochi minuti, l'applicazione di cui sono stato aggiornato.  È possibile provare a un'app aggiornata con un messaggio di errore e controllare la funzionalità di ripristino automatico di tessuti di servizio.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="copying-of-the-application-package-does-not-succeed"></a>La copia del pacchetto dell'applicazione non riesce

Controllare se `openssh` è installato. Per impostazione predefinita, Ubuntu Desktop non è installato. Installare Office tramite il comando seguente:

```
 sudo apt-get install openssh-server openssh-client**
```

Se il problema persiste, provare a disattivare PAM per ssh modificando il file **sshd_config** utilizzando i comandi seguenti:

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

Se il problema persiste, provare ad aumentare il numero di ssh sessioni eseguendo i comandi seguenti:

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
Tasti per ssh autenticazione (anziché password) non è ancora supportata (poiché la piattaforma utilizza ssh per copiare i pacchetti), quindi utilizzare l'autenticazione tramite password.


## <a name="next-steps"></a>Passaggi successivi

Configurare l'ambiente di sviluppo e distribuire un'applicazione di servizio tessuti in un cluster Linux.
