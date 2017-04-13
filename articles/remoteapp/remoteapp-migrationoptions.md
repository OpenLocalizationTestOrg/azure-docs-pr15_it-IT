
<properties 
    pageTitle="Opzioni per la migrazione da Azure RemoteApp | Microsoft Azure" 
    description="Informazioni sulle opzioni per la migrazione fuori RemoteApp di Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/06/2016" 
    ms.author="elizapo" />

# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Opzioni per la migrazione fuori RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Se si ha smesso di utilizzare RemoteApp di Azure a causa di [annuncio pensionistico](https://go.microsoft.com/fwlink/?linkid=821148) o aver completato la valutazione, è necessario eseguire la migrazione da Azure RemoteApp a un altro servizio di app. Esistono due diversi approcci per la migrazione: un autogestito (spesso denominate infrastruttura come servizio [IaaS]) o distribuzione Software come servizio [PaaS/SaaS] o una completamente (spesso denominata piattaforma gestita come servizio) che offre. 

Modalità self-service IaaS è una distribuzione fai da te gestita, gestita e proprietà dell'utente, distribuiti direttamente su sistemi fisici o macchine (). Al termine, completamente gestito PaaS/offerta SaaS con il è analoga alla RemoteApp di Azure - un partner fornisce un livello di servizio nella parte superiore di una soluzione di gestione remota che gestisce operative e la manutenzione, mentre l'utente, come il cliente, eseguire la gestione di alcune immagini e app.

Per ulteriori informazioni, inclusi esempi di diverse opzioni di hosting, vedere informazioni su.    

## <a name="self-managed-iaas-solutions"></a>Autogestito soluzioni (IaaS)

### <a name="rds-on-iaas"></a>**Licenze in IaaS** 
È possibile distribuire una nativa distribuzione Servizi Desktop remoto (in Windows Server) basato su sessione utilizzando RemoteApp o desktop locale o in un ambiente host (come in macchine virtuali di Azure). Licenze nelle distribuzioni IaaS sono ideali per utenti che hanno familiari con i clienti e che dispongono di competenze tecniche esistenti con i servizi desktop remoto. 

> [AZURE.NOTE]
> È necessario contratti multilicenza con Software Assurance (SA) per licenze di accesso client di servizi desktop remoto per utilizzare questa opzione di distribuzione.

Distribuzione di licenze in macchine virtuali di Azure è più semplice che mai quando si utilizza la distribuzione e modificare i modelli (vedi una [Panoramica](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) e quindi [passare ottenerli](https://aka.ms/rdautomation)). È possibile ottenere le stesse capacità di ridimensionamento flessibile con Azure classico modello dedicata (non risorse modello di risorse Azure) all'interno di Azure RemoteApp tramite [script scala automatica](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), anche se sono disponibili ulteriori personalizzazioni e configurazioni. Quando si distribuisce Servizi Desktop remoto in macchine virtuali di Azure, supporto viene fornito tramite [Azure supporto](https://azure.microsoft.com/support/plans/), lo stessa supportare i professionisti supportate con RemoteApp di Azure. È possibile ottenere stime dei costi in base all'esistente utilizzo contattando [Il supporto di Azure](https://azure.microsoft.com/support/plans/)o è possibile eseguire calcoli se stessi con una breve per essere rilasciati calcolatore dei costi.  Inoltre, con macchine virtuali di serie N (attualmente in privato preview) è possibile aggiungere vGPU - ascoltare altre informazioni sull'aggiunta di vGPU e su come sfruttare i [miglioramenti introdotti desktop in Windows Server 2016](https://myignite.microsoft.com/videos/2794) la sessione di Ignite.   

Sono disponibili altre guide passo dopo passo distribuzione per [Windows Server 2012 R2](http://aka.ms/rdsonazure) e [Windows Server 2016](http://aka.ms/rdsonazure2016) agevolare la distribuzione. Visitare il [blog di Desktop remoto](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services) per le ultime novità.
 
### <a name="citrix-on-iaas"></a>**Citrix su IaaS** 
Un Citrix nativo distribuzione di informazioni basato sulla sessione o XenDesktop può essere distribuito in locale o in un ambiente ospitato (ad esempio in macchine virtuali di Azure). 

Consultare la Guida dettagliata alla distribuzione, [Citrix XA 7.6 su Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx), per altre informazioni. Per ulteriori informazioni su [Citrix su Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), tra cui una calcolatrice prezzo. È inoltre disponibile un [contattare Citrix](http://citrix.com/English/contact/index.asp) per esaminare le opzioni disponibili con.

## <a name="fully-managed-paassaas-offerings"></a>Completamente gestite offerte (PaaS/SaaS)

### <a name="citrix-cloud"></a>**Citrix Cloud** 
[Soluzione cloud esistente Citrix](https://www.citrix.com/products/citrix-cloud/), identiche strutturalmente esprimere Citrix informazioni. Citrix offre una [promozione 50%](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/) per gli attuali clienti di Azure RemoteApp. 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix informazioni Express (in anteprima tecnica)**
[Eseguire la registrazione per loro l'anteprima tecnica](http://now.citrix.com/remoteapp)e guardare la [sessione Ignite](https://myignite.microsoft.com/videos/2792) (dal minuto 20:30). Informazioni Express è strutturalmente identico Citrix cloud ad eccezione del fatto che includa Gestione semplificata dell'interfaccia utente e altre caratteristiche e funzionalità che sono simili a RemoteApp di Azure. 

Ulteriori informazioni su [Citrix informazioni Express](http://now.citrix.com/remoteapp).   

### <a name="citrix-service-provider-program"></a>**Programma di Provider del servizio Citrix** 
Il programma di Provider del servizio Citrix semplifica l'individuazione di provider di servizi offrire la semplicità di virtuale cloud computing per SMB, offrendo loro i servizi che vogliono in un modello semplice e in. Provider di servizi Citrix crescita SPLA Microsoft ed espandere loro investimenti piattaforma desktop con qualsiasi dispositivo, accesso da postazioni remote più ampio supporto di applicazioni, esperienze, maggiore sicurezza e scalabilità. A sua volta, il provider di servizi Citrix attirare più sottoscrittori, aumentare la soddisfazione dei clienti e ridurre i costi operativi. [Altre informazioni](http://www.citrix.com/products/service-providers.html) o [trovare un partner](https://www.citrix.com/buy/partnerlocator.html).

### <a name="microsoft-hosted-service-provider"></a>**Microsoft ospitato Provider di servizi** 
Partner di hosting offrono in genere completamente gestita ospitato desktop di Windows e servizio dell'applicazione, che può includere la gestione risorse Azure, sistemi operativi, applicazioni e supporto tecnico tramite il partner contratti con Microsoft e altri fornitori di software insieme da un contratto di licenza Provider del servizio per consentire rivendita di licenza accesso sottoscrittore (SAL) di licenza. Di seguito vengono fornite informazioni dettagliate e informazioni di contatto per alcuni hoster specializzate nell'assistenza clienti con la migrazione di Azure RemoteApp. Controllare [l'elenco attuale ospitato provider di servizi](http://aka.ms/rdsonazurecertified) che sono state completate licenze in IaaS valutazione e percorso di apprendimento.  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en) specializzata in indipendenti transizione nel cloud e ISV' per ottimizzare le impostazioni di cloud corrente. ASPEX offre una vasta gamma di servizi gestiti, attrezzi e servizi di consulenza.  

Posizione principale: Anversa, Belgio

Area di operazione: occidentale

Stato di partner: [argento](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Provider di servizi Cloud Microsoft: Sì

Offrire soluzioni di RemoteApp e Desktop sessione: Sì, entrambi

Soluzioni di migrazione RemoteApp Azure: Sì, [altre informazioni](https://www.aspex.be/en/azure-remote-apps)

**Contatto:**

- Telefono: +3232202198
- Posta elettronica:[info@aspex.be](mailto:info@aspex.be)
- Web: [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink (il nome della piattaforma: MyCloudIT)**
[MyCloudIT](http://www.mycloudit.com) è una piattaforma di automazione per le aziende IT semplificare, ottimizzare e ridurre la migrazione e distribuzione di desktop remoto, applicazioni remote e infrastruttura di Microsoft Azure Cloud. 

La piattaforma MyCloudIT consente di ridurre i tempi di distribuzione del 95%, costo del 30% di Azure e sposta infrastruttura IT intera dei propri clienti nel cloud nel giro di pochi sequenze di tasti. Partner ora possono gestire i clienti da un dashboard globale, gli utenti finali di servizio in tutto il mondo proficui ed espansione ricavi senza aggiungere un ulteriore sovraccarico o vasta formazione Azure.  

Posizione principale: Dallas, TX, USA

Regione operativa: in tutto il mondo

Stato di partner: [oro](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Provider di servizi Cloud Microsoft: Sì

Offrire soluzioni di RemoteApp e Desktop sessione: Sì, entrambi

Soluzioni di migrazione RemoteApp Azure: Sì, [altre informazioni](https://mycloudit.com/remote-app-microsoft/)

**Contatto:**
- Maurizio Garoutte, Vicepresidente dello sviluppo aziendale

   Telefono: 972-218-0741

   Posta elettronica:[brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com) specializzata in grado di fornire hosting soluzioni desktop, l'esecuzione di completo desktop e applicazioni ISV esperienze basate sulla tecnologia Microsoft a un client globale base da Azure e i propri Data Center.

Posizione principale: Londra, Regno Unito; Singapore; Houston, TX

Regione operativa: in tutto il mondo

Stato di partner: oro

Provider di servizi Cloud Microsoft: Sì

Offrire soluzioni di RemoteApp e Desktop sessione: Sì, entrambi

Soluzioni di migrazione RemoteApp Azure: Sì, [altre informazioni](http://www.acuutech.com/ara-migration/)

**Contatto:**

- Regno Unito:

  5/6 York Zenzero, Guida di orientamento alla Langston

  Loughton, Essex IG10 3TQ
  
  Numero di telefono: + 44 (0) 20 8502 2155
 
- Singapore:

  Via Cecil 100, #09-02 
  
  Il globo, Singapore 069532
  
  Numero di telefono: + 65 6709 4933
 
- America del Nord: 

  3601 s Sandman St.
  
  200 famiglia di prodotti, Houston, TX 77098
  
  Telefono: +1 713 691 0800

## <a name="need-more-help"></a>Serve ulteriore assistenza?
Ancora necessario Guida scegliendo o altre domande? Utilizzare uno dei metodi seguenti per ottenere assistenza. 

1.  Inviare tramite posta elettronica al [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2.  Contattare il [supporto di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Iniziare aprendo un [Azure supportano maiuscole/minuscole](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3.  Telefonare. [Trova un numero di vendite locale](https://azure.microsoft.com/overview/sales-number/).
