<properties
    pageTitle="Applicare criteri di gestione risorse macchine virtuali di Azure | Microsoft Azure"
    description="Come applicare criteri a una macchina virtuale Azure Manager delle risorse Linux"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/13/2016"
    ms.author="singhkay"/>

# <a name="apply-policies-to-azure-resource-manager-virtual-machines"></a>Applicare criteri di gestione risorse macchine virtuali di Azure

Con i criteri di un'organizzazione può applicare vari convenzioni e le regole dell'organizzazione. Imposizione di comportamento desiderato consente di ridurre i rischi durante il contributo per il successo dell'organizzazione. In questo articolo illustrate di come è possibile usare i criteri di gestione di risorse Azure per definire il comportamento desiderato per macchine virtuali dell'organizzazione.

La struttura per i passaggi necessari eseguire questa operazione è possibile eseguire sotto

1. Criterio di gestione risorse Azure 101
2. Definire un criterio per la macchina virtuale
3. Creare i criteri
4. Applicare il criterio

## <a name="azure-resource-manager-policy-101"></a>Criterio di gestione risorse Azure 101

Per acquisire familiarità con i criteri di gestione risorse di Azure, è consigliabile leggere l'articolo seguente e quindi continuare con la procedura descritta in questo articolo. Articoli elencati di seguito vengono illustrati la definizione di base e la struttura di un criterio, come criteri viene valutata e offre diversi esempi di definizione dei criteri.

* [Utilizzare criteri per la gestione delle risorse e controllare l'accesso](../resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Definire un criterio per la macchina virtuale

Uno degli scenari comuni per un'organizzazione potrebbe essere solo per consentire agli utenti di creare macchine virtuali di sistemi operativi specifici che sono stati testati per essere compatibile con un'applicazione line. Utilizzo di un criterio di gestione risorse di Azure questa operazione può essere eseguita in pochi passaggi. In questo esempio di criteri, che verranno consentire solo Ubuntu 14.04.2-LTS macchine virtuali di da creare. Definizione del criterio è simile alla seguente

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "Canonical"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "UbuntuServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "14.04.2-LTS"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

Il criterio precedente può essere facilmente modificato per uno scenario di cui consentire o qualsiasi immagine Ubuntu LTS da utilizzare per una distribuzione macchina virtuale con la sotto modifica

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

#### <a name="virtual-machine-property-fields"></a>Campi delle proprietà macchina virtuale

Nella tabella seguente vengono descritte le proprietà di macchina virtuale che possono essere utilizzate come campi nella definizione del criterio. Per ulteriori informazioni sui campi di criteri, vedere l'articolo seguente:

* [Campi e le origini](../resource-manager-policy.md#fields-and-sources)


| Nome campo     | Descrizione                                        |
|----------------|----------------------------------------------------|
| imagePublisher | Indica l'autore dell'immagine               |
| imageOffer     | Specifica l'offerta di publisher immagine selezionata |
| imageSku       | Specifica la SKU per l'offerta selezionata             |
| imageVersion   | Specifica la versione di immagine per la scelta SKU     |

## <a name="create-the-policy"></a>Creare i criteri

Un criterio può essere creato facilmente tramite l'API REST direttamente o i cmdlet di PowerShell. Per creare i criteri, vedere l'articolo seguente:

* [Creazione di un criterio](../resource-manager-policy.md#creating-a-policy)


## <a name="apply-the-policy"></a>Applicare il criterio

Dopo aver creato il criterio è necessario applicare in un ambito definito. L'ambito può essere un abbonamento, gruppo di risorse o anche la risorsa. Per applicare i criteri, vedere l'articolo seguente:

* [Creazione di un criterio](../resource-manager-policy.md#applying-a-policy)
