Azure Gestione risorse, definire i parametri per i valori per specificare quando si distribuisce il modello. Il modello include una sezione denominata parametri che contiene tutti i valori dei parametri.
È necessario definire un parametro per i valori che variano in base a project che si distribuisce o in base all'ambiente per distribuire. Non definire i parametri per i valori che saranno sempre lo stesso. Ogni valore di parametro viene utilizzato nel modello per definire le risorse che vengono distribuite. 

Quando si definiscono parametri, utilizzare il campo **allowedValues** per specificare i valori di un utente può fornire durante la distribuzione. Utilizzare il campo **valore predefinito** per assegnare il valore al parametro, se viene specificato alcun valore durante la distribuzione.

Verranno illustrate ogni parametro nel modello.

### <a name="sitename"></a>NomeSito

Il nome dell'applicazione web che si desidera creare.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName

Il nome del piano di servizio di App da usare per l'hosting web app.
    
    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>SKU

Livello prezzo per il piano di hosting.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Il modello definisce i valori che sono consentiti per questo parametro e assegna un valore predefinito (S1) se viene specificato alcun valore.

### <a name="workersize"></a>workerSize

Dimensione istanza del piano di hosting (small, medium o grande).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
Il modello definisce i valori che sono consentiti per questo parametro (0, 1 o 2) e viene assegnato un valore predefinito (0) se viene specificato alcun valore. I valori corrispondono alle piccole, medie e grandi dimensioni.
