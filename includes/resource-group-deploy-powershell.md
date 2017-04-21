## <a name="how-to-deploy-with-powershell"></a>Come distribuire con PowerShell

1. Accedere al proprio account Azure.

          Add-AzureAccount

   Dopo avere fornito le credenziali, il comando restituisce le informazioni sull'account.

          Id                             Type       ...
          --                             ----    
          example@contoso.com            User       ...   

2. Se si hanno più abbonamenti, fornire l'id di abbonamento che si desidera utilizzare per la distribuzione. 

          Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Passare al modulo di gestione di risorse Azure.

          Switch-AzureMode AzureResourceManager

4. Se non si dispone di un gruppo di risorse esistente, creare un nuovo gruppo di risorse. Specificare il nome del gruppo di risorse e percorso in cui è necessario per la soluzione.

        New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"

   Viene restituito un riepilogo di nuovo gruppo di risorse.

        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Per creare una nuova distribuzione per il gruppo di risorse, eseguire il comando **Nuovo AzureResourceGroupDeployment** e fornire i parametri necessari. I parametri includerà un nome per la distribuzione, il nome del gruppo di risorse, il percorso o l'URL per il modello che è stato creato e altri parametri necessari per lo scenario. 
   
   Sono disponibili le opzioni seguenti per fornire i valori dei parametri: 
   
   - Utilizzare parametri all'interno del testo.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

   - Utilizzare un oggetto parametro.

            $parameters = @{"<ParameterName>"="<Parameter Value>"}
            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

   - Utilizzo di un file di parametri.

            New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

  Quando il gruppo di risorse è stato distribuito, verrà visualizzato un riepilogo della distribuzione.

             DeploymentName    : ExampleDeployment
             ResourceGroupName : ExampleResourceGroup
             ProvisioningState : Succeeded
             Timestamp         : 4/14/2015 7:00:27 PM
             Mode              : Incremental
             ...

6. Per ottenere informazioni sugli errori di distribuzione.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Per ottenere informazioni dettagliate sugli errori di distribuzione.

        Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput
