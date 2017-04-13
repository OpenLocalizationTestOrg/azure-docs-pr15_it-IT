<properties
   pageTitle="Manager delle risorse modello funzioni | Microsoft Azure"
   description="Descrive le funzioni da usare in un modello di gestione di risorse Azure come recuperare i valori e lavorare con stringhe e valori numerici e recuperare le informazioni di distribuzione."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-template-functions"></a>Funzioni di modello Azure Manager delle risorse

Questo argomento illustra tutte le funzioni che è possibile usare in un modello di gestione di risorse Azure.

Funzioni di modello e i relativi parametri sono senza maiuscole/minuscole. Manager delle risorse viene risolto **variables('var1')** e **VARIABLES('VAR1')** come lo stesso. Quando viene valutata, a meno che la funzione espressamente Modifica maiuscole/minuscole (ad esempio toUpper o toLower), la funzione mantiene le maiuscole/minuscole. Alcuni tipi di risorse possono avere requisiti maiuscole e minuscole indipendentemente dalla modalità di valutazione delle funzioni.

## <a name="numeric-functions"></a>Funzioni numeriche

Manager delle risorse sono disponibili le seguenti funzioni per l'utilizzo di numeri interi:

- [aggiungere](#add)
- [copyIndex](#copyindex)
- [div](#div)
- [int](#int)
- [resto](#mod)
- [mul](#mul)
- [Sub](#sub)


<a id="add" />
### <a name="add"></a>aggiungere

**aggiungere (operand1, operand2)**

Restituisce la somma di due interi forniti.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1                           |   Sì    | Primo integer da aggiungere.
| operand2                           |   Sì    | Secondo valore integer da aggiungere.

Nell'esempio seguente aggiunge due parametri.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to add"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to add"
        }
      }
    },
    ...
    "outputs": {
      "addResult": {
        "type": "int",
        "value": "[add(parameters('first'), parameters('second'))]"
      }
    }

<a id="copyindex" />
### <a name="copyindex"></a>copyIndex

**copyIndex(offset)**

Restituisce l'indice corrente di un ciclo di iterazione. 

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| scarto                           |   No    | Importo da aggiungere al valore corrente di iterazione.

Questa funzione è sempre utilizzata con un oggetto di **Copia** . Per una descrizione completa di uso **copyIndex**, vedere [creazione di più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md).

Nell'esempio seguente mostra un ciclo di copia e il valore di indice incluso nel nome. 

    "resources": [ 
      { 
        "name": "[concat('examplecopy-', copyIndex())]", 
        "type": "Microsoft.Web/sites", 
        "copy": { 
          "name": "websitescopy", 
          "count": "[parameters('count')]" 
        }, 
        ...
      }
    ]


<a id="div" />
### <a name="div"></a>div

**div (operand1, operand2)**

Restituisce la divisione di interi di due interi forniti.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1                           |   Sì    | Numero intero che viene diviso.
| operand2                           |   Sì    | Numero intero che viene utilizzato da dividere. Non può essere 0.

Nell'esempio seguente divide un parametro da un altro parametro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "divResult": {
        "type": "int",
        "value": "[div(parameters('first'), parameters('second'))]"
      }
    }

<a id="int" />
### <a name="int"></a>int

**int(valueToConvert)**

Converte il valore specificato per eccesso all'intero.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| valueToConvert                     |   Sì    | Valore da convertire in numeri interi. Il tipo di valore può essere solo stringa o Integer.

Nell'esempio seguente converte il valore del parametro fornito dall'utente per eccesso all'intero.

    "parameters": {
        "appId": { "type": "string" }
    },
    "variables": { 
        "intValue": "[int(parameters('appId'))]"
    }


<a id="mod" />
### <a name="mod"></a>resto

**resto (operand1; operand2)**

Restituisce il resto della divisione integer utilizzando i due numeri interi specificati.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1                           |   Sì    | Numero intero che viene diviso.
| operand2                           |   Sì    | Numero intero che viene utilizzato per eseguire una divisione, deve essere diversa da 0.

Nell'esempio seguente restituisce il resto della divisione di un parametro da un altro parametro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer being divided"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer used to divide"
        }
      }
    },
    ...
    "outputs": {
      "modResult": {
        "type": "int",
        "value": "[mod(parameters('first'), parameters('second'))]"
      }
    }

<a id="mul" />
### <a name="mul"></a>mul

**mul (operand1, operand2)**

Restituisce la moltiplicazione di due interi forniti.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1                           |   Sì    | Primo integer per la moltiplicazione.
| operand2                           |   Sì    | Secondo valore integer per la moltiplicazione.

Nell'esempio seguente moltiplica un parametro da un altro parametro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "First integer to multiply"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Second integer to multiply"
        }
      }
    },
    ...
    "outputs": {
      "mulResult": {
        "type": "int",
        "value": "[mul(parameters('first'), parameters('second'))]"
      }
    }

<a id="sub" />
### <a name="sub"></a>Sub

**Sub (operand1, operand2)**

Restituisce la sottrazione di due interi forniti.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| operand1                           |   Sì    | Numero intero che viene sottratto dal.
| operand2                           |   Sì    | Numero intero che viene sottratto.

Nell'esempio seguente sottrae un parametro da un altro parametro.

    "parameters": {
      "first": {
        "type": "int",
        "metadata": {
          "description": "Integer subtracted from"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Integer to subtract"
        }
      }
    },
    ...
    "outputs": {
      "subResult": {
        "type": "int",
        "value": "[sub(parameters('first'), parameters('second'))]"
      }
    }

## <a name="string-functions"></a>Funzioni stringa

Manager delle risorse sono disponibili le seguenti funzioni per l'utilizzo di stringhe:

- [base 64](#base64)
- [Concatena](#concat)
- [lunghezza](#lengthstring)
- [padLeft](#padleft)
- [Sostituisci](#replace)
- [Ignora](#skipstring)
- [dividere](#split)
- [stringa](#string)
- [sottostringa](#substring)
- [Scrivi](#takestring)
- [toLower](#tolower)
- [toUpper](#toupper)
- [tagliare](#trim)
- [uniqueString](#uniquestring)
- [URI](#uri)


<a id="base64" />
### <a name="base64"></a>base 64

**base 64 (inputString)**

Restituisce la rappresentazione di base 64 della stringa di input.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| inputString                        |   Sì    | Valore stringa da restituire come una rappresentazione in formato base 64.

Nell'esempio seguente viene illustrato come utilizzare la funzione base 64.

    "variables": {
      "usernameAndPassword": "[concat('parameters('username'), ':', parameters('password'))]",
      "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
    }

<a id="concat" />
### <a name="concat---string"></a>Concatena - stringa

**CONCATENA (stringa1, stringa2 string3,....)**

Combina più valori stringa e restituisce la stringa concatenata. 

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringa1                        |   Sì    | Un valore stringa da concatenare.
| stringhe aggiuntive             |   No     | I valori per concatenare stringa.

Questa funzione può richiedere qualsiasi numero di argomenti e possa accettare stringhe o matrici per i parametri. Per un esempio di concatenare matrici, vedere [concatena - matrice](#concatarray).

Nell'esempio seguente viene illustrato come combinare più valori di stringa per restituire una stringa concatenata.

    "outputs": {
        "siteUri": {
          "type": "string",
          "value": "[concat('http://', reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
        }
    }


<a id="lengthstring" />
### <a name="length---string"></a>lunghezza - stringa

**Length(String)**

Restituisce il numero di caratteri di una stringa.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringa                        |   Sì    | Il valore di stringa da utilizzare per ottenere il numero di caratteri.

Per un esempio dell'uso di lunghezza con una matrice, vedere [Lunghezza - matrice](#length).

Nell'esempio seguente restituisce il numero di caratteri di una stringa. 

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "nameLength": "[length(parameters('appName'))]"
    }
        

<a id="padleft" />
### <a name="padleft"></a>padLeft

**padLeft (valueToPad, totalLength, paddingCharacter)**

Restituisce una stringa allineata a destra mediante l'aggiunta di caratteri a sinistra fino a raggiungere la lunghezza totale specificata.
  
| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| valueToPad                         |   Sì    | Stringa o int per allineare a destra.
| totalLength                        |   Sì    | Il numero totale di caratteri nella stringa restituita.
| paddingCharacter                   |   No     | Il carattere da usare per spaziatura a sinistra fino a raggiunta la lunghezza totale. Il valore predefinito è uno spazio.

Nell'esempio seguente viene illustrato come impostare il valore del parametro fornito dall'utente aggiungendo il carattere zero fino a quando la stringa raggiunge 10 caratteri. Se il valore del parametro originale è più di 10 caratteri, non vengono aggiunti caratteri.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "paddedAppName": "[padLeft(parameters('appName'),10,'0')]"
    }

<a id="replace" />
### <a name="replace"></a>Sostituisci

**Sostituisci (originalString, oldCharacter, newCharacter)**

Restituisce una nuova stringa con tutte le istanze di un carattere nella stringa specificata sostituita da un altro carattere.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| originalString                     |   Sì    | Stringa contenente tutte le istanze di un carattere sostituiti da un altro carattere.
| oldCharacter                       |   Sì    | Il carattere da rimuovere dalla stringa originale.
| newCharacter                       |   Sì    | Carattere da aggiungere al posto del carattere rimosso.

Nell'esempio seguente viene illustrato come rimuovere tutte le linee tratteggiate dalla stringa fornito dall'utente.

    "parameters": {
        "identifier": { "type": "string" }
    },
    "variables": { 
        "newidentifier": "[replace(parameters('identifier'),'-','')]"
    }

<a id="skipstring" />
### <a name="skip---string"></a>Ignora - stringa
**Ignora (originalValue, numberToSkip)**

Restituisce una stringa con tutti i caratteri dopo il numero specificato nella stringa.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| originalValue                      |   Sì    | Stringa da utilizzare per l'omissione.
| numberToSkip                       |   Sì    | Il numero di caratteri da ignorare. Se questo valore è minore o uguale a 0, tutti i caratteri nella stringa. Se è maggiore della lunghezza della stringa, viene restituita una stringa vuota. 

Per un esempio dell'uso salta con una matrice, vedere [ignorare - matrice](#skip).

Nell'esempio seguente ignora il numero specificato di caratteri nella stringa.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for skipping"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[skip(parameters('first'),parameters('second'))]"
      }
    }


<a id="split" />
### <a name="split"></a>dividere

**Dividi (inputString, delimiterString)**

**Dividi (inputString, delimiterArray)**

Restituisce una matrice di stringhe che contiene le sottostringhe della stringa di input sono racchiusi tra delimitatori specificati.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| inputString                        |   Sì    | Stringa da dividere.
| delimitatore                          |   Sì    | Il delimitatore da utilizzare, può essere un'unica stringa o una matrice di stringhe.

Nell'esempio seguente divide la stringa di input con un punto e virgola.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "stringPieces": "[split(parameters('inputString'), ',')]"
    }

Nell'esempio seguente divide la stringa di input con un punto e virgola o un punto e virgola.

    "variables": {
      "stringToSplit": "test1,test2;test3",
      "delimiters": [ ",", ";" ]
    },
    "resources": [ ],
    "outputs": {
      "exampleOutput": {
        "value": "[split(variables('stringToSplit'), variables('delimiters'))]",
        "type": "array"
      }
    }

<a id="string" />
### <a name="string"></a>stringa

**String(valueToConvert)**

Converte il valore specificato in una stringa.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| valueToConvert                     |   Sì    | Valore da convertire in stringa. Qualsiasi tipo di valore può essere convertito, inclusi gli oggetti e le matrici.

Nell'esempio seguente converte i valori di parametro fornito dall'utente in stringhe.

    "parameters": {
      "jsonObject": {
        "type": "object",
        "defaultValue": {
          "valueA": 10,
          "valueB": "Example Text"
        }
      },
      "jsonArray": {
        "type": "array",
        "defaultValue": [ "a", "b", "c" ]
      },
      "jsonInt": {
        "type": "int",
        "defaultValue": 5
      }
    },
    "variables": { 
      "objectString": "[string(parameters('jsonObject'))]",
      "arrayString": "[string(parameters('jsonArray'))]",
      "intString": "[string(parameters('jsonInt'))]"
    }

<a id="substring" />
### <a name="substring"></a>sottostringa

**sottostringa (stringToParse, startIndex, lunghezza)**

Restituisce una sottostringa che inizia in corrispondenza della posizione di carattere specificato e contiene il numero specificato di caratteri.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringToParse                     |   Sì    | Stringa originale dal quale è estratto sottostringa.
| startIndex                         | No      | In base zero posizione del carattere iniziale della sottostringa.
| lunghezza                             | No      | Il numero di caratteri della sottostringa.

Nell'esempio seguente estrae i primi tre caratteri da un parametro.

    "parameters": {
        "inputString": { "type": "string" }
    },
    "variables": { 
        "prefix": "[substring(parameters('inputString'), 0, 3)]"
    }

<a id="takestring" />
### <a name="take---string"></a>Scrivi - stringa
**Riprendi (originalValue, numberToTake)**

Restituisce una stringa con il numero di caratteri specificato dall'inizio della stringa.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| originalValue                      |   Sì    | Stringa da scrivere i caratteri da.
| numberToTake                       |   Sì    | Il numero di caratteri da eseguire. Se il valore è minore o uguale a 0, viene restituita una stringa vuota. Se è maggiore della lunghezza della stringa specificata, tutti i caratteri nella stringa.

Per un esempio dell'uso Scrivi con una matrice, vedere [take - matrice](#take).

Nell'esempio seguente consente di accedere il numero specificato di caratteri dalla stringa.

    "parameters": {
      "first": {
        "type": "string",
        "metadata": {
          "description": "Value to use for taking"
        }
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of characters to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "string",
        "value": "[take(parameters('first'), parameters('second'))]"
      }
    }

<a id="tolower" />
### <a name="tolower"></a>toLower

**toLower(stringToChange)**

Converte la stringa specificata in lettere minuscole.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringToChange                     |   Sì    | Stringa da convertire in lettere minuscole.

Nell'esempio seguente converte il valore del parametro fornito dall'utente in lettere minuscole.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "lowerCaseAppName": "[toLower(parameters('appName'))]"
    }

<a id="toupper" />
### <a name="toupper"></a>toUpper

**toUpper(stringToChange)**

Converte la stringa specificata in lettere maiuscole.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringToChange                     |   Sì    | Stringa da convertire in lettere maiuscole.

Nell'esempio seguente converte il valore del parametro fornito dall'utente in lettere maiuscole.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "upperCaseAppName": "[toUpper(parameters('appName'))]"
    }

<a id="trim" />
### <a name="trim"></a>tagliare

**Trim (stringToTrim)**

Rimuove tutti i caratteri di spazi vuoti iniziali e finali dalla stringa specificata.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| stringToTrim                       |   Sì    | Stringa da rimuovere.

Nell'esempio seguente Taglia spazi rispetto al valore del parametro fornito dall'utente.

    "parameters": {
        "appName": { "type": "string" }
    },
    "variables": { 
        "trimAppName": "[trim(parameters('appName'))]"
    }

<a id="uniquestring" />
### <a name="uniquestring"></a>uniqueString

**uniqueString (baseString,...)**

Crea una stringa hash deterministica in base ai valori forniti come parametri. 

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| baseString      |   Sì    | Stringa utilizzata la funzione hash per creare una stringa univoca.
| altri parametri in base alle esigenze    | No       | È possibile aggiungere stringhe secondo le esigenze per creare il valore che specifica il livello di unicità.

Questa funzione è utile quando è necessario creare un nome univoco per una risorsa. Fornire i valori dei parametri che limitare l'ambito di univocità per il risultato. È possibile specificare se il nome sia univoco per difetto abbonamento, gruppo di risorse o distribuzione. 

Valore restituito non è una stringa casuale, ma il risultato di una funzione hash. Valore restituito è 13 caratteri. Non è globalmente univoco. È consigliabile combinare il valore con un prefisso dalla convenzione di denominazione per creare un nome significativo. Nell'esempio seguente mostra il formato del valore restituito. Naturalmente, il valore effettivo variano i parametri indicati.

    tcvhiyu5h2o5o

Negli esempi seguenti viene illustrato come utilizzare uniqueString per creare un valore univoco per i livelli di usati comune.

Valore univoco nell'ambito all'abbonamento

    "[uniqueString(subscription().subscriptionId)]"

Valore univoco nell'ambito di gruppo di risorse

    "[uniqueString(resourceGroup().id)]"

Valore univoco per la distribuzione di un gruppo di risorse

    "[uniqueString(resourceGroup().id, deployment().name)]"
    
Nell'esempio seguente viene illustrato come creare un nome univoco per un account di archiviazione in base al gruppo di risorse (all'interno del gruppo di risorse non è il nome univoco se creato nello stesso modo).

    "resources": [{ 
        "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]", 
        "type": "Microsoft.Storage/storageAccounts", 
        ...



<a id="uri" />
### <a name="uri"></a>URI

**URI (baseUri, relativeUri)**

Crea un URI assoluto combinando baseUri e la stringa relativeUri.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| baseUri                            |   Sì    | Stringa uri di base.
| relativeUri                        |   Sì    | Stringa uri relativo da aggiungere alla stringa uri di base.

Il valore per il parametro **baseUri** può includere un file specifico, ma solo il percorso di base viene utilizzato per costruire URI. Ad esempio, se **http://contoso.com/resources/azuredeploy.json** come baseUri parametro in un URI di base di **http://contoso.com/resources/**.

Nell'esempio seguente viene illustrato come creare un collegamento a un modello nidificato in base al valore del modello padre.

    "templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"

## <a name="array-functions"></a>Funzioni di matrice

Manager delle risorse sono disponibili diverse funzioni per l'uso con i valori della matrice.

- [Concatena](#concatarray)
- [lunghezza](#length)
- [Ignora](#skip)
- [Scrivi](#take)

Per ottenere una matrice di valori stringa delimitato da un valore, vedere [dividere](#split).

<a id="concatarray" />
### <a name="concat---array"></a>Concatena - matrice

**CONCATENA (matrice1, matrice2; matrice3;...)**

Consente di combinare più matrici e restituisce la matrice concatenata. 

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| Matrice1                        |   Sì    | Matrice di concatenare.
| matrici aggiuntive             |   No     | Matrici da concatenare.

Questa funzione può richiedere qualsiasi numero di argomenti e possa accettare stringhe o matrici per i parametri. Per un esempio di concatenazione di valori stringa, vedere [concatena - stringa](#concat).

Nell'esempio seguente viene illustrato come combinare due matrici.

    "parameters": {
        "firstarray": {
            type: "array"
        }
        "secondarray": {
            type: "array"
        }
     },
     "variables": {
         "combinedarray": "[concat(parameters('firstarray'), parameters('secondarray'))]
     }
        

<a id="length" />
### <a name="length---array"></a>Lunghezza - matrice

**Length(Array)**

Restituisce il numero di elementi in una matrice.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| in forma di matrice                        |   Sì    | Matrice da utilizzare per ottenere il numero di elementi.

È possibile utilizzare questa funzione con una matrice per specificare il numero di iterazioni durante la creazione di risorse. Nell'esempio seguente il parametro **siteNames** fare riferimento a una matrice di nomi da utilizzare durante la creazione di siti web.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

Per ulteriori informazioni sull'uso della funzione con una matrice, vedere [creazione di più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md). 

Per un esempio dell'uso di lunghezza con un valore stringa, vedere [lunghezza - stringa](#lengthstring).

<a id="skip" />
### <a name="skip---array"></a>ignorare - matrice
**Ignora (originalValue, numberToSkip)**

Restituisce una matrice con tutti gli elementi dopo il numero specificato nella matrice.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| originalValue                      |   Sì    | Matrice da utilizzare per l'omissione.
| numberToSkip                       |   Sì    | Il numero di elementi da ignorare. Se il valore è minore o uguale a 0, vengono restituiti tutti gli elementi della matrice. Se è maggiore della lunghezza della matrice, una stringa vuota. 

Per un esempio dell'uso salta con una stringa, vedere [ignorare - stringa](#skipstring).

Nell'esempio seguente ignora il numero specificato di elementi della matrice.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for skipping"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to skip"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[skip(parameters('first'), parameters('second'))]"
      }
    }

<a id="take" />
### <a name="take---array"></a>scrivere - matrice
**Riprendi (originalValue, numberToTake)**

Restituisce una matrice con il numero specificato di elementi dall'inizio della matrice.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| originalValue                      |   Sì    | Matrice da prendere gli elementi da.
| numberToTake                       |   Sì    | Il numero di elementi da eseguire. Se questo valore è minore o uguale a 0, una stringa vuota. Se è maggiore della lunghezza della matrice specificata, vengono restituiti tutti gli elementi della matrice.

Per un esempio dell'uso Scrivi con una stringa, vedere [accettano - stringa](#takestring).

Nell'esempio seguente consente di accedere il numero specificato di elementi dalla matrice.

    "parameters": {
      "first": {
        "type": "array",
        "metadata": {
          "description": "Values to use for taking"
        },
        "defaultValue": [ "one", "two", "three" ]
      },
      "second": {
        "type": "int",
        "metadata": {
          "description": "Number of elements to take"
        }
      }
    },
    "resources": [
    ],
    "outputs": {
      "return": {
        "type": "array",
        "value": "[take(parameters('first'),parameters('second'))]"
      }
    }

## <a name="deployment-value-functions"></a>Funzioni di valore di distribuzione

Manager delle risorse vengono fornite le funzioni seguenti per ottenere valori dal sezioni del modello e i valori correlati alla distribuzione:

- [distribuzione](#deployment)
- [parametri](#parameters)
- [variabili](#variables)

Per ottenere i valori da risorse, gruppi di risorse o le sottoscrizioni, vedere [funzioni di risorsa](#resource-functions).

<a id="deployment" />
### <a name="deployment"></a>distribuzione

**distribuzione)**

Restituisce informazioni sull'operazione di distribuzione corrente.

Questa funzione restituisce l'oggetto passato durante la distribuzione. Le proprietà nell'oggetto restituito variano in base se l'oggetto di distribuzione viene passato come collegamento o come oggetto in linea. 

Quando l'oggetto di distribuzione verrà passato in linea, ad esempio quando si utilizza il parametro **- TemplateFile** in Azure PowerShell in modo che puntino a un file locale, l'oggetto restituito è nel formato seguente:

    {
        "name": "",
        "properties": {
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [
                ],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Quando l'oggetto viene passato come collegamento, ad esempio quando si utilizza il parametro **- TemplateUri** in modo che puntino a un oggetto remoto, viene restituito nel formato seguente. 

    {
        "name": "",
        "properties": {
            "templateLink": {
                "uri": ""
            },
            "template": {
                "$schema": "",
                "contentVersion": "",
                "parameters": {},
                "variables": {},
                "resources": [],
                "outputs": {}
            },
            "parameters": {},
            "mode": "",
            "provisioningState": ""
        }
    }

Nell'esempio seguente viene illustrato come utilizzare distribuzione () per creare un collegamento a un altro modello in base all'URI del modello padre.

    "variables": {  
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
    }  

<a id="parameters" />
### <a name="parameters"></a>parametri

**parametri (parameterName)**

Restituisce un valore di parametro. Nella sezione parametri del modello, è necessario definire il nome del parametro specificato.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| parameterName                      |   Sì    | Il nome del parametro da restituire.

Nell'esempio seguente è illustrato un utilizzo semplificato della funzione parametri.

    "parameters": { 
      "siteName": {
          "type": "string"
      }
    },
    "resources": [
       {
          "apiVersion": "2014-06-01",
          "name": "[parameters('siteName')]",
          "type": "Microsoft.Web/Sites",
          ...
       }
    ]

<a id="variables" />
### <a name="variables"></a>variabili

**variabili (NomeVariabile)**

Restituisce il valore della variabile. Nella sezione variabili del modello, è necessario definire il nome della variabile specificato.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| variabile nome                      |   Sì    | Il nome della variabile da restituire.

Nell'esempio seguente viene utilizzato un valore variabile.

    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
      {
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[variables('storageName')]",
        ...
      }
    ],

## <a name="resource-functions"></a>Funzioni delle risorse

Manager delle risorse fornisce le funzioni seguenti per ottenere i valori delle risorse:

- [listKeys e l'elenco {valore}](#listkeys)
- [provider di servizi](#providers)
- [Guida di riferimento](#reference)
- [resourceGroup](#resourcegroup)
- [resourceId](#resourceid)
- [abbonamento](#subscription)

Per ottenere i valori da parametri, variabili o la distribuzione corrente, vedere [distribuzione valore funzioni](#deployment-value-functions).

<a id="listkeys" />
<a id="list" />
### <a name="listkeys-and-listvalue"></a>listKeys e l'elenco {valore}

**listKeys (resourceName o resourceIdentifier, apiVersion)**

**elenco {valore} (resourceName o resourceIdentifier, apiVersion)**

Restituisce i valori per qualsiasi tipo di risorsa che supporta l'operazione di elenco. L'uso più comune è **listKeys**. 
  
| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier |   Sì    | Identificatore univoco per la risorsa.
| apiVersion                         |   Sì    | Versione dell'API di stato di esecuzione delle risorse.

Qualsiasi operazione che inizia con **elenco** può essere utilizzata una funzione nel modello scelto. Operazioni disponibili includono non solo **listKeys**, ma anche le operazioni come **elenco**, **listAdminKeys**e **listStatus**. Per determinare quali tipi di risorse hanno un'operazione di elenco, utilizzare il comando PowerShell seguente.

    Get-AzureRmProviderOperation -OperationSearchString *  | where {$_.Operation -like "*list*"} | FT Operation

In alternativa, è possibile recuperare l'elenco con Azure CLI. Nell'esempio seguente recupera tutte le operazioni per **apiapps**e viene utilizzata l' utilità JSON [jq](http://stedolan.github.io/jq/download/) per filtrare solo le operazioni di elenco.

    azure provider operations show --operationSearchString */apiapps/* --json | jq ".[] | select (.operation | contains(\"list\"))"

È possibile specificare il resourceId usando la [funzione resourceId](#resourceid) o utilizzando il formato **{providerNamespace} / {risorsa} / {resourceName}**.

Nell'esempio seguente viene illustrato come restituire chiavi primarie e secondarie da un account di archiviazione nella sezione output.

    "outputs": { 
      "listKeysOutput": { 
        "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2016-01-01')]", 
        "type" : "object" 
      } 
    } 

L'oggetto restituito da listKeys è nel formato seguente:

    {
      "keys": [
        {
          "keyName": "key1",
          "permissions": "Full",
          "value": "{value}"
        },
        {
          "keyName": "key2",
          "permissions": "Full",
          "value": "{value}"
        }
      ]
    }

<a id="providers" />
### <a name="providers"></a>provider di servizi

**provider di servizi (providerNamespace; [tipo di risorsa])**

Restituisce informazioni su un provider di risorse e i tipi di risorse supportati. Se non si specifica un tipo di risorsa, la funzione restituisce tutti i tipi supportati per il provider di risorse.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| providerNamespace                  |   Sì    | Namespace del provider
| tipo di risorsa                       |   No     | Il tipo di risorsa all'interno dello spazio dei nomi specificato.

Ogni tipo supportato viene restituito nel formato seguente. Non è garantito l'ordinamento in forma di matrice.

    {
        "resourceType": "",
        "locations": [ ],
        "apiVersions": [ ]
    }

Nell'esempio seguente viene illustrato come utilizzare la funzione di provider:

    "outputs": {
        "exampleOutput": {
            "value": "[providers('Microsoft.Storage', 'storageAccounts')]",
            "type" : "object"
        }
    }

<a id="reference" />
### <a name="reference"></a>Guida di riferimento

**Guida di riferimento (resourceName o resourceIdentifier; [apiVersion])**

Restituisce un oggetto che rappresenta lo stato di runtime della risorsa.

| Parametro                          | Obbligatorio | Descrizione
| :--------------------------------: | :------: | :----------
| resourceName o resourceIdentifier |   Sì    | Nome o l'identificatore univoco di una risorsa.
| apiVersion                         |   No     | Versione dell'API della risorsa specificata. Includere questo parametro quando all'interno di stesso modello non è stato effettuato il provisioning della risorsa.

La funzione di **riferimento** deriva il relativo valore da uno stato di runtime e non può essere utilizzata nella sezione variabili. Può essere utilizzato nella sezione output di un modello.

Usando la funzione di riferimento, in modo implicito dichiarare che una risorsa dipende da un'altra risorsa se viene eseguito il provisioning della risorsa a cui viene fatto riferimento nel modello stesso. Non è necessario utilizzare la proprietà **dependsOn** . La funzione non viene valutata fino a quando non è stata completata la risorsa cui viene fatto riferimento distribuzione.

Nell'esempio seguente fa riferimento a un account di archiviazione che viene distribuito nello stesso modello.

    "outputs": {
        "NewStorage": {
            "value": "[reference(parameters('storageAccountName'))]",
            "type" : "object"
        }
    }

Nell'esempio seguente fa riferimento a un account di archiviazione che non è stato distribuito in questo modello, ma è presente nel gruppo di risorse stesso come risorse in fase di distribuzione.

    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }

È possibile recuperare un valore specifico dall'oggetto restituito, ad esempio l'endpoint blob URI, come illustrato nell'esempio seguente.

    "outputs": {
        "BlobUri": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

Nell'esempio seguente fa riferimento a un account di archiviazione in un gruppo di risorse diversi.

    "outputs": {
        "BlobUri": {
            "value": "[reference(resourceId(parameters('relatedGroup'), 'Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
            "type" : "string"
        }
    }

Le proprietà sull'oggetto restituito dalla funzione **riferimento** variano in base al tipo di risorsa. Per visualizzare i nomi di proprietà e i valori per un tipo di risorsa, creare un modello semplice che restituisce l'oggetto nella sezione **output** . Se si dispone di una risorsa esistente dello stesso tipo, il modello restituisce solo l'oggetto senza distribuire le nuove risorse. Se non si dispone di una risorsa esistente dello stesso tipo, il modello distribuisce solo quel tipo e restituisce l'oggetto. Aggiungere le proprietà di altri modelli che è necessario recuperare dinamicamente i valori durante la distribuzione. 

<a id="resourcegroup" />
### <a name="resourcegroup"></a>resourceGroup

**resourceGroup()**

Restituisce un oggetto che rappresenta il gruppo di risorse corrente. 

L'oggetto restituito è nel formato seguente:

    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
      "name": "{resourceGroupName}",
      "location": "{resourceGroupLocation}",
      "tags": {
      },
      "properties": {
        "provisioningState": "{status}"
      }
    }

Nell'esempio seguente viene utilizzato il percorso di gruppo di risorse per assegnare la posizione per un sito web.

    "resources": [
       {
          "apiVersion": "2014-06-01",
          "type": "Microsoft.Web/sites",
          "name": "[parameters('siteName')]",
          "location": "[resourceGroup().location]",
          ...
       }
    ]

<a id="resourceid" />
### <a name="resourceid"></a>resourceId

**resourceId ([subscriptionId], [resourceGroupName], tipo di risorsa, resourceName1, [resourceName2]...)**

Restituisce l'identificatore univoco di una risorsa. 
      
| Parametro         | Obbligatorio | Descrizione
| :---------------: | :------: | :----------
| subscriptionId    |   No     | Valore predefinito è l'abbonamento corrente. Specificare il valore quando è necessario recuperare una risorsa in un'altra sottoscrizione.
| resourceGroupName |   No     | Valore predefinito è gruppo risorse corrente. Specificare il valore quando è necessario recuperare una risorsa in un altro gruppo di risorse.
| tipo di risorsa      |   Sì    | Tipo di risorsa incluso spazio dei nomi del provider di risorse.
| resourceName1     |   Sì    | Nome della risorsa.
| resourceName2     |   No     | Risorsa nome segmento successivo se sono nidificata delle risorse.

Utilizzare questa funzione quando il nome della risorsa è ambiguo o non provisioning all'interno del modello stesso. Viene restituito l'identificatore nel formato seguente:

    /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/{resourceProviderNamespace}/{resourceType}/{resourceName}

Nell'esempio seguente viene illustrato come recuperare gli ID risorsa per un sito web e un database. Il sito web esistente in un gruppo di risorse denominato **myWebsitesGroup** e il database è presente nel gruppo di risorse corrente per il modello.

    [resourceId('myWebsitesGroup', 'Microsoft.Web/sites', parameters('siteName'))]
    [resourceId('Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]
    
Spesso, è necessario utilizzare questa funzione quando si utilizza un account di archiviazione o virtuali in un gruppo di risorse alternativo. L'account di archiviazione o virtuali possono essere utilizzati in più gruppi di risorse; di conseguenza, non si desidera eliminarli quando si elimina un gruppo di risorse singola. Nell'esempio seguente mostra come una risorsa da un gruppo di risorse esterni può essere utilizzata:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]
    }

<a id="subscription" />
### <a name="subscription"></a>abbonamento

**Subscription()**

Restituisce i dettagli dell'abbonamento nel formato seguente.

    {
        "id": "/subscriptions/#####",
        "subscriptionId": "#####",
        "tenantId": "#####"
    }

Nell'esempio seguente viene illustrata la funzione di sottoscrizione nella sezione output di chiamate. 

    "outputs": { 
      "exampleOutput": { 
          "value": "[subscription()]", 
          "type" : "object" 
      } 
    } 


## <a name="next-steps"></a>Passaggi successivi
- Per una descrizione delle sezioni in un modello di gestione risorse di Azure, vedere [modelli di creazione condivisa Manager delle risorse di Azure](resource-group-authoring-templates.md)
- Per unire più modelli, vedere [uso dei modelli collegati con Gestione risorse di Azure](resource-group-linked-templates.md)
- Per scorrere un numero di volte specificato quando si crea un tipo di risorsa, vedere [creazione di più istanze di risorse in Gestione risorse di Azure](resource-group-create-multiple.md)
- Per informazioni su come distribuire il modello che è stato creato, vedere [distribuire un'applicazione con il modello di gestione risorse di Azure](resource-group-template-deploy.md)

