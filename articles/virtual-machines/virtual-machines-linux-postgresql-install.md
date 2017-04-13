<properties
    pageTitle="Configurare PostgreSQL su una VM Linux | Microsoft Azure"
    description="Informazioni su come installare e configurare PostgreSQL in un computer virtuale Linux in Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


# <a name="install-and-configure-postgresql-on-azure"></a>Installare e configurare PostgreSQL su Azure

PostgreSQL è un database di origine Apri avanzato simile a Oracle e DB2. Includa per le aziende, ad esempio conformità completo ACID, elaborazione delle transazioni affidabile e il controllo della concorrenza più versioni. Inoltre, supporta standard, ad esempio SQL ANSI e SQL/MED (inclusi wrapper dati esterni per Oracle, MySQL, MongoDB e molti altri). È altamente extensible con supporto per oltre 12 linguaggi procedurali, indici GIN ed essenza, il supporto per dati spaziale e più funzionalità NoSQL simili a quelle per JSON o applicazioni basate su valore chiave.

In questo articolo si imparerà a installare e configurare PostgreSQL in un computer virtuale Azure che eseguono Linux.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="install-postgresql"></a>Installare PostgreSQL

> [AZURE.NOTE] È necessario avere già una macchina virtuale Azure che eseguono Linux per completare questa esercitazione. Per creare e configurare un VM Linux prima di procedere, vedere l' [esercitazione macchine Virtuali Linux Azure](virtual-machines-linux-quick-create-cli.md).

In questo caso, Usa la porta 1999 come porta PostgreSQL.  

Connettersi a Linux macchine Virtuali creati tramite PuTTY. Se si tratta la prima volta che si sta utilizzando una macchina virtuale Linux Azure, informazioni su [come usare SSH con Linux su Azure](virtual-machines-linux-mac-create-ssh-keys.md) per imparare a utilizzare PuTTY per connettersi a un VM Linux.

1. Eseguire il seguente comando per passare alla radice di (amministratori):

        # sudo su -

2. Alcune distribuzioni avere dipendenze che è necessario installare prima di installare PostgreSQL. Cercare il distro in questo elenco, eseguire il comando appropriato:

    - Red Hat Linux base:

            # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

    - Debian Linux base:

            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

    - SUSE Linux:

            # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. Scaricare PostgreSQL nella directory principale e quindi decomprimere il pacchetto:

        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

        # tar jxvf  postgresql-9.3.5.tar.bz2

    Le risposte sopra è illustrato un esempio. È possibile trovare l'indirizzo di download più dettagliata nell' [indice della /pub/origine /](https://ftp.postgresql.org/pub/source/).

4. Per avviare la compilazione, eseguire questi comandi:

        # cd postgresql-9.3.5

        # ./configure --prefix=/opt/postgresql-9.3.5

5. Se si desidera creare tutti gli elementi che possono essere visualizzate, tra cui la documentazione (pagine HTML e uomo) e moduli aggiuntivi (pensionistici), eseguire il comando seguente:

        # gmake install-world

    Visualizzato il seguente messaggio di conferma:

        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Configurare PostgreSQL

1. (Facoltativo) Creare una logica per abbreviare il riferimento PostgreSQL per non includere il numero di versione:

        # ln -s /opt/pgsql9.3.5 /opt/pgsql

2. Creare una directory per il database:

        # mkdir -p /opt/pgsql_data

3. Creare un utente non radice e modificare il profilo dell'utente. Passare a questo nuovo utente (denominato *postgres* in questo esempio):

        # useradd postgres

        # chown -R postgres.postgres /opt/pgsql_data

        # su - postgres

   > [AZURE.NOTE] Per motivi di sicurezza, PostgreSQL utilizza un utente non radice per inizializzare, avviare o arrestare il database.


4. Modificare il file *bash_profile* tramite l'immissione di comandi riportati di seguito. Queste righe verranno aggiunti alla fine del file *bash_profile* :

        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF

5. Eseguire il file *bash_profile* :

        $ source .bash_profile

6. Convalidare il funzionamento dell'installazione utilizzando il comando seguente:

        $ which psql

    Se l'installazione viene eseguito correttamente, verrà visualizzato il seguente risposta:

        /opt/pgsql/bin/psql

7. È anche possibile controllare la versione PostgreSQL:

        $ psql -V

8. Inizializzare il database:

        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

    Verrà visualizzato il seguente output:

![immagine](./media/virtual-machines-linux-postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Configurare PostgreSQL

<!--    [postgres@ test ~]$ exit -->

Eseguire i comandi seguenti:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Modificare le due variabili nel file /etc/init.d/postgresql. Il prefisso è impostato sul percorso di installazione di PostgreSQL: **/opt/pgsql**. PGDATA è impostato sul percorso di archiviazione di dati di PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![immagine](./media/virtual-machines-linux-postgresql-install/no2.png)

Modificare il file per renderlo eseguibile:

    # chmod +x /etc/init.d/postgresql

Avviare PostgreSQL:

    # /etc/init.d/postgresql start

Verificare se il punto finale della PostgreSQL è in:

    # netstat -tunlp|grep 1999

Verrà visualizzato il seguente output:

![immagine](./media/virtual-machines-linux-postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Connettersi al database Postgres

Passare all'utente postgres nuovamente:

    # su - postgres

Creare un database Postgres:

    $ createdb events

Connettersi al database eventi appena creato:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Creare ed eliminare una tabella Postgres

Ora che si è connessi al database, è possibile creare tabelle al suo interno.

Ad esempio, creare una nuova tabella di esempio Postgres utilizzando il comando seguente:

    CREATE TABLE potluck (name VARCHAR(20), food VARCHAR(30),   confirmed CHAR(1), signup_date DATE);

A questo punto è stata configurata una tabella di quattro colonne con i seguenti nomi di colonna e le restrizioni:

1. La colonna "nome" è stata limitata tramite il comando VARCHAR in 20 caratteri.
2. La colonna "alimentari" indica l'alimento che consentiranno di ogni persona. VARCHAR limita il testo da 30 in caratteri.
3. La colonna "confermata" record se la persona ha RSVP'd per il pranzo. I valori accettabili sono "Y" e "N".
4. Nella colonna è "Data" visualizzato quando si ha effettuato l'iscrizione per l'evento. Postgres richiede che deve essere scritto date come yyyy-mm-dd.

Verrà visualizzato il seguente se è stata creata la tabella:

![immagine](./media/virtual-machines-linux-postgresql-install/no4.png)

È anche possibile controllare la struttura della tabella utilizzando il comando seguente:

![immagine](./media/virtual-machines-linux-postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Aggiungere dati a una tabella

Prima di tutto, inserire le informazioni in una riga:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

È visualizzato il seguente output:

![immagine](./media/virtual-machines-linux-postgresql-install/no6.png)

È possibile aggiungere altre persone sono riportate alcune anche alla tabella. Di seguito sono riportate alcune opzioni oppure è possibile creare:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Visualizzare le tabelle

Utilizzare il comando seguente per visualizzare una tabella:

    select * from potluck;

L'output è:

![immagine](./media/virtual-machines-linux-postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Eliminare i dati in una tabella

Per eliminare i dati in una tabella, usare il comando seguente:

    delete from potluck where name=’John’;

Consente di eliminare tutte le informazioni nella riga "John". L'output è:

![immagine](./media/virtual-machines-linux-postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aggiornamento dei dati in una tabella

Utilizzare il comando seguente per aggiornare i dati in una tabella. In questo caso, Sandy ha confermato che ha partecipano, in modo che verrà modificata la sua RSVP da "N" a "Y":

    UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##<a name="get-more-information-about-postgresql"></a>Altre informazioni su PostgreSQL
Ora che è stata completata l'installazione di PostgreSQL in una macchina virtuale Linux Azure, sarà possibile utilizzare l'uso in Azure. Per ulteriori informazioni su PostgreSQL, visitare il [sito Web PostgreSQL](http://www.postgresql.org/).
