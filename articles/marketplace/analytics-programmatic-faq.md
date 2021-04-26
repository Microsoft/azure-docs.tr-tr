---
title: Analiz verilerine program aracılığıyla erişim hakkında sık sorulan sorular
description: Ticari Market tekliflerinizin Iş ortağı merkezindeki analiz verilerine programlı bir şekilde erişme hakkında sık sorulan sorular.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584181"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>Analiz verilerine program aracılığıyla erişim hakkında sık sorulan sorular

Bu makalede, ticari Market teklifleriniz için Iş Ortağı Merkezi 'nde analiz verilerine programlı bir şekilde erişme hakkında sıkça sorulan sorular ele alınmaktadır.

## <a name="api-responses"></a>API yanıtları

200 (başarılı) dışında bir API yanıtı alabildiğim farklı senaryolar nelerdir?

Bu tablo, API yanıtlarını ve bunları alırsanız ne yapılacağını açıklar.

| Hata açıklaması | Hata kodu | Sorun giderme |
| ------------ | ------------- | ------------- |
| Yetkisiz | 401 | Bu bir kimlik doğrulama istisnadır. Azure Active Directory (Azure AD) belirtecinin doğruluğunu denetleyin. Azure AD belirteci 60 dakika boyunca geçerlidir; bu süre sonra Azure AD belirtecini yeniden oluşturmanız gerekir. |
| Geçersiz tablo adı | 400 | Veri kümesinin adı yanlış. "Tüm veri kümelerini al" API 'sini çağırarak veri kümesi adını yeniden denetleyin. |
| Sütun adı yanlış | 400| Sorgudaki sütunun adı yanlış. "Tüm veri kümelerini al" API 'sini çağırarak sütun adını yeniden denetleyin veya aşağıdaki tablolardaki sütun adlarına başvurun:<br><ul><li>[Sipariş Ayrıntıları tablosu](orders-dashboard.md#orders-details-table)</li><li>[Kullanım Ayrıntıları tablosu](usage-dashboard.md#usage-details-table)</li><li>[Müşteri Ayrıntıları tablosu](customer-dashboard.md#customer-details-table)</li><li>[Market öngörüleri Ayrıntıları tablosu](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Null veya eksik değer | 400 | API 'nin istek yükünün bir parçası olarak zorunlu parametreler eksik olabilir. |
| Geçersiz rapor parametreleri | 400 | Rapor parametrelerinin doğru olduğundan emin olun. Örneğin, parametre için 4 ' ten az bir değer vermiş olabilirsiniz `RecurrenceInterval` . |
| Yinelenme aralığı 4 ile 90 arasında olmalıdır | 400 | `RecurrenceInterval`İstek parametresinin değerinin 4 ile 90 arasında olduğundan emin olun. |
| Geçersiz QueryId | 400 | Oluşturulan ' i yeniden denetleyin `QueryId` . |
| Oluşturma için geçersiz rapor parametreleri-raporun başlangıç zamanı, geçerli UTC zamanından en az 4 saat olmalıdır- | 400 | İstek yükünün parçası olarak başlangıç zamanı parametresi geçmişte olmamalıdır. Raporun başlangıç saati, geçerli UTC zamanından en az 4 saat olmalıdır. |
| İstenen ' String ' değeri bulunamadı | 400 | İstek parametrelerini mi, yoksa mi güncelleştirmeyeceğinizi `callbackurl` denetleyin `format` . |
| Verilen filtrelere sahip öğe bulunamadı. | 404 | `reportID`Rapor yürütmeleri API 'sinde kullanılan parametreyi denetleyin. |
| Belirtilen filtre koşulları için oluşmuş yürütmeler yok. Lütfen REPORTID veya ExecutionID 'yi yeniden denetleyin ve raporun zamanlanan yürütme zamanından sonra API 'yi yeniden deneyin | 404 | ' Nin doğru olduğundan emin olun `reportId` . İstek yükünde belirtilen şekilde raporun zamanlanan yürütme zamanından sonra API 'yi yeniden deneyin. |
| Rapor oluşturulurken iç hatayla karşılaşıldı. Bağıntı KIMLIĞI <> | 500 | "StartTime", "QueryStartTime" ve "QueryEndTime" alanları için tarih biçiminin doğru olduğundan emin olun. |
| Hizmet kullanılamıyor | 500 | Sürekli olarak bir hizmet alırsanız (5xx hatası), lütfen bir [destek bileti](support.md)oluşturun. |
||||

## <a name="no-records"></a>Kayıt yok

Raporu güvenli konumdan indirdiğimde API yanıtı 200 ' i alıyorum. Neden kayıt alıyorum?

Sorgudaki dizenin, sütun üst bilgisi için izin verilen değerlerden birine sahip olup olmadığını denetleyin. Örneğin, bu sorgu sıfır sonuç döndürür:

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

Bu örnekte, SKUBillingType için izin verilen değerler ücretli veya ücretsizdir. Çeşitli sütunlarda tüm olası değerler için aşağıdaki tablolara bakın:

- [Sipariş Ayrıntıları tablosu](orders-dashboard.md#orders-details-table)
- [Kullanım Ayrıntıları tablosu](usage-dashboard.md#usage-details-table)
- [Müşteri Ayrıntıları tablosu](customer-dashboard.md#customer-details-table)
- [Market öngörüleri Ayrıntıları tablosu](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>Sonraki adımlar

- [Ticari Market Analytics verilerine erişim için API 'Ler](analytics-available-apis.md)
