---
title: Azure Market
description: EA müşterilerinin Azure Market’i nasıl kullandığını açıklar
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: ce9dff017a796e420586ad191a59c149bed07190
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726835"
---
# <a name="azure-marketplace"></a>Azure Market

Bu makalede, EA müşterilerinin ve iş ortaklarının market ücretlerini nasıl görüntüleyecekleri ve Azure Market satın alımlarını nasıl etkinleştirecekleri açıklanmaktadır.

## <a name="azure-marketplace-for-ea-customers"></a>EA müşterileri için Azure Market

Doğrudan müşteriler, Azure Market ücretlerini Azure Enterprise Portal'da görüntüleyebilir. Azure Market’ten yapılan satın alma ve tüketim işlemleri, Azure Ön Ödemesinin dışında faturalanır ve üç aylık veya aylık borçlar olarak yazılır.

Dolaylı müşteriler, Azure Market aboneliklerini Azure Enterprise Portal'ın **Abonelikleri Yönet** sayfasında bulabilir ancak fiyatlar gizli olacaktır. Müşteriler, Azure Market fiyatları için Lisanslama Çözümü Sağlayıcısı (LSP) ile iletişime geçmelidir.

Yeni aylık veya yıllık yinelenen Azure Market satın alma işlemleriyle ilgili ücretlerin tamamı, Azure Market öğelerinin satın alındığı dönemde faturalanacaktır. Bu öğeler bir sonraki dönemde ilk satın alma işlemiyle aynı günde otomatik olarak yenilenecektir.

Aylık yinelenen ücretler her takvim ayının ilk gününde yenilenmeye devam edecektir. Yıllık ücretler de satın alma tarihinin yıl dönümünde yenilenecektir.

Azure Market’teki bazı üçüncü taraf satıcı hizmetleri artık Kurumsal Anlaşma (EA) Azure Ön Ödeme bakiyenizi kullanacaktır. Önceden bu hizmetler, EA Azure Ön Ödemesi dışında faturalandırılıyor ve faturası ayrı kesiliyordu. Azure Market’teki bu hizmetler için EA Azure Ön Ödemesi, müşterinin satın alma ve ödeme yönetiminin kolaylaştırılmasına yardımcı olur. Azure Ön Ödemeyi kullanmaya başlayan hizmetlerin tam listesi için lütfen [Azure web sitesindeki 6 Mart 2018 güncelleştirmesini](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/) inceleyin.

### <a name="partners"></a>İş Ortakları

LSP'ler, Azure Enterprise Portal'ın fiyat listesi sayfasından Azure Market fiyat listesini indirebilir. Sağ üstten **Market Fiyat Listesi**'ni seçin. Azure Market fiyat listesinde kullanılabilir durumdaki tüm hizmetler ve fiyatları yer alır.

Fiyat listesini indirmek için:

1. Azure Enterprise Portal'da **Raporlar** > **Fiyat Listesi**'ni seçin.
1. Sağ üst köşede, adınızın altında yer alan Azure Market fiyat listesi bağlantısını bulun.
1. Bağlantıya sağ tıklayıp **Hedefi Farklı Kaydet**'i seçin.
1. **Kaydet** penceresinde belgenin adını `AzureMarketplacePricelist.zip` olarak değiştirin. Bunu yaptığınızda dosya .xlsx yerine .zip dosyasına dönüşür.
1. İndirme işlemi tamamlandıktan sonra ülkeye özgü fiyat listelerinin bulunduğu bir zip dosyası elde edersiniz.
1. LSP'ler ülkeye özgü fiyatlandırma için ilgili ülke dosyasına başvurmalıdır. LSP'ler yeni veya kullanımdan kaldırılan SKU'ları **Bildirimler** sekmesinden takip edebilir.
1. Fiyatlar çok sık değiştirilmez. Fiyat artışları ve döviz kuru (FX) değişiklikleri LSP'lere 30 gün öncesinden e-posta ile bildirilir.
1. LSP'lere kayıt, ISV ve üç aylık dönem başına bir fatura gönderilir.

### <a name="enabling-azure-marketplace-purchases"></a>Azure Market'ten satın alma işlemlerini etkinleştirme

Kuruluş yöneticileri, kendi kayıtları altındaki tüm Azure abonelikleri için Azure Market'ten satın alma işlemlerini devre dışı bırakabilir veya etkinleştirebilir. Kuruluş yöneticilerinin satın alma işlemlerini devre dışı bırakması ve önceden Azure Market aboneliği satın almış olan Azure aboneliklerinin mevcut olması durumunda ilgili Azure Market abonelikleri iptal edilmez ve bu ayardan etkilenmez.

Müşteriler, doğrudan Azure aboneliklerini Azure Enterprise Portal kayıtlarıyla ilişkilendirerek Azure EA aboneliğine dönüştürebilir ancak bu işlem sonucunda alt abonelikleri otomatik olarak dönüştürülmez.

Azure Market'ten satın alma işlemlerini etkinleştirmek için:

1. Azure Enterprise Portal'da kuruluş yöneticisi olarak oturum açın.
1. **Yönet**'e gidin.
1. **Kayıt Ayrıntıları**'nın altında **Azure Market** satırının yanındaki kalem simgesini seçin.
1. **Etkin/Devre Dışı** durumunu veya Ücretsiz **Yalnızca KLG SKU'ları** seçeneğini belirleyin.
1. **Kaydet**’i seçin.

> [!NOTE]
> KLG (kendi lisansını getir) ve Yalnızca Ücretsiz seçeneği, Azure Market SKU satın alma ve edinme işlemlerini KLG ve Ücretsiz SKU'lar ile sınırlar.

### <a name="services-billed-hourly-for-azure-ea"></a>Azure EA için saatlik olarak faturalanan hizmetler

Aşağıdaki hizmetler, MOSP ile aylık ücret yerine Kurumsal Anlaşma kapsamında saatlik olarak faturalandırılır:

- Uygulama Teslim Ağı
- Web Uygulaması Güvenlik Duvarı

### <a name="azure-remoteapp"></a>Azure RemoteApp

Kurumsal Anlaşmanız varsa Azure RemoteApp için Kurumsal Anlaşma fiyat düzeyinize göre ödeme yaparsınız. Ek ücret alınmaz. Standart fiyata ilk 40 saat dahildir. Sınırsız fiyata ilk 80 saat dahildir. RemoteApp, 80 saati aşan kullanımları iletmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Fiyatlandırma](ea-pricing-overview.md) hakkında daha fazla bilgi edinin.
- Azure Marketi Hizmetleri ve Azure EA ön ödemesi hakkında soruların ve yanıtların listesini görmek için [maliyet yönetimi + FATURALANDıRMA SSS](../cost-management-billing-faq.yml) makalesini okuyun.