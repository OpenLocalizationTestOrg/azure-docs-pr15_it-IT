## <a name="obtain-an-azure-resource-manager-token"></a>Ottenere un token di Manager delle risorse di Azure

Azure Active Directory deve autenticare tutte le attività eseguite su risorse con Gestione risorse di Azure. L'esempio riportato di seguito viene utilizzata l'autenticazione tramite password, per altri metodi, vedere [le richieste di autenticazione Manager delle risorse di Azure][lnk-authenticate-arm].

1. Aggiungere il codice seguente al metodo **Main** in Program.cs per recuperare un token di Azure Active Directory utilizzando l'id applicazione e la password.

    ```
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.windows.net/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
    
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```

2. Creare un oggetto **ResourceManagementClient** che utilizza il token aggiungendo il codice seguente alla fine del metodo **Main** :

    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```

3. Creare o ottenere un riferimento al gruppo di risorse in uso:

    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx