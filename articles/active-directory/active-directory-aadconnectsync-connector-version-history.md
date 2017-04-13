<properties
   pageTitle="Cronologia delle versioni connettore versione | Microsoft Azure"
   description="In questo argomento vengono elencati tutte le versioni dei connettori per Forefront Identity Manager (FIM) e Microsoft identità Manager (MIM)"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="billmath"/>

# <a name="connector-version-release-history"></a>Cronologia delle versioni versione connettore
I connettori per Forefront Identity Manager (FIM) e Microsoft identità Manager (MIM) vengono aggiornati di frequente.

>[AZURE.NOTE]
In questo argomento è disponibile solo in FIM e MIM. Questi connettori non sono supportati in Azure AD Connect.

In questo argomento elenca tutte le versioni dei connettori che sono stati pubblicati.

Collegamenti correlati:

- [Scaricare i connettori più recente](http://go.microsoft.com/fwlink/?LinkId=717495)
- Documentazione di riferimento [Connettore LDAP generico](active-directory-aadconnectsync-connector-genericldap.md)
- Documentazione di riferimento [Connettore SQL generico](active-directory-aadconnectsync-connector-genericsql.md)
- Documentazione di riferimento [Connector di servizi Web](http://go.microsoft.com/fwlink/?LinkID=226245)
- Documentazione di riferimento [Connettore PowerShell](active-directory-aadconnectsync-connector-powershell.md)
- Documentazione di riferimento [Lotus Domino connettore](active-directory-aadconnectsync-connector-domino.md)

## <a name="111170"></a>1.1.117.0
Rilascio: 2016 marzo

**Nuovo connettore**  
Versione iniziale del [Connettore SQL generico](active-directory-aadconnectsync-connector-genericsql.md).

**Nuove caratteristiche:**

- Connettore LDAP generico:
    - Aggiunta del supporto per l'importazione delta con Isode.
- Connettore di servizi Web:
    - Aggiornare la csEntryChangeResult e setImportErrorCode attività per consentire agli errori di livello oggetto da restituire al motore di sincronizzazione.
    - Aggiornare i modelli di SAP6 e SAP6User per utilizzare la nuova funzionalità di errore di livello oggetto.
- Connettore di Lotus Domino:
    - Per l'esportazione, è necessario uno certificatore per ogni rubrica. È ora possibile usare la stessa password per rilascio degli attestati tutti per semplificare la gestione.

**Problemi risolti:**

- Connettore LDAP generico:
    - Per IBM Tivoli DS, alcuni attributi di riferimento non sono state rilevate correttamente.
    - Per aprire LDAP durante un'importazione delta, gli spazi vuoti all'inizio e alla fine di stringhe troncati.
    - Per Novell e NetIQ, un'esportazione che un oggetto spostato tra le unità organizzative/contenitori e allo stesso tempo rinominata l'oggetto non è riuscita.
- Connettore di servizi Web:
    - Se il servizio web ha più parametri per l'associazione stesso, quindi il connettore non ha correttamente rilevato questi punti finali.
- Connettore di Lotus Domino:
    - Esportazione dell'attributo fullName a un database di posta elettronica non funziona.
    - Un'esportazione che aggiunta e rimozione membro da un gruppo esportata solo i membri aggiunti.
    - Se un documento di Notes non è valido (l'attributo isValid impostata su false) e quindi si verifica un errore connettore.

## <a name="older-releases"></a>Versioni precedenti
Prima di marzo 2016, sono disponibili i connettori come argomenti di supporto.

**LDAP generico**

- [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, settembre 2015
- [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, marzo 2015
- [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, gennaio 2015
- [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, settembre 2014
- [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, marzo 2014

**Servizi Web**

- [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, settembre 2014

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, settembre 2014

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, settembre 2015
- [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, marzo 2015
- [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, agosto 2014
- [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, febbraio 2014  
- [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, ottobre 2013
- [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, agosto 2013

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
