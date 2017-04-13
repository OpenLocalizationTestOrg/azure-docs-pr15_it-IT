<properties
    pageTitle="Approvato distribuzioni di Linux | Microsoft Azure"
    description="Informazioni sulle Linux nelle distribuzioni approvato Azure, comprese le linee guida per Ubuntu, OpenLogic, Oracle e SUSE."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager"
    />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="szark"/>



#<a name="linux-on-azure-endorsed-distributions"></a>Linux nelle distribuzioni approvato Azure

> [AZURE.NOTE] Se si dispone di qualche minuto, Aiutaci a migliorare la documentazione di supporto virtuale Linux Azure accettando [sondaggio rapido](https://aka.ms/linuxdocsurvey) dei propri interessi. Tutte le risposte aiutano Microsoft a Guida viene visualizzato il proprio lavoro.

Le immagini Linux nella raccolta di Azure o Marketplace vengono fornite da un numero di partner e stiamo lavorando con varie comunità Linux per aggiungere anche altre caratteristiche per la lista di distribuzione approvato. Nel frattempo, per le distribuzioni non disponibili nella raccolta è sempre possibile porta-Your-proprietari-Linux seguendo le indicazioni in [questa pagina](virtual-machines-linux-classic-create-upload-vhd.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="supported-distributions--versions"></a>Distribuzioni supportate e le versioni ##

La tabella seguente elenca le versioni supportate in Azure e distribuzioni Linux. Anche fare riferimento per il [supporto per le immagini di Linux in Microsoft Azure](https://support.microsoft.com/en-us/kb/2941892) per informazioni più dettagliate.

I driver di servizi di integrazione Linux (elenco) per Hyper-V e Azure sono moduli kernel che Microsoft contribuisce direttamente al kernel Linux padre.  I driver di elenco sono compilati nel kernel di distribuzione per impostazione predefinita o per precedenti RHEL/CentOS basato su distribuzioni sono disponibili come download separato [qui](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409).  Vedere [questo articolo](virtual-machines-linux-create-upload-generic.md#linux-kernel-requirements) per ulteriori informazioni sui driver elenco.

L'agente di Linux Azure già preinstallato in immagini raccolta Azure e sono in genere disponibile dal repository pacchetto di distribuzione.  Codice sorgente disponibili nel [GitHub](https://github.com/azure/walinuxagent).

Distribuzione|Versione|Driver|Agente
---|---|---|---
CentOS da OpenLogic | CentOS 6.3 + 7.0 + | CentOS 6.3: [Download di elenco](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +: Nel Kernel | Pacchetto: In [OpenLogic repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) in "WALinuxAgent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) | 494.4.0+ | Nel Kernel | Codice sorgente: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian | 7.9 Debian +, 8.2 + | Nel Kernel | Pacchetto: In repo in "waagent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent)
Linux Oracle | 6.4 +, 7.0 + | Nel Kernel | Pacchetto: In repo in "WALinuxAgent" <br/>Codice sorgente: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Red Hat Enterprise Linux | RHEL 6,7 + 7.1 + | Nel Kernel|Pacchetto: In repo in "WALinuxAgent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux Enterprise | SLES 11 SP4, SLES 12 SP1 + e <p> SLES per SAP 11 SP3 + | Nel Kernel | Pacchetto: Nel [Cloud: strumenti](https://build.opensuse.org/project/show/Cloud:Tools) repo in "agente di azure python" <br/>Codice sorgente: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE | openSUSE 13.2 + | Nel Kernel | Pacchetto: Nel [Cloud: strumenti](https://build.opensuse.org/project/show/Cloud:Tools) repo in "agente di azure python" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04, 14.04, 16.04, 16.10 | Nel Kernel | Pacchetto: In repo in "walinuxagent" <br/>Codice sorgente: [GitHub](https://github.com/Azure/WALinuxAgent)


## <a name="partners"></a>Partner

### <a name="openlogic"></a>OpenLogic
[http://www.openlogic.com/Azure](http://www.openlogic.com/azure)

OpenLogic è dei principali fornitori soluzioni Apri origine aziendali per il cloud e data center. OpenLogic consente di centinaia di zeri enterprise in una vasta gamma di settore per acquisire, supporto e controllare software open source in modo sicuro. OpenLogic offre supporto tecnico espressi in commerciali e indennizzo per 600 pacchetti Apri origine supportato da Comunità Expert OpenLogic, incluso il supporto di livello enterprise per CentOS come partner di avvio per le immagini CentOS basate su Azure.

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/Running-coreos/cloud-Providers/Azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

Dal sito Web CoreOS:

*CoreOS è progettato per la sicurezza, la coerenza e l'affidabilità. Invece di installazione di pacchetti tramite slurp o apt, CoreOS utilizza Linux contenitori per gestire i servizi a un livello superiore di astrazione. Codice di un singolo servizio e tutte le dipendenze vengono inserite in un contenitore che può essere eseguito su uno o più computer CoreOS.*


### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/Debian-Images-Microsoft-Azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ è indipendente consulenza e società di servizi, specializzata allo sviluppo e implementazione delle soluzioni professionale mediante l'utilizzo di software gratuito. Come specialisti Apri origine iniziali, Credative ha riconoscimento internazionale con molti reparti IT utilizzando il relativo supporto. In combinazione con Microsoft, Credativ si sta preparando immagini Debian corrispondenti per Debian 8 (Jessie) e Debian prima 7 (Wheezy), che sono progettate appositamente per l'esecuzione in Azure e può essere gestita facilmente tramite la piattaforma. Credativ supportano anche la manutenzione a lungo termine e aggiornamento delle immagini Debian per Azure attraverso il centro di supporto Apri origine.

### <a name="oracle"></a>Oracle
[http://www.Oracle.com/technetwork/topics/cloud/FAQ-1963009.HTML](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Strategia di Oracle è offrire un ampio portfolio di soluzioni a cloud pubblici e privati, offrendo clienti e flessibili come la distribuzione di software Oracle nuvole Oracle, nonché altre aree.  Collaborazione Oracle con Microsoft consente agli utenti di installare il software Oracle nel cloud Microsoft pubblici e privati con fiducia della certificazione e supportare da Oracle.  Oracle impegno e gli investimenti in soluzioni cloud pubblici e privati Oracle viene modificato.

### <a name="red-hat"></a>Red Hat
[http://www.redhat.com/en/partners/strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Il fornitore mondiale di soluzioni open source, Red Hat consente di più di 90% di società Fortune 500 risolvono sfide, allineare le IT e strategie commerciali e prepararsi per il futuro della tecnologia. A tale scopo, Red Hat che forniscono soluzioni protette tramite un modello aperto di business e un modello di abbonamento conveniente prevedibili.

### <a name="suse"></a>SUSE
[http://www.SuSE.com/SuSE-Linux-Enterprise-Server-on-Azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server in Azure è una piattaforma affidabile che offre maggiore affidabilità e sicurezza per il cloud computing. Piattaforma Linux versatile del SUSE integra perfettamente con servizi cloud Azure per fornire un ambiente basato su cloud facilmente gestibili. E con più di 9,200 applicazioni certificate da oltre 1.800 fornitori di software indipendenti per SUSE Linux Enterprise Server, SUSE garantisce che è possibile distribuire possiamo carichi di lavoro in esecuzione supportati nell'interfaccia di dati in Azure.

### <a name="canonical"></a>Canonici
[http://www.Ubuntu.com/cloud/Azure](http://www.ubuntu.com/cloud/azure)

Progettazione canonico e success del community aperta governance unità Ubuntu client, server e cloud computing, inclusi quelli di servizi cloud personali per gli utenti. Obiettivi del canonico di una piattaforma gratuita unificata in Ubuntu, dal telefono nel cloud, con una famiglia di interfacce coerente per il telefono, tablet, TV e desktop, rendono Ubuntu la prima per istituti diversi dal provider di servizi cloud pubblico produttori di largo e preferito tra singoli tecnici.

Con gli sviluppatori e per ingegneria centri tutto il mondo, Canonical posizione ideale per collaborare con hardware Maker, provider di contenuti e agli sviluppatori di software per visualizzare le soluzioni Ubuntu sul mercato, da PC a server e dispositivi portatili.

