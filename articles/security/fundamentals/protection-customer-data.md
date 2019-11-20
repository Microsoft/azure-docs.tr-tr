---
title: Azure 'da müşteri verilerinin korunması
description: Bu makalede, Azure 'un müşteri verilerini nasıl koruduğu ele alınmaktadır.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 741cbc82f2ed3ffffb553b146d981b4e35a273f4
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726674"
---
# <a name="azure-customer-data-protection"></a>Azure müşteri verileri koruması   
Microsoft Operasyon ve destek personelinin müşteri verilerine erişimi varsayılan olarak reddedilir. Müşteri verilerine erişim verildiğinde, liderlik onayı gereklidir ve sonra erişim dikkatlice yönetilir ve günlüğe kaydedilir. Erişim denetimi gereksinimleri aşağıdaki Azure Güvenlik Ilkesiyle belirlenir:

- Varsayılan olarak müşteri verilerine erişim yoktur.
- Müşteri sanal makinelerinde (VM) Kullanıcı veya yönetici hesabı yok.
- Görevi tamamlaması gereken en düşük ayrıcalığa izin verin; Denetim ve günlük erişim istekleri.

Azure destek personeline, Microsoft tarafından atanan benzersiz kurumsal Active Directory hesapları atanır. Azure, önemli bilgi sistemlerine erişimi denetlemek için Microsoft Information Technology (MSıT) tarafından yönetilen Microsoft kurumsal Active Directory bağımlıdır. Multi-Factor Authentication gereklidir ve yalnızca güvenli konsollardan erişime izin verilir.

Tüm erişim girişimleri izlenir ve temel bir raporlar kümesi aracılığıyla görüntülenebilir.

## <a name="data-protection"></a>Veri koruma
Azure, müşterilere hem varsayılan hem de müşteri seçenekleri olarak güçlü veri güvenliği sağlar.

**Veri**ayırma: Azure, birden fazla müşteri dağıtımı ve VM 'Lerin aynı fiziksel donanımda depolandığı anlamına gelen çok kiracılı bir hizmettir. Azure, her müşterinin verilerini diğerlerinin verilerinden ayırmak için mantıksal yalıtım kullanır. Ayrımı, müşterilerin farklı bir verilere erişmesini zorluklarken, çok kiracılı hizmetlerden oluşan ölçek ve ekonomik avantajlar sağlar.

**REST veri koruması**: Müşteriler, Azure 'da depolanan verilerin standartlarına uygun şekilde şifrelendiğinden emin olmanın sorumluluğundadır. Azure, müşterilere ihtiyaçlarını en iyi şekilde karşılayan çözümü seçme esnekliği sunarak çok çeşitli şifreleme özellikleri sunmaktadır. Azure Key Vault, müşterilerin verileri şifrelemek için bulut uygulamaları ve Hizmetleri tarafından kullanılan anahtarların denetimini kolayca korumasına yardımcı olur. Azure disk şifrelemesi, müşterilerin VM 'Leri şifrelemesini sağlar. Azure Depolama Hizmeti Şifrelemesi, bir müşterinin depolama hesabına yerleştirilmiş tüm verileri şifrelemeyi mümkün hale getirir.

**Aktarım sırasında veri koruması**: Müşteriler, kendi VM 'Leri ve son kullanıcılar arasındaki trafik için şifrelemeyi etkinleştirebilir. Azure, geçiş sırasında, iki sanal ağ arasındaki gibi şirket içi ve dışarıdan veri aktarımı içindeki verileri ve dışarıdan verileri korur. Azure, şu arasındaki iletişimleri şifrelemek için CESG/NCSC tarafından önerilen 2.048 bit RSA/SHA256 şifreleme anahtarları ile endüstri standardı Aktarım Katmanı Güvenliği (TLS) 1,2 veya üzeri protokol kullanır:

- Müşteri ve bulut.
- Azure sistemleri ve veri merkezleri arasında dahili olarak.

**Şifreleme**: Verilerin gizliliğini ve bütünlüğünü sağlamak için en iyi yöntem olarak, depolamada ve aktarımda veri şifreleme, müşteriler tarafından dağıtılabilir. Müşterilerin Azure bulut hizmetlerini, internet 'ten gelen iletişimleri ve hatta Azure 'da barındırılan VM 'Leri arasında korumak üzere SSL kullanacak şekilde yapılandırması kolaydır.

**Veri artıklığı**: Microsoft, veri merkezinde bir sizme veya fiziksel hasar oluşması durumunda verilerin korunmasını sağlar. Müşteriler şunları kabul edebilir:

- Uyumluluk veya gecikme sorunları için ülke içi/bölge içi depolama.
- Güvenlik veya olağanüstü durum kurtarma amacıyla ülke dışı/bölge dışı depolama.

Veriler, artıklık için seçili coğrafi alan içinde çoğaltılarak, ancak dışında iletilebilir. Müşterilerin kopya sayısı ve çoğaltma veri merkezlerinin sayısı ve konumu dahil olmak üzere verileri çoğaltmak için birden çok seçeneği vardır.

Depolama hesabınızı oluşturduğunuzda, aşağıdaki çoğaltma seçeneklerinden birini seçin:

- **Yerel olarak yedekli depolama (LRS)** : Yerel olarak yedekli depolama verilerinizin üç kopyasını tutar. LRS, tek bir bölgedeki tek bir tesis içinde üç kez çoğaltılır. LRS, verilerinizi normal donanım arızalarından korur, ancak tek bir tesisin arızasından vermez.
- Bölgesel olarak **yedekli depolama (ZRS)** : Alanlar arası yedekli depolama verilerinizin üç kopyasını tutar. ZRS, LRS 'den daha yüksek dayanıklılık sağlamak için iki ve üç tesiste üç kez çoğaltılır. Çoğaltma, tek bir bölgede veya iki bölge içinde gerçekleşir. ZRS, verilerinizin tek bir bölge içinde dayanıklı olmasını sağlamaya yardımcı olur.
- **Coğrafi olarak yedekli depolama (GRS)** : Coğrafi olarak yedekli depolama, depolama hesabınızı oluşturduğunuzda hesabınız için varsayılan olarak etkinleştirilir. GRS verilerinizin altı kopyasını tutar. GRS ile verileriniz birincil bölge içinde üç kez çoğaltılır. Verileriniz, birincil bölgeden yüzlerce mil uzakta olan ikincil bölgede üç kez de çoğaltılır ve en yüksek düzeyde dayanıklılık sağlar. Birincil bölgedeki bir hata durumunda Azure Storage ikincil bölgeye yük devreder. GRS, verilerinizin iki ayrı bölgede dayanıklı olmasını sağlamaya yardımcı olur.

**Veri yok etme**: Müşteriler verileri sildiğinden veya Azure 'a ayrıldığında, Microsoft, depolama kaynaklarının yeniden kullanılmadan önce üzerine yazılması için katı standartların yanı sıra kullanımdan kaldırılan donanımın fiziksel olarak yok edilmesini de izler. Microsoft, müşteri isteğinde ve sözleşme sonlandırmasıyla ilgili verilerin tamamını silme işlemini yürütür.

## <a name="customer-data-ownership"></a>Müşteri verileri sahipliği
Microsoft, müşterilerin Azure 'a dağıtacağı uygulamaları denetlemez, onaylamaz veya izlemez. Üstelik, Microsoft Azure 'da ne tür veri müşterileri istediğinizi bilmez. Microsoft, Azure 'a girilen müşteri bilgileri üzerinde veri sahipliğini talep etmez.

## <a name="records-management"></a>Kayıt yönetimi
Azure, arka uç verileri için bekletme gereksinimleri olan iç kayıtları kurdu. Müşteriler kendi kayıt saklama gereksinimlerini belirlemekten sorumludur. Azure 'da depolanan kayıtlar için müşteriler, verilerini ayıklamaktan ve müşteri tarafından belirtilen bir bekletme dönemi boyunca bunların içeriğini Azure dışında korumadan sorumludur.

Azure, müşterilerin verileri ve Denetim raporlarını üründen dışarı aktaralmasına olanak tanır. Dışarı aktarmalar, müşteri tarafından tanımlanan bir saklama süresi dönemi için bilgileri saklamak üzere yerel olarak kaydedilir.

## <a name="electronic-discovery-e-discovery"></a>Elektronik bulma (e-bulma)
Azure müşterileri, Azure hizmetleri kullanımıyla ilgili e-bulma gereksinimleriyle uyumlu değildir. Azure müşterilerinin müşteri verilerini koruması gerekiyorsa, verileri yerel olarak dışa aktarabilir ve kaydedebilirler. Ayrıca, müşteriler Azure müşteri destek departmanından verilerinin dışarı aktarmaları talep edebilir. Müşterilerin verilerini dışarı aktarmaya izin vermenin yanı sıra Azure, kapsamlı günlük kaydı ve izleme dahili olarak.

## <a name="next-steps"></a>Sonraki adımlar
Microsoft 'un Azure altyapısını güvenli hale getirmeye yönelik daha fazla bilgi edinmek için bkz.:

- [Azure tesisler, şirket içi ve fiziksel güvenlik](physical-security.md)
- [Azure altyapı kullanılabilirliği](infrastructure-availability.md)
- [Azure Information System bileşenleri ve sınırları](infrastructure-components.md)
- [Azure ağ mimarisi](infrastructure-network.md)
- [Azure üretim ağı](production-network.md)
- [Azure SQL veritabanı güvenlik özellikleri](infrastructure-sql.md)
- [Azure üretim işlemleri ve yönetimi](infrastructure-operations.md)
- [Azure altyapı izleme](infrastructure-monitoring.md)
- [Azure altyapı bütünlüğü](infrastructure-integrity.md)
