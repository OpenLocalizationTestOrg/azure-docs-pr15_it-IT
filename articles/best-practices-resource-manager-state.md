<properties
    pageTitle="Stato di gestione nei modelli di gestione risorse | Microsoft Azure"
    description="Mostra consigliati approcci per l'utilizzo di oggetti complessi per condividere i dati di stato con i modelli di gestione risorse Azure e collegato"
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="tomfitz"/>

# <a name="sharing-state-in-azure-resource-manager-templates"></a>Condividere lo stato in Gestione risorse Azure modelli

In questo argomento viene procedure consigliate per la gestione e la condivisione dello stato all'interno di modelli. I parametri e variabili illustrate in questo argomento sono riportati alcuni esempi del tipo di oggetti è possibile definire per organizzare in modo appropriato ai propri requisiti di distribuzione. Da questi esempi, è possibile implementare gli oggetti con valori di proprietà che è possibile utilizzare per l'ambiente.

In questo argomento fa parte di un white paper più grande. Per leggere il documento completo, scaricare [World classe Manager modelli le considerazioni sulle risorse e affidabile consigliate](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).


## <a name="provide-standard-configuration-settings"></a>Specificare le impostazioni di configurazione standard

Piuttosto che offre un modello che fornisce la massima flessibilità e risparmiare varianti, un motivo comune consiste nel fornire una selezione di configurazioni note. In effetti, gli utenti possono selezionare dimensioni maglietta standard, ad esempio sandbox piccole, medie e grandi dimensioni. Altri esempi di dimensioni maglietta sono prodotti, ad esempio community edition o enterprise edition. In altri casi, le configurazioni di carico di lavoro specifiche di una tecnologia – può essere ad esempio mappa ridurre o nessun sql.

Con gli oggetti complessi, è possibile creare variabili che contengono insiemi di dati, talvolta noto come "contenitori di proprietà" e utilizzare i dati per guidare la dichiarazione di risorse nel modello scelto. Questo approccio fornisce ottimale, note configurazioni di varie dimensioni che sono preconfigurate per i clienti. Senza note configurazioni, gli utenti del modello devono determinare cluster ridimensionamento autonomamente, fattore nei vincoli di risorse piattaforma ed eseguire operazioni matematiche per identificare le partizioni risultante di account di archiviazione e altre risorse (a causa di vincoli di dimensioni e la risorsa cluster). Oltre a rendere più semplice per il cliente, sono più facili da alcuni configurazioni note supportano e consentono di fornire un livello superiore di densità.

Nell'esempio seguente viene illustrato come definire le variabili che contengono oggetti complessi per rappresentare insiemi di dati. Le raccolte definiscono valori che vengono utilizzati per le dimensioni del computer virtuale, le impostazioni di rete, le impostazioni di sistema operativo e impostazioni relative alla disponibilità.

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      },
      "tshirtSizeMedium": {
        "vmSize": "Standard_A3",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1],
          "jumpbox": 0
        }
      },
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

Si noti che la variabile **tshirtSize** concatena dimensioni maglietta fornito tramite un parametro (**Small**, **Medium**, **grande**) per il testo **tshirtSize**. Utilizzare questa variabile per recuperare la variabile oggetto complesse associato per la dimensione maglietta.

È possibile fare riferimento queste variabili in un secondo momento nel modello. La possibilità di fare riferimento a variabili denominate e le relative proprietà semplifica la sintassi e rende più facile comprensione contesto. Nell'esempio seguente definisce una risorsa per distribuire utilizzando gli oggetti mostrati in precedenza per impostare i valori. Ad esempio, la dimensione di memoria virtuale è impostata per il recupero del valore per `variables('tshirtSize').vmSize` mentre il valore per la dimensione del disco vengono recuperati `variables('tshirtSize').diskSize`. Inoltre, l'URI per un modello collegato è impostato con il valore di `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
            "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## <a name="pass-state-to-a-template"></a>Passare stato a un modello

Condividere lo stato in un modello tramite i parametri che forniscono direttamente durante la distribuzione.

La tabella seguente elenca i parametri in genere utilizzati nei modelli.

Nome | Valore | Descrizione
---- | ----- | -----------
posizione    | Stringa da un elenco delle aree Azure limitato   | La posizione in cui sono distribuite le risorse.
storageAccountNamePrefix    | Stringa    | Nome DNS univoco per l'Account di archiviazione in cui vengono inseriti dischi della macchina virtuale
NomeDominio  | Stringa    | Nome di dominio jumpbox accessibili macchine Virtuali nel formato: **{nomedominio}. { posizione}.cloudapp.com** , ad esempio: **mydomainname.westus.cloudapp.azure.com**
adminUsername   | Stringa    | Nome utente per le macchine virtuali
adminPassword   | Stringa    | Password per le macchine virtuali
tshirtSize  | Stringa da un elenco di vincolo di offerte dimensioni maglietta   | La dimensione dell'unità di scala denominato effettuare il provisioning. Ad esempio "Piccoli", "medio", "Grande"
virtualNetworkName  | Stringa    | Nome della rete virtuale che l'utente desidera utilizzare.
enableJumpbox   | Stringa da un elenco limitato (abilitato/disabilitato) | Parametro che identifica se si desidera abilitare un jumpbox per l'ambiente. Valori: "abilitato", "disabilitato"

Il parametro **tshirtSize** utilizzato nella sezione precedente è definito come:

    "parameters": {
      "tshirtSize": {
        "type": "string",
        "defaultValue": "Small",
        "allowedValues": [
          "Small",
          "Medium",
          "Large"
        ],
        "metadata": {
          "Description": "T-shirt size of the MongoDB deployment"
        }
      }
    }


## <a name="pass-state-to-linked-templates"></a>Passare stato modelli collegati

Quando ci si connette ai modelli collegati, spesso utilizzare una combinazione di statica e generato variabili.

### <a name="static-variables"></a>Variabili statiche

Variabili statiche spesso vengono utilizzate per fornire i valori di base, ad esempio gli URL, che vengono utilizzati in un modello.

Nell'estratto modello seguente, `templateBaseUrl` specifica la posizione principale per il modello in GitHub. Riga successiva consente di creare una nuova variabile `sharedTemplateUrl` che concatena l'URL di base con il nome del modello di risorse condivise noto. Sotto la riga, una variabile oggetto complesso viene utilizzata per memorizzare una dimensione maglietta, in cui l'URL di base è concatenata dei modelli in configurazione e archiviato nel `vmTemplate` proprietà.

Il vantaggio di questo approccio è che se la posizione dei modelli cambia, è sufficiente modificare la variabile statica in un'unica posizione, che passa in tutti i modelli collegati.

    "variables": {
      "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
      "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "slaveCount": 1,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      }
    }

### <a name="generated-variables"></a>Variabili generate

Oltre alle variabili statiche, vengono generate in modo dinamico diverse variabili. In questa sezione vengono illustrati alcuni dei tipi di variabili generate comuni.

#### <a name="tshirtsize"></a>tshirtSize

Si ha familiarità con questa variabile generata degli esempi precedenti.

#### <a name="networksettings"></a>networkSettings

In capacità, la funzionalità di un modello di soluzione ambito-to-end, i modelli collegati normalmente creano risorse esistenti in una rete. Un approccio semplice consiste nell'utilizzare un oggetto complesso per archiviare le impostazioni di rete e passare i modelli collegati.

Di seguito è riportato un esempio di comunicazione le impostazioni di rete.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

#### <a name="availabilitysettings"></a>availabilitySettings

Le risorse create in modelli collegati spesso vengono inserite in un set di disponibilità. Nell'esempio seguente viene specificato il nome del set di disponibilità e anche il dominio e il dominio di aggiornamento contare per l'utilizzo.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Se sono necessarie più set di disponibilità (ad esempio, uno per i nodi master) e un altro per nodi di dati, è possibile utilizzare un nome come un prefisso, specificare più set di disponibilità o seguire il modello illustrato in precedenza per la creazione di una variabile per una dimensione maglietta specifica.

#### <a name="storagesettings"></a>storageSettings

Dettagli di archiviazione spesso vengono condivisi con i modelli collegati. Nell'esempio seguente, un oggetto *storageSettings* vengono fornite informazioni dettagliate sui nomi di account e contenitore dello spazio di archiviazione.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### <a name="ossettings"></a>osSettings

Con i modelli collegati, potrebbe essere necessario passare a impostazioni del sistema operativo a vari tipi di nodi tra tipi diversi di configurazione. Un oggetto complesso è un modo semplice per archiviare e condividere informazioni sul sistema operativo e rende più semplice supportare più opzioni del sistema operativo per la distribuzione.

Nell'esempio seguente mostra un oggetto per *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

#### <a name="machinesettings"></a>machineSettings

Una variabile generata *machineSettings* è un oggetto complesso che contiene una combinazione di variabili di base per la creazione di una macchina virtuale. Le variabili includono nome utente e la password, un prefisso per i nomi di macchine Virtuali e un riferimento all'immagine del sistema operativo.

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

Si noti che *osImageReference* recupera i valori da variabile *osSettings* definita nel modello principale. Ciò significa che è possibile modificare facilmente il sistema operativo per una macchina virtuale, ovvero completamente o in base alle preferenze di un modello cliente.

#### <a name="vmscripts"></a>vmScripts

L'oggetto *vmScripts* contiene i dettagli di script per scaricare ed eseguire su un'istanza di macchine Virtuali, tra cui interno riferimenti e all'esterno. Di fuori riferimenti includono l'infrastruttura. I riferimenti interno includono il software installato installata e configurazione.

La proprietà *scriptsToDownload* per elencare gli script per scaricare la macchina virtuale. L'oggetto contiene inoltre i riferimenti agli argomenti della riga di comando per diversi tipi di azioni. Queste azioni includono esecuzione installazione predefinita per ogni singolo nodo, un'installazione da eseguire dopo tutti i nodi vengono distribuiti e script aggiuntivi che possono essere specifiche di un modello specifico.

In questo esempio è stata inviata da un modello per la distribuzione MongoDB che richiede un'analisi offrire disponibilità. *ArbiterNodeInstallCommand* è stata aggiunta a *vmScripts* per installare l'analisi.

La sezione variabili è dove si trovano le variabili di definiscono il testo specifico per eseguire lo script con i valori appropriati.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## <a name="return-state-from-a-template"></a>Stato restituito da un modello

Non solo è possibile passare i dati in un modello, è anche possibile condividere dati al modello di chiamata. Nella sezione **output** di un modello collegata, è possibile fornire coppie di parole chiave/valore che possono essere utilizzati dal modello di origine.

Nell'esempio seguente viene illustrato come passare l'indirizzo IP privato generato in un modello collegato.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

All'interno del modello principale, è possibile utilizzare i dati con la sintassi seguente:

    "[reference('master-node').outputs.masterip.value]"

È possibile utilizzare l'espressione seguente nella sezione output o la sezione di risorse del modello principale. È possibile utilizzare l'espressione nella sezione variabili in quanto si basa sullo stato del runtime. Per restituire il valore da modello principale, usare:

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
Per un esempio dell'uso di sezione output di un modello collegato per restituire i dischi dei dati per una macchina virtuale, vedere [creazione di più dischi di dati per una macchina virtuale](resource-group-create-multiple.md#creating-multiple-data-disks-for-a-virtual-machine).

## <a name="define-authentication-settings-for-virtual-machine"></a>Definire le impostazioni di autenticazione per macchina virtuale

È possibile utilizzare lo stesso modello illustrato in precedenza per le impostazioni di configurazione per specificare le impostazioni di autenticazione per una macchina virtuale. Creare un parametro per il passaggio in tipo di autenticazione.

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

Aggiungere variabili per i diversi tipi di autenticazione e viene utilizzata una variabile per archiviare il tipo per la distribuzione in base al valore del parametro.

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

Quando si definisce la macchina virtuale, impostare **osProfile** la variabile creata.

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## <a name="next-steps"></a>Passaggi successivi
- Per conoscere le sezioni del modello, vedere [Creazione di modelli di Azure Manager delle risorse](resource-group-authoring-templates.md)
- Per visualizzare le funzioni sono disponibili all'interno di un modello, vedere [Funzioni di modello di Azure Manager delle risorse](resource-group-template-functions.md)

