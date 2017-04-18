<properties
   pageTitle="SAP NetWeaver su Linux macchine (): Guida alla distribuzione | Microsoft Azure"
   description="SAP NetWeaver su Linux macchine (): Guida alla distribuzione"
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

# <a name="sap-netweaver-on-azure-virtual-machines-vms--deployment-guide"></a>SAP NetWeaver su Azure macchine (): Guida alla distribuzione

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
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
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
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
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

Microsoft Azure consente alle aziende di acquisire risorse di elaborazione e di archiviazione minimo ora senza cicli di approvvigionamento lunga. Macchine virtuali di Azure consente alle aziende distribuire classica applicazioni, ad esempio applicazioni in Azure basate su SAP NetWeaver ed estendere i loro l'affidabilità e disponibilità senza ulteriori risorse disponibili in locale. Microsoft Azure supporta anche la connettività tra locale, che consente alle aziende di integrare attivamente macchine virtuali di Azure i domini locale, il cloud privato e loro orizzontale sistema SAP.

In questo articolo vengono illustrati step by step la preparazione di una macchina virtuale Azure per la distribuzione delle applicazioni SAP NetWeaver in base a. Si presuppone che le informazioni contenute in [pianificazione in corso e una Guida all'implementazione] [-Guida alla pianificazione] viene definito. In caso contrario, il documento corrispondente deve essere leggere per primo.

Carta si integra con la documentazione di installazione di SAP e note di SAP che rappresentano le risorse principale per le installazioni e distribuzioni del software SAP in dato piattaforme.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="introduction"></a>Introduzione
Un numero elevato di società in tutto il mondo Usa le applicazioni SAP NetWeaver in base a: più in evidenza la famiglia SAP Business-per eseguire analisi missione critiche processi aziendali. L'integrità del sistema è una risorsa fondamentale, e la possibilità di supporto dell'organizzazione in caso di problemi di funzionamento, inclusi problemi di prestazioni, diventa un requisito essenziale.
Microsoft Azure fornisce strumentazione piattaforma eccellente per soddisfare i requisiti di supporto di tutte le applicazioni aziendali critiche. Questa Guida garantisce che una destinazione per la distribuzione di Software SAP è configurata in modo che il supporto dell'organizzazione possa essere disponibili, indipendentemente dal fatto che che modo la macchina virtuale viene creato, di Microsoft Azure Virtual Machine vengano si esce Azure Marketplace o tramite un'immagine specifica del cliente.
Nel programma di installazione seguente, tutte le necessarie procedura descritta in dettaglio.

## <a name="prerequisites-and-resources"></a>Prerequisiti e risorse
### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, verificare che siano soddisfatti i prerequisiti descritti nei capitoli seguenti.

#### <a name="local-personal-computer"></a>PC locale
La configurazione di una macchina virtuale di Azure per la distribuzione di Software SAP comprende diversi passaggi. Per gestire macchine virtuali di Windows o Linux macchine virtuali, è necessario utilizzare uno script di PowerShell e il portale di Microsoft Azure. Per tale, è necessario un PC locale che esegue Windows 7 o versioni successive. Se si desidera gestire macchine virtuali Linux e si decide di usare un computer Linux per questa attività, è possibile usare anche l'interfaccia di riga di comando di Azure (Azure CLI).

#### <a name="internet-connection"></a>Connessione a Internet
Per scaricare ed eseguire gli script e gli strumenti necessari, è necessaria una connessione Internet. Inoltre, Microsoft Azure Virtual Machine esecuzione Azure Enhanced monitoraggio estensione richiede l'accesso a Internet. Nel caso in cui questa macchina virtuale Azure fa parte di un dominio locale o di Azure virtuali, assicurarsi che le impostazioni proxy siano impostate come descritto in capitolo [Configurare un Proxy di] [ deployment-guide-configure-proxy] in questo documento.

#### <a name="microsoft-azure-subscription"></a>Abbonamento a Microsoft Azure
Esiste già un account Azure e conoscono secondo le credenziali di accesso.

#### <a name="topology-consideration-and-networking"></a>Considerazione topologia e reti
La topologia e architettura della distribuzione SAP in Azure deve essere definito. Architettura in merito alla:

* Lo spazio di archiviazione di Microsoft Azure account da utilizzare
* Rete virtuale per distribuire il sistema SAP in
* Gruppo di risorse per distribuire il sistema SAP in
* Area Azure per distribuire il sistema SAP
* Configurazione di SAP (livello 2 o 3 livello)
* Le dimensioni di macchine Virtuali e il numero di ulteriori dischi rigidi virtuali alla VM(s)
* Configurazione di sistema SAP trasporto e correzione

Azure gli account di archiviazione o reti virtuali Azure come tale devono sono state create e già configurate. Come creare e configurarli è coperto [pianificazione e una Guida all'implementazione] [-Guida alla pianificazione].

#### <a name="sap-sizing"></a>Ridimensionamento di SAP
* Carico di lavoro SAP previsto è, ad esempio utilizzando Quicksizer SAP e viene definito il secondo numero SAP 
* È necessario conoscere la richiesta delle risorse e memoria CPU da parte di sistema SAP
* È necessario conoscere le operazioni dei / o necessarie al secondo
* Si noti la larghezza di banda necessari in eventuale comunicazioni tra diverse macchine virtuali di Azure
* La larghezza di banda necessari tra le risorse locali e di Azure distribuito SAP noto di sistemi

#### <a name="resource-groups"></a>Gruppi di risorse
Gruppi di risorse sono un nuovo concetto contenenti tutte le risorse con il ciclo di vita stesso ad esempio, vengono creati ed eliminati nello stesso momento. Leggere [questo articolo] [ resource-group-overview] per ulteriori informazioni sui gruppi di risorse. 

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Risorse SAP
Durante il lavoro configurazione sono necessarie le risorse seguenti:

* Nota SAP [1928533]
    * l'elenco dei formati di Azure Virtual Machine, sono supportati per la distribuzione di Software SAP 
    * informazioni importanti capacità per le dimensioni del computer virtuale Azure
    * combinazione del sistema operativo e DB e software supportati SAP
* SAP nota [2015553] voce prerequisiti devono essere supportati da SAP durante la distribuzione di software SAP in Microsoft Azure.
* SAP nota [1999351] contenente ulteriori informazioni sulla risoluzione dei problemi per Enhanced Azure monitoraggio per SAP.
* SAP nota [2178632] contenente informazioni dettagliate su tutte le statistiche di monitoraggio disponibili per SAP in Microsoft Azure. 
* SAP nota [1409604] contenente la versione agente Host SAP richiesta per Windows in Microsoft Azure quando si distribuisce il nuovo Manager delle risorse Azure.
* SAP nota [2191498] contenente la versione agente Host SAP richiesta per Linux in Microsoft Azure quando si distribuisce il nuovo Manager delle risorse Azure.
* SAP nota [2243692] contenente le informazioni sulla gestione delle licenze per SAP su Linux su Azure
* SAP nota [1984787] contenente informazioni generali su SUSE LINUX Enterprise Server 12
* SAP nota [2002167] contenente informazioni generali Red Hat Enterprise Linux 7. x
* [Copia](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) che contiene tutti necessari SAP note per Linux
* Cmdlet di PowerShell specifico SAP che fanno parte di [PowerShell di Azure][azure-ps]
* SAP specifico Azure CLI che fanno parte di [Azure CLI][azure-cli]
* [Portale di Microsoft Azure][azure-portal]

[comment]: <> (Livello di patch MSSedusch TODO aggiungere ARM agente Host SAP in 1409604 nota SAP)
 
Le guide seguenti riguardano l'argomento di SAP in Microsoft Azure anche:

* [SAP NetWeaver su Azure macchine ()-implementazione Guida alla pianificazione e] [-Guida alla pianificazione]
* [SAP NetWeaver su Azure macchine (): Guida alla distribuzione (in questo documento)] [Guida alla distribuzione]
* [SAP NetWeaver su Azure macchine (): Guida alla distribuzione di DBMS] [dbms Guida]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scenari di distribuzione di macchine virtuali per SAP in Microsoft Azure
In questo capitolo conoscere i diversi metodi di distribuzione e i singoli passaggi per ogni tipo di distribuzione.

### <a name="deployment-of-vms-for-sap"></a>Distribuzione di macchine virtuali per SAP
Microsoft Azure in diversi modi per distribuire macchine virtuali e dischi associati. In questo modo è molto importante conoscere le differenze poiché preparati delle macchine virtuali potrebbero essere diversi dipende dal modo di distribuzione. In generale è esaminare gli scenari seguenti:

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Distribuzione di una macchina virtuale fuori Azure Marketplace
Si desidera eseguire un Microsoft o parti 3 ° fornito immagine da Azure Marketplace per distribuire le macchine Virtuali. Dopo aver distribuito la macchina virtuale in Microsoft Azure, seguire la stessa linee guida e strumenti per installare il software SAP all'interno di una macchina virtuale come si farebbe in un ambiente locale. Per installare il software SAP all'interno della macchina virtuale Azure, SAP e Microsoft consiglia per caricare e archiviare il supporto di installazione SAP in dischi rigidi virtuali Azure o per creare una macchina virtuale Azure funziona come un "server di File" che contiene tutte le necessarie SAP supporto di installazione.

[comment]: <> (MSSedusch TODO perché è necessario consigliabile una gestione dei file, ad esempio File Server o disco rigido virtuale? Si tratta in modo diverso da locale?)

Per ulteriori informazioni vedere capitolo [Scenario 1: distribuire una macchina virtuale fuori Azure Marketplace per SAP] [distribuzione-Guida-3,2].

#### <a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>Distribuzione di una macchina virtuale con un'immagine personalizzata
A causa di requisiti patch specifiche relative alla versione del sistema operativo o DBMS, le immagini fornite da Azure Marketplace potrebbero non secondo le proprie esigenze. Di conseguenza, potrebbe essere necessario creare una macchina virtuale utilizzando la propria immagine macchine Virtuali del sistema operativo/DB 'private' che può essere distribuito più volte in un secondo momento.
La procedura per creare un'immagine privata si differenziano tra Windows e un'immagine di Linux.

___

> ![Windows][Logo_Windows] Windows
>
> Per preparare un'immagine di Windows che può essere utilizzata per distribuire più macchine virtuali, le impostazioni di Windows (ad esempio Windows SID e hostname) devono essere estratti/generalized nella macchina virtuale locale. Questa operazione può essere eseguita utilizzando sysprep come descritto in <https://technet.microsoft.com/library/cc721940.aspx>.
>
> ![Linux][Logo_Linux] Linux
>
> Per preparare un'immagine di Linux che può essere utilizzata per distribuire più macchine virtuali, alcune impostazioni Linux devono essere estratti/generalized nella macchina virtuale locale. Questa operazione può essere eseguita tramite waagent-il deprovisioning come descritto in [questo articolo] [ virtual-machines-linux-capture-image] o in [questo articolo][virtual-machines-linux-agent-user-guide-command-line-options].

___

Impostare il contenuto del database utilizzando il responsabile di provisioning Software SAP per installare un nuovo sistema SAP, ripristinare un database da un disco rigido virtuale collegato al computer virtuale o direttamente ripristinare un database dallo spazio di archiviazione di Azure se DBMS supportata. (vedere [DBMS Guide][dbms-guide]) distribuzione. Se è già stato installato un sistema SAP in macchine Virtuali il locale (in particolare per sistemi di livello 2), è possibile adattare le impostazioni di sistema SAP dopo la distribuzione di Azure macchina virtuale tramite la procedura di sistema rinominare supportata da SAP Software il Provisioning di gestore (nota SAP [1619720]). In caso contrario è possibile installare il software SAP in un secondo momento dopo la distribuzione di Azure macchina virtuale.

Per ulteriori informazioni vedere capitolo [Scenario 2: distribuire una macchina virtuale con un'immagine personalizzata per SAP] [3.3 di Guida alla distribuzione].

#### <a name="moving-a-vm-from-on-premises-to-microsoft-azure-with-a-non-generalized-disk"></a>Spostamento di una macchina virtuale da locale a Microsoft Azure con un disco non generalized
Si prevede di spostare un sistema SAP specifico da locale a Microsoft Azure. Ciò è possibile caricando il disco rigido virtuale che contiene il sistema operativo, i file binari SAP e file binari DBMS eventuale plus i dischi rigidi virtuali con file di dati e Registro di sistema DBMS a Microsoft Azure. In opposto per lo scenario descritto in capitolo [distribuzione una macchina virtuale con un'immagine personalizzata] [distribuzione-Guida-3.1.2] sopra, mantenere il nome host, SID SAP e account utente SAP nella macchina virtuale Azure come sono state configurate nell'ambiente locale. Di conseguenza, generalizzazione il sistema operativo non è necessario. In questo caso applicherà prevalentemente per gli scenari di croce locale in cui una parte del paesaggio SAP viene eseguita in locale e parti in Microsoft Azure.

Per ulteriori informazioni vedere capitolo [Scenario 3: spostare una macchina virtuale locali con un disco rigido virtuale Azure non generalized SAP] [3.4 di Guida alla distribuzione].

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenario 1: Distribuire una macchina virtuale fuori Azure Marketplace per SAP
Microsoft Azure offre la possibilità di distribuire un'istanza di macchine Virtuali fuori Azure Marketplace, che offre alcune immagini del sistema operativo standard di Windows Server e diverse distribuzioni Linux. È anche possibile distribuire un'immagine che include DBMS SKU, ad esempio SQL Server. Per informazioni dettagliate con le immagini con SKU DBMS fanno riferimento [DBMS Guida alla distribuzione di] [dbms Guida]

Sarà simile a SAP specifica i passaggi distribuzione una macchina virtuale fuori Azure Marketplace:

![Diagramma di flusso della distribuzione di macchine Virtuali per sistemi SAP con un'immagine di macchine Virtuali da Azure Marketplace][deployment-guide-figure-100]

In seguito il diagramma di flusso è necessario eseguire la procedura seguente:

#### <a name="create-virtual-machine-using-the-azure-portal"></a>Creare macchina virtuale tramite il portale di Azure
Il modo più semplice per creare una nuova macchina virtuale con un'immagine da Azure Marketplace è tramite il portale Azure. Passare a <https://portal.azure.com/#create>. Immettere il tipo del sistema operativo che si desidera distribuire nel campo di ricerca, ad esempio Windows, SLES o RHEL e selezionare la versione. Assicurarsi di selezionare il modello di distribuzione "Gestione risorse di Azure" e fare clic su Crea.

La procedura guidata mostrerà tramite i parametri necessari per creare la macchina virtuale insieme a tutte le risorse necessarie come interfacce di rete o gli account di archiviazione. Alcuni di questi parametri sono:

1. Nozioni di base
    1. Nome: Il nome della risorsa, ovvero il nome del computer virtuale
    1. Nome utente e password/SSH chiave pubblica: immettere il nome utente e la password dell'utente che viene creato durante il provisioning. Per una macchina virtuale Linux, è inoltre possibile immettere la chiave SSH pubblica che si desidera utilizzare per l'accesso al computer utilizzando SSH.
    1. Abbonamento: Selezionare l'abbonamento che si desidera utilizzare per effettuare il provisioning di nuova macchina virtuale.
    1. Gruppo risorse: Il nome del gruppo di risorse. Inserire il nome di un nuovo gruppo di risorse o di un gruppo di risorse già esistente
    1. Posizione: Selezionare il percorso in cui devono essere distribuita la nuova macchina virtuale. Se si desidera connettere la macchina virtuale per la rete locale, assicurarsi di selezionare il percorso di rete virtuale Azure che si connette alla rete locale. Per ulteriori informazioni, vedere capitolo [Microsoft Azure Networking] [ planning-guide-microsoft-azure-networking] [Guida alla pianificazione di] [-Guida alla pianificazione].
1. Dimensione: Leggere nota SAP [1928533] per un elenco dei tipi di macchine Virtuali supportati. Vedere anche assicurarsi di selezionare il tipo corretto se si desidera utilizzare l'archiviazione Premium. Non tutti i tipi di macchine Virtuali di supporto Premium lo spazio di archiviazione. Vedere capitolo [lo spazio di archiviazione: lo spazio di archiviazione di Microsoft Azure e dischi dati] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] [Azure Premium archiviazione] e [pianificazione-Guida--premium-archiviazione azure] [Guida alla pianificazione di] [-Guida alla pianificazione] per altri dettagli.
1. Impostazioni
    1. Account di archiviazione: È possibile selezionare un account di archiviazione esistente o crearne uno nuovo. Leggere capitolo [archiviazione Microsoft Azure] [2.3 di Guida dbms] della [DBMS Guida] [dbms Guida] per altri dettagli sui tipi di archiviazione diversa. Si noti che non tutti i tipi di spazio di archiviazione sono supportati per l'esecuzione di applicazioni SAP.
    1. Virtuali e Subnet: selezionare la rete virtuale connessi alla rete locale se si vuole integrare la macchina virtuale intranet.
    1. Indirizzo IP pubblico: selezionare l'indirizzo IP pubblico che si desidera utilizzare oppure immettere i parametri per creare un nuovo indirizzo IP pubblico. È possibile utilizzare un indirizzo IP pubblico ad per accedere al computer virtuale tramite internet. Assicurarsi di anche creare un gruppo di sicurezza di rete per filtrare l'accesso al computer virtuale.
    1. Gruppo di sicurezza di rete: vedere [che cos'è un gruppo di sicurezza di rete (NSG)] [ virtual-networks-nsg] per altri dettagli.
    1. Monitoraggio: È possibile disattivare l'impostazione di diagnostica. Verrà attivato automaticamente quando si eseguono i comandi necessari per abilitare il monitoraggio di Azure Enhanced come descritto in capitolo [Configurazione monitoraggio][deployment-guide-configure-monitoring-scenario-1].
    1. Disponibilità: Selezionare un Set di disponibilità o immettere i parametri per creare un nuovo Set di disponibilità. Per ulteriori informazioni vedere capitolo [Azure disponibilità set] [pianificazione-Guida-3.2.3].
1. Riepilogo: Convalidare le informazioni fornite nella pagina di riepilogo e fare clic su OK.

Dopo aver procedura guidata, la macchina virtuale verrà distribuita nel gruppo di risorse selezionato.

#### <a name="create-virtual-machine-using-a-template"></a>Creare macchina virtuale utilizzando un modello
È anche possibile creare una distribuzione utilizzando uno dei modelli SAP pubblicati in [modelli di Guida introduttiva di azure github archivio][azure-quickstart-templates-github]. Oppure è possibile creare una macchina virtuale tramite il [Portale di Azure][virtual-machines-windows-tutorial], [PowerShell] [ virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] o [Azure CLI] [ virtual-machines-linux-tutorial] manualmente.

* [modello di configurazione di livello 2 (solo una macchina virtuale)] [ sap-templates-2-tier-marketplace-image] utilizzare questo modello se si desidera creare un sistema di livello 2 utilizzando solo una macchina virtuale.
* [modello di configurazione di livello 3 (più macchine virtuali)] [ sap-templates-3-tier-marketplace-image] utilizzare questo modello se si desidera creare un sistema di livello 3 utilizzando più macchine virtuali.

Dopo l'apertura di uno dei modelli in precedenza, il portale di Azure consente di passare al riquadro Modifica parametri. Immettere le informazioni seguenti:

* **sapSystemId**: ID di sistema SAP
* **osType**: sistema operativo che si desidera distribuire, ad esempio Windows Server 2012 R2, SLES 12 o RHEL 7.2
    * L'elenco contiene solo versioni supportate da SAP in Microsoft Azure
* **sapSystemSize**: le dimensioni del sistema SAP
    * La quantità di fornirà il nuovo sistema SAP. Se non si sa quante SAP il sistema richiederà, contattare il Partner Technology SAP o la integrazione dei sistemi
* **systemAvailability**: (solo modello di livello 3) la disponibilità del sistema 
    * Selezionare HA per una configurazione è adatta per l'installazione HA. Due server database e due server per la ASCS verrà creato.
* storageType: (solo modello di livello 2) tipo di spazio di archiviazione che deve essere utilizzato 
    * Per sistemi di dimensioni maggiori, è consigliabile l'utilizzo di spazio di archiviazione Premium. Per ulteriori informazioni sui tipi di archiviazione diversa, leggere 
        * [Microsoft Azure archiviazione] [dbms-Guida-2.3] di [dbms Guida] [DBMS Guida]
        * [Spazio di archiviazione Premium: Spazio di archiviazione High-Performance per carichi di lavoro di Azure macchina virtuale][storage-premium-storage-preview-portal]
        * [Introduzione all'archiviazione di Microsoft Azure][storage-introduction]
* **adminUsername** e **adminPassword**: nome utente e la password
    * Creazione di un nuovo utente che può essere utilizzato per accedere al computer.
* **newOrExistingSubnet**: determina se è necessario creare una nuova rete virtuale e subnet o una subnet esistente deve essere utilizzata. Se si dispone già di una rete virtuale connessi alla rete locale, selezionare esistente.
* **struttura**: l'ID di subnet a cui macchine virtuali deve essere connesso a. Selezionare la subnet della rete VPN o strada virtuale per connettere la macchina virtuale per la rete locale. L'ID in genere l'aspetto /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Dopo aver immesso tutti i parametri, selezionare l'abbonamento e il gruppo di risorse che si desidera utilizzare. È possibile selezionare un gruppo di risorse esistente o crearne uno nuovo selezionando "+ nuovo" nel menu a discesa. Se si crea un nuovo gruppo di risorse, è necessario selezionare l'area in cui verrà creati un gruppo di risorse e la macchina virtuale.

Esaminare le condizioni legali, accettarle e fare clic su Crea.

Si noti che l'agente di macchine Virtuali di Azure viene distribuito per impostazione predefinita, quando si usa un'immagine da Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Configurare le impostazioni del Proxy
A seconda della configurazione di rete locale, potrebbero essere necessaria per configurare il proxy sul computer virtuale è connesso alla rete locale tramite VPN o strada. In caso contrario la macchina virtuale potrebbe non essere possibile per l'accesso a internet e pertanto non è possibile scaricare estensioni necessarie o la raccolta dati di monitoraggio. Vedere [Configurare un Proxy di] capitolo[ deployment-guide-configure-proxy] di questo documento.

#### <a name="join-domain-windows-only"></a>Dominio (solo Windows)
Nel caso che la distribuzione di Azure sia connesso a locale in Active Directory/DNS tramite Azure a siti o strada (fare riferimento anche come Cross locale nel [pianificazione e implementazione Guide][planning-guide]), è probabile che la macchina virtuale viene aggiunta a un dominio locale. Considerazioni di questo passaggio sono descritti nel capitolo [partecipare macchine Virtuali nel dominio locale (solo Windows)] [4.3 di Guida alla distribuzione] di questo documento.

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurare il monitoraggio
Configurare l'Azure Enhanced monitoraggio estensione per SAP come descritto in capitolo [configurare Azure Enhanced monitoraggio estensione per SAP] [distribuzione-Guida-4.5] di questo documento.

Controllare i prerequisiti per il monitoraggio SAP per necessari minime versioni Kernel SAP e agente Host SAP in risorse elencate in capitolo [SAP risorse]. [2.2 di Guida alla distribuzione] di questo documento.

#### <a name="monitoring-check"></a>Controllo di monitoraggio
Verificare se il monitoraggio funziona come descritto in capitolo [verifica e risoluzione dei problemi per l'installazione di monitoraggio - to-End per SAP su Azure][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Passaggi di distribuzione post
Dopo aver creato la macchina virtuale, in cui verrà distribuito ed è quindi su installazione tutti i componenti software necessari in macchina virtuale. Pertanto questo tipo di distribuzione di macchine Virtuali richiedono software sia installato beeing già disponibile in Microsoft Azure in alcune altre macchine Virtuali o del disco che può essere collegato. O siamo in scenari tra locale in cui la connettività per le risorse locali (installare condivisioni) è un dato.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenario 2: Distribuire una macchina virtuale con un'immagine personalizzata per SAP
Come descritto in [pianificazione in corso e una Guida all'implementazione] la procedura dettagliata già in [-Guida alla pianificazione] esiste un modo per la preparazione e creare un'immagine personalizzata e usarlo per creare più macchine virtuali di nuove. La sequenza di passaggi nel diagramma di flusso sarà simile a:
 
![Diagramma di flusso della distribuzione di macchine Virtuali per sistemi SAP con un'immagine di macchine Virtuali in privato Marketplace][deployment-guide-figure-300]

In seguito il diagramma di flusso è necessario eseguire la procedura seguente:

#### <a name="create-virtual-machine"></a>Creare macchina virtuale
Per creare una distribuzione utilizzando un'immagine del sistema operativo privata tramite il portale di Azure, usare uno dei modelli SAP pubblicati in [modelli di Guida introduttiva di azure github archivio][azure-quickstart-templates-github].
È anche possibile creare una macchina virtuale utilizzando [PowerShell] [ virtual-machines-upload-image-windows-resource-manager] manualmente. 

* [modello di configurazione di livello 2 (solo una macchina virtuale)] [ sap-templates-2-tier-user-image] utilizzare questo modello se si desidera creare un sistema di livello 2 utilizzando solo una macchina virtuale e la propria immagine del sistema operativo.
* [modello di configurazione di livello 3 (più macchine virtuali)] [ sap-templates-3-tier-user-image] utilizzare questo modello se si desidera creare un sistema di livello 3 utilizzando più macchine virtuali e la propria immagine del sistema operativo.

Dopo l'apertura di uno dei modelli in precedenza, il portale di Azure consente di passare al riquadro Modifica parametri. Immettere le informazioni seguenti:

* **sapSystemId**: ID di sistema SAP
* **osType**: tipo di sistema operativo che si desidera distribuire Windows o Linux
* **sapSystemSize**: le dimensioni del sistema SAP
    * La quantità di fornirà il nuovo sistema SAP. Se non si sa quante SAP il sistema richiederà, contattare il Partner Technology SAP o la integrazione dei sistemi
* **systemAvailability**: (solo modello di livello 3) la disponibilità del sistema 
    * Selezionare HA per una configurazione è adatta per l'installazione HA. Due server database e due server per la ASCS verrà creato.
* **storageType**: (solo modello di livello 2) tipo di spazio di archiviazione che deve essere utilizzato 
    * Per sistemi di dimensioni maggiori, è consigliabile l'utilizzo di spazio di archiviazione Premium. Per ulteriori informazioni sui tipi di archiviazione diversa, leggere 
        * [Microsoft Azure archiviazione] [dbms-Guida-2.3] di [dbms Guida] [DBMS Guida]
        * [Spazio di archiviazione Premium: Spazio di archiviazione High-Performance per carichi di lavoro di Azure macchina virtuale][storage-premium-storage-preview-portal]
        * [Introduzione all'archiviazione di Microsoft Azure][storage-introduction]
* **adminUsername** e **adminPassword**: nome utente e la password
    * Creazione di un nuovo utente che può essere utilizzato per accedere al computer.
* **userImageVhdUri**: URI del file privato OS immagine ad esempio, https://`<accountname`>.blob.core.windows.net/vhds/userimage.vhd
* **userImageStorageAccount**: nome dell'account di archiviazione archiviazione ad esempio, l'immagine del sistema operativo privato `<accountname`> nell'esempio precedente URI
* **newOrExistingSubnet**: determina se è necessario creare una nuova rete virtuale e subnet o una subnet esistente deve essere utilizzata. Se si dispone già di una rete virtuale connessi alla rete locale, selezionare esistente.
* **struttura**: l'ID di subnet a cui macchine virtuali deve essere connesso a. Selezionare la subnet della rete VPN o strada virtuale per connettere la macchina virtuale per la rete locale. L'ID in genere l'aspetto /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Dopo aver immesso tutti i parametri, selezionare l'abbonamento e il gruppo di risorse che si desidera utilizzare. È possibile selezionare un gruppo di risorse esistente o crearne uno nuovo selezionando "+ nuovo" nel menu a discesa. Se si crea un nuovo gruppo di risorse, è necessario selezionare l'area in cui verrà creati un gruppo di risorse e la macchina virtuale.

Esaminare le condizioni legali, accettarle e fare clic su Crea.

#### <a name="install-vm-agent-linux-only"></a>Installare l'agente di macchine Virtuali (solo Linux)
Agente di Linux deve essere già installata nell'immagine utente se si desidera utilizzare i modelli sopra. In caso contrario la distribuzione avrà esito negativo. Scaricare e installare l'agente di macchine Virtuali nell'immagine utente come descritto in capitolo [scaricare, installare e abilitare l'agente di macchine Virtuali di Azure] [distribuzione-Guida-4.4] di questo documento.
Se non si usa i modelli in precedenza, è possibile installare anche l'agente di macchine Virtuali in un secondo momento.

#### <a name="join-domain-windows-only"></a>Dominio (solo Windows)
Nel caso che la distribuzione di Azure sia connesso a locale in Active Directory/DNS tramite Azure a siti o strada (fare riferimento anche come Cross locale nel [pianificazione e implementazione Guide][planning-guide]), è probabile che la macchina virtuale viene aggiunta a un dominio locale. Considerazioni di questo passaggio sono descritti nel capitolo [partecipare macchine Virtuali nel dominio locale (solo Windows)] [4.3 di Guida alla distribuzione] di questo documento.

#### <a name="configure-proxy-settings"></a>Configurare le impostazioni del Proxy
A seconda della configurazione di rete locale, potrebbero essere necessaria per configurare il proxy sul computer virtuale è connesso alla rete locale tramite VPN o strada. In caso contrario la macchina virtuale potrebbe non essere possibile per l'accesso a internet e pertanto non è possibile scaricare estensioni necessarie o la raccolta dati di monitoraggio. Vedere [Configurare un Proxy di] capitolo[ deployment-guide-configure-proxy] di questo documento.

#### <a name="configure-monitoring"></a>Configurare il monitoraggio
Configurare l'estensione di monitoraggio di Azure per SAP come descritto in capitolo [configurare Azure Enhanced monitoraggio estensione per SAP] [distribuzione-Guida-4.5] di questo documento.
Controllare i prerequisiti per il monitoraggio SAP per necessari minime versioni Kernel SAP e agente Host SAP in risorse elencate in capitolo [SAP risorse]. [2.2 di Guida alla distribuzione] di questo documento.

#### <a name="monitoring-check"></a>Controllo di monitoraggio
Verificare se il monitoraggio funziona come descritto in capitolo [verifica e risoluzione dei problemi per l'installazione di monitoraggio - to-End per SAP su Azure][deployment-guide-troubleshooting-chapter].

### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenario 3: Spostare una macchina virtuale locali con un disco rigido virtuale Azure non generalized SAP
Questo scenario è l'indirizzamento nel caso di un sistema SAP semplicemente vengano spostato nella relativa corrente e la forma locali Azure. Indica che nessun nome modificare di nome host Windows o Linux e SAP SID o qualcosa di simile a quella entrerà in vigore. In questo caso, il disco rigido virtuale fa riferimento non come immagine durante la distribuzione ma viene utilizzato direttamente come disco del sistema operativo. Per quanto riguarda alla distribuzione, in questo caso è diverso dal due casi ex dal fatto che l'agente di macchine Virtuali non può essere installato automaticamente durante la distribuzione. Agente di macchine Virtuali di Azure pertanto deve essere scaricato da Microsoft e deve essere installato e attivato all'interno della macchina virtuale manualmente in un secondo momento. Dopo l'attività è stata eseguita correttamente, è possibile continuare avviare l'estensione Azure monitoraggio Host SAP e la configurazione. Per informazioni dettagliate sulla funzione dell'agente di macchine Virtuali di Azure, consultare questo articolo:

[comment]: <> (Collegamento Windows Update MSSedusch TODO seguente) 

___

> ![Windows][Logo_Windows] Windows
>
> <http://blogs.msdn.com/b/wats/Archive/2014/02/17/BGInfo-guest-Agent-Extension-for-Azure-VMS.aspx>
>
> ![Linux][Logo_Linux] Linux
>
> [Guida di Azure Linux agente utente][virtual-machines-linux-agent-user-guide]

___

Il flusso di lavoro di diversi passaggi è simile alla:
 
![Diagramma di flusso di distribuzione di macchine Virtuali di sistemi SAP utilizzando un disco macchine Virtuali][deployment-guide-figure-400]

Supponendo che il disco è già caricato e definito in Azure (vedere [pianificazione e implementazione Guide][planning-guide]), seguire questa procedura

#### <a name="create-virtual-machine"></a>Creare macchina virtuale
Per creare una distribuzione utilizzando un disco OS privato tramite il portale di Azure, utilizzare il modello SAP pubblicato in [modelli di Guida introduttiva di azure github archivio][azure-quickstart-templates-github]. È anche possibile creare una macchina virtuale utilizzando il [PowerShell o Azure CLI manualmente.

* [modello di configurazione di livello 2 (solo una macchina virtuale)][sap-templates-2-tier-os-disk]
    * Utilizzare questo modello se si desidera creare un sistema di livello 2 utilizzando solo una macchina virtuale.

Dopo l'apertura del modello precedente, il portale di Azure consente di passare al riquadro Modifica parametri. Immettere le informazioni seguenti:

* **sapSystemId**: ID di sistema SAP
* **osType**: tipo di sistema operativo che si desidera distribuire Windows o Linux
* **sapSystemSize**: le dimensioni del sistema SAP
    * La quantità di fornirà il nuovo sistema SAP. Se non si sa quante SAP il sistema richiederà, contattare il Partner Technology SAP o la integrazione dei sistemi
* **storageType**: (solo modello di livello 2) tipo di spazio di archiviazione che deve essere utilizzato 
    * Per sistemi di dimensioni maggiori, è consigliabile l'utilizzo di spazio di archiviazione Premium. Per ulteriori informazioni sui tipi di archiviazione diversa, leggere 
        * [Microsoft Azure archiviazione] [dbms-Guida-2.3] di [dbms Guida] [DBMS Guida]
        * [Spazio di archiviazione Premium: Spazio di archiviazione High-Performance per carichi di lavoro di Azure macchina virtuale][storage-premium-storage-preview-portal]
        * [Introduzione all'archiviazione di Microsoft Azure][storage-introduction]
* **osDiskVhdUri**: URI del sistema operativo privato disco, ad esempio https://`<accountname`>.blob.core.windows.net/vhds/osdisk.vhd
* **newOrExistingSubnet**: determina se è necessario creare una nuova rete virtuale e subnet o una subnet esistente deve essere utilizzata. Se si dispone già di una rete virtuale connessi alla rete locale, selezionare esistente.
* **struttura**: l'ID di subnet a cui macchine virtuali deve essere connesso a. Selezionare la subnet della rete VPN o strada virtuale per connettere la macchina virtuale per la rete locale. L'ID in genere l'aspetto /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Dopo aver immesso tutti i parametri, selezionare l'abbonamento e il gruppo di risorse che si desidera utilizzare. È possibile selezionare un gruppo di risorse esistente o crearne uno nuovo selezionando "+ nuovo" nel menu a discesa. Se si crea un nuovo gruppo di risorse, è necessario selezionare l'area in cui verrà creati un gruppo di risorse e la macchina virtuale.

Esaminare le condizioni legali, accettarle e fare clic su Crea.

#### <a name="install-vm-agent"></a>Installare l'agente di macchine Virtuali
Agente di Linux deve essere già installata nel disco di sistema operativo se si desidera utilizzare i modelli sopra. In caso contrario la distribuzione avrà esito negativo. Scaricare e installare l'agente di macchine Virtuali nella macchina virtuale come descritto in capitolo [scaricare, installare e abilitare l'agente di macchine Virtuali di Azure] [distribuzione-Guida-4.4] di questo documento.

Se non si usa i modelli in precedenza, è possibile installare anche l'agente di macchine Virtuali in un secondo momento.

#### <a name="join-domain-windows-only"></a>Dominio (solo Windows)
Nel caso che la distribuzione di Azure sia connesso a locale in Active Directory/DNS tramite Azure a siti o strada (fare riferimento anche come Cross locale nel [pianificazione e implementazione Guide][planning-guide]), è probabile che la macchina virtuale viene aggiunta a un dominio locale. Considerazioni di questo passaggio sono descritti nel capitolo [partecipare macchine Virtuali nel dominio locale (solo Windows)] [4.3 di Guida alla distribuzione] di questo documento.

#### <a name="configure-proxy-settings"></a>Configurare le impostazioni del Proxy
A seconda della configurazione di rete locale, potrebbero essere necessaria per configurare il proxy sul computer virtuale è connesso alla rete locale tramite VPN o strada. In caso contrario la macchina virtuale potrebbe non essere possibile per l'accesso a internet e pertanto non è possibile scaricare estensioni necessarie o la raccolta dati di monitoraggio. Vedere [Configurare un Proxy di] capitolo[ deployment-guide-configure-proxy] di questo documento.

#### <a name="configure-monitoring"></a>Configurare il monitoraggio
Configurare Azure Enhanced monitoraggio estensione per SAP come descritto in capitolo [configurare Azure Enhanced monitoraggio estensione per SAP] [distribuzione-Guida-4.5] di questo documento.

Controllare i prerequisiti per il monitoraggio SAP per necessari minime versioni kernel SAP e agente Host SAP in risorse elencate in capitolo [SAP risorse]. [2.2 di Guida alla distribuzione] di questo documento.

#### <a name="monitoring-check"></a>Controllo di monitoraggio
Verificare se il monitoraggio funziona come descritto in capitolo [verifica e risoluzione dei problemi per l'installazione di monitoraggio - to-End per SAP su Azure][deployment-guide-troubleshooting-chapter].

### <a name="scenario-4-updating-the-monitoring-configuration-for-sap"></a>Scenario 4: Aggiornamento della configurazione monitoraggio per SAP
Esistono casi in cui è necessario aggiornare la configurazione di monitoraggio:

* Il team di MS/SAP comune esteso le funzionalità di monitoraggio e deciso di aggiungere più contatori o eliminare alcuni contatori. 
* Microsoft introduce una nuova versione dell'infrastruttura di Azure sottostante fornire i dati di monitoraggio e la Azure Enhanced monitoraggio estensione per SAP adeguamento a tali modifiche.
* Per aggiungere ulteriori dischi rigidi virtuali installati per la macchina virtuale Azure oppure si rimuove un disco rigido virtuale. In questo caso, è necessario aggiornare l'insieme di spazio di archiviazione dati correlati. Se si cambia la configurazione mediante l'aggiunta o eliminazione di endpoint o assegnazione di indirizzi IP per una macchina virtuale, il firewall non influisce sulle monitoraggio della configurazione.
* Modificate le dimensioni delle macchine Virtuali di Azure ad esempio da A5 a qualsiasi altra dimensione di macchine Virtuali.
* Aggiungere nuove interfacce di rete di Azure una macchina virtuale

Per aggiornare la configurazione del monitoraggio, procedere come segue:

* Aggiornare l'infrastruttura di monitoraggio seguendo i passaggi descritti in capitolo [configurare Azure Enhanced monitoraggio estensione per SAP] [distribuzione-Guida-4.5] di questo documento. Eseguire nuovamente dello script descritti in questo capitolo rileva una configurazione di monitoraggio viene distribuita che verrà eseguita automaticamente le modifiche necessarie alla configurazione di monitoraggio. 

___

> ![Windows][Logo_Windows] Windows
>
> Per l'aggiornamento dell'agente di macchine Virtuali di Azure, non è necessario alcun intervento dell'utente. Agente di macchine Virtuali automatico Aggiorna se stesso e non richiede il riavvio macchine Virtuali.
>
> ![Linux][Logo_Linux] Linux
>
> Seguire i passaggi descritti in [questo articolo] [ virtual-machines-linux-update-agent] per aggiornare l'agente di Linux Azure. 

___

## <a name="detailed-single-deployment-steps"></a>Dettagliate passaggi di distribuzione singola

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>La distribuzione dei cmdlet di PowerShell di Azure
* Passare a <https://azure.microsoft.com/downloads/>
* Nella sezione 'Della riga di comando strumenti' è una sezione denominata 'Windows PowerShell'. Fare clic sul collegamento "Installa".
* Gestione di scaricare Microsoft verrà visualizzata con una voce che terminano con .exe. Selezionare l'opzione "Esegui".
* Verrà visualizzata con una finestra popup in cui viene chiesto se si desidera eseguire il programma di installazione di Microsoft Web piattaforma. Premere Sì
* Viene visualizzata una schermata analogo al seguente:
 
![Schermata di installazione per i cmdlet di PowerShell di Azure][deployment-guide-figure-500]
<a name="figure-5"></a>

* Premere installa e accettare il contratto di licenza.

Controllare di frequente se sono stati aggiornati i cmdlet di PowerShell. In genere sono disponibili aggiornamenti per un periodo mensile. Il modo più semplice per eseguire questa operazione consiste nell'eseguire la procedura di installazione, come descritto in precedenza fino alla schermata di installazione visualizzata in [questa] [ deployment-guide-figure-5] figura. In questa schermata viene visualizzata la data di rilascio dei cmdlet di e il numero di versione effettivo. Se non diversamente specificato in modo diverso nelle note SAP [1928533] o [2015553], si consiglia di lavorare con la versione più recente di cmdlet PowerShell di Azure.

È possibile verificare la versione corrente di Azure cmdlet sul desktop o portatile con il comando PS:

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

Presentare il risultato come illustrato di seguito in [questo] [ deployment-guide-figure-6] figura.

![Risultato della verifica della versione cmdlet PS Azure][deployment-guide-figure-600]
<a name="figure-6"></a>

Se la versione di Azure cmdlet installata sul desktop o portatile è quella corrente, nella prima schermata dopo l'avvio di Microsoft Web piattaforma Installer avrà un aspetto leggermente diversa rispetto a quello illustrato di seguito in [questo] [ deployment-guide-figure-5] figura.

Notare, seguito dal cerchio rosso nella [Figura] [ deployment-guide-figure-7] sotto.
 
![Schermata di installazione per i cmdlet di PowerShell Azure che indica che la versione più recente di Azure PS cmdlet siano installati][deployment-guide-figure-700]
<a name="figure-7"></a>

Se la schermata appare come [sopra][deployment-guide-figure-7], che indica che la versione più recente di Azure cmdlet è già installata, è necessario continuare con l'installazione. In questo caso è possibile 'uscire' l'installazione in questa fase.

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Distribuzione di Azure CLI
* Passare a <https://azure.microsoft.com/downloads/>
* Nella sezione 'Della riga di comando strumenti' è una sezione denominata "Interfaccia della riga di comando Azure". Seguire il collegamento di installazione del sistema operativo.

Controllare di frequente se CLI Azure sono stati aggiornati. In genere sono disponibili aggiornamenti per un periodo mensile. Il modo più semplice per eseguire questa operazione consiste nel seguire la procedura di installazione come descritto in precedenza.

È possibile verificare la versione corrente installata di Azure CLI sul desktop o portatile con il comando:

```
azure --version
```

Presentare il risultato come illustrato di seguito in [questo] [ deployment-guide-figure-azure-cli-version] figura.

![Risultato della verifica versione CLI Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Partecipare a macchine Virtuali nel dominio locale (solo Windows)
Nei casi in cui si distribuirle SAP in uno scenario di croce locale in Active Directory locale e DNS viene esteso in Azure, è probabile che le macchine virtuali fanno parte di un dominio locale. Le procedure dettagliate di partecipazione a una macchina virtuale per un dominio locale e di software aggiuntivo devono per essere che un membro di un dominio locale è cliente dipendenti. In genere come partecipare a una macchina virtuale a un dominio locale indica che l'installazione di software aggiuntivo come software di protezione da Malware o vari agenti del software di backup o monitoraggio.

Inoltre, è necessario assicurarsi che nei casi in cui le impostazioni proxy Internet risulteranno quando si partecipa a un dominio, che il Account(S-1-5-18) sistema locale Windows nella macchina virtuale Guest sono previsti anche queste impostazioni. Più semplice consiste nel forzare il proxy con dominio i criteri di gruppo che si applicano ai sistemi all'interno del dominio.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Scaricare, installare e abilitare l'agente di macchine Virtuali di Azure
I passaggi seguenti sono necessari quando una macchina virtuale per SAP viene distribuito da un immagini del sistema operativo non generalizzata ad esempio non preparata con Sysprep per Windows. Non è necessario installare l'agente per macchine virtuali di distribuzione di Azure Marketplace. Queste immagini contengano già l'agente di Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

* Scaricare l'agente di Azure macchine Virtuali:
    * Scaricare il pacchetto di installazione dell'agente macchine Virtuali di Azure da: <https://go.microsoft.com/fwlink/?LinkId=394789>
    * Archiviare il pacchetto MSI agente macchine Virtuali in locale nel portatile o un server
* Installare l'agente di Azure macchine Virtuali:
    * Connettersi a macchine Virtuali di Azure distribuito con servizi Terminal (RDP)
    * Aprire una finestra di Esplora risorse nella macchina virtuale e una directory di destinazione per il file MSI dell'agente di macchine Virtuali
    * Trascinare e rilasciare il file di programma di installazione MSI di Azure macchine Virtuali agente dal server di portatile locale nella directory di destinazione dell'agente di macchine Virtuali nella macchina virtuale
    * Fare doppio clic sul file MSI nella macchina virtuale
    * Per macchine Virtuali connessi ai domini locale, verificare come assicurarsi che le impostazioni proxy Internet eventuale applicano per l'account di sistema locale di Windows (S-1-5-18) nella macchina virtuale come descritto in capitolo [Configurare un Proxy di][deployment-guide-configure-proxy]. Agente di macchine Virtuali verrà eseguita in questo contesto e deve essere in grado di connettersi Azure.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Installare l'agente di macchine Virtuali per Linux utilizzando il seguente comando

- **SLES**

```
sudo zypper install WALinuxAgent
```
- **RHEL**

```
sudo yum install WALinuxAgent
```

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configurare un Proxy
La procedura per la configurazione del proxy si differenziano tra Windows e Linux.

#### <a name="windows"></a>Windows
Queste impostazioni devono essere valide per l'account LocalSystem accedere a Internet. Se le impostazioni del proxy non sono impostate dai criteri di gruppo, è possibile configurarli per l'account di sistema locale di eseguire la procedura per la configurazione.

1.  Aprire gpedit
1.  Passare a configurazione Computer –> modelli amministrativi -> componenti di Windows -> Internet Explorer e Abilita "proxy impostazioni per computer (anziché per utente)
1.  Aprire il pannello di controllo e passare alla rete e Internet -> Opzioni Internet
1.  Aprire la scheda connessioni e fare clic su Impostazioni LAN
1.  Disabilitare "Rileva automaticamente impostazioni"
1.  Abilitare "Usare un server proxy per la rete LAN" e immettere l'host proxy e la porta

#### <a name="linux"></a>Linux
Configurare il proxy corretto nel file di configurazione di Microsoft Azure Guest Agent che si trova /etc/waagent.conf. Impostare i parametri seguenti:

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

Riavviare l'agente dopo avere modificato la configurazione con

```
sudo service waagent restart
```

Le impostazioni del proxy in /etc/waagent.conf anche richiedere estensioni macchine Virtuali necessarie. Se si desidera utilizzare archivi Azure, assicurarsi che il traffico a questi archivi non attraversano intranet locale. Se è stato creato route definiti dall'utente per attivare forzata Tunneling, assicurarsi di aggiungere una route che indirizza il traffico al archivi direttamente a Internet e non attraverso la connessione al sito.

- **SLES** Inoltre, è necessario aggiungere route per gli indirizzi IP elencati nella /etc/regionserverclnt.cfg. Nella schermata seguente è illustrato un esempio. 

- **RHEL** Inoltre, è necessario aggiungere route per gli indirizzi IP degli host elencati in /etc/yum.repos.d/rhui-load-balancers. Nella schermata seguente è illustrato un esempio.

Per ulteriori informazioni sulle route definite dall'utente, vedere [l'articolo][virtual-networks-udr-overview].

![Forzata Tunneling][deployment-guide-figure-50]

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurare Azure estensione monitoraggio avanzato per SAP
Quando la macchina virtuale è pronto come descritto in capitolo [distribuzione scenari di macchine virtuali per SAP in Microsoft Azure] [distribuzione Guida 3], l'agente di macchine Virtuali di Azure è installato nel computer. Il passaggio successivo importante è per la distribuzione di Azure Enhanced monitoraggio estensione per SAP, è disponibile nell'archivio di estensione Azure nei data center globale di Microsoft Azure. Per informazioni dettagliate, controllare [pianificazione in corso e una Guida all'implementazione] [pianificazione-Guida-9.1]. 

È possibile usare PowerShell Azure o Azure CLI per installare e configurare l'Azure Enhanced monitoraggio estensione per SAP. Leggere capitolo Azure PowerShell [distribuzione-Guida-4.5.1] Se si desidera installare l'estensione in Windows o Linux VM tramite un computer Windows. Per l'installazione l'estensione in una VM Linux usando un Linux desktop leggere capitolo del mese di Azure CLI [4.5.2 di Guida alla distribuzione].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell per Linux e macchine virtuali di Windows
Per eseguire il processo di installazione di Azure Enhanced monitoraggio estensione per SAP, procedere come segue:

* Assicurarsi di avere installato la versione più recente del cmdlet Microsoft Azure PowerShell. Vedere capitolo [cmdlet per la distribuzione di Azure PowerShell] [distribuzione-Guida-4.1] di questo documento.  
* Eseguire il seguente cmdlet di PowerShell. Per un elenco di ambienti disponibili, eseguire il cmdlet Get-AzureRmEnvironment. Se si desidera utilizzare Azure pubblico, l'ambiente è AzureCloud. Per Azure in Cina, selezionare AzureChinaCloud.

```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Login-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>
    
    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

Dopo che sono stati i dati e account Azure Virtual Machine, lo script verrà distribuire estensioni necessarie e attivare le funzionalità necessarie. Possono essere necessari alcuni minuti.
Leggere [questo articolo MSDN] [ msdn-set-azurermvmaemextension] per ulteriori informazioni sui Set-AzureRmVMAEMExtension.
  
![Schermata dei risultati di successo di SAP specifico Azure cmdlet Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

Esecuzione di Set di AzureRmVMAEMExtension produrrà tutti i passaggi necessari per configurare l'host il monitoraggio delle funzionalità per SAP. 

L'output che lo script dovrebbe offrire l'aspetto:

* Conferma che la configurazione di monitoraggio per il disco rigido virtuale di Base, che include il sistema operativo, più tutti dischi rigidi virtuali aggiuntivi installata per la macchina virtuale è stata configurata.
* I messaggi di due conferma la configurazione di metriche di archiviazione per un account di archiviazione specifico. 
* Una riga di output per ottenere uno stato sull'aggiornamento effettivo della configurazione di monitoraggio.
* Un altro appariranno per confermare che la configurazione è stata distribuita o aggiornata.
* L'ultima riga di output è informativo con la possibilità di testare la configurazione del monitoraggio.
* Per verificare che tutti i passaggi del monitoraggio di Azure Enhanced sono stati eseguiti correttamente e che l'infrastruttura di Azure fornisce i dati necessari, procedere con la verifica della preparazione per Azure Enhanced monitoraggio estensione per SAP, come descritto in capitolo [per la preparazione per verificare la presenza di Azure Enhanced monitoraggio per SAP] [distribuzione-Guida-5.1] in questo documento. 
* Per continuare a questa operazione, attendere 15-30 minuti fino a diagnostica Azure avrà i relativi dati raccolti.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI per macchine virtuali Linux

Per eseguire il processo di installazione di Azure Enhanced monitoraggio estensione per SAP usa CLI Azure, procedere come segue:

1. Installare Azure CLI come descritto in [questo] [ azure-cli] articolo
1. Eseguire l'accesso con l'account Azure

    ```
    azure login
    ```
1. Passare alla modalità di gestione risorse di Azure

    ```
    azure config mode arm
    ```
1. Attivare il monitoraggio avanzato Azure

    ```
    azure vm enable-aem <resource-group-name> <vm-name>
    ```  
1. Verificare che il monitoraggio di Azure Enhanced sia attivo il supporto virtuale Linux Azure. Verifica l'esistenza di /var/lib/AzureEnhancedMonitor/PerfCounters file. Se è presente, Visualizza le informazioni raccolte da AEM con:

    ```
    cat /var/lib/AzureEnhancedMonitor/PerfCounters
    ```
    Si otterrà output come:
    
    ```
    2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
    2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
    …
    …
    ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Verifica e risoluzione dei problemi per l'installazione di monitoraggio-to-End per SAP su Azure
Dopo aver distribuito le macchine Virtuali Azure e configurare l'infrastruttura di monitoraggio Azure pertinenti, controllare se tutti i componenti del Azure Enhanced monitoraggio funzionano in modo corretto. 

Pertanto, eseguire la verifica della preparazione per l'Azure Enhanced monitoraggio estensione per SAP come descritto in capitolo [per la preparazione per verificare la presenza di Azure Enhanced monitoraggio per SAP] [5.1 di Guida alla distribuzione]. Se il risultato di questo controllo è positivo e viene visualizzato tutto contatori pertinenti, monitoraggio Azure è stata impostata correttamente. In questo caso, procedere con l'installazione dell'agente SAP Host come descritto nelle note SAP elencati in capitolo [SAP risorse]. [2.2 di Guida alla distribuzione] di questo documento. Se il risultato della verifica preparazione indica contatori mancanti, continuare a eseguire la verifica dell'integrità per l'infrastruttura di monitoraggio di Azure, come descritto in capitolo [verifica dell'integrità per la configurazione dell'infrastruttura di monitoraggio di Azure] [5.2 di Guida alla distribuzione]. In caso di problemi con la configurazione di monitoraggio di Azure, controllare capitolo [la risoluzione del problema dell'infrastruttura di monitoraggio di Azure per SAP] [distribuzione-Guida-5.3] per ulteriori informazioni sulla risoluzione dei problemi.

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Verifica della preparazione per il monitoraggio Azure avanzata per SAP
Con questo controllo assicurarsi che le metriche che verranno visualizzate all'interno dell'applicazione SAP siano state completamente da infrastruttura monitoraggio di Azure. 

#### <a name="execute-the-readiness-check-on-a-windows-vm"></a>Eseguire la verifica della preparazione in una macchina virtuale di Windows
Per eseguire la verifica della preparazione, accesso al computer virtuale Azure (account di amministratore non è necessario) ed eseguire la procedura seguente:

* Aprire un prompt dei comandi di Windows e passare alla cartella di installazione dell'estensione di monitoraggio di Azure per SAP C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`> \drop

Parte versione fornita nel percorso di estensione monitoraggio sopra può variare. Se viene visualizzato più cartelle della versione estensione monitoraggio nella cartella di installazione, verificare la configurazione del servizio Windows 'AzureEnhancedMonitoring' e passare alla cartella indicata come 'Percorso file eseguibile'.
 
![Proprietà del servizio in esecuzione la Azure Enhanced monitoraggio estensione per SAP][deployment-guide-figure-1000]

* Eseguire azperflib.exe nella finestra di comando senza parametri.

> [AZURE.NOTE] Il azperflib.exe viene eseguito in un ciclo e aggiorna contatori raccolti ogni 60 secondi. Per completare il ciclo continuo, chiudere la finestra di comando.

Se l'estensione di monitoraggio Enhanced Azure non è installato o il servizio 'AzureEnhancedMonitoring' non è in esecuzione, l'estensione non è stato configurato correttamente. In questo caso, seguire capitolo [la risoluzione del problema dell'infrastruttura di monitoraggio di Azure per SAP] [distribuzione-Guida-5.3] per informazioni dettagliate come ridistribuire l'estensione.

##### <a name="check-the-output-of-azperflibexe"></a>Controllare l'output della azperflib.exe
L'output di azperflib.exe mostra che tutti popolati contatori Azure per SAP. Nella parte inferiore dell'elenco di contatori raccolti, trovare un riepilogo e un indicatore di stato che indica lo stato del monitoraggio Azure. 
 
![Output della verifica dell'integrità eseguendo azperflib.exe che indica che non si verificano problemi esiste][deployment-guide-figure-1100]
<a name="figure-11"></a>

Controllare il risultato restituito per l'output dell'importo totale' contatori', che vengono segnalati come vuoto e per la verifica dell'integrità' ' come mostrato in precedenza per la figura [sopra][deployment-guide-figure-11].

È possibile interpretare i valori dei risultati, come indicato di seguito:

| Valori Azperflib.exe risultato | Azure monitorare lo stato di disponibilità |
| ------------------------------|----------------------------------- |
| **Totale contatori: vuoto** | I seguenti contatori archiviazione Azure 2 possono essere vuoti: <ul><li>Operazione latenza di lettura dello spazio di archiviazione MS Server</li><li>Operazione latenza di lettura dello spazio di archiviazione E2E MS</li></ul>Tutti gli altri contatori devono contenere valori. |
| **Verifica dell'integrità** | Stato OK se restituito Mostra solo OK |

Se non restituiscono valori di azperflib.exe mostrano tutti i contatori popolati vengono restituiti correttamente, seguire le istruzioni della verifica dell'integrità per la configurazione dell'infrastruttura di monitoraggio di Azure come descritto in capitolo [verifica dell'integrità per la configurazione dell'infrastruttura di monitoraggio di Azure] [distribuzione-Guida-5.2] sotto.

#### <a name="execute-the-readiness-check-on-a-linux-vm"></a>Eseguire la verifica della preparazione in una VM Linux
Per eseguire la verifica della preparazione metta SSH la macchina virtuale Azure ed eseguire la procedura seguente:

* Controllare l'output di Azure Enhanced monitoraggio estensione
    * altre /var/lib/AzureEnhancedMonitor/PerfCounters
        * Deve fornire un elenco di contatori. Il file non deve essere vuoto
    * Cat /var/lib/AzureEnhancedMonitor/PerfCounters | GREP errore
        * Deve restituire un'unica riga nel punto in cui l'errore si è Nessuno, ad esempio 3; config; Errore; 0; 0; **Nessuno**; 0; 1456416792; tst servercs;
    * altre /var/lib/AzureEnhancedMonitor/LatestErrorRecord
        * Dovrebbe essere vuoto o non incluso
* Se il primo controllo precedente non è riuscito, eseguire i test aggiuntivi:
    * Assicurarsi che la waagent viene installato e avviato
        * Sudo ls-al/var/lib/waagent /
            * indicare il contenuto della directory waagent
        * PS-ax | GREP waagent
            * deve essere visualizzata una voce simile a "/usr/sbin/waagent python-daemon'
    * Assicurarsi che l'estensione Linux Diagnostics viene installata e avviata
        * Sudo Mostra - c ' ls-/var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-* al'
            * indicare il contenuto della directory estensione diagnostica Linux
        * PS-ax | GREP diagnostica
            * deve essere visualizzata una voce simile a "/var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py python-daemon'
    * Assicurarsi che l'estensione di monitoraggio Enhanced Azure viene installata e avviata
        * Sudo Mostra - c ' ls-/var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/ al'
            * indicare il contenuto della directory estensione monitoraggio Enhanced Azure
        * PS-ax | GREP AzureEnhanced
            * deve essere visualizzata una voce simile a 'daemon /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py python'
* Installare l'agente di Host SAP come descritto nella nota SAP [1031096] e controllare l'output della saposcol
    * Eseguire /usr/sap/hostctrl/exe/saposcol -d
    * Eseguire ccm dettagli
    * Verificare se l'unità di misura metriche "Virtualization_Configuration\Enhanced monitoraggio Access" è vero
* Se si dispone già di un server di applicazioni SAP NetWeaver ABAP installato, aprire transazione ST06 e controllare se è attivato il monitoraggio avanzato.

Se uno dei controlli sopra fail, seguire capitolo [la risoluzione del problema dell'infrastruttura di monitoraggio di Azure per SAP] [distribuzione-Guida-5.3] per informazioni dettagliate come ridistribuire l'estensione.

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Verifica dell'integrità per la configurazione dell'infrastruttura di monitoraggio di Azure
Se alcune del monitoraggio dei dati non viene recapitati correttamente come indicato dalla prova descritta in capitolo [per la preparazione per verificare la presenza di Azure Enhanced monitoraggio per SAP] [5.1 di Guida alla distribuzione] sopra, eseguire il cmdlet AzureRmVMAEMExtension Test per verificare se la configurazione corrente dell'infrastruttura Azure monitoraggio e l'estensione di monitoraggio SAP sia corretta.

Per testare la configurazione del monitoraggio, eseguire la sequenza di seguito:

* Assicurarsi che sia installato la versione più recente del cmdlet Microsoft Azure PowerShell come descritto nel capitolo [cmdlet per la distribuzione di Azure PowerShell] [distribuzione-Guida-4.1] di questo documento.
* Eseguire il seguente cmdlet di PowerShell. Per un elenco di ambienti disponibili, eseguire il cmdlet Get-AzureRmEnvironment. Se si desidera utilizzare Azure pubblico, l'ambiente è AzureCloud. Per Azure in Cina, selezionare AzureChinaCloud.

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* Dopo che sono stati i dati e account Azure Virtual Machine, lo script verificherà la configurazione della macchina virtuale che scelto.

 
![Schermata di immissione del cmdlet di Azure specifico SAP VMConfigForSAP_GUI Test][deployment-guide-figure-1200]

Dopo aver immesso le informazioni sull'account e la macchina virtuale di Azure, lo script verificherà la configurazione della macchina virtuale che scelto.
 
![Output di test ha esito positivo dell'infrastruttura di monitoraggio di Azure per SAP][deployment-guide-figure-1300]

Verificare che ogni controllo è contrassegnata con OK. Se alcuni dei controlli non sono ok, eseguire il cmdlet di aggiornamento come descritto in capitolo [configurare Azure Enhanced monitoraggio estensione per SAP] [distribuzione-Guida-4.5] di questo documento. Attendere 15 minuti a un'altra ed eseguire le verifiche descritte in capitolo [per la preparazione per verificare la presenza di Azure Enhanced monitoraggio per SAP] [5.1 di Guida alla distribuzione] e [verifica dell'integrità per la configurazione dell'infrastruttura di monitoraggio di Azure] [distribuzione-Guida-5.2] nuovamente. Se i controlli indicano ancora un problema con contatori alcuni o tutti, procedere al capitolo [la risoluzione del problema dell'infrastruttura di monitoraggio di Azure per SAP] [5.3 di Guida alla distribuzione].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>La risoluzione del problema dell'infrastruttura di monitoraggio di Azure per SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Azure contatori non mostrare tutto
La raccolta la misurazione delle prestazioni in Azure viene eseguita dal servizio di Windows 'AzureEnhancedMonitoring'. Se il servizio non è stato installato correttamente o se non è in esecuzione nelle macchine Virtuali, Nessuna metrica prestazioni può essere raccolti tutto.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Directory di installazione di estensione Enhanced monitoraggio Azure non contiene alcun dato 

###### <a name="issue"></a>Problema
Directory di installazione C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`> \drop non contiene alcun dato.

###### <a name="solution"></a>Soluzione
L'estensione non è installato. Verificare che sia un problema di proxy (come descritto in precedenza). Potrebbe essere necessario riavviare il computer e/o eseguire di nuovo lo script di script AzureRmVMAEMExtension Set di configurazione

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Servizio per Azure Enhanced monitoraggio non esiste. 

###### <a name="issue"></a>Problema
Servizio Windows 'AzureEnhancedMonitoring' non esiste. Azperflib.exe: L'output azperlib.exe genera un errore come illustrato nella [figura riportata di seguito][deployment-guide-figure-14].
 
![Esecuzione di azperflib.exe indica che il servizio di Azure Enhanced monitoraggio estensione per SAP non è in esecuzione][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Soluzione
Se il servizio non esiste come illustrato nella [figura precedente][deployment-guide-figure-14], l'estensione di monitoraggio di Azure per SAP non è stato installato correttamente. Ridistribuire l'estensione secondo la procedura descritta in per lo scenario di distribuzione in capitolo [distribuzione scenari di macchine virtuali per SAP in Microsoft Azure] [distribuzione-Guida-3]. 

Dopo la distribuzione dell'estensione, ricontrollare se i contatori Azure vengono forniti all'interno della macchina virtuale Azure dopo 1 ora.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Il servizio per Azure Enhanced monitoraggio è presente ma non è possibile avviare 

###### <a name="issue"></a>Problema
Servizio Windows 'AzureEnhancedMonitoring' esiste ed è abilitato, ma non è possibile avviare. Controllare il registro eventi di applicazione per ulteriori informazioni.

###### <a name="solution"></a>Soluzione
Configurazione errata. Abilitare nuovamente l'estensione monitoraggio per la macchina virtuale come descritto in capitolo [configurare Azure Enhanced monitoraggio estensione per SAP] [4.5 di Guida alla distribuzione].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Alcuni contatori Azure mancanti
La raccolta la misurazione delle prestazioni in Azure viene eseguita dal servizio di Windows 'AzureEnhancedMonitoring', che ottiene i dati da diverse origini. Alcuni dati di configurazione sono stati raccolti in locale, dati sulle prestazioni vengono letti da Azure diagnostica e lo spazio di archiviazione contatori utilizzati dalla registrazione a livello di sottoscrizione di spazio di archiviazione.

Se la risoluzione dei problemi tramite SAP nota [1999351] non Guida, eseguire nuovamente lo script di configurazione AzureRmVMAEMExtension Set. Potrebbe essere necessario attendere un'ora perché lo spazio di archiviazione analitica o diagnostica contatori potrebbero non essere creati immediatamente dopo l'abilitazione. Se il problema persiste, aprire un messaggio di supporto tecnico clienti SAP sul componente BC-operazione-NT-AZR.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Azure contatori non mostrare tutto

La raccolta la misurazione delle prestazioni in Azure viene eseguita una deamon. Se il deamon non è in esecuzione, Nessuna metrica prestazioni può essere raccolti tutto.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Directory di installazione di estensione Enhanced monitoraggio Azure non contiene alcun dato 

###### <a name="issue"></a>Problema
La directory/var raccolta/waagent/non contiene una sottodirectory per l'estensione Enhanced monitoraggio Azure.

###### <a name="solution"></a>Soluzione
L'estensione non è installato. Verificare che sia un problema di proxy (come descritto in precedenza). Potrebbe essere necessario riavviare il computer e/o eseguire di nuovo lo script di configurazione AzureRmVMAEMExtension Set

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Alcuni contatori Azure mancanti

La raccolta la misurazione delle prestazioni in Azure viene eseguita daemon, che ottiene i dati da diverse origini. Alcuni dati di configurazione sono stati raccolti in locale, dati sulle prestazioni vengono letti da Azure diagnostica e lo spazio di archiviazione contatori utilizzati dalla registrazione a livello di sottoscrizione di spazio di archiviazione.

Per un completo e aggiornato elenco di problemi noti vedere nota SAP [1999351] contenente ulteriori informazioni sulla risoluzione dei problemi per Enhanced Azure monitoraggio per SAP.

Se la risoluzione dei problemi tramite SAP nota [1999351] non è stato utile, eseguire nuovamente lo script di configurazione AzureRmVMAEMExtension Set come descritto in capitolo [configurare Azure Enhanced monitoraggio estensione per SAP] [4.5 di Guida alla distribuzione]. Potrebbe essere necessario attendere un'ora perché lo spazio di archiviazione analitica o diagnostica contatori potrebbero non essere creati immediatamente dopo l'abilitazione. Se il problema persiste, aprire un messaggio di supporto tecnico clienti SAP sul componente BC-operazione-NT-AZR per Windows o BC-operazione-LNX-AZR per una macchina virtuale Linux.
