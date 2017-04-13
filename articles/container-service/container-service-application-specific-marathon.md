<properties
   pageTitle="Applicazione o un servizio Marathon specifiche dell'utente | Microsoft Azure"
   description="Creare un'applicazione o servizio Marathon specifiche dell'utente"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Contenitori, Marathon, Micro-servizi, controller di dominio/sistema operativo, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# <a name="create-an-application-or-user-specific-marathon-service"></a>Creare un'applicazione o servizio Marathon specifiche dell'utente

Servizio contenitore Azure fornisce un set di server master su cui è preconfigurare Apache Mesos e Marathon. Questi può essere utilizzati per gestire le applicazioni nel cluster, ma è consigliabile non usare i server master a tale scopo. Ad esempio, modificare la configurazione di Marathon richiede l'accesso ai server master e di apportare modifiche, questo facilita il server master univoco che è leggermente diversa dallo standard e devono essere assistito e gestiti in modo indipendente. Inoltre, la configurazione necessaria affinché un team potrebbe non essere la configurazione ottimale per un'altra parte del team.

In questo articolo verrà viene illustrato come aggiungere un'applicazione o servizio Marathon specifiche dell'utente.

Questo servizio apparterrà a un singolo utente o del team, pertanto sono gratuite per configurarlo in modo che richiedono. Inoltre, il servizio contenitore Azure garantisce che il servizio continua a eseguire. Se il servizio non riesce, il servizio contenitore Azure verrà viene riavviato. La maggior parte dei casi è anche non è possibile notare che aveva il tempo di inattività.

## <a name="prerequisites"></a>Prerequisiti

[Distribuire un'istanza di Azure contenitore servizio](container-service-deployment.md) con tipo orchestrator cc/OS e [Assicurarsi che il client è possibile connettersi al cluster](container-service-connect.md). Inoltre, eseguire la procedura seguente.

[AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Creare un'applicazione o servizio Marathon specifiche dell'utente

Iniziare creando un file di configurazione JSON che definisce il nome del servizio di applicazione che si desidera creare. Nell'esempio si utilizza `marathon-alice` come nome del framework. Salvare il file come `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Successivamente, utilizzare CLI cc/OS per installare l'istanza di Marathon con le opzioni impostate nel file di configurazione:

```bash
dcos package install --options=marathon-alice.json marathon
```

Verrà visualizzata la `marathon-alice` servizio in esecuzione nella scheda servizi dell'interfaccia utente/sistema operativo controller di dominio. L'interfaccia utente sarà `http://<hostname>/service/marathon-alice/` se si vuole accedere direttamente.

## <a name="set-the-dcos-cli-to-access-the-service"></a>Impostare CLI cc/OS per accedere al servizio

È possibile configurare il CLI cc/OS per accedere a questo nuovo servizio mediante l'impostazione di `marathon.url` proprietà in modo che puntino alla `marathon-alice` istanza come indicato di seguito:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

È possibile verificare quali istanza Marathon in cui è utilizzati il CLI con la `dcos config show` comando. È possibile ripristinare l'utilizzo del servizio Marathon master con il comando `dcos config unset marathon.url`.
