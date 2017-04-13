## <a name="download-and-understand-the-arm-template"></a>Scaricare e comprendere il modello ARM

È possibile scaricare il modello ARM esistente per la creazione di un VNet con due subnet da github, apportare le modifiche potrebbero desiderata e riutilizzare i dati. A tale scopo, eseguire la procedura seguente.

1. Passare alla [pagina il modello di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Fare clic su **azuredeploy.json**e quindi fare clic su **materie**.
3. Salvare il file a un una cartella locale nel computer in uso.
4. Se si ha familiarità con i modelli ARM, andare al passaggio 7.
5. Aprire il file che appena salvato e visualizzare il contenuto in **parametri** nella riga 5. I parametri del modello ARM offrono un segnaposto per i valori che possono essere compilati durante la distribuzione.

    | Parametro | Descrizione |
    |---|---|
    | **posizione** | Azure area geografica in cui verrà creato il VNet |
    | **vnetName** | Nome per il nuovo VNet |
    | **addressPrefix** | Spazio di indirizzi per VNet, in formato CIDR |
    | **subnet1Name** | Nome per la prima VNet |
    | **subnet1Prefix** | Blocco CIDR per la prima subnet |
    | **subnet2Name** | Nome per la seconda VNet |
    | **subnet2Prefix** | Blocco CIDR per la seconda subnet |

    >[AZURE.IMPORTANT] Modelli ARM mantenuti nel github possono cambiare nel tempo. Accertarsi di controllare il modello prima di usarlo.
    
6. Controllare il contenuto in **risorse** e tenere presente quanto segue:

    - **tipo**. Tipo di risorsa che viene creato dal modello. In questo caso, **Microsoft.Network/virtualNetworks**, che rappresentano un VNet.
    - **nome**. Nome della risorsa. Si noti l'utilizzo di **[parameters('vnetName')]**, vale a dire il nome verrà specificato come input dall'utente o un file di parametri durante la distribuzione.
    - **proprietà**. Elenco delle proprietà per la risorsa. Questo modello utilizza le proprietà di spazio e subnet dell'indirizzo durante la creazione di VNet.

7. Passare alla [pagina il modello di esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Fare clic su **azuredeploy paremeters.json**e quindi fare clic su **materie**.
9. Salvare il file a un una cartella locale nel computer in uso.
10. Aprire il file che appena salvato e modificare i valori per i parametri. Utilizzare valori riportati di seguito per distribuire VNet descritto in questo scenario.

        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }

11. Salvare il file.
  