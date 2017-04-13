<properties
    pageTitle="Azure Active Directory servizi di dominio: Partecipare a una VM RHEL di un dominio gestito | Microsoft Azure"
    description="Partecipare a una macchina virtuale Red Hat Enterprise Linux in servizi di dominio Active Directory di Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Partecipare a una macchina virtuale Red Hat Enterprise Linux 7 di un dominio gestito
In questo articolo viene illustrato come aggiungere una macchina virtuale Red Hat Enterprise Linux (RHEL) 7 a un dominio gestito di servizi di dominio Active Directory Azure.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Effettuare il provisioning di una macchina virtuale Red Hat Enterprise Linux
Per eseguire il provisioning di una macchina virtuale RHEL 7 tramite il portale di Azure, procedere come segue.

1. Accedere al [portale di Azure](https://portal.azure.com).

    ![Azure del dashboard del portale](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. Fare clic su **Nuovo** nel riquadro a sinistra e digitare **Red Hat** nella barra di ricerca come illustrato nella schermata seguente. Movimenti Red Hat Enterprise Linux verranno visualizzati nei risultati della ricerca. Fare clic su **Red Hat Enterprise Linux 7.2**.

    ![Selezionare RHEL nei risultati](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. I risultati della ricerca nel riquadro di **tutto il necessario** indicare l'immagine Red Hat Enterprise Linux 7.2. Fare clic su **Red Hat Enterprise Linux 7.2** per visualizzare ulteriori informazioni sull'immagine macchina virtuale.

    ![Selezionare RHEL nei risultati](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. Nel riquadro **Red Hat Enterprise Linux 7.2** risulta ulteriori informazioni sull'immagine macchina virtuale. Nell'elenco a discesa **Selezionare un modello di distribuzione** selezionare **classica**. Fare clic sul pulsante **Crea** .

    ![Visualizzare i dettagli di immagine](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. Nel riquadro di **Creare macchine Virtuali** , immettere il **Nome Host** per la nuova macchina virtuale. Inoltre, specificare un nome utente amministratore locale nel campo **nome utente** e una **Password**. È anche possibile utilizzare una chiave SSH per eseguire l'autenticazione utente amministratore locale. Selezionare un **Livello prezzi** per la macchina virtuale.

    ![Creare macchine Virtuali - dettagli di base](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. Fare clic su **configurazione facoltativa**. Nel riquadro di **configurazione facoltativo** , fare clic su **rete**.

    ![Creare macchine Virtuali - configurare virtuali](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. Verrà visualizzato un riquadro intitolato **rete**. Nel riquadro della **rete** , fare clic su **Rete virtuale** per selezionare la rete virtuale a cui devono essere distribuite VM Linux. Verrà aperto il riquadro **Virtuali** . Nel riquadro **Virtuali** scegliere l'opzione **Usa una rete virtuale esistente** . Quindi selezionare la rete virtuale in cui è disponibili Azure servizi di dominio Active Directory. In questo esempio abbiamo selezionare la rete virtuale 'MyPreviewVNet'.

    ![Creare macchine Virtuali - selezione virtuali](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. Nel riquadro **configurazione facoltativo** , fare clic su **OK** .

    ![Creare macchine Virtuali - virtuali selezionata](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. A questo punto si è pronti creare la macchina virtuale. Nel riquadro **Creare macchine Virtuali** , fare clic sul pulsante **Crea** .

    ![Creare macchine Virtuali - pronto](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. Distribuzione della nuova macchina virtuale in base all'immagine RHEL 7.2 deve iniziare.

  ![Creare macchine Virtuali - distribuzione avviata](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. Dopo alcuni minuti, la macchina virtuale devono essere distribuita correttamente e pronto per l'utilizzo.

  ![Creare macchine Virtuali - distribuito](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Quando in modalità remota connettere la macchina virtuale Linux appena provisioning
La macchina virtuale RHEL 7.2 è stato eseguito il provisioning in Azure. L'attività successiva consiste nel connettere la macchina virtuale quando in modalità remota.

**Connettersi alla macchina virtuale RHEL 7.2** Seguire le istruzioni vedere l'articolo [come effettuare l'accesso a una macchina virtuale che eseguono Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

Il resto dei passaggi presuppongono utilizzare il client PuTTY SSH per connettere la macchina virtuale RHEL. Per ulteriori informazioni, vedere la [pagina di Download PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Aprire il programma PuTTY.

2. Immettere il **Nome Host** per la macchina virtuale RHEL appena creata. In questo esempio, di virtual machine è il nome host "contoso rhel.cloudapp .net". Se non si conoscono il nome host delle macchine Virtuali, fare riferimento al dashboard di macchine Virtuali nel portale di Azure.

    ![Connettere puTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. Accedere al computer virtuali utilizzando le credenziali di amministratore locale specificato quando è stata creata la macchina virtuale. In questo esempio abbiamo usato l'account di amministratore locale "mahesh".

    ![Accesso puTTY](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installare pacchetti necessari sul computer virtuale Linux
Dopo la connessione al computer virtuale, l'attività successiva è necessario installare pacchetti necessari per l'aggiunta al dominio sul computer virtuale. Eseguire i passaggi seguenti:

1. **Installare realmd:** Il pacchetto realmd viene usato per l'aggiunta al dominio. Nel terminale PuTTY, digitare il comando seguente:

    Sudo slurp installa realmd

    ![Installare realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Dopo alcuni minuti, il pacchetto realmd dovrebbe ottenere installato nel computer virtuale.

    ![realmd installato](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **Installare sssd:** Il pacchetto realmd dipende dal sssd per eseguire le operazioni di join di dominio. Nel terminale PuTTY, digitare il comando seguente:

    Sudo slurp installa sssd

    ![Installare sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Dopo alcuni minuti, il pacchetto sssd dovrebbe ottenere installato nel computer virtuale.

    ![realmd installato](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **Installare kerberos:** Nel terminale PuTTY, digitare il comando seguente:

    Sudo slurp installa krb5 workstation krb5-librerie

    ![Installare kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    Dopo alcuni minuti, il pacchetto realmd dovrebbe ottenere installato nel computer virtuale.

    ![Kerberos installato](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Aggiungere la macchina virtuale Linux al dominio gestito
Ora che i pacchetti necessari sono installati nel computer virtuale Linux, l'attività successiva consiste nel aggiungere la macchina virtuale al dominio gestito.

1. Individuare il dominio gestito AAD servizi di dominio. Nel terminale PuTTY, digitare il comando seguente:

    alla scoperta di autenticazione Sudo CONTOSO100.COM

    ![Alla scoperta di autenticazione](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    Se **alla scoperta di autenticazione** non riesce a trovare il dominio gestito, assicurarsi che il dominio sia raggiungibile dal computer virtuale (provare ping). Assicurarsi che la macchina virtuale effettivamente è stata distribuita alla stessa rete virtuale in cui è disponibile il dominio gestito.

2. Inizializzare kerberos. Nel terminale PuTTY, digitare il comando seguente. Assicurarsi di specificare un utente che appartiene al gruppo 'AAD controller di dominio Administrators'. Solo gli utenti possono aggiungere computer al dominio gestito.

    kinitbob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Assicurarsi che specificare il nome di dominio in lettere maiuscole, kinit altro ha esito negativo.

3. Aggiungere il computer al dominio. Nel terminale PuTTY, digitare il comando seguente. Specificare lo stesso utente specificato nel passaggio precedente ('kinit').

    join dell'area di autenticazione Sudo - CONTOSO100.COM -U dettagliato'bob@CONTOSO100.COM'

    ![Join dell'area di autenticazione](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Viene visualizzato un messaggio ("iscritti viene eseguito correttamente al computer nell'area di autenticazione") quando il computer viene aggiunto al dominio gestito.


## <a name="verify-domain-join"></a>Verificare il join di dominio
È possibile verificare rapidamente se il computer è stato aggiunto al dominio gestito. Connettere il nuovo dominio RHEL VM utilizzando SSH e un account utente di dominio e quindi selezionare per vedere se l'account utente viene risolto correttamente.

1. Nel terminale PuTTY, digitare il comando seguente per connettersi al nuovo dominio macchina virtuale RHEL utilizzando SSH. Usare un account di dominio che appartiene al dominio gestito (ad esempio 'bob@CONTOSO100.COM' in questo caso.)

    SSH -l bob@CONTOSO100.COM rhel.cloudapp.net contoso

2. Nel terminale PuTTY, digitare il comando seguente per verificare se la home directory è stata inizializzata correttamente.

    pwd

3. Nel terminale PuTTY, digitare il comando seguente per verificare se le appartenenze ai gruppi sono stati risolti correttamente.

    ID

Di seguito è riportato un esempio di output di questi comandi:

![Verificare il join di dominio](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## <a name="troubleshooting-domain-join"></a>Risoluzione dei problemi aggiunta a un dominio
Fare riferimento all'articolo [aggiunta al dominio risoluzione dei problemi](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) .


## <a name="related-content"></a>Contenuti correlati
- [Servizi di dominio Active Directory Azure - Guida introduttiva](./active-directory-ds-getting-started.md)

- [Aggiungere una macchina virtuale di Windows Server a un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- Informazioni su [come effettuare l'accesso a una macchina virtuale che eseguono Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

- [L'installazione di Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)

- [Red Hat Enterprise Linux 7 - Guida all'integrazione di Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
