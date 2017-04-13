<properties
    pageTitle="Attivare automaticamente le impostazioni di diagnostica utilizzando un modello di gestione risorse | Microsoft Azure"
    description="Informazioni su come usare un modello di Manager delle risorse per creare impostazioni di diagnostiche che consentiranno di flusso i registri diagnostici a un evento hub o memorizzati in un account di archiviazione."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Attivare automaticamente le impostazioni di diagnostica al momento della creazione delle risorse utilizzando un modello di Manager delle risorse
In questo articolo mostra come è possibile utilizzare un [modello di gestione risorse di Azure](../resource-group-authoring-templates.md) per configurare le impostazioni di diagnostica su una risorsa che viene creata. In questo modo è possibile avviare automaticamente streaming i registri diagnostici e il formato per gli hub di evento, l'archiviazione delle immagini in un Account di archiviazione o vengono inviati al Log Analitica quando viene creata una risorsa.

Il metodo per l'attivazione dei registri diagnostici utilizzando un modello di Manager delle risorse dipende dal tipo di risorsa.

- Risorse di **Non elaborazione** (ad esempio, gruppi di sicurezza di rete, App logica, automazione) utilizzano [le impostazioni di diagnostica illustrate in questo articolo](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
- **Calcolare** Risorse (tampone/LAD basate) utilizzano il [file di configurazione tampone/LAD descritto in questo articolo](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

In questo articolo viene descritto come configurare diagnostica usando dei metodi.

La procedura di base è i seguenti:

1. Creare un modello come file JSON che descrive come creare la risorsa e attivare diagnostica.
2. [Distribuire il modello utilizzando il metodo di distribuzione](../resource-group-template-deploy.md).

Di seguito è fornire un esempio del file modello JSON per generare per non elaborazione e risorse di elaborazione.

## <a name="non-compute-resource-template"></a>Modello di calcolo non risorsa
Per le risorse non calcolo, sarà necessario eseguire due operazioni:

1. Aggiungere parametri a blob parametri per il nome dell'account di archiviazione, servizio bus regola ID e/o ID di area di lavoro OMS Log Analitica (attivare l'archiviazione dei registri diagnostici in un account di archiviazione, streaming dei registri a un hub di eventi e/o invio dei log al Log Analitica).

    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Matrice di risorse della risorsa per il quale si vuole abilitare registri diagnostici, aggiungere una risorsa di tipo `[resource namespace]/providers/diagnosticSettings`.

    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

Il blob di proprietà per l'impostazione diagnostica segue [il formato descritto in questo articolo](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Ecco un esempio completo che consente di creare un gruppo di sicurezza di rete e attiva il flusso di hub di eventi e l'archiviazione in un account di archiviazione.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Calcolare il modello di risorse
Per abilitare la diagnostica su una risorsa di calcolo, ad esempio un cluster di macchina virtuale o tessuti servizio, è necessario:

1. Aggiungere l'estensione di Azure diagnostica per la definizione di risorse macchine Virtuali.
2. Specificare un hub di account e/o evento lo spazio di archiviazione come parametro.
3. Aggiungere il contenuto del file XML WADCfg nella casella della proprietà XMLCfg escape tutti i caratteri XML in modo corretto.

> [AZURE.WARNING] Quest'ultimo passaggio può essere difficile da impostare appropriato. Per un esempio che divide lo Schema di configurazione di diagnostica in variabili escape e formattate correttamente, [vedere questo articolo](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) .

L'intero processo, inclusi esempi, viene descritto [in questo documento](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).


## <a name="next-steps"></a>Passaggi successivi
- [Per ulteriori informazioni sui registri diagnostici Azure](./monitoring-overview-of-diagnostic-logs.md)
- [Flusso Azure registri diagnostici a un hub di evento](./monitoring-stream-diagnostic-logs-to-event-hubs.md)
