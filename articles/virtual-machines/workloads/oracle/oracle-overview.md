---
title: Microsoft Azure Oracle çözümleri | Microsoft Docs
description: Tamamen Azure altyapısında çalışan veya Oracle bulut altyapısı (OCı) ile platformlar arası bağlantı kullanma dahil olmak üzere Microsoft Azure ' de Oracle uygulamalarını ve çözümlerini dağıtmaya yönelik seçenekler hakkında bilgi edinin.
documentationcenter: ''
author: dbakevlar
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/05/2020
ms.author: kegorman
ms.openlocfilehash: 971f7c919595f915451faf9266ee3bb18b35087e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677177"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Azure 'da Oracle uygulamalarına ve çözümlerine genel bakış

Bu makalede, Azure altyapısını kullanarak Oracle çözümlerini çalıştırmaya yönelik yetenekler açıklanır. Ayrıca bkz. kullanılabilir [WebLogic Server Azure uygulamaları](oracle-weblogic.md), Azure Market 'TEKI [Oracle VM görüntüleri](oracle-vm-solutions.md) ve [Oracle bulut altyapısı (OCI) ile Azure bağlantısı](oracle-oci-overview.md)yapma özelliği.

## <a name="oracle-databases-on-azure-infrastructure"></a>Azure altyapısında Oracle veritabanları

Azure Marketi 'nde bulunan Oracle Linux görüntülerde Oracle Database kullanarak Oracle veritabanlarını Azure altyapısında çalıştırın:

* Oracle Database 12,1, 12,2 ve 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 ve 18,3 Standard Edition

* Oracle Database 19,3

Ayrıca, Azure 'da bulunan Oracle Linux olmayan bir görüntüde Oracle Database kurulumunu ve şirket içi ortamınızdan özel bir görüntü yüklemeyi tercih ettiğiniz özel bir görüntüye dayandırın.

İsteğe bağlı olarak birden çok bağlı disk ile yapılandırın ve Oracle otomatik depolama yönetimi (ASM) yükleyerek veritabanı performansını geliştirebilirsiniz.

## <a name="weblogic-server-with-azure-service-integrations"></a>Azure hizmet tümleştirmelerine sahip WebLogic Server

Bulut yolculuğunun hızlandırmanız için çeşitli WebLogic Server Azure uygulamaları arasından seçim yapın.  Veritabanı, Azure uygulama ağ geçidi ve Azure Active Directory dahil olmak üzere, önceden yapılandırılmış birkaç Azure hizmet tümleştirmeleri mevcuttur.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Oracle Linux ve WebLogic Server 'daki uygulamalar

Desteklenen Oracle Linux görüntülerde Azure 'da kurumsal uygulamaları çalıştırın. Aşağıdaki sanal makine görüntüleri Azure Marketi 'nde kullanılabilir:

* Oracle WebLogic Server 12.1.2

* Ayırıcı kurumsal çekirdek (UEK) 6,8, 6,9, 6,10, 7,3 ile 7,7, 8,0, 8,1 ile Oracle Linux. 

## <a name="high-availability-and-disaster-recovery-options"></a>Yüksek kullanılabilirlik ve olağanüstü durum kurtarma seçenekleri

* Şirket içinde yüksek kullanılabilirlik için [kullanılabilirlik alanları](../../../availability-zones/az-overview.md) ile [birlikte, Azure](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) altyapısına yönelik fsfo, parçalı veya [altın kapıyla](https://www.oracle.com/middleware/technologies/goldengate.html) birlikte [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [Active Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)'ı yapılandırın. Ayrıca, bu konfigürasyonları, ek kullanılabilirlik ve olağanüstü durum kurtarma için birden çok Azure bölgesinde da ayarlayabilirsiniz.

* Azure 'da ve fiziksel sunucularınızda Oracle Linux sanal makinelerinize yönelik olağanüstü durum kurtarmayı düzenlemek ve yönetmek için [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) kullanın. 

* Azure [VMware çözümünü](../../../vmware-cloudsimple/oracle-real-application-clusters.md) veya [Flashgrid ufuk kümesini](https://www.flashgrid.io/oracle-rac-in-azure/)kullanarak Azure 'Da Oracle gerçek uygulama kümelerini (RAC) etkinleştirin.

## <a name="backup-oracle-workloads"></a>Oracle Iş yüklerini yedekleme

* [Azure Backup](../../../backup/backup-overview.md) kullanarak Oracle VM 'lerinizi yedekleme

* Oracle Database Oracle RMAN kullanarak yedekleyin ve isteğe bağlı olarak [Azure Blob sigortası](../../../storage/blobs/storage-how-to-mount-container-linux.md) kullanarak [yüksek düzeyde yedekli bir Azure Blob depolama hesabı](../../../storage/common/storage-redundancy.md) takın ve ek dayanıklılık için RMAN yedeklemelerinizi bu depoya yazın.

## <a name="integration-of-azure-with-oci"></a>OCı ile Azure tümleştirmesi

Oracle bulut altyapısında (OCı) arka uç veritabanlarına bağlı olan Azure altyapısında Oracle uygulamaları çalıştırın. Bu çözüm aşağıdaki özellikleri kullanır: 

* **Platformlar arası ağ** -uygulama ve veritabanı katmanı arasında yüksek bant genişliğine sahip, özel ve düşük gecikme süreli bağlantılar oluşturmak Için Azure ExpressRoute ve Oracle FastConnect arasında bulunan doğrudan Interconnect 'i kullanın.
* **Tümleşik kimlik** -çözümler için tek bir kimlik kaynağı oluşturmak üzere Azure AD ile Oracle IDCs arasında federasyon kimliği ayarlayın. OCı ve Azure arasında kaynakları yönetmek için çoklu oturum açmayı etkinleştirin.

### <a name="deploy-oracle-applications-on-azure"></a>Azure 'da Oracle uygulamaları dağıtma

Azure altyapısını ayarlamak ve Oracle uygulamalarını yüklemek için Terrayform şablonlarını kullanın. 

Oracle, Azure/Oracle bulut bağlantısı çözümü aracılığıyla bir Oracle veritabanına bağlanırken bu uygulamaların Azure 'da çalıştırılmasını sertifikalandırmıştır:

* E-Iş paketi
* JD edi EnterpriseOne
* PeopleSoft
* Oracle perakende uygulamaları
* Oracle Hyperion finans yönetimi

Ayrıca, Azure 'da OCı ve diğer Azure hizmetleriyle bağlantı sağlayan özel uygulamalar dağıtın.

### <a name="set-up-oracle-databases-in-oci"></a>OCı 'da Oracle veritabanlarını ayarlama

Azure 'da çalışan Oracle yazılımıyla birlikte Oracle Database Cloud Services (özerk veritabanı, RAC, sınavı verileri, DBaaS, tek düğüm) kullanın. [OCI veritabanı seçenekleri](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm)hakkında daha fazla bilgi edinin. 
 

## <a name="licensing"></a>Lisanslama

Oracle uygulamalarının Azure 'da dağıtılması, "kendi lisansını getir" modelini temel alır. Oracle yazılımını kullanmak için doğru lisansa sahip olduğunuz ve Oracle ile geçerli bir destek sözleşmenizin olduğu varsayılır. Oracle 'ın Şirket içinden Azure 'a lisans taşınabilirlik garantisi vardır. [Sss](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)Oracle-Azure bakın.

## <a name="next-steps"></a>Sonraki adımlar

* [Web Logic Server Azure uygulamaları](oracle-weblogic.md) ve destekledikleri Azure hizmet tümleştirmeleri hakkında daha fazla bilgi edinin.

* Azure altyapısına [Oracle VM görüntülerini](oracle-vm-solutions.md) dağıtma hakkında daha fazla bilgi edinin.

* [OCI Ile Azure bağlantısı](oracle-oci-overview.md)oluşturma hakkında daha fazla bilgi edinin.

* Ignite 2019 [' den Oracle 'ın Azure 'a genel bakış oturumuna](https://www.pluralsight.com/courses/microsoft-ignite-session-57) göz atın. 
