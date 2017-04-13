<properties
    pageTitle="Utilizzare il controllo dell'accesso basato sui ruoli nel portale di Azure | Microsoft Azure"
    description="Guida introduttiva nella gestione degli accessi controllo dell'accesso basato sui ruoli nel portale di Azure. Utilizzare le assegnazioni di ruolo per assegnare le autorizzazioni per le risorse."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="use-role-assignments-to-manage-access-to-your-azure-subscription-resources"></a>Utilizzare le assegnazioni di ruolo per gestire l'accesso alle risorse dell'abbonamento Azure

> [AZURE.SELECTOR]
- [Gestire l'accesso utente o gruppo](role-based-access-control-manage-assignments.md)
- [Gestire l'accesso da risorsa](role-based-access-control-configure.md)

Azure basato sui ruoli accesso controllo (RBAC) consente la gestione degli accessi diffusamente per Azure. Usa RBAC, è possibile concedere solo la quantità di access che gli utenti devono eseguire il proprio lavoro. In questo articolo spiega come iniziare a utilizzarlo con RBAC nel portale di Azure. Se si desiderano ulteriori dettagli su come RBAC consente di gestire l'accesso, vedere [che cos'è controllo dell'accesso basato sui ruoli](role-based-access-control-what-is.md).

## <a name="view-access"></a>Visualizzazione di access
È possibile visualizzare chi ha accesso a una risorsa, gruppo di risorse o l'abbonamento da relativa blade principale nel [portale di Azure](https://portal.azure.com). Ad esempio, si desidera visualizzare chi ha accesso a uno dei gruppi di risorse:

1. Selezionare **i gruppi di risorse** sulla barra di spostamento a sinistra.  
    ![Gruppi di risorse - icona](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Selezionare il nome del gruppo di risorse da e il **gruppi di risorse** .
3. Selezionare **il controllo di accesso (IAM)** dal menu a sinistra.  
4. E il controllo di accesso sono elencati tutti gli utenti, gruppi e le applicazioni che sono stato concesso l'accesso al gruppo di risorse.  

    ![Blade utenti - vs ereditate assegnate schermata di accesso](./media/role-based-access-control-configure/view-access.png)

Si noti che alcuni utenti sono state **assegnate** accedervi mentre altri **ereditato** . L'accesso è assegnato specificamente al gruppo di risorse o ereditato da un'assegnazione alla sottoscrizione padre.

> [AZURE.NOTE] Gli amministratori di abbonamento classica e amministratori di co sono considerati proprietari della sottoscrizione nel nuovo modello RBAC.


## <a name="add-access"></a>Aggiungere l'accesso
Concedere l'accesso all'interno della risorsa, gruppo di risorse o l'abbonamento che costituisce l'ambito dell'assegnazione ruolo.

1. Scegliere **Aggiungi** e il controllo di accesso.  
2. Selezionare il ruolo che si desidera assegnare e il **Selezionare un ruolo** .
3. Selezionare l'utente, gruppo o dell'applicazione nella directory che si desidera concedere l'accesso al. È possibile eseguire una ricerca nella directory con nomi visualizzati, indirizzi di posta elettronica e gli identificatori di oggetto.  

    ![Aggiungere blade utenti: schermata di ricerca](./media/role-based-access-control-configure/grant-access2.png)

4. Fare clic su **OK** per creare l'assegnazione. Popup **Aggiunta utente** consente di tenere traccia dello stato di avanzamento.  
    ![Aggiunta utente indicatore - cattura di schermata](./media/role-based-access-control-configure/addinguser_popup.png)

Dopo l'aggiunta di un'assegnazione di ruolo correttamente, verrà visualizzato nella e **utenti** .

## <a name="remove-access"></a>Rimuovere l'accesso

1. Selezionare l'assegnazione di ruolo e il controllo di accesso.
2. Selezionare **Rimuovi** e il dettagli delle assegnazioni.  
3. Selezionare **Sì** per confermare la rimozione.  
    ![Blade utenti - Rimuovi dalla schermata di ruolo](./media/role-based-access-control-configure/remove-access1.png)

Non è possibile rimuovere le assegnazioni ereditate. Notare che il pulsante Rimuovi è in grigio nell'immagine seguente. Se, tuttavia, esaminare i dati di dettaglio **Assegnate al** . Passare alla risorsa è elencata per rimuovere l'assegnazione di ruolo.

![Rimozione di Disabilita accesso ereditate blade utenti - pulsante schermata](./media/role-based-access-control-configure/remove-access2.png)

## <a name="other-tools-to-manage-access"></a>Altri strumenti per gestire l'accesso
È possibile assegnare i ruoli e gestire l'accesso con i comandi di Azure RBAC negli strumenti diversa da quella portale Azure.  Seguire i collegamenti per ulteriori informazioni sui prerequisiti e iniziare a utilizzare i comandi di Azure RBAC.

- [PowerShell Azure](role-based-access-control-manage-access-powershell.md)
- [Azure interfaccia della riga di comando](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

## <a name="next-steps"></a>Passaggi successivi
- [Creare un report di cronologia delle modifiche di access](role-based-access-control-access-change-history-report.md)
- Vedere i [ruoli predefiniti RBAC](role-based-access-built-in-roles.md)
- Definire i propri [ruoli personalizzati in RBAC Azure](role-based-access-control-custom-roles.md)
