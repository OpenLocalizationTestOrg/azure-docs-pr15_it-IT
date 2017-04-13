<properties
    pageTitle="Sincronizzazione di Azure AD Connect: concetti tecnici | Microsoft Azure"
    description="Vengono descritti i concetti tecnici di sincronizzazione di Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-technical-concepts"></a>Sincronizzazione di Azure AD Connect: concetti tecnici
In questo articolo è riportato un riepilogo dell'argomento [architettura delle informazioni](active-directory-aadconnectsync-technical-concepts.md).

Sincronizzazione di Azure AD Connect si basa su una piattaforma di sincronizzazione metadirectory a tinta unita.
Nelle sezioni seguenti sono concetti per la sincronizzazione metadirectory.
Si su MIIS, ILM e FIM, i servizi di sincronizzazione Azure Active Directory offre la piattaforma successiva per la connessione a origini dati, la sincronizzazione dei dati tra origini dati, nonché il provisioning e deprovisioning delle identità.

![Concetti tecnici](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

Nelle sezioni seguenti sono disponibili ulteriori dettagli sui seguenti aspetti del servizio di sincronizzazione FIM:

- Connettore
- Flusso di attributi
- Spazio connettore
- Metaverse
- Il provisioning

## <a name="connector"></a>Connettore

I moduli di codice utilizzato per comunicare con una directory connessa sono denominati connettori (precedentemente noti come agenti di gestione (MAs)).

Questi sono installati nel computer di sincronizzazione di Azure AD Connect.
I connettori consentono senza agenti di comunicare con i protocolli di sistema remoto anziché ricorrere alla distribuzione di agenti specializzati. Di conseguenza, rischio e gli orari di distribuzione, soprattutto quando si gestiscono applicazioni critiche e sistemi.

Nella figura riportata sopra, il connettore è sinonimo di spazio connettore ma include tutte le comunicazioni con il sistema esterno.

Il connettore è responsabile per l'importazione tutti e la funzionalità di esportazione al sistema e consente agli sviluppatori di dover imparare a cui connettersi a livello nativo ogni sistema quando si usa il provisioning dichiarativi per personalizzare le trasformazioni dei dati.

Importazioni ed esportazioni solo si verificano quando pianificato, consentendo per isolamento ulteriormente dalle modifiche all'interno del sistema, dal momento che le modifiche non vengono propagate automaticamente all'origine dati connessa. Inoltre, gli sviluppatori possono anche creare propri connettori per la connessione a qualsiasi origine dati.

## <a name="attribute-flow"></a>Flusso di attributi

Metaverse è la visualizzazione consolidata di tutte le identità unite da adiacenti spazi connettore. Nella figura precedente, il flusso di attributi è rappresentato da linee con frecce per il flusso di ingresso e in uscita. Il flusso di attributi è il processo di copia o la trasformazione dei dati da un sistema e tutti attributo flussi (in ingresso o in uscita).

Il flusso di attributi quando tra lo spazio connettore e il metaverse direzioni le operazioni di sincronizzazione (completo o delta) sono programmate per l'esecuzione.

Attributo flusso si verifica solo quando vengono eseguiti questi sincronizzazione. Attributo flussi sono definiti in regole di sincronizzazione. Possono essere in ingresso (ISR nell'immagine precedente) o in uscita (OSR nell'immagine precedente).

## <a name="connected-system"></a>Sistema connesso

Sistema connesso (o directory connessa) è che fa riferimento al sistema remoto Azure AD Connect sincronizzazione è connesso a e leggere e scrivere dati identità da e verso.

## <a name="connector-space"></a>Spazio connettore

Ogni origine dati connessa viene rappresentato come un sottoinsieme filtrato degli oggetti e attributi nell'area connettore.
Questo consente al servizio di sincronizzazione di funzionare in locale senza la necessità di contattare il sistema remoto quando si sincronizzano gli oggetti e limita l'interazione all'importazione e consente di esportare solo.

Quando l'origine dati e il connettore hanno la possibilità di fornire un elenco delle modifiche (un'importazione delta), quindi l'efficienza operativa aumenta notevolmente come solo le modifiche apportate dall'ultimo ciclo di polling sono scambiati. Lo spazio connettore isola l'origine dati connessa dalle modifiche propagazione automaticamente richiedendo che la pianificazione di connettore Importa ed Esporta. Questa assicurazione aggiunta concede tranquillità durante la verifica, la visualizzazione in anteprima o conferma prossimo aggiornamento.

## <a name="metaverse"></a>Metaverse

Metaverse è la visualizzazione consolidata di tutte le identità unite da adiacenti spazi connettore.

Come identità sono collegate tra loro e autorità è assegnato per diversi attributi tramite mapping del flusso di importazione, l'oggetto metaverse centrale inizia a informazioni di aggregazione da più sistemi. Da questo flusso di attributo oggetto mapping contengono informazioni ai sistemi in uscita.

Gli oggetti vengono creati quando un sistema rilevanti progetti loro nel metaverse. Non appena vengono rimosse tutte le connessioni, l'oggetto metaverse viene eliminato.

Gli oggetti nel metaverse non possono essere modificati direttamente. Tutti i dati dell'oggetto devono contribuiti tramite il flusso di attributi. Metaverse mantiene persistenti connettori con ogni spazio connettore. Questi connettori non richiedono nuova valutazione per ogni sincronizzazione. Ciò significa che sincronizzazione di Azure AD Connect non è necessario individuare ogni volta che l'oggetto remoto corrispondente. Consente di evitare la necessità di agenti costosi impedire la modifica degli attributi che normalmente sarebbe responsabile per correlare gli oggetti.

Durante l'individuazione dei nuove origini dati che possono contenere oggetti preesistenti che devono essere gestiti, sincronizzazione di Azure AD Connect utilizza un processo denominato una regola di join da valutare candidati potenziali con cui stabilire un collegamento.
Dopo aver stabilito il collegamento, non si ripresenta la valutazione e il flusso di attributi normale può verificarsi tra l'origine dati connessa remoto e il metaverse.

## <a name="provisioning"></a>Il provisioning

Quando progetti di origine autorevole un nuovo oggetto nel metaverse un nuovo oggetto spazio connettore può essere creato in un altro connettore che rappresenta un'origine dati connessa downstream.

Ciò per sé stabilisce un collegamento e il flusso di attributi è possibile procedere in maniera bidirezionale.

Ogni volta che una regola determina che è necessario creare un nuovo oggetto spazio connettore, si tratta di provisioning. Tuttavia, poiché questa operazione avviene solo all'interno dello spazio connettore, essa vengono trasferiti nell'origine dati connessa finché non viene eseguita un'esportazione.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Azure Active Directory connettersi sincronizzazione: Le opzioni di personalizzazione di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
