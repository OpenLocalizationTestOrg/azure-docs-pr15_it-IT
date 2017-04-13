<properties
    pageTitle="Azure B2C Directory attiva: Produzione scala e anteprima B2C tenant | Microsoft Azure"
    description="Un argomento sui tipi di Azure Active Directory B2C tenant"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure B2C Directory attiva: Produzione scala e anteprima B2C tenant

Se si prevede di scrivere un'app di produzione B2C Azure Active Directory (Azure Active Directory), è necessario essere certi che si tenant destro "tipo" per passare dinamica in. Per vedere cosa si dispone, seguire questa procedura per [passare a e il caratteristiche B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure e cercare nella casella **tipo Tenant**.

## <a name="summary"></a>Riepilogo

Azure Active Directory B2C supporta le applicazioni di produzione solo in **scala di produzione** B2C tenant in Nord America.

| Tipo di tenant | Paesi/aree geografiche | In genere disponibili? |
| ----------- | -------------- | --------------------- |
| **Tenant scala di produzione** | Nord America paesi/aree geografiche | Sì |
| **Tenant scala di produzione** | Tutti i paesi ad eccezione del Nord America | No |
| **Tenant di anteprima** | Tutti i paesi/aree geografiche | No |

> [AZURE.NOTE]
Azure Active Directory B2C tenant (per gli utenti) sono attualmente disponibili in alcuni paesi in cui sono disponibili i tenant di Azure Active Directory (per i dipendenti). Leggere le sezioni seguenti per informazioni dettagliate.

## <a name="production-scale-b2c-tenant-in-north-america"></a>Tenant B2C produzione scala in Nord America

Se è stato [creato il tenant B2C](active-directory-b2c-get-started.md) in Nord America, ad esempio, in uno dei seguenti paesi o aree geografiche: United Uniti, Canada, Costa Rica, Repubblica Domenicana, El Salvador, Guatemala, Messico, Panama, Portorico e Trinidad e Tobago e il **tipo di Tenant** l'interfaccia utente amministratore B2C indica che **la scala di produzione**, il tenant può essere utilizzato per le applicazioni di produzione.

> [AZURE.NOTE]
Tenant di produzione scala sono in grado di ridimensionamento per 100s milioni di identità consumer per ogni tenant.

![Schermata di un tenant di produzione scala](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>Visualizzare in anteprima B2C tenant descritta in qualsiasi paese/area geografica

Se è stato creato un tenant B2C durante il periodo di anteprima del B2C di Azure Active Directory, è probabile che i **Tenant digitare** indica che **Anteprima tenant**. In questo caso, è necessario utilizzare il tenant solo per sviluppo e testing e non per le applicazioni di produzione.

> [AZURE.IMPORTANT]
Non è un percorso di migrazione dal tenant B2C un'anteprima in un tenant B2C scala di produzione. Si noti che esistono noti problemi quando si elimina un tenant di anteprima B2C e ricrea un tenant B2C scala di produzione con lo stesso nome di dominio. È necessario creare un tenant B2C scala di produzione con un nome di dominio diverso.

![Schermata di un tenant di anteprima](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>Tenant B2C produzione scala fuori del Nord America

Azure Active Directory B2C attualmente non è generalmente disponibile fuori del Nord America. Scopi, tuttavia è possibile creare e usare tenant di produzione scala, per sviluppo e la verifica, in uno dei seguenti paesi o aree geografiche: Algeria, Austria, Azerbaigian, Bahrein, Belarus, Belgio, Bulgaria, Croazia, Cipro, Repubblica ceca, Danimarca, Egitto, Estonia, Finlandia, Francia, Germania, Grecia, Ungheria, Islanda, Irlanda, Israele, Italia, Giordania, Kazakhstan, Kenya, Kuwait, Lativa, Libano, Liechtenstein, Lussemburgo, Lituania, ex Repubblica Jugoslava di Macedonia, Malta, Montenegro, Marocco, Paesi Bassi, Nigeria, Norvegia , Oman, Pakistan, Polonia, Portogallo, Qatar, Romania, Russia, Arabia Saudita, Serbia, Slovacchia, Slovenia, Sudafrica, Spagna, Svezia, Svizzera, Tunisia, Turchia, Ucraina, uniti Emirati Arabi Uniti e Regno Unito.

Una volta Azure Active Directory B2C annuncia disponibilità generale in sopra paesi o aree geografiche, è possibile continuare a usare questi tenant scala di produzione e pubblicazione con le applicazioni di produzione senza alcuna perdita di dati.

## <a name="availability-of-b2c-tenants"></a>Disponibilità di B2C tenant

Tenant B2C non sono attualmente disponibili in seguenti paesi: Afghanistan, Argentina, Australia, Brasile, Cile, Colombia, Ecuador, Hong Kong-R.A.S, India, Indonesia, Iraq, Giappone, Corea, Malaysia, Nuova Zelanda, Paraguay, Perù, Filippine, Singapore, Sri Lanka, Taiwan, Thailand (ไทย), Uruguay e Venezuela. Si prevede di includerli in futuro.
