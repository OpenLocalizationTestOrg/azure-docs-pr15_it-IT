<properties 
    pageTitle="Creare ContentKeys con .NET" 
    description="Informazioni su come creare contenuti tasti che consentono di proteggere l'accesso alle risorse." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="create-contentkeys-with-net"></a>Creare ContentKeys con .NET

> [AZURE.SELECTOR]
- [RESTO](media-services-rest-create-contentkey.md)
- [.NET](media-services-dotnet-create-contentkey.md)

Servizi multimediali consente di creare e distribuire risorse crittografati. Un **ContentKey** offre accesso sicuro a s le **risorse**. 

Quando si crea una nuova attività (ad esempio, prima di [caricare file](media-services-dotnet-upload-files.md)), è possibile specificare le seguenti opzioni di crittografia: **StorageEncrypted**, **CommonEncryptionProtected**o **EnvelopeEncryptionProtected**. 

Quando si esegue risorse per i clienti, è possibile [configurare per risorse da crittografare in modo dinamico](media-services-dotnet-configure-asset-delivery-policy.md) con uno della due crittografia seguenti: **DynamicEnvelopeEncryption** o **DynamicCommonEncryption**.

Le attività crittografate devono essere associate a **ContentKey**s. In questo articolo viene descritto come creare una chiave di contenuto.

>[AZURE.NOTE] Quando si crea un nuovo bene **StorageEncrypted** mediante Media Services .NET SDK, **ContentKey** viene automaticamente creato e collegato all'attività.

##<a name="contentkeytype"></a>ContentKeyType

Uno dei valori che è necessario impostare quando creare un contenuto chiave è il tipo di chiave contenuto. Scegliere uno dei seguenti valori. 

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

##<a id="envelope_contentkey"></a>Creare il tipo di busta ContentKey

Frammento di codice seguente crea una chiave del contenuto del tipo di crittografia della busta. Associa quindi il tasto con il bene specificato.

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

chiamata

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>Creare il tipo di comune ContentKey    

Frammento di codice seguente crea una chiave del contenuto del tipo di crittografia più comuni. Associa quindi il tasto con il bene specificato.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
chiamata

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
