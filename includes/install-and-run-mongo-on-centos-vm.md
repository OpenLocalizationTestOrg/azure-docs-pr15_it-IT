Seguire questa procedura per installare ed eseguire MongoDB in un computer virtuale che eseguono CentOS Linux.

> [AZURE.WARNING] Funzionalità di sicurezza MongoDB, ad esempio l'autenticazione e associazione di indirizzi IP, non sono abilitate per impostazione predefinita. Funzionalità di sicurezza possono essere attivate prima di distribuire MongoDB in un ambiente di produzione.  Per ulteriori informazioni, vedere [sicurezza e l'autenticazione](http://www.mongodb.org/display/DOCS/Security+and+Authentication) .

1. Configurare il sistema di gestione di pacchetto (SLURP) in modo che è possibile installare MongoDB. Creare un file di */etc/yum.repos.d/10gen.repo* per memorizzare le informazioni sulle repository e aggiungere quanto segue:

        [10gen]
        name=10gen Repository
        baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64
        gpgcheck=0
        enabled=1

2. Salvare il file repo e quindi eseguire il seguente comando per aggiornare il database pacchetto locale:

        $ sudo yum update

3. Per installare il pacchetto, eseguire il comando seguente per installare l'ultima versione stabile di MongoDB e i relativi strumenti:

        $ sudo yum install mongo-10gen mongo-10gen-server

    Attendere MongoDB Scarica e installa.

4. Creare una directory di dati. Per impostazione predefinita MongoDB archivia i dati nella directory */data/db* , ma è necessario creare tale directory. Per creare il, eseguire:

        $ sudo mkdir -p /srv/datadrive/data
        $ sudo chown `id -u` /srv/datadrive/data

    Per ulteriori informazioni sull'installazione MongoDB su Linux, vedere [Guida introduttiva Unix][QuickstartUnix].

5. Per avviare il database, eseguire:

        $ mongod --dbpath /srv/datadrive/data --logpath /srv/datadrive/data/mongod.log

    Tutti i messaggi di log verranno indirizzati al file */srv/datadrive/data/mongod.log* come MongoDB server verrà avviato e prealloca una quantità file diario. Possono richiedere alcuni minuti prima di MongoDB preallocare i file del diario e avviare in attesa di connessioni.

6. Per avviare la shell amministrativa MongoDB, aprire una finestra separata SSH o PuTTY ed eseguire:

        $ mongo
        > db.foo.save ( { a:1 } )
        > db.foo.find()
        { _id : ..., a : 1 }
        > show dbs  
        ...
        > show collections  
        ...  
        > help  

    Verrà creato il database per l'inserimento.

7. Dopo aver installato MongoDB è necessario configurare un endpoint in modo che MongoDB accessibile in remoto. Nel portale di gestione fare clic su **macchine virtuali**, quindi fare clic sul nome del computer virtuale nuovo, quindi scegliere **i punti finali**.
    
    ![Punti finali][Image7]

8. Fare clic su **Aggiungi Endpoint** nella parte inferiore della pagina.
    
    ![Punti finali][Image8]

9. Aggiungere un endpoint con le impostazioni seguenti:

 - **Nome**: Mongo
 - **Protocollo**: TCP
 - **Porta pubblica**: 27017
 - **Porta privato**: 27017
 
 In questo modo MongoDB per accedervi in remoto.



[QuickStartUnix]: http://www.mongodb.org/display/DOCS/Quickstart+Unix


[Image7]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint.png
[Image8]: ./media/install-and-run-mongo-on-centos-vm/LinuxVmAddEndpoint2.png
