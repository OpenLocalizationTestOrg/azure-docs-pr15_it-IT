<properties
   pageTitle="Installare il controller di dominio/sistema operativo CLI | Microsoft Azure"
   description="Installare il controller di dominio/sistema operativo CLI."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenitori, servizi di Micro controller di dominio/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] Si tratta per l'uso con i cluster basati su controller di dominio/OS ACS. È necessario ripetere l'operazione per cluster basate su sciame ACS.

Prima di tutto, [connettersi al cluster basato su controller di dominio/OS ACS](../articles/container-service/container-service-connect.md). Una volta eseguita questa operazione, è possibile installare CLI cc/sistema operativo del computer client con i comandi seguenti:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Se si utilizza una versione precedente di Python, è possibile notare alcune "InsecurePlatformWarnings". È possibile ignorare questi.

Per iniziare senza riavviare la shell, eseguire:

```bash
source ~/.bashrc
```

Questo passaggio non sarà necessario quando si avvia nuova shell.

A questo punto è possibile verificare che sia installata CLI:

```bash
dcos --help
```