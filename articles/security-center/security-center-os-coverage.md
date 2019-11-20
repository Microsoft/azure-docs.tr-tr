---
title: Azure Güvenlik Merkezi tarafından desteklenen platformlar | Microsoft Docs
description: Bu belge, Azure Güvenlik Merkezi tarafından desteklenen platformların bir listesini sağlar.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: d91ac6d50faaadf560ae7ff9e9cce5f7bc4b180a
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803919"
---
# <a name="supported-platforms"></a>Desteklenen platformlar 

## Sanal makineler/sunucular<a name="vm-server"></a>

Güvenlik Merkezi, farklı türlerde karma ortamlarda sanal makineleri/sunucuları destekler:

* Yalnızca Azure
* Azure ve şirket içi
* Azure ve diğer bulutlar
* Azure, diğer bulutlar ve şirket içi

Azure Güvenlik Merkezi, Azure aboneliği üzerinde etkinleştirilen bir Azure ortamında, abonelik içinde dağıtılan IaaS kaynaklarını otomatik olarak keşfeder.

> [!NOTE]
> Güvenlik özelliklerinin tam kümesini almak için, Azure Güvenlik Merkezi tarafından kullanılan ve [Azure Güvenlik Merkezi 'ne veri göndermek üzere doğru şekilde yapılandırılmış](security-center-enable-data-collection.md#manual-agent) [Log Analytics aracısına](../azure-monitor/platform/agents-overview.md#log-analytics-agent)sahip olmanız gerekir.


Aşağıdaki bölümlerde, Azure Güvenlik Merkezi tarafından kullanılan [Log Analytics aracısının](../azure-monitor/platform/agents-overview.md#log-analytics-agent)üzerinde çalıştığı desteklenen sunucu işletim sistemleri listelenmektedir.

### Windows Server işletim sistemleri<a name="os-windows"></a>

|İşletim Sistemi|Azure Güvenlik Merkezi tarafından desteklenir|Microsoft Defender ATP ile tümleştirme desteği|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Yukarıda listelenen Windows işletim sistemlerine yönelik desteklenen özellikler hakkında daha fazla bilgi edinmek için bkz. [sanal makine/sunucu tarafından desteklenen özellikler](security-center-services.md##vm-server-features).

### Linux işletim sistemleri<a name="os-linux"></a>

64 bit

* CentOS 6 ve 7
* Amazon Linux 2017,09
* Oracle Linux 6 ve 7
* Red Hat Enterprise Linux Server 6 ve 7
* Borçlu GNU/Linux 8 ve 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS ve 18,04 LTS
* SUSE Linux Enterprise Server 12

32 bit
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Borçlu GNU/Linux 8 ve 9
* Ubuntu Linux 14,04 LTS ve 16,04 LTS

> [!NOTE]
> Desteklenen Linux işletim sistemlerinin listesi sürekli olarak değiştiğinden, tercih ediyorsanız, Desteklenen sürümlerin en güncel listesini görüntülemek için [buraya](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) tıklayın ve bu konunun son yayımlandığı tarihten sonra değişiklikler olması gerekir.

Yukarıda listelenen Linux işletim sistemleri için desteklenen özellikler hakkında daha fazla bilgi edinmek için bkz. [sanal makine/sunucu tarafından desteklenen özellikler](security-center-services.md##vm-server-features).

### Yönetilen sanal makine Hizmetleri<a name="virtual-machine"></a>

Sanal makineler, Azure Kubernetes (AKS), Azure Databricks ve daha fazlası gibi bazı Azure yönetilen hizmetlerin bir parçası olarak da bir müşteri aboneliğinde oluşturulur. Bu sanal makineler de Azure Güvenlik Merkezi tarafından bulunur ve Log Analytics Aracısı yukarıda listelenen desteklenen [Windows/Linux işletim sistemlerine](#os-windows)göre yüklenebilir ve yapılandırılabilir.

### Cloud Services<a name="cloud-services"></a>

Bulut hizmetinde çalışan sanal makineler de desteklenir. Yalnızca üretim yuvalarında çalışan Cloud Services Web ve çalışan rolleri izlenir. Bulut hizmetleri hakkında daha fazla bilgi edinmek için bkz. [Azure Cloud Services 'ye genel bakış](../cloud-services/cloud-services-choose-me.md).

## PaaS hizmetleri<a name="paas-services"></a>

Aşağıdaki Azure PaaS kaynakları, Azure Güvenlik Merkezi tarafından desteklenir:

* SQL
* PostGreSQL
* MySQL
* CosmosDB
* Depolama hesabı
* App Service
* İşlev
* Bulut Hizmeti
* Sanal ağ
* Alt ağ
* NIC
* NSG
* Batch hesabı
* Service Fabric hesabı
* Otomasyon hesabı
* Yük dengeleyici
* Arama
* Service Bus ad alanı
* Stream Analytics
* Olay hub’ı ad alanı
* Mantıksal uygulamalar
* Redis
* Data Lake Analytics
* Data Lake Store
* Key vault

Yukarıdaki PaaS kaynakları listesinin desteklenen özellikleri hakkında daha fazla bilgi edinmek için bkz. [PaaS hizmetleri tarafından desteklenen özellikler](security-center-services.md#paas-services).

## <a name="next-steps"></a>Sonraki adımlar

- [Güvenlik Merkezi 'nin verileri ve Log Analytics aracısını nasıl topladığı](security-center-enable-data-collection.md)hakkında bilgi edinin.
- [Güvenlik Merkezi 'nin verileri nasıl yönettiğini ve korumalarını](security-center-data-security.md)öğrenin.
- [Azure Güvenlik Merkezi 'ni benimsemek için tasarım konularını nasıl planlayacağınızı ve anlayacağınızı](security-center-planning-and-operations-guide.md)öğrenin.
- [Farklı bulut ortamları için kullanılabilen özellikler](security-center-services.md)hakkında bilgi edinin.
- [Azure Güvenlik Merkezi 'Nde VM 'ler & sunucular için tehdit algılama](security-center-alerts-iaas.md)hakkında daha fazla bilgi edinin.
- [Azure Güvenlik Merkezi 'ni kullanma hakkında sık sorulan soruları](security-center-faq.md)bulun.
- [Azure güvenliği ve uyumluluğu ile ilgili blog gönderilerini](https://blogs.msdn.com/b/azuresecurity/)bulun.
