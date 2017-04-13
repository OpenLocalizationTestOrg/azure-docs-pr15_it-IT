<properties
    pageTitle="Configurare i cluster dominio HDInsight | Microsoft Azure"
    description="Informazioni su come installare e configurare cluster dominio HDInsight"
    services="hdinsight"
    documentationCenter=""
    authors="saurinsh"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/26/2016"
    ms.author="saurinsh"/>

# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>Configurare dominio HDInsight cluster (Preview)

Informazioni su come configurare un cluster di Azure HDInsight con Azure Active Directory (Azure Active Directory) e [Apache cane](http://hortonworks.com/apache/ranger/) per usufruire di autenticazione e i criteri di controllo (RBAC) RTF accesso basato sui ruoli.  Dominio HDInsight possono essere configurati solo nei cluster basati su Linux. Per ulteriori informazioni, vedere [cluster di introdurre dominio HDInsight](hdinsight-domain-joined-introduction.md).

In questo articolo è la prima esercitazione di una serie:

- Creare un cluster di HDInsight connesso a Azure Active Directory (tramite la funzionalità di servizi di dominio Directory Azure) con cane Apache abilitato.
- Creare e applicare criteri Hive tramite cane Apache e consentire agli utenti (ad esempio, scienziati dati) a cui connettersi Hive utilizzando strumenti basate su ODBC, ad esempio Excel, Tableau e così via. Microsoft sta collaborando sull'aggiunta di altri carichi di lavoro, ad esempio HBase, vivacità ed eccesso, al dominio HDInsight breve.

Un esempio di topologia finale simile al seguente:

![Dominio topologia HDInsight](.\media\hdinsight-domain-joined-configure\hdinsight-domain-joined-topology.png)

Poiché Azure Active Directory attualmente supporta solo reti virtuali classiche (VNets) e basate su Linux HDInsight cluster supportano solo VNets in base a Gestione risorse di Azure, l'integrazione di HDInsight Azure Active Directory richiede due VNets e un peering tra di esse. Per informazioni sul confronto tra le due modelli, vedere [Azure Manager delle risorse e distribuzione classica: informazioni su modelli di distribuzione e lo stato delle risorse a disposizione](../resource-manager-deployment-model.md). Due VNets deve essere nella stessa regione di Azure Active Directory.

Nomi dei servizi Azure deve essere univoci globale. In questa esercitazione vengono utilizzati i nomi seguenti. Contoso è un nome fittizio. Quando accede all'interno dell'esercitazione, è necessario sostituire *contoso* con un nome diverso. 
    
**Nomi:**

|Proprietà|Valore|
|--------|-----|
| Azure Active Directory VNet|contosoaadvnet|
| Azure Active Directory macchine)|contosoaadadmin. Questa macchina virtuale viene utilizzata per configurare unità organizzativa e invertire zona DNS.|
| Directory di Azure Active Directory|contosoaaddirectory|
| Nome di dominio Active Directory Azure|Contoso (contoso.onmicrosoft.com)|
| HDInsight VNet|contosohdivnet|
| Gruppo di risorse HDInsight VNet|contosohdirg|
| HDInsight cluster|contosohdicluster|

In questa esercitazione illustra i passaggi per la configurazione di un cluster di HDInsight dominio. Ogni sezione sono disponibili collegamenti ad altri articoli con informazioni di base.

## <a name="prerequisite"></a>Prerequisito:

- Acquisire familiarità con [i servizi di dominio Active Directory Azure](https://azure.microsoft.com/services/active-directory-ds/) dalla struttura [prezzi](https://azure.microsoft.com/pricing/details/active-directory-ds/) .
- Assicurarsi che l'abbonamento sia whitelisted per questa versione di anteprima pubblico. È possibile farlo inviando un messaggio di posta elettronica a hdipreview@microsoft.com con l'ID di sottoscrizione.
- Un certificato SSL firmato da un'autorità di certificazione per il dominio. Sono richieste configurando LDAP sicuro. I certificati autofirmati non possono essere utilizzati.

## <a name="procedures"></a>Procedure

1. Creare un Azure VNet classico per la tua promozione Azure.  
2. Creare e configurare Azure Active Directory e Azure Active Directory.
3. Aggiungere una macchina virtuale VNet classico per la creazione di un'unità organizzativa. 
4. Creare un'unità organizzativa per Azure Active Directory.
5. Creare un HDInsight VNet in modalità di gestione delle risorse Azure.
6. Programma di installazione zone invertire DNS per il Azure Active Directory.
6. Peer due VNets.
7. Creare un cluster di HDInsight.

> [AZURE.NOTE] In questa esercitazione si presuppone che non si dispone di un annuncio Azure. Se si usano monitor, è possibile ignorare la parte nel passaggio 2.
    
## <a name="create-an-azure-classic-vnet"></a>Creare un VNet classica Azure

In questa sezione, si crea un VNet classico tramite il portale di Azure. Nella sezione successiva, si attiva la Azure Active Directory per il Azure AD VNet classica. Per ulteriori informazioni sulla procedura seguente e l'utilizzo di altri metodi di creazione VNet, vedere [creare una rete virtuale (classica) tramite il portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md).

**Per creare un VNet classica**

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Fare clic su **Nuovo** > **rete** > **virtuali**.
3. In **Selezionare un modello di distribuzione**, selezionare **classica**e quindi fare clic su **Crea**.
4. Immettere o selezionare i valori seguenti:

    - **Nome**: contosoaadvnet
    - **Spazio di indirizzi**: 10.1.0.0/16
    - **Nome subnet**: Subnet1
    - **Intervallo di indirizzi subnet**: 10.1.0.0/24
    - **Abbonamento**: (selezionare una sottoscrizione per la creazione di questo VNet).
    - **ResourceGroup**:
    - **Posizione**: (selezionare un'area per il cluster HDInsight).

        > [AZURE.IMPORTANT] È necessario scegliere un percorso che supporti Azure Active Directory. Per ulteriori informazioni, vedere [prodotti disponibili per l'area geografica](https://azure.microsoft.com/en-us/regions/services/). 
        >
        > VNet classica e VNet di gruppo risorse devono essere nella stessa regione di Azure Active Directory.

5. Fare clic su **Crea** per creare il VNet.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Creare e configurare Azure Active Directory per la tua promozione Azure

In questa sezione, sarà necessario:

1. Creare un annuncio Azure.
2. Creare gli utenti di Azure Active Directory. Questi utenti sono gli utenti del dominio. Utilizzare il primo utente per la configurazione del cluster HDInsight con Azure Active Directory.  Due utenti sono facoltativi per questa esercitazione. Servono in [configurare Hive criteri per i cluster HDInsight dominio](hdinsight-domain-joined-run-hive.md) quando si configurano i criteri di Apache cane.
3. Creare il gruppo di amministratori controller di dominio AAD e aggiungere l'utente di Azure Active Directory al gruppo. Utilizzare questo utente per creare un'unità organizzativa.
4. Abilitare Servizi di dominio di Active Directory Azure (Azure Active Directory) per Azure Active Directory.
7. Configurare LDAPS per Azure Active Directory. Lightweight Directory Access Protocol (LDAP) viene utilizzato per leggere e scrivere Azure Active Directory.

Se si preferisce utilizzare un annuncio di Azure esistente, è possibile ignorare i passaggi 1 e 2.

**Per creare un annuncio Azure**

1. Dal [portale classica Azure](https://manage.windowsazure.com), fare clic su **Nuovo** > **Servizi App** > **Active Directory** > **Directory** > **Creare personalizzata**. 
3. Immettere o selezionare i valori seguenti:

    - **Nome**: contosoaaddirectory
    - **Nome di dominio**: contoso.  Questo nome deve essere univoco globale.
    - **Paese o area geografica**: selezionare il paese o area geografica.
4. Fare clic su **completa**.


**Creare un utente di Azure Active Directory**

1. Dal [portale classica Azure](https://manage.windowsazure.com), fare clic su **Active Directory** -> **contosoaaddirectory**. 
3. Fare clic su **utenti** dal menu superiore.
4. Fare clic su **Aggiungi utente**.
4. Immettere **Nome utente**e quindi fare clic su **Avanti**. 
5. Configurare il profilo utente; **Ruolo**, selezionare **Amministratore globale**; e quindi fare clic su **Avanti**.  Per creare unità organizzative è necessario il ruolo di amministratore globale.
6. Fare clic su **Crea** per ottenere una password temporanea.
7. Creare una copia della password e quindi fare clic su **completa**. Più avanti in questa esercitazione si utilizzerà l'utente di amministratore globale per accedere all'interfaccia di amministrazione macchina virtuale per la creazione di un'unità organizzativa e configurazione del DNS inverso.


Seguire la stessa procedura per creare due più utenti con ruolo **utente** hiveuser1 e hiveuser2. Gli utenti seguenti verranno usati in [configurare Hive criteri per i cluster dominio HDInsight](hdinsight-domain-joined-run-hive.md).

**Per creare il gruppo degli amministratori controller di dominio AAD e aggiungere un utente di Azure Active Directory**

1. Dal [portale classica Azure](https://manage.windowsazure.com), fare clic su **Active Directory** > **contosoaaddirectory**. 
3. Fare clic su **gruppi** dal menu superiore.
4. Fare clic su **Aggiungi un gruppo** o **Aggiungi gruppo**.
5. Immettere o selezionare i valori seguenti:

    - **Nome**: AAD controller di dominio amministratori.  Non modificare il nome del gruppo.
    - **Tipo di gruppo**: sicurezza.
6. Fare clic su **completa**.
7. Fare clic su **Amministratori controller di dominio AAD** per aprire il gruppo.
8. Fare clic su **Aggiungi membri**.
9. Selezionare il primo utente che è stato creato nel passaggio precedente e quindi fare clic su **completa**.
10. Ripetere questi passaggi per creare un altro gruppo denominato **HiveUsers**e aggiungere i due utenti Hive al gruppo.

Per ulteriori informazioni, vedere [servizi di dominio Active Directory di Azure (Preview) - creare il gruppo ' AAD controller di dominio amministratori '](../active-directory-domain-services/active-directory-ds-getting-started.md).

**Per abilitare Azure Active Directory per la tua promozione Azure**

1. Dal [portale classica Azure](https://manage.windowsazure.com), fare clic su **Active Directory** > **contosoaaddirectory**. 
3. Fare clic su **Configura** dal menu superiore.
4. Scorrere fino a **Servizi di dominio**e impostare i valori seguenti:

    - **Attivare servizi di dominio per la directory**: Sì.
    - **Nome di dominio DNS dei servizi di dominio**: indica il nome DNS predefinito della directory di Azure. Ad esempio contoso.onmicrosoft.com.
    - **Servizi di dominio di connettersi a questa rete virtuale**: selezionare la rete virtuale classica creata in precedenza, ad esempio **contosoaadvnet**.
    
6. Fare clic su **Salva** dalla parte inferiore della pagina. Verrà visualizzato **in sospeso...** accanto a **Attiva servizi di dominio per la directory**.  
7. Attendere fino a **in sospeso...** scompare, e **l'Indirizzo IP** popolata. Verranno visualizzato popolati due indirizzi IP. Questi sono gli indirizzi IP dei controller di dominio viene completato il provisioning da servizi di dominio. Tutti gli indirizzi IP saranno visibili dopo il corrispondente controller di dominio è stato eseguito il provisioning e pronto. Annotare i due indirizzi IP. Saranno necessari in un secondo momento.

Per ulteriori informazioni, vedere [servizi di dominio Active Directory Azure (Preview) - servizi di dominio Active Directory di Azure abilitare](../active-directory-domain-services/active-directory-ds-getting-started-enableaadds.md).

**Per la sincronizzazione delle password**

Se si usa il proprio dominio, è necessario sincronizzare la password. Vedere [attivare la sincronizzazione delle password in servizi di dominio Active Directory Azure per una Azure basata solo su cloud directory Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).


**Per configurare LDAPS per Azure AD**

1. È possibile ottenere un certificato SSL firmato da un'autorità di certificazione per il dominio. I certificati autofirmati non possono essere utilizzati. Se non è possibile ottenere un certificato SSL, vedere raggiungere hdipreview@microsoft.com per un'eccezione.
1. Dal [portale classica Azure](https://manage.windowsazure.com), fare clic su **Active Directory** > **contosoaaddirectory**. 
3. Fare clic su **Configura** dal menu superiore.
4. Scorrere fino a **servizi di dominio**.
5. Fare clic su **Configura certificato**.
6. Seguire le istruzioni per specificare il file di certificato e la password. Verrà visualizzato **in sospeso...** accanto a **Attiva servizi di dominio per la directory**.  
7. Attendere fino a **in sospeso...** scompare, e se si ha compilato **Sicura certificato LDAP** .  Questa operazione può richiedere fino a 10 minuti o più.
 
>[AZURE.NOTE] Se alcune attività in background vengono eseguiti di Azure Active Directory, potrebbe essere visualizzato un errore durante il caricamento di certificato - <i>vi è un'operazione da eseguire per il tenant. Provare più tardi</i>.  Nel caso in cui si verifica questo errore, provare nuovamente in un secondo momento. Il secondo IP controller di dominio può richiedere fino a 3 ore per effettuare il provisioning.

Per ulteriori informazioni, vedere [Configurare sicura LDAP (LDAPS) per un dominio di servizi di dominio Active Directory di Azure gestiti](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="configure-an-organizational-unit-and-reverse-dns"></a>Configurare un'unità organizzativa e inversa DNS

In questa sezione, aggiungere una macchina virtuale per la VNet di Azure Active Directory e installare strumenti di amministrazione in questa macchina virtuale in modo che è possibile configurare un'unità organizzativa e invertire DNS. Ricerca DNS inversa è necessaria per l'autenticazione Kerberos.

**Per creare una macchina virtuale alla rete virtuale**

1. Dal [portale classica Azure](https://manage.windowsazure.com), fare clic su **Nuovo** > **calcolare** > **macchina virtuale** > **Dalla raccolta**.
3. Selezionare un'immagine e quindi fare clic su **Avanti**.  Se non si conosce quello da utilizzare, selezionare l'impostazione predefinita, **Data Center di Windows Server 2012 R2**.
4. Immettere o selezionare i valori seguenti:

    - Nome macchina virtuale: **contosoaadadmin**
    - Livello: **base**
    - Nome utente: (Immettere un nome utente)
    - Password: (Immettere una password)
    
    Nota il nome utente e la password è l'amministratore locale.
    
5. Fare clic su **Avanti**
6. In **Rete regione/virtuale**, selezionare la nuova rete virtuale creata nell'ultimo passaggio (contosoaadvnet) e quindi fare clic su **Avanti**.
7. Fare clic su **completa**.

**Per RDP per la macchina virtuale**

1. Dal [portale classica Azure](https://manage.windowsazure.com), fare clic su **macchine virtuali** > **contosoaadadmin**.
3. Fare clic su **Dashboard** dal menu superiore.
4. Fare clic su **Connetti** dalla parte inferiore della pagina.
5. Seguire le istruzioni e usare il nome utente amministratore locale e la password per la connessione.

**Per aggiungere macchine Virtuali al dominio Azure Active Directory**

1. Da sessione RDP, fare clic su **Start**e quindi fare clic su **Server Manager**.
2. Nel menu a sinistra, fare clic su **Server locale** .
3. Dal gruppo di lavoro, fare clic su **gruppo di lavoro**.
4. Fare clic su **Cambia**.
5. Fare clic su **dominio**, immettere **contoso.onmicrosoft.com**e quindi fare clic su **OK**.
6. Immettere le credenziali dell'utente di dominio e quindi fare clic su **OK**.
7. Fare clic su **OK**.
8. Fare clic su **OK** per accettare di riavviare il computer.
9. Fare clic su **Chiudi**.
10. Fare clic su **Riavvia**.

Per ulteriori informazioni, vedere [partecipare a una macchina virtuale di Windows Server per un dominio gestito](../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm.md).

**Per installare gli strumenti di amministrazione di Active Directory e DNS**

1. RDP in **contosoaadadmin** utilizzando l'account utente di Azure Active Directory.
2. Fare clic su **Start**e quindi fare clic su **Server Manager**.
3. Fare clic su **Dashboard** dal menu a sinistra.
4. Fare clic su **Gestisci**e quindi fare clic su **Aggiungi ruoli e funzionalità**.
5. Fare clic su **Avanti**.
6. Selezionare **installazione basato sui ruoli o funzionalità di base**e quindi fare clic su **Avanti**.
7. Selezionare la macchina virtuale corrente del pool di server e fare clic su **Avanti**.
8. Fare clic su **successivo** per ignorare i ruoli.
9. Espandere **Strumenti di amministrazione Server remoto**, espandere **Strumenti di amministrazione ruoli**, selezionare **Active Directory e AD LDS** e **Gli strumenti Server DNS**e quindi fare clic su **Avanti**. 
10. Fare clic su **Avanti**
10. Fare clic su **Installa**.

Per ulteriori informazioni, vedere [installare Active Directory di strumenti di amministrazione del computer virtuale](../active-directory-domain-services/active-directory-ds-admin-guide-administer-domain.md#task-2---install-active-directory-administration-tools-on-the-virtual-machine).


**Per configurare il DNS inverso**

1. RDP a contosoaadadmin utilizzando l'account utente di Azure Active Directory.
2. Fare clic su **Start**, fare clic su **Strumenti di amministrazione**e quindi fare clic su **DNS**. 
3. Fare clic su **No** per ignorare l'aggiunta di ContosoAADAdmin.
4. Selezionare **il computer seguente**, immettere l'indirizzo IP del primo server DNS che è configurato in precedenza e quindi fare clic su **OK**.  Si devono vedere che controller di dominio/DNS viene aggiunto al riquadro a sinistra.
3. Espandere il server DNS/controller di dominio, rapida **Zone di ricerca inversa**e quindi fare clic su **Nuova zona**. Verrà visualizzata la creazione guidata nuova zona.
4. Fare clic su **Avanti**.
5. Selezionare **zona primaria**e quindi fare clic su **Avanti**.
6. Selezionare questa opzione **per tutti i server DNS in esecuzione su controller di dominio in questo dominio**e quindi fare clic su **Avanti**.
6. Selezionare **IPv4 invertire zona di ricerca**e quindi fare clic su **Avanti**.
7. In **ID di rete**, immettere il prefisso per l'intervallo di rete HDInsight VNET e quindi fare clic su **Avanti**. Nella sezione seguente, è necessario creare HDInsight VNet.
8. Fare clic su **Avanti**.
9. Fare clic su **Avanti**.
10. Fare clic su **Fine**.



L'unità organizzativa creati successivamente verrà utilizzato quando si creano cluster HDInsight. Utenti di sistema Hadoop e gli account computer verranno inseriti in questa unità Organizzativa.

**Creare un'unità Organizzativa su un dominio gestito di servizi di dominio Active Directory di Azure**

1. RDP in **contosoaadadmin** utilizzando l'account di dominio che si trova nel gruppo **Amministratori controller di dominio AAD** .
2. Fare clic su **Start**, fare clic su **Strumenti di amministrazione**e quindi fare clic su **Centro amministrativo di Active Directory**.
5. Fare clic sul nome di dominio nel riquadro sinistro. Ad esempio contoso.
6. Fare clic su **Nuovo** sotto il nome di dominio nel riquadro **attività** e quindi fare clic su **Unità organizzativa**.
7. Immettere un nome, ad esempio **HDInsightOU**e quindi fare clic su **OK**. 


Per ulteriori informazioni, vedere [creare un'unità Organizzativa su un dominio di servizi di dominio Active Directory di Azure gestiti](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).


## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>Creare un Manager delle risorse VNet per cluster HDInsight

In questa sezione, è necessario creare un Manager delle risorse VNet Azure che verrà utilizzato per il cluster HDInsight. Per ulteriori informazioni sulla creazione di Azure VNET mediante altri metodi, vedere [creazione di una rete virtuale](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

Dopo aver creato il VNet, sarà necessario configurare Manager delle risorse VNet per utilizzare gli stessi server DNS per il VNet di Azure Active Directory. Se è stata seguita la procedura descritta in questa esercitazione per creare VNet classica e Azure Active Directory, i server DNS siano 10.1.0.4 e 10.1.0.5.

**Per creare un Manager delle risorse VNet**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su ** **Nuovo**e quindi **rete virtuale****. 
3. In **Selezionare un modello di distribuzione**, selezionare **Gestione risorse**e quindi fare clic su **Crea**.
4. Digitare o selezionare i valori seguenti:

    - **Nome**: contosohdivnet
    - **Spazio di indirizzi**: 10.2.0.0/16. Verificare che l'intervallo di indirizzi non può coincidere con l'intervallo di indirizzi IP di VNet classica.
    - **Nome subnet**: Subnet1
    - **Intervallo di indirizzi subnet**: 10.2.0.0/24
    - **Abbonamento**: (selezionare l'abbonamento Azure).
    - **Gruppo risorse**: contosohdirg
    - **Posizione**: (selezionare nella stessa posizione come VNet di Active Directory Azure, vale a dire contosoaadvnet).

5. Fare clic su **Crea**.


**Per configurare il DNS per Manager delle risorse VNet**

1. Dal [portale di Azure](https://portal.azure.com), fare clic su **altri servizi** -> **reti virtuali**. Assicurarsi che non vengano fare clic su **reti virtuali (classiche)**.
2. Fare clic su **contosohdivnet**.
4. Fare clic su **server DNS** a partire dal lato sinistro della stessa e nuovo.
6. Fare clic su **Personalizza**e quindi immettere i valori seguenti:

    - 10.1.0.4
    - 10.1.0.5

    Questi indirizzi IP di server DNS devono corrispondere ai server DNS VNet di Active Directory di Azure (VNet classica).
7. Fare clic su **Salva**.

























## <a name="peer-the-azure-ad-vnet-and-the-hdinsight-vnet"></a>Peer Azure VNet di Active Directory e HDInsight VNet

**Per esaminare due VNet**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **altri servizi** dal menu a sinistra.
3. Fare clic su **reti virtuali**. Non fare clic su **reti virtuali (classiche)**.
4. Fare clic su **contosohdivnet**.  Si tratta HDInsight VNet.
5. Fare clic su **Peerings** nel menu a sinistra della stessa e.
6. Fare clic su **Aggiungi** dal menu superiore. Viene aperta e **l'aggiungere peering** .
7. Impostare e **aggiungere peering** il o selezionare i valori seguenti:

    - **Nome**: ContosoAADHDIVNetPeering
    - **Modello di distribuzione di rete virtuale**: classica
    - **Abbonamento**: selezionare il proprio nome di sottoscrizione utilizzato per vnet classica (Azure Active Directory).
    - **Rete virtuale**: contosoaadvnet.
    - **Consenti l'accesso virtuali**: (controllo)
    - **Consenti inoltrato il traffico**: (controllo). Lasciare le altre due caselle di controllo deselezionata.

8. Fare clic su **OK**.



## <a name="create-hdinsight-cluster"></a>Creare cluster HDInsight


In questa sezione, si crea un cluster basati su Linux Hadoop in HDInsight tramite il portale di Azure e [gestione di risorse di Azure modello](../resource-group-template-deploy.md). Per altri metodi di creazione cluster e la comprensione delle impostazioni, vedere [creare HDInsight cluster](hdinsight-hadoop-provision-linux-clusters.md). Per ulteriori informazioni sull'utilizzo di gestione risorse modello per creare cluster Hadoop in HDInsight, vedere [creare Hadoop cluster in HDInsight utilizzare i modelli di Manager delle risorse](hdinsight-hadoop-create-windows-clusters-arm-templates.md)


**Per creare un cluster di HDInsight dominio tramite il portale di Azure**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Nuovo** **Intelligence + analitica**, quindi **HDInsight**.
3. Da e il **Nuovo HDInsight cluster** , immettere o selezionare i valori seguenti:

    - **Nome cluster**: immettere un nuovo nome cluster per il cluster dominio HDInsight.
    - **Abbonamento**: selezionare un abbonamento Azure utilizzato per la creazione di questo cluster.
    - **Configurazione cluster**:

        - **Tipo di cluster**: Hadoop. HDInsight dominio è attualmente disponibile solo supportati su Hadoop cluster.
        - **Sistema operativo**: Linux.  HDInsight dominio è supportato solo nei cluster basati su Linux HDInsight.
        - **Versione**: Hadoop 2.7.3 (HDI 3.5). HDInsight dominio è supportato solo nella versione cluster HDInsight 3.5.
        - **Tipo di cluster**: PREMIUM

        Fare clic su **Seleziona** per salvare le modifiche.

    - **Le credenziali**: configurare le credenziali per l'utente cluster e l'utente SSH.
    - **Origine dati**: creare un nuovo account di archiviazione o utilizzare un account di archiviazione esistente come account di archiviazione predefinito per il cluster HDInsight. Il percorso deve corrispondere a due VNets.  Il percorso è anche la posizione del cluster HDInsight.
    - **Prezzi**: selezionare il numero dei nodi di lavoro del cluster.
    - **Configurazioni avanzate**: 

        - **Partecipazione a dominio & Vnet/Subnet**: 

            - **Impostazioni del dominio**: 

                - **Nome di dominio**: contoso.onmicrosoft.com
                - **Nome utente**: immettere un nome utente di dominio. In questo dominio deve disporre dei privilegi seguenti: aggiungere computer al dominio e spostarla in un'unità organizzativa configurato in precedenza; Creazione di identità di servizio all'interno dell'unità di organizzazione che è stato configurato in precedenza; Creare le voci DNS inverse. In questo dominio utente diventa l'amministratore del dominio cluster HDInsight.
                - **Password di dominio**: immettere la password dell'utente di dominio.
                - **Unità organizzativa**: immettere il nome distinto di tht unità Organizzativa configurato in precedenza. Ad esempio: OU = HDInsightOU, Cc = contoso, Cc = onmicrosoft, controller di dominio = com
                - **URL LDAPS**: ldaps://contoso.onmicrosoft.com:636
                - **Gruppo di utenti di Access**: specificare il livello di protezione gruppo cui utenti wan per la sincronizzazione al cluster. Ad esempio HiveUsers.

                Fare clic su **Seleziona** per salvare le modifiche.

                ![Portale HDInsight dominio Configura impostazione del dominio](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
            - **Virtuali**: contosohdivnet
            - **Subnet**: Subnet1

            Fare clic su **Seleziona** per salvare le modifiche.       
        Fare clic su **Seleziona** per salvare le modifiche.
    - **Gruppo risorse**: selezionare il gruppo di risorse utilizzato per la HDInsight VNet (contosohdirg).

4. Fare clic su **Crea**.  

Un'altra opzione per la creazione di cluster HDInsight dominio consiste nell'usare il modello di gestione delle risorse Azure. La procedura seguente mostra come:

**Per creare un cluster di dominio HDInsight utilizzando un modello di gestione delle risorse**

1. Fare clic sull'immagine seguente per aprire un modello di gestione risorse nel portale di Azure. Il modello di gestione risorse si trova in un contenitore di blob pubblico. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Da e il **parametri** , immettere i valori seguenti:

    - **Abbonamento**: (selezionare l'abbonamento Azure).
    - **Gruppo risorse**: fare clic su **Usa esistente**, quindi specificare il gruppo di risorse stesso è stato utilizzato.  Ad esempio contosohdirg. 
    - **Posizione**: specificare un percorso di gruppo di risorse.
    - **Nome cluster**: immettere un nome per il cluster Hadoop che verrà creato. Ad esempio contosohdicluster.
    - **Tipo di cluster**: selezionare un tipo di grafico.  Il valore predefinito è **hadoop**.
    - **Posizione**: selezionare un percorso per il cluster.  L'account di archiviazione predefinito utilizzato nella stessa posizione.
    - **Conteggio dei nodi di lavoro cluster**: selezionare il numero dei nodi di lavoro.
    - **Nome di accesso cluster e la password**: il nome di accesso predefinito è **amministratore**.
    - **SSH nome utente e password**: il nome utente predefinito è **sshuser**.  È possibile rinominarla. 
    - **Id virtuali**: /subscriptions/&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >
    - **Virtuale Subnet di rete**: /subscriptions/&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >/subnet/Subnet1
    - **Nome di dominio**: contoso.onmicrosoft.com
    - **Nome distinto unità organizzazione**: OU = HDInsightOU, Cc = contoso, Cc = onmicrosoft, controller di dominio = com
    - **Cluster di utenti gruppo D Ns**: "\"CN = HiveUsers, OU = AADDC utenti, Cc =<DomainName>, controller di dominio = onmicrosoft, controller di dominio = com\""
    - **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
    - **DomainAdminUserName**: (immettere il nome utente amministratore)
    - **DomainAdminPassword**: (immettere la password di amministratore dominio)
    - **Accetto i termini e condizioni sopra**: (controllo)
    - **Aggiungi a dashboard**: (controllo)

6. Fare clic su **Acquista**. Verrà visualizzato un nuovo riquadro intitolato **la distribuzione dei modelli di distribuzione**. Richiede circa circa il 20 minuti per creare un cluster. Dopo aver creato il grafico, è possibile fare clic e il cluster nel portale per aprirlo.

Dopo aver completato l'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in archiviazione di Azure, in modo che è possibile eliminare un cluster quando non è in uso. Anche addebitate per un cluster di HDInsight, anche quando non è in uso. Poiché le spese per il cluster sono tutte le volte più le spese per lo spazio di archiviazione, è opportuno economico eliminare cluster quando non sono in uso. Per istruzioni dell'eliminazione di un cluster, vedere [gestire Hadoop cluster in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md#delete-clusters).





## <a name="next-steps"></a>Passaggi successivi

- Per la configurazione dei criteri Hive e fase Hive query, vedere [configurare Hive criteri per i cluster dominio HDInsight](hdinsight-domain-joined-run-hive.md).
- Per eseguire query Hive utilizzando SSH nei cluster HDInsight dominio, vedere [Usare SSH con basati su Linux Hadoop in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-domain-joined-hdinsight-cluster).
