---
title: Azure IoT Hub cihaz güncelleştirmesi için istemci hata kodları | Microsoft Docs
description: Bu belge, çeşitli cihaz güncelleştirme bileşenleri için bir istemci hata kodları tablosu sağlar.
author: chrisjlin
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: dbdddc7cee0c3664a83501ba619a38e1cc44e1f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200350"
---
# <a name="device-update-for-iot-hub-error-codes"></a>IoT Hub hata kodları için cihaz güncelleştirmesi

Bu belge, çeşitli cihaz güncelleştirme bileşenlerine yönelik bir hata kodları tablosu sağlar. Bu, sorunları tanılamak ve gidermek için kendi hata kodlarını ayrıştırmayı denemek isteyen kullanıcılar için bir başvuru olarak kullanılmak üzere tasarlanmıştır.

Hata kodları oluşturabilecek iki birincil istemci tarafı bileşeni vardır: Cihaz Güncelleştirme Aracısı ve teslim Iyileştirme Aracısı.

## <a name="device-update-agent"></a>Cihaz Güncelleştirme Aracısı

### <a name="resultcode-and-extendedresultcode"></a>ResultCode ve ExtendedResultCode

IoT Hub çekirdek PnP arabirimine yönelik cihaz güncelleştirmesi `ResultCode` ve `ExtendedResultCode` Bu, sorunları tanılamak için kullanılabilir. Cihaz güncelleştirme çekirdeği PnP arabirimi hakkında [daha fazla bilgi edinin](device-update-plug-and-play.md) .

#### <a name="resultcode"></a>ResultCode

`ResultCode` genel bir durum kodudur ve http durum kodu kuralına uyar.
Http durum kodları hakkında [daha fazla bilgi edinin](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` kodlanmış hata bilgilerine sahip bir tamsayıdır.

Büyük olasılıkla `ExtendedResultCode` pnp arabiriminde işaretli bir tamsayı olarak görülecektir. Kodunu çözmek için, `ExtendedResultCode` işaretli tamsayıyı işaretsiz onaltılık olarak dönüştürün. Yalnızca ilk 4 baytı `ExtendedResultCode` kullanılır ve `F` `FFFFFFF` ilk nibble 'ın **tesis kodu** olduğu ve bitlerin geri kalanının **hata kodu** olduğu formundadır.

**Tesis kodları**

| Tesis kodu     | Description  |
|-------------------|--------------|
| D                 | DO SDK 'dan oluşan hata|
| E                 | Hata kodu bir errno değil |


Örnek:

`ExtendedResultCode``-536870781`

Öğesinin işaretsiz onaltılı temsili `-536870781` `FFFFFFFF E0000083` .

| Yoksayma    | Tesis kodu  | Hata Kodu   |
|-----------|----------------|--------------|
| KULLANıR  | E              | 0000083      |

`0x83` onaltılı değerinde `131` , için errno değeri olan Decimal değerinde `ENOLCK` .

## <a name="delivery-optimization-agent"></a>Teslim Iyileştirme Aracısı
Aşağıdaki tabloda, cihaz güncelleştirme istemcisinin teslim Iyileştirme (DO) bileşeni ile ilgili hata kodları listelenmektedir. DO bileşeni, güncelleştirme içeriğini IoT cihazına indirmekten sorumludur.

DO hata kodu, bir API çağrısına yanıt olarak oluşturulan özel durumlar incelenerek elde edilebilir. Tüm hata kodları 0x80D0 ön eki tarafından tanımlanabilir.

| Hata Kodu  | Dize hatası                       | Tür                 | Description |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | yok                  | Teslim Iyileştirme hizmeti sağlayamadı |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | Işi indir         | Bir dosyanın indirilmesi, tanımlı süre içinde ilerleme bulunmadığını görmez |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | Işi indir         | SetProperty () veya GetProperty (), bilinmeyen bir özellik KIMLIĞIYLE çağrıldı |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | Işi indir         | Bir salt okunurdur özelliğinde SetProperty () çağrılamıyor |
| 0x80D02013L | DO_E_INVALID_STATE                 | Işi indir         | İstenen eyleme, geçerli iş durumunda izin verilmiyor. İş iptal edilmiş veya aktarımını tamamlamış olabilir. Şimdi salt okuma durumunda. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | Işi indir         | İndirme Havuzu (yerel dosya veya akış arabirimi) belirtilmediğinden indirme başlatılamadı |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | Idodownload arabirimi| İndirme, URI sağlamadan başlatıldı |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | Geçici koşullar | Ağ bağlantısı kaybı nedeniyle indirme duraklatıldı |

## <a name="device-update-content-service"></a>Cihaz güncelleştirme içerik hizmeti
Aşağıdaki tabloda, cihaz güncelleştirme hizmetinin içerik hizmeti bileşeni ile ilgili hata kodları listelenmektedir. İçerik hizmeti bileşeni, güncelleştirme içeriğinin içeri aktarılmasını işlemekten sorumludur.

| Hata Kodu                    | Dize hatası                                                               | Sonraki adımlar                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | Aynı kimliğe sahip güncelleştirme zaten var.                              | IoT Hub için bu cihaz güncelleştirmesi örneğine önceden aktarılmamış bir güncelleştirmeyi içeri aktardığınızdan emin olun. |
| "Duplicatecontenmport"      | Aynı anda birden çok kez aktarılan içerik.                  | Updatealreadyile aynı var. |
| "CannotProcessImportManifest" | İçeri aktarma bildirimi işlenirken hata oluştu.                                          | Uygun içeri aktarma bildirim biçimlendirmesi için [kavramları içeri aktarma](./import-concepts.md) ve [güncelleştirme belgelerini içeri](./import-update.md) aktarma bölümüne bakın. |
| "CannotDownload"              | İçeri aktarma bildirimi indirilemiyor.                                           | İçeri aktarma bildirim dosyası URL 'sinin hala geçerli olduğundan emin olun. |
| "CannotParse"                 | İçeri aktarma bildirimi ayrıştırılamıyor.                                              | İçeri aktarma [güncelleştirme](./import-update.md) belgelerinde tanımlanan şemaya karşı, içeri aktarma bildiriminizi doğru olup olmadığını denetleyin. |
| "UnsupportedVersion"          | İçeri aktarma bildirim şeması sürümü desteklenmiyor.                           | İçeri aktarma bildiriminiz [içeri aktarma güncelleştirme](./import-update.md) belgelerinde tanımlanan en son şemayı kullanmakta olduğundan emin olun. |
| "Updatelimitexcelıbaşında"         | Aşılan sınır nedeniyle güncelleştirme içeri aktarılırken hata oluştu.                              | IoT Hub için cihaz güncelleştirmesi Örneğinizde izin verilen farklı sağlayıcı, ad veya sürüm sayısı sınırına ulaştınız. Örnekten bazı güncelleştirmeleri silip yeniden deneyin. |
| "UpdateProvider"              | Yeni bir güncelleştirme sağlayıcısı içeri aktarılamıyor.                                       | IoT Hub için cihaz güncelleştirmesi Örneğinizde izin verilen farklı __sağlayıcıların__ sayısı sınırına ulaştınız. Örnekten bazı güncelleştirmeleri silip yeniden deneyin. |
| "UpdateName"                  | Belirtilen sağlayıcı için yeni bir güncelleştirme adı içeri aktarılamıyor.                | IoT Hub cihaz güncelleştirmesi örneğinizdeki bir sağlayıcı altında izin verilen farklı __ad__ sayısı sınırına ulaştınız. Örnekten bazı güncelleştirmeleri silip yeniden deneyin. |
| UpdateVersion               | Belirtilen sağlayıcı ve ad için yeni bir güncelleştirme sürümü içeri aktarılamıyor.    | IoT Hub cihaz güncelleştirmesi örneğinizdeki bir sağlayıcı ve ad altında izin verilen farklı __Sürüm__ sayısı sınırına ulaştınız. Örnekten bu adı taşıyan bazı güncelleştirmeleri silip yeniden deneyin. |
| "UpdateProviderCompatibility" | Belirtilen uyumlulukla ek güncelleştirme sağlayıcısı içeri aktarılamıyor. | Bir içeri aktarma bildiriminde cihaz üreticisi ve cihaz modeli uyumluluk özelliklerini tanımlarken, IoT Hub için cihaz güncelleştirmesinin belirli bir üretici/model için tek bir sağlayıcıyı ve ad bileşimini desteklediğini aklınızda bulundurun. Bu, aynı üretici/model uyumluluk özelliklerini birden fazla sağlayıcı/ad birleşimiyle kullanmayı denerseniz, bu hataları görürsünüz. Bu sorunu çözmek için, belirli bir cihaza yönelik tüm güncelleştirmelerin (üretici/model tarafından tanımlanan şekilde) aynı sağlayıcıyı ve adı kullanmasını sağlayın. Gerekli olmasa da, sağlayıcıyı üretici ile aynı ve ad ile aynı şekilde, tıpkı kolaylık sağlaması için yapmayı düşünmek isteyebilirsiniz. |
| "UpdateNameCompatibility"     | Belirtilen uyumlulukla ek güncelleştirme adı içeri aktarılamıyor.     | UpdateProviderCompatibility. ContentLimitNamespaceCompatibility ile aynı. |
| "UpdateVersionCompatibility"  | Belirtilen uyumlulukla ek güncelleştirme sürümü içe aktarılamıyor.  | UpdateProviderCompatibility. ContentLimitNamespaceCompatibility ile aynı. |
| "CannotProcessUpdateFile"     | Kaynak dosya işlenirken hata oluştu.                                              |                                    |
| "ContentFileCannotDownload"   | Kaynak dosya indirilemiyor.                                               | Güncelleştirme dosyalarının URL 'sinin hala geçerli olduğundan emin olmak için denetleyin. |

**[Sonraki adım: cihaz güncelleştirmesiyle ilgili sorunları giderme](.\troubleshoot-device-update.md)**
