<properties
    pageTitle="Limitare l'accesso al contenuto Azure CDN per paese | Microsoft Azure"
    description="Informazioni su come limitare l'accesso al contenuto Azure CDN utilizzando la funzionalità di filtro geografico."
    services="cdn"
    documentationCenter=""
    authors="camsoper, rli"
    manager="akucer"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="Lichard"/>

#<a name="restrict-access-to-your-content-by-country---akamai"></a>Limitare l'accesso al contenuto per paese - Akamai

> [AZURE.SELECTOR]
- [Verizon](cdn-restrict-access-by-country.md)
- [Akamai Standard](cdn-restrict-access-by-country-akamai.md)

##<a name="overview"></a>Panoramica

Quando un utente richiede al contenuto, per impostazione predefinita, il contenuto sia served indipendentemente da dove l'utente ha effettuato questo richiesta da. In alcuni casi, è consigliabile limitare l'accesso al contenuto per paese. In questo argomento viene illustrato come utilizzare la funzionalità di **Filtro geografico** per configurare il servizio per consentire o bloccare l'accesso per paese.

> [AZURE.IMPORTANT] I prodotti Verizon e Akamai forniscono le stesse funzionalità di filtro geografico, ma l'interfaccia utente diversa. Questo documento descrive l'interfaccia di **Azure CDN Standard da Akamai**. Per i filtri a geografico con **Azure CDN Standard/Premium da Verizon**, vedere [limitare l'accesso al contenuto per paese - Verizon](cdn-restrict-access-by-country.md).

Per informazioni su considerazioni valide per la configurazione di questo tipo di restrizione, vedere la sezione [Considerazioni](cdn-restrict-access-by-country.md#considerations) alla fine dell'argomento.  

![Paese filtro](./media/cdn-filtering/cdn-country-filtering-akamai.png)

##<a name="step-1-define-the-directory-path"></a>Passaggio 1: Definire il percorso della directory

Selezionare l'endpoint all'interno del portale e trovare la scheda filtro geografico nella barra di spostamento sinistro per trovare questa caratteristica.

Quando si configura un filtro di paese, è necessario specificare il percorso relativo al percorso in cui è consentiti o negare l'accesso agli utenti. È possibile applicare il filtro geografico per tutti i file con "/" o le cartelle selezionate specificando i percorsi di directory "/ immagini /". È inoltre possibile applicare il filtro geografico in un unico file specificando il file e escludendo la barra rovesciata "/ pictures/city.png".

Filtro percorso directory di esempio:

    /                                 
    /Photos/
    /Photos/Strasbourg/
    /Photos/Strasbourg/city.png

##<a name="step-2-define-the-action-block-or-allow"></a>Passaggio 2: Definire l'azione: bloccare o consentire

**Blocco:** Gli utenti di paesi specificati non consentiti l'accesso alle risorse richieste da tale percorso ricorsiva. Se nessuna altre opzioni di filtro paese sono stati configurati per tale posizione, quindi tutti gli altri utenti saranno autorizzati ad accedere.

**Consentire:** Solo gli utenti dai paesi specificati saranno autorizzati ad accedere alle risorse richieste da tale percorso ricorsiva.

##<a name="step-3-define-the-countries"></a>Passaggio 3: Definire i paesi

Selezionare i paesi che si desidera bloccare o consentire per il percorso. Per ulteriori informazioni, vedere [CDN Azure dai codici paese Akamai](https://msdn.microsoft.com/library/mt761717.aspx).

Ad esempio, la regola di blocco /Photos/Strasburgo/filtrerà file, tra cui:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##<a name="country-codes"></a>Codici paese

La funzionalità di **Filtro geografico** utilizza codici paese per definire i paesi da cui consentita o bloccata per una directory protetta una richiesta. Sono disponibili i codici dei paesi in [Rete CDN di Azure dai codici paese Akamai](https://msdn.microsoft.com/library/mt761717.aspx). 

##<a id="considerations"></a>Considerazioni

- Può essere necessaria fino a qualche minuto le modifiche al proprio paese configurazione del filtro per rendere effettive.
- Questa caratteristica non supporta i caratteri jolly (ad esempio, "*").
- La configurazione di filtro geografico associata con il relativo percorso sarà applicata in modo ricorsivo tale percorso.
- Solo una regola può essere applicata allo stesso percorso relativo (è possibile creare più filtri paese associati allo stesso percorso. Tuttavia, una cartella potrebbe essere più filtri paese. Ciò è dovuto natura ricorsiva dei filtri paese. In altre parole, è possibile assegnare una sottocartella di una cartella già configurata un filtro paese diverso.

