<properties
    pageTitle="Azure Active Directory servizi di dominio: Amministrare un dominio gestito | Microsoft Azure"
    description="Amministrare domini gestiti di servizi di dominio di Azure Active Directory"
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

# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Amministrare un dominio gestito di servizi di dominio di Azure Active Directory
In questo articolo viene illustrato come amministrare un dominio gestito di servizi di dominio di Azure Active Directory (AD).


## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo, è necessario:

1. Una **sottoscrizione Azure**valida.

2. Una **directory di Azure Active Directory** - uno sincronizzata con una directory locale o basata solo su cloud.

3. **Servizi di dominio Active Directory Azure** deve essere abilitata per la directory di Azure Active Directory. Se non è ancora stato fatto, seguire tutte le attività descritte nella [Guida introduttiva](./active-directory-ds-getting-started.md).

4. Un **dominio macchina virtuale** da cui si amministra i servizi di dominio Active Directory Azure gestite dominio. Se non si dispone ad esempio una macchina virtuale, seguire tutte le attività descritte nell'articolo intitolato [partecipare a una macchina virtuale di Windows a un dominio gestito](./active-directory-ds-admin-guide-join-windows-vm.md).

5. È necessario le credenziali di un **account utente che appartengono al gruppo ' AAD controller di dominio Administrators'** directory per amministrare il dominio gestito.

<br>


## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Attività amministrative che è possibile eseguire su un dominio gestito
I membri del gruppo 'AAD controller di dominio Administrators' vengono concessi i privilegi per il dominio gestito che consentono loro di eseguire attività come:

- Aggiungere computer al dominio gestito.

- Configurare l'oggetto Criteri di gruppo predefiniti per i contenitori "AADDC computer" e "AADDC utenti" nel dominio gestito.

- Amministrare DNS nel dominio gestito.

- Creare e gestire personalizzato organizzative () nel dominio gestito.

- Ottenere l'accesso amministrativo a computer aggiunto al dominio gestito.


## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Non si dispone di un dominio gestito privilegi di amministratore
Il dominio viene gestito da Microsoft, comprese le attività, ad esempio patch, monitoraggio e l'esecuzione di backup. Di conseguenza, il dominio è bloccato e non si dispone dei privilegi per eseguire alcune attività amministrative per il dominio. Alcuni esempi di attività che non è possibile eseguire sono inferiori.

- Non si dispone i privilegi di amministratore di dominio o amministratore dell'organizzazione per il dominio gestito.

- Non è possibile estendere lo schema del dominio gestito.

- Non è possibile connettersi a controller di dominio per il dominio gestito tramite Desktop remoto.

- È possibile aggiungere controller di dominio al dominio gestito.


## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Attività 1: effettuare il provisioning di una macchina virtuale di Windows Server dominio per amministrare il dominio gestito
Azure domini gestiti di servizi di dominio Active Directory possono essere gestiti usando familiari strumenti di amministrazione di Active Directory, ad esempio la Active Directory Amministrazione centrale (ADAC) o PowerShell di Active Directory. Amministratori tenant non dispongano di privilegi per connettersi a controller di dominio nel dominio gestito tramite Desktop remoto. Di conseguenza, i membri del gruppo 'AAD controller di dominio Administrators' possono amministrare domini gestiti in remoto utilizzando strumenti di amministrazione di Active Directory da un computer client/Server Windows appartenente a dominio gestito. Strumenti di amministrazione di Active Directory possono essere installati come parte della caratteristica facoltativa remoto Server strumenti di amministrazione in Windows Server e computer client al dominio gestito.

Il primo passaggio consiste nel configurare una macchina virtuale di Windows Server appartenente a dominio gestito. Per istruzioni, vedere l'articolo intitolato [partecipare a una macchina virtuale di Windows Server a un dominio di servizi di dominio Active Directory di Azure gestiti](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Amministrare il dominio gestito da un computer client (ad esempio Windows 10)
Le istruzioni fornite in questo articolo utilizzano una macchina virtuale di Windows Server per amministrare AAD-DS gestite dominio. Tuttavia, è possibile scegliere di utilizzare una macchina virtuale di Windows client (ad esempio Windows 10) per farlo.

È possibile [installare Remote Server strumenti di amministrazione](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) in un computer virtuale di Windows client seguendo le istruzioni disponibili in TechNet.


## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Attività 2 - gli strumenti di amministrazione di Active Directory di installazione del computer virtuale
Eseguire la procedura seguente per installare gli strumenti di amministrazione di Active Directory sul computer virtuale di dominio. Per ulteriori [informazioni sull'installazione e utilizzo degli strumenti di amministrazione Server remoto](https://technet.microsoft.com/library/hh831501.aspx), vedere Technet.

1. Passare al livello di nodo **macchine virtuali** nel portale di classica Azure. Selezionare la macchina virtuale creata nel passaggio 1 e fare clic su **Connetti** nella barra dei comandi nella parte inferiore della finestra.

    ![Connettersi a macchina virtuale di Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Il portale classico viene richiesto di aprire o salvare un file con estensione 'RDP', che consente di connettere la macchina virtuale. Fare clic per aprire il file dopo aver terminato il download.

3. Al prompt dei comandi di accesso, utilizzare le credenziali di un utente che appartengono al gruppo 'AAD controller di dominio Administrators'. Ad esempio, verranno utilizzati 'bob@domainservicespreview.onmicrosoft.com' in questo caso.

4. Dalla schermata Start aprire **Server Manager**. Fare clic su **Aggiungi ruoli e funzionalità** nel riquadro centrale della finestra di Server Manager.

    ![Avviare Server Manager sul computer virtuale](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. Nella pagina di **aggiunta di ruoli e guidata funzionalità** **Prima di iniziare** , fare clic su **Avanti**.

    ![Prima di iniziare a pagina](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. Nella pagina **Tipo di installazione** , lasciare l'opzione di **installazione basato sui ruoli o basato su feature** selezionata e fare clic su **Avanti**.

    ![Pagina tipo di installazione](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. Nella pagina **Selezione Server** selezionare la macchina virtuale corrente del pool di server e fare clic su **Avanti**.

    ![Pagina di selezione dei server](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. Nella pagina **Ruoli Server** , fare clic su **Avanti**. Abbiamo Ignora questa pagina poiché è stiamo non viene installato alcun ruolo nel server.

9. Nella pagina **caratteristiche** fare clic per espandere il nodo **Strumenti di amministrazione Server remoto** e quindi fare clic per espandere il nodo **Strumenti di amministrazione ruoli** . Selezionare la caratteristica **di dominio Active Directory e AD LDS** dall'elenco di strumenti di amministrazione ruoli.

    ![Pagina delle caratteristiche](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)

10. Nella pagina di **Conferma** , fare clic su **Installa** per installare Active Directory e funzionalità di strumenti AD LDS sul computer virtuale. Durante l'installazione della caratteristica viene completata correttamente, fare clic su **Chiudi** per chiudere la procedura guidata **Aggiungi ruoli e funzionalità** .

    ![Pagina di conferma](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)


## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Attività 3 - connettersi ed esplorare il dominio gestito
Ora che gli strumenti di amministrazione di Active Directory sono installati nel dominio macchina virtuale, è possibile utilizzare questi strumenti per esplorare e amministrare il dominio gestito.

> [AZURE.NOTE] È necessario essere membri del gruppo 'AAD controller di dominio amministratori' per l'amministrazione del dominio gestito.

1. Dalla schermata Start fare clic su **Strumenti di amministrazione**. Verranno visualizzati gli strumenti di amministrazione di Active Directory è installati nel computer virtuale.

    ![Strumenti di amministrazione installati sul server](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Fare clic su **Centro amministrativo di Active Directory**.

    ![Centro di amministrazione di Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Per esplorare il dominio, fare clic sul nome di dominio nel riquadro a sinistra (ad esempio, ' contoso100.com'). Si noti due contenitori denominati rispettivamente "AADDC computer" e "AADDC utenti".

    ![ADAC - Visualizza dominio](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)

4. Fare clic sul contenitore denominato **Utenti AADDC** per visualizzare tutti gli utenti e gruppi appartenenti al dominio gestito. Verrà visualizzato agli account utente e gruppi da un annuncio Azure tenant Mostra la in questo contenitore. Avviso in questo esempio, un account utente per l'utente chiamato 'bob' e un gruppo denominato 'AAD controller di dominio Administrators' sono disponibili in questo contenitore.

    ![ADAC - gli utenti del dominio](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)

5. Fare clic su contenitore denominato **AADDC computer** per visualizzare i computer connessi al dominio gestito. Verrà visualizzata una voce per il computer virtuale corrente, che viene aggiunto al dominio. Account computer per tutti i computer che fanno parte di dominio gestito servizi di dominio Active Directory Azure sono archiviate nel contenitore "AADDC computer".

    ![ADAC - dominio computer](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Contenuti correlati

- [Servizi di dominio Active Directory Azure - Guida introduttiva](./active-directory-ds-getting-started.md)

- [Aggiungere una macchina virtuale di Windows Server a un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Distribuire strumenti di amministrazione Server remoto](https://technet.microsoft.com/library/hh831501.aspx)
