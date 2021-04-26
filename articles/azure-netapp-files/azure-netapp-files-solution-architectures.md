---
title: Azure NetApp Files kullanan çözüm mimarileri | Microsoft Docs
description: Azure NetApp Files kullanarak çözüm mimarileri için en iyi yöntemlere başvurular sağlar.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: b-juche
ms.openlocfilehash: d5ff0f2444aac153116e8e33a6ed2af74c93f057
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553524"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Azure NetApp Files kullanan çözüm mimarileri
Bu makalede, Azure NetApp Files kullanmaya yönelik çözüm mimarilerini anlamanıza yardımcı olabilecek en iyi yöntemlere başvurular sağlanmaktadır.  

Aşağıdaki diyagramda Azure NetApp Files sunduğu çözüm mimarilerinin kategorileri özetlenmektedir:

![Çözüm mimarisi kategorileri](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS uygulamaları ve veritabanı çözümleri

Bu bölüm, Linux OSS uygulamalarına ve veritabanlarına yönelik çözümler için başvurular sağlar. 

### <a name="oracle"></a>Oracle

* [Azure dağıtım Azure NetApp Files kullanarak en iyi Yöntem Kılavuzu](https://www.netapp.com/us/media/tr-4780.pdf)
* [Oracle VM görüntüleri ve bunların Microsoft Azure dağıtımı: paylaşılan depolama yapılandırma seçenekleri](../virtual-machines/workloads/oracle/oracle-vm-solutions.md#shared-storage-configuration-options)
* [Azure NetApp Files tek birimlerinde Oracle veritabanı performansı](performance-oracle-single-volumes.md)
* [Oracle Database ile Azure NetApp Files kullanmanın avantajları](solutions-benefits-azure-netapp-files-oracle-database.md)

### <a name="machine-learning"></a>Machine Learning
*   [Cloudera Machine Learning](https://docs.cloudera.com/machine-learning/cloud/requirements-azure/topics/ml-requirements-azure.html)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows Uygulamaları ve SQL Server çözümleri

Bu bölüm, Windows Uygulamaları ve SQL Server çözümleri için başvurular sağlar.

### <a name="file-sharing-and-global-file-caching"></a>Dosya paylaşımı ve genel dosya önbelleğe alma

* [Kendi Azure NFS 'Nizi oluşturun mi? Linux dosya paylaşımlarını buluta paketleyin](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares)
* [Genel dosya önbelleği/Azure NetApp Files dağıtımı](https://youtu.be/91LKb1qsLIM)
* [Azure NetApp Files için bulut uyumluluğu](https://cloud.netapp.com/hubfs/Cloud%20Compliance%20for%20Azure%20NetApp%20Files%20-%20November%202020.pdf)

### <a name="sql-server"></a>SQL Server

* [Azure dağıtım kılavuzu 'nda Azure NetApp Files kullanarak SQL Server](https://www.netapp.com/pdf.html?item=/media/27154-tr-4888.pdf)
* [SQL Server dağıtımı için Azure NetApp Files kullanmanın avantajları](solutions-benefits-azure-netapp-files-sql-server.md)
* [Azure NetApp Files SMB üzerinden SQL Server dağıtma](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Azure NetApp Files ile SMB üzerinden SQL Server Always-On yük devretme kümesi dağıtma](https://www.youtube.com/watch?v=zuNJ5E07e8Q) 
* [Azure NetApp Files ile Always-On kullanılabilirlik grupları dağıtma](https://www.youtube.com/watch?v=y3VQmzzeyvc) 

## <a name="sap-on-azure-solutions"></a>Azure çözümlerinde SAP

Bu bölümde, Azure çözümlerinde SAP başvuruları sağlanmaktadır. 

### <a name="generic-sap-and-sap-netweaver"></a>Genel SAP ve SAP NetWeaver 

* [Azure NetApp Files kullanarak Microsoft Azure SAP uygulamaları](https://www.netapp.com/us/media/tr-4746.pdf)
* [SAP uygulamaları için Azure NetApp Files SUSE Linux Enterprise Server üzerindeki Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](../virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files.md)
* [SAP uygulamaları için Azure NetApp Files Red Hat Enterprise Linux üzerindeki Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](../virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files.md)
* [SAP uygulamaları için Azure NetApp Files (SMB) ile Windows üzerinde Azure VM 'lerinde SAP NetWeaver için yüksek kullanılabilirlik](../virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb.md)
* [SAP NetWeaver için Red Hat Enterprise Linux Azure VM 'lerde yüksek kullanılabilirlik çoklu SID Kılavuzu](../virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid.md)

### <a name="sap-hana"></a>SAP HANA 

* [SAP HANA Azure sanal makine depolama alanı yapılandırmaları](../virtual-machines/workloads/sap/hana-vm-operations-storage.md)
* [SAP HANA için Azure NetApp Files üzerinde NFS v4.1 birimleri](../virtual-machines/workloads/sap/hana-vm-operations-netapp.md)
* [Red Hat Enterprise Linux Azure NetApp Files ile SAP HANA ölçeği yüksek kullanılabilirliği](../virtual-machines/workloads/sap/sap-hana-high-availability-netapp-files-red-hat.md)
* [SUSE Linux Enterprise Server Azure NetApp Files ile Azure VM 'lerinde bekleme düğümüyle genişleme SAP HANA](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md)
* [Red Hat Enterprise Linux Azure NetApp Files ile Azure VM 'lerinde bekleme düğümüyle genişleme SAP HANA](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel.md)
* [RHEL-Azure sanal makinelerinde HSR ve pacemaker ile ölçeği genişletme SAP HANA](../virtual-machines/workloads/sap/sap-hana-high-availability-scale-out-hsr-rhel.md)
* [Azure uygulamayla tutarlı anlık görüntü aracı (AzAcSnap)](azacsnap-introduction.md)

### <a name="sap-anydb"></a>SAP AnyDB

* [Oracle Azure sanal makineler için SAP dağıtımı iş yükü-Azure sanal makineleri](../virtual-machines/workloads/sap/dbms_guide_oracle.md#oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux)
* [Azure NetApp Files ile SAP AnyDB (Oracle 19c) dağıtma](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-anydb-oracle-19c-with-azure-netapp-files/ba-p/2064043)

### <a name="sap-iq-nls"></a>SAP ıQ-NLS

*   [SUSE Linux Enterprise Server Azure NetApp Files kullanarak SAP IQ-NLS HA çözümünü dağıtma](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172#.X2tDfpNzBh4.linkedin)
* [HA senaryosunda SAP IQ lisansını yönetme](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/how-to-manage-sap-iq-license-in-ha-scenario/ba-p/2052583)

### <a name="sap-tech-community-and-blog-posts"></a>SAP teknoloji topluluğu ve blog gönderileri 

* [Azure NetApp Files – saniye cinsinden yedekleme SAP HANA](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – HANA veritabanınızı bir anlık görüntü yedeklemesinden geri yükleme](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files – yedeklemeyi bulut eşitlemeyle boşaltma SAP HANA](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Azure NetApp Files kullanarak SAP HANA sistem kopyalarınızı hızlandırın](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Bulut birimleri ONTAP ve Azure NetApp Files: SAP HANA sistem geçişi kolaylaştırıldı](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)
* [HANA N + d Scale-Out mimarideki ANF yatırımınızı en üst düzeye çıkarmak için mimari kararlar-1. Bölüm](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2078737)
* [HANA N + d Scale-Out mimarideki ANF yatırımınızı en üst düzeye çıkarmak için mimari kararlar-2. Bölüm](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2117130)
* [HANA N + d Scale-Out mimarideki ANF yatırımınızı en üst düzeye çıkarmak için mimari kararlar-Bölüm 3](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/architectural-decisions-to-maximize-anf-investment-in-hana-n-m/ba-p/2215948)
* [Azure NetApp Files ile SAP yatay boyutlandırma ve birim birleştirme](https://techcommunity.microsoft.com/t5/sap-on-microsoft/sap-landscape-sizing-and-volume-consolidation-with-anf/m-p/2145572/highlight/true#M14)

## <a name="azure-vmware-solutions"></a>Azure VMware çözümleri

* [Azure VMware çözümü-Konuk işletim sistemi takmaları ile Azure NetApp Files](../azure-vmware/netapp-files-with-azure-vmware-solution.md)

## <a name="virtual-desktop-infrastructure-solutions"></a>Sanal masaüstü altyapısı çözümleri

Bu bölüm, sanal masaüstü altyapısı çözümlerine yönelik başvurular sağlar.

### <a name="windows-virtual-desktop"></a>Windows Sanal Masaüstü

* [Windows Sanal Masaüstü ile Azure NetApp Files kullanmanın avantajları](solutions-windows-virtual-desktop.md)
* [Windows sanal masaüstündeki FSLogix profil kapsayıcıları için depolama seçenekleri](../virtual-desktop/store-fslogix-profile.md#azure-platform-details)
* [Azure NetApp Files kullanarak bir konak havuzu için FSLogix profil kapsayıcısı oluşturma](../virtual-desktop/create-fslogix-profile-container.md)
* [Kurumsal ölçekte Windows Sanal Masaüstü](/azure/architecture/example-scenario/wvd/windows-virtual-desktop)
* [Kurumsal Azure NetApp Files en iyi uygulamalar için Microsoft FSLogix](/azure/architecture/example-scenario/wvd/windows-virtual-desktop-fslogix#azure-netapp-files-best-practices)
* [MSIX uygulama Iliştirme için Azure NetApp Files ayarlama](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/setting-up-azure-netapp-files-for-msix-app-attach-step-by-step/m-p/1990021)

## <a name="hpc-solutions"></a>HPC çözümleri

Bu bölümde, yüksek performanslı bilgi Işlem (HPC) çözümleri için başvurular sağlanmaktadır. 

### <a name="generic-hpc"></a>Genel HPC

* [Azure NetApp Files: bulut depolamalarınızdan en iyi şekilde yararlanmak](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Azure Batch ve Azure NetApp Files MPı iş yüklerini çalıştırma](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure Cycle Cloud: Azure NetApp Files CycleCloud HPC ortamları](/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>Petrol ve gaz

* [Yüksek performanslı bilgi işlem (HPC): Azure 'da yağ ve gaz](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Azure 'da rezervoır benzetim yazılımını çalıştırma](/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>Elektronik tasarım otomasyonu (EDA)

* [Elektronik tasarım otomasyonu için Azure NetApp Files kullanmanın avantajları](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud: Azure NetApp Files ile EDA HPC Laboratuvarı](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)
* [Yarı iletken sektör için Azure](https://azurecomcdn.azureedge.net/cvt-f40f39cd9de2d875ab0c198a8d7b186350cf0bca161e80d7896941389685d012/mediahandler/files/resourcefiles/azure-for-the-semiconductor-industry/Azure_for_the_Semiconductor_Industry.pdf)

### <a name="analytics"></a>Analiz

* [Azure NetApp Files: Microsoft Azure SAS kılavuzuyla kullanılacak paylaşılan bir dosya sistemi](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/705192)
* [Azure NetApp Files: MS Azure 'da SAS kılavuzuyla birlikte kullanılacak paylaşılan bir dosya sistemi – RHEL 8.3/nConnect UPDATE](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/722261#M21648)
* [SAS ile Microsoft Azure kullanmak için en iyi uygulamalar®](https://communities.sas.com/t5/Administration-and-Deployment/Best-Practices-for-Using-Microsoft-Azure-with-SAS/m-p/676833#M19680)

## <a name="azure-platform-services-solutions"></a>Azure platform hizmetleri çözümleri

Bu bölüm, Azure platform hizmetleri için çözümler sağlar. 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Hizmetleri ve Kubernetes

* [Azure NetApp Files Azure Kubernetes hizmeti ile tümleştirme](../aks/azure-netapp-files.md)
* [Azure NetApp Files ile Azure 'da bu dünya dışı Kubernetes performansı](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Azure NetApp Files + Trident = Kubernetes için dinamik ve kalıcı depolama](https://anfcommunity.com/2021/02/16/azure-netapp-files-trident-dynamic-and-persistent-storage-for-kubernetes/)
* [Trident-kapsayıcılar için depolama Orchestrator](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)
* [Azure Kubernetes hizmeti 'nde (AKS) Magento e-ticaret platformu](/azure/architecture/example-scenario/magento/magento-azure)

### <a name="azure-batch"></a>Azure Batch

* [Azure Batch ve Azure NetApp Files MPı iş yüklerini çalıştırma](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
