<properties
    pageTitle="Risoluzione dei problemi di controllo accesso basato sui ruoli | Microsoft Azure"
    description="È possibile ottenere assistenza per problemi o domande sulle risorse di controllo dell'accesso basato su ruoli."
    services="azure-portal"
    documentationCenter="na"
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="kgremban"/>

# <a name="role-based-access-control-troubleshooting"></a>Risoluzione dei problemi di controllo dell'accesso basato sui ruoli

## <a name="introduction"></a>Introduzione

[Controllo dell'accesso basato sui ruoli](role-based-access-control-configure.md) è una funzionalità potente che consente di delegare diffusamente accesso alle risorse di Azure. Di conseguenza, che si può avere la certezza la concessione di una persona specifica il diritto di utilizzare esattamente quali elementi sono necessari e non superiore. Tuttavia, in alcuni casi il modello di risorse per le risorse Azure può essere complesso e può essere difficile capire esattamente quali si desidera concedere le autorizzazioni per.

In questo documento consentirà di capire cosa aspettarsi quando usando alcuni dei ruoli nel portale di Azure. Questi tre ruoli coprono tutti i tipi di risorse:

- Proprietario  
- Per i collaboratori  
- Utilità per la lettura  

Proprietari e collaboratori abbiano accesso completo all'ambiente di gestione, ma un collaboratore non è possibile concedere l'accesso ad altri utenti o gruppi. È possibile ottenere elementi più interessante con il ruolo di utilità per la lettura, in modo che sia nel punto in cui verranno dedicate del tempo. Vedere l' [articolo avviato get controllo dell'accesso basato sui ruoli](role-based-access-control-configure.md) per informazioni dettagliate su come concedere l'accesso.

## <a name="app-service-workloads"></a>Carichi di lavoro di applicazione del servizio

### <a name="write-access-capabilities"></a>Funzionalità di scrittura

Se si concede l'accesso in sola lettura un utente a una singola web app, alcune caratteristiche sono disabilitate che non ci si potrebbe aspettare. Le seguenti funzionalità di gestione richiedono **scrittura** di accedere a un'app web (Collaboratore o proprietario) e non saranno disponibili in uno scenario di sola lettura.

- Comandi (ad esempio, inizio, tabulazioni e così via)
- Ad esempio la modifica delle impostazioni generali di configurazione, le impostazioni di scala, le impostazioni di backup e le impostazioni di monitoraggio
- Accesso alle credenziali di pubblicazione e altre informazioni riservate come le impostazioni dell'app e le stringhe di connessione
- Trasmissione dei log
- Configurazione dei registri diagnostici
- Console (prompt dei comandi)
- Distribuzioni attive e recenti (per la distribuzione continua fra locale)
- Stimata dedicato
- Test Web
- Rete virtuale (visibile solo a un lettore se in precedenza è stata configurata una rete virtuale da un utente con accesso scrittura).

Se non è possibile accedere a uno di questi riquadri, è necessario chiedere all'amministratore per l'accesso per i collaboratori in web app.

### <a name="dealing-with-related-resources"></a>Gestire le risorse correlate

App Web vengono complicate dalla presenza di una serie di risorse diversi che interplay. Ecco un gruppo di risorse tipiche di due siti Web:

![Gruppo di risorse Web app](./media/role-based-access-control-troubleshooting/website-resource-model.png)

Di conseguenza, se un utente concedere l'accesso a solo il web app, la maggior parte delle funzionalità di e il sito Web nel portale di Azure sarà disabilitata.

Questi elementi richiedono accesso **scrittura** per il **piano di servizio App** che corrisponde al sito Web:  

- Visualizzazione dell'applicazione web dei prezzi del livello (gratuito o Standard)  
- Configurazione di scala (numero di istanze, le dimensioni del computer virtuali, impostazioni autoscale)  
- Quote (spazio di archiviazione, la larghezza di banda, CPU)  

Questi elementi richiedono accesso **scrivere** all'intero **gruppo di risorse** contenente il sito Web:  

- I certificati SSL e associazioni (è poiché i certificati SSL possono essere condiviso tra siti nello stesso gruppo di risorse e geografico posizione)  
- Regole di avviso  
- Impostazioni AutoScale  
- Componenti di informazioni approfondite dell'applicazione  
- Test Web  

## <a name="virtual-machine-workloads"></a>Macchina virtuale carichi di lavoro

Molto simile con web apps, alcune caratteristiche in e il computer virtuale richiedono l'accesso scrittura alla macchina virtuale o ad altre risorse nel gruppo di risorse.

Macchine virtuali riguardano nomi di dominio, reti virtuali, gli account di archiviazione e le regole di avviso.

Questi elementi richiedono **scrittura** di accedere a **macchina virtuale**:

- Punti finali  
- Indirizzi IP  
- Dischi  
- Estensioni  

Questi richiedono **scrittura** di accedere a **macchina virtuale**e il **gruppo di risorse** (insieme il nome di dominio) che si trova in:  

- Set di disponibilità  
- Caricare set non bilanciate  
- Regole di avviso  

Se non è possibile accedere a uno di questi riquadri, è necessario chiedere all'amministratore per l'accesso per i collaboratori al gruppo di risorse.

## <a name="see-more"></a>Ulteriori informazioni
- [Controllo dell'accesso basato su ruoli](role-based-access-control-configure.md): Guida introduttiva a RBAC nel portale di Azure.
- [Ruoli predefiniti](role-based-access-built-in-roles.md): informazioni sui ruoli disponibili standard in RBAC.
- [Ruoli personalizzati in Azure RBAC](role-based-access-control-custom-roles.md): informazioni su come creare ruoli personalizzati secondo le proprie esigenze di accesso.
- [Creare un accesso report Cronologia modifiche](role-based-access-control-access-change-history-report.md): tenere traccia di modifica delle assegnazioni di ruolo in RBAC.
