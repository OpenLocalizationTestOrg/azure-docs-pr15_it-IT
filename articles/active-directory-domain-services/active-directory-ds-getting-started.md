<properties
    pageTitle="Servizi di dominio Active Directory Azure: Creare il gruppo di amministratori controller di dominio AAD | Microsoft Azure"
    description="Guida introduttiva di servizi di dominio di Azure Active Directory"
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

# <a name="get-started-with-azure-ad-domain-services"></a>Guida introduttiva a servizi di dominio Active Directory di Azure

In questo articolo vengono descritte le attività di configurazione necessarie per attivare i servizi di dominio di Azure Active Directory per il tenant di Azure Active Directory.

## <a name="task-1-create-the-aad-dc-administrators-group"></a>Attività 1: Creare il gruppo 'AAD controller di dominio amministratori'
La prima attività consiste nel creare un gruppo amministrativo nel tenant di Azure Active Directory. In questo gruppo amministrativo speciale rappresenta la cosiddetta **AAD controller di dominio amministratori**. Membri di questo gruppo dispongono di privilegi di amministratore nel computer che appartengono al dominio al dominio gestito servizi di dominio Active Directory Azure. Computer dominio in questo gruppo viene aggiunto al gruppo "Administrators". Inoltre, membri di questo gruppo possono utilizzare Desktop remoto per connettersi in remoto al computer di dominio.  

> [AZURE.NOTE] Non si dispone dei privilegi di amministratore di dominio o amministratore dell'organizzazione nel dominio gestito creato con servizi di dominio Active Directory Azure. Sui domini gestiti, questi privilegi sono riservati dal servizio e non sono disponibili per gli utenti nel tenant. Tuttavia, è possibile utilizzare il gruppo amministratore speciale creato in questa attività di configurazione, eseguire alcune operazioni con privilegi. Queste operazioni includono aggiunta computer al dominio, che appartengono al gruppo Administrators nel computer, dominio configurazione criteri di gruppo e così via.

In questa attività di configurazione, creare il gruppo amministrativo e aggiungere uno o più utenti nella directory al gruppo. Per creare il gruppo amministrativo per i servizi di dominio Active Directory di Azure, procedere come segue:

1. Passare al **portale classica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com))

2. Selezionare il nodo **Active Directory** nel riquadro sinistro.

3. Selezionare il tenant di Azure Active Directory (directory) per il quale si desidera attivare servizi di dominio Active Directory Azure. È possibile creare solo un dominio per ogni directory di Azure Active Directory.

    ![Selezionare Azure Active Directory](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Fare clic sulla scheda **gruppi** .

5. Per aggiungere un gruppo al tenant di Azure Active Directory, fare clic su **Aggiungi gruppo** dal riquadro attività nella parte inferiore della pagina.

    ![Pulsante di gruppo](./media/active-directory-domain-services-getting-started/add-group-button.png)

6. Creare un gruppo denominato **AAD controller di dominio Administrators**. Impostare **tipo di gruppo** di **sicurezza**.

    > [AZURE.WARNING] Per abilitare l'accesso all'interno dei servizi di dominio Active Directory Azure gestito dominio, creare un gruppo con lo stesso nome.

    ![Creare l'amministratore del gruppo](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Aggiungere una descrizione per il gruppo in modo che altri utenti comprendere il gruppo viene utilizzato per concedere privilegi amministrativi all'interno di servizi di dominio Active Directory Azure.

8. Dopo aver creato il gruppo, fare clic sul nome del gruppo per visualizzare le proprietà di questo gruppo. Per aggiungere utenti ai membri di questo gruppo, fare clic sul pulsante **Aggiungi membri** nel riquadro inferiore.

    ![Gruppo membri pulsante Aggiungi](./media/active-directory-domain-services-getting-started/add-group-members-button.png)

9. Nella finestra di dialogo **Aggiungi membri** , selezionare gli utenti che devono essere membri del gruppo e al termine, selezionare la casella di controllo.

    ![Aggiungere utenti al gruppo amministrativo](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## <a name="task-2-create-or-select-an-azure-virtual-network"></a>Attività 2: Creare o selezionare una rete virtuale Azure
L'attività di configurazione successivo consiste nel [creare o selezionare una rete virtuale Azure](active-directory-ds-getting-started-vnet.md).
