## <a name="how-to-deploy-with-azure-cli"></a>Come distribuire con CLI Azure

1. Accedere al proprio account Azure.

        azure login

  Dopo avere fornito le credenziali, il comando restituisce il risultato di accesso dell'utente.

        ...
        info:    login command OK

2. Se si hanno più abbonamenti, fornire l'id di abbonamento che si desidera utilizzare per la distribuzione.

        azure account set <YourSubscriptionNameOrId>

3. Passare al modulo di gestione risorse di Azure

        azure config mode arm

   L'utente riceve conferma della nuova modalità.

        info:     New mode is arm

4. Se non si dispone di un gruppo di risorse esistente, creare un nuovo gruppo di risorse. Specificare il nome del gruppo di risorse e percorso in cui è necessario per la soluzione.

        azure group create -n ExampleResourceGroup -l "West US"

   Viene restituito un riepilogo di nuovo gruppo di risorse.

        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Per creare una nuova distribuzione per il gruppo di risorse, eseguire il comando seguente e fornire i parametri necessari. I parametri includerà un nome per la distribuzione, il nome del gruppo di risorse, il percorso o l'URL per il modello che è stato creato e altri parametri necessari per lo scenario.

   Sono disponibili le opzioni seguenti per fornire i valori dei parametri:

   - Utilizzare parametri all'interno del testo e un modello locale.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Utilizzare parametri all'interno del testo e un collegamento a un modello.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

   - Usare un file di parametri.

             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

  Quando il gruppo di risorse è stato distribuito, verrà visualizzato un riepilogo della distribuzione.

         info:    Executing command group deployment create
         + Initializing template configurations and parameters
         + Creating a deployment
         ...
         info:    group deployment create command OK


6. Per ottenere informazioni sulla distribuzione più recente.

         azure group log show -l ExampleResourceGroup

7. Per ottenere informazioni dettagliate sugli errori di distribuzione.

         azure group log show -l -v ExampleResourceGroup
