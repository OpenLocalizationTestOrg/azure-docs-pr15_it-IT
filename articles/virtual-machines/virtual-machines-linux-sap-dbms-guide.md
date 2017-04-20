<properties
   pageTitle="SAP NetWeaver su Linux macchine (): Guida alla distribuzione di DBMS | Microsoft Azure"
   description="SAP NetWeaver su Linux macchine (): Guida alla distribuzione di DBMS"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="sedusch"/>

# <a name="sap-netweaver-on-azure-virtual-machines-vms--dbms-deployment-guide"></a>SAP NetWeaver su Azure macchine (): Guida alla distribuzione di DBMS

[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-linux-sap-dbms-guide.md (SAP NetWeaver su Linux macchine (): Guida alla distribuzione di DBMS) [dbms-guide-2.1]:virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (memorizzazione nella cache per macchine virtuali e dischi rigidi virtuali) [dbms-guide-2.2]:virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (RAID Software) [dbms-guide-2.3]:virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (spazio di archiviazione di Microsoft Azure) [dbms-guide-2]:virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (struttura di una distribuzione RDBMS) [dbms-guide-3]:virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (disponibilità e il ripristino di emergenza con macchine virtuali di Azure) [dbms-guide-5.5.1]:virtual-machines-linux-sap-dbms-guide.md# 0fef0e79-d3fe-4ae2-85AF-73666a6f7268 (SQL Server 2012 SP1 CU4 e versioni successive) [dbms-guide-5.5.2]:virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e versioni precedenti) [dbms-guide-5.6]:virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (con immagini di SQL Server disconnettersi da Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (generale di SQL Server per SAP su Azure riepilogo) [dbms-guide-5]:virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (specifiche per SQL Server RDBMS) [dbms-guide-8.4.1]:virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (configurazione dello spazio di archiviazione) [dbms-guide-8.4.2]:virtual-machines-linux-sap-dbms-guide.md# 23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup e ripristino) [dbms-guide-8.4.3]:virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (considerazioni sulle prestazioni di Backup e ripristino) [dbms-guide-8.4.4]:virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (altro) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-linux-sap-deployment-guide.md (SAP NetWeaver su Linux macchine (): Guida alla distribuzione) [deployment-guide-2.2]:virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP risorse) [deployment-guide-3.1.2]:virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (distribuzione una macchina virtuale con un'immagine personalizzata) [deployment-guide-3.2]:virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: distribuire una macchina virtuale fuori Azure Marketplace per SAP) [deployment-guide-3.3]:virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: distribuire una macchina virtuale con un'immagine personalizzata per SAP) [deployment-guide-3.4]:virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 ( Scenario 3: Spostare una macchina virtuale locali con un disco rigido virtuale Azure non generalized SAP) [deployment-guide-3]:virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (distribuzione scenari di macchine virtuali per SAP in Microsoft Azure) [deployment-guide-4.1]:virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (cmdlet per la distribuzione di Azure PowerShell) [deployment-guide-4.2]:virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Download e l'importazione SAP pertinenti cmdlet di PowerShell) [deployment-guide-4.3]:virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (partecipare macchine Virtuali nel dominio locale - solo Windows) [deployment-guide-4.4.2]:virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [4.4 di Guida alla distribuzione]: Virtual-Machines-Linux-SAP-Deployment-Guide.MD#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Download, installazione e attivazione dell'agente macchine Virtuali di Azure) [deployment-guide-4.5.1]:virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (configurare Azure Enhanced monitoraggio estensione per SAP) [deployment-guide-5.1]:virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (per la preparazione per cercare Azure Enhanced monitoraggio per SAP) [deployment-guide-5.2]:virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (verifica dell'integrità per Azure Configurazione dell'infrastruttura di monitoraggio) [deployment-guide-5.3]:virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (la risoluzione del problema dell'infrastruttura di monitoraggio di Azure per SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-linux-sap-get-started.md
[getting-started-dbms]:virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-linux-sap-planning-guide.md (SAP NetWeaver su Linux macchine (): Guida alla pianificazione e implementazione) [planning-guide-1.2]:virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (risorse) [planning-guide-11]:virtual-machines-linux-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (alta disponibilità (HA) e il ripristino di emergenza (DR) per SAP NetWeaver in esecuzione in macchine virtuali di Azure) [planning-guide-11.4.1]:virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (disponibilità per i server di applicazioni SAP) [planning-guide-11.5]:virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (utilizzo per l'avvio automatico per le istanze SAP) [planning-guide-2.1]:virtual-machines-linux-sap-planning-guide.md# 1625df66-4CC6-4d60-9202-de8a0b77f803 (basata solo su Cloud - le distribuzioni in Azure senza dipendenze nella rete dei clienti in locale) [planning-guide-2.2]:virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-locale - distribuzione di singola o più macchine virtuali di SAP in Azure con il requisito di vengano integrati in rete locale) [planning-guide-3.1]:virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure aree geografiche) [planning-guide-3.2.1]:virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 [planning-guide-3.2.2]:virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (aggiornare domini) [planning-guide-3.2.3]:virtual-machines-linux-sap-planning-guide.md#18810088-(guasto domini) F9BE - 4c 97-958a - 27996255c 665 (Azure disponibilità set) [planning-guide-3.2]:virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (di Microsoft Azure macchina virtuale concetto) [planning-guide-3.3.2]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (spazio di archiviazione di Azure Premium) [planning-guide-5.1.1]:virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (da una macchina virtuale locale Azure con un disco non generalized) [planning-guide-5.1.2]:virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (distribuzione una macchina virtuale con un'immagine specifica del cliente) [planning-guide-5.2.1]:virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (preparazione per spostare una macchina virtuale locali in Azure con un disco non generalized) [ Planning-Guide-5.2.2]:Virtual-Machines-Linux-SAP-Planning-Guide.MD#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (preparazione per la distribuzione di una macchina virtuale con un'immagine specifica del cliente per SAP) [planning-guide-5.2]:virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (preparazione macchine virtuali con SAP per Azure) [planning-guide-5.3.1]:virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (differenza tra un disco Azure e Azure immagine) [planning-guide-5.3.2]:virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (caricamento di un disco rigido virtuale locali in Azure) [planning-guide-5.4.2]:virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (dischi quando si copiano tra gli account di archiviazione di Azure) [planning-guide-5.5.1]:virtual-machines-linux-sap-planning-guide.md# 4efec401-91e0-40c0-8e64-f2dceadff646 (struttura macchine Virtuali/disco rigido virtuale per le distribuzioni SAP) [planning-guide-5.5.3]:virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (impostazione montaggio per dischi collegati) [planning-guide-7.1]:virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (singola macchina virtuale con SAP NetWeaver demo/formazione scenario) [planning-guide-7]:virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (distribuzione concetti di Cloud-Only delle istanze SAP) [planning-guide-9.1]:virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure monitoraggio soluzione per SAP) [planning-guide-azure-premium-storage]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (spazio di archiviazione di Azure Premium)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]:virtual-machines-linux-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica.

Questa guida fa parte della documentazione all'implementazione e distribuzione del software SAP in Microsoft Azure. Prima di leggere questa Guida, leggere [pianificazione in corso e una Guida all'implementazione] [-Guida alla pianificazione]. In questo documento è descritta la distribuzione di gestione dei sistemi di Database relazionali (RDBMS) e prodotti correlati in combinazione con SAP in Microsoft Azure macchine () utilizzando l'infrastruttura di Azure come una funzionalità di servizio (IaaS).

Carta integra la documentazione di installazione di SAP e note di SAP che rappresentano le risorse principali per le installazioni e distribuzioni del software SAP in dato piattaforme

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="general-considerations"></a>Considerazioni generali
In questo capitolo considerazioni dell'esecuzione SAP correlate vengono introdotti i sistemi DBMS in macchine virtuali di Azure. Esistono alcuni riferimenti a sistemi DBMS specifici in questo capitolo. Se, tuttavia i sistemi DBMS specifici vengono gestiti all'interno di questo documento dopo questo capitolo.

### <a name="definitions-upfront"></a>Spese definizioni
In tutto il documento verrà usata le condizioni seguenti:

* IaaS: Infrastruttura come servizio.
* PaaS: Piattaforma come servizio.
* SaaS: Software come servizio.
* Componente SAP: una singola SAP applicazione, ad esempio ECC, bianco e nero, gestione soluzione o EP.  Componenti SAP possono essere basati sulle tecnologie ABAP o Java tradizionali o da un'applicazione non NetWeaver in base ad esempio oggetti Business.
* Ambiente SAP: uno o più componenti SAP raggruppati per eseguire una funzione aziendale, ad esempio sviluppo, QAS, formazione, DR o produzione.
* SAP orizzontale: Questa opzione fa riferimento alle risorse SAP intere di un cliente orizzontale IT. Orizzontale SAP include tutti produzione e ambienti non di produzione.
* Sistema SAP: Combinazione del livello DBMS e applicazione di ad esempio un sistema ERP SAP sviluppo, il sistema di test SAP bianco e nero, sistema di produzione CRM SAP e così via. Nelle distribuzioni Azure questa caratteristica non è supportata per dividere questi due livelli tra locale e Azure. Questa indica che un sistema SAP è distribuito in locale o viene distribuito in Azure. Tuttavia, è possibile distribuire i sistemi diversi di un orizzontale SAP in Azure o locale. Ad esempio, si potrebbe distribuire lo sviluppo di CRM SAP e testare sistemi in Azure, ma i CRM SAP produzione sistema locale.
* Distribuzione basata solo su cloud: una distribuzione in cui la sottoscrizione Azure non è connesso tramite una connessione di ExpressRoute per l'infrastruttura di rete locale o al sito. In comune documentazione Azure questi tipi di distribuzioni sono inoltre descritti come distribuzioni 'Basata solo su Cloud'. Macchine virtuali distribuite con questo metodo sono accessibili tramite Internet e pubblico endpoint Internet assegnate alle macchine virtuali di Azure. Active Directory (AD) in locale e DNS non è esteso in Azure in questi tipi di distribuzioni. Pertanto macchine virtuali non fanno parte di Active Directory locale. Nota: Distribuzioni basata solo su Cloud in questo documento sono definite come completate paesaggi SAP che sono in esecuzione esclusivamente in Azure senza estensione di Active Directory o la risoluzione dei nomi locali al cloud pubblica. Configurazioni basata solo su cloud non sono supportate per sistemi SAP di produzione o configurazioni nel punto in cui stm SAP o altre risorse locale devono essere usato tra i sistemi SAP ospitati in Azure e risorse che si trovano in locale.
* Croce locale: Descrive uno scenario in cui sono distribuite macchine virtuali a un abbonamento Azure contenente da sito, più siti o ExpressRoute connessione tra il datacenter(s) locale e Azure. Documentazione in comune Azure, questi tipi di distribuzioni sono inoltre descritti come scenari tra locale. Il motivo per la connessione è estendere domini locale, Active Directory locale e locale DNS in Azure. Orizzontale locale viene esteso alle risorse di Azure della sottoscrizione. Con questa estensione, macchine virtuali possono far parte del dominio locale. Gli utenti di dominio del dominio locale possono accedere ai server ed eseguire servizi su tali macchine virtuali (ad esempio DBMS services). Risoluzione dei nomi e le comunicazioni tra macchine virtuali distribuito in locale e macchine virtuali distribuite in Azure possibili. Dovrebbe essere lo scenario più comune per la distribuzione di attività SAP in Azure. Vedere [questo] [ vpn-gateway-cross-premises-options] articolo e [questo] [ vpn-gateway-site-to-site-create] per ulteriori informazioni.

> [AZURE.NOTE] Distribuzioni locali di croce di sistemi SAP in macchine virtuali di Azure sistemi SAP sono membri di un dominio locale sono supportate per sistemi di produzione SAP. Configurazioni tra locale sono supportate per la distribuzione di parti o completare paesaggi SAP in Azure. Anche il completamento orizzontale SAP in esecuzione in Azure richiede con tali macchine virtuali da parte del dominio locale e gli annunci. Nelle versioni precedenti della documentazione sono stati illustrati gli scenari IT ibrido, in cui è possibile eseguire il termine "Ibrido" nel fatto che non esiste una connessione tra locale tra locale e Azure. In questo caso "Ibrido" significa anche che macchine virtuali di Azure fanno parte di Active Directory locale.

Parte della documentazione di Microsoft vengono descritti scenari tra locale leggermente diverso, in particolare per le configurazioni DBMS HA. Per quanto riguarda il SAP di documenti, il Cross locale scenario soltanto boils per difetto assenza di una connettività (ExpressRoute) da sito o private e al fatto che orizzontale SAP viene distribuito tra locale e Azure correlati.

### <a name="resources"></a>Risorse
Sono disponibili per l'argomento della distribuzioni SAP in Azure guide seguenti:

* [SAP NetWeaver su Azure macchine ()-implementazione Guida alla pianificazione e] [-Guida alla pianificazione]
* [SAP NetWeaver su Azure macchine (): Guida alla distribuzione] [Guida alla distribuzione]
* [SAP NetWeaver su Azure macchine (): Guida alla distribuzione DBMS (questo documento)] [dbms Guida]

Le note SAP seguenti riguardano l'argomento della SAP su Azure:

| Numero di nota   | Titolo
|------------|--------
| [1928533] | Le applicazioni SAP in Azure: i tipi di prodotti supportati e macchine Virtuali di Azure
| [2015553] | SAP in Microsoft Azure: prerequisiti di supporto
| [1999351] | Risoluzione dei problemi avanzata Azure monitoraggio SAP
| [2178632] | Chiave monitoraggio metriche per SAP in Microsoft Azure
| [1409604] | Virtualizzazione su Windows: Enhanced monitoraggio
| [2191498] | SAP su Linux con Azure: Enhanced monitoraggio
| [2039619] | Le applicazioni SAP in Microsoft Azure utilizzando il Database Oracle: prodotti supportati e le versioni
| [2233094] | DB6: Le applicazioni SAP su Azure con IBM DB2 per Linux, UNIX e Windows - informazioni aggiuntive
| [2243692] | Linux in Microsoft Azure (IaaS) macchine Virtuali: problemi di licenza SAP
| [1984787] | SUSE LINUX Enterprise Server 12: Note sull'installazione
| [2002167] | Red Hat Enterprise Linux 7: installazione e aggiornamento

Leggere inoltre [Dello Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) che contiene tutte le note di SAP per Linux.

Avere familiarità con l'architettura di Microsoft Azure e come macchine virtuali di Microsoft Azure sono distribuito e gestito. È possibile trovare ulteriori informazioni in questo caso <https://azure.microsoft.com/documentation/>
 
> [AZURE.NOTE] Si **sta discutendo piattaforma Microsoft Azure come un offerte di servizi (PaaS) della piattaforma Microsoft Azure** . In questo articolo riguarda un sistema di gestione database (DBMS) in Microsoft Azure macchine virtuali in esecuzione (IaaS) come eseguire il DBMS nell'ambiente locale. Funzioni di database e funzionalità tra queste due offerte sono molto diverse e non devono essere combinate tra loro. Vedere anche: <https://azure.microsoft.com/services/sql-database/>

Poiché si stanno esaminando IaaS, in genere l'installazione di Windows, Linux e DBMS e la configurazione sono sostanzialmente diverso da quello di qualsiasi virtuali o computer metallo bare sarà necessario installare locale. Esistono tuttavia alcune architettura e sistema di gestione delle decisioni relative all'implementazione che saranno diversi quando si utilizza IaaS. Lo scopo di questo documento è descritte le specifiche all'architettura e le differenze di gestione di sistema che è necessario essere pronti per quando si usa IaaS.

In generale, le aree globale della differenza che verrà illustrate in questo articolo sono:

* Pianificazione di un layout macchine Virtuali/disco rigido virtuale appropriato di sistemi SAP avere a disposizione i dati appropriati file layout e ottenere abbastanza IOPS per il carico di lavoro.
* Considerazioni sulla rete quando si usa IaaS.
* Caratteristiche di database specifico da utilizzare per ottimizzare il layout di database.
* Eseguire il backup e ripristino considerazioni IaaS.
* Utilizzo di diversi tipi di immagini per la distribuzione.
* Disponibilità in IaaS Azure.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struttura di una distribuzione RDBMS
In ordine seguire questo capitolo, è necessario capire cosa presentato in [] [3 di Guida alla distribuzione] capitolo di [Guida alla distribuzione di] [Guida alla distribuzione]. Conoscere la serie di macchine Virtuali diversi e le differenze e le differenze di Azure Standard e lo spazio di archiviazione Premium deve essere interpretati e noti prima di leggere questo capitolo.

Fino a marzo 2015 dischi rigidi virtuali Azure che contengono un sistema operativo sono stati limitati a 127 GB dimensioni. Questa limitazione si ha elevata in marzo 2015 (per altre informazioni controllo <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/> ). Da tale posizione su dischi rigidi virtuali contenente il sistema operativo può avere le stesse dimensioni qualsiasi disco rigido virtuale. Tuttavia, si preferisce ancora una struttura di distribuzione in cui il sistema operativo, DBMS e l'eventuale file binari SAP sono separati dai file di database. Si prevede di conseguenza, sistemi SAP in esecuzione in macchine virtuali di Azure avrà la base macchine Virtuali o disco rigido virtuale installato con il sistema operativo, database management system eseguibili e SAP eseguibili. File di dati e log DBMS verranno archiviati in archiviazione di Azure (Standard o lo spazio di archiviazione Premium) file disco rigido virtuale distinti e allegati come dischi logici all'immagine originale di Azure del sistema operativo macchine Virtuali. 

Dipendono sfruttando Azure Standard o lo spazio di archiviazione Premium (ad esempio utilizzando la serie DS o macchine virtuali di serie GS) sono disponibili altre quote in Azure sono descritte [di seguito][virtual-machines-sizes]. Quando si pianifica il dischi rigidi virtuali Azure, è necessario trovare il bilanciamento ottimale delle quote per le operazioni seguenti:

* Il numero di file di dati.
* Il numero di dischi rigidi virtuali che contengono i file.
* Quote di IOPS di un singolo disco rigido virtuale.
* La velocità effettiva per disco rigido virtuale.
* Il numero di possibili dischi rigidi virtuali aggiuntive per dimensione memoria virtuale.
* La velocità di spazio di archiviazione complessiva può fornire una macchina virtuale.
 
Azure applicherà una quota IOPS per unità disco rigido virtuale. Queste quote sono diverse per dischi rigidi virtuali ospitati in Azure Standard dello spazio di archiviazione e di archiviazione aggiuntivo Premium. Latenza dei / o sarà anche molto diversa tra i due tipi di spazio di archiviazione con archiviazione Premium l'esecuzione di fattori latenza i/o migliore. Ognuno dei diversi tipi di macchine Virtuali contengono un numero limitato di dischi rigidi virtuali in grado di collegare. Un'altra limitazione è che solo alcuni tipi di macchine Virtuali possono sfruttare le spazio di archiviazione di Azure Premium. Questo errore indica che la decisione per un determinato tipo di macchine Virtuali potrebbe non solo dipendere dai requisiti di CPU e memoria, ma anche mediante IOPS, requisiti di velocità latenza e disco in genere adattabile con il numero di dischi rigidi virtuali o il tipo di spazio di archiviazione Premium dischi. In particolare con lo spazio di archiviazione Premium le dimensioni di un disco rigido virtuale inoltre potrebbero essere dipende dal numero di secondo e la velocità di trasmissione che deve essere ottenuto ogni disco rigido virtuale.

Il fatto che la velocità IOPS globale, il numero di dischi rigidi virtuali installati e le dimensioni della macchina virtuale sono tutti collegati tra loro, potrebbe causare una Azure configurazione di un sistema SAP per essere diversa da quella della distribuzione locale. Il limite IOPS per LUN sono in genere configurabile nelle distribuzioni locali. Mentre in archiviazione Azure tali limiti sono fisso o come spazio di archiviazione Premium dipendente dal tipo di disco. Pertanto con distribuzioni locali è possibile vedere configurazioni clienti dei server di database che utilizzano molti volumi diversi per i file eseguibili speciali come SAP e il DBMS o volumi speciali per i database temporanei o tabella spazi. Quando un sistema di locale viene spostato in Azure potrebbe portare a una perdita di larghezza di banda IOPS potenziale da perdite un disco rigido virtuale per eseguibili o database che non eseguono uno o non moltissimi IOPS. Di conseguenza, in macchine virtuali di Azure è consigliabile installare eseguibili DBMS e SAP su disco del sistema operativo se possibile.

La posizione del file di database e file di log e il tipo di archiviazione di Azure utilizzato, devono essere definite da latenza, secondo e requisiti di velocità effettiva. Per ottenere abbastanza IOPS per il registro delle transazioni, che si debba sfruttare più dischi rigidi virtuali per il file di registro delle transazioni o utilizzare un disco di archiviazione Premium più grande. In questo caso uno semplicemente da creare un software RAID (ad esempio Windows Pool di archiviazione per Windows o MDADM e LVM (logico Volume Manager) per Linux) con dischi rigidi virtuali che contengano il registro delle transazioni.

___

> ![Windows][Logo_Windows] Windows
>
> Unità D:\ in una macchina virtuale Azure è un'unità non permanente che è supportata da alcuni dischi locali sul nodo di calcolo Azure. Perché non persistente, significa che le modifiche apportate al contenuto sull'unità D:\ andrà perduta quando la macchina virtuale viene riavviata. Con "modifiche" si intendono i file salvati, directory create, le applicazioni installate, e così via.
>
> ![Linux][Logo_Linux] Linux
>
> Macchine virtuali di Azure Linux collegare automaticamente un'unità /mnt/resource che è un'unità non permanente supportata da dischi locali sul nodo di calcolo Azure. Perché non persistente, significa che le modifiche apportate al contenuto in /mnt/resource vengono persi durante la macchina virtuale viene riavviata. Le modifiche, è importante i file salvati, directory create, le applicazioni installate, e così via.

___

Dipende la serie di macchine Virtuali Azure, dischi locali sul nodo di calcolo illustrare le prestazioni diversi che possono essere classificata come:

* A0 A7: Prestazioni molto limitate. Non può essere utilizzato per tutte le operazioni oltre ai file di pagina di windows
* A8 A11: Caratteristiche di prestazioni ottimali molto con alcuni 1000 IOPS e > velocità effettiva almeno 1GB/sec
* Serie di D: Caratteristiche di prestazioni ottimali molto con alcuni 1000 IOPS e > velocità effettiva almeno 1GB/sec
* Serie DS: Caratteristiche di prestazioni ottimali molto con alcuni 1000 IOPS e > velocità effettiva almeno 1GB/sec
* G-serie: Caratteristiche di prestazioni ottimali molto con alcuni 1000 IOPS e > velocità effettiva almeno 1GB/sec
* Serie di GS: Caratteristiche di prestazioni ottimali molto con alcuni 1000 IOPS e > velocità effettiva almeno 1GB/sec

Istruzioni precedenti sono applicando i tipi di macchine Virtuali di certificato con SAP. La serie di macchine Virtuali con eccellente secondo e velocità idonei per l'utilizzo, alcune caratteristiche DBMS, ad esempio tempdb o spazio di tabella temporanea.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>La memorizzazione nella cache per macchine virtuali e dischi rigidi virtuali
Quando si crea tali dischi/dischi rigidi virtuali tramite il portale o quando viene caricati dischi rigidi virtuali in macchine virtuali di montaggio, è possibile scegliere se il traffico i/o tra la macchina virtuale e tali dischi rigidi virtuali all'interno dello spazio di archiviazione Azure vengono memorizzati nella cache. Azure Standard e lo spazio di archiviazione Premium usare due diverse tecnologie per questo tipo di cache. In entrambi i casi la cache di se stesso da disco eseguito il unità stesso utilizzato da disco temporaneo (D:\ in Windows) o /mnt/resource su Linux della macchina virtuale.
 
Per l'archiviazione Standard Azure i tipi di cache possibili sono:

* Alcuna memorizzazione nella cache
* Leggere la memorizzazione nella cache
* Leggere e scrivere la memorizzazione nella cache

Per ottenere prestazioni coerenti e deterministico, è necessario impostare la memorizzazione nella cache su Azure Standard dello spazio di archiviazione per tutti i dischi rigidi virtuali contenente **DBMS relativi file di dati, file di log e lo spazio di tabella per 'NONE'**. La memorizzazione nella cache della macchina virtuale può rimanere con il valore predefinito.

Per lo spazio di archiviazione di Azure Premium sono presenti le seguenti opzioni di memorizzazione nella cache:

* Alcuna memorizzazione nella cache
* Leggere la memorizzazione nella cache

Suggerimento per lo spazio di archiviazione di Azure Premium è per utilizzare al meglio **leggere la cache per i file di dati** del database SAP e non scelto **alcuna memorizzazione nella cache per VHD(s) dei file di log**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>Software RAID
Come già detto, sarà necessario bilanciare il numero di IOPS necessaria per i file di database attraverso il numero di dischi rigidi virtuali è possibile configurare e digitare IOPS massima una macchina virtuale Azure fornirà per disco rigido virtuale o lo spazio di archiviazione Premium disco. Più semplice per gestire il carico IOPS su dischi rigidi virtuali consiste nel creare un software RAID su dischi rigidi virtuali di diversi. Quindi assegnare un numero di file di dati del DBMS SAP a LUN separate dalla software RAID. Dipende requisiti da prendere in considerazione l'utilizzo di spazio di archiviazione Premium anche dopo due dei tre diversi dischi di archiviazione Premium offrono quota IOPS maggiore rispetto a dischi rigidi virtuali in base a Standard dello spazio di archiviazione. Oltre la latenza dei / o migliore significativa fornita dallo spazio di archiviazione di Azure Premium. 

Ciò vale anche per il registro delle transazioni dei sistemi DBMS diversi. Con numerose loro appena aggiunto tutti i file desiderati del log delle transazioni non consente di dal momento che i sistemi DBMS scrivere in uno dei file in una sola volta. Se la velocità di IOPS è necessari più spazio di archiviazione basato su un singolo Standard disco rigido virtuale può fornire, è possibile stripe su più dischi rigidi virtuali lo spazio di archiviazione Standard o è possibile utilizzare un tipo di disco archiviazione Premium più grande che oltre la velocità di IOPS offre latenza inferiore fattori per la scrittura i/o nel log delle transazioni.
 
Situazioni esperti in Azure distribuzioni che si preferiscono utilizzare un software RAID sono:

* Registro delle transazioni Log e ripristinare richiedono ulteriori IOPS rispetto a quelle Azure fornite per un singolo disco rigido virtuale. Come detto in precedenza si possono essere risolti creando un LUN su più dischi rigidi virtuali mediante un software RAID.
* Irregolari i/o distribuzione carico di lavoro rispetto ai file di dati diversi del database SAP. In questi casi uno può subire un file di dati di raggiungere la quota piuttosto spesso. Mentre in altri file di dati non si ricevono anche vicino la quota IOPS di un singolo disco rigido virtuale. In tal caso la soluzione più semplice è creare un LUN su più dischi rigidi virtuali mediante un software RAID. 
* Non si conosce quali il carico di lavoro i/o esatta per file di dati e solo circa riconosce il carico di lavoro globale IOPS contro DBMS. È facile eseguire per creare un LUN con l'aiuto di un software RAID. La somma delle quote di più dischi rigidi virtuali dietro questo LUN deve soddisfare quindi velocità IOPS nota.

___

> ![Windows][Logo_Windows] Windows
>
> L'uso di Windows Server 2012 o versioni successive spazi di archiviazione è preferibile poiché è la più efficiente rispetto al Striping Windows di versioni precedenti di Windows. Tenere presente che potrebbe essere necessario creare il pool di archiviazione di Windows e spazi di archiviazione utilizzando i comandi di PowerShell quando si utilizza Windows Server 2012 come sistema operativo. I comandi di PowerShell disponibili qui <https://technet.microsoft.com/library/jj851254.aspx>

> 
> ![Linux][Logo_Linux] Linux
>
> Per creare un software RAID su Linux sono supportati solo MDADM e LVM (logico Volume Manager). Per ulteriori informazioni, leggere gli articoli seguenti:
>
> * [Configurare il Software RAID su Linux] [ virtual-machines-linux-configure-raid] (per MDADM)
> * [Configurare LVM su una macchina virtuale Linux in Azure][virtual-machines-linux-configure-lvm]


___

Considerazioni per l'utilizzo di macchine Virtuali di serie che sono in grado di utilizzare lo spazio di archiviazione di Azure Premium in genere sono:

* Richieste di latenza dei / o che si trovano vicino garantiscono dispositivi SAN/NAS.
* Domanda di fattori meglio i/o latenza che può fornire l'archiviazione Standard Azure.
* Superiore IOPS macchine Virtuali di cosa consentirebbe con più dischi rigidi di spazio di archiviazione Standard virtuali rispetto a un determinato tipo di macchine Virtuali.

Poiché l'archiviazione di Azure sottostante replica ogni disco rigido virtuale in almeno tre nodi di archiviazione, semplice RAID 0 striping può essere utilizzato. Non è necessario implementare RAID5 o RAID1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Spazio di archiviazione di Microsoft Azure
Lo spazio di archiviazione di Microsoft Azure verrà archiviata la base macchina virtuale (con sistema operativo) e dischi rigidi virtuali o BLOB per almeno 3 nodi di archiviazione separata. Quando si crea un account di archiviazione, è possibile scegliere di protezione come illustrato di seguito:

![Replica geografico abilitata per lo spazio di archiviazione di Azure account][dbms-guide-figure-100]

Azure lo spazio di archiviazione della replica locale (in locale ridondanti) fornisce diversi livelli di protezione dalla perdita di dati a causa di errore di infrastruttura che solo alcuni utenti potrebbero rimanere per distribuire. Come mostrato in precedenza sono disponibili diverse 4 opzioni con una quinta da una variazione di uno dei tre prima. Osservando più da vicino loro è possibile distinguere:

* **Premium localmente ridondanti dello spazio di archiviazione (LRS)**: lo spazio di archiviazione di Azure Premium fornisce supporto disco prestazioni ad alta, bassa latenza per macchine virtuali in esecuzione è/O-che richiede significative attività carichi di lavoro. Sono disponibili 3 repliche dei dati all'interno del Data Center del Azure stesso di un'area di Azure. Le copie saranno diversi guasto e aggiornare i domini (per i concetti vedere [questa] [pianificazione-Guida-3,2] capitoli [Guide][planning-guide]) pianificazione. In caso di replica dei dati in uscita dal servizio a causa di un errore di nodo dello spazio di archiviazione o errore del disco, viene generata automaticamente una nuova replica.
* **Spazio di archiviazione ridondanti in locale (LRS)**: In questo caso sono disponibili 3 repliche dei dati all'interno del Data Center del Azure stesso di un'area di Azure. Le copie saranno diversi guasto e aggiornare i domini (per i concetti vedere [questa] [pianificazione-Guida-3,2] capitoli [Guide][planning-guide]) pianificazione. In caso di replica dei dati in uscita dal servizio a causa di un errore di nodo dello spazio di archiviazione o errore del disco, viene generata automaticamente una nuova replica. 
* **Spazio di archiviazione ridondanti geografico (GRS)**: In questo caso esiste una replica asincrona che verrà feed un repliche 3 aggiuntive dei dati in un'altra area geografica di Azure nella maggior parte dei casi nella stessa area geografica (ad esempio Europa occidentale ed Europa Nord America). Verrà creato in 3 repliche aggiuntive, in modo che non esistono 6 repliche in somma. Una variazione di questo è un componente aggiuntivo in cui i dati presenti nell'area di Azure replicato geografico possono essere utilizzati per la lettura (accesso in lettura geografico ridondante).
* **Zona ridondanti dello spazio di archiviazione (ZRS)**: In questo caso 3 repliche dei dati rimarranno nella stessa regione di Azure. Come spiegato in [] [pianificazione-Guida-3.1] capitolo della [Guida alla pianificazione] [-Guida alla pianificazione] un'area di Azure può essere un numero di Data Center in prossimità. Nel caso dei LRS repliche vengono distribuite più il Data Center diverso da rendere una regione Azure.

Altre informazioni sono disponibili [qui][storage-redundancy].
 
> [AZURE.NOTE] Per distribuzioni DBMS, non è consigliabile l'utilizzo di spazio di archiviazione ridondanti geografico
>
> Spazio di archiviazione Azure geografico-replica è asincrona. Replica di singoli dischi rigidi virtuali installato a un singolo macchine Virtuali non vengono sincronizzati nel passaggio di blocco. Non è pertanto appropriata replicare file DBMS distribuiti in diversi dischi rigidi virtuali o distribuiti da un software RAID in base a più dischi rigidi virtuali. Software DBMS richiede che lo spazio di archiviazione disco persistente è sincronizzato con precisione in LUN diversi e sottostante dischi/dischi rigidi virtuali/assi. Software DBMS utilizza diversi meccanismi alle attività di scrittura IO sequenza e un DBMS segnalerà che lo spazio di archiviazione disco riservata dalla replica è danneggiato se si va anche con pochi millisecondi. Pertanto se una richiesta una configurazione di database con un database esteso su più dischi rigidi virtuali replicato geografico, ad esempio replica deve essere eseguita con database medie e funzionalità. Una non deve basarsi su Azure archiviazione geografico replica per eseguire questo processo. 
>
> Il problema è più semplice spiegare con un sistema di esempio. Si supponga di che avere un sistema SAP caricato in Azure con 8 dischi rigidi virtuali contenente i file di dati di DBMS più un disco rigido virtuale che contiene il file di log transazione. Ognuna di questi 9 dischi rigidi virtuali avrà dati scritti in un metodo coerente in base alle DBMS, se i dati vengono salvati i file di log transazioni o dati.
>
> Per correttamente geografico replicare i dati e gestire un'immagine di database coerente, il contenuto di tutti i dischi rigidi nove virtuali devono essere replicate geografico nello stesso ordine delle operazioni sono state eseguite le nove dischi rigidi virtuali diversi in. Tuttavia, lo spazio di archiviazione di Azure geografico replica non consente di dichiarare le dipendenze tra dischi rigidi virtuali. Questo errore indica lo spazio di archiviazione di Microsoft Azure geografico replica non riconosce il fatto che il contenuto in questi nove dischi rigidi virtuali diversi sono correlate tra loro e che le modifiche ai dati sono coerenti solo quando la replica nell'ordine delle operazioni di dove si trova tra i dischi rigidi 9 virtuali.
>
> Oltre a probabilità viene elevato immagini replicato geografico nello scenario non forniscono un'immagine coerente del database, è anche un insieme di prestazioni che viene visualizzata con geografico ridondanti dello spazio di archiviazione che possa influire gravemente sulle prestazioni. In riepilogo non utilizzare questo tipo di ridondanza di archiviazione per DBMS tipo carichi di lavoro.
 
#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>Mapping dischi rigidi virtuali in account di archiviazione del servizio di Azure macchina virtuale
Un Account di archiviazione di Azure è non solo un costrutto amministrativo, ma anche un oggetto di limitazioni. Considerando che le limitazioni variano in se si parla Account Azure Standard dello spazio di archiviazione o un Account di archiviazione di Azure Premium. Il proprio le funzionalità e limitazioni sono elencate [qui][storage-scalability-targets]
 
In modo che per l'archiviazione Standard Azure è importante tenere presente nel IOPS account di archiviazione è presente un limite (riga contenente 'Frequenza richiesta totale' [nell'articolo][storage-scalability-targets]). Inoltre, non esiste un limite iniziale di 100 gli account di archiviazione per la sottoscrizione Azure (dal luglio 2015). Di conseguenza, è consigliabile saldo totale IOPS di macchine virtuali tra più account di archiviazione quando si usa lo spazio di archiviazione Standard di Azure. Mentre un singolo macchine Virtuali ideale utilizza un account di archiviazione se possibile. Pertanto se si parla distribuzioni DBMS in cui ogni disco rigido virtuale ospitati in archiviazione Standard Azure Impossibile raggiungere il limite di quota, è opportuno distribuire solo dischi rigidi virtuali 30-40 per conto di spazio di archiviazione di Azure che utilizza l'archiviazione Standard Azure. Mano, se si sfruttare lo spazio di archiviazione di Azure Premium e si desidera memorizzare i volumi di database di grandi dimensioni, potrebbe essere fitta in termini di IOPS. Ma un Account di archiviazione di Azure Premium consiste nel modo più restrittivo volume dati Account Azure Standard dello spazio di archiviazione. Di conseguenza, è possibile distribuire solo un numero limitato di dischi rigidi virtuali all'interno di un Account di archiviazione di Azure Premium prima di raggiungere il limite di volume di dati. L'interazione utente finale di un Account di archiviazione Azure come "SAN virtuale" che è limitato funzionalità in IOPS e/o capacità. Di conseguenza, l'attività rimane, come illustrato distribuzioni locali, per definire il layout di dischi rigidi virtuali sistemi SAP diversi sul 'immaginario SAN dispositivi diversi' o gli account di archiviazione Azure.
 
Per l'archiviazione Standard Azure non è consigliabile presentare lo spazio di archiviazione dall'account di archiviazione diverso per una singola macchina virtuale se possibile.

Mentre l'utilizzo della directory o della serie GS delle macchine virtuali di Azure è comunque possibile dischi rigidi virtuali montaggio disconnettersi da Azure gli account di archiviazione Standard e gli account di archiviazione Premium. Casi di utilizzo come scrivere i backup in archiviazione Standard sottoposta dischi rigidi virtuali considerando che dati DBMS e i file di log in archiviazione Premium vengono in mente nel punto in cui tali archiviazione eterogenei potrebbe risultare utili. 

In base a distribuzioni del cliente e verifica circa 30-40 dischi rigidi virtuali contenente il file di dati di database e file di log possono effettuare il provisioning in un unico Azure Standard dello spazio di archiviazione conto con prestazioni accettabili. Come detto in precedenza, il limite di un Account di archiviazione di Azure Premium è probabile che sia la capacità di dati che può contenere e non IOPS.

Come con SAN dispositivi in locale, condivisione richiede alcune monitoraggio per non hanno un limite rileva bottiglia per un Account di archiviazione Azure. L'estensione monitoraggio Azure per SAP e il portale di Azure sono strumenti che possono essere utilizzati per rilevare occupato gli account di archiviazione di Azure che può offrire prestazioni IO non ottimale.  Se viene rilevata questa situazione che è consigliabile spostare macchine virtuali occupate a un altro Account di archiviazione di Azure. Per informazioni dettagliate su come attivare l'host SAP funzionalità di monitoraggio, vedere [Guida alla distribuzione di] [Guida alla distribuzione].

Ecco un altro articolo i riepiloghi consigliate per l'archiviazione Standard Azure e Azure Standard gli account di archiviazione disponibile in <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>
 
#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>Spostamento distribuito macchine virtuali di DBMS dallo spazio di archiviazione Azure Standard per lo spazio di archiviazione di Azure Premium
È riscontrare un certo situazioni come cliente cui spostare una macchina virtuale distribuita dallo spazio di archiviazione Standard di Azure nello spazio di archiviazione di Azure Premium. Questa operazione non è possibile senza fisica spostare i dati. Esistono diversi modi per raggiungere l'obiettivo:

* È possibile copiare semplicemente tutti dischi rigidi virtuali, disco rigido virtuale di base, nonché dischi rigidi virtuali dati in un nuovo Account di archiviazione Premium Azure. Molto spesso si è scelto il numero di dischi rigidi virtuali in archiviazione Standard Azure non a causa del fatto che è necessario il volume di dati. Ma è necessario che molte dischi rigidi virtuali a causa di IOPS. Ora che si sposta per lo spazio di archiviazione di Azure Premium è possibile andare al modo in cui meno dischi rigidi virtuali per ottenere la velocità IOPS alcuni. Dato che in Azure archiviazione pagare per i dati usati e non la dimensione del disco nominale Standard, il numero di dischi rigidi virtuali non è importante in termini di costi. Tuttavia, con l'archiviazione Premium Azure, avrebbe pagato per la dimensione del disco nominale. La maggior parte dei clienti di conseguenza, provare a tenere il numero di dischi rigidi virtuali Azure Premium lo spazio di archiviazione in corrispondenza del numero necessario per raggiungere la velocità IOPS necessarie. La maggior parte dei clienti decidono rispetto a quella di un semplice 1:1 e quindi copia.
* Se non ancora installato, non è installare un singolo disco rigido virtuale che può contenere un backup del database SAP. Dopo il backup, disinstallare tutti dischi rigidi virtuali incluso il disco rigido virtuale contenente il backup e copiare il disco rigido virtuale di base e il disco rigido virtuale con la copia di backup in un account di archiviazione di Azure Premium. Finestra di quindi distribuire la macchina virtuale basata su disco rigido virtuale di base e installare il disco rigido virtuale con il backup. È ora possibile creare ulteriori dischi di archiviazione Premium vuota per la macchina virtuale utilizzati per ripristinare il database in. Si presuppone che il DBMS consente di modificare percorsi dei file di dati e log come parte del processo di ripristino.
* Un'altra possibilità è una variazione del ex processo, nel punto in cui sufficiente copiare il backup disco rigido virtuale lo spazio di archiviazione di Azure Premium e allegarlo rispetto a una macchina virtuale che si è appena distribuito e installato.
* La possibilità di quarta scegliendo quando si è bisognose per modificare il numero di file di dati del database. In questo caso eseguire una copia di omogeneo sistema SAP utilizzando l'esportazione/importazione. Inserire i file di esportazione in un disco rigido virtuale che viene copiato in un Account di archiviazione di Azure Premium e collegarlo a una macchina virtuale che consente di eseguire i processi di importazione. Clienti usare questa possibilità principalmente quando si desidera ridurre il numero di file di dati.

### <a name="deployment-of-vms-for-sap-in-azure"></a>Distribuzione di macchine virtuali per SAP in Azure
Microsoft Azure in diversi modi per distribuire macchine virtuali e dischi associati. In questo modo è molto importante conoscere le differenze poiché preparati delle macchine virtuali potrebbero essere diversi dipende dal modo di distribuzione. In generale è esaminare gli scenari descritti nei capitoli seguenti.

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>Distribuzione di una macchina virtuale di Azure Marketplace
Si desidera eseguire un Microsoft o parti 3 ° fornito immagine da Azure Marketplace per distribuire le macchine Virtuali. Dopo aver distribuito le macchine Virtuali di Azure, seguire la stessa linee guida e strumenti per installare il software SAP all'interno di una macchina virtuale come si farebbe in un ambiente locale. Per installare il software SAP all'interno della macchina virtuale Azure, SAP e Microsoft consiglia per caricare e archiviare il supporto di installazione SAP in dischi rigidi virtuali Azure o per creare una macchina virtuale Azure funziona come un "server di File" che contiene tutte le necessarie SAP supporto di installazione.

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>Distribuzione di una macchina virtuale con un'immagine GRG specifica del cliente
A causa di requisiti patch specifiche relative alla versione del sistema operativo o DBMS, le immagini disponibili in Azure Marketplace potrebbero non secondo le proprie esigenze. Di conseguenza, potrebbe essere necessario creare una macchina virtuale utilizzando la propria immagine macchine Virtuali del sistema operativo/DBMS 'private' che può essere distribuito più volte in un secondo momento. Per preparare ad esempio un'immagine 'privata' per la duplicazione, il sistema operativo deve essere generalizzato nella macchina virtuale locale. Per informazioni dettagliate su come generalizzare una macchina virtuale, vedere [Guida alla distribuzione di] [Guida alla distribuzione].

Se è già stato installato contenuto SAP in macchine Virtuali il locale (in particolare per sistemi di livello 2), è possibile adattare le impostazioni di sistema SAP dopo la distribuzione di macchine Virtuali di Azure attraverso l'istanza di rinominare procedure supportata da SAP Software il Provisioning di gestore (nota SAP [1619720]). In caso contrario è possibile installare il software SAP in un secondo momento dopo la distribuzione di Azure macchina virtuale.
 
Prima del contenuto il database utilizzato dall'applicazione SAP, è possibile generare il contenuto appena da un'installazione SAP o è possibile importare contenuto in Azure utilizzando un disco rigido virtuale con una copia di backup del database DBMS o sfruttando funzionalità del DBMS di backup direttamente in Microsoft Azure archiviazione. In questo caso, si può anche preparare dischi rigidi virtuali con i DBMS dati e log file locale e quindi importare come dischi in Azure. Ma il trasferimento di dati DBMS Guida caricati da locale in Azure altrettanto dischi disco rigido virtuale che devono essere preparati locale.

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>Spostamento di una macchina virtuale da locale a Azure con un disco non generalized
Si prevede di spostare un sistema SAP specifico da locale a Azure (ascensore e MAIUSC). Ciò è possibile caricando il disco rigido virtuale che contiene il sistema operativo, i file binari SAP e un eventuale file binari DBMS più dischi rigidi virtuali con i file di dati e log di DBMS di Azure. In opposto scenario #2 precedente, mantenere il nome host, SID SAP e account utente SAP nella macchina virtuale Azure come sono state configurate nell'ambiente locale. Di conseguenza, generalizzazione l'immagine non è necessario. In questo caso applicherà prevalentemente per gli scenari di croce locale in cui una parte del paesaggio SAP viene eseguita in locale e parti in Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Disponibilità e il ripristino di emergenza con macchine virtuali di Azure
Azure offre le seguenti funzionalità di alta disponibilità (HA) e il ripristino di emergenza (DR) che si applicano a diversi componenti che serve per distribuzioni SAP e DBMS

### <a name="vms-deployed-on-azure-nodes"></a>Macchine virtuali distribuite in nodi di Azure
La piattaforma Azure non offre caratteristiche quali Live Migration per macchine virtuali distribuite. Pertanto, se in un cluster di server in cui viene distribuita una macchina virtuale è necessaria manutenzione, la macchina virtuale arrestato e si riavvia. Manutenzione in Azure viene eseguita tramite definiti in modo aggiornare domini all'interno di cluster di server. Viene mantenuto solo un aggiornamento del dominio alla volta. Durante tale riavvio si verificherà un'interruzione del servizio mentre la macchina virtuale viene chiuso, di manutenzione e si riavvia macchine Virtuali. La maggior parte dei fornitori DBMS tuttavia funzionalità disponibilità e il ripristino di emergenza che riavvia rapidamente i servizi DBMS in un altro nodo se il nodo principale non è disponibile. La piattaforma Azure offre funzionalità per distribuire macchine virtuali, lo spazio di archiviazione e altri servizi di Azure in aggiornare i domini per assicurarsi che gli errori di manutenzione o infrastruttura pianificati da impatto solo un sottoinsieme di macchine virtuali o servizi.  Con una pianificazione accurata è possibile ottenere livelli di disponibilità analoghi alle infrastrutture locale.

Set di disponibilità di Microsoft Azure sono un raggruppamento logico di macchine virtuali o servizi che assicura macchine virtuali e altri servizi che sono distribuiti in diversi guasto e l'aggiornamento di domini in un cluster in modo che non vi sarà solo un arresto nodo in qualsiasi punto nel tempo (leggere [questo] [ virtual-machines-manage-availability] articolo per informazioni dettagliate).

È necessario configurare in base allo scopo di attuare macchine virtuali come illustrato di seguito:

![Definizione di un Set di disponibilità per le configurazioni DBMS HA][dbms-guide-figure-200]

Se si desidera creare configurazioni di disponibile delle distribuzioni DBMS (indipendenti delle singole DBMS HA funzionalità utilizzate), sarà necessario macchine virtuali DBMS:

* Aggiungere le macchine virtuali della stessa rete virtuale di Azure (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* Macchine virtuali di configurazione HA inoltre opportuno nella stessa subnet. Risoluzione dei nomi tra le diverse subnet non è supportata nelle distribuzioni basata solo su Cloud, funziona solo la risoluzione IP. Usa da sito o la connettività ExpressRoute per distribuzioni locali di croce, una rete con almeno una subnet verrà già stabilita. Verrà eseguita la risoluzione dei nomi in locale sulla base dell'infrastruttura di rete e criteri di Active Directory. 
[commento]: <>  (Test TODO MSSedusch se comunque true in ARM)

#### <a name="ip-addresses"></a>Indirizzi IP
Si consiglia di configurazione macchine virtuali per le configurazioni HA in modo flessibile. Basarsi su indirizzi IP per risolvere i partner HA all'interno della configurazione HA non è affidabile in Azure a meno che non vengono utilizzati indirizzi IP statici. Esistono due concetti "Arresto" in Azure:

* Arrestare tramite portale Azure o PowerShell Azure cmdlet Interrompi AzureRmVM: In questo caso la macchina virtuale Ottiene arresto e deselezionare ricevuto. L'account Azure non verrà addebitato per questa macchina virtuale non è più in modo che solo in base alle tariffe che causeranno siano per lo spazio di archiviazione utilizzato. Tuttavia, se l'indirizzo IP privato dell'interfaccia di rete non statica, l'indirizzo IP viene rilasciato e non è garantito che l'interfaccia di rete Ottiene l'indirizzo IP precedente assegnato nuovamente dopo il riavvio di macchina virtuale. Eseguire la chiusura verso il basso tramite il portale Azure o chiamando Interrompi AzureRmVM verranno automaticamente de allocazione. Se non si desidera deallocat la macchina utilizzare Interrompi AzureRmVM - StayProvisioned 
* Se si arresta macchine Virtuali dal livello del sistema operativo, la macchina virtuale Ottiene arrestare e non deselezionare allocata. Tuttavia, in questo caso, l'account Azure verrà comunque addebitato macchine Virtuali, nonostante il fatto che si tratti di chiusura. In tal caso, l'assegnazione dell'indirizzo IP da una macchina virtuale ha smesso di rimarrà invariata. Arrestare la macchina virtuale all'interno di non automaticamente impone allocazione di annullamento.

Anche per gli scenari tra locale, per impostazione predefinita un arresto e-allocazione significa assegnazione disattivazione degli indirizzi IP da macchine Virtuali, anche se i criteri in locale in impostazioni DHCP sono diversi. 

* L'eccezione è uno viene assegnato un indirizzo IP statico all'interfaccia di rete come descritto [di seguito][virtual-networks-reserved-private-ip].
* In questo caso l'indirizzo IP resta come l'interfaccia di rete non viene eliminato.

> [AZURE.IMPORTANT] Per mantenere l'intera distribuzione semplice e gestibile, recommendation Cancella consiste nel configurare macchine virtuali collaborazione un DBMS HA DR configurazione o all'interno di Azure in modo che è presente una risoluzione dei nomi di funzioni tra macchine virtuali diverse correlate.
 
## <a name="deployment-of-host-monitoring"></a>Distribuzione di monitoraggio degli Host
Per l'utilizzo di produttivo delle applicazioni SAP in macchine virtuali di Azure, SAP richiede la possibilità di ottenere host il monitoraggio dei dati da host fisici macchine virtuali Azure. Un livello di patch SAP HostAgent specifico devono avvenire che consente di questa funzionalità in SAPOSCOL e HostAgent SAP. Il livello di patch esatta descritte in SAP nota [1409604].

Per informazioni dettagliate sulla distribuzione dei componenti in grado di host SAPOSCOL e SAPHostAgent e la gestione del ciclo di vita dei componenti, fare riferimento [Guida alla distribuzione di] [Guida alla distribuzione]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Specifiche per Microsoft SQL Server

### <a name="sql-server-iaas"></a>SQL Server IaaS
A partire da Microsoft Azure, è possibile migrare le applicazioni di SQL Server esistenti basate su piattaforma Windows Server in macchine virtuali di Azure. SQL Server in una macchina virtuale consente di ridurre il costo totale di proprietà di distribuzione, gestione e la manutenzione di applicazioni aziendali ampiezza facilmente la migrazione di queste applicazioni di Microsoft Azure. Con SQL Server in una macchina virtuale Azure, amministratori e sviluppatori possono utilizzare la stessa sviluppo e gli strumenti di amministrazione che sono disponibili in locale. 

> [AZURE.IMPORTANT] Nota che non si sta esaminando Database di SQL Azure Microsoft, ovvero una piattaforma come un'offerta di servizi della piattaforma Microsoft Azure. La discussione in questo articolo è relativo che esegue il prodotto di SQL Server noto per distribuzioni locali in macchine virtuali di Azure, sfruttando l'infrastruttura di come una funzionalità di servizio di Azure. Funzioni di database e funzionalità tra queste due offerte sono diverse e non devono essere combinate tra loro. Vedere anche: <https://azure.microsoft.com/services/sql-database/>
 
Si consiglia di esaminare [questa] [ virtual-machines-sql-server-infrastructure-services] documentazione prima di continuare.

Nelle sezioni seguenti verranno aggregati parti di parti della documentazione sotto il collegamento sopra e menzionati. Specifiche relative alle SAP sono citate anche e alcuni concetti sono descritti in modo più dettagliato. Tuttavia, si consiglia di lavorare anche la documentazione sopra prima prima di leggere la documentazione specifica di SQL Server.

In informazioni specifiche IaaS che è necessario conoscere prima di continuare esiste alcune SQL Server:

* **Contratto di servizio macchina virtuale**: esiste un contratto di servizio per macchine virtuali in esecuzione in Azure che può essere trovato qui: <https://azure.microsoft.com/support/legal/sla/>  
* **Supporto tecnico versione SQL**: per i clienti SAP, sono supportate SQL Server 2008 R2 o versioni successive in Microsoft Azure Virtual Machine. Edizioni precedenti non sono supportate. Leggere questa generale [Istruzione di supporto](https://support.microsoft.com/kb/956893) per altri dettagli. Si noti che in genere SQL Server 2008 è supportata anche. Tuttavia, a causa di funzionalità significativa per SAP introdotta con SQL Server 2008 R2, SQL Server 2008 R2 è la versione minima per SAP. Tenere presente che se si ha esteso SQL Server 2012 e il 2014 con maggiore integrazione nello scenario IaaS (ad esempio backup direttamente sull'archiviazione Azure). Di conseguenza, limitare questo documento per SQL Server 2012 e 2014 con il relativo livello patch più recente per Azure.
* **Supporto delle funzionalità SQL**: caratteristiche più SQL Server sono supportate in macchine virtuali di Microsoft Azure con alcune eccezioni. **SQL Server Failover cluster utilizzando dischi condiviso non è supportato**.  Distribuito tecnologie come Database che rispecchiano, disponibilità AlwaysOn, replica, la distribuzione dei Log e servizio gestore sono supportati in una singola regione di Azure. SQL Server AlwaysOn è supportata anche tra diverse aree di Azure come descritto di seguito: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.  Controllare l' [Istruzione di supporto](https://support.microsoft.com/kb/956893) per altri dettagli. In [questo] è illustrato un esempio su come distribuire una configurazione AlwaysOn[ virtual-machines-workload-template-sql-alwayson] articolo. Inoltre, estrarre le procedure consigliate documentazione [qui][virtual-machines-sql-server-infrastructure-services] 
* **Prestazioni SQL**: siamo certi che Microsoft Azure macchine virtuali ospitato eseguirà ben rispetto a quando altre offerte di virtualizzazione cloud pubblico, ma i singoli risultati possono variare. Guardare [questo] [ virtual-machines-sql-server-performance-best-practices] articolo.
* **Utilizzo di immagini da Azure Marketplace**: il modo più veloce per distribuire una nuova macchina virtuale di Microsoft Azure consiste nell'utilizzare un'immagine da Azure Marketplace. Sono disponibili immagini di Azure Marketplace che contengono SQL Server. Le immagini in cui già è installato SQL Server non è possibile immediatamente utilizzate per le applicazioni SAP NetWeaver. Il motivo è che all'interno di tali immagini e non le regole di confronto richiesti da sistemi SAP NetWeaver è installato il confronto di SQL Server predefinito. Per utilizzare ad esempio immagini, controllare la procedura descritta in capitolo [usando un immagini di SQL Server disconnettersi da Microsoft Azure Marketplace] [5.6 di Guida dbms]. 
* Controllare i [Prezzi dettagli](https://azure.microsoft.com/pricing/) per ulteriori informazioni. La [Guida alla gestione delle licenze di SQL Server 2012](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) e [Guida di gestione delle licenze di SQL Server 2014](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf) sono anche una risorsa importante.
 
### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>Istruzioni di configurazione di SQL Server per SAP correlati installazioni di SQL Server in macchine virtuali di Azure

#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Consigli sulla struttura di macchine Virtuali/disco rigido virtuale per SAP correlati distribuzioni di SQL Server
Secondo descrizione generale eseguibili di SQL Server dovrebbero essere disponibile o installati in unità di sistema del disco rigido virtuale base di Virtual Machine (unità c:\).  In genere, la maggior parte dei database di sistema di SQL Server non vengono utilizzata un livello elevato da SAP NetWeaver carico di lavoro. Di conseguenza i database di sistema di SQL Server (master, msdb e model) possono rimanere sull'unità C:\ anche. Un'eccezione potrebbe essere tempdb, che in caso di alcuni ERP SAP e tutti carichi di lavoro bianco e nero, che sia necessario maggiore volume di dati o volume di operazioni i/o che non si adattarla macchina virtuale originale. Per tali sistemi deve essere eseguita la procedura seguente:

* Spostare i file di dati tempdb principale della stessa unità logica come file di dati principale del database SAP.
* Aggiungere i file di dati aggiuntivi tempdb a ciascuna delle altre unità logiche contenente un file di dati del database utente SAP.
* Aggiungere il file di log tempdb unità logica che contiene il file di log del database utente.
* **Esclusivamente per i tipi di macchine Virtuali che utilizzano SSDs locale** nel calcolo nodo tempdb file di dati e log potrebbe trovarsi in unità D:\. Tuttavia, potrebbe essere consiglia di utilizzare più file di dati di tempdb. Prestare particolare attenzione D:\ unità risultano diversi in base al tipo di macchine Virtuali.
 
Queste configurazioni abilitano tempdb di utilizzare più spazio di unità di sistema in grado di fornire. Per determinare le dimensioni di tempdb corretto, una possibile controllare le dimensioni di tempdb sui sistemi esistenti eseguiti in locale. Inoltre, ad esempio una configurazione consentirebbe numeri IOPS rispetto a tempdb che non può essere fornito con l'unità di sistema. Nuovo sistemi che eseguono locale utilizzare per il monitoraggio di carico di lavoro i/o per tempdb in modo che è possibile derivano i numeri IOPS che si prevede di guardare le tempdb.

Una configurazione macchina virtuale che esegue SQL Server con un database SAP e posizione tempdb dati e file di log tempdb sull'unità D:\ sarà simile a:
 
![Configurazione di riferimento di Azure macchine Virtuali IaaS per SAP][dbms-guide-figure-300]

Tenere presente che l'unità D:\ abbia dimensioni diverse dipende dal tipo macchine Virtuali. Dipende il requisito di dimensioni di tempdb è che si debba coppia tempdb log file di dati e con SAP database file di dati e log nei casi in cui è troppo piccolo D:\ unità.

#### <a name="formatting-the-vhds"></a>Formattazione di dischi rigidi virtuali
Per SQL Server NTFS Blocca dimensioni per dischi rigidi virtuali contenente dati di SQL Server e file di log devono essere 64 KB. Non è necessario formattare l'unità D:\. Unità include pre-formattata.

Per assicurarsi che il ripristino o la creazione di database non inizializzazione i file di dati da mantenendo focalizzato il contenuto dei file, una necessario assicurarsi che il contesto dell'utente in che il servizio SQL Server è in esecuzione determinate autorizzazioni. In genere gli utenti al gruppo degli amministratori di Windows hanno le autorizzazioni. Se il servizio SQL Server viene eseguito nel contesto dell'utente dell'utente non Windows amministratore, è necessario assegnare il diritto di utente 'Eseguire le operazioni di manutenzione volume' quell'utente.  Visualizzare i dettagli in questo articolo della Microsoft Knowledge Base: <https://support.microsoft.com/kb/2574695>
 
#### <a name="impact-of-database-compression"></a>Impatto della compressione di database
In configurazioni nel punto in cui la larghezza di banda i/o può diventare un fattore di limitazione, tutte le misure riducendo IOPS potrebbe essere utile per estendere il carico di lavoro sarà possibile eseguire in uno scenario di IaaS come Azure. Pertanto, se non è ancora stata eseguita, l'applicazione di compressione di pagina di SQL Server è consigliato da SAP e Microsoft prima di caricamento di un SAP esistente database in Azure.
 
Suggerimento per eseguire la compressione del Database prima di caricare in Azure è dato dalla due motivi:

* La quantità di dati da caricare è inferiore.
* La durata dell'esecuzione compressione è breve presupponendo che è possibile utilizzare hardware più complessa con più CPU o larghezza di banda i/o superiore o inferiore i/o latenza locale.
* Dimensioni database inferiori possano essere meno i costi per l'allocazione disco

Compressione di database avviene anche in un macchine virtuali di Azure locale. Per ulteriori informazioni su come comprimere un Server SQL SAP esistente database, vedere qui: <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>
  
### <a name="sql-server-2014--storing-database-files-directly-on-azure-blog-storage"></a>SQL Server 2014: memorizzazione Database file direttamente su Azure Blog
SQL Server 2014 verrà visualizzata la possibilità di archiviare i file di database direttamente in archivio Blob Azure senza spaziale di un disco rigido virtuale circostanti. In particolare con l'uso dello spazio di archiviazione di Azure Standard o tipi di macchine Virtuali più piccoli in questo modo scenari in cui è possibile superare i limiti di IOPS che viene applicata a un numero limitato di dischi rigidi virtuali che può essere installato a alcuni tipi di macchine Virtuali più piccoli. Funziona per i database utente tuttavia non per i database di sistema di SQL Server. Vale anche per i file di dati e log di SQL Server. Se si desidera distribuire un database SAP SQL Server in questo modo anziché 'ritorno a capo' in dischi rigidi virtuali, tenere le operazioni seguenti presente:

* Account di archiviazione esigenze in veniva eseguita nella stessa regione Azure come quello che viene utilizzato per distribuire macchine Virtuali SQL Server è in esecuzione in.
* Elencate in precedenza in merito alla distribuire dischi rigidi virtuali su più account di archiviazione di Azure considerazioni per questo metodo di distribuzioni anche. Indica il conteggio di operazioni i/o fronte dei limiti dell'Account di archiviazione Azure.
[commento]: <>  (MSSedusch TODO ma verrà utilizzato larghezza di banda di rete e non larghezza di banda dello spazio di archiviazione, non è vero?)

Di seguito sono elencati i dettagli di questo tipo di distribuzione: <https://msdn.microsoft.com/library/dn385720.aspx>
 
Per archiviare i file di dati di SQL Server direttamente sullo spazio di archiviazione di Azure Premium, è necessario disporre di una versione di patch di SQL Server 2014 minima che descritte di seguito: <https://support.microsoft.com/kb/3063054>. L'archiviazione dei file di dati di SQL Server in archiviazione Standard Azure funziona con la versione di SQL Server 2014. Tuttavia, le stesse patch contengano un'altra serie di correzioni rendere più affidabile utilizzo diretto di archiviazione Blob Azure per il file di dati di SQL Server e backup. Si consiglia di utilizzare queste patch in generale.

### <a name="sql-server-2014-buffer-pool-extension"></a>Estensione del Pool di Buffer SQL Server 2014
SQL Server 2014 introdotta una nuova caratteristica denominata Buffer Pool di estensione. Questa funzionalità estende pool di buffer di SQL Server che si trova in memoria con una seconda cache Livella supportato da SSDs locale di un server o macchine Virtuali. In questo modo per mantenere un maggiore working set di dati 'in memoria'. Confrontate con quelle di accesso all'archiviazione Standard Azure l'accesso nell'estensione del pool di buffer a cui è archiviato in locale SSDs di una macchina virtuale Azure è più velocemente diversi fattori.  Di conseguenza, sfruttando nell'unità locale D:\ dei tipi di macchine Virtuali contenenti eccellente secondo e la velocità effettiva potrebbe essere un modo molto conveniente per ridurre il carico IOPS contro lo spazio di archiviazione di Azure e migliorare i tempi di risposta di query in modo significativo. Si applica in particolare se non si usa lo spazio di archiviazione Premium. In caso di Premium lo spazio di archiviazione e l'utilizzo della Cache di lettura di Azure Premium sul nodo di calcolo, come consigliato per i file di dati, grandi differenze non previsto. Motivo è che entrambe le cache (estensione del Pool di Buffer di SQL Server e Cache di lettura dello spazio di archiviazione Premium) utilizza dischi locali dei nodi di elaborazione.
Per ulteriori informazioni su questa funzionalità, vedere questa documentazione: <https://msdn.microsoft.com/library/dn133176.aspx> 

### <a name="backuprecovery-considerations-for-sql-server"></a>Considerazioni sulla copia di backup o ripristino per SQL Server
Durante la distribuzione di SQL Server in Azure necessario esaminare la metodologia di backup. Anche se il sistema non è un sistema produttività, SAP backup del database ospitato da SQL Server deve essere periodicamente. Poiché lo spazio di archiviazione di Azure conserva tre immagini, una copia di backup è ora meno importante riguardo ai compensatori un arresto anomalo dello spazio di archiviazione. Il motivo per priorità per la gestione di un piano di backup e ripristino appropriato è più possibile compensa logico/manuale errori fornendo punto di funzionalità di ripristino di tempo. In modo che l'obiettivo è uno dei due backup di utilizzo per ripristinare il database a un certo punto nel tempo o usare i backup in Azure da un altro sistema copiando il database esistente. Ad esempio, potrebbe trasferire da una configurazione SAP di livello 2 per la configurazione di sistema di livello 3 dello stesso sistema tramite il ripristino di una copia di backup.

Esistono tre modi diversi a backup SQL Server per lo spazio di archiviazione di Azure:
 
1. SQL Server 2012 CU4 database e versioni successive può a livello nativo backup a un URL. Questo dettagliate sulle [nuove funzionalità di SQL Server 2014-parte 5-Backup e ripristino miglioramenti](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx)di blog. Vedere capitolo [SQL Server 2012 SP1 CU4 e versioni successive] [5.5.1 di Guida dbms].
1. Versioni di SQL Server prima di SQL 2012 CU4 possono utilizzare la funzionalità di reindirizzamento per eseguire il backup di un disco rigido virtuale e in cui sono spostare il flusso di scrittura verso un percorso di archiviazione di Azure che è stato configurato. Vedere capitolo [SQL Server 2012 SP1 CU3 e versioni precedenti] [5.5.2 di Guida dbms].
1. Il metodo finale consiste nell'eseguire un backup di SQL Server convenzionale al comando disco in un dispositivo disco disco rigido virtuale.  Questo è uguale al modello di distribuzione in locale e non viene descritto in modo dettagliato in questo documento.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 e versioni successive
Questa funzionalità consente di backup direttamente a archiviazione BLOB Azure. Senza questo metodo, è necessario eseguire il backup di altri dischi rigidi virtuali Azure che occupano capacità disco rigido virtuale e IOPS. L'idea è questa:
 
 ![Utilizzo di Backup di SQL Server 2012 a Microsoft Azure archiviazione BLOB][dbms-guide-figure-400]

Il vantaggio in questo caso è che non deve dedicare dischi rigidi virtuali per archiviare i backup di SQL Server in. Pertanto si dispone di un numero minore dischi rigidi virtuali allocati e la larghezza di banda intero di VHD IOPS può essere usata per i file di dati e di log. Si noti che le dimensioni massime di una copia di backup sono limitata a un massimo di 1 TB come descritto nella sezione "Limitazioni" in questo articolo: <https://msdn.microsoft.com/library/dn435916.aspx#limitations>. Se le dimensioni di backup, nonostante l'utilizzo di Backup di SQL Server compressione supera 1 TB di modificare le dimensioni, le funzionalità descritte in capitolo [SQL Server 2012 SP1 CU3 e versioni precedenti] [dbms-Guida-5.5.2] in questo documento deve essere utilizzato.

[Documentazione correlata](https://msdn.microsoft.com/library/dn449492.aspx) che descrive il ripristino del database da un backup archivio Blob Azure è consigliabile non ripristinare direttamente dall'archivio BLOB Azure se i backup > 25 GB. Considerazioni relative alle prestazioni e non a causa di limitazioni funzionali, si basa semplicemente il suggerimento in questo articolo. Di conseguenza, diverse condizioni possono applicare alla scala cronologica del caso.

Documentazione sulla modalità di configurare e utilizzare questo tipo di backup sono disponibili in [questa](https://msdn.microsoft.com/library/dn466438.aspx) esercitazione
 
Esempio di sequenza di passaggi può essere letti [qui](https://msdn.microsoft.com/library/dn435916.aspx).

Per creare i backup, è di massima priorità per assicurarsi che i BLOB per ogni backup sono denominati in modo leggermente diverso. In caso contrario verranno sovrascritti e la catena di ripristino viene interrotta.
 
Per poter non confondere operazioni tra i 3 diversi tipi di backup, è consigliabile creare diversi contenitori sotto l'account di archiviazione utilizzato per i backup. I contenitori possono essere solo da macchine Virtuali o in base al tipo di Backup e macchine Virtuali. Lo schema potrebbe aspetto:
 
 ![Utilizzo di Backup di SQL Server 2012 a Microsoft Azure lo spazio di archiviazione BLOB: diversi contenitori account lo spazio di archiviazione separata][dbms-guide-figure-500]

Nell'esempio precedente, i backup non verrà eseguiti nello stesso account di archiviazione in cui sono distribuite macchine virtuali. Non vi sarà un nuovo account di archiviazione in particolare per i backup. All'interno di account di archiviazione, non vi sarà creati con una matrice del tipo di backup e il nome di macchine Virtuali di contenitori diversi. Ad esempio segmentazione semplifica consente di gestire i backup di macchine virtuali diverse.

I BLOB uno scrive direttamente il backup, non viene aggiunto al numero di dischi rigidi virtuali di una macchina virtuale. Pertanto uno potrebbe aumentare il numero massimo di dischi rigidi virtuali installati di SKU di VM specifico per i dati e transazione accedere file ed eseguire sempre una copia di backup rispetto a un contenitore di archiviazione. 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 e versioni precedenti
Il primo passaggio da eseguire per ottenere una copia di backup direttamente sull'archiviazione Azure, è possibile scaricare il file msi collegato [in questo](https://www.microsoft.com/download/details.aspx?id=40740) articolo KBA.
 
File di installazione di download di x64 e la documentazione. Il file verrà installato un programma denominato: 'Microsoft SQL Server Backup a Microsoft Azure strumento'. Leggere con attenzione la documentazione del prodotto.  Lo strumento funziona sostanzialmente nel modo seguente:

* Dal lato SQL Server è definito un percorso su disco per il backup di SQL Server (non utilizzare l'unità D:\ per questo).
* Lo strumento consentono di definire regole che possono essere utilizzate per indirizzare diversi tipi di backup per i contenitori di archiviazione Azure diversi.
* Una volta le regole, lo strumento reindirizza il flusso di scrittura del backup a uno dei dischi rigidi virtuali/dischi alla posizione di archiviazione di Azure che è stata definita in precedenza.
* Lo strumento verrà lasciato backup un file di piccole stub di alcuni KB dimensioni sul disco rigido virtuale/disco che è stato definito per SQL Server. **Il file deve essere lasciato nel percorso di archiviazione dal momento che è necessario ripristinare nuovamente dallo spazio di archiviazione Azure.**
    * Se si hanno più file stub (ad esempio attraverso perdita del supporto di archiviazione contenente il file stub) e si sceglie l'opzione di backup in un account di Microsoft Azure archiviazione, è possibile recuperare file stub tramite lo spazio di archiviazione di Microsoft Azure scaricandola dal contenitore dello spazio di archiviazione in cui è stato inserito. È quindi necessario inserire il file stub in una cartella nel computer locale in cui lo strumento è configurato per rilevare e caricare allo stesso contenitore con la stessa password di crittografia se è stata utilizzata la crittografia con la regola originale. 

Questo errore indica che lo schema come descritto in precedenza per le versioni più recenti di SQL Server è possibile attivare posizione, nonché di versioni di SQL Server che non si consente l'indirizzo di accesso diretto una posizione di archiviazione Azure.
 
Questo metodo non dovrebbe essere usato con versioni più recenti di SQL Server che supportano il backup a livello nativo contro lo spazio di archiviazione di Azure. Le eccezioni sono nel punto in cui le limitazioni di backup nativo in Azure sono bloccando l'esecuzione di backup nativo in Azure.

#### <a name="other-possibilities-to-backup-sql-server-databases"></a>Altre possibilità di backup database di SQL Server
Altre possibilità per i database di backup consiste nell'allegare dischi rigidi virtuali aggiuntivi a una macchina virtuale che consente di archiviare i backup in. In questo caso è necessario assicurarsi che i dischi rigidi virtuali non sono in esecuzione completi. In tal caso, sarà necessario disinstallare il disco rigido virtuale per così dire 'archiviarlo' e sostituirlo con un nuovo disco rigido virtuale vuoto. Se si passa verso il basso il percorso, si desidera mantenere questi dischi rigidi virtuali in account di archiviazione Azure separati da quelli che dischi rigidi virtuali con i file di database.

La seconda possibilità consiste nell'usare una macchina virtuale di grandi dimensioni contenenti molti dischi rigidi virtuali associati. Ad esempio D14 con 32VHDs. Utilizzare gli spazi di archiviazione per creare un ambiente flessibile nel punto in cui è possibile creare azioni che vengono utilizzate quindi come destinazioni di backup per il server di DBMS diverso.
 
Se si ha descritte alcune procedure consigliate [qui](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx) anche. 

#### <a name="performance-considerations-for-backupsrestores"></a>Considerazioni sulle prestazioni per l'esecuzione di backup/Ripristina
Come privo distribuzioni, le prestazioni di backup e ripristino dipende del numero di volumi può essere letti in parallelo e la velocità di tali volumi potrebbe. Inoltre, l'utilizzo della CPU usata da compressione backup potrebbe riprodurre un ruolo significativo in macchine virtuali con solo fino a 8 thread di CPU. Di conseguenza, uno presuppongono:

* Minore è il numero di dischi rigidi virtuali usata per archiviare i dati di file, minore velocità complessiva in lettura.
* Minore che il numero di CPU thread in macchine Virtuali, più gravi l'impatto di compressione backup.
* Obiettivi meno (BLOB o dischi rigidi virtuali) per scrivere il backup, il valore più basso la produttività.
* Minore la macchina virtuale la dimensione, minore la velocità quota di archiviazione scrivere e leggere dallo spazio di archiviazione Azure. Indipendentemente dal se i backup direttamente sono archiviati nel Blob Azure o se vengono archiviati in dischi rigidi virtuali nuovamente archiviati nei blob Azure.

Quando si usa Microsoft Azure lo spazio di archiviazione BLOB come destinazione di backup in versioni più recenti, sono limitate a designare un solo destinazione URL per ogni backup specifico.
 
Ma quando si utilizza il 'Microsoft SQL Server Backup a Microsoft Azure strumento' nelle versioni precedenti, è possibile definire più di una destinazione di file. Con più di una destinazione, il backup è possibile ridimensionare e la velocità del backup è superiore. In questo modo quindi in più file anche l'account di archiviazione Azure. Nel testing, con più destinazioni file uno sicuramente possibile ottenere la velocità che uno Impossibile raggiungere con le estensioni backup implementata in da SQL Server 2012 SP1 CU4 in. Inoltre non vengono bloccati, il limite di 1TB come backup nativo in Azure.

Tuttavia, tenere presente, la velocità anche dipende dalla posizione dell'Account di archiviazione Azure utilizzare per il backup. Potrebbe essere un'idea di individuare l'account di archiviazione in un paese diverso rispetto a macchine virtuali sono in esecuzione in. Ad esempio eseguire la configurazione di macchine Virtuali in Europa occidentale, ma si inserire l'Account di archiviazione che consente di eseguire il backup verifica in Nord Europa. Che sicuramente avrà effetto sulla velocità backup e non possono generare una velocità di 150MB/sec come sembra che sia possibile nei casi in cui lo spazio di archiviazione di destinazione e macchine virtuali sono in esecuzione in Data Center locale stesso.

#### <a name="managing-backup-blobs"></a>Gestione BLOB di Backup
È necessario per gestire i backup autonomamente. Poiché si prevede che molti BLOB verrà creato mediante l'esecuzione di backup del registro delle transazioni frequenti, amministrazione di tali BLOB facilmente possibile sovraccaricare il portale di Azure. È recommendable per utilizzare al meglio una finestra di esplorazione di spazio di archiviazione di Azure. Esistono diverse quelli buoni disponibile che consentono di gestire un account di archiviazione Azure

* Microsoft Visual Studio con Azure SDK installato (<https://azure.microsoft.com/downloads/>)
* Esplora archivi di Microsoft Azure (<https://azure.microsoft.com/downloads/>)
* 3 ° strumenti di terze parti

[commento]: <>  (Non supportato su ARM)
[commento]: <>  (# # # Backup macchine Virtuali di azure)
[commento]: <>  (Macchine virtuali all'interno del sistema SAP possibile backup utilizzando la funzionalità di Azure macchina virtuale Backup. Azure macchina virtuale Backup ottenuto introdotti all'inizio dell'anno 2015 e nel frattempo è un metodo standard per il backup di una macchina virtuale completa in Azure. Eseguire il Backup Azure archivia le copie di backup in Azure e consente il ripristino di una macchina virtuale.) 
[commento]: <> (macchine virtuali che fase possano backup database in modo coerente se i sistemi DBMS supporta VSS di Windows (ombreggiatura servizio Copia del Volume - <https://msdn.microsoft.com/library/windows/desktop/bb968832.aspx>) come ad esempio SQL Server. Per consentire l'utilizzo del backup macchine Virtuali di Azure potrebbe essere un modo per ottenere una copia di backup ripristinabile di un database SAP. Tuttavia, tenere presente che in base a backup macchine Virtuali di Azure consente di ripristinare in un momento di database non è possibile. Pertanto, il suggerimento consiste nell'eseguire il backup dei database con funzionalità DBMS anziché ricorrere a Backup macchine Virtuali di Azure.) [commento]: <> (per acquisire familiarità con Azure macchina virtuale Backup, iniziare da qui <https://azure.microsoft.com/documentation/services/backup/>)

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Utilizzo di un immagini di SQL Server disconnettersi da Microsoft Azure Marketplace
Microsoft offre macchine virtuali di Azure Marketplace che contengano già versioni di SQL Server. Per i clienti SAP che richiedono una licenza per SQL Server e Windows, potrebbe essere un'opportunità per coprire in pratica la necessità di licenze da utilizzare macchine virtuali con SQL Server già installato. Per utilizzare tali immagini per SAP, è necessario apportare tenendo presente quanto segue:

* Le versioni di valutazione non SQL Server acquisiscono costi più elevati rispetto a solo un 'Windows sola' macchine Virtuali distribuito da Azure Marketplace. Vedere gli articoli seguenti per confrontare i prezzi: <https://azure.microsoft.com/pricing/details/virtual-machines/> e <https://azure.microsoft.com/pricing/details/virtual-machines/#Sql>. 
* È possibile utilizzare solo versioni di SQL Server supportati da SAP, ad esempio SQL Server 2012.
* Le regole di confronto dell'istanza di SQL Server installato in macchine virtuali presenti Azure Marketplace non è il confronto che SAP NetWeaver richiede l'istanza di SQL Server per l'esecuzione. È possibile modificare le regole di confronto anche se con le istruzioni nella sezione seguente.

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>La modifica del confronto di SQL Server di Microsoft Windows/SQL Server macchine Virtuali
Poiché le immagini di SQL Server Azure Marketplace non siano configurate per utilizzare le regole di confronto che è necessario per le applicazioni SAP NetWeaver, è necessario modificare immediatamente dopo la distribuzione. Per SQL Server 2012, a tale scopo con la procedura seguente non appena la macchina virtuale è stata distribuita e un amministratore è in grado di accedere a macchina virtuale distribuita:

* Aprire una finestra di comando di Windows 'come amministratore'.
* Passare alla directory c:\Programmi\Microsoft c:\Programmi\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012.
* Eseguire il comando: /ACTION /QUIET Setup.exe = REBUILDDATABASE /INSTANCENAME = MSSQLSERVER /SQLSYSADMINACCOUNTS =`<local_admin_account_name`> /SQLCOLLATION = SQL_Latin1_General_Cp850_BIN2   
    * `<local_admin_account_name`> è l'account che è stato definito come account di amministratore per la distribuzione di macchina virtuale per la prima volta attraverso la raccolta.

Il processo di operazione può richiedere alcuni minuti. Per assicurarsi che il passaggio finisce con il risultato corretto, effettuare le operazioni seguenti:

* Aprire SQL Server Management Studio.
* Aprire una finestra di Query.
* Eseguire sp_helpsort comando nel database master SQL Server.

Il risultato desiderato dovrebbe essere simile:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Se il risultato non, interrompere la distribuzione di SAP e provare a utilizzare perché il comando di installazione non risolvono come previsto. Distribuzione di applicazioni SAP NetWeaver su istanza di SQL Server con tabelle codici di SQL Server diverse da quella indicata sopra **non** è supportato.

### <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server ad alta disponibilità per SAP in Azure
Come detto in precedenza, non è possibile creare spazio di archiviazione condivisa necessaria per l'utilizzo delle funzionalità di disponibilità di SQL Server meno recente. Questa funzionalità è necessario installare il due o più istanze di SQL Server in Cluster di Failover di Server un Windows (WSFC) usando un disco condiviso per l'utente database (e infine database temporaneo). Questo è il metodo di disponibilità standard tempo anche supportato da SAP. Poiché Azure non supporta lo spazio di archiviazione condiviso, non è possibile realizzare configurazioni di disponibilità di SQL Server con una configurazione cluster disco condiviso. Tuttavia, molti altri metodi di disponibilità sono comunque e sono descritti nelle sezioni seguenti.

[commento]: <>  (Articolo è ancora fa riferimento a ASM)
[commento]: <>  (Prima di leggere le tecnologie di disponibilità specifiche diversi utilizzabili per SQL Server in Azure, è un ottimo documento che fornisce ulteriori dettagli e puntatori [here][virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions])

#### <a name="sql-server-log-shipping"></a>Distribuzione dei Log di SQL Server
Uno dei metodi di disponibilità (HA) è la distribuzione dei Log di SQL Server. Nel caso macchine virtuali partecipanti nella configurazione HA utilizzo la risoluzione dei nomi, non c'è alcun problema e la configurazione di Azure non sarà diverso da qualsiasi installazione è stata completata locale. Non è consigliabile si basano su solo la risoluzione IP. Per quanto riguarda l'impostazione di distribuzione Log e principi intorno la distribuzione dei Log controllare questa documentazione:

<https://technet.microsoft.com/library/ms187103.aspx>
 
Per ottenere realmente qualsiasi disponibilità, è necessario distribuirle che si trovano all'interno di tale la distribuzione dei Log configurazione all'interno del Set di disponibilità Azure stesso.

#### <a name="database-mirroring"></a>Database che rispecchiano
Mirroring di database in modalità supportata dal SAP (vedere la nota SAP [965908]) si basa sulla definizione di un partner di failover nella stringa di connessione SAP. Per i casi tra locale, si presuppone che due macchine virtuali sono nello stesso dominio e che il contesto dell'utente due istanze di SQL Server sono in esecuzione in sono anche gli utenti del dominio e disporre dei privilegi in due istanze di SQL Server correlate. Di conseguenza, la configurazione del Database che rispecchiano in Azure non si differenziano tra una configurazione tipica locale.

In distribuzioni basata solo su Cloud, il metodo più semplice è che il programma di installazione di un altro dominio nell'Azure che tali macchine virtuali DBMS (e ideale dedicato macchine virtuali di SAP) all'interno di un dominio.

Se un dominio non è possibile, uno inoltre possibile utilizzare i certificati per il database che rispecchiano endpoint come descritto di seguito: <https://technet.microsoft.com/library/ms191477.aspx>

Esercitazione per la configurazione del Database che rispecchiano in Azure sono disponibili qui: <https://technet.microsoft.com/library/ms189852.aspx> 

#### <a name="alwayson"></a>AlwaysOn
Come AlwaysOn è supportata per SAP locale (vedere la nota SAP [1772688]), questa caratteristica è supportata per essere utilizzato in combinazione con SAP in Azure. Il fatto che non si è possibile creare dischi condivisi in Azure non significa che uno non è possibile creare una configurazione AlwaysOn Windows Server Failover Cluster (WSFC) tra macchine virtuali diverse. Solo, significa che non è la possibilità di usare un disco condiviso come base della configurazione del cluster. Pertanto è possibile creare una configurazione AlwaysOn WSFC in Azure e semplicemente non selezionare il tipo di base che utilizza disco condiviso. Ambiente di Azure tali macchine virtuali vengono distribuite deve risolvere macchine virtuali in base al nome e le macchine virtuali dovrebbe essere nello stesso dominio. Questo è valido per Azure solo e distribuzioni locali tra. Esistono alcune considerazioni sulla distribuzione di SQL Server disponibilità gruppo comunicare ascoltatore (da non confondere con il Set di disponibilità Azure) poiché Azure in questo momento non è consentito per creare semplicemente un oggetto di Active Directory/DNS come è possibile in locale. Pertanto sono necessarie per risolvere il comportamento specifico di Azure alcuni passaggi di installazione diversa.

Di seguito sono riportate alcune considerazioni sull'utilizzo di un gruppo di disponibilità comunicare ascoltatore:

* Utilizzo di un gruppo di disponibilità comunicare ascoltatore è possibile solo con Windows Server 2012 o Windows Server 2012 R2 come guest del sistema operativo della macchina virtuale. Per Windows Server 2012 è necessario assicurarsi che sia applicata questa patch: <https://support.microsoft.com/kb/2854082> 
* Per Windows Server 2008 R2 questa patch non esiste e AlwaysOn deve essere utilizzato allo stesso modo di Database che rispecchiano specificando un partner di failover nella stringa di connessioni (eseguite tramite SAP default.pfl parametro dbs/mss/server, vedere la nota SAP [965908]).
* Quando si usa un gruppo di disponibilità comunicare ascoltatore, è necessario essere connessi a un servizio di bilanciamento del carico dedicato macchine virtuali di Database. Risoluzione dei nomi in distribuzioni basata solo su Cloud uno richiedono tutti macchine virtuali di un sistema SAP (server delle applicazioni, server DBMS e (A) SCS) sono nella stessa rete virtuale o richiedono da un livello di applicazione SAP manutenzione del file etc\host per ottenere i nomi di macchine Virtuali le macchine SQL Server risolto. Per evitare che Azure l'assegnazione di nuovi indirizzi IP nei casi in cui sia macchine virtuali sono a proposito arresto, uno necessario assegnare indirizzi IP statici per le interfacce di rete di tali macchine virtuali nella configurazione AlwaysOn (la definizione di un indirizzo IP statico descritto in [questo] [ virtual-networks-reserved-private-ip] articolo) [commento]: <> (blog precedente) [commento]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx> <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* Sono disponibili speciali passaggi necessari per la compilazione della configurazione di cluster WSFC nel punto in cui il cluster richiede un indirizzo IP speciale assegnata, poiché Azure con le funzionalità correnti da assegnare il nome del cluster lo stesso indirizzo IP come il nodo del cluster viene creato in. Di conseguenza, che un passaggio manuale da eseguire per assegnare un indirizzo IP diverso al cluster.
* Comunicare ascoltatore gruppo disponibilità verrà creato in Azure con endpoint TCP/IP che vengono assegnati alle macchine virtuali in esecuzione primari e secondari riproduzioni del gruppo di disponibilità.
* In potrebbe essere necessario per proteggere gli endpoint degli ACL.

[commento]: <>  (Blog vecchio TODO)
[commento]: <>  (La procedura dettagliata e qualsiasi cosa per il di installazione di una configurazione AlwaysOn su Azure si consiglia di utilizzare quando attraverso le esercitazioni disponibili [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[commento]: <>  (Configurazione preconfigurato AlwaysOn tramite la raccolta Azure < https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[commento]: <>  (Creazione di un gruppo di disponibilità comunicare ascoltatore è meglio descritta nell'esercitazione [this][virtual-machines-windows-classic-ps-sql-int-listener])
[commento]: <>  (Endpoint di rete di protezione degli ACL sono stati spiegati meglio qui:)
[commento]: <>  (* < https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[commento]: <>  (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx>)
[commento]: <>  (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[commento]: <>  (* < https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

È possibile distribuire un gruppo di disponibilità AlwaysOn di SQL Server in diverse aree di Azure anche. Questa funzionalità verrà utilizzati la connettività di Azure VNet a Vnet ([ulteriori dettagli][virtual-networks-configure-vnet-to-vnet-connection]).
[commento]: <> (TODO vecchio blog) [commento]: <> (di seguito viene descritto il programma di installazione di disponibilità AlwaysOn di SQL Server in questo caso: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Riepilogo sul disponibilità elevata SQL Server in Azure
Dato che lo spazio di archiviazione di Azure protegge il contenuto, non esiste una meno motivo affermare su un'immagine hot standby. Questo significa che lo scenario di disponibilità deve solo proteggersi dai seguenti casi:

* Indisponibilità della macchina virtuale nel suo insieme a causa di manutenzione nel server cluster in Azure o altri motivi
* Problemi relativi al software dell'istanza di SQL Server
* Impedire l'errore manuale nel punto in cui i dati vengono eliminati e ripristino in un momento è necessaria

Esamina tecnologie corrispondente uno potrà dire che i primi due casi possono essere coperta dal Database che rispecchiano o AlwaysOn, mentre il terzo caso può essere incluso solo nella distribuzione dei Log.

È necessario bilanciare la configurazione più complessa di AlwaysOn confrontate con quelle di Database che rispecchiano, con i vantaggi del AlwaysOn. Elencare i vantaggi come:

*   Repliche secondarie leggibile.
*   Backup dalle repliche secondarie.
*   Migliore scalabilità.
*   Più di una repliche secondario.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>SQL Server generale per SAP riepilogo Azure
Sono disponibili numerosi consigli in questa guida si consiglia di leggerlo più volte prima di pianificare la distribuzione di Azure. In generale, tuttavia, assicurarsi di seguire il DBMS generale dieci superiore su Azure punti specifici:

[commento]: <> velocità  (2.3 maggiore rispetto a quanto? Rispetto a un disco rigido virtuale?)
1. Utilizzare l'ultima versione DBMS, ad esempio SQL Server 2014, che presenta la maggior parte dei vantaggi in Azure. Per SQL Server, si tratta di SQL Server 2012 SP1 CU4 che comprende la funzionalità di backup e archiviazione Azure. Tuttavia, in combinazione con SAP sarebbe consigliabile almeno CU1 SP1 2014 di SQL Server o SQL Server 2012 SP2 e CU più recente.
1. Pianificare con attenzione l'orizzontale di sistema SAP in Azure per bilanciare il layout di file di dati e Azure restrizioni:
    * Non dispone di un numero eccessivo dischi rigidi virtuali, ma è sufficiente per assicurarsi che è possibile contattare il IOPS richiesto.
    * Tenere presente che IOPS anche sono limitate per conto di spazio di archiviazione di Azure e che gli account di archiviazione sono limitati all'interno di ciascuna sottoscrizione Azure ([ulteriori dettagli][azure-subscription-service-limits]). 
    * Solo strisce tra dischi rigidi virtuali se è necessario ottenere una maggiore produttività.
1. Non è mai installare il software o spostare i file che richiedono la persistenza sull'unità D:\ mentre è non permanente e qualsiasi sull'unità andranno perso in riavviare il computer Windows.
1. Non usare disco rigido virtuale Azure memorizzazione nella cache per l'archiviazione Standard Azure.
1. Non usare Azure replicato geografico gli account di archiviazione.  Utilizzare localmente ridondanti per carichi di lavoro DBMS.
1. Soluzione HA/DR del fornitore DBMS replicare dati del database.
1. Sempre la risoluzione dei nomi, non si basano su indirizzi IP.
1. Utilizzare la massima compressione di database possibili. Per SQL Server è la compressione page.
1. Prestare attenzione con immagini di SQL Server Azure Marketplace. Se si utilizza SQL Server uno, è necessario modificare le regole di confronto istanza prima di installare qualsiasi sistema SAP NetWeaver su di esso.
1. Installare e configurare il monitoraggio di Host SAP per Azure come descritto in [Guida alla distribuzione] [Guida alla distribuzione].

## <a name="specifics-to-sap-ase-on-windows"></a>Dettagli ASE SAP in Windows
A partire da Microsoft Azure, è possibile migrare le applicazioni SAP ASE esistenti in macchine virtuali di Azure. ASE SAP in un computer virtuale consente di ridurre il costo totale di proprietà di distribuzione, gestione e la manutenzione di applicazioni aziendali ampiezza facilmente la migrazione di queste applicazioni di Microsoft Azure. Con ASE SAP in una macchina virtuale Azure, amministratori e sviluppatori possono utilizzare la stessa sviluppo e gli strumenti di amministrazione che sono disponibili in locale.

Esiste un contratto di servizio per macchine virtuali Azure che sono disponibili qui: <https://azure.microsoft.com/support/legal/sla>

Siamo certi che Microsoft Azure macchine virtuali ospitato eseguirà ben rispetto a quando altre offerte di virtualizzazione cloud pubblico, ma i singoli risultati possono variare. SAP dimensionamento SAP numeri di SAP diversi certified che SKU macchine Virtuali saranno forniti in una nota SAP separata [1928533].

Istruzioni e consigli per quanto riguarda l'utilizzo di spazio di archiviazione di Azure, distribuzione di macchine virtuali SAP o SAP monitoraggio riguardano le distribuzioni di ASE SAP in combinazione con le applicazioni SAP come indicato nel corso dei primi quattro capitoli di questo documento.

### <a name="sap-ase-version-support"></a>Supporto delle versioni ASE SAP 
SAP attualmente supporta SAP ASE versione 16.0 per l'uso con i prodotti di SAP Business Suite. Tutti gli aggiornamenti per ASE SAP server o driver ODBC e JDBC da utilizzare con i prodotti di SAP Business Suite vengono forniti esclusivamente tramite il sito Marketplace servizio di SAP: <https://support.sap.com/swdc>.

Per le installazioni locali, non scaricare gli aggiornamenti per il server SAP ASE o per i driver ODBC e JDBC direttamente da siti Web di Sybase. Per informazioni dettagliate sulle patch che sono supportati per l'utilizzo con SAP Business Suite prodotti locale e in macchine virtuali di Azure, vedere le note SAP seguenti:

* [1590719]
* [1973241]
 
Informazioni generali sull'esecuzione di SAP Business Suite in SAP ASE disponibili in [Copia](https://scn.sap.com/community/ase)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Istruzioni di configurazione ASE SAP per SAP correlati SAP ASE installazioni in macchine virtuali di Azure

#### <a name="structure-of-the-sap-ase-deployment"></a>Struttura della distribuzione ASE SAP
Secondo descrizione generale eseguibili SAP ASE dovrebbero essere disponibile o installati in unità di sistema del disco rigido virtuale base di Virtual Machine (unità c:\). In genere, la maggior parte dei database di sistema e strumenti ASE SAP non vengono realmente utilizzata definitiva dal carico di lavoro SAP NetWeaver. Di conseguenza i database di sistema e strumenti (schema, modello, saptools, sybmgmtdb, sybsystemdb) possono rimanere sul anche la C:\drive. 

Un'eccezione potrebbe essere il database temporaneo contenente tutte le tabelle di lavoro e le tabelle temporanee create da SAP ASE, che in caso di alcuni ERP SAP e tutti i carichi di bianco e nero potrebbe richiedere maggiore volume di dati o volume di operazioni i/o che non si adattarla disco rigido virtuale base della macchina virtuale originale (unità c:\).
 
A seconda della versione di SAPInst/SWPM usato per installare il sistema, potrebbe contenere il database:

* Un singolo database temporaneo SAP ASE creato durante l'installazione di ASE SAP
* Un tempdb SAP ASE create con l'installazione ASE SAP e un saptempdb aggiuntivi creati da routine di installazione SAP
* Un tempdb SAP ASE create con l'installazione ASE SAP e un database di temporaneo aggiuntivi che sono stati creati manualmente (ad esempio SAP nota [1752266]) per soddisfare le esigenze specifiche tempdb ERP/bianco e nero

In caso di sistemi ERP specifici o tutti i carichi di bianco e nero sia appropriato, per quanto riguarda le prestazioni, per mantenere i dispositivi di tempdb del database temporaneo inoltre creato (tramite SWPM o manualmente) in un'unità diversa da quella c:\. esistenza non tempdb aggiuntive è consigliabile creare uno (nota SAP [1752266]).

Per tali sistemi la procedura seguente deve essere eseguita per il database temporaneo inoltre creato:

* Spostare il dispositivo tempdb prima il dispositivo prima del database SAP
* Aggiungere dispositivi tempdb a ognuno dei dischi rigidi virtuali contenente un dispositivi del database SAP

Questa configurazione consente tempdb per l'utilizzo di più spazio di unità di sistema in grado di fornire. Come riferimento una possibile controllare le dimensioni del dispositivo tempdb sui sistemi esistenti eseguiti in locale. O questa configurazione consentirebbe numeri IOPS rispetto a tempdb che non può essere fornito con l'unità di sistema. Nuovamente sistemi che eseguono locale possono essere utilizzati per monitorare carico di lavoro i/o per tempdb.

Non inserire mai eventuali dispositivi ASE SAP in unità D:\ della macchina virtuale. Questo vale anche per il database temporaneo, anche se gli oggetti di permanenza nel database temporaneo solo temporanei.

#### <a name="impact-of-database-compression"></a>Impatto della compressione di Database
In configurazioni nel punto in cui la larghezza di banda i/o può diventare un fattore di limitazione, tutte le misure riducendo IOPS potrebbe essere utile per estendere il carico di lavoro sarà possibile eseguire in uno scenario di IaaS come Azure. Di conseguenza, è consigliabile per assicurarsi che la compressione ASE SAP viene utilizzata prima di caricare un database esistente SAP in Azure.

Suggerimento per eseguire la compressione prima di caricare in Azure se non è già stato implementato è dato dalla diversi motivi:

* La quantità di dati da caricare in Azure è inferiore
* La durata dell'esecuzione compressione è inferiore presupponendo che è possibile utilizzare hardware più complessa con più CPU o larghezza di banda i/o superiore o inferiore i/o latenza locale
* Dimensioni database inferiori possano essere meno i costi per l'allocazione disco

Compressione dei dati e line lavorare in una macchina virtuale ospitata in macchine virtuali di Azure, come avviene in locale. Per ulteriori informazioni su come verificare se la compressione è già in uso in un database esistente SAP ASE selezionare Nota SAP [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilizzo di DBACockpit per controllare le istanze di Database
Per sistemi SAP che utilizzano SAP ASE come piattaforma di database, è possibile accedere come finestre del browser incorporato in transazione DBACockpit o Webdynpro il DBACockpit. Tuttavia, tutte le funzionalità di monitoraggio e amministrazione del database sono disponibile solo il DBACockpit per attuare Webdynpro.

Come con i sistemi locali diversi passaggi necessari per abilitare tutte le funzionalità di SAP NetWeaver utilizzata dall'implementazione Webdynpro del DBACockpit. Seguire nota SAP [1245200] per abilitare l'utilizzo della webdynpros e generare quelli necessari. Quando seguendo le istruzioni visualizzate nelle note in precedenza si configura anche il responsabile di comunicazioni Internet (colori) insieme a porte da utilizzare per le connessioni http e https. L'impostazione predefinita per http è simile alla seguente:

> colori/server_port_0 = protocollo HTTP, porta = = 8000, PROCTIMEOUT = 600, TIMEOUT = 600
>
> colori/server_port_1 = protocollo = HTTPS, porta 443$ $, PROCTIMEOUT = = 600, TIMEOUT = 600

e i collegamenti generati transazione DBACockpit sarà simile alla seguente:

> https://`<fullyqualifiedhostname`>: 44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>: 8000/sap/bc/webdynpro/sap/dba_cockpit

A seconda se e come la macchina virtuale Azure hosting sistema SAP è connesso tramite da sito, più siti o ExpressRoute (distribuzione locale di croce) è necessario assicurarsi che colori sta utilizzando un nome host completo che possono essere risolte nel computer in cui si sta tentando di aprire DBACockpit da. Vedere nota SAP [773830] per capire come colori determinano il nome host completo a seconda di parametri del profilo e impostare in modo esplicito parametro colori/host_name_full se necessario.

Se è stata distribuita la macchina virtuale in uno scenario basata solo su Cloud senza connessione tra locale tra locale e Azure, è necessario definire un indirizzo IP pubblico e un domainlabel. Il formato del nome del DNS pubblico della macchina virtuale quindi aspetto simile al seguente:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com

Sono disponibili ulteriori dettagli relativi al nome DNS [qui][virtual-machines-azurerm-versus-azuresm].

Impostazione SAP profilo parametro colori/host_name_full al nome DNS della macchina virtuale Azure il collegamento può essere simile a:

> https://mydomainlabel.westeurope.cloudapp.NET:44300/sap/bc/webdynpro/sap/dba_cockpit

> http://mydomainlabel.westeurope.cloudapp.NET:8000/sap/bc/webdynpro/sap/dba_cockpit

In questo caso è necessario assicurarsi di:

* Aggiungere le regole in entrata al gruppo di sicurezza di rete nel portale di Azure per le porte TCP/IP utilizzate per comunicare con colori
* Aggiungere le regole in entrata alla configurazione di Windows Firewall per le porte TCP/IP utilizzati per comunicare con i colori

Per un processo automatico importati di tutte le correzioni disponibili, è consigliabile applicare periodicamente l'insieme di correzione nota SAP applicabile alla versione SAP:

* [1558958]
* [1619967]
* [1882376]

Ulteriori informazioni sul pannello di controllo per amministratori di database per SAP ASE disponibili nelle note SAP seguenti:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496] 
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerazioni sulla copia di backup o ripristino per ASE SAP
Quando si distribuisce ASE SAP in Azure deve essere rivisti la metodologia di backup. Anche se il sistema non è un sistema produttività, SAP backup del database ospitato da SAP ASE deve essere periodicamente. Poiché lo spazio di archiviazione di Azure conserva tre immagini, una copia di backup è ora meno importante riguardo ai compensatori un arresto anomalo dello spazio di archiviazione. Il motivo principale per la gestione di un piano di backup e ripristino appropriato è più possibile compensa logico/manuale errori fornendo punto di funzionalità di ripristino di tempo. In modo che l'obiettivo è uno dei due backup di utilizzo per ripristinare il database a un certo punto nel tempo o usare i backup in Azure da un altro sistema copiando il database esistente. Ad esempio, potrebbe trasferire da una configurazione SAP di livello 2 per la configurazione di sistema di livello 3 dello stesso sistema tramite il ripristino di una copia di backup.

Backup e ripristino di un database in Azure avviene nello stesso modo locale. Vedere le note SAP:

* [1588316]
* [1585981]

Per informazioni dettagliate sulle configurazioni di immagine della creazione e programmazione backup. In base al strategia ed esigenze è possibile configurare database e log trasferisce su disco su uno dei dischi rigidi virtuali di esistenti o aggiungere un disco rigido virtuale aggiuntivo per il backup.  Per ridurre i rischi di perdita di dati in caso di errore che si consiglia di utilizzare un disco rigido virtuale senza dispositivo di database in cui si trova.

Oltre ai dati e line compressione SAP ASE offre anche la compressione backup. In modo da occupare meno spazio con le immagini di database e di log è consigliabile utilizzare la compressione backup. Vedere la nota SAP [1588316] per ulteriori informazioni. Comprimere il backup è fondamentale per ridurre la quantità di dati da trasferire se si prevede di scaricare backup o dischi rigidi virtuali contenente immagini backup da Azure Virtual Machine nella distribuzione locale.

Non utilizzare unità D:\ come database o log dettagli destinazione.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerazioni sulle prestazioni per l'esecuzione di backup/Ripristina
Come privo distribuzioni, le prestazioni di backup e ripristino dipende del numero di volumi può essere letti in parallelo e la velocità di tali volumi potrebbe. Inoltre, l'utilizzo della CPU usata da compressione backup potrebbe riprodurre un ruolo significativo in macchine virtuali con solo fino a 8 thread di CPU. Di conseguenza, uno presuppongono:

* Minore è il numero di dischi rigidi virtuali usata per archiviare i dispositivi di database, minore velocità effettiva complessiva di lettura
* Minore che il numero di CPU thread in macchine Virtuali, più gravi l'impatto di compressione backup
* Riduce le (striscia directory; dischi rigidi virtuali) per scrivere il backup, il valore più basso la produttività

Per aumentare il numero di destinatari per scrivere che sono disponibili due opzioni che può essere utilizzato/combinati a seconda delle esigenze:

* Striping il volume di destinazione di backup su più dischi rigidi virtuali collegati al fine di migliorare la produttività IOPS su tale volume strisce
* Creazione di una configurazione dettagli a livello di ASE SAP che utilizza più di una directory di destinazione per scrivere i dettagli per

Striping un volume su più dischi rigidi virtuali collegati sono state illustrate in precedenza in questa Guida. Per ulteriori informazioni sull'uso di più directory nella configurazione dettagli ASE SAP fare riferimento alla documentazione sulla sp_config_dump Stored Procedure che viene utilizzato per creare la configurazione di dettagli nel [Centro informazioni per Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Ripristino di emergenza con macchine virtuali di Azure

#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replica dei dati con Server di replica di Sybase SAP
Con la ASE SAP SAP Sybase Server di replica fornisce una soluzione di riserva calda trasferire le transazioni del database in modo asincrono a una posizione distante. 

Installazione e il funzionamento di SRS come si ottengono buoni risultati funzionale in una macchina virtuale ospitata in servizi di macchina virtuale di Windows Azure, come avviene in locale.

HADR ASE tramite il Server di replica SAP è prevista con una versione futura. Verranno testato con e rilasciato per le piattaforme di Microsoft Azure non appena disponibile.

## <a name="specifics-to-sap-ase-on-linux"></a>Dettagli ASE SAP su Linux

A partire da Microsoft Azure, è possibile migrare le applicazioni SAP ASE esistenti in macchine virtuali di Azure. ASE SAP in un computer virtuale consente di ridurre il costo totale di proprietà di distribuzione, gestione e la manutenzione di applicazioni aziendali ampiezza facilmente la migrazione di queste applicazioni di Microsoft Azure. Con ASE SAP in una macchina virtuale Azure, amministratori e sviluppatori possono utilizzare la stessa sviluppo e gli strumenti di amministrazione che sono disponibili in locale.

Per la distribuzione di macchine virtuali di Azure, è importante conoscere i contratti di servizio ufficiale che può essere trovati qui: <https://azure.microsoft.com/support/legal/sla>

Informazioni sul ridimensionamento SAP e un elenco di SAP certificata SKU macchine Virtuali verrà illustrate nella nota SAP [1928533]. Altri SAP ridimensionamento documenti per macchine virtuali di Azure sono disponibili qui di seguito, <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Istruzioni e consigli per quanto riguarda l'utilizzo di spazio di archiviazione di Azure, distribuzione di macchine virtuali SAP o SAP monitoraggio riguardano le distribuzioni di ASE SAP in combinazione con le applicazioni SAP come indicato nel corso dei primi quattro capitoli di questo documento.

Le due note SAP seguenti includono informazioni generali su ASE Linux e ASE nel Cloud:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Supporto delle versioni ASE SAP 
SAP attualmente supporta SAP ASE versione 16.0 per l'uso con i prodotti di SAP Business Suite. Tutti gli aggiornamenti per ASE SAP server o driver ODBC e JDBC da utilizzare con i prodotti di SAP Business Suite vengono forniti esclusivamente tramite il sito Marketplace servizio di SAP: <https://support.sap.com/swdc>.

Per le installazioni locali, non scaricare gli aggiornamenti per il server SAP ASE o per i driver ODBC e JDBC direttamente da siti Web di Sybase. Per informazioni dettagliate sulle patch che sono supportati per l'utilizzo con SAP Business Suite prodotti locale e in macchine virtuali di Azure, vedere le note SAP seguenti:

* [1590719]
* [1973241]
 
Informazioni generali sull'esecuzione di SAP Business Suite in SAP ASE disponibili in [Copia](https://scn.sap.com/community/ase)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Istruzioni di configurazione ASE SAP per SAP correlati SAP ASE installazioni in macchine virtuali di Azure

#### <a name="structure-of-the-sap-ase-deployment"></a>Struttura della distribuzione ASE SAP
Secondo descrizione generale eseguibili ASE SAP da disponibile o installati nel file system radice della macchina virtuale (/sybase). In genere, la maggior parte dei database di sistema e strumenti ASE SAP non vengono realmente utilizzata definitiva dal carico di lavoro SAP NetWeaver. Di conseguenza i database di sistema e strumenti (schema, modello, saptools, sybmgmtdb, sybsystemdb) possono rimanere nel sistema di file principale. 

Un'eccezione potrebbe essere il database temporaneo contenente tutte le tabelle di lavoro e le tabelle temporanee create da SAP ASE, che in caso di alcuni ERP SAP e tutti i carichi di bianco e nero potrebbe richiedere maggiore volume di dati o volume di operazioni i/o che non si adattarla disco rigido del sistema operativo della macchina virtuale originale.
 
A seconda della versione di SAPInst/SWPM usato per installare il sistema, potrebbe contenere il database:

* Un singolo database temporaneo SAP ASE creato durante l'installazione di ASE SAP
* Un tempdb SAP ASE create con l'installazione ASE SAP e un saptempdb aggiuntivi creati da routine di installazione SAP
* Un tempdb SAP ASE create con l'installazione ASE SAP e un database di temporaneo aggiuntivi che sono stati creati manualmente (ad esempio SAP nota [1752266]) per soddisfare le esigenze specifiche tempdb ERP/bianco e nero

In caso di sistemi ERP specifici o tutti i carichi di bianco e nero sia appropriato, per quanto riguarda le prestazioni, per mantenere i dispositivi di tempdb del database temporaneo inoltre creato (tramite SWPM o manualmente) in un file separato che può essere rappresentato da un disco di dati di Azure singola o RAID Linux che interessano più dischi di dati di Azure. Se nessun tempdb aggiuntive è consigliabile crearne uno (nota SAP [1752266]).

Per tali sistemi la procedura seguente deve essere eseguita per il database temporaneo inoltre creato:

* Spostare la directory di tempdb prima nel file System prima del database SAP
* Aggiungere tempdb directory a ognuno dei dischi rigidi virtuali contenente un file System del database SAP

Questa configurazione consente tempdb per l'utilizzo di più spazio di unità di sistema in grado di fornire. Come riferimento una possibile controllare le dimensioni di directory tempdb sui sistemi esistenti eseguiti in locale. O questa configurazione consentirebbe numeri IOPS rispetto a tempdb che non può essere fornito con l'unità di sistema. Nuovamente sistemi che eseguono locale possono essere utilizzati per monitorare carico di lavoro i/o per tempdb.

Non è mai posizionare le directory ASE SAP /mnt o /mnt/resource della macchina virtuale. Questo vale anche per il database temporaneo, anche se gli oggetti di permanenza nel database temporaneo solo temporanei in quanto /mnt o /mnt/resource è uno spazio di temp macchine Virtuali di Azure predefinito che non è persistente. Ulteriori informazioni sullo spazio temp macchine Virtuali di Azure sono disponibili in [questo articolo][virtual-machines-linux-how-to-attach-disk]

#### <a name="impact-of-database-compression"></a>Impatto della compressione di Database
In configurazioni nel punto in cui la larghezza di banda i/o può diventare un fattore di limitazione, tutte le misure riducendo IOPS potrebbe essere utile per estendere il carico di lavoro sarà possibile eseguire in uno scenario di IaaS come Azure. Di conseguenza, è consigliabile per assicurarsi che la compressione ASE SAP viene utilizzata prima di caricare un database esistente SAP in Azure.

Suggerimento per eseguire la compressione prima di caricare in Azure se non è già stato implementato è dato dalla diversi motivi:

* La quantità di dati da caricare in Azure è inferiore
* La durata dell'esecuzione compressione è inferiore presupponendo che è possibile utilizzare hardware più complessa con più CPU o larghezza di banda i/o superiore o inferiore i/o latenza locale
* Dimensioni database inferiori possano essere meno i costi per l'allocazione disco

Compressione dei dati e line lavorare in una macchina virtuale ospitata in macchine virtuali di Azure, come avviene in locale. Per ulteriori informazioni su come verificare se la compressione è già in uso in un database esistente SAP ASE selezionare Nota SAP [1750510]. Vedere anche nota SAP [2121797] per ulteriori informazioni sui database compressione.

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Utilizzo di DBACockpit per controllare le istanze di Database
Per sistemi SAP che utilizzano SAP ASE come piattaforma di database, è possibile accedere come finestre del browser incorporato in transazione DBACockpit o Webdynpro il DBACockpit. Tuttavia, tutte le funzionalità di monitoraggio e amministrazione del database sono disponibile solo il DBACockpit per attuare Webdynpro.

Come con i sistemi locali diversi passaggi necessari per abilitare tutte le funzionalità di SAP NetWeaver utilizzata dall'implementazione Webdynpro del DBACockpit. Seguire nota SAP [1245200] per abilitare l'utilizzo della webdynpros e generare quelli necessari. Quando seguendo le istruzioni visualizzate nelle note in precedenza si configura anche il responsabile di comunicazioni Internet (colori) insieme a porte da utilizzare per le connessioni http e https. L'impostazione predefinita per http è simile alla seguente:

> colori/server_port_0 = protocollo HTTP, porta = = 8000, PROCTIMEOUT = 600, TIMEOUT = 600
>
> colori/server_port_1 = protocollo = HTTPS, porta 443$ $, PROCTIMEOUT = = 600, TIMEOUT = 600

e i collegamenti generati transazione DBACockpit sarà simile alla seguente:

> https://`<fullyqualifiedhostname`>: 44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>: 8000/sap/bc/webdynpro/sap/dba_cockpit

A seconda se e come la macchina virtuale Azure hosting sistema SAP è connesso tramite da sito, più siti o ExpressRoute (distribuzione locale di croce) è necessario assicurarsi che colori sta utilizzando un nome host completo che possono essere risolte nel computer in cui si sta tentando di aprire DBACockpit da. Vedere nota SAP [773830] per capire come colori determinano il nome host completo a seconda di parametri del profilo e impostare in modo esplicito parametro colori/host_name_full se necessario.

Se è stata distribuita la macchina virtuale in uno scenario basata solo su Cloud senza connessione tra locale tra locale e Azure, è necessario definire un indirizzo IP pubblico e un domainlabel. Il formato del nome del DNS pubblico della macchina virtuale quindi aspetto simile al seguente:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com

Sono disponibili ulteriori dettagli relativi al nome DNS [qui][virtual-machines-azurerm-versus-azuresm].

Impostazione SAP profilo parametro colori/host_name_full al nome DNS della macchina virtuale Azure il collegamento può essere simile a:

> https://mydomainlabel.westeurope.cloudapp.NET:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.NET:8000/sap/bc/webdynpro/sap/dba_cockpit

In questo caso è necessario assicurarsi di:

* Aggiungere le regole in entrata al gruppo di sicurezza di rete nel portale di Azure per le porte TCP/IP utilizzate per comunicare con colori
* Aggiungere le regole in entrata alla configurazione di Windows Firewall per le porte TCP/IP utilizzati per comunicare con i colori

Per un processo automatico importati di tutte le correzioni disponibili, è consigliabile applicare periodicamente l'insieme di correzione nota SAP applicabile alla versione SAP:

* [1558958]
* [1619967]
* [1882376]

Ulteriori informazioni sul pannello di controllo per amministratori di database per SAP ASE disponibili nelle note SAP seguenti:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496] 
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerazioni sulla copia di backup o ripristino per ASE SAP
Quando si distribuisce ASE SAP in Azure deve essere rivisti la metodologia di backup. Anche se il sistema non è un sistema produttività, SAP backup del database ospitato da SAP ASE deve essere periodicamente. Poiché lo spazio di archiviazione di Azure conserva tre immagini, una copia di backup è ora meno importante riguardo ai compensatori un arresto anomalo dello spazio di archiviazione. Il motivo principale per la gestione di un piano di backup e ripristino appropriato è più possibile compensa logico/manuale errori fornendo punto di funzionalità di ripristino di tempo. In modo che l'obiettivo è uno dei due backup di utilizzo per ripristinare il database a un certo punto nel tempo o usare i backup in Azure da un altro sistema copiando il database esistente. Ad esempio, potrebbe trasferire da una configurazione SAP di livello 2 per la configurazione di sistema di livello 3 dello stesso sistema tramite il ripristino di una copia di backup.

Backup e ripristino di un database in Azure avviene nello stesso modo locale. Vedere le note SAP:

* [1588316]
* [1585981]

Per informazioni dettagliate sulle configurazioni di immagine della creazione e programmazione backup. In base al strategia ed esigenze è possibile configurare database e log trasferisce su disco su uno dei dischi rigidi virtuali di esistenti o aggiungere un disco rigido virtuale aggiuntivo per il backup.  Per ridurre i rischi di perdita di dati in caso di errore che si consiglia di utilizzare un disco rigido virtuale Nessun directory/file di database in cui si trova.

Oltre ai dati e line compressione SAP ASE offre anche la compressione backup. In modo da occupare meno spazio con le immagini di database e di log è consigliabile utilizzare la compressione backup. Vedere la nota SAP [1588316] per ulteriori informazioni. Comprimere il backup è fondamentale per ridurre la quantità di dati da trasferire se si prevede di scaricare backup o dischi rigidi virtuali contenente immagini backup da Azure Virtual Machine nella distribuzione locale.

Non usare il macchine Virtuali di Azure spazio temp /mnt o /mnt/resource come destinazione di dettagli del database o log.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerazioni sulle prestazioni per l'esecuzione di backup/Ripristina
Come privo distribuzioni, le prestazioni di backup e ripristino dipende del numero di volumi può essere letti in parallelo e la velocità di tali volumi potrebbe. Inoltre, l'utilizzo della CPU usata da compressione backup potrebbe riprodurre un ruolo significativo in macchine virtuali con solo fino a 8 thread di CPU. Di conseguenza, uno presuppongono:

* Minore è il numero di dischi rigidi virtuali usata per archiviare i dispositivi di database, minore velocità effettiva complessiva di lettura
* Minore che il numero di CPU thread in macchine Virtuali, più gravi l'impatto di compressione backup
* Riduce le (Linux RAID software, dischi rigidi virtuali) per scrivere il backup, il valore più basso la produttività

Per aumentare il numero di destinatari per scrivere che sono disponibili due opzioni che può essere utilizzato/combinati a seconda delle esigenze:

* Striping il volume di destinazione di backup su più dischi rigidi virtuali collegati al fine di migliorare la produttività IOPS su tale volume strisce
* Creazione di una configurazione dettagli a livello di ASE SAP che utilizza più di una directory di destinazione per scrivere i dettagli per

Striping un volume su più dischi rigidi virtuali collegati sono state illustrate in precedenza in questa Guida. Per ulteriori informazioni sull'uso di più directory nella configurazione dettagli ASE SAP fare riferimento alla documentazione sulla sp_config_dump Stored Procedure che viene utilizzato per creare la configurazione di dettagli nel [Centro informazioni per Sybase](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Ripristino di emergenza con macchine virtuali di Azure

#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replica dei dati con Server di replica di Sybase SAP
Con la ASE SAP SAP Sybase Server di replica fornisce una soluzione di riserva calda trasferire le transazioni del database in modo asincrono a una posizione distante. 

Installazione e il funzionamento di SRS come si ottengono buoni risultati funzionale in una macchina virtuale ospitata in servizi di macchina virtuale di Windows Azure, come avviene in locale.

HADR ASE tramite SAP replica Server non è supportata in questo momento. Potrebbe essere testato con quindi rilasciato per le piattaforme di Microsoft Azure in futuro.

## <a name="specifics-to-oracle-database-on-windows"></a>Specifiche per Database Oracle in Windows
Poiché a metà anno 2013, il software Oracle è supportato da Oracle per l'esecuzione in Microsoft Windows Hyper-V e Azure. Leggere questo articolo viene presentata per ulteriori informazioni sul supporto di Windows Hyper-V e Azure generale da Oracle: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

In seguito il supporto generale è supportato anche lo scenario specifico delle applicazioni SAP sfruttando database Oracle. Dettagli sono denominati in questa parte del documento.

### <a name="oracle-version-support"></a>Supporto della versione Oracle
Tutti i dettagli Oracle le versioni e corrispondente del sistema operativo che sono supportati per l'esecuzione SAP su Oracle in macchine virtuali di Azure sono disponibili in seguito nota SAP [2039619]

Informazioni generali sull'esecuzione di SAP Business Suite in Oracle disponibili nella copia: <https://scn.sap.com/community/oracle>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Istruzioni di configurazione Oracle per le installazioni di SAP in macchine virtuali di Azure

#### <a name="storage-configuration"></a>Configurazione di archiviazione
Solo singola istanza Oracle utilizzando dischi NTFS formattato è supportato. Tutti i file di database devono essere archiviati del file system in base a dischi disco rigido virtuale. Questi dischi rigidi virtuali sono installati per la macchina virtuale Azure e si basano sulle archivio BLOB Azure pagina (<https://msdn.microsoft.com/library/azure/ee691964.aspx>). Qualsiasi tipo di unità di rete o condivisioni remote come file Azure servizi:
 
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-file-Service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/Persisting-Connections-to-Microsoft-Azure-Files.aspx>
 
**non** sono supportati per i file di database Oracle!

Usa dischi rigidi virtuali Azure in base a archiviazione BLOB di Azure pagina, le istruzioni apportate in questo documento capitolo [memorizzazione nella cache per macchine virtuali e dischi rigidi virtuali] [dbms-Guida-2.1] e [archiviazione Microsoft Azure] [2.3 di Guida dbms] applicare alle distribuzioni con anche il Database Oracle.

Come illustrato in precedenza in generale parte del documento, saranno disponibili le quote di velocità IOPS per dischi rigidi virtuali Azure. Le quote di esatte in base al tipo di macchine Virtuali utilizzate. Un elenco dei tipi di macchine Virtuali con le quote sono disponibili [qui][virtual-machines-sizes]

Per identificare i tipi di macchine Virtuali di Azure supportati, fare riferimento alla nota SAP [1928533]

Come la quota IOPS corrente per disco soddisfa i requisiti, è possibile archiviare tutti i file di database in una singola collegate Azure disco rigido virtuale. 

Se sono necessarie altre IOPS, è consigliabile utilizzare pool di archiviazione finestra (solo disponibile in Windows Server 2012 e versioni successive) o striping di Windows per Windows 2008 R2 per creare una grande dispositivo logico su più dischi disco rigido virtuale installati. Vedere anche capitolo [RAID Software] [2.2 di Guida dbms] di questo documento. Questo approccio semplifica il sovraccarico di amministrazione per gestire lo spazio su disco ed evita l'impegno distribuire manualmente i file in più dischi rigidi virtuali collegati.

#### <a name="backup--restore"></a>Backup e ripristino
Per il backup / ripristinare le funzionalità, BR SAP * strumenti per Oracle supportati nello stesso modo su sistemi operativi Windows Server e Hyper-V standard. Gestione di ripristino Oracle (RMAN) è supportata anche per i backup su disco e ripristinare da disco.

#### <a name="high-availability"></a>Disponibilità
[commento]: <>  (collegamento fa riferimento a ASM)
Protezione di dati Oracle è supportata per disponibilità e ripristino di emergenza. Informazioni dettagliate, vedere [questo] [ virtual-machines-windows-classic-configure-oracle-data-guard] documentazione.

#### <a name="other"></a>Altri
Tutti gli altri argomenti generali come set di disponibilità di Azure o SAP monitoraggio applicano come descritto nei primi tre capitoli di questo documento per le distribuzioni di macchine virtuali con anche il Database Oracle.

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>Specifiche per il Database MaxDB SAP in Windows

### <a name="sap-maxdb-version-support"></a>Supporto delle versioni MaxDB SAP
SAP attualmente supporta SAP MaxDB versione 7.9 per l'utilizzo con applicazioni basate su SAP NetWeaver di Azure. Tutti gli aggiornamenti per server SAP MaxDB o driver ODBC e JDBC da utilizzare con i prodotti basati su SAP NetWeaver vengono forniti esclusivamente tramite il sito Marketplace servizio SAP <https://support.sap.com/swdc>.
Sono disponibili informazioni generali sull'esecuzione SAP NetWeaver in SAP MaxDB in <https://scn.sap.com/community/maxdb>.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>Tipi supportati per le versioni di Microsoft Windows e Azure SV per DBMS MaxDB SAP
Per individuare la versione di Microsoft Windows supportata SAP MaxDB DBMS su Azure, vedere:

* [Matrice di disponibilità dei prodotti SAP (PAM)][sap-pam]
* Nota SAP [1928533]

Si consiglia di utilizzare la versione più recente del sistema operativo Microsoft Windows, Microsoft Windows 2012 R2.

### <a name="available-sap-maxdb-documentation"></a>Documentazione MaxDB SAP disponibili
È disponibile l'elenco aggiornato della documentazione SAP MaxDB seguenti nota SAP [767598]
    
### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Linee guida di configurazione MaxDB SAP per le installazioni di SAP in macchine virtuali di Azure

#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Configurazione di archiviazione
Procedure consigliate per SAP MaxDB di archiviazione Azure seguono i consigli indicati nel capitolo [struttura di una distribuzione RDBMS] [dbms-Guida-2].

> [AZURE.IMPORTANT] Come per altri database SAP MaxDB include inoltre i file di dati e di log. Tuttavia, la terminologia SAP MaxDB il termine corretto è "volume" (non "file"). Ad esempio, sono disponibili MaxDB SAP volumi di dati e registro. Non confondere con volumi del sistema operativo. 

In breve, è necessario:

* Impostare l'account di archiviazione Azure contenente SAP MaxDB dati e log volumi (ad esempio file) per **Lo spazio di archiviazione ridondanti locale (LRS)** come specificato nei capitolo [archiviazione Microsoft Azure] [2.3 di Guida dbms].
* Separare il percorso di IO per indicare i volumi dati SAP MaxDB (ad esempio file) dal percorso di IO per indicare i volumi log (ad esempio file). Ciò significa che il volume di dati di SAP MaxDB (ad esempio file) devono essere installato in un'unità logica e volumi log SAP MaxDB (ad esempio file) devono essere installato in un'altra unità logica.
* Impostare il file corretto memorizzazione nella cache per ogni blob Azure, a seconda che si usa per SAP MaxDB volumi di dati o log (ad esempio file) e che si utilizzi Azure Standard o spazio di archiviazione di Azure Premium, come descritto in capitolo [memorizzazione nella cache per macchine virtuali] [dbms-Guida-2.1].
* Come la quota IOPS corrente per disco soddisfa i requisiti, è possibile archiviare tutti i volumi di dati in un disco rigido virtuale Azure singola collegate e inoltre archiviare tutti i volumi log di database in un altro singolo collegate Azure disco rigido virtuale.
* Se è necessario altre IOPS e/o spazio, è consigliabile utilizzare Microsoft di pool di archiviazione finestra (solo disponibili in Microsoft Windows Server 2012 e versioni successive) o striping di Microsoft Windows per Microsoft Windows 2008 R2 per creare un dispositivo logico grande su più dischi disco rigido virtuale installati. Vedere anche capitolo [RAID Software] [2.2 di Guida dbms] di questo documento. Questo approccio semplifica il sovraccarico di amministrazione per gestire lo spazio su disco ed evita l'impegno di distribuzione manualmente i file in più dischi rigidi virtuali collegati.
* Per i requisiti IOPS più alti, è possibile utilizzare l'archiviazione Premium Azure, disponibile in serie DS e macchine virtuali di serie GS.

![Configurazione di riferimento di Azure macchine Virtuali IaaS per DBMS MaxDB SAP][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Eseguire il backup e ripristino
Quando si distribuisce SAP MaxDB in Azure, è necessario esaminare la metodologia di backup. Anche se il sistema non è un sistema produttività, SAP backup del database ospitato da SAP MaxDB deve essere periodicamente. Poiché lo spazio di archiviazione di Azure conserva tre immagini, una copia di backup è ora meno importante in termini di proteggere il sistema da errore di archiviazione e gli errori di amministrazione o operativi più importanti. Il motivo principale per la gestione di un efficace di backup e ripristino piano è in modo che è possibile compensa errori logici o manuali fornendo funzionalità di ripristino in un momento. In modo che l'obiettivo è utilizzare backup per ripristinare il database a un determinato punto nel tempo o usare i backup in Azure da un altro sistema copiando il database esistente. Ad esempio, potrebbe trasferire da una configurazione SAP di livello 2 per la configurazione di sistema di livello 3 dello stesso sistema tramite il ripristino di una copia di backup.

Backup e ripristino di un database in Azure è nello stesso modo, sia per i sistemi locali, è possibile utilizzare gli strumenti backup e ripristino MaxDB SAP standard, che sono descritti in uno dei documenti documentazione SAP MaxDB elencati nella nota SAP [767598]. 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Considerazioni sulle prestazioni di Backup e ripristino
Come privo distribuzioni, le prestazioni di backup e ripristino dipende del numero di volumi può essere letti parallelo e la velocità di tali volumi. Inoltre, l'utilizzo della CPU usata da compressione backup può avere un ruolo significativo in macchine virtuali con fino a 8 thread di CPU. Di conseguenza, uno presuppongono:

* Minore è il numero di dischi rigidi virtuali usata per archiviare i dispositivi di database, il valore più basso la velocità di lettura complessiva
* Minore che il numero di CPU thread in macchine Virtuali, più gravi l'impatto di compressione backup
* Riduce le (strisce directory; dischi rigidi virtuali) per scrivere il backup, il valore più basso la produttività

Per aumentare il numero di destinatari per scrivere, sono disponibili due opzioni che è possibile usare, possibilmente in combinazione, a seconda delle esigenze:

* Dedicare volumi separati per il backup
* Striping il volume di destinazione di backup su più dischi rigidi virtuali collegati al fine di migliorare la produttività IOPS su tale volume a strisce con disco
* In dispositivi separato logico dedicato per:
    * SAP volumi di backup MaxDB (ad esempio file)
    * SAP volumi di dati MaxDB (ad esempio file)
    * SAP volumi log MaxDB (ad esempio file)

Striping un volume su più dischi rigidi virtuali collegati è illustrato in precedenza capitolo [RAID Software] [2.2 di Guida dbms] di questo documento. 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Altri
Tutti gli altri argomenti generali, ad esempio set di disponibilità di Azure o SAP monitoraggio applicano anche come descritto nei primi tre capitoli di questo documento per le distribuzioni di macchine virtuali con il database MaxDB SAP.
Altre impostazioni specifiche SAP MaxDB sono trasparente alle macchine virtuali di Azure e sono descritte in diversi documenti elencati nella nota SAP [767598] e nelle note SAP:

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>Specifiche per liveCache SAP in Windows

### <a name="sap-livecache-version-support"></a>SAP liveCache il supporto della versione
Versione minima di liveCache SAP supportati in macchine virtuali di Azure è **SAP LC/LCAPPS 10.0 SP 25** inclusi **liveCache 7.9.08.31** e **25 LCA compilazione**rilasciato per **2 EhP per SAP servizi 7.0** e versioni successive.

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>Tipi di versioni di Microsoft Windows e Azure SV supportati per SAP liveCache DBMS
Per individuare la versione di Microsoft Windows supportata liveCache SAP in Azure, vedere:

* [Matrice di disponibilità dei prodotti SAP (PAM)][sap-pam]
* Nota SAP [1928533]

Si consiglia di utilizzare la versione più recente del sistema operativo Microsoft Windows, Microsoft Windows 2012 R2. 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP liveCache le istruzioni di configurazione per le installazioni di SAP in macchine virtuali di Azure

#### <a name="recommended-azure-vm-types"></a>Tipi di macchine Virtuali Azure consigliati
Come SAP liveCache è un'applicazione di calcolo di grandi dimensioni, l'importo e la velocità di RAM e CPU ha un forte impatto sulle prestazioni liveCache SAP. 

Per i tipi di macchine Virtuali di Azure supportati da SAP (nota SAP [1928533]), sono supportate tutte le risorse CPU virtuale a macchina virtuale dalla dedicato risorse CPU fisiche dell'hypervisor. Nessun overprovisioning (e pertanto non concorrenza per le risorse CPU) entrerà in vigore.

Analogamente, per tutti i tipi di istanza macchine Virtuali di Azure supportati da SAP, la memoria macchine Virtuali è 100% mappati alla memoria fisica – overprovisioning (impegno eccessivo), ad esempio, non viene utilizzata.

Da questa prospettiva si consiglia di utilizzare il nuovo tipo di serie D o macchine Virtuali di Azure serie DS (in combinazione con lo spazio di archiviazione di Azure Premium), che abbiano 60% processori più veloci rispetto della serie. Per il carico CPU e RAM maggiore, è possibile utilizzare serie G e macchine virtuali di serie GS (in combinazione con lo spazio di archiviazione di Azure Premium) con la famiglia di v3 E5 processore Intel® Xeon® più recente, che hanno due volte la memoria e quattro volte la provincia a tinta unita unità di archiviazione aggiuntivo (SSDs) D/DS della serie.

#### <a name="storage-configuration"></a>Configurazione di archiviazione
Come SAP liveCache si basa sulla tecnologia MaxDB SAP, tutto lo spazio di archiviazione Azure procedure consigliate descritte per SAP MaxDB capitolo [configurazione dello spazio di archiviazione] [8.4.1 di Guida dbms] sono validi per liveCache SAP. 

#### <a name="dedicated-azure-vm-for-livecache"></a>Dedicato macchine Virtuali di Azure per liveCache
Come SAP liveCache Usa misura significativa potenza, per l'utilizzo di produttivo si consiglia di distribuire in un computer virtuale dedicato Azure. 
 
![Dedicato macchine Virtuali di Azure per liveCache per produttivi use case][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>Eseguire il backup e ripristino
Eseguire il backup e ripristino, incluse le considerazioni sulle prestazioni, sono già descritti nella pertinenti capitoli SAP MaxDB [Backup e ripristino] [8.4.2 di Guida dbms] e [considerazioni sulle prestazioni di Backup e ripristino] [dbms-Guida-8.4.3]. 

#### <a name="other"></a>Altri
Tutti gli altri argomenti generali sono già descritti nel relativo capitolo [dbms-Guida-8.4.4] MaxDB SAP [questa]. 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>Specifiche per il Server di contenuto SAP in Windows
Il Server di contenuto SAP è un componente separato, basate sul server per archiviare il contenuto, ad esempio documenti elettronici in diversi formati. SAP Content Server fornito dal sviluppo della tecnologia e deve essere utilizzata tra le applicazioni per tutte le applicazioni SAP. È installato in un sistema separato. Il contenuto tipico è materiale didattico e documentazione da Warehouse Knowledge o disegni tecnici provenienti da mySAP PLM sistema di gestione di documenti. 

### <a name="sap-content-server-version-support"></a>Supporto della versione Server contenuto SAP
Supporta attualmente SAP:

* **SAP Content Server** con versione **6.50 (e versioni successive)**
* **SAP MaxDB versione 7.9**
* **Microsoft IIS (Internet Information Server) versione 8.0 (e versioni successive)**

Si consiglia di utilizzare la versione più recente di SAP Content Server, ossia in fase di questo documento **6.50 SP4**e la versione più recente di **Microsoft IIS 8.5**. 

Verificare le versioni più recenti supportate dei Server contenuto SAP e Microsoft IIS in [SAP prodotto disponibilità matrice PAM][sap-pam].

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>Tipi di Microsoft Windows e Azure SV supportati per Server contenuto SAP
Per individuare la versione supportata di Windows Server contenuto SAP in Azure, vedere:

* [Matrice di disponibilità dei prodotti SAP (PAM)][sap-pam]
* Nota SAP [1928533]

Si consiglia di utilizzare la versione più recente di Microsoft Windows, ossia in fase di questo documento **Windows Server 2012 R2**.

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>SAP contenuto linee guida di configurazione per le installazioni di SAP in macchine virtuali di Azure

#### <a name="storage-configuration"></a>Configurazione di archiviazione 
Se si configura SAP Content Server per archiviare i file del database SAP MaxDB, tutto lo spazio di archiviazione Azure procedura consigliata indicata per SAP MaxDB capitolo del mese di spazio di archiviazione configurazione [dbms-Guida-8.4.1] valide anche per lo scenario SAP Content Server. 

Se si configura SAP Content Server per archiviare i file nel file system, è consigliabile utilizzare un'unità logica dedicata. Utilizzo di spazi di archiviazione consente di anche aumentare le dimensioni del disco logico e la velocità effettiva IOPS, come descritto in capitolo [RAID Software] [2.2 di Guida dbms]. 

#### <a name="sap-content-server-location"></a>Percorso del Server contenuto SAP
SAP Content Server deve essere distribuito nella stessa regione Azure e Azure VNET nel punto in cui viene distribuito sistema SAP. Sono gratuite decidere se si desidera distribuire i componenti di SAP Server contenuto in una macchina virtuale Azure dedicato o nella stessa macchina virtuale in cui viene eseguito il sistema SAP. 
 
![Macchine Virtuali di Azure dedicato per Server contenuto SAP][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>Percorso del Server Cache SAP
Il Server di Cache SAP è un componente aggiuntivo basate sul server per consentire l'accesso ai documenti (memorizzati nella cache) in locale. Il Server di Cache SAP nella cache dei documenti di un Server di contenuti SAP. Si tratta di ottimizzare il traffico di rete se documenti devono essere recuperati più volte diverse posizioni. La regola generale è che il Server di Cache SAP deve essere fisica vicino client che accede al Server di Cache SAP. 

Qui sono disponibili due opzioni:

1. **Client è un sistema SAP back-end** Se un sistema SAP back-end è configurato per accedere a SAP Content Server, tale sistema SAP è un client. Durante la stessa regione Azure – nel centro dati di Azure stesso-distribuzione sistema SAP e SAP Server contenuto sono fisica vicini. Di conseguenza, non è necessario disporre di un Server di Cache SAP dedicato. I client dell'interfaccia utente SAP (SAP grafica o un browser web) accedono direttamente a sistema SAP e sistema SAP Recupera documenti dal Server del contenuto SAP.
1. **Client è un browser web locale** SAP Content Server può essere configurato per accedere direttamente dal browser. In questo caso un web browser in esecuzione in-locale è un client di SAP Content Server. Data Center locale e Azure Data Center vengono inseriti in un'altra posizione fisica (ideale vicini). Il data center locale sia connesso a Azure tramite VPN da sito Azure o ExpressRoute. Anche se entrambe le opzioni offrono connessione di rete VPN protetta in Azure, connessione di rete da sito non è disponibile un contratto di servizio rete della larghezza di banda e latenza tra la data center locale e la Data Center di Azure. Per velocizzare l'accesso ai documenti, è possibile eseguire una delle operazioni seguenti:
    1. Installare SAP Cache Server in locale, Chiudi per locale nel web browser (opzione su figura [this][dbms-guide-900-sap-cache-server-on-premises])
    1. Configurare Azure ExpressRoute, che offre una connessione di rete dedicato ad alta velocità e bassa latenza tra Data Center locale e Azure Data Center.
 
![Opzione per l'installazione SAP Cache Server in locale][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>Backup e ripristino
Se si configura il Server di contenuto SAP per archiviare i file del database SAP MaxDB, le considerazioni procedura e le prestazioni di backup e ripristino sono già descritte in SAP MaxDB capitolo [Backup e ripristino] [8.4.2 di Guida dbms] capitolo [considerazioni sulle prestazioni di Backup e ripristino] e [dbms-Guida-8.4.3]. 

Se si configura il Server di contenuto SAP per archiviare i file nel file system, un'opzione consiste nell'eseguire backup e ripristino manuale della struttura intero file in cui si trovano i documenti. Analogamente a backup e ripristino MaxDB SAP, è consigliabile mantenere un volume dedicato a scopo di backup. 

#### <a name="other"></a>Altri
Altre impostazioni specifiche SAP Content Server sono trasparente alle macchine virtuali di Azure e sono descritte in vari documenti e le note SAP:

* <https://Service.SAP.com/contentserver> 
* Nota SAP [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>Specifiche di IBM DB2 per LUW in Windows
Con Microsoft Azure, è possibile trasferire facilmente applicazione SAP esistente in esecuzione su IBM DB2 per Linux, UNIX e Windows (LUW) in macchine virtuali Azure. Con SAP su IBM DB2 per LUW, amministratori e sviluppatori possono utilizzare la stessa sviluppo e gli strumenti di amministrazione che sono disponibili in locale.
Informazioni generali sull'esecuzione di SAP Business Suite in IBM DB2 per LUW disponibili in SAP Community rete (si) in <https://scn.sap.com/community/db2-for-linux-unix-windows>.

Per ulteriori informazioni e aggiornamenti relativi SAP su DB2 per LUW su Azure, vedere nota SAP [2233094]. 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 per Linux, UNIX e supporto della versione di Windows
SAP su IBM DB2 per LUW su Microsoft Azure macchina virtuale Services è supportato nella versione 10.5 DB2.

Per informazioni sui tipi di macchine Virtuali di Azure e prodotti SAP supportati, vedere nota SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 per Linux, UNIX e linee guida di configurazione di Windows per le installazioni di SAP in macchine virtuali di Azure

#### <a name="storage-configuration"></a>Configurazione di archiviazione
Tutti i file di database devono essere archiviati del file system in base a dischi disco rigido virtuale. Questi dischi rigidi virtuali sono installati per la macchina virtuale Azure e basano nella pagina archivio BLOB di Azure (<https://msdn.microsoft.com/library/azure/ee691964.aspx>). Qualsiasi tipo di unità di rete o condivisioni remote quali sono i seguenti servizi Azure file **non** supportati per i file di database: 

* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-file-Service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/Persisting-Connections-to-Microsoft-Azure-Files.aspx>
 
Se si utilizza dischi rigidi virtuali Azure in base a archiviazione BLOB di Azure pagina, le diciture nel documento in capitolo [struttura di una distribuzione RDBMS] [dbms-Guida-2] vengono applicate anche a distribuzioni con IBM DB2 per Database LUW. 

Come illustrato in precedenza in generale parte del documento, saranno disponibili le quote di velocità IOPS per dischi rigidi virtuali Azure. Le quote di esatte dipendono dal tipo di macchine Virtuali utilizzato. Un elenco dei tipi di macchine Virtuali con le quote sono disponibili [qui][virtual-machines-sizes].

Come la quota IOPS corrente per disco è sufficiente, è possibile memorizzare tutti i file di database in una singola collegate Azure disco rigido virtuale. 

Per prestazioni ottimali considerazioni consultare anche capitolo "Dati sicurezza e le prestazioni considerazioni per Database directory" in Guide all'installazione di SAP.

In alternativa, è possibile utilizzare i pool di archiviazione di Windows (solo disponibile in Windows Server 2012 e versioni successive) o striping di Windows per Windows 2008 R2 come descritto in capitolo [RAID Software] [2.2 di Guida dbms] di questo documento per creare un dispositivo logico grande su più dischi disco rigido virtuale installati.
Per i dischi contenenti i percorsi di archiviazione DB2 per la directory sapdata e saptmp, è necessario specificare una dimensione di settore disco fisico di 512 KB. Quando si utilizza il pool di archiviazione di Windows, è necessario creare il pool di archiviazione manualmente mediante l'interfaccia della riga di comando utilizzando il parametro "-LogicalSectorSizeDefault". Per informazioni dettagliate, vedere <https://technet.microsoft.com/library/hh848689.aspx>.

#### <a name="backuprestore"></a>Backup e ripristino
Le funzionalità di backup e ripristino per IBM DB2 per LUW sono supportata nello stesso modo standard sistemi operativi Windows Server e Hyper-V.

Assicurarsi di avere una strategia di backup del database validi in posizione. 

Come privo distribuzioni, le prestazioni di backup e ripristino dipendono del numero di volumi può essere letti in parallelo e la velocità di tali volumi potrebbe. Inoltre, l'utilizzo della CPU usata da compressione backup potrebbe riprodurre un ruolo significativo in macchine virtuali con solo fino a 8 thread di CPU. Di conseguenza, uno presuppongono:

* Minore è il numero di dischi rigidi virtuali usata per archiviare i dispositivi di database, minore velocità effettiva complessiva di lettura
* Minore che il numero di CPU thread in macchine Virtuali, più gravi l'impatto di compressione backup
* Riduce le (strisce directory; dischi rigidi virtuali) per scrivere il backup, il valore più basso la produttività

Per aumentare il numero di destinatari per scrivere, due opzioni possono essere usate/combinati a seconda delle esigenze:

* Striping il volume di destinazione di backup su più dischi rigidi virtuali collegati al fine di migliorare la produttività IOPS su tale volume strisce
* Utilizzo di più di una directory di destinazione per scrivere il backup

#### <a name="high-availability-and-disaster-recovery"></a>Disponibilità e il ripristino di emergenza
Microsoft Cluster Server (MSCS) non è supportata.

Ripristino di emergenza disponibilità DB2 (HADR) è supportato. Se le macchine virtuali di configurazione HA disporre di risoluzione dei nomi, la configurazione di Azure non sarà diversa da qualsiasi installazione è stata completata locale. Non è consigliabile si basano su solo la risoluzione IP.

Non utilizzare replica con Azure archivio geografico. Per ulteriori informazioni, vedere capitolo [archiviazione Microsoft Azure] [2.3 di Guida dbms] e capitolo [disponibilità e il ripristino di emergenza con macchine virtuali di Azure] [dbms-Guida-3].

#### <a name="other"></a>Altri
Tutti gli altri argomenti generali come set di disponibilità di Azure o SAP monitoraggio applicano come descritto nei primi tre capitoli di questo documento per le distribuzioni di macchine virtuali con IBM DB2 per LUW anche. 

Inoltre, vedere capitolo [generale di SQL Server per SAP su Azure riepilogo] [5.8 di Guida dbms].

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>Specifiche di IBM DB2 per LUW su Linux
Con Microsoft Azure, è possibile trasferire facilmente applicazione SAP esistente in esecuzione su IBM DB2 per Linux, UNIX e Windows (LUW) in macchine virtuali Azure. Con SAP su IBM DB2 per LUW, amministratori e sviluppatori possono utilizzare la stessa sviluppo e gli strumenti di amministrazione che sono disponibili in locale. Informazioni generali sull'esecuzione di SAP Business Suite in IBM DB2 per LUW disponibili in SAP Community rete (si) in <https://scn.sap.com/community/db2-for-linux-unix-windows>.

Per ulteriori informazioni e aggiornamenti relativi SAP su DB2 per LUW su Azure, vedere nota SAP [2233094].

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 per Linux, UNIX e supporto della versione di Windows
SAP su IBM DB2 per LUW su Microsoft Azure macchina virtuale Services è supportato nella versione 10.5 DB2.

Per informazioni sui tipi di macchine Virtuali di Azure e prodotti SAP supportati, vedere nota SAP [1928533].

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM DB2 per Linux, UNIX e linee guida di configurazione di Windows per le installazioni di SAP in macchine virtuali di Azure

#### <a name="storage-configuration"></a>Configurazione di archiviazione
Tutti i file di database devono essere archiviati in un file system in base a dischi disco rigido virtuale. Questi dischi rigidi virtuali sono installati per la macchina virtuale Azure e basano nella pagina archivio BLOB di Azure (<https://msdn.microsoft.com/library/azure/ee691964.aspx>).
Qualsiasi tipo di unità di rete o condivisioni remote quali sono i seguenti servizi Azure file **non** supportati per i file di database:

* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/12/Introducing-Microsoft-Azure-file-Service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/Archive/2014/05/27/Persisting-Connections-to-Microsoft-Azure-Files.aspx>

Se si utilizza dischi rigidi virtuali Azure in base a archiviazione BLOB di Azure pagina, le diciture nel documento in capitolo [struttura di una distribuzione RDBMS] [dbms-Guida-2] vengono applicate anche a distribuzioni con IBM DB2 per Database LUW.

Come illustrato in precedenza in generale parte del documento, saranno disponibili le quote di velocità IOPS per dischi rigidi virtuali Azure. Le quote di esatte dipendono dal tipo di macchine Virtuali utilizzato. Un elenco dei tipi di macchine Virtuali con le quote sono disponibili [qui][virtual-machines-sizes].

Come la quota IOPS corrente per disco è sufficiente, è possibile memorizzare tutti i file di database in una singola collegate Azure disco rigido virtuale.

Per prestazioni ottimali considerazioni consultare anche capitolo "Dati sicurezza e le prestazioni considerazioni per Database directory" in Guide all'installazione di SAP.

In alternativa, è possibile utilizzare LVM (logico Volume Manager) o MDADM come descritto in capitolo [RAID Software] [2.2 di Guida dbms] di questo documento per creare una grande dispositivo logico su più dischi disco rigido virtuale installati.
Per i dischi contenenti i percorsi di archiviazione DB2 per la directory sapdata e saptmp, è necessario specificare una dimensione di settore disco fisico di 512 KB.

#### <a name="backuprestore"></a>Backup e ripristino
Le funzionalità di backup e ripristino per IBM DB2 per LUW sono supportata in esattamente come standard Linux installazione locale.

Assicurarsi di avere una strategia di backup del database validi in posizione.

Come privo distribuzioni, le prestazioni di backup e ripristino dipendono del numero di volumi può essere letti in parallelo e la velocità di tali volumi potrebbe. Inoltre, l'utilizzo della CPU usata da compressione backup potrebbe riprodurre un ruolo significativo in macchine virtuali con solo fino a 8 thread di CPU. Di conseguenza, uno presuppongono:

* Minore è il numero di dischi rigidi virtuali usata per archiviare i dispositivi di database, minore velocità effettiva complessiva di lettura
* Minore che il numero di CPU thread in macchine Virtuali, più gravi l'impatto di compressione backup
* Riduce le (strisce directory; dischi rigidi virtuali) per scrivere il backup, il valore più basso la produttività

Per aumentare il numero di destinatari per scrivere, due opzioni possono essere usate/combinati a seconda delle esigenze:

* Striping il volume di destinazione di backup su più dischi rigidi virtuali collegati al fine di migliorare la produttività IOPS su tale volume strisce
* Utilizzo di più di una directory di destinazione per scrivere il backup

#### <a name="high-availability-and-disaster-recovery"></a>Disponibilità e il ripristino di emergenza
Ripristino di emergenza disponibilità DB2 (HADR) è supportato. Se le macchine virtuali di configurazione HA disporre di risoluzione dei nomi, la configurazione di Azure non sarà diversa da qualsiasi installazione è stata completata locale. Non è consigliabile si basano su solo la risoluzione IP.

Non utilizzare replica con Azure archivio geografico. Per ulteriori informazioni, vedere capitolo [archiviazione Microsoft Azure] [2.3 di Guida dbms] e capitolo [disponibilità e il ripristino di emergenza con macchine virtuali di Azure] [dbms-Guida-3].

#### <a name="other"></a>Altri
Tutti gli altri argomenti generali come set di disponibilità di Azure o SAP monitoraggio applicano come descritto nei primi tre capitoli di questo documento per le distribuzioni di macchine virtuali con IBM DB2 per LUW anche.

Inoltre, vedere capitolo [generale di SQL Server per SAP su Azure riepilogo] [5.8 di Guida dbms].