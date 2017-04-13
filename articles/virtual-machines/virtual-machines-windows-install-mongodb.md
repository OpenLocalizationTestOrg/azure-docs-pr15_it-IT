<properties
    pageTitle="Installare MongoDB in un Windows macchine Virtuali | Microsoft Azure"
    description="Informazioni su come installare MongoDB in una macchina virtuale Azure che eseguono Windows Server 2012 R2 creato con il modello di distribuzione di Manager delle risorse."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Installare e configurare MongoDB su una macchina virtuale di Windows in Azure
[MongoDB](http://www.mongodb.org) sui database NoSQL Apri origine e prestazioni elevate popolari. In questo articolo consente di installare e configurare MongoDB su una Windows Server 2012 R2 macchine () in Azure. È anche possibile [installare MongoDB in una macchina virtuale Linux in Azure](virtual-machines-linux-install-mongodb.md).


## <a name="prerequisites"></a>Prerequisiti

Prima di installare e configurare MongoDB, è necessario creare una macchina virtuale e, se possibile, aggiungere un disco di dati. Vedere gli articoli seguenti per creare una macchina virtuale e aggiungere un disco dati:

- [Creare una macchina virtuale Server di Windows tramite il portale di Azure](virtual-machines-windows-hero-tutorial.md) o [creare una macchina virtuale Server di Windows tramite PowerShell Azure](virtual-machines-windows-ps-create.md)
- [Allegare un disco di dati a una macchina virtuale Server di Windows tramite il portale di Azure](virtual-machines-windows-attach-disk-portal.md) o [allegare un disco di dati a una macchina virtuale Server di Windows tramite PowerShell Azure](https://msdn.microsoft.com/library/mt603673.aspx)
    
Per iniziare l'installazione e configurazione MongoDB, [accedere al Server di Windows macchine Virtuali](virtual-machines-windows-connect-logon.md) mediante Desktop remoto.


## <a name="install-mongodb"></a>Installare MongoDB

> [AZURE.IMPORTANT] Funzionalità di sicurezza MongoDB, ad esempio l'autenticazione e associazione di indirizzi IP, non sono abilitate per impostazione predefinita. Funzionalità di sicurezza possono essere attivate prima di distribuire MongoDB in un ambiente di produzione. Per ulteriori informazioni, vedere [MongoDB sicurezza e l'autenticazione](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Dopo la connessione per la macchina virtuale con Desktop remoto, aprire Internet Explorer dal menu di **avvio** nella macchina virtuale.

2. Selezionare **sicurezza, privacy e le impostazioni di compatibilità è consigliabile utilizzare** Internet Explorer alla prima apertura e fare clic su **OK**.

3. Configurazione della protezione avanzata di Internet Explorer è attivata per impostazione predefinita. Aggiungere il sito Web MongoDB all'elenco dei siti consentiti:

    - Selezionare l'icona di **Strumenti** in alto a destra.
    - In **Opzioni Internet**fare clic sulla scheda **protezione** e quindi selezionare l'icona **Siti attendibili** .
    - Fare clic sul pulsante **siti** . Aggiungere _https://\*. mongodb.org_ all'elenco dei siti attendibili e quindi chiudere la finestra di dialogo.

    ![Configurare le impostazioni di sicurezza di Internet Explorer](./media/virtual-machines-windows-install-mongodb/configure-internet-explorer-security.png)

4. Passare alla pagina [MongoDB - download](http://www.mongodb.org/downloads) (http://www.mongodb.org/downloads).

5. Per impostazione predefinita, è necessario selezionare l'edizione **Community Server** e l'ultima versione stabile corrente per Windows Server 2008 R2 a 64 bit e versioni successive. Per scaricare il programma di installazione, fare clic su **Scarica (msi)**.

    ![Scaricare MongoDB programma di installazione](./media/virtual-machines-windows-install-mongodb/download-mongodb.png)

    Al termine del download, eseguire il programma di installazione.

6. Leggere e accettare il contratto di licenza. Quando viene richiesto, selezionare installa **completata** .

7. Nella schermata finale, fare clic su **Installa**.


## <a name="configure-the-vm-and-mongodb"></a>Configurare la macchina virtuale e MongoDB

1. Le variabili di percorso non vengono aggiornate dal programma di installazione MongoDB. Senza il MongoDB `bin` posizione nella variabile path, è necessario specificare il percorso completo ogni volta che si utilizza un file eseguibile MongoDB. Per aggiungere il percorso per la variabile di percorso:

    - Menu di scelta rapida **Start** e selezionare **sistema**.
    - Fare clic su **impostazioni di sistema avanzate**e quindi fare clic su **Variabili**.
    - In **variabili di sistema**, selezionare **il percorso**e quindi fare clic su **Modifica**.

    ![Configurare le variabili di percorso](./media/virtual-machines-windows-install-mongodb/configure-path-variables.png)

    Aggiungere il percorso del MongoDB `bin` cartella. MongoDB viene in genere installato `C:\Program Files\MongoDB`. Verificare che il percorso di installazione di una macchina virtuale. Nell'esempio seguente aggiunge il valore predefinito MongoDB installare percorso in cui il `PATH` variabile:

    ```
    ;C:\Program Files\MongoDB\Server\3.2\bin
    ```

    > [AZURE.NOTE] Assicurarsi di aggiungere il punto e virgola iniziale (`;`) per indicare che si sta aggiungendo la posizione in cui il `PATH` variabile.

2. Creare directory di dati e log MongoDB sul disco dati. Menu di **avvio** selezionare **prompt dei comandi**. Negli esempi seguenti creano la directory sull'unità f:

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```

3. Avviare un'istanza di MongoDB con il comando seguente, modificare il percorso ai dati e directory del Registro di conseguenza:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    Possono richiedere alcuni minuti prima di MongoDB allocare i file del diario e avviare in attesa di connessioni. Tutti i messaggi di log sono indirizzati al file *F:\MongoLogs\mongolog.log* come `mongod.exe` server verrà avviato e assegna i file del diario.

    > [AZURE.NOTE] Il prompt dei comandi rimane attivo l'attività mentre è in esecuzione l'istanza MongoDB. Lasciare aperta per continuare l'esecuzione MongoDB finestra prompt dei comandi. In alternativa, installare MongoDB come servizio, come descritto nel passaggio successivo.

4. Per un'esperienza MongoDB sia più efficace, installare il `mongod.exe` come servizio. Creazione di un servizio di conseguenza, che non è necessario lasciare un prompt dei comandi in esecuzione ogni volta che si desidera utilizzare MongoDB. Creare il servizio come indicato di seguito, regolare il percorso di directory di dati e log conseguenza:

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log `
        --logappend  --install
    ```

    Il comando precedente crea un servizio denominato MongoDB, con una descrizione del "Mongo DB". Vengono inoltre specificati i parametri seguenti:

    - Il `--dbpath` opzione specifica il percorso della directory di dati.
    - Il `--logpath` opzione deve essere utilizzata per specificare un file di log, perché il servizio in esecuzione non dispone di una finestra di comando per visualizzare l'output.
    - Il `--logappend` opzione specifica che il riavvio del servizio comporta l'output aggiungere il file esistente.

  Per avviare il servizio MongoDB, eseguire il comando seguente:

    ```
    net start MongoDB
    ```

    Per ulteriori informazioni sulla creazione di servizio MongoDB, vedere [configurare un servizio di Windows per MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Verificare l'istanza MongoDB

Con MongoDB in esecuzione come una singola istanza o installato come servizio, è possibile iniziare la creazione e l'utilizzo dei database. Per avviare la shell amministrativa MongoDB, aprire un'altra finestra prompt dei comandi dal menu di **avvio** e immettere il comando seguente:

```
mongo  
```

È possibile elencare i database con la `db` comando. Inserire alcuni dati come indicato di seguito:

```
db.foo.insert( { a : 1 } )
```

Cercare dati nel modo seguente:

```
db.foo.find()
```

L'output è simile alla seguente:

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Uscita la `mongo` console come indicato di seguito:

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configurare regole gruppo di sicurezza di rete e firewall
Ora che MongoDB è installata e in esecuzione, aprire una porta in Windows Firewall in modo che sia possibile connettersi in remoto al MongoDB. Per creare una nuova regola in ingresso per consentire la porta TCP 27017, aprire un prompt dei comandi PowerShell amministrativi e immettere il comando seguente:

```powerShell
New-NetFirewallRule -DisplayName "Allow MongoDB" -Direction Inbound `
    -Protocol TCP -LocalPort 27017 -Action Allow
```

È anche possibile creare la regola utilizzando lo strumento di gestione grafica di **Windows Firewall con sicurezza avanzata** . Creare una nuova regola in ingresso per consentire la porta TCP 27017.

Se necessario, creare una regola gruppo di sicurezza di rete per consentire l'accesso a MongoDB dall'esterno subnet virtuali Azure esistente. È possibile creare regole gruppo di sicurezza di rete tramite il [portale di Azure](virtual-machines-windows-nsg-quickstart-portal.md) e [Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md). Come con le regole di Windows Firewall, permettono di porte TCP 27017 all'interfaccia di rete virtuale delle VM MongoDB.

> [AZURE.NOTE] Porte TCP 27017 sono la porta predefinita utilizzata da MongoDB. È possibile modificare questa porta utilizzando la `--port` parametro quando si avvia `mongod.exe` manualmente o da un servizio. Se si cambia la porta, assicurarsi di aggiornare le regole di Windows Firewall e il gruppo di sicurezza di rete nei passaggi precedenti.


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione, è stato illustrato come installare e configurare MongoDB sulle macchine Virtuali di Windows. È ora possibile accedere MongoDB sulle macchine Virtuali di Windows, seguendo scenari avanzati nella [documentazione MongoDB](https://docs.mongodb.com/manual/).
