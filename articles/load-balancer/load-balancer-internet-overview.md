
<properties
   pageTitle="Panoramica di bilanciamento del carico dei è connessa a Internet | Microsoft Azure "
   description="Panoramica di Internet affiancate di bilanciamento del carico e delle relative caratteristiche. Funzionamento un bilanciamento del carico per Azure utilizzando macchine virtuali e servizi cloud."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="internet-facing-load-balancer-overview"></a>Panoramica di bilanciamento carico esposto Internet

Bilanciamento del carico Azure cartine il pubblico indirizzo IP e porte numero del traffico in ingresso il IP indirizzo e la porta numero privato della macchina virtuale e viceversa per il traffico di risposta dal computer virtuale. Regole di bilanciamento del carico consentono di distribuire specifici tipi di traffico tra più macchine virtuali o servizi. Ad esempio, è possibile distribuire il carico di traffico richiesta web tra più server web o ruoli web.

Per un servizio cloud che contiene le istanze di ruoli web o ruoli di lavoro, è possibile definire un endpoint pubblico nel file di definizione (.csdef) del servizio.

Il file _servicedefinition.csdef_ contiene la configurazione dell'endpoint e quando si dispone di più istanze del ruolo di una distribuzione di ruolo web o lavoro, il servizio di bilanciamento del carico sarà il programma di installazione per renderla. Il modo per aggiungere le istanze alla distribuzione cloud in fase di modifica il conteggio delle istanze nel file di configurazione del servizio (.csfg).

La figura seguente mostra un endpoint di bilanciamento del carico per il traffico web crittografati condivisi tra tre macchine virtuali per la porta TCP pubblica e privata 443. Questi tre macchine virtuali sono in un set di bilanciamento del carico.

![esempio di bilanciamento carico pubblico](./media/load-balancer-internet-overview/IC727496.png))

Figura 1 - endpoint di bilanciamento del carico per il traffico web crittografato

Quando client Internet inviano richieste di pagine web all'indirizzo IP pubblico del servizio cloud sulla porta TCP 443, il bilanciamento del carico di Azure distribuisce le richieste tra le tre macchine virtuali nel set di bilanciamento del carico. Per ulteriori informazioni sugli algoritmi bilanciamento carico, vedere la [pagina Panoramica di bilanciamento del carico](load-balancer-overview.md#load-balancer-features).

Per impostazione predefinita, bilanciamento del carico Azure distribuisce il traffico di rete in modo uniforme tra più istanze di macchina virtuale. È inoltre possibile configurare affinità sessione, per ulteriori informazioni, vedere [modalità di distribuzione carico di bilanciamento del carico](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [interne bilanciamento del carico](load-balancer-internal-overview.md) comprendere meglio il bilanciamento del carico è meglio per la distribuzione cloud.

È anche possibile [iniziare la creazione di un servizio di bilanciamento del carico è connessa a Internet](load-balancer-get-started-internet-arm-ps.md) e configurare il tipo della [modalità di distribuzione](load-balancer-distribution-mode.md) per una specifica bilanciamento rete il traffico caricamento.

Se l'applicazione necessita di mantenere le connessioni attiva per i server dietro un bilanciamento del carico, è possibile comprendere a fondo [le impostazioni di timeout di inattività TCP per un bilanciamento del carico](load-balancer-tcp-idle-timeout.md). Può risultare utile per conoscere il comportamento della connessione inattivo quando si utilizza Bilanciamento del carico di Azure.
