<properties
    pageTitle="Installare RStudio con Server R HDInsight (preview) | Microsoft Azure"
    description="Come installare RStudio con Server R HDInsight (preview)."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/16/2016"
   ms.author="jeffstok"/>


# <a name="installing-rstudio-with-r-server-on-hdinsight-preview"></a>Installazione di RStudio con Server R HDInsight (preview)

Sono disponibili più ambienti di sviluppo integrato (IDE) per R oggi, tra cui Microsoft recente annunciato [R Tools per Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS), una famiglia di strumenti di desktop e server da [RStudio](https://www.rstudio.com/products/rstudio-server/)o basato su Eclipse del Walware [StatET](http://www.walware.de/goto/statet). Tra i più diffusi su Linux è l'utilizzo di [RStudio Server](https://www.rstudio.com/products/rstudio-server/) che fornisce un IDE basate su browser per i client remoti di.  Installazione RStudio Server nel nodo del bordo di un cluster di HDInsight Premium offre un'esperienza completa con IDE per lo sviluppo e l'esecuzione di script R con R Server nel cluster e può essere molto più produttivo rispetto all'utilizzo della Console R predefinito.

In questo articolo si imparerà a installare la versione di (gratuito) della community di RStudio Server nel nodo del bordo di un cluster utilizzando uno script personalizzato. Se si preferisce la versione Pro le licenze commerciali di RStudio Server, è necessario seguire le istruzioni di installazione dal [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [AZURE.NOTE] La procedura descritta in questo documento richiede un Server R cluster HDInsight e non funzionare correttamente se si utilizza un cluster HDInsight dove R è stato installato utilizzando l' [Installazione R Script azione](hdinsight-hadoop-r-scripts-linux.md).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster di Azure HDInsight con Server R installato. Per ulteriori informazioni, vedere [Introduzione a Server R cluster HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Un client SSH. Per distribuzioni Linux e Unix o Mac OS X, il `ssh` comando viene fornito con il sistema operativo. Per Windows, è consigliabile [Cygwin](http://www.redhat.com/services/custom/cygwin/) con [l'opzione OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU)o [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  


## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Installare RStudio nel cluster utilizzando uno script personalizzato

1. Identificare il nodo del bordo del cluster. Per un cluster di HDInsight con Server R, di seguito è la convenzione di denominazione per nodo principale e nodo del bordo.

    * Nodo testa-`CLUSTERNAME-ssh.azurehdinsight.net`
    * Nodo del bordo-`R-Server.CLUSTERNAME-ssh.azurehdinsight.net` 

2. SSH in nodo del bordo del cluster utilizzando il modello di denominazione sopra. 
 
    * Se si esegue la connessione da un client Linux, vedere [connettersi a un cluster basati su Linux HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).
    * Se si esegue la connessione da un client di Windows, vedere [connettersi a un cluster basati su Linux HDInsight utilizzando PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster).

3. Una volta connessi, diventare un utente radice nel cluster. Nella sessione SSH, usare il comando seguente.

        sudo su -

4. Scaricare lo script personalizzato per installare RStudio. Usare il comando seguente.

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Modificare le autorizzazioni per il file di script personalizzati ed eseguire lo script. Usare i comandi seguenti.

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Se è stata utilizzata una password SSH durante la creazione di un cluster di HDInsight con Server R, è possibile ignorare questo passaggio e passare alla successiva. Se invece si utilizza una chiave SSH per creare il cluster, è necessario impostare una password per l'utente SSH. Sarà necessario questa password quando ci si connette a RStudio. Eseguire i comandi seguenti. Quando viene richiesto di **password Kerberos corrente**, premere **INVIO**.  Si noti che è necessario sostituire `USERNAME` con un utente SSH per il cluster HDInsight.

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:
        
    Se la password è impostata correttamente, è visualizzato un messaggio simile al seguente.

        passwd: password updated successfully


    Chiudere la sessione SSH.

7. Creare un tunnel SSH al cluster mediante la mappatura `localhost:8787` cluster HDInsight nel computer client. È necessario creare un tunnel SSH prima di aprire una nuova sessione del browser.

    * In un client Linux o un client di Windows con [Cygwin](http://www.redhat.com/services/custom/cygwin/) quindi aprire una sessione terminale e usare il comando seguente.

            ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
            
        Sostituire **nomeutente** con un utente SSH per il cluster HDInsight e **nome cluster** con il nome del cluster HDInsight è inoltre possibile utilizzare una chiave SSH anziché una password aggiungendo`-i id_rsa_key`     

    * Se tramite un client di Windows in PuTTY quindi

        1.  Aprire PuTTY e immettere le informazioni di connessione. Se non ha familiarità con PuTTY, vedere [Utilizzare SSH con basati su Linux Hadoop in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md) per informazioni su come usarlo con HDInsight.
        2.  Nella sezione **categoria** a sinistra della finestra di dialogo espandere **connessione**, espandere **SSH**e quindi selezionare **tunnel**.
        3.  Specificare le informazioni seguenti nel modulo **Opzioni che controllano l'inoltro di porta SSH** :

            * **Porta di origine** - porta sul client che si desidera inoltrare. Ad esempio **8787**.
            * **Destinazione** - la destinazione che deve essere assegnata a computer client locale. Ad esempio **localhost:8787**.

            ![Creare un tunnel SSH] (./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Creare un tunnel SSH")

        4. Fare clic su **Aggiungi** per aggiungere le impostazioni e quindi fare clic su **Apri** per aprire un collegamento SSH.
        5. Quando richiesto, accedere al server. Per stabilire una sessione SSH e abilitare il tunnel.

8. Aprire un web browser e immettere l'URL seguente in base alla porta che immesso per il tunnel.

        http://localhost:8787/ 

9. Verrà richiesto di immettere il nome utente SSH e la password per la connessione al cluster. Se è stata utilizzata una chiave SSH durante la creazione del cluster, è necessario immettere la password creata nel passaggio 5.

    ![Connettersi a R Studio] (./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Creare un tunnel SSH")

10. Per verificare se l'installazione di RStudio è stata eseguita correttamente, è possibile eseguire un test script che viene eseguito R in base a processi MapReduce e motori cluster. Tornare alla console di SSH e immettere i comandi seguenti per scaricare lo script di test per l'esecuzione in RStudio.

    * Se è stato creato un cluster di Hadoop con R, usare questo comando.
        
            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

    * Se è stato creato un cluster di motori con R, usare questo comando.

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. In RStudio, verrà visualizzato lo script di test che è stato scaricato. Fare doppio clic sul file per aprirlo, selezionare il contenuto del file e quindi fare clic su **Esegui**. Verrà visualizzato l'output nel riquadro della **Console** .
 
    ![Verificare il funzionamento dell'installazione] (./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Verificare il funzionamento dell'installazione")

In alternativa, è possibile digitare `source(testhdi.r)` o `source(testhdi_spark.r)` per eseguire lo script.

## <a name="see-also"></a>Vedere anche

- [Calcolare le opzioni di contesto per R Server nei cluster HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)

- [Opzioni relative all'archiviazione Azure per Server R nella HDInsight premium](hdinsight-hadoop-r-server-storage.md)


 
