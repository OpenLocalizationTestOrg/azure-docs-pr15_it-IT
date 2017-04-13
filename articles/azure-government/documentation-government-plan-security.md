<properties
    pageTitle="Servizi pubblici Azure | Microsoft Azure"
    description="Fornisce una panoramica sui servizi disponibili per la pubblica amministrazione Azure"
    services="Azure-Government"
    cloud="gov"
    documentationCenter=""
    authors="zakramer"
    manager="liki"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/18/2016"
    ms.author="ryansoc" />


#  <a name="security"></a>Sicurezza

##  <a name="principles-for-securing-customer-data-in-azure-government"></a>Principi per la protezione dei dati dei clienti in Azure per la pubblica amministrazione

Azure per la pubblica amministrazione offre una gamma di funzionalità e i servizi che è possibile utilizzare per creare soluzioni basate su cloud per soddisfare le esigenze di dati regolata controllati. Una soluzione clienti conforme è semplicemente l'implementazione effettiva delle funzionalità di Azure Government della casella, unitamente a una procedura di protezione dei dati a tinta unita.

Quando si ospita una soluzione per enti pubblici Azure, Microsoft gestisce molti di questi requisiti a livello di infrastruttura cloud.

Il diagramma seguente illustra il modello di difesa in Azure. Ad esempio Microsoft offre dell'infrastruttura cloud base DDOS, insieme a funzionalità cliente, ad esempio apparecchiature di sicurezza per applicazione specifica per un cliente che deve DDOS.

![testo alternativo](./media/azure-government-Defenseindepth.png)

Questa pagina illustra i principi di base per la protezione dei servizi e applicazioni, le indicazioni e procedure consigliate su come applicare questi principi; in altre parole, come i clienti devono utilizzare smart del governo Azure per soddisfare gli obblighi e le responsabilità necessari per una soluzione che gestisce informazioni ITAR.

 Principi generali per la protezione dei dati dei clienti sono:

- Protezione dei dati utilizzando la crittografia
- Gestione delle informazioni riservate
- Isolamento per limitare l'accesso ai dati

###  <a name="protecting-customer-data-using-encryption"></a>Protezione dei dati dei clienti utilizzando la crittografia

Riduzione dei rischi e riunioni obblighi normativi determinano l'ingrandimento lo stato attivo e priorità crittografia dei dati. Utilizzare un'implementazione di crittografia efficaci per migliorare misure di sicurezza di rete e l'applicazione corrente, ovvero e ridurre il rischio globale dell'ambiente cloud.

#### <a name="encryption-at-rest"></a>Crittografia inattivi
La crittografia dei dati inattivi riguarda la protezione del testo dei clienti contenuto nella spazio su disco. Esistono diversi modi che questo problema può verificarsi:

#### <a name="storage-service-encryption"></a>Crittografia del servizio di archiviazione

La crittografia del servizio di archiviazione Azure viene abilitata a livello di account di archiviazione, risultante in blocco BLOB e BLOB di pagine da crittografare automaticamente quando scritta allo spazio di archiviazione Azure. Quando si leggono dati dallo spazio di archiviazione di Azure, verrà decrittografare dal servizio di archiviazione prima di essere restituito. Consente di proteggere i dati senza dover modificare o aggiungere codice a tutte le applicazioni.

#### <a name="client-side-encryption"></a>Crittografia lato client
La crittografia lato client è integrata nel linguaggio e le librerie client lo spazio di archiviazione di .NET, che possono essere utilizzate Azure chiave archivio API, effettuare questa operazione semplice implementare. Utilizzare Azure chiave archivio per ottenere accesso alle informazioni riservate in Azure chiave archivio per le persone specifiche con Azure Active Directory.

#### <a name="encryption-in-transit"></a>Crittografia durante il transito

La crittografia di base disponibile per la connettività per la pubblica amministrazione Azure supporta livello sicurezza protocollo TLS (Transport) 1.2 e certificati x. 509. Federal Information Processing Standard (FIPS) 140-2 livello 1 algoritmi utilizzati anche per le connessioni di rete infrastruttura tra i Data Center per enti pubblici Azure.  Windows Server 2012 R2 e Windows 8-plus macchine virtuali e nelle condivisioni File Azure possono utilizzare piccole e medie imprese 3.0 per la crittografia tra la macchina virtuale e la condivisione di file. Utilizzare la crittografia lato Client per crittografare i dati prima viene trasferita in archiviazione nelle applicazioni client e per decrittografare i dati dopo tale voce viene trasferita fuori dello spazio di archiviazione.

#### <a name="best-practices-for-encryption"></a>Procedure consigliate per la crittografia

- Macchine virtuali IaaS: Utilizzare la crittografia di Azure disco. Attivare la crittografia del servizio di archiviazione per crittografare i file disco rigido virtuale utilizzato per eseguire il backup tali dischi in archiviazione Azure, ma questo solo appena scritti i dati vengono crittografati. Ciò significa che, se si crea una macchina virtuale e quindi attiva la crittografia del servizio di archiviazione per l'account di archiviazione che contiene il file disco rigido virtuale, solo le modifiche verranno crittografate, non il file disco rigido virtuale originale.
- La crittografia lato client: Questo è il metodo più sicuro per la crittografia dei dati, perché crittografa prima di transito e crittografa i dati inattivi. È necessario aggiungere codice alle applicazioni mediante lo spazio di archiviazione, si consiglia di effettuare. In questi casi, è possibile utilizzare HTTPs per i dati in transito e la crittografia del servizio di archiviazione per crittografare i dati inattivi. La crittografia lato client comporta anche un carico maggiore sul client, è necessario conto per i piani di scalabilità, soprattutto se si ha la crittografia e il trasferimento di una grande quantità di dati.

###  <a name="protecting-customer-data-by-managing-secrets"></a>Protezione dei dati dei clienti tramite la gestione delle informazioni riservate

Gestione delle chiavi sicura è fondamentale per proteggere i dati nel cloud. I clienti consigliabile cercare di semplificare la gestione delle chiavi e mantenere il controllo delle chiavi utilizzate da servizi e applicazioni cloud per crittografare i dati.

#### <a name="best-practices-for-managing-secrets"></a>Procedure consigliate per la gestione delle informazioni riservate

- Consente di ridurre al minimo i rischi di informazioni riservate vengano esposti tramite file di configurazione hardcoded, script, o nel codice sorgente chiave archivio. Archivio chiave Azure crittografare chiavi (ad esempio, le chiavi di crittografia per la crittografia disco Azure) e informazioni riservate (ad esempio le password), per memorizzare le FIPS 140-2 livello 2 convalidati moduli di protezione hardware (HSM). Per garanzia aggiunta, è possibile importare o generare chiavi in queste HSM.
- Modelli e il codice dell'applicazione devono contenere solo riferimenti URI per le informazioni riservate (ovvero che l'effettivi informazioni riservate non sono nel codice, configurazione o codice sorgente archivi). In questo modo attacchi di phishing chiave in pronti contro termine interne o esterne, ad esempio raccolto Bot in GitHub.
- Utilizzare i controlli RBAC sicuri all'interno di archivio di chiave. Se un operatore attendibile lascia la società o trasferimenti a un nuovo gruppo all'interno della società, deve essere impediti possano accedere le informazioni riservate.

Per ulteriori informazioni <a href="https://azure.microsoft.com/documentation/services/key-vault">documentazione pubblica Azure chiave archivio.</a>

###  <a name="isolation-to-restrict-data-access"></a>Isolamento per limitare l'accesso ai dati

Isolamento è basato sui utilizzando limiti, segmentazione e contenitori per limitare l'accesso ai dati a solo gli utenti autorizzati, servizi e applicazioni. Ad esempio, la separazione tra tenant è un meccanismo di protezione essenziali per le piattaforme multi-tenant cloud, ad esempio Microsoft Azure. Isolamento logico consente di impedire un tenant di interferire con le operazioni di qualsiasi altro tenant.

#### <a name="environment-isolation"></a>Isolamento dell'ambiente
L'ambiente per enti pubblici Azure è un'istanza di fisica separata dal resto della rete Microsoft. Grazie a una serie di controlli fisici e logici che includono le seguenti:

- Proteggere gli ostacoli fisici utilizzando fotocamere e dispositivi biometrici.
- Uso delle credenziali specifiche e autenticazione a più fattori da personale Microsoft che richiedono l'accesso logico all'ambiente di produzione.
- Tutta l'infrastruttura del servizio per la pubblica amministrazione Azure si trova negli Stati Uniti.

#### <a name="per-customer-isolation"></a>Per ogni cliente isolamento
Controllo dell'accesso rete implementa Azure e separazione tramite isolamento VLAN, ACL, caricare bilanciamento del carico e filtri

I clienti possono inoltre isolare le risorse in più abbonamenti, gruppi di risorse, reti virtuali e subnet.

## <a name="screening"></a>Analisi

Recentemente annunciata FedRAMP alta e il riconoscimento di reparto di difesa impatto livello 4. Questo ha generato la barra di sicurezza e conformità tra l'ambiente per enti pubblici Azure.

È ora stiamo applicazione tutto il nostro operatori nazionali agenzia controllare con giurisdizione e carta di credito (NACLC) come definito nella sezione 5.6.2.2 del DoD Cloud Computing sicurezza requisiti Guida (SRG):

>[AZURE.NOTE] Indagini sfondo minimi necessari per il personale CSP l'accesso al livello 4 e 5 le informazioni in base a un "non critica dipendenti" (ad esempio, di DoD ADP-2) sono un nazionali agenzia consultare giurisdizione e carta di credito (NACLC) (per i consulenti "non critica sensibili") oppure un protocollo moderato rischio sfondo indagini (MBI) per una designazione posizione "rischio moderato".

La tabella seguente riepiloga l'analisi corrente per gli operatori di Azure per enti pubblici:

Azure mondiglia Gov e controlli in background | Descrizione|
---|---|
Cittadinanza negli Stati Uniti |Verifica della cittadinanza negli Stati Uniti.
Controllo in background di Microsoft cloud (ogni due anni)|Numero di previdenza sociale della ricerca, controllo penale cronologia, elenco Office di controllo attività esterna (OFAC), elenco Bureau di settore e sicurezza (BIS), elenco Office di difesa Trade controlli beneficio persone.
Controllo Agenzia nazionale con giurisdizione e carta di credito (NACLC) (ogni cinque anni) | Aggiunge l'ID digitale sfondo controllo su database FBI. Per ulteriori informazioni, visitare il<a href="https://www.opm.gov/investigations/background-investigations/federal-investigations-notices/1997/fin97-02/"> Sito di gestione del personale di Office</a>. | 
<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/CJIS">Penali giustizia Information Services (CJIS)</a> | CJIS stato, locale e government FBI controllo quali ID digitale processi record e convalida penale cronologie sul personale operativa a cui era possibile accedere ai dati di informazioni (CJI) giustizia penale critiche.  Ogni stato indica le proprie sfondo controllo e della successiva approvazione di tutti i dipendenti con accesso a CJI.|

Per Azure personale, applicano i seguenti principi di accesso:

- Vengono definiti in modo chiaro compiti, con responsabilità distinta per la richiesta, approvare e distribuire le modifiche.
- Accesso avviene tramite le interfacce definite con funzionalità specifiche.
- Accesso just in time (Just) e viene concesso solo in base al problema o per un evento di manutenzione specifico e sempre per una durata limitata.
- L'accesso è basato su regole, con definiti ruoli assegnati solo le autorizzazioni necessarie per la risoluzione dei problemi.

Analisi standard include la convalida di cittadinanza negli Stati Uniti del personale del supporto tecnico Microsoft per accedere ai sistemi ospitato per enti pubblici Azure. Personale di supporto che hanno l'esigenza di trasferire dati usare le funzionalità sicure all'interno di Azure per enti pubblici. Trasferimento di protezione dei dati richiede un insieme separato credenziali di autenticazione per accedere. Ad esempio, per accedere ai metadati di sistema, personale utilizzano strumenti specifici gestione interna basata sul web, API di sola lettura e JIT elevazione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni e aggiornamenti abbonarsi alla <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog di Microsoft Azure per enti pubblici.</a>
