<properties
    pageTitle="Azure AD Connect: Istanze del servizio di sincronizzazione | Microsoft Azure"
    description="Questa pagina documenti considerazioni speciali per le istanze di Azure Active Directory."
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
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Considerazioni speciali per le istanze
Azure AD Connect più comunemente usati con l'istanza di tutto il mondo di Azure Active Directory e in Office 365. Ma sono disponibili anche altre istanze e questi requisiti differenti per gli URL e altre considerazioni speciali.

## <a name="microsoft-cloud-germany"></a>Germania Microsoft Cloud
[Microsoft Cloud Germania](http://www.microsoft.de/cloud-deutschland) è un cloud sovrani gestito da un dominio trusted dati tedesco.

Questo cloud è attualmente in anteprima. Molti degli scenari che è in genere possibile eseguire per se stessi, ad esempio verificare domini, devono essere eseguiti dall'operatore. Contattare il rappresentante Microsoft per ulteriori informazioni su come partecipare nel riquadro di anteprima.

URL da aprire in un server proxy |
--- |
\*. microsoftonline.de |
\*. windows.net |
+ Elenchi di revoche di certificati |

Quando accede a directory di Azure Active Directory è necessario utilizzare un account del dominio onmicrosoft.de.

Funzionalità non è attualmente presente in Germania Microsoft Cloud:

- Integrità di connessione di Azure Active Directory non è disponibile.
- Gli aggiornamenti automatici non è disponibile.
- Writeback di password non è disponibile.

## <a name="microsoft-azure-government-cloud"></a>Cloud di Microsoft Azure Government
[Cloud di Microsoft Azure Government](https://azure.microsoft.com/features/gov/) è un cloud per governo degli Stati Uniti.

Questo cloud è stato supportato da versioni precedenti di DirSync. Da build 1.1.180 di Azure AD Connect successiva generazione del cloud è supportata. Questa generazione utilizzando i punti finali in base solo Stati Uniti e disporre di un elenco di URL per aprire il server proxy.

URL da aprire in un server proxy |
--- |
\*. microsoftonline.com |
\*. gov.us.microsoftonline.com |
+ Elenchi di revoche di certificati |

Non saranno in grado di rilevare automaticamente che la directory di Azure Active Directory si trova nel cloud per enti pubblici Azure AD Connect. È necessario eseguire le operazioni seguenti durante l'installazione di Azure AD Connect.

1. Avviare l'installazione di Azure AD Connect.
2. Come viene visualizzata la prima pagina in cui si deve accettare il contratto di licenza, non continuare e lascia l'installazione guidata in esecuzione.
3. Avviare regedit e modificare la chiave del Registro di sistema `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` al valore `2`.
4. Passare all'installazione guidata di Azure AD Connect accettare il contratto di licenza e continuare. Durante l'installazione, assicurarsi di usare il percorso di installazione di **configurazione personalizzata** (e non installazione rapida). Quindi continuare l'installazione come di consueto.

Funzionalità non è attualmente presente nel cloud Microsoft Azure Government:

- Integrità di connessione di Azure Active Directory non è disponibile.
- Gli aggiornamenti automatici non è disponibile.
- Writeback di password non è disponibile.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
