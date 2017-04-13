Azure Gestione risorse, definire i parametri per i valori per specificare quando si distribuisce il modello. Il modello include una sezione denominata parametri che contiene tutti i valori dei parametri.
È necessario definire un parametro per i valori che variano in base a project che si distribuisce o in base all'ambiente per distribuire. Non definire i parametri per i valori che saranno sempre lo stesso. Ogni valore di parametro viene utilizzato nel modello per definire le risorse sono distribuire. 

Quando si definiscono parametri, utilizzare il campo **allowedValues** per specificare i valori di un utente può fornire durante la distribuzione. Utilizzare il campo **valore predefinito** per assegnare il valore al parametro, se viene specificato alcun valore durante la distribuzione.

Verranno illustrate ogni parametro nel modello.

### <a name="logicappname"></a>logicAppName

Il nome dell'app logica per creare.

    "logicAppName": {
        "type": "string"
    }