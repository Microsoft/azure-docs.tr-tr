---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: e61b804c20063e87c19df4484291f81fdbb03a7b
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72601164"
---
>[!NOTE]
>Düzeltilmeyen kaynaklar için, kotalarda artış istemek üzere bir destek bileti açın. Daha yüksek limitleri elde etmeye yönelik bir denemede ek Azure Media Services hesapları oluşturmayın.

| Kaynak | Varsayılan limit | 
| --- | --- | 
| Tek bir abonelikteki hesapları Azure Media Services | 25 (sabit) |
| Media Services hesap başına medya ayrılmış birimleri |25 (S1)<br/>10 (S2, S3)<sup>1</sup> | 
| Media Services hesap başına iş sayısı | 50.000<sup>2</sup> |
| İş başına zincirleme görev sayısı | 30 (sabit) |
| Media Services hesap başına varlık sayısı | 1\.000.000|
| Görev başına varlık sayısı | 50 |
| İş başına varlık sayısı | 100 |
| Tek seferde bir varlıkla ilişkilendirilen benzersiz bulucu sayısı | 5<sup>4</sup> |
| Media Services hesap başına Canlı Kanallar |5|
| Kanal başına durdurulmuş durumdaki program sayısı |50|
| Kanal başına çalışır durumdaki program sayısı |3|
| Media Services hesap başına durdurulan veya çalıştırılan akış uç noktaları|2|
| Akış uç noktası başına akış birimleri |10 |
| Depolama hesapları | 1\.000<sup>5</sup> (sabit) |
| İlkeler | 1\.000.000<sup>6</sup> |
| Dosya boyutu| Bazı senaryolarda, Media Services ' de işlenmek üzere desteklenen en büyük dosya boyutu sınırı vardır. <sup>7</sup> |

<sup>1</sup> Türü (örneğin, S2 'den S1 'e) değiştirirseniz, en fazla ayrılmış birim sınırları sıfırlanır.

<sup>2</sup> Bu sayı, kuyruğa alınmış, tamamlanmış, etkin ve iptal edilmiş işleri içerir. Silinen işleri içermez. Eski işleri, **ıjob. Delete** veya **Delete** http isteğini kullanarak silebilirsiniz.

1 Nisan 2017 itibariyle, hesabınızda 90 günden eski olan iş kayıtları, ilişkili görev kayıtlarıyla birlikte otomatik olarak silinir. Toplam kayıt sayısı en fazla kotanın altındaysa bile otomatik silme gerçekleşir. İş ve görev bilgilerini arşivlemek için [Media Services .NET SDK ile varlıkları yönetme](../articles/media-services/previous/media-services-dotnet-manage-entities.md)bölümünde açıklanan kodu kullanın.

<sup>3</sup> İş varlıklarını listelemek için bir istek yaptığınızda, istek başına en fazla 1.000 iş döndürülür. Gönderilen tüm işleri izlemek için, [OData sistem sorgu seçenekleri](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))' nde açıklandığı gibi en üstteki veya sorguları atla ' yı kullanın.

<sup>4</sup> Konumlandırıcı, Kullanıcı başına erişim denetimini yönetmek için tasarlanmamıştır. Bireysel kullanıcılara farklı erişim hakları vermek için dijital hak yönetimi (DRM) çözümlerini kullanın. Daha fazla bilgi için bkz. [Azure Media Services Içeriğinizi koruma](../articles/media-services/previous/media-services-content-protection-overview.md).

<sup>5</sup> Depolama hesapları aynı Azure aboneliğinden olmalıdır.

<sup>6</sup> Farklı Media Services ilkeleri için 1.000.000 ilke sınırlaması vardır. Bir örnek, bulucu ilkesi veya ContentKeyAuthorizationPolicy için bir örnektir. 

>[!NOTE]
> Her zaman aynı günleri ve erişim izinlerini kullanırsanız, aynı ilke KIMLIĞINI kullanın. Bilgi ve örnek için bkz. [Media Services .NET SDK ile varlıkları yönetme](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies).

<sup>7</sup> Tek bir blob için desteklenen en büyük boyut Şu anda Azure Blob depolamada 5 TB 'a kadar. Ek sınırlar, hizmet tarafından kullanılan VM boyutlarına göre Media Services geçerlidir. Boyut sınırı, karşıya yüklediğiniz dosyalar ve ayrıca Media Services işleme sonucu olarak oluşturulan dosyalar için geçerlidir (kodlama veya çözümleme). Kaynak dosyanız 260 GB 'tan büyükse, Işiniz muhtemelen başarısız olur. 

Aşağıdaki tabloda S1, S2 ve S3 medya ayrılmış birimleri üzerindeki sınırlar gösterilmektedir. Kaynak dosyanız tabloda tanımlanan sınırlardan daha büyükse, kodlama işiniz başarısız olur. Uzun süreli 4K çözümleme kaynaklarını kodlarsanız, gereken performansı elde etmek için S3 medya ayrılmış birimlerini kullanmanız gerekir. S3 medya ayrılmış birimlerindeki 260 GB sınırından büyük 4K içeriğiniz varsa bir destek bileti açın.

|Medya ayrılmış birim türü   |Maksimum giriş boyutu (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|
