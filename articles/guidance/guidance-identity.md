<properties
   pageTitle="Gestione delle identità in Azure | Microsoft Azure"
   description="Spiega che vengono confrontati i vari metodi disponibili per la gestione delle identità nei sistemi ibride che includono il bordo in locale/cloud con Azure."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/26/2016"
   ms.author="telmosampaio"/>
   
# <a name="managing-identity-in-azure"></a>Gestione delle identità in Azure

Nella maggior parte dei sistemi aziendali basati su Windows, utilizzare Active Directory (AD) per fornire servizi di gestione di identità alle applicazioni. Active Directory funziona bene in un ambiente locale, ma quando si estende dell'infrastruttura di rete nel cloud, è necessario alcune importanti decisioni relative sulla gestione delle identità. È necessario espandere i domini locale per incorporare macchine virtuali nel cloud? È necessario creare nuovi domini nel cloud e in caso affermativo, come? Implementare il proprio foresta nel cloud o che si desidera utilizzare di Azure Active Directory (AAD)?

In questo articolo sono illustrate alcune opzioni comuni per soddisfare le posto da questo scenario, e consente di che determinare quale soluzione si adatta soddisfano le proprie esigenze in base alle esigenze.

## <a name="using-azure-active-directory"></a>Utilizzo di Azure Active Directory

È possibile utilizzare AAD per creare un dominio di Active Directory nel cloud e collegarlo a un locale nel dominio di Active Directory. AAD consente di configurare il single sign-on (SSO) per gli utenti che eseguono applicazioni accessibili tramite il cloud.

[! [0]][0]

AAD è un modo semplice per implementare un dominio di protezione nel cloud. Viene utilizzato da molte applicazioni di Microsoft, ad esempio Microsoft Office 365. 

Vantaggi dell'utilizzo AAD:

- Non è necessario mantenere un'infrastruttura di Active Directory nel cloud. AAD è completamente gestite e gestiti da Microsoft.

- AAD offre le stesse informazioni di identità sono disponibile in locale.

- Autenticazione può verificarsi in Azure, riducendo la necessità di applicazioni esterne e agli utenti di contattare il dominio locale.

Aspetti da considerare quando si utilizza AAD:

- Servizi di identità sono limitati a utenti e gruppi. Non è possibile autenticare l'account di servizio e dal computer.

- È necessario configurare la connettività con il proprio dominio locale per mantenere la directory AAD sincronizzata. 

- Sei responsabile per la pubblicazione di applicazioni che gli utenti possono accedere nel cloud tramite AAD.

Per informazioni dettagliate, vedere [Implementazione di Azure Active Directory][implementing-aad].

## <a name="using-active-directory-in-the-cloud-joined-to-an-on-premises-forest"></a>Utilizzo di Active Directory nel cloud fa parte di un insieme di strutture locale

È possibile ospitare servizi Active Directory (AD DS) locale, ma in uno scenario ibrido dove si trovano gli elementi di un'applicazione in Azure può essere più efficiente replicare questa funzionalità e archivio di Active Directory nel cloud. Questo approccio può aiutare a ridurre la latenza causata inviando l'autenticazione e le richieste di autorizzazione locale dal cloud di nuovo in Active Directory in esecuzione in locale. 

[! [1]][1]

Questo approccio è necessario creare il proprio dominio nel cloud e partecipare alla foresta locale. Creare macchine virtuali per ospitare i servizi di dominio Active Directory.

Vantaggi dell'utilizzo di un dominio distinto nel cloud:

- Consente di eseguire l'autenticazione utente, il servizio e computer account locale e nel cloud.

- Consente di accedere alle stesse informazioni di identità che sono disponibile in locale.

- Non è necessario gestire una foresta separata di Active Directory; il dominio nel cloud a cui può appartenere alla foresta locale.

- È possibile applicare criteri di gruppo definiti dagli oggetti oggetto Criteri di gruppo locale per il dominio nel cloud.

Considerazioni per l'utilizzo di un dominio distinto nel cloud:

- È necessario creare e gestire il proprio server Active Directory e il dominio nel cloud.

- È possibile che alcuni latenza di sincronizzazione tra il server di dominio nel cloud e il server locale.

Per informazioni su come configurare questa architettura, vedere [Estensione Active Directory Directory Services (somma) in Azure][extending-adds].

## <a name="using-active-directory-with-a-separate-forest"></a>Utilizzo di Active Directory con una foresta separata

Un'organizzazione che esegue Active Directory (AD) locale potrebbe essere un insieme di strutture che comprende molti domini diversi. È possibile usare i domini per fornire isolamento tra le aree funzionali che devono essere mantenuti separate, probabilmente per motivi di sicurezza, ma è possibile condividere informazioni tra i domini per stabilire relazioni di trust.

[! [2]][2]

Le organizzazioni che utilizzano domini separati possono trarre vantaggio Azure spostando una o più di questi domini in una foresta separata nel cloud. In alternativa, un'organizzazione potrebbe essere necessario mantenere tutte le risorse cloud in modo logico distinte dai tali tenuti locale e memorizzare informazioni sulle risorse cloud nella propria directory come parte di un insieme di strutture anche contenute nel cloud.

Vantaggi dell'utilizzo di un insieme di strutture separato nel cloud:

- È possibile implementare identità locale e separare identità solo Azure.

- Non è necessario replicare da locale in strutture di Active Directory in Azure, ridurre gli effetti della latenza di rete.

Considerazioni:

- Autenticazione per le identità locale nel cloud esegue di rete aggiuntive *hop* in locale nel server Active Directory.

- È necessario implementare il proprio server Active Directory e foresta nel cloud e stabilire relazioni di trust appropriato tra insiemi di strutture.

Documento di [creando una foresta di risorse di servizi di Directory Active Directory (somma) in Azure] [ adds-forest-in-azure] viene descritto come implementare questo approccio in modo più dettagliato.

## <a name="using-active-directory-federation-services-adfs-with-azure"></a>Utilizzo di Active Directory Federation Services (ADFS) con Azure

ADFS può essere eseguito in locale, ma in uno scenario ibrido dove si trovano le applicazioni di Azure può essere più efficiente per implementare questa funzionalità nel cloud, come illustrato di seguito.

[! [3]][3]

Questa architettura è particolarmente utile per:

- Soluzioni che utilizzano autorizzazioni federati per esporre applicazioni web in modo che le organizzazioni partner.

- Sistemi che supportano l'accesso dal web browser in esecuzione all'esterno del firewall dell'organizzazione.

- Sistemi che consentono agli utenti di accedere alle applicazioni web mediante la connessione da dispositivi esterni autorizzati ad esempio computer remoti, i blocchi appunti e altri dispositivi mobili. 

Vantaggi dell'utilizzo di ADFS con Azure:

- È possibile sfruttare le applicazioni attestazioni.

- Offre la possibilità di considerare attendibile partner esterni per l'autenticazione.

- Fornisce la compatibilità con numerosi protocolli di autenticazione.

Considerazioni per l'utilizzo di ADFS con Azure:

- È possibile implementare funzionalità somma, ADFS e Proxy di applicazione Web ADFS server nel cloud richiede l'esecuzione.

- Questa architettura può essere complessa da configurare.

Per informazioni dettagliate, vedere [Implementazione di Active Directory Federation Services (ADFS) in Azure][adfs-in-azure].

## <a name="next-steps"></a>Passaggi successivi

Le risorse seguenti illustrano come implementare architetture descritte in questo articolo.

- [Implementazione di Azure Active Directory][implementing-aad]
- [Estensione servizi Directory Active Directory (somma) per Azure][extending-adds]
- [Creazione di un insieme di strutture di risorse di servizi di Directory Active Directory (somma) in Azure][adds-forest-in-azure]
- [Implementazione di Active Directory Federation Services (ADFS) in Azure][adfs-in-azure]

<!-- Links -->
[0]: ./media/guidance-identity/figure1.png "Architettura di identità del cloud usando Azure Active Directory"
[1]: ./media/guidance-identity/figure2.png "Architettura della rete ibrido con Active Directory protetta"
[2]: ./media/guidance-identity/figure3.png "Architettura della rete ibrido con domini Active Directory e strutture distinti protetta"
[3]: ./media/guidance-identity/figure4.png "Architettura della rete ibrido con ADFS protetta"
[implementing-aad]: ./guidance-identity-aad.md
[extending-adds]: ./guidance-identity-adds-extend-domain.md
[adds-forest-in-azure]: ./guidance-identity-adds-resource-forest.md
[adfs-in-azure]: ./guidance-identity-adfs.md