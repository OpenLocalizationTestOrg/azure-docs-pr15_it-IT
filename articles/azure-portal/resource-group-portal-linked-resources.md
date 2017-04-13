<properties 
    pageTitle="Risorse correlate e collegate nella raccolta riquadro" 
    description="Informazioni sulle risorse correlate e collegate che vengono visualizzate nella raccolta riquadro del portale di Azure anteprima." 
    services="azure-portal" 
    documentationCenter="" 
    authors="adamabdelhamed" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-portal" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/16/2015" 
    ms.author="adamab"/>

# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Risorse correlate e collegate nella raccolta riquadro

La raccolta riquadro consente di trovare le sezioni per una determinata risorsa e trascinarli nel blade corrente. Usando la raccolta di sezione, è possibile creare visualizzazioni di gestione che si estendono su risorse. Per una risorsa specificata, le risorse correlate includono tutte le risorse che condividono stesso gruppo di risorse come risorsa e tutte le risorse che collegano da e verso la risorsa.

## <a name="linked-resources-in-azure-resource-manager"></a>Risorse collegate in Gestione risorse di Azure

Il collegamento è una funzionalità di gestione risorse di Azure.  Consente di dichiarare relazioni tra le risorse anche se non si trovano nello stesso gruppo di risorse. Il collegamento ha alcun effetto in fase di esecuzione di risorse, Nessun impatto sulla fatturazione e alcun effetto sull'accesso basato sui ruoli.  È sufficiente un meccanismo che è possibile utilizzare per rappresentare le relazioni in modo che gli strumenti come raccolta riquadro può fornire una gestione RTF esperienza utente.  Strumenti di possono controllare i collegamenti mediante i collegamenti di API e fornire esperienze anche di gestione delle relazioni personalizzate. 

## <a name="how-do-i-link-my-resources"></a>Come collegare le risorse

Quando si creano risorse tramite il portale o distribuzione di un modello tramite PowerShell Azure o CLI Azure, i collegamenti vengono creati automaticamente per alcune risorse dipendenti. È possibile collegare le risorse anche a livello di programmazione tramite l' [API REST di risorse collegate](https://msdn.microsoft.com/library/azure/mt238499.aspx) o se dichiarazione le relazioni del modello. Per informazioni complete di utilizzo delle risorse collegate, vedere [collegamento risorse in Gestione risorse di Azure](../resource-group-link-resources.md).

## <a name="next-steps"></a>Passaggi successivi

- Se è necessaria un'introduzione alla scrittura di modelli di Manager delle risorse di Azure, vedere [modelli di creazione condivisa](../resource-group-authoring-templates.md).
- Per esplorare maggiori dettagli sulla creazione di collegamenti tra le risorse, vedere [collegamento risorse in Gestione risorse di Azure](../resource-group-link-resources.md).
- Per informazioni più sull'uso dei gruppi di risorse tramite il portale di anteprima, vedere [tramite il portale di anteprima Azure per gestire le risorse Azure](resource-group-portal.md).
