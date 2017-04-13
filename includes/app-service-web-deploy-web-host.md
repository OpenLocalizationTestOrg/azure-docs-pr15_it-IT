### <a name="app-service-plan"></a>Piano di servizio di App

Crea il piano di servizio di hosting web app. Il nome del piano tramite il parametro **hostingPlanName** . Il percorso del piano è nella stessa posizione utilizzata per il gruppo di risorse. Le dimensioni di livello e lavoro prezzi vengono specificati nei parametri **sku** e **workerSize**

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },

