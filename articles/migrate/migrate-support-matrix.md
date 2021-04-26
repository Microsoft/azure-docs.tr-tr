---
title: Azure geçişi destek matrisi
description: Azure geçişi hizmeti için destek ayarlarının ve sınırlamaların özetini sağlar.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: af0b8a4d3dfbce32e412f5294fb19ade61fd7661
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628178"
---
# <a name="azure-migrate-support-matrix"></a>Azure geçişi destek matrisi

Sunucuları Microsoft Azure buluta değerlendirmek ve geçirmek için [Azure geçişi hizmetini](./migrate-services-overview.md) kullanabilirsiniz. Bu makalede, Azure geçişi senaryoları ve dağıtımları için genel destek ayarları ve sınırlamaları özetlenmektedir.

## <a name="supported-assessmentmigration-scenarios"></a>Desteklenen değerlendirme/geçiş senaryoları

Tablo, desteklenen bulma, değerlendirme ve geçiş senaryolarını özetler.

**Dağıtım** | **Ayrıntılar** 
--- | --- 
**Bulma** | Sunucu meta verilerini ve dinamik performans verilerini bulabilirsiniz.
**Uygulama bulma** | VMware VM 'lerinde çalışan uygulamaları, rolleri ve özellikleri bulabilirsiniz. Şu anda bu özellik yalnızca bulma ile sınırlıdır. Değerlendirme Şu anda sunucu düzeyindedir. Henüz uygulama, rol veya özellik tabanlı değerlendirmeler sunmuyoruz. 
**Değerlendirme** | Şirket içi iş yüklerini ve VMware VM 'leri, Hyper-V VM 'Leri ve fiziksel sunucularda çalışan verileri değerlendirin. Azure geçişi kullanmayı değerlendirin: Sunucu değerlendirmesi, Microsoft Data Migration Yardımcısı (DMA) ve diğer araçlar ve ISV teklifleri.
**Geçiş** | Fiziksel sunucular, VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular ve bulut tabanlı VM 'lerde çalışan iş yüklerini ve verileri Azure 'a geçirin. Azure geçişi 'ni kullanarak geçirin: Sunucu değerlendirmesi ve Azure veritabanı geçiş hizmeti (DMS) ve diğer araçlar ve ISV teklifleri.

> [!NOTE]
> Şu anda ISV araçları, Azure Kamu 'da Azure geçişi 'ne veri gönderemiyor. Tümleşik Microsoft araçları kullanabilir veya ortak araçları bağımsız olarak kullanabilirsiniz.

## <a name="supported-tools"></a>Desteklenen araçlar


Belirli araç desteği tabloda özetlenmiştir.

**Araç** | **Değerlendirme** | **Geçiş** 
--- | --- | ---
Azure geçişi: Sunucu değerlendirmesi | [VMware VM](./tutorial-discover-vmware.md)'lerini, [Hyper-V VM](./tutorial-discover-hyper-v.md)'lerini ve [fiziksel sunucuları](./tutorial-discover-physical.md)değerlendirin. |  Kullanılamıyor (yok)
Azure geçişi: sunucu geçişi | Yok | [VMware VM 'leri](tutorial-migrate-vmware.md), [Hyper-V VM 'leri](tutorial-migrate-hyper-v.md)ve [fiziksel sunucuları](tutorial-migrate-physical-virtual-machines.md)geçirin.
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | Yok | VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular ve diğer bulut iş yüklerini geçirin. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular ve diğer bulut iş yüklerini değerlendirin. | Yok
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | VMware VM 'lerini, Hyper-V sanal makinelerini, fiziksel sunucu diğer bulut iş yüklerini değerlendirin. |  VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular, genel bulut iş yükleri geçirin.
[Cihaz 42](https://go.microsoft.com/fwlink/?linkid=2097158) | VMware VM 'lerini, Hyper-V VM 'lerini, fiziksel sunucuları ve diğer bulut iş yüklerini değerlendirin.| Yok
[DMA](/sql/dma/dma-overview) | SQL Server veritabanlarını değerlendirin. | Yok
[DMS](../dms/dms-overview.md) | Yok | SQL Server, Oracle, MySQL, PostgreSQL, MongoDB geçirin. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Sanal Masaüstü altyapısını (VDı) değerlendirme | Yok
[Movere](https://www.movere.io/) | VMware VM 'leri, Hyper-V VM 'Leri, Xen VM 'leri, fiziksel sunucular, iş istasyonları (VDı dahil) ve diğer bulut iş yükleri değerlendirin. | Yok
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Yok | VMWare VM 'leri, Hyper-V VM 'Leri, Xen VM 'leri, KVM VM 'Leri, fiziksel sunucular ve diğer bulut iş yükleri geçirin 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | VMware VM 'lerini, Hyper-V VM 'lerini, fiziksel sunucuları ve diğer bulut iş yüklerini değerlendirin. | Yok
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | VMware VM 'lerini, Hyper-V VM 'lerini, fiziksel sunucuları ve diğer bulut iş yüklerini ve SQL Server veritabanlarını değerlendirin. | Yok
[WEBAPP Geçiş Yardımcısı](https://appmigration.microsoft.com/) | Web uygulamalarını değerlendirme | Web uygulamalarını geçirme.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | Yok |  VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular ve diğer bulut iş yüklerini geçirin.


## <a name="project"></a>Project

**Destek** | **Ayrıntılar**
--- | ---
Abonelik | Bir abonelik içinde birden fazla proje olabilir.
Azure izinleri | Kullanıcının bir proje oluşturmak için abonelikte katkıda bulunan veya sahip izinlerinin olması gerekir.
VMware Sanal Makineleri  | Tek bir projede en fazla 35.000 VMware VM 'yi değerlendirin.
Hyper-V Sanal Makineleri    | Tek bir projede 35.000 adede kadar Hyper-V VM 'yi değerlendirin.

Bir proje, değerlendirme sınırlarına kadar hem VMware VM 'lerini hem de Hyper-V sanal makinelerini içerebilir.

## <a name="azure-permissions"></a>Azure izinleri

Azure geçişi 'nin Azure ile çalışması için, sunucuları değerlendirmeye ve geçirmeye başlamadan önce bu izinlere ihtiyacınız vardır.

**Görev** | **İzinler** | **Ayrıntılar**
--- | --- | ---
Proje oluşturma | Azure hesabınızın bir proje oluşturmak için izinleri olması gerekir. | [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)veya [fiziksel sunucular](./tutorial-discover-physical.md#prepare-an-azure-user-account)için ayarlayın.
Azure geçişi gereci kaydetme| Azure geçişi, Azure geçişi ile sunucuları değerlendirmek için basit bir [Azure geçiş](migrate-appliance.md) gereci kullanır: Sunucu değerlendirmesi ve VMware VM 'Lerinin Azure geçişi ile [aracısız geçişini](server-migrate-overview.md) çalıştırmak için: sunucu geçişi. Bu gereç sunucularını bulur ve Azure geçişi 'ne meta veri ve performans verileri gönderir.<br/><br/> Kayıt sırasında, abonelik kaynak sağlayıcısıyla çalışacak şekilde kayıt sağlayıcıları (Microsoft. OffAzure, Microsoft. Migrate ve Microsoft. Keykasası), Gereç içinde seçilen abonelikle kaydedilir. Kaydolmak için abonelik üzerinde katkıda bulunan veya sahip erişiminizin olması gerekir.<br/><br/> **VMware**-ekleme sırasında Azure geçişi iki Azure Active Directory (Azure AD) uygulaması oluşturur. İlk uygulama, Gereç aracıları ve Azure geçişi hizmeti arasında iletişim kurar. Uygulamanın Azure Kaynak Yönetimi çağrısı yapma izni yoktur veya kaynaklar için Azure RBAC erişimi vardır. İkinci uygulama, yalnızca aracısız VMware geçişi için Kullanıcı aboneliğinde oluşturulan bir Azure Key Vault erişir. Aracısız geçişte, Azure geçişi, erişim anahtarlarını aboneliğinizdeki çoğaltma depolama hesabına yönetmek için bir Key Vault oluşturur. Bu, gereçden bulma başlatıldığında Azure Key Vault (müşteri kiracısında) Azure RBAC erişimine sahiptir.<br/><br/> **Hyper-V**-ekleme sırasında. Azure geçişi bir Azure AD uygulaması oluşturur. Uygulama, Gereç aracıları ve Azure geçişi hizmeti arasında iletişim kurar. Uygulamanın Azure Kaynak Yönetimi çağrısı yapma izni yoktur veya kaynaklar için Azure RBAC erişimi vardır. | [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)veya [fiziksel sunucular](./tutorial-discover-physical.md#prepare-an-azure-user-account)için ayarlayın.
VMware aracısız geçişi için bir Anahtar Kasası oluşturma | VMware VM 'lerini aracısız Azure geçişi: sunucu geçişi ile geçirmek için Azure geçişi, erişim anahtarlarını aboneliğinizdeki çoğaltma depolama hesabına yönetmek üzere bir Key Vault oluşturur. Kasayı oluşturmak için, projenin bulunduğu kaynak grubunda izinleri (sahip veya katkıda bulunan ve Kullanıcı erişimi Yöneticisi) ayarlarsınız. | İzinleri [ayarlayın](./tutorial-discover-vmware.md#prepare-an-azure-user-account) .

## <a name="supported-geographies-public-cloud"></a>Desteklenen coğrafyalar (genel bulut)

Ortak bulutta bir dizi coğrafi bölgedeki bir proje oluşturabilirsiniz.

- Yalnızca bu coğrafi bölgelerde proje oluşturabilseniz de, diğer hedef konumlar için sunucuları değerlendirebilir veya geçirebilirsiniz.
- Proje Coğrafya yalnızca keşfedilen meta verileri depolamak için kullanılır.
- Bir proje oluşturduğunuzda, bir Coğrafya seçersiniz. Proje ve ilgili kaynaklar Coğrafya 'daki bölgelerden birinde oluşturulur. Bölge, Azure geçişi hizmeti tarafından ayrılır.

**Coğrafya** | **Meta veri depolama konumu**
--- | ---
Asya Pasifik | Doğu Asya veya Güneydoğu Asya
Avustralya | Avustralya Doğu veya Avustralya Güneydoğu
Brezilya | Güney Brezilya
Kanada | Kanada Orta veya Kanada Doğu
Avrupa | Kuzey Avrupa veya Batı Avrupa
Fransa | Orta Fransa
Hindistan | Orta Hindistan veya Güney Hindistan
Japonya |  Japonya Doğu veya Japonya Batı
Güney Kore | Kore Orta veya Kore Güney
İsviçre | İsviçre Kuzey
Birleşik Krallık | UK Güney veya UK Batı
Birleşik Devletler | Orta ABD veya Batı ABD 2

> [!NOTE]
> Isviçre coğrafya için İsviçre Batı yalnızca REST API kullanıcılar için kullanılabilir ve onaylanmış bir aboneliğe gerek duyar.

## <a name="supported-geographies-azure-government"></a>Desteklenen coğrafyalar (Azure Kamu)

**Görev** | **Coğrafya** | **Ayrıntılar**
--- | --- | ---
Proje oluşturma | Birleşik Devletler | Meta veriler US Gov Arizona depolanır, US Gov Virginia
Hedef değerlendirme | Birleşik Devletler | Hedef bölgeler: US Gov Arizona, US Gov Virginia, US Gov Teksas
Hedef çoğaltma | Birleşik Devletler | Hedef bölgeler: US DoD Orta, US DoD Doğu, US Gov Arizona, US Gov Iowa, US Gov Teksas, US Gov Virginia


## <a name="vmware-assessment-and-migration"></a>VMware değerlendirmesi ve geçişi

Azure geçişi: Sunucu değerlendirmesi ve Azure geçişi: VMware VM 'Leri için sunucu geçiş desteği matrisi ' nı [gözden geçirin](migrate-support-matrix-vmware.md) .

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V değerlendirmesi ve geçiş

Azure geçişi: Sunucu değerlendirmesi ve Azure geçişi: Hyper-V VM 'Leri için sunucu geçiş desteği matrisi ' nı [gözden geçirin](migrate-support-matrix-hyper-v.md) .



## <a name="azure-migrate-versions"></a>Azure geçişi sürümleri

Azure geçişi hizmetinin iki sürümü vardır:

- **Geçerli sürüm**: bu sürümü kullanarak yeni projeler oluşturabilir, şirket içi değerlendirir bulabilir ve değerlendirmeleri ve geçişleri yönetebilirsiniz. [Daha fazla bilgi edinin](whats-new.md).
- **Önceki sürüm**: Azure geçişi 'nin önceki sürümünü kullanan müşteri için (yalnızca şirket Içi VMware VM 'lerinin değerlendirmesi desteklenir), artık geçerli sürümü kullanmalısınız. Önceki sürümde yeni projeler oluşturamaz veya yeni bulmalar gerçekleştiremezsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Geçiş için [VMware VM 'Lerini değerlendirin](./tutorial-assess-vmware-azure-vm.md) .
- Geçiş için [Hyper-V VM 'Lerini değerlendirin](tutorial-assess-hyper-v.md) .
