<properties
    pageTitle="Aggiungere un nuovo account tenant Azure Stack di Azure Active Directory | Microsoft Azure"
    description="Dopo la distribuzione di Microsoft Azure Stack di prova, è necessario creare account utente di almeno un tenant in modo che è possibile esplorare il portale tenant."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Aggiungere un nuovo account tenant Azure Stack di Azure Active Directory

Dopo [la distribuzione di prova di Stack Azure](azure-stack-run-powershell-script.md), è necessario un account utente tenant consente di esplorare il portale tenant e testare le offerte e piani. È possibile creare un account tenant tramite [il portale Azure](#create-an-azure-stack-tenant-account-using-the-azure-portal) o tramite [PowerShell](#create-an-azure-stack-tenant-account-using-powershell).

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Creare un account tenant Stack Azure tramite il portale di Azure

È necessario disporre di un abbonamento Azure a usare il portale di Azure.

1. Accedere a [Azure](http://manage.windowsazure.com).

2.  Nella barra di spostamento a sinistra di Microsoft Azure, fare clic su **Active Directory**.

3.  Nell'elenco, fare clic sulla cartella che si desidera utilizzare per Stack di Azure o crearne uno nuovo.

4.  In questa pagina directory, fare clic su **utenti**.

5.  Fare clic su **Aggiungi utente**.

6.  Nella procedura guidata **Aggiungi utente** , nell'elenco **tipo di utente** , scegliere **nuovo utente nell'organizzazione**.

7.  Nella casella **nome utente** digitare un nome per l'utente.

8.  Nel **@** , scegliere la voce appropriata.

9.  Fare clic sulla freccia avanti.

10.  Nella pagina **profilo utente** della procedura guidata, digitare un **nome**, **Cognome**e **nome visualizzato**.

11. Nell'elenco **ruolo** selezionare **utente**.

12. Fare clic sulla freccia avanti.

13. Nella pagina **Guida password temporanea** , fare clic su **Crea**.

14. Copiare la **nuova password**.

15. Accedere a Microsoft Azure con il nuovo account. Modificare la password quando richiesto.

16. Accedere a `https://portal.azurestack.local` con il nuovo account per visualizzare il portale tenant.

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>Creare un account tenant di Azure Stack tramite PowerShell

Se non si dispone di un abbonamento a Azure, non è possibile utilizzare il portale di Azure per aggiungere un account utente tenant. In questo caso, è possibile utilizzare invece di Azure Active Directory modulo per Windows PowerShell.

> [AZURE.NOTE] Se si usa l'Account Microsoft (Live ID) per la distribuzione di Azure Stack di prova, è possibile utilizzare PowerShell AAD per creare account del tenant. 

1.  Installare [Microsoft Online Services Assistente per l'accesso per i professionisti IT RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950).

2.  Installare il [Modulo Azure Active Directory per Windows PowerShell (versione a 64 bit)](http://go.microsoft.com/fwlink/p/?linkid=236297) e aprirlo.

3.  Eseguire i cmdlet seguenti:




    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack PoC
   
            $msolcred = get-credential
    
    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".
    
            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId
    
    ```

4.  Accedere a Microsoft Azure con il nuovo account. Modificare la password quando richiesto.

5.  Accedere a `https://portal.azurestack.local` con il nuovo account per visualizzare il portale tenant.



