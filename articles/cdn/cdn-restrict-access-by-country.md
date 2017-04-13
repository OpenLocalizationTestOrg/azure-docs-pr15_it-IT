<properties
    pageTitle="Limitare l'accesso al contenuto Azure CDN per paese | Microsoft Azure"
    description="Informazioni su come limitare l'accesso al contenuto Azure CDN utilizzando la funzionalità di filtro geografico."
    services="cdn"
    documentationCenter=""
    authors="camsoper, rli"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="casoper"/>

#<a name="restrict-access-to-your-content-by-country---verizon"></a>Limitare l'accesso al contenuto per paese - Verizon

> [AZURE.SELECTOR]
- [Verizon](cdn-restrict-access-by-country.md)
- [Akamai Standard](cdn-restrict-access-by-country-akamai.md)

##<a name="overview"></a>Panoramica

Quando un utente richiede al contenuto, per impostazione predefinita, il contenuto sia served indipendentemente da dove l'utente ha effettuato questo richiesta da. In alcuni casi, è consigliabile limitare l'accesso al contenuto per paese. In questo argomento viene illustrato come utilizzare la funzionalità di **Filtro geografico** per configurare il servizio per consentire o bloccare l'accesso per paese.

> [AZURE.IMPORTANT] I prodotti Verizon e Akamai forniscono le stesse funzionalità di filtro geografico, ma l'interfaccia utente diversa. Questo documento descrive l'interfaccia per **Azure CDN Standard da Verizon** e **Azure CDN Premium da Verizon**. Per i filtri a geografico con **Azure CDN Standard da Akamai**, vedere [limitare l'accesso al contenuto per paese - Akamai](cdn-restrict-access-by-country-akamai.md).

Per informazioni su considerazioni valide per la configurazione di questo tipo di restrizione, vedere la sezione [Considerazioni](cdn-restrict-access-by-country.md#considerations) alla fine dell'argomento.  

>[AZURE.NOTE] Dopo la configurazione è impostata, verrà applicata a tutti gli endpoint **CDN Azure da Verizon** nel profilo CDN Azure.

![Paese filtro](./media/cdn-filtering/cdn-country-filtering.png)

##<a name="step-1-define-the-directory-path"></a>Passaggio 1: Definire il percorso della directory

Quando si configura un filtro di paese, è necessario specificare il percorso relativo al percorso in cui è consentiti o negare l'accesso agli utenti. È possibile applicare il filtro paese per tutti i file con "/" o le cartelle selezionate specificando i percorsi di directory.

Filtro percorso directory di esempio:

    /                                 
    /Photos/
    /Photos/Strasbourg

##<a name="step-2-define-the-action-block-or-allow"></a>Passaggio 2: Definire l'azione: bloccare o consentire

**Blocco:** Gli utenti di paesi specificati non consentiti l'accesso alle risorse richieste da tale percorso ricorsiva. Se nessuna altre opzioni di filtro paese sono stati configurati per tale posizione, quindi tutti gli altri utenti saranno autorizzati ad accedere.

**Consentire:** Solo gli utenti dai paesi specificati saranno autorizzati ad accedere alle risorse richieste da tale percorso ricorsiva.

##<a name="step-3-define-the-countries"></a>Passaggio 3: Definire i paesi

Selezionare i paesi che si desidera bloccare o consentire per il percorso. Per ulteriori informazioni, vedere [CDN Azure dai codici paese Verizon](https://msdn.microsoft.com/library/mt761717.aspx).

Ad esempio, la regola di blocco /Photos/Strasburgo/filtrerà file, tra cui:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##<a name="country-codes"></a>Codici paese

La funzionalità di **Filtro geografico** utilizza codici paese per definire i paesi da cui consentita o bloccata per una directory protetta una richiesta. Sono disponibili i codici dei paesi in [Rete CDN di Azure dai codici paese Verizon](https://msdn.microsoft.com/library/mt761717.aspx). Se si specifica (Europa) "dell'Unione europea" o "PA" (Asia/Pacifico), un sottoinsieme di indirizzi IP provenienti da un paese in tale aree geografiche verrà bloccato o consentito.


##<a id="considerations"></a>Considerazioni

- Può richiedere fino a 90 minuti per le modifiche alla configurazione del filtro paese abbiano effetto.
- Questa caratteristica non supporta i caratteri jolly (ad esempio, "*").
- Paese associata al relativo percorso di configurazione del filtro sarà applicata in modo ricorsivo tale percorso.
- Solo una regola può essere applicata allo stesso percorso relativo (è possibile creare più filtri paese associati allo stesso percorso. Tuttavia, una cartella potrebbe essere più filtri paese. Ciò è dovuto natura ricorsiva dei filtri paese. In altre parole, è possibile assegnare una sottocartella di una cartella già configurata un filtro paese diverso.
