<properties
    pageTitle="Installare IIS sul primo macchine Virtuali Windows | Microsoft Azure"
    description="Provare con la macchina virtuale di Windows prima installazione di IIS e apertura della porta 80 tramite il portale di Azure."
    keywords=""
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="cynthn"/>

# <a name="experiment-with-installing-a-role-on-your-windows-vm"></a>Sperimentare l'installazione di un ruolo nelle macchine Virtuali di Windows
    
Dopo avere inserito il prima macchine () funzionanti, è possibile spostare ai servizi e installare software. Per questa esercitazione verranno utilizzare Gestione Server in macchine Virtuali di Server Windows per installarlo. Quindi, verrà creato un gruppo di sicurezza rete (NSG) tramite il portale di Azure per aprire la porta 80 per il traffico IIS. 

Se è già stata creata la macchina virtuale prima, è necessario tornare per [creare il prima macchina virtuale di Windows nel portale di Azure](virtual-machines-windows-hero-tutorial.md) prima di procedere con questa esercitazione.

## <a name="make-sure-the-vm-is-running"></a>Assicurarsi che la macchina virtuale è in esecuzione

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Nel menu hub fare clic su **macchine virtuali**. Selezionare la macchina virtuale dall'elenco.
3. Se lo stato è **interrotto (Deallocated)**, fare clic sul pulsante di **avvio** e il **Nozioni di base** della macchina virtuale. Se lo stato è **in esecuzione**, è possibile spostare con il passaggio successivo.

## <a name="connect-to-the-virtual-machine-and-sign-in"></a>Connettere la macchina virtuale e l'accesso

1.  Nel menu hub fare clic su **macchine virtuali**. Selezionare la macchina virtuale dall'elenco.

3. In blade per la macchina virtuale, fare clic su **Connetti**. Verrà creata e scarica file Remote Desktop Protocol (file con estensione rdp) che è simile a un collegamento a connettersi al computer. È consigliabile salvare il file sul desktop per facilitare l'accesso. **Aprire** il file a connettere la macchina virtuale.

    ![Schermata del portale di Azure che mostra come connettere la macchina virtuale](./media/virtual-machines-windows-hero-tutorial/connect.png)

4. Viene visualizzato un avviso che il RDP è stata inviata da un autore sconosciuto. Questo è normale. Nella finestra di Desktop remoto, fare clic su **Connetti** per continuare.

    ![Screenshot che mostra un avviso relativo a un editore sconosciuto](./media/virtual-machines-windows-hero-tutorial/rdp-warn.png)

5. Nella finestra di sicurezza di Windows, digitare il nome utente e la password dell'account locale creato al momento della creazione della macchina virtuale. Il nome utente viene inserito come *vmname*& #92; *nome utente*, quindi fare clic su **OK**.

    ![Schermata di immissione nome macchine Virtuali, nome utente e password](./media/virtual-machines-windows-hero-tutorial/credentials.png)
    
6.  Viene visualizzato un avviso che non è possibile verificare il certificato. Questo è normale. Fare clic su **Sì** per verificare l'identità della macchina virtuale e completare l'accesso.

    ![Schermata che mostra un messaggio sulla verifica dell'identità di macchina virtuale](./media/virtual-machines-windows-hero-tutorial/cert-warning.png)


Se verificano problemi quando si tenta di connettersi, vedere [risolvere i problemi di Desktop remoto connessioni a una basato su Windows Azure macchina virtuale](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="install-iis-on-your-vm"></a>Installare IIS su di una macchina virtuale

Ora che è stato effettuato l'accesso per la macchina virtuale, verrà installato un ruolo del server in modo che è possibile provare altre.

1. Se non è già aperto, aprire **Server Manager** . Fare clic sul menu **Start** e quindi fare clic su **Server Manager**.
2. In **Server Manager**, selezionare **Server locale** dal riquadro di sinistra. 
3. Nel menu selezionare **Gestisci** > **aggiungere ruoli e funzionalità**.
4. In aggiunta di ruoli e guidata funzionalità, nella pagina **Tipo di installazione** scegliere **installazione basato sui ruoli o funzionalità di base**e quindi fare clic su **Avanti**.

    ![Schermata che mostra la scheda Aggiungi ruoli e guidata funzionalità per tipo di installazione](./media/virtual-machines-windows-hero-tutorial/role-wizard.png)

5. Selezionare la macchina virtuale dal pool di server e fare clic su **Avanti**.
6. Nella pagina **Ruoli del Server** , selezionare **Il Server Web (IIS)**.

    ![Schermata che mostra la scheda Aggiungi ruoli e guidata funzionalità per i ruoli di Server](./media/virtual-machines-windows-hero-tutorial/add-iis.png)

7. Nella finestra popup sull'aggiunta di funzionalità necessarie per IIS, assicurarsi che **Include gli strumenti di gestione** sia selezionata e quindi fare clic su **Aggiungi funzionalità**. Quando si chiude la finestra popup, fare clic su **Avanti** nella procedura guidata.

    ![Schermata che Mostra popup per confermare l'aggiunta del ruolo IIS](./media/virtual-machines-windows-hero-tutorial/confirm-add-feature.png)

8. Nella pagina caratteristiche fare clic su **Avanti**.
9. Nella pagina **Ruolo Server Web (IIS)** fare clic su **Avanti**. 
10. Nella pagina **Servizi ruolo** fare clic su **Avanti**. 
11. Nella pagina di **Conferma** fare clic su **Installa**. 
12. Una volta completato l'installazione, fare clic su **Chiudi** nella procedura guidata.



## <a name="open-port-80"></a>Aprire la porta 80 

Affinché la macchina virtuale accettare il traffico in ingresso sulla porta 80, è necessario aggiungere una regola in entrata al gruppo di sicurezza di rete. 

1. Aprire il [portale di Azure](https://portal.azure.com).
2. In **macchine virtuali di** selezionare la macchina virtuale creato.
3. Le impostazioni di macchine virtuali, selezionare **le interfacce di rete** e quindi selezionare l'interfaccia di rete esistente.

    ![Schermata che mostra l'impostazione di macchina virtuale per le interfacce di rete](./media/virtual-machines-windows-hero-tutorial/network-interface.png)

4. In **Nozioni di base** per l'interfaccia di rete, fare clic sul **gruppo di sicurezza di rete**.

    ![Schermata che mostra la sezione nozioni di base per l'interfaccia di rete](./media/virtual-machines-windows-hero-tutorial/select-nsg.png)

5. In blade **Nozioni di base** per la NSG, è necessario disporre di una regola in entrata predefinito esistente per **rdp consentire predefinito** che consente agli utenti di accedere a macchina virtuale. Aggiungere un'altra regola in entrata per consentire il traffico IIS. Fare clic su **regola di protezione in ingresso**.

    ![Schermata che mostra la sezione nozioni di base per la NSG](./media/virtual-machines-windows-hero-tutorial/inbound.png)

6. In **in ingresso di regole di protezione**, fare clic su **Aggiungi**.

    ![Schermata che mostra il pulsante per aggiungere una regola di sicurezza](./media/virtual-machines-windows-hero-tutorial/add-rule.png)

7. In **in ingresso di regole di protezione**, fare clic su **Aggiungi**. Digitare l'intervallo di porte **80** e assicurarsi che sia selezionato **Consenti** . Al termine, fare clic su **OK**.

    ![Schermata che mostra il pulsante per aggiungere una regola di sicurezza](./media/virtual-machines-windows-hero-tutorial/port-80.png)
 
Per ulteriori informazioni su NSGs, regole in entrata e in uscita, vedere [Consenti accesso esterno di una macchina virtuale tramite il portale di Azure](virtual-machines-windows-nsg-quickstart-portal.md)
 
## <a name="connect-to-the-default-iis-website"></a>Connettersi al sito Web IIS predefinita

1. Nel portale di Azure, fare clic su **macchine virtuali** e quindi selezionare la macchina virtuale.
2. In e il **Essentials** , copiare l' **indirizzo IP pubblico**.

    ![Schermata che mostra dove trovare l'indirizzo IP pubblico della macchina virtuale](./media/virtual-machines-windows-hero-tutorial/ipaddress.png)

2. Aprire un browser e nella barra degli indirizzi digitare l'indirizzo IP pubblico …: http://<publicIPaddress> e premere **INVIO** per passare a tale indirizzo.
3. Il browser deve aprire la pagina web IIS predefinita. Il comando è simile al seguente:

    ![Schermata che mostra l'aspetto della pagina IIS predefinita in un browser](./media/virtual-machines-windows-hero-tutorial/iis-default.png)

    

## <a name="next-steps"></a>Passaggi successivi

- È inoltre possibile utilizzare [come allegare un disco dati](virtual-machines-windows-attach-disk-portal.md) alla macchina virtuale. Dati dischi offrono ulteriore spazio di archiviazione per la macchina virtuale.
