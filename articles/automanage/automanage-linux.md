---
title: Linux için Azure oto yönetimi
description: Otomatik olarak eklendi ve Linux makineler için yapılandırılan hizmetlere yönelik sanal makinelerin en iyi uygulamaları için Azure otomatik yönetimi hakkında bilgi edinin.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 0d35963d96ead68c35ca44467119b3c03d0b16c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863074"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Sanal makinelerin en iyi uygulamaları için Azure oto yönetimi-Linux

Linux VM 'de sanal makineler (VM) için otomatik yönetme kullandığınızda bu Azure hizmetleri sizin için otomatik olarak eklendi. Bunlar, [bulut benimseme çerçevesinden](/azure/cloud-adoption-framework/manage/azure-server-management)bulabileceğiniz en iyi yöntemler teknik incelemesi için önemlidir.

Bu hizmetlerin tümü için otomatik olarak kullanılacak, otomatik olarak yapılandıracağız, drara için izleyici ve Drın algılanıp algılanmadığını düzelteceğiz. Bu işlem hakkında daha fazla bilgi edinmek için bkz. [sanal makineler Için Azure oto yönetimi](automanage-virtual-machines.md).

## <a name="supported-linux-distributions-and-versions"></a>Desteklenen Linux dağıtımları ve sürümleri

Oto yönetimi aşağıdaki Linux dağıtımlarını ve sürümlerini destekler:

- CentOS 7.3 +
- RHEL 7.4 +
- Ubuntu 16,04 ve 18,04
- SLES 12 (yalnızca SP3-SP5)

## <a name="participating-services"></a>Katılım Hizmetleri

>[!NOTE]
> Şu anda Linux sanal makinelerinde Microsoft Antimalware desteklenmez.

|Hizmet    |Açıklama    |Desteklenen ortamlar<sup>1</sup>    |Desteklenen Tercihler<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[VM öngörüleri Izleme](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |VM'ler için Azure İzleyici, çalışan işlemleri ve diğer kaynaklardaki bağımlılıklar dahil olmak üzere sanal makinelerinizin performansını ve sistem durumunu izler. [Daha fazla bilgi](../azure-monitor/vm/vminsights-overview.md) edinin.    |Üretim    |No    |
|[Backup](https://docs.microsoft.com/azure/backup/backup-overview)   |Azure Backup, VM'lerinizdeki verilerin istenmeden yok edilmesine karşı koruma sağlayan bağımsız ve yalıtılmış yedekler sunar. [Daha fazla bilgi](../backup/backup-azure-vms-introduction.md) edinin. Ücretler, korunan sanal makinelerin sayısına ve boyutuna bağlıdır. [Daha fazla bilgi](https://azure.microsoft.com/pricing/details/backup/) edinin.    |Üretim    |Yes    |
|[Azure Güvenlik Merkezi](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Güvenlik Merkezi, veri merkezlerinizin güvenlik duruşunu güçlendirir ve buluttaki karma iş yükleriniz genelinde gelişmiş tehdit koruması sağlayan Birleşik bir altyapı güvenliği yönetim sistemidir. [Daha fazla bilgi](../security-center/security-center-introduction.md) edinin.  Oto yönetimi, sanal makinenizin Azure Güvenlik Merkezi 'nin Ücretsiz katmanında bulunduğu aboneliği yapılandırır. Aboneliğiniz zaten Azure Güvenlik Merkezi 'ne eklendi, bu işlemi yeniden yapılandırın.    |Üretim, geliştirme ve test    |No    |
|[Güncelleştirme yönetimi](https://docs.microsoft.com/azure/automation/update-management/overview)    |Sanal makinelerinize yönelik işletim sistemi güncelleştirmelerini yönetmek için Azure Otomasyonu 'nda Güncelleştirme Yönetimi kullanabilirsiniz. Tüm aracı makinelerde kullanılabilir güncelleştirmelerin durumunu hızlı bir şekilde değerlendirebilir ve sunucular için gerekli güncelleştirmeleri yükleme işlemini yönetebilirsiniz. [Daha fazla bilgi](../automation/update-management/overview.md) edinin.    |Üretim, geliştirme ve test    |No    |
|[Değişiklik İzleme & envanteri](https://docs.microsoft.com/azure/automation/change-tracking/overview) |Değişiklik İzleme ve envanter, sanal makine ve sunucu altyapısı değişikliklerini izlemenize olanak tanımak için değişiklik izleme ve envanter işlevlerini birleştirir. Hizmet, istenmeyen değişiklikleri tanılamanıza ve uyarı almanıza yardımcı olması için ortamınızdaki hizmetler, Daemon 'ları yazılım, kayıt defteri ve dosyalar arasında değişiklik izlemeyi destekler. Envanter desteği, Konuk kaynakları yüklü uygulamalar ve diğer yapılandırma öğeleri hakkında görünürlük için sorgulamanızı sağlar.  [Daha fazla bilgi](../automation/change-tracking/overview.md) edinin.    |Üretim, geliştirme ve test    |No    |
|[Azure Konuk yapılandırması](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)  | Konuk yapılandırma ilkesi, makinenin uyumluluğuyla ilgili yapılandırmayı ve raporu izlemek için kullanılır. Oto Yönet hizmeti, Konuk yapılandırma uzantısını kullanarak Azure Linux taban çizgisini yükler. Linux makinelerinde, Konuk yapılandırma hizmeti taban çizgisini yalnızca denetim modunda yükler. VM 'nizin taban çizgisiyle uyumlu olmadığını görebilir, ancak uyumsuzluk otomatik olarak düzeltilmeyecektir. [Daha fazla bilgi](../governance/policy/concepts/guest-configuration.md) edinin.    |Üretim, geliştirme ve test    |No    |
|[Önyükleme Tanılamaları](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)  | Önyükleme Tanılaması, Azure sanal makineler (VM) için VM önyükleme hatalarının tanılanmasına izin veren bir hata ayıklama özelliğidir. Önyükleme Tanılaması, seri günlük bilgilerini ve ekran görüntülerini toplayarak bir kullanıcının VM 'nin durumunu gözlemlemeye olanak sağlar. Bu, yalnızca yönetilen diskleri kullanan makineler için etkinleştirilir. |Üretim, geliştirme ve test    |No    |
|[Azure Otomasyonu Hesabı](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Otomasyonu, altyapınızın ve uygulamalarınızın yaşam döngüsü boyunca yönetimi destekler. [Daha fazla bilgi](../automation/automation-intro.md) edinin.    |Üretim, geliştirme ve test    |No    |
|[Log Analytics Çalışma Alanı](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Azure Izleyici, günlük verilerini bir Azure kaynağı olan bir Log Analytics çalışma alanında ve verilerin toplandığı, toplandığı ve yönetim sınırı olarak hizmet verdiği bir kapsayıcı olarak depolar. [Daha fazla bilgi](../azure-monitor/logs/design-logs-deployment.md) edinin.    |Üretim, geliştirme ve test    |No    |


<sup>1</sup> ortam seçimi, oto yönetimini etkinleştirirken kullanılabilir. [Daha fazla bilgi](automanage-virtual-machines.md#environment-configuration) edinin. Ayrıca, ortamın varsayılan ayarlarını ayarlayabilir ve en iyi yöntemler kısıtlamaları içinde kendi tercihlerinizi ayarlayabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Azure portal sanal makineler için oto yönetimini etkinleştirmeyi deneyin.

> [!div class="nextstepaction"]
> [Azure portal sanal makineler için oto yönetimini etkinleştir](quick-create-virtual-machines-portal.md)