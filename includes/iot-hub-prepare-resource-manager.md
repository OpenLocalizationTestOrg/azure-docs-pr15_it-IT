## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Preparazione per autenticare le richieste di gestione di risorse di Azure

È necessario eseguire l'autenticazione di tutte le operazioni eseguite con [Gestione risorse di Azure] risorse[ lnk-authenticate-arm] con Azure Active Directory (AD). Per configurare questa impostazione in modo semplice consiste nell'utilizzare PowerShell o CLI Azure.

È consigliabile installare [Azure PowerShell 1.0] [ lnk-powershell-install] o versione successiva prima di continuare.

La procedura seguente viene illustrato come configurare l'autenticazione tramite password di un'applicazione di Active Directory utilizzando PowerShell. È possibile eseguire questi comandi in una sessione di PowerShell standard.

1. Eseguire l'accesso alla sottoscrizione Azure utilizzando il comando seguente:

    ```
    Login-AzureRmAccount
    ```

2. Prendere nota **dell'ID tenant** e **SubscriptionId**. Saranno necessari più avanti.

3. Creare una nuova applicazione di Azure Active Directory tramite il comando seguente, sostituire i segnaposto:

    - **{DisplayName}:** un nome visualizzato per l'applicazione, ad esempio **MySampleApp**
    - **{URL della home page}:** l'URL della home page della propria applicazione, ad esempio **http://mysampleapp/home**. Questo URL non è necessario in modo che punti a un'applicazione reale.
    - **{Application identifier}:** Identificatore univoco, ad esempio **http://mysampleapp**. Questo URL non è necessario in modo che punti a un'applicazione reale.
    - **{Password}:** Password che verrà utilizzato per eseguire l'autenticazione con le app.

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. Prendere nota dell' **ID applicazione** dell'applicazione che è stata creata. È necessario questo più avanti.

5. Creare un nuovo servizio principale utilizzando il comando seguente, sostituendo **{MyApplicationId}** con **ApplicationId** nel passaggio precedente:

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. Installazione di un'assegnazione di ruolo utilizzando il comando seguente, sostituendo **{MyApplicationId}** con l' **ID applicazione**.

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
Completata la creazione dell'applicazione di Azure Active Directory che consentirà di eseguire l'autenticazione dell'applicazione c# personalizzato. Più avanti in questa esercitazione sono necessari i seguenti valori:

- ID tenant
- SubscriptionId
- ApplicationId
- Password

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: ../articles/powershell-install-configure.md
