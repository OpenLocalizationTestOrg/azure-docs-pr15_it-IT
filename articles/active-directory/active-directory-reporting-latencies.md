<properties
   pageTitle="Azure Active Directory Reporting latenza | Microsoft Azure"
   description="Quantità di tempo impiegato per i report eventi compaiano di Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-report-latencies"></a>Latenza di Report di Azure Active Directory

*Questa documentazione fa parte di [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Report                                                  | Valore minimo  | Media    | Massimo
------------------------------------------------------- | -------- | ---------- | ----------
**Report sulla protezione**                                    |          |            |
Attività di accesso irregolare                              | 2 ore  | 4 ore    | 8 ore
Componenti aggiuntivi di accesso da origini sconosciute                           | 2 ore  | 4 ore    | 8 ore
Accessi dopo più errori                        | 2 ore  | 4 ore    | 8 ore
Componenti aggiuntivi di accesso da più aree geografiche                      | 2 ore  | 4 ore    | 8 ore
Componenti aggiuntivi di accesso da indirizzi IP con attività sospetti     | 2 ore  | 4 ore    | 8 ore
Componenti aggiuntivi di accesso da dispositivi eventualmente virus                 | 2 ore  | 4 ore    | 8 ore
Utenti con attività di accesso anomala                   | 2 ore  | 4 ore    | 8 ore
Utenti con credenziali persa                           | 2 ore  | 4 ore    | 8 ore
**Report delle applicazioni**                                 |          |            |
Account il provisioning di attività                           | 2 ore  | 4 ore    | 8 ore
Account errori di provisioning                             | 2 ore  | 4 ore    | 8 ore
Utilizzo dell'applicazione                                       | 2 ore  | 4 ore    | 8 ore
Stato di attivazione della password                                | 2 ore  | 4 ore    | 8 ore
**Controllo e i report di attività**                            |          |            |
Report di controllo                                            | minuto | 15 minuti | 30 minuti
Attività (Azure Active Directory) la reimpostazione della password                      | 2 ore  | 4 ore    | 8 ore
Attività (identità Manager) la reimpostazione della password              | 2 ore  | 4 ore    | 8 ore
Attività di registrazione (Azure Active Directory) la reimpostazione della password         | 2 ore  | 4 ore    | 8 ore
Attività di registrazione (identità Manager) la reimpostazione della password | 2 ore  | 4 ore    | 8 ore
Self service attività gruppi (Azure Active Directory)                 | 2 ore  | 4 ore    | 8 ore
Self service attività gruppi (identità Manager)         | 2 ore  | 4 ore    | 8 ore
**Report di RMS**                                         |          |            |
Utenti di RMS più attivi                                   | 2 ore  | 4 ore    | 8 ore
Uso di RMS                                               | 2 ore  | 4 ore    | 8 ore
Utilizzo di RMS periferica                                        | 2 ore  | 4 ore    | 8 ore
Utilizzo dell'applicazione di RMS abilitata                           | 2 ore  | 4 ore    | 8 ore
**Report in anteprima privato**                             |          |            |
Tutte le attività di accesso degli utenti                               | 2 ore  | 4 ore    | 8 ore
