<properties
    pageTitle="Sincronizzazione di Azure AD Connect: informazioni sulle espressioni di Provisioning dichiarativo | Microsoft Azure"
    description="Spiega le espressioni di provisioning dichiarative."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Sincronizzazione di Azure AD Connect: informazioni sulle espressioni di Provisioning dichiarativo
Sincronizzazione di Azure AD Connect si basa su dichiarativo provisioning introdotto in Forefront Identity Manager 2010. È possibile implementare la logica aziendale integrazione identità completa senza la necessità di scrivere codice compilato.

Un elemento essenziale di provisioning dichiarativo è il linguaggio delle espressioni usato nei flussi di attributo. La lingua usata è un sottoinsieme di Microsoft® Visual Basic, Applications Edition (VBA). Viene utilizzata in Microsoft Office e gli utenti con esperienza di VBScript anche la riconosca. Il linguaggio delle espressioni di Provisioning dichiarativo solo utilizza le funzioni e non è una lingua strutturata. Non esistono metodi o le istruzioni. Funzioni se, tuttavia annidate su flusso di programma express.

Per ulteriori informazioni, vedere [Introduzione a Visual Basic per riferimento lingua applicazioni per Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Gli attributi sono tipizzati. Una funzione accetta solo gli attributi di tipo corretto. È inoltre maiuscole e minuscole. Nomi di funzioni e i nomi degli attributi deve iniz maiuscole e minuscole o viene generato un errore.

## <a name="language-definitions-and-identifiers"></a>Definizioni di lingua e identificatori

- Funzioni dispone di un nome seguito dagli argomenti tra parentesi quadre: nomefunzione (argomento1, argomento N).
- Quali sono identificati da racchiusa tra parentesi quadre: [attributeName]
- Parametri vengono identificati da segni di percentuale: ParameterName %
- Le costanti di stringa sono racchiusi tra virgolette: ad esempio "Contoso" (Nota: è necessario utilizzare le virgolette semplici "" e non inglesi "")
- Valori numerici vengono espresso senza virgolette e deve essere decimale. Valori esadecimali sono preceduti & H. Ad esempio 98052 & HFF
- Valori booleani sono espressi con le costanti: True, False.
- Incorporati costanti e valori letterali sono espressi con solo il nome della persona: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Funzioni
Provisioning dichiarativo utilizza molte funzioni per consentire la possibilità di trasformare i valori degli attributi. Queste funzioni possono essere nidificate in modo che il risultato di una funzione passato a un'altra funzione.

`Function1(Function2(Function3()))`

L'elenco completo delle funzioni sono disponibili nel [riferimento alle funzioni](active-directory-aadconnectsync-functions-reference.md).

### <a name="parameters"></a>Parametri
Un parametro viene definito da un connettore o da un amministratore tramite PowerShell. In genere contengono valori che sono diversi da un sistema, ad esempio il nome del dominio l'utente si trova. Questi parametri possono essere utilizzati per flussi di attributo.

Per le regole in entrata di sincronizzazione di Active Directory Connector disponibili i parametri seguenti:

| Nome parametro | Commento |
| --- | --- |
| Domain.Netbios | Formato NetBIOS del dominio attualmente importato, ad esempio FABRIKAMSALES |
| Domain.FQDN | Formato di nome di dominio completo del dominio attualmente importato, ad esempio sales.fabrikam.com |
| Domain.LDAP | Formato LDAP del dominio attualmente importato, ad esempio controller di dominio = vendite, Cc = fabrikam, Cc = com |
| Forest.Netbios | Formato NetBIOS del nome foresta attualmente importato, ad esempio FABRIKAMCORP |
| Forest.FQDN | Formato nome di dominio completo del nome foresta attualmente importato, ad esempio fabrikam.com |
| Forest.LDAP | Formato LDAP del nome foresta attualmente importato, ad esempio controller di dominio = fabrikam, Cc = com |

Il sistema fornisce il parametro seguente, che viene utilizzato per ottenere l'identificatore del connettore attualmente in esecuzione:  
`Connector.ID`

Ecco un esempio che consente di compilare il dominio di attributo metaverse con il nome netbios del dominio in cui si trova l'utente:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operatori
È possono utilizzare gli operatori seguenti:

- **Confronto**: <, < = <>, =, > > =
- **Matematica**: +, -, \*, -
- **Stringa**: & (concatenazione)
- **Logiche**: & & (e) | | (o)
- **Ordine di valutazione**:)

Gli operatori vengono valutati da sinistra a destra e hanno la stessa priorità di valutazione. Vale a dire il \* (moltiplicatore) non viene valutata prima - (sottrazione). 2\*(5 + 3) non è diverso da quello di 2\*5 + 3. Tra parentesi () vengono utilizzati per modificare l'ordine di valutazione quando a sinistra per ordine di valutazione destra non è appropriato.

## <a name="multi-valued-attributes"></a>Attributi multivalore
Le funzioni è possono utilizzare gli attributi sia a valore singolo e multivalore. Per gli attributi multivalore, la funzione opera su ogni valore e si applica la stessa funzione a ogni valore.

Per esempio:  
`Trim([proxyAddresses])`Eseguire un ritaglio di ogni valore dell'attributo proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Per ogni valore con un @-sign, sostituire il dominio con @contoso.com.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Cercare l'indirizzo SIP e rimuovere tutti i valori.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul modello di configurazione per [Il Provisioning dichiarativo comprensione](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Vedere come dichiarativo il provisioning è utilizzato fuori della casella comprendere [la configurazione predefinita](active-directory-aadconnectsync-understanding-default-configuration.md).
- Informazioni su come apportare modifiche pratica utilizzando il provisioning dichiarativo in [come apportare modifiche alla configurazione predefinita](active-directory-aadconnectsync-change-the-configuration.md).

**Argomenti della panoramica**

- [Sincronizzazione di Azure AD Connect: comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)

**Argomenti della Guida di riferimento**

- [Sincronizzazione di Azure AD Connect: funzioni di riferimento](active-directory-aadconnectsync-functions-reference.md)
