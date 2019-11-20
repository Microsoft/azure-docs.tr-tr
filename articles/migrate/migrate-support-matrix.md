---
title: Azure geçişi destek matrisi
description: Azure geçişi hizmeti için destek ayarlarının ve sınırlamaların özetini sağlar.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: raynew
ms.openlocfilehash: e752d52d0f792d39f417031c26d923fce07ccdf8
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747987"
---
# <a name="azure-migrate-support-matrix"></a>Azure geçişi destek matrisi

Makineleri değerlendirmek ve Microsoft Azure buluta geçirmek için [Azure geçişi hizmetini](migrate-overview.md) kullanabilirsiniz. Bu makalede, Azure geçişi senaryoları ve dağıtımları için genel destek ayarları ve sınırlamaları özetlenmektedir.


## <a name="azure-migrate-versions"></a>Azure geçişi sürümleri

Azure geçişi hizmetinin iki sürümü vardır:

- **Geçerli sürüm**: bu sürümü kullanarak yeni Azure geçişi projeleri oluşturabilir, şirket içi değerlendirir bulabilir ve değerlendirmeleri ve geçişleri yönetebilirsiniz. [Daha fazla bilgi edinin](whats-new.md#release-version-july-2019).
- **Önceki sürüm**: Azure geçişi 'nin önceki sürümünü kullanan müşteri için (yalnızca şirket Içi VMware VM 'lerinin değerlendirmesi desteklenir), artık geçerli sürümü kullanmalısınız. Önceki sürümde, yeni Azure geçişi projesi oluşturamaz veya yeni bulmalar gerçekleştiremezsiniz.

## <a name="supported-assessmentmigration-scenarios"></a>Desteklenen değerlendirme/geçiş senaryoları

Tablo, desteklenen bulma, değerlendirme ve geçiş senaryolarını özetler.

**Dağıtım** | **Ayrıntılar** 
--- | --- 
**Uygulamaya özel bulma** | VMware VM 'lerinde çalışan uygulamaları, rolleri ve özellikleri bulabilirsiniz. Şu anda bu özellik yalnızca bulma ile sınırlıdır. Değerlendirme Şu anda makine düzeyindedir. Henüz uygulama, rol veya özelliğe özgü değerlendirme sunmuyoruz. 
**Şirket içi değerlendirme** | Şirket içi iş yüklerini ve VMware VM 'leri, Hyper-V VM 'Leri ve fiziksel sunucularda çalışan verileri değerlendirin. Azure geçişi sunucu değerlendirmesi ve Microsoft Data Migration Yardımcısı (DMA) ile diğer araçların yanı sıra diğer araçları ve ISV tekliflerini kullanmayı değerlendirin.
**Azure 'a şirket içi geçiş** | Fiziksel sunucular, VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular ve bulut tabanlı VM 'lerde çalışan iş yüklerini ve verileri Azure 'a geçirin. Azure geçişi sunucu değerlendirmesi ve Azure veritabanı geçiş hizmeti 'ni (DMS) ve diğer araçlar ve ISV tekliflerini kullanarak geçirin.


## <a name="supported-tools"></a>Desteklenen Araçlar

Belirli araç desteği tabloda özetlenmiştir.

**Araç** | **Lamayı** | **Geçirme** 
--- | --- | ---
Azure geçişi sunucu değerlendirmesi | [VMware VM](tutorial-prepare-vmware.md)'lerini, [Hyper-V VM](tutorial-prepare-hyper-v.md)'lerini ve [fiziksel sunucuları](tutorial-prepare-physical.md)değerlendirin. |  Kullanılamıyor (yok)
Azure geçiş sunucusu geçişi | NA | [VMware VM 'leri](tutorial-migrate-vmware.md), [Hyper-V VM 'leri](tutorial-migrate-hyper-v.md)ve [fiziksel sunucuları](tutorial-migrate-physical-virtual-machines.md)geçirin.
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | NA | VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular, genel bulut iş yükleri geçirin. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular, genel bulut iş yükleri değerlendirin. | NA
[Corent teknolojisi](https://go.microsoft.com/fwlink/?linkid=2084928) | VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular, genel bulut iş yükleri değerlendirin ve geçirin. |  VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular, genel bulut iş yükleri geçirin.
[Cihaz 42](https://go.microsoft.com/fwlink/?linkid=2097158) | VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular, genel bulut iş yükleri değerlendirin.| NA
[DMA](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) | Şirket içi SQL Server veritabanlarını değerlendirin. | NA
[DMS](https://docs.microsoft.com/azure/dms/dms-overview) | NA | SQL Server, Oracle, MySQL, PostgreSQL, MongoDB geçirin. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Sanal Masaüstü altyapısını (VDı) değerlendirme | NA
[Movere](https://go.microsoft.com/fwlink/?linkid=2109528) | VMWare VM 'leri, Hyper-V VM 'Leri, Xen VM 'Leri, fiziksel makineler, iş istasyonları (VDı dahil), genel bulut iş yükleri | NA
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | NA | VMWare VM 'leri, Hyper-V VM 'Leri, Xen VM 'leri, KVM VM 'Leri, fiziksel makineler, genel bulut iş yükleri geçirin 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular, genel bulut iş yükleri değerlendirin. | NA
[Unifrivcloud](https://go.microsoft.com/fwlink/?linkid=2097195) | VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular, genel bulut iş yükleri ve SQL Server veritabanlarını değerlendirin. | NA
[WEBAPP Geçiş Yardımcısı](https://appmigration.microsoft.com/) | Web uygulamalarını değerlendirme | Web uygulamalarını geçirme.


## <a name="azure-migrate-projects"></a>Azure geçişi projeleri

**Destek** | **Ayrıntılar**
--- | ---
Abonelik | Bir abonelikte birden çok Azure geçişi projesi oluşturabilirsiniz.
Azure izinleri | Azure geçişi projesi oluşturmak için abonelikte katkıda bulunan veya sahip izinlerinizin olması gerekir.
VMware Sanal Makineleri  | Tek bir projede en fazla 35.000 VMware VM 'yi değerlendirin.
Hyper-V Sanal Makineleri | Tek bir projede 35.000 adede kadar Hyper-V VM 'yi değerlendirin.

Bir proje, değerlendirme sınırlarına kadar hem VMware VM 'lerini hem de Hyper-V sanal makinelerini içerebilir.

## <a name="supported-geographies"></a>Desteklenen coğrafi lıklar

Bir dizi coğrafi bölgedeki Azure geçişi projesi oluşturabilirsiniz. Yalnızca bu coğrafi bölgelerde proje oluşturabilseniz de, diğer hedef konumlar için makineleri değerlendirebilir veya geçirebilirsiniz. Proje Coğrafya yalnızca keşfedilen meta verileri depolamak için kullanılır.

**Coğrafya** | **Meta veri depolama konumu**
--- | ---
Azure Kamu | ABD Devleti Virginia
Asya Pasifik | Doğu Asya veya Güneydoğu Asya
Avustralya | Avustralya Doğu veya Avustralya Güneydoğu
Brezilya | Güney Brezilya
Kanada | Kanada Orta veya Kanada Doğu
Avrupa | Kuzey Avrupa veya Batı Avrupa
Fransa | Fransa Orta
Hindistan | Orta Hindistan veya Güney Hindistan
Japonya |  Japonya Doğu veya Japonya Batı
Güney Kore | Kore Orta veya Kore Güney
Birleşik Krallık | UK Güney veya UK Batı
Amerika Birleşik Devletleri | Orta ABD veya Batı ABD 2


 > [!NOTE]
 > Azure Kamu desteği şu anda yalnızca Azure geçişi 'nin [eski sürümünde](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) sunulmaktadır.



## <a name="vmware-assessment-and-migration"></a>VMware değerlendirmesi ve geçişi

VMware VM 'Leri için Azure geçişi sunucu değerlendirmesi ve sunucu geçiş desteği matrisini [gözden geçirin](migrate-support-matrix-vmware.md) .

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V değerlendirmesi ve geçiş

Hyper-V VM 'Leri için Azure geçişi sunucu değerlendirmesi ve sunucu geçiş desteği matrisini [gözden geçirin](migrate-support-matrix-hyper-v.md) .


## <a name="next-steps"></a>Sonraki adımlar

- Geçiş için [VMware VM 'Lerini değerlendirin](tutorial-assess-vmware.md) .
- Geçiş için [Hyper-V VM 'Lerini değerlendirin](tutorial-assess-hyper-v.md) .

