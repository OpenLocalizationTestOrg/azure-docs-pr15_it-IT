<properties 
    pageTitle="Gestire gli account di servizi di supporto Azure con PowerShell" 
    description="Informazioni su come gestire gli account di servizi multimediali di Windows Azure con i cmdlet di PowerShell." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"
    ms.author="juliako"/>


#<a name="manage-azure-media-services-accounts-with-powershell"></a>Gestire gli account di servizi di supporto Azure con PowerShell

> [AZURE.SELECTOR]
- [Portale](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [RESTO](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Per creare un account di servizi multimediali di Windows Azure, è necessario disporre di un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Versione di valutazione gratuita di Azure</a>.

##<a name="overview"></a>Panoramica 

Questo articolo elenca i cmdlet di PowerShell Azure per i servizi di supporto Azure (AMS) in framework Manager delle risorse di Azure. I cmdlet esistono dello spazio dei nomi **Microsoft.Azure.Commands.Media** .

## <a name="versions"></a>Versioni

**ApiVersion**: "2015-10-01"
               

## <a name="new-azurermmediaservice"></a>Nuovo AzureRmMediaService

Crea un servizio di supporto.

### <a name="syntax"></a>Sintassi

Insieme di parametri: StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Insieme di parametri: StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>Parametri

**-ResourceGroupName &lt;stringa&gt;**

Specifica il nome del gruppo di risorse a cui appartiene il servizio di supporto.

Alias | Nessuno
---|---
Obbligatorio?   |  vero
Posizione?   |  0
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Accettare i caratteri jolly?  |FALSO

**Nome account - &lt;stringa&gt;**

Specifica il nome del servizio di supporto.

Alias |Nome
---|---
Obbligatorio? |vero
Posizione? |1
Valore predefinito |Nessuno
Accettare input della pipeline? |FALSO
Accettare i caratteri jolly? |FALSO

**-Percorso &lt;stringa&gt;**

Specifica il percorso della risorsa del servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio? |vero
Posizione? |2
Valore predefinito  |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**-StorageAccountId &lt;stringa&gt;**

Specifica un account di archiviazione principale che associato al servizio di supporto.

- Nuovo account di archiviazione (creato con l'API di gestione delle risorse) è supportata solo.

- Account di archiviazione deve esistere né nella stessa posizione con il servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio? |vero
Posizione? |3
Valore predefinito  |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Nome del set di parametro |StorageAccountIdParamSet
Accettare i caratteri jolly?|FALSO

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Specifica gli account di archiviazione associato al servizio di supporto.

- Nuovo account di archiviazione (creato con l'API di gestione delle risorse) è supportata solo.

- Account di archiviazione deve esistere né nella stessa posizione con il servizio di supporto.

- Un solo account di archiviazione può essere specificato come principale.

Alias |Nessuno
---|---
Obbligatorio?  |vero
Posizione?  |3
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Nome del set di parametro |StorageAccountsParamSet
Accettare i caratteri jolly? |FALSO

**-Tag &lt;Hashtable&gt;**

Specifica una tabella hash di tag associato al servizio di supporto.

- Esempio:@{"tag1"="value1";"tag2"=:value2"}

Alias |Nessuno
---|---
Obbligatorio?  |FALSO
Posizione?  |denominata
Valore predefinito |Nessuno
Accettare input della pipeline? |FALSO
Accettare i caratteri jolly? |FALSO

**&lt;ParametriComando&gt;**

Questo cmdlet supporta i parametri comuni:-eseguire il Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer - PipelineVariable, - dettagliato, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Ingressi

Il tipo di input è il tipo di oggetti che è possibile inviare al cmdlet.

### <a name="outputs"></a>Output

Il tipo di output è il tipo di oggetti che genera il cmdlet.

## <a name="set-azurermmediaservice"></a>Set AzureRmMediaService

Aggiorna un servizio di supporto.

### <a name="syntax"></a>Sintassi

    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>Parametri

**-ResourceGroupName &lt;stringa&gt;**

Specifica il nome del gruppo di risorse a cui appartiene il servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio?  |vero
Posizione?  |0
Valore predefinito |Nessuno
Accettare Input della Pipeline? |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**Nome account - &lt;stringa&gt;**

Specifica il nome del servizio di supporto.

Alias |Nome
---|---
Obbligatorio? |Vero
Posizione? |1
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Specifica gli account di archiviazione associato al servizio di supporto.

- Nuovo account di archiviazione (creato con l'API di gestione delle risorse) è supportata solo.

- Account di archiviazione deve esistere né nella stessa posizione con il servizio di supporto.

- Un solo account di archiviazione può essere specificato come principale.

Alias |Nessuno
---|---
Obbligatorio? |FALSO
Posizione? |Denominata
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Nome del set di parametro |StorageAccountsParamSet
Accettare i caratteri jolly? |FALSO

**-Tag &lt;Hashtable&gt;**

Specifica una tabella hash dei tag associati a questo servizio di supporto.

- I tag associati al servizio di supporto vengono sostituiti con valore specificato dal cliente.

Alias |Nessuno
---|---
Obbligatorio? |FALSO
Posizione?  |Denominata
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**&lt;ParametriComando&gt;**

Questo cmdlet supporta i parametri comuni:-eseguire il Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer - PipelineVariable, - dettagliato, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Ingressi

Il tipo di input è il tipo di oggetti che è possibile inviare al cmdlet.

### <a name="outputs"></a>Output

Il tipo di output è il tipo di oggetti che genera il cmdlet.

## <a name="remove-azurermmediaservice"></a>Rimuovi AzureRmMediaService

Rimuove un servizio di supporto.

### <a name="syntax"></a>Sintassi

    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametri

**-ResourceGroupName &lt;stringa&gt;**

Specifica il nome del gruppo di risorse a cui appartiene il servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio? |vero
Posizione? |0
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**Nome account - &lt;stringa&gt;**

Specifica il nome del servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio? |vero
Posizione? |2
Valore predefinito |Nessuno
Accettare input della pipeline?  |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**&lt;ParametriComando&gt;**

Questo cmdlet supporta i parametri comuni:-eseguire il Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer - PipelineVariable, - dettagliato, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Ingressi

Il tipo di input è il tipo di oggetti che è possibile inviare al cmdlet.

### <a name="outputs"></a>Output

Il tipo di output è il tipo di oggetti che genera il cmdlet.

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService

Ottiene tutti i servizi multimediali di un gruppo di risorse o un servizio di supporto con un nome specificato.

### <a name="syntax"></a>Sintassi

ParameterSet: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>] 

ParameterSet: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametri

**-ResourceGroupName &lt;stringa&gt;**

Specifica il nome del gruppo di risorse a cui appartiene il servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio? |vero
Posizione?  |0
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Nome del set di parametro |ResourceGroupParameterSet, AccountNameParameterSet
Accettare i caratteri jolly?   FALSO

**Nome account - &lt;stringa&gt;**

Specifica il nome del servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio? |vero
Posizione?  |1
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Nome del set di parametro  |AccountNameParameterSet
Accettare i caratteri jolly? |FALSO

**&lt;ParametriComando&gt;**

Questo cmdlet supporta i parametri comuni:-eseguire il Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer - PipelineVariable, - dettagliato, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Ingressi

Il tipo di input è il tipo di oggetti che è possibile inviare al cmdlet.

### <a name="outputs"></a>Output

Il tipo di output è il tipo di oggetti che genera il cmdlet.

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys

Ottiene i tasti di un servizio di supporto.

### <a name="syntax"></a>Sintassi

    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametri

**-ResourceGroupName &lt;stringa&gt;**

Specifica il nome del gruppo di risorse a cui appartiene il servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio? |vero
Posizione?  |0
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**Nome account - &lt;stringa&gt;**

Specifica il nome del servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio? |vero
Posizione? |1
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**&lt;ParametriComando&gt;**

Questo cmdlet supporta i parametri comuni:-eseguire il Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer - PipelineVariable, - dettagliato, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Ingressi

Il tipo di input è il tipo di oggetti che è possibile inviare al cmdlet.

### <a name="outputs"></a>Output

Il tipo di output è il tipo di oggetti che genera il cmdlet.

## <a name="set-azurermmediaservicekey"></a>Set AzureRmMediaServiceKey

Rigenera una chiave primaria o secondaria di un servizio di supporto.

### <a name="syntax"></a>Sintassi

    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>Parametri

**-ResourceGroupName &lt;stringa&gt;**

Specifica il nome del gruppo di risorse a cui appartiene il servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio?  |vero
Posizione?  |0
Valore predefinito |Nessuno
Accettare input della pipeline?  |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**Nome account - &lt;stringa&gt;**

Specifica il nome del servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio? |vero
Posizione?  |1
Valore predefinito |Nessuno
Accettare input della pipeline?   |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**-KeyType &lt;KeyType&gt;**

Specifica il tipo di chiave del servizio di supporto.

- Primario o secondario

Alias |Nessuno
---|---
Obbligatorio?  |vero
Posizione?  |2
Valore predefinito |Nessuno
Accettare input della pipeline? |FALSO
Accettare i caratteri jolly? |FALSO

**&lt;ParametriComando&gt;**

Questo cmdlet supporta i parametri comuni:-eseguire il Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer - PipelineVariable, - dettagliato, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Ingressi

Il tipo di input è il tipo di oggetti che è possibile inviare al cmdlet.

### <a name="outputs"></a>Output

Il tipo di output è il tipo di oggetti che genera il cmdlet.

## <a name="sync-azurermmediaservicestoragekeys"></a>Sincronizzazione AzureRmMediaServiceStorageKeys

Sincronizza le chiavi account lo spazio di archiviazione per un account di archiviazione associato al servizio di supporto.

### <a name="syntax"></a>Sintassi

    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parametri

**-ResourceGroupName &lt;stringa&gt;**

Specifica il nome del gruppo di risorse a cui appartiene il servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio? |vero
Posizione? |0
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**Nome account - &lt;stringa&gt;**

Specifica il nome del servizio di supporto.

Alias |Nessuno
---|---
Obbligatorio? |vero
Posizione? |1
Valore predefinito |Nessuno
Accettare input della pipeline? |true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**-StorageAccountId &lt;stringa&gt;**

Specifica l'account di archiviazione associato al servizio di supporto.

Alias |ID
---|---
Obbligatorio? |vero
Posizione?  |2
Valore predefinito |Nessuno
Accettare input della pipeline? |      true(ByPropertyName)
Accettare i caratteri jolly? |FALSO

**&lt;ParametriComando&gt;**

Questo cmdlet supporta i parametri comuni:-eseguire il Debug, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer - PipelineVariable, - dettagliato, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Ingressi

Il tipo di input è il tipo di oggetti che è possibile inviare al cmdlet.

### <a name="outputs"></a>Output

Il tipo di output è il tipo di oggetti che genera il cmdlet.

## <a name="next-step"></a>Passaggio successivo 

Controllare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
