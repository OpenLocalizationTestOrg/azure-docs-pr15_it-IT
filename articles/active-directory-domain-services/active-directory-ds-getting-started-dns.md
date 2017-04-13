<properties
    pageTitle="Azure servizi di dominio Active Directory: Impostazioni di aggiornamento DNS per la rete virtuale Azure | Microsoft Azure"
    description="Guida introduttiva di servizi di dominio di Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---update-dns-settings-for-the-azure-virtual-network"></a>Servizi di dominio Active Directory Azure - impostazioni di aggiornamento DNS per la rete virtuale Azure

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Passaggio 4: Aggiornare le impostazioni DNS per la rete virtuale Azure
Precedente attività di configurazione, si attiva servizi di dominio Active Directory di Azure per la directory. È necessario assicurarsi che i computer all'interno della rete virtuale possono connettersi e utilizzo di tali servizi. Aggiornare le impostazioni del server DNS per la rete virtuale in modo che puntino a due indirizzi IP in cui è disponibili in rete virtuale Azure servizi di dominio Active Directory.

> [AZURE.NOTE] Prendere nota degli indirizzi IP per i servizi di dominio Active Directory di Azure visualizzati nella scheda **Configura** della directory, dopo aver attivato Servizi di dominio Active Directory di Azure per la directory.

Effettuare le seguenti operazioni di configurazione per aggiornare le impostazioni di server DNS per la rete virtuale in cui è stata attivata servizi di dominio Active Directory Azure.

1. Passare al **portale classica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selezionare il nodo **reti** nel riquadro sinistro.

    ![Nodo reti virtuali](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. Nella scheda **Reti virtuali** , selezionare la rete virtuale in cui è abilitato Servizi di dominio Active Directory di Azure per visualizzarne le proprietà.

4. Fare clic sulla scheda **Configura** .

    ![Nodo reti virtuali](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. Nella sezione **server DNS** immettere gli indirizzi IP dei servizi di dominio Active Directory Azure.

6. Assicurarsi di inserire entrambi gli indirizzi IP che sono stati visualizzati nella sezione dei **Servizi di dominio** nella scheda **Configura** della directory.

7. Per salvare le impostazioni del server DNS per la rete virtuale, fare clic su **Salva** nel riquadro attività nella parte inferiore della pagina.

   ![Aggiornare le impostazioni del server DNS per la rete virtuale.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] Dopo aver aggiornato le impostazioni del server DNS per la rete virtuale, potrebbe richiedere un po' di tempo per macchine virtuali di rete per ottenere la configurazione del DNS aggiornati. Se una macchina virtuale non riesce a connettersi al dominio, è possibile cancellare la cache DNS (ad esempio. 'ipconfig /flushdns') nella macchina virtuale. Questo comando impone l'aggiornamento delle impostazioni DNS sul computer virtuale.


## <a name="task-5---enable-password-synchronization-to-azure-ad-domain-services"></a>Attività 5 - attiva la sincronizzazione delle password in servizi di dominio Active Directory di Azure
L'attività di configurazione successivo è per [attivare la sincronizzazione delle password in servizi di dominio Active Directory Azure](active-directory-ds-getting-started-password-sync.md).
