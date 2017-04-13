<properties
    pageTitle="Azure Active Directory servizi di dominio: Amministrare il DNS su domini gestiti | Microsoft Azure"
    description="Amministrare il DNS su domini gestiti di servizi di dominio di Azure Active Directory"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Amministrare DNS su un dominio gestito di servizi di dominio Active Directory di Azure
Azure i servizi di dominio Active Directory include un server DNS (risoluzione dei nomi di dominio) che fornisce la risoluzione DNS relativa al dominio gestito. In alcuni casi, potrebbe essere necessario configurare il sistema DNS nel dominio gestito. Potrebbe essere necessario creare record DNS per i computer che non appartengono al dominio, configurare indirizzi IP virtuali per servizi di bilanciamento del carico o configurazione server d'inoltro DNS esterni. Per questo motivo, gli utenti che appartengono al gruppo 'AAD controller di dominio Administrators' dispone di privilegi di amministratore DNS nel dominio gestito.


## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo, è necessario:

1. Una **sottoscrizione Azure**valida.

2. Una **directory di Azure Active Directory** - uno sincronizzata con una directory locale o basata solo su cloud.

3. **Servizi di dominio Active Directory Azure** deve essere abilitata per la directory di Azure Active Directory. Se non è ancora stato fatto, seguire tutte le attività descritte nella [Guida introduttiva](./active-directory-ds-getting-started.md).

4. Un **dominio macchina virtuale** da cui si amministra i servizi di dominio Active Directory Azure gestite dominio. Se non si dispone ad esempio una macchina virtuale, seguire tutte le attività descritte nell'articolo intitolato [partecipare a una macchina virtuale di Windows a un dominio gestito](./active-directory-ds-admin-guide-join-windows-vm.md).

5. È necessario le credenziali di un **account utente che appartengono al gruppo ' AAD controller di dominio Administrators'** nella directory di amministrazione di DNS per il dominio gestito.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Attività 1: effettuare il provisioning di una macchina virtuale dominio per amministrare DNS relativa al dominio gestito
Azure domini gestiti di servizi di dominio Active Directory possono essere gestiti in remoto utilizzando familiari strumenti di amministrazione di Active Directory, ad esempio la Active Directory Amministrazione centrale (ADAC) o PowerShell di Active Directory. Analogamente, è possibile amministrare DNS relativa al dominio gestito in remoto tramite gli strumenti di amministrazione di Server DNS.

Gli amministratori nella directory di Azure Active Directory non dispongano di privilegi per connettersi a controller di dominio nel dominio gestito tramite Desktop remoto. I membri del gruppo 'AAD controller di dominio Administrators' possono amministrare DNS per i domini gestiti in remoto tramite gli strumenti di Server DNS da un computer client/Server Windows appartenente a dominio gestito. Strumenti di Server DNS possono essere installati come parte della caratteristica facoltativa remoto Server strumenti di amministrazione in Windows Server e computer client al dominio gestito.

La prima attività è effettuare il provisioning di una macchina virtuale di Windows Server appartenente a dominio gestito. Per istruzioni, vedere l'articolo intitolato [partecipare a una macchina virtuale di Windows Server a un dominio di servizi di dominio Active Directory di Azure gestiti](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Attività 2 - strumenti installare DNS Server sul computer virtuale
Eseguire la procedura seguente per installare gli strumenti di amministrazione DNS nella macchina virtuale di dominio. Per ulteriori informazioni [sull'installazione e utilizzo di strumenti di amministrazione Server remoto](https://technet.microsoft.com/library/hh831501.aspx), vedere il sito Technet.

1. Passare al livello di nodo **macchine virtuali** nel portale di classica Azure. Selezionare la macchina virtuale creata nel passaggio 1 e fare clic su **Connetti** nella barra dei comandi nella parte inferiore della finestra.

    ![Connettersi a macchina virtuale di Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Il portale classico viene richiesto di aprire o salvare un file con estensione 'RDP', che consente di connettere la macchina virtuale. Fare clic sul file volta completato il download.

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

9. Nella pagina **caratteristiche** fare clic per espandere il nodo **Strumenti di amministrazione Server remoto** e quindi fare clic per espandere il nodo **Strumenti di amministrazione ruoli** . Selezionare **Gli strumenti Server DNS** caratteristica dall'elenco di strumenti di amministrazione ruoli.

    ![Pagina delle caratteristiche](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

10. Nella pagina di **Conferma** , fare clic su **Installa** per installare la funzionalità di strumenti di Server DNS nella macchina virtuale. Durante l'installazione della caratteristica viene completata correttamente, fare clic su **Chiudi** per chiudere la procedura guidata **Aggiungi ruoli e funzionalità** .

    ![Pagina di conferma](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)


## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Attività 3 - avvio console di gestione DNS per amministrare DNS
Ora che la caratteristica strumenti Server DNS è installata nel dominio macchina virtuale, è possibile utilizzare gli strumenti DNS per amministrare DNS nel dominio gestito.

> [AZURE.NOTE] È necessario essere membri del gruppo 'AAD controller di dominio amministratori' amministrare DNS nel dominio gestito.

1. Dalla schermata Start fare clic su **Strumenti di amministrazione**. Verrà visualizzata la console **DNS** è installata nel computer virtuale.

    ![Strumenti di amministrazione - Console DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)

2. Fare clic su **DNS** per avviare la console di gestione DNS.

3. Nella finestra di dialogo **connessione al Server DNS** fare clic sull'opzione intitolato **seguente computer**e immettere il nome di dominio DNS del dominio gestito (ad esempio, ' contoso100.com').

    ![Console DNS - connettersi al dominio](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

4. La Console DNS si connette al dominio gestito.

    ![Console DNS - amministrare dominio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

5. È ora possibile usare la console DNS per aggiungere voci DNS per i computer all'interno della rete virtuale in cui è stata abilitata AAD servizi di dominio.

> [AZURE.WARNING] Prestare attenzione quando si amministra DNS relativa al dominio gestito tramite gli strumenti di amministrazione DNS. Verificare che si **non eliminare o modificare i record DNS predefiniti che vengono utilizzati da servizi di dominio nel dominio**. Record DNS predefiniti includono record DNS del dominio, record server dei nomi e gli altri record utilizzato per posizione controller di dominio. Se si modificano questi record, vengono interrotti servizi di dominio della rete virtuale.


Vedere l' [articolo strumenti DNS in Technet](https://technet.microsoft.com/library/cc753579.aspx) per ulteriori informazioni sulla gestione DNS.


## <a name="related-content"></a>Contenuti correlati

- [Servizi di dominio Active Directory Azure - Guida introduttiva](./active-directory-ds-getting-started.md)

- [Aggiungere una macchina virtuale di Windows Server a un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-join-windows-vm.md)

- [Amministrare un dominio gestito di servizi di dominio Active Directory di Azure](active-directory-ds-admin-guide-administer-domain.md)

- [Strumenti di amministrazione di DNS](https://technet.microsoft.com/library/cc753579.aspx)
