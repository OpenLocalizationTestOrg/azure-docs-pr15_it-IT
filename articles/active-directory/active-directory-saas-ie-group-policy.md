<properties
    pageTitle="Come distribuire l'estensione del Pannello di accesso per Internet Explorer tramite criteri di gruppo | Microsoft Azure"
    description="Modalità di utilizzo dei criteri di gruppo per distribuire il componente aggiuntivo di Internet Explorer per il portale di App personali."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/16/2016"
    ms.author="markvi"/>

#<a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Come distribuire l'estensione del Pannello di accesso per Internet Explorer tramite criteri di gruppo

In questa esercitazione viene illustrato come utilizzare criteri di gruppo per l'installazione remota l'estensione del Pannello di accesso per Internet Explorer nei computer degli utenti. Questa estensione è necessaria per gli utenti di Internet Explorer per accedere all'App che possono essere configurate procedendo [basate su password servizio single sign-on](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

È consigliabile che gli amministratori di automatizzare l'implementazione di questa estensione. In caso contrario, sarà necessario scaricare e installare l'estensione, che è soggetta a errori dell'utente e richiede autorizzazioni di amministratore. In questa esercitazione illustra un metodo per automatizzare le distribuzioni di software tramite criteri di gruppo. [Ulteriori informazioni sui criteri di gruppo.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

È disponibile per [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) e [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), nessuno dei due richieda autorizzazioni di amministratore per installare anche l'estensione del Pannello di accesso.

##<a name="prerequisites"></a>Prerequisiti

- È stata configurata [Servizi di dominio Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e si aggiungono computer degli utenti al proprio dominio.
- È necessario disporre dell'autorizzazione "Modifica impostazioni" per modificare oggetti Criteri gruppo (). Per impostazione predefinita, i membri dei gruppi di sicurezza seguenti hanno questa autorizzazione: gli amministratori di dominio, gli amministratori dell'organizzazione e proprietari autori criteri di gruppo. [Ulteriori informazioni.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##<a name="step-1-create-the-distribution-point"></a>Passaggio 1: Creare il punto di distribuzione

Prima di tutto, è necessario inserire il programma di installazione in un percorso di rete che è possibile accedervi da tutti i computer che si desidera installare in remoto l'estensione in. A tale scopo, procedere come segue:

1. Accedere al server come amministratore

2. Nella finestra **Server Manager** , passare a **file e servizi di archiviazione**.

    ![Servizi di archiviazione e i file aperti](./media/active-directory-saas-ie-group-policy/files-services.png)

3. Passare alla scheda **Azioni** . Quindi fare clic su **attività** > **Nuova condivisione...**

    ![Servizi di archiviazione e i file aperti](./media/active-directory-saas-ie-group-policy/shares.png)

4. Completare la **Creazione guidata nuova condivisione** e impostare le autorizzazioni per verificare che sia accessibile dal computer degli utenti. [Ulteriori informazioni sulle azioni.](https://technet.microsoft.com/library/cc753175.aspx)

5. Scaricare il pacchetto di Microsoft Windows Installer (file con estensione msi) seguente: [Accesso pannello Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)

6. Copiare il programma di installazione nella posizione desiderata per la condivisione.

    ![Copiare il file con estensione msi per l'utente è l'opzione Condividi.](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. Verificare che il computer client in grado di accedere il programma di installazione dalla condivisione. 

##<a name="step-2-create-the-group-policy-object"></a>Passaggio 2: Creare l'oggetto Criteri di gruppo

1. Accedere al server che ospita l'installazione di servizi di dominio Active Directory (AD DS).

2. In Server Manager, fare clic su **Strumenti** > **Gestione criteri di gruppo**.

    ![Passare a strumenti > Gestione di criteri di gruppo](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. Nel riquadro sinistro della finestra di **Gestione dei criteri di gruppo** , visualizzare la gerarchia di unità Organizzativa e determinare nell'ambito della quale si desidera applicare i criteri di gruppo. Ad esempio, è possibile decidere di selezionare un'unità Organizzativa piccole per distribuire per alcuni utenti per la verifica o si potrebbe scegliere un'unità Organizzativa principale per distribuire l'intera organizzazione.

    > [AZURE.NOTE] Se si desidera creare o modificare le unità organizzative (OU), passare nuovamente per gestione di Server e fare clic su **Strumenti** > **Active Directory utenti e computer**.

4. Dopo aver selezionato un'unità Organizzativa, pulsante destro del mouse su di esso e selezionare **Crea un oggetto Criteri di gruppo in questo dominio e collegarlo qui...**

    ![Creare un nuovo oggetto Criteri di gruppo](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. Il **Nuovo oggetto Criteri di gruppo** al prompt dei comandi digitare un nome per il nuovo oggetto Criteri di gruppo.

    ![Assegnare un nome nuovo oggetto Criteri di gruppo](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. Pulsante destro del mouse sull'oggetto Criteri di gruppo appena creato e selezionare **Modifica**.

    ![Modificare il nuovo oggetto](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##<a name="step-3-assign-the-installation-package"></a>Passaggio 3: Assegnare il pacchetto di installazione

1. Determinare se si desidera distribuire l'estensione in base a **Configurazione di Computer** o **Configurazione utente**. Quando si usa [Configurazione computer](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), l'estensione verrà installato nel computer indipendentemente dall'utente che effettua l'accesso. Mano, con la [Configurazione utente](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), gli utenti avranno l'estensione installato per poterli indipendentemente dal computer che eseguono l'accesso.

2. Nel riquadro sinistro della finestra di **Gestione dei criteri di gruppo** , passare a uno dei seguenti percorsi di cartella, a seconda del tipo di configurazione si è scelto:
    - `Computer Configuration/Policies/Software Settings/`
    - `User Configuration/Policies/Software Settings/`

3. Fare clic su **installazione del Software**e quindi selezionare **Nuovo** > **pacchetto...**

    ![Creare un nuovo pacchetto di installazione del software](./media/active-directory-saas-ie-group-policy/new-package.png)

4. Passare alla cartella condivisa contenente il programma di installazione da [passaggio 1: creare il punto di distribuzione](#step-1-create-the-distribution-point), selezionare il file MSI e fare clic su **Apri**.

    > [AZURE.IMPORTANT] Se l'opzione Condividi si trova nello stessa server, verificare che si accede al file MSI tramite il percorso del file di rete, anziché il percorso del file locale.

    ![Selezionare il pacchetto di installazione dalla cartella condivisa.](./media/active-directory-saas-ie-group-policy/select-package.png)

5. Prompt dei comandi **Distribuisci** selezionare **assegnato** per il metodo di distribuzione. Fare clic su **OK**.

    ![Selezionare assegnato, quindi fare clic su OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

L'estensione a questo punto viene distribuito all'unità organizzativa selezionata. [Altre informazioni sull'installazione di Software di criteri di gruppo.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##<a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Passaggio 4: Abilitazione automatica l'estensione per Internet Explorer 

Oltre a eseguire il programma di installazione, ogni estensione per Internet Explorer deve essere abilitato in modo esplicito prima che può essere utilizzato. Seguire la procedura seguente per attivare l'estensione del Pannello di accesso tramite criteri di gruppo:

1. Nella finestra di **Gestione dei criteri di gruppo** , passare a uno dei seguenti percorsi, a seconda del tipo di configurazione si è scelto di [passaggio 3: assegnare il pacchetto di installazione](#step-3-assign-the-installation-package):
    - `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. Fare clic su **Elenco componenti aggiuntivi**e selezionare **Modifica**.
    ![Modificare l'elenco dei componenti aggiuntivi.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. Nella finestra **Elenco componenti aggiuntivi** selezionare **attivato**. Quindi, nella sezione **Opzioni** , fare clic su **Mostra...**.

    ![Fare clic su attiva e quindi fare clic su Mostra...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. Nella finestra **Mostra contenuti** , procedere come segue:

    1. Per la prima colonna (campo **Nome del valore** ), copiare e incollare l'ID di classe seguenti:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

    2. Per la seconda colonna (campo **valore** ), digitare il valore seguente:`1`

    3. Fare clic su **OK** per chiudere la finestra **Mostra contenuti** .

    ![Compilare i valori come specificato in precedenza.](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. Fare clic su **OK** per applicare le modifiche e chiudere la finestra **Elenco componenti aggiuntivi** .

L'estensione ora deve essere abilitata per i computer nell'unità Organizzativa selezionata. [Ulteriori informazioni sull'utilizzo dei criteri di gruppo per abilitare o disabilitare i componenti aggiuntivi di Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

##<a name="step-5-optional-disable-remember-password-prompt"></a>Passaggio 5 (facoltativo): disattivare "Memorizza Password" prompt dei comandi

Quando gli utenti accesso aggiuntivo ai siti Web con l'estensione del Pannello di accesso, è possibile che Internet Explorer visualizzi il seguente messaggio che chiede "Si desidera memorizzare la password?"

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Se si desidera impedire agli utenti di visualizzare questo messaggio, quindi eseguire la procedura seguente per evitare che il completamento automatico da ricordare password:

1. Nella finestra di **Gestione dei criteri di gruppo** , passare al percorso elencato di seguito. Si noti che questa impostazione è disponibile solo nella **Configurazione utente**.
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`

2. Cercare l'impostazione denominata **attivare la caratteristica Completamento automatico per nomi utente e password nei moduli**.

    > [AZURE.NOTE] Questa impostazione con il nome **non consentono di completamento automatico per salvare le password**vengano elencate in versioni precedenti di Active Directory. La configurazione dell'impostazione diversa da quella descritta in questa esercitazione.

    ![È necessario ricordare di cercare in questo gruppo impostazioni utente.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)

3. Fare clic con il pulsante destro sull'impostazione precedente e selezionare **Modifica**.

4. Nella finestra intitolata **attivare la caratteristica Completamento automatico per i nomi utente e password sui moduli**, selezionare **disabilitato**.

    ![Selezionare Disabilita](./media/active-directory-saas-ie-group-policy/disable-passwords.png)

5. Fare clic su **OK** per applicare le modifiche e chiudere la finestra.

Gli utenti non saranno possibile archiviare le credenziali o utilizzare completamento automatico per accedere alle credenziali memorizzate in precedenza. Tuttavia, questo criterio consente agli utenti di continuare a utilizzare completamento automatico per altri tipi di campi del modulo, ad esempio campi di ricerca.

> [AZURE.WARNING] Se questo criterio è attivato dopo che gli utenti sono scelto di memorizzare alcune credenziali, questo verrà criteri *non* deselezionare le credenziali già archiviato.

##<a name="step-6-testing-the-deployment"></a>Passaggio 6: Test della distribuzione

Seguire la procedura seguente per verificare se è stata completata la distribuzione di un'estensione:

1. Se è stata distribuita mediante **Configurazione Computer**, eseguire l'accesso a un computer client appartenente all'unità organizzativa selezionata nel [passaggio 2: creare l'oggetto Criteri di gruppo](#step-2-create-the-group-policy-object). Se è stata distribuita utilizzando **Configurazione utente**, assicurarsi che per l'accesso come utente che appartiene a tale OU.

2. Potrebbe richiedere qualche accesso aggiuntivi per i criteri di gruppo diventa completamente aggiornare con questo computer. Per forzare l'aggiornamento, aprire una finestra del **prompt dei comandi** ed eseguire il comando seguente:`gpupdate /force`

3. È necessario riavviare il computer per l'installazione da eseguire sul posto. Avvio può richiedere più tempo rispetto a installazioni comune durante l'estensione.

4. Dopo il riavvio, aprire **Internet Explorer**. Nell'angolo superiore destro della finestra, fare clic su **Strumenti** (icona a forma di ingranaggio) e quindi selezionare **Gestione componenti aggiuntivi**.

    ![Passare a strumenti > Gestione componenti aggiuntivi](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. Nella finestra **Gestione componenti aggiuntivi** , verificare che l' **Estensione del Pannello di accesso** è stato installato e che il suo **stato** è stato impostato su **attivato**.

    ![Verificare che l'estensione del Pannello di accesso sia installato e attivato.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Risoluzione dei problemi l'estensione del Pannello di accesso per Internet Explorer](active-directory-saas-ie-troubleshooting.md)
