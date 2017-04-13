<properties 
    pageTitle="Configurare una connessione da un indicizzatore di ricerca di Azure a SQL Server in un computer virtuale Azure | Microsoft Azure | Indicizzatori" 
    description="Abilitazione delle connessioni crittografate e configurare il firewall per consentire le connessioni a SQL Server in una Azure macchine () da un indicizzatore nella ricerca Azure." 
    services="search" 
    documentationCenter="" 
    authors="jack4it" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="09/26/2016" 
    ms.author="jackma"/>

# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurare una connessione da un indicizzatore di ricerca di Azure a SQL Server in macchine Virtuali un Azure

Come indicato nella [Connessione di Database SQL Azure alla ricerca di Azure con indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions), la creazione di indicizzatori rispetto a **SQL Server in macchine virtuali di Azure** (o **Macchine virtuali di SQL Azure** in breve) è supportata dal servizio di ricerca di Azure, ma sono disponibili alcuni prerequisiti correlati alla sicurezza a prendersi cura della prima. 

**Attività durata:** Informazioni su 30 minuti, presupponendo che è già installato un certificato nella macchina virtuale.

## <a name="enable-encrypted-connections"></a>Abilitazione delle connessioni crittografate

Ricerca Azure richiede un canale crittografato per tutte le richieste di indicizzatore tramite una connessione internet pubblica. In questa sezione sono elencati i passaggi per garantire il corretto funzionamento.

1. Controllare le proprietà del certificato per verificare che il nome del soggetto è il nome di dominio completo (FQDN) di macchina virtuale Azure. È possibile usare uno strumento come CertUtils o snap-in certificati per visualizzare le proprietà. È possibile ottenere il nome di dominio completo del e il servizio macchine Virtuali Essentials nella sezione nel campo **indirizzo IP pubblico/DNS nome etichetta** , nel [portale di Azure](https://portal.azure.com/).

    - Per le macchine virtuali create utilizzando il modello più recente di **Manager delle risorse** , il nome di dominio completo è formattato come `<your-VM-name>.<region>.cloudapp.azure.com`. 

    - Per le versioni precedenti macchine virtuali di creata come una **classica** macchine Virtuali, il FQDN è formattato come `<your-cloud-service-name.cloudapp.net>`. 

2. Configurare SQL Server per utilizzare il certificato utilizzando l'Editor del Registro di sistema (regedit). 

    Sebbene Gestione configurazione SQL Server viene spesso usato per questa operazione, è possibile utilizzare per questo scenario. Non non è possibile trovare il certificato importato il FQDN di macchine Virtuali in Azure mancata corrispondenza tra il FQDN definito da macchine Virtuali (identifica il dominio come computer locale o il dominio di rete in cui è connesso). Quando i nomi non corrispondono, utilizzare regedit per specificare il certificato.

    - In regedit, passare a questa chiave del Registro di sistema: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
    Il `[MSSQL13.MSSQLSERVER]` parte varia in base alla versione e istanza nome. 

    - Impostare il valore della chiave del **certificato** di **identificazione personale** del certificato SSL importati nella macchina virtuale.

    Esistono diversi modi per ottenere l'identificazione personale, alcuni più efficace di altri utenti. Se lo si copia in dallo snap-in **certificati** in MMC, otterranno probabilmente un invisibili zeri carattere [come descritto in questo articolo del supporto tecnico](https://support.microsoft.com/kb/2023869/), che genera un errore quando si tenta di una connessione. Diverse soluzioni alternative sono disponibili per risolvere il problema. Il metodo più semplice consiste su e quindi digitare di nuovo il primo carattere di identificazione personale per rimuovere i caratteri iniziali nel campo valore chiave regedit. In alternativa, è possibile utilizzare un altro strumento per copiare l'identificazione personale.

3. Concedere le autorizzazioni per l'account di servizio. 

    Accertarsi che l'account di servizio SQL Server è consentito delle autorizzazioni appropriate per la chiave privata del certificato SSL. Se si ignorare questo passaggio, SQL Server non verrà avviato. È possibile utilizzare snap-in **certificati** o **CertUtils** per questa attività.

4. Riavviare il servizio SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurare la connettività di SQL Server nella macchina virtuale

Dopo aver configurato la connessione crittografata richiesta dal servizio di ricerca di Azure, esistono ulteriori passaggi di configurazione di SQL Server in macchine virtuali di Azure. Se già fatto, il passaggio successivo consiste nel completare la configurazione utilizzando uno degli articoli seguenti:

- Per una **Gestione risorse** macchine Virtuali, vedere [connettersi a un SQL Server virtuale computer Azure Gestione risorse](../virtual-machines/virtual-machines-windows-sql-connect.md). 

- Per una **classica** macchine Virtuali, vedere [connettersi a un SQL Server virtuale computer Azure classica](../virtual-machines/virtual-machines-windows-classic-sql-connect.md).

In particolare, consultare la sezione in ogni articolo per "la connessione tramite internet".

## <a name="configure-the-network-security-group-nsg"></a>Configurare il gruppo di sicurezza di rete (NSG)

Non è insolito configurare NSG ed endpoint Azure corrispondente o controllo elenco di accesso per rendere accessibile alle altre parti di una macchina virtuale Azure. È molto probabile che si esegue questa operazione in precedenza per consentire di logica di applicazione connettere la macchina virtuale Azure SQL. È non è diverso per una connessione di ricerca di Azure per la macchina virtuale Azure SQL. 

I collegamenti seguenti istruzioni sulla configurazione di NSG per distribuzioni di macchine Virtuali. Utilizzare queste istruzioni per ACL un endpoint di Azure ricerca in base all'indirizzo IP.

> [AZURE.NOTE] Per informazioni, vedere [che cos'è un gruppo di sicurezza della rete?](../virtual-network/virtual-networks-nsg.md)

- Per una **Gestione risorse** macchine Virtuali, vedere [come creare NSGs per le distribuzioni ARM](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

- Per una **classica** macchine Virtuali, vedere [come creare NSGs per le distribuzioni classica](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Indirizzi IP può comportare alcuni problemi che sono facilmente superare se si conoscono il problema e potenziali soluzioni alternative. Le ultime sezioni contengono suggerimenti per la gestione dei problemi relativi agli indirizzi IP nell'elenco ACL.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Limitare l'accesso all'indirizzo IP del servizio di ricerca

È consigliabile limitare l'accesso all'indirizzo IP del servizio di ricerca nell'elenco ACL invece rendere nelle macchine virtuali Azure SQL aperta per le richieste di connessione. È possibile trovare l'indirizzo IP ping il nome di dominio completo (ad esempio `<your-search-service-name>.search.windows.net`) del servizio di ricerca.

#### <a name="managing-ip-address-fluctuations"></a>Gestione fluttuazioni indirizzo IP

Se il servizio di ricerca contiene solo un'unità di ricerca (una replica e una partizione), l'indirizzo IP cambierà durante l'avvio di servizio routine, invalidando un ACL esistente con indirizzo IP del servizio di ricerca.

Per evitare l'errore di connettività successive consiste nell'utilizzare più di una replica e una partizione di ricerca di Azure. In questo modo aumenta il costo, ma che anche risolve il problema con l'indirizzo IP. Risultati di ricerca di Azure, ma gli indirizzi IP non vengono modificati quando si dispone di più unità di ricerca.

Un altro approccio è per consentire la connessione all'esito negativo e quindi riconfigurare i NSG gli elenchi di controllo. In Media, è probabile che gli indirizzi IP per modificare ogni due settimane. Per i clienti che eseguire l'indicizzazione controllati con frequenza irregolare, questo approccio potrebbe essere valido.

Un terzo approccio valido (ma non è particolarmente sicuro) è per specificare l'intervallo di indirizzi IP dell'area di Azure in cui viene eseguito il provisioning del servizio di ricerca. L'elenco di intervalli di indirizzi IP da cui indirizzi IP vengono assegnati alle risorse Azure viene pubblicato in [intervalli di indirizzi IP di Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Includa gli indirizzi IP al portale di ricerca di Azure

Se si utilizza il portale di Azure per creare un indicizzatore, operatori logici portale di ricerca di Azure necessario anche l'accesso per la macchina virtuale Azure SQL durante l'ora di creazione. Indirizzi IP portale ricerca Azure possano essere trovati tramite il ping `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Passaggi successivi

Con configurazione dall'area di lavoro, è ora possibile specificare SQL Server in macchine Virtuali di Azure come origine dati per un indicizzatore di ricerca di Azure. Per ulteriori informazioni, vedere [Connessione di Database SQL Azure alla ricerca di Azure usando indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) .
