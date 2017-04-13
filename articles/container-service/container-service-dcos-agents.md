<properties
   pageTitle="Pubblici e privati cc/OS agente pool ACS | Microsoft Azure"
   description="Funzionamento pool agente pubblici e privati con un cluster di Azure contenitore servizio."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenitori, Micro-servizi, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="timlt"/>

# <a name="dcos-agent-pools-for-azure-container-service"></a>CC/OS agente pool per il servizio contenitore Azure

Servizio contenitore Azure cc/OS divide agenti in pool pubblico o privato. Una distribuzione può essere eseguita in uno dei due pool, che interessano accessibilità tra computer nel servizio di contenitore. Il computer può essere esposto su internet (pubblico) o mantenuti interno (privato). In questo articolo fornisce una breve panoramica di origine di un pool di pubblico e privato.

### <a name="private-agents"></a>Agenti privati

I nodi agente privato eseguire tramite una rete non routing. Questa rete è accessibile solo dall'area amministratore o attraverso il router bordo zona pubblico. Per impostazione predefinita, controller di dominio/OS avvia App nei nodi agente privato. Per ulteriori informazioni sulla protezione di rete, consultare la [documentazione cc/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/) .

### <a name="public-agents"></a>Agenti pubblici

I nodi agente pubblico eseguire cc/OS App e servizi tramite una rete accessibile al pubblico. Per ulteriori informazioni sulla protezione di rete, consultare la [documentazione cc/OS](https://dcos.io/docs/1.7/administration/securing-your-cluster/) .

## <a name="using-agent-pools"></a>Usando pool agente

Per impostazione predefinita, **Marathon** distribuisce qualsiasi applicazione di nuovo per i nodi agente *privato* . È necessario in modo esplicito distribuire l'applicazione al livello di nodo *pubblico* durante la creazione dell'applicazione. Selezionare la scheda **facoltativo** e immettere **slave_public** per il valore **Accettato dei ruoli delle risorse** . Questo processo è descritta [di seguito](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) e nella documentazione di [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) .

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sulla [Gestione i contenitori di controller di dominio/OS](container-service-mesos-marathon-ui.md).

Informazioni su come [aprire il firewall](container-service-enable-public-access.md) fornito da Azure per consentire l'accesso pubblico per il contenitore controller di dominio/OS.