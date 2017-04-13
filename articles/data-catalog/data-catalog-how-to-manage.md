<properties
   pageTitle="Come gestire le risorse dati | Microsoft Azure"
   description="Articolo procedure evidenziazione come controllare la visibilità e proprietà dei beni dati registrato nel catalogo dati di Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>


# <a name="how-to-manage-data-assets"></a>Modalità di gestione dati

## <a name="introduction"></a>Introduzione

**Catalogo dati di Azure** offre funzionalità per l'individuazione di origine dati, consentendo agli utenti di individuare facilmente e comprendere le origini dati che è necessario eseguire l'analisi e prendere decisioni. Queste funzionalità di individuazione di effettuare l'impatto più significativo quando tutti gli utenti possono trovare e comprendere più ampia gamma di origini dati disponibili. Con questo, il comportamento predefinito del catalogo dati per tutte le origini dati registrate sia visibile a – e rilevati dai: tutti gli utenti del catalogo.

Catalogo dati è possibile accedere ai dati. Accesso ai dati verrà controllata dal proprietario dell'origine dati. Catalogo dati consente agli utenti per individuare origini dati e visualizzare i metadati relativi alle origini registrate nel catalogo.

Si possono verificare situazioni, tuttavia, in cui le origini dati solo devono essere visibili a specifici utenti o ai membri dei gruppi specifici. Per questi scenari, catalogo dati consente agli utenti di assunzione dei beni registrati dati all'interno del catalogo e quindi controllare la visibilità dei beni sono proprietari.

> [AZURE.NOTE] Le funzionalità descritte in questo articolo sono disponibili solo nel Standard Edition di Azure catalogo dati. La versione gratuita non sono disponibili funzionalità di supporto per la proprietà e limitare la visibilità risorse di dati.

## <a name="managing-ownership-of-data-assets"></a>Gestione delle proprietà di attività di dati
Per impostazione predefinita, le attività di dati registrate nel catalogo dei dati sono prive; tutti gli utenti con autorizzazione per accedere al catalogo consentono di individuare e annotare queste risorse. Gli utenti possono diventare proprietari di dati senza proprietario e quindi di limitare la visibilità dei beni che sono proprietari.

Quando si appartiene una risorsa di dati nel catalogo dei dati, solo gli utenti autorizzati dai proprietari consentono di individuare la risorsa e visualizzare i metadati e solo i proprietari possono eliminare le risorse dal catalogo.

> [AZURE.NOTE] La proprietà nel catalogo dati interessa solo i metadati archiviati nel catalogo. Non ha previsto tutte le autorizzazioni dell'origine dati sottostante.

### <a name="taking-ownership"></a>Acquisizione della proprietà
Gli utenti possono diventare proprietari dei beni dati selezionando l'opzione "Diventa proprietario" nel portale di catalogo dati. È necessaria alcuna autorizzazione speciale per diventare proprietario di un bene dati senza proprietario; tutti gli utenti possono diventare proprietari di un bene dati senza proprietario.

### <a name="adding-owners-and-co-owners"></a>Aggiunta di proprietari e condivisa
Se una risorsa di dati è già proprietario, gli utenti non è possibile semplicemente richiedere la proprietà: deve essere aggiunto come comproprietari da un proprietario esistente. Qualsiasi proprietario possibile aggiungere altri utenti o gruppi di sicurezza come proprietari condivisa.

> [AZURE.NOTE] È buona norma disporre di almeno due persone come proprietari per le risorse dati secondari.

### <a name="removing-owners"></a>Rimozione di proprietari
Così come qualsiasi proprietario della risorsa è possibile aggiungere comproprietari, qualsiasi proprietario bene può rimuovere qualsiasi comproprietario.

Se un proprietario della risorsa rimuove a se stesso come un proprietario, ha non è più possibile gestire le risorse. Se un proprietario della risorsa rimuove a se stesso come un proprietario, non esistono nessun altro comproprietari bene verrà ripristinata a uno stato senza proprietario.

## <a name="visibility"></a>Visibilità
I proprietari dei beni di dati è possibile controllare la visibilità dei beni dati che sono proprietari. Per limitare la visibilità da quello predefinito, nel punto in cui tutti gli utenti del catalogo dati consentono di individuare e visualizzare le risorse dati: il proprietario della risorsa possibile attivare o disattivare l'impostazione di visibilità da "Tutti" a "Proprietari e utenti" nelle proprietà per la risorsa. Proprietari quindi possono aggiungere utenti e gruppi di sicurezza specifici.

> [AZURE.NOTE] Se possibile, le autorizzazioni di proprietario e visibilità risorse devono essere assegnate ai gruppi di sicurezza e non ai singoli utenti.

## <a name="catalog-administrators"></a>Amministratori di catalogo
Gli amministratori di catalogo dei dati disponibili in modo implicito comproprietari di tutte le risorse nel catalogo. I proprietari di risorse non è possibile rimuovere la visibilità dagli amministratori catalogo e gli amministratori possono gestire la proprietà e la visibilità per tutte le risorse dati nel catalogo.

## <a name="summary"></a>Riepilogo
Modello di crowdsourcing del catalogo dati per i metadati e dati individuazione dei beni consente tutti gli utenti di catalogo contribuire e individuare. Edizione Standard del catalogo dati offre funzionalità per la gestione e la proprietà per limitare la visibilità e l'uso delle risorse di dati specifici.
