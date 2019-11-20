---
title: Azure maliyet yönetimi 'nde AWS maliyetlerini ve kullanımını yönetme
description: Bu makale, AWS maliyetlerinizi ve kullanımınızı yönetmek için maliyet yönetimi 'nde maliyet analizini ve bütçeleri nasıl kullanacağınızı anlamanıza yardımcı olur.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 772f6cdde575a9ac669c73ecca039914357ffe2f
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338892"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Azure 'da AWS maliyetlerini ve kullanımını yönetme

Azure maliyet yönetimi için AWS maliyet ve kullanım raporu tümleştirmesini ayarladıktan ve yapılandırdıktan sonra AWS maliyetlerinizi ve kullanımınızı yönetmeye hazırsınız demektir. Bu makale, AWS maliyetlerinizi ve kullanımınızı yönetmek için maliyet yönetimi 'nde maliyet analizini ve bütçeleri nasıl kullanacağınızı anlamanıza yardımcı olur.

Tümleştirmeyi zaten yapılandırmadıysanız, bkz. [AWS kullanım raporu tümleştirmesini ayarlama ve yapılandırma](aws-integration-set-up-configure.md).

_Başlamadan önce_: Maliyet analizinden daha fazla bilginiz varsa bkz. [Maliyet Analizi ile maliyetleri İnceleme ve çözümleme](quick-acm-cost-analysis.md) hızlı başlangıcı. Ayrıca, Azure 'daki bütçeleri tanımıyorsanız, [Azure bütçeleri oluşturma ve yönetme](tutorial-acm-create-budgets.md) öğreticisine bakın.

## <a name="view-aws-costs-in-cost-analysis"></a>Maliyet analizinde AWS maliyetlerini görüntüleme

AWS maliyetleri, aşağıdaki kapsamlarda maliyet analizi kapsamında mevcuttur:

- Yönetim grubu altındaki AWS bağlı hesapları
- AWS bağlı hesap maliyetleri
- AWS birleştirilmiş hesap maliyetleri

Sonraki bölümlerde, her birinin maliyet ve kullanım verilerini görmeniz için kapsamların nasıl kullanılacağı açıklanır.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Yönetim grubu altındaki AWS bağlı hesaplarını görüntüle

Yönetim grubu kapsamını kullanarak maliyetleri görüntülemek, farklı aboneliklerden ve bağlı hesaplardan gelen toplam maliyetleri görmenin tek yoludur. Bir yönetim grubu kullanmak, platformlar arası bir görünüm sağlar.

Maliyet Analizi ' nde kapsam seçiciyi açın ve AWS bağlantılı hesaplarınızı tutan yönetim grubunu seçin. Azure portal örnek bir resim aşağıda verilmiştir:

![Seçim kapsamı görünümü örneği](./media/aws-integration-manage/select-scope01.png)



Aşağıda, sağlayıcıya (Azure ve AWS) göre gruplanan, maliyet analizi 'nde Yönetim grubu maliyetinin gösterildiği bir örnek verilmiştir.

![Maliyet analizinde bir çeyrek için Azure ve AWS maliyetlerini gösteren örnek](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>AWS bağlı hesap maliyetlerini görüntüle

AWS bağlantısı hesap maliyetlerini görüntülemek için kapsam seçiciyi açın ve AWS bağlı hesabını seçin. Bağlı hesapların AWS bağlayıcısında tanımlandığı şekilde bir yönetim grubuyla ilişkilendirildiğini unutmayın.

Bir AWS bağlı hesap kapsamını seçmeyi gösteren bir örnek aşağıda verilmiştir.

![Seçim kapsamı görünümü örneği](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>AWS birleştirilmiş hesap maliyetlerini görüntüle

AWS birleştirilmiş hesap maliyetlerini görüntülemek için kapsam seçiciyi açın ve AWS birleştirilmiş hesabını seçin. Aşağıda, bir AWS birleştirilmiş hesap kapsamının seçilmesinin gösterildiği bir örnek verilmiştir.

![Seçim kapsamı görünümü örneği](./media/aws-integration-manage/select-scope03.png)



Bu kapsam, AWS birleştirilmiş hesabıyla ilişkili tüm AWS bağlı hesaplarının toplu bir görünümünü sağlar. Aşağıda, bir AWS birleştirilmiş hesabının, hizmet adına göre gruplanan maliyetleri gösteren bir örnek verilmiştir.

![Maliyet analizinde AWS birleştirilmiş maliyetlerin gösterildiği örnek](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Filtreleme ve gruplama için kullanılabilen Boyutlar

Aşağıdaki tabloda, Grup ve filtreleme için kullanılabilen boyutlar maliyet analizine göre açıklanmaktadır.

| Boyut | Amazon CUR üst bilgisi | Kapsamlar | Açıklamalar |
| --- | --- | --- | --- |
| Kullanılabilirlik bölgesi | LineItem/kullanılabilirliği Bilityzone | Tümü |   |
| Location | Ürün/bölge | Tümü |   |
| Metre |   | Tümü |   |
| Ölçüm kategorisi | LineItem/ProductCode | Tümü |   |
| Ölçüm alt kategorisi | LineItem/UsageType | Tümü |   |
| İşlem | LineItem/Işlem | Tümü |   |
| Resource | LineItem/RESOURCEID | Tümü |   |
| Kaynak türü | Ürün/InstanceType | Tümü | Ürün/InstanceType null ise, LineItem/UsageType kullanılır. |
| Kaynak Guid'si | Yok | Tümü | Azure ölçüm GUID 'ı. |
| Hizmet adı | Ürün/ÜrünAdı | Tümü | Ürün/ÜrünAdı null ise, LineItem/ProductCode kullanılır. |
| Hizmet katmanı |   |   |   |
| Abonelik Kimliği | LineItem/Usageaccountıd | Birleştirilmiş hesap ve yönetim grubu |   |
| Abonelik adı | Yok | Birleştirilmiş hesap ve yönetim grubu | Hesap adları AWS kuruluş API 'SI kullanılarak toplanır. |
| Etiket | resourceTags/\* | Tümü | _Kullanıcı:_ ön ek, platformlar arası etiketlere izin vermek için Kullanıcı tanımlı etiketlerden kaldırılır. _AWS:_ öneki bozulmadan bırakılır. |
| Faturalama hesabı kimliği | Fatura/Payeraccountıd | Yönetim grubu |   |
| Faturalama hesabı adı | Yok | Yönetim grubu | Hesap adları AWS kuruluş API 'SI kullanılarak toplanır. |
| Sağlayıcı | Yok | Yönetim grubu | AWS veya Azure. |

## <a name="set-budgets-on-aws-scopes"></a>AWS kapsamlarında bütçeleri ayarlama

Bütçelerde maliyetleri kullanarak maliyetlerinizi önceden yönetebilir ve kuruluşunuzdaki sorumlulukları yönetin. Bütçeler AWS birleştirilmiş hesabı ve AWS bağlı hesap kapsamları üzerinde ayarlanır. Aşağıda, maliyet yönetimi 'nde gösterilen bir AWS birleştirilmiş hesabının bütçeleriyle ilgili bir örnek verilmiştir:

![AWS birleştirilmiş hesabı için bütçeleri gösteren örnek](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>AWS veri toplama işlemi

AWS bağlayıcısını ayarladıktan sonra veri toplama ve bulma işlemi başlar. Tüm kullanım verilerinin toplanması birkaç saat sürebilir. Süre şunlara bağlıdır:

- AWS S3 demetini içindeki GEÇERLI dosyaları işlemek için gereken süre.
- AWS birleştirilmiş hesabının ve AWS bağlı hesap kapsamlarının oluşturulması için gereken süre.
- AWS 'nin saat ve sıklığı, S3 demet içindeki maliyet ve kullanım raporu dosyalarını yazıyor

## <a name="aws-integration-pricing"></a>AWS tümleştirme fiyatlandırması

Her AWS Bağlayıcısı 90 ücretsiz deneme günü alır. Genel Önizleme sırasında ücret alınmaz.

Liste fiyatı AWS aylık maliyetlerinizin% 1 ' i oranında. Her ay, önceki aydan faturalanan maliyetlerinizi temel alarak ücretlendirilirsiniz.

AWS API 'Lerine erişmek için ek ücret uygulanabilir.

## <a name="aws-integration-limitations"></a>AWS tümleştirme sınırlamaları

- Maliyet yönetimi, birden çok para birimi türü içeren maliyet raporlarını desteklemez. Birden çok para birimi olan bir kapsam seçerseniz bir hata iletisi gösterilir.
- Bulut bağlayıcıları AWS GovCloud (US), AWS gov veya AWS Çin 'yi desteklemez.
- Maliyet yönetimi yalnızca AWS _kullanım maliyetlerini_ gösterir. Vergi, destek, para iadesi, RI, krediler veya diğer ücret türleri henüz desteklenmiyor.

## <a name="troubleshooting-aws-integration"></a>AWS tümleştirmesinde sorun giderme

Sık karşılaşılan sorunları çözmek için aşağıdaki sorun giderme bilgilerini kullanın.

### <a name="no-permission-to-aws-linked-accounts"></a>AWS bağlantılı hesaplara izin yok

**Hata kodu:** _Erişilmesini_

AWS bağlı hesap maliyetlerine erişim izinleri almanın iki yolu vardır:

- AWS bağlantılı hesaplarına sahip yönetim grubuna erişim sağlayın.
- AWS bağlantılı hesabı için size izin verin.

Varsayılan olarak AWS Bağlayıcısı Oluşturucu, bağlayıcının oluşturduğu tüm nesnelerin sahibidir. Dahil olmak üzere, AWS birleştirilmiş hesabı ve AWS bağlantılı hesabı.

Bağlayıcı ayarlarını doğrulayabilmek için en az bir katkıda bulunan rolüne ihtiyacınız olacaktır, okuyucu bağlayıcı ayarlarını doğrulayamaz

### <a name="collection-failed-with-assumerole"></a>Koleksiyon AssumeRole ile başarısız oldu

**Hata kodu:** _FailedToAssumeRole_

Bu hata, maliyet yönetiminin AWS AssumeRole API 'sini çağıramadığı anlamına gelir. Bu sorun, rol tanımıyla ilgili bir sorun nedeniyle ortaya çıkabilir. Aşağıdaki koşulların doğru olduğundan emin olun:

- Dış KIMLIK, rol tanımınızdaki ve bağlayıcı tanımındaki ile aynıdır.
- Rol türü **size veya 3. tarafa ait başka BIR AWS hesabı** olarak ayarlanır.
- **MFA gerektir** seçeneği temizlenir.
- AWS rolündeki güvenilir AWS hesabı _432263259397_' dir.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Koleksiyon, erişim reddedildi hatasıyla başarısız oldu-GEÇERLI rapor tanımları

**Hata kodu:** _AccessDeniedReportDefinitions_ 

Bu hata, maliyet yönetiminin maliyet ve kullanım raporu tanımlarını göremediği anlamına gelir. Bu izin, CUR 'in Azure maliyet yönetimi tarafından beklendiği gibi tanımlandığını doğrulamak için kullanılır. [AWS 'de maliyet ve kullanım raporu oluşturma](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)konusuna bakın.

### <a name="collection-failed-with-access-denied---list-reports"></a>Koleksiyon, erişim reddedildi-liste raporları ile başarısız oldu

**Hata kodu:** _AccessDeniedListReports_ 

Bu hata, maliyet yönetiminin, nesne 'nin bulunduğu S3 demetini içinde listekullanamayacağı anlamına gelir. AWS ıAM ilkesi, demet üzerinde ve demet içindeki nesnelerde bir izin gerektirir. Bkz. [AWS 'de rol ve Ilke oluşturma](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Koleksiyon, erişim reddedildi hatasıyla başarısız oldu-Indirme raporu 

**Hata kodu:** _AccessDeniedDownloadReport_ 

Bu hata, maliyet yönetiminin Amazon S3 demetini içinde depolanan GEÇERLI dosyaları erişemeyeceği ve indiremediği anlamına gelir. Role bağlı AWS JSON ilkesinin, [AWS 'de rol ve Ilke oluşturma](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) bölümünde gösterilen örneğe benzer olduğundan emin olun.

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Maliyet ve kullanım raporu bulamadığı için koleksiyon başarısız oldu

**Hata kodu:** _FailedToFindReport_

Bu hata, maliyet yönetiminin bağlayıcıda tanımlı maliyet ve kullanım raporunu bulamadığı anlamına gelir. Bunun silinmediğinden ve role bağlı AWS JSON ilkesinin [AWS 'de rol ve Ilke oluşturma](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) bölümünde gösterilen örneğe benzer olduğundan emin olun.

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Maliyet ve kullanım raporu tanımlarının eşleşmemesi nedeniyle bağlayıcı oluşturulamıyor veya doğrulanamıyor

**Hata kodu:** _ReportIsNotValid_

AWS maliyet ve kullanım raporunun tanımıyla ilgili bu hata, bu rapor için özel ayarlar gerektirdiğimiz için, [AWS 'de maliyet ve kullanım raporu oluşturma](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws) içindeki gereksinimlere bakın.

## <a name="next-steps"></a>Sonraki adımlar

- Azure ortamınızı zaten yönetim gruplarıyla yapılandırmadıysanız, bkz. [yönetim gruplarının ilk kurulumu](../governance/management-groups/overview.md#initial-setup-of-management-groups).
