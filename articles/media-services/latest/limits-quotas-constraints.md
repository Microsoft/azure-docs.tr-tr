---
title: Azure Media Services v3 içindeki kotalar ve sınırlamalar | Microsoft Docs
description: Bu konuda Azure Media Services v3 içindeki kotalar ve sınırlamalar açıklanmaktadır
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 2a530d6a1a447ccde448259623da6faeaa6a4d72
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598356"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Azure Media Services v3 içindeki kotalar ve sınırlamalar

Bu makalede Azure Media Services v3 içindeki kotalar ve sınırlamalar açıklanmaktadır.

| Kaynak | Varsayılan Sınır | 
| --- | --- | 
| Azure Media Services hesabı başına Varlık sayısı | 1\.000.000|
| Dinamik Bildirim Filtreleri|100|
| Iş başına Jobgirişler | 50 (düzeltildi)|
| Iş başına Joi koyar | 20 (Sabit) |
| Dönüşümde Transformoutkoyar | 20 (Sabit) |
| Jobınput başına dosya sayısı|10 (Sabit)|
| Dosya boyutu| Bazı senaryolarda, Media Services ' de işlenmek üzere desteklenen en büyük dosya boyutu sınırı vardır. <sup>(1</sup> |
| Media Services hesap başına iş sayısı | 500.000 <sup>(2)</sup> (Sabit)|
| Media Services hesabı başına Canlı Etkinlik sayısı |5|
| Tek bir abonelikteki hesapları Media Services | 25 (sabit) |
| Canlı olay başına canlı çıktılar |3 <sup>(3)</sup> |
| En büyük canlı çıkış süresi | 25 saat |
| Depolama hesapları | 100<sup>(4)</sup> (Sabit) |
| Media Services hesap başına akış uç noktaları (durdurulmuş veya çalışıyor)|2 (Sabit)|
| Akış İlkeleri | 100 <sup>(5)</sup> |
| Media Services hesap başına dönüşümler | 100 (düzeltildi)|
| Tek seferde bir varlıkla ilişkili benzersiz akış Konumlandırıcı | 100<sup>(6)</sup> (Sabit) |
| Içerik anahtarı Ilkesi başına seçenekler |30 | 
| Hesap başına Media Services anahtar teslim hizmetindeki her bir DRM türü için aylık lisans sayısı|1\.000.000|

<sup>1</sup> tek bir blob için desteklenen en büyük boyut Şu anda Azure Blob depolamada 5 TB 'a kadar. Ek sınırlar, hizmet tarafından kullanılan VM boyutlarına göre Media Services geçerlidir. Boyut sınırı, karşıya yüklediğiniz dosyalar ve ayrıca Media Services işleme sonucu olarak oluşturulan dosyalar için geçerlidir (kodlama veya çözümleme). Kaynak dosyanız 260 GB 'tan büyükse, Işiniz muhtemelen başarısız olur. 

Aşağıdaki tabloda S1, S2 ve S3 medya ayrılmış birimleri üzerindeki sınırlar gösterilmektedir. Kaynak dosyanız tabloda tanımlanan sınırlardan daha büyükse, kodlama işiniz başarısız olur. Uzun süreli 4K çözümleme kaynaklarını kodlarsanız, gereken performansı elde etmek için S3 medya ayrılmış birimlerini kullanmanız gerekir. S3 medya ayrılmış birimlerindeki 260 GB sınırından büyük 4K içeriğiniz varsa bir destek bileti açın.

|Medya ayrılmış birim türü   |Maksimum giriş boyutu (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> bu sayı, kuyruğa alınmış, tamamlanmış, etkin ve Iptal edilmiş işleri içerir. Silinen Işleri içermez. 

Toplam kayıt sayısı maksimum kotanın altında olsa da, hesabınızdaki 90 günden eski bir Iş kaydı otomatik olarak silinir. 

<sup>3</sup> canlı çıktılar oluşturma sırasında başlar ve silindiğinde durdurulur.

<sup>4</sup> depolama hesapları aynı Azure aboneliğinden olmalıdır.

<sup>5</sup> özel bir [akış Ilkesi](https://docs.microsoft.com/rest/api/media/streamingpolicies)kullanırken, medya hizmeti hesabınız için sınırlı sayıda ilke kümesi tasarlamalı ve aynı şifreleme seçenekleri ve protokoller gerektiğinde bunları streamingbulucular için yeniden kullanmanız gerekir. Her bir akış bulucu için yeni bir akış Ilkesi oluşturmamalısınız.

<sup>6</sup> akış Konumlandırıcı, Kullanıcı başına erişim denetimini yönetmek için tasarlanmamıştır. Ayrı kullanıcılara farklı erişim hakları vermek için Digital Rights Management (DRM) çözümlerini kullanın.

## <a name="support-ticket"></a>Destek bileti

Düzeltilmeyen kaynaklar için bir [destek bileti](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)açarak kotaların ortaya çıkarılmasını isteyebilirsiniz. İsteğe bağlı istenen kota değişiklikleri, kullanım örneği senaryoları ve gerekli bölgeler üzerinde ayrıntılı bilgi ekleyin. <br/>Daha yüksek sınırlar elde etmek için başka Azure Media Services hesapları **oluşturmayın**.

## <a name="next-steps"></a>Sonraki adımlar

[Genel Bakış](media-services-overview.md)
