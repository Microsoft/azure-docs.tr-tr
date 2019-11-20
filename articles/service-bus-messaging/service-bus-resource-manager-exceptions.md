---
title: Azure Service Bus Kaynak Yöneticisi özel durumları | Microsoft Docs
description: Azure Resource Manager ve önerilen eylemler tarafından ortaya çıkacak Service Bus özel durumların listesi.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: darosa
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2019
ms.author: aschhab
ms.openlocfilehash: 25b0c14fb94cba611dfa9fa9bece1b728f39a905
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585215"
---
# <a name="service-bus-resource-manager-exceptions"></a>Service Bus Kaynak Yöneticisi özel durumları

Bu makalede, Azure Resource Manager aracılığıyla şablonlar veya doğrudan çağrılar kullanılarak Azure Service Bus ile etkileşim kurulurken oluşturulan özel durumlar listelenmektedir.

> [!IMPORTANT]
> Bu belge sıkça güncelleştirilir. Lütfen güncelleştirmeler için yeniden denetleyin.

Aşağıda, Azure Resource Manager üzerinden ortaya çıkacak çeşitli özel durumlar/hatalar verilmiştir.

## <a name="error-bad-request"></a>Hata: Hatalı Istek

"Kötü Istek", Kaynak Yöneticisi tarafından alınan isteğin doğrulaması başarısız olduğunu gösterir.

| Hata kodu | Hata alt kodu | Hata iletisi | Açıklama | Öneri |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Hatalı İstek | 40000 | Alt kod = 40000. ' *Namespace Name '* ad alanı ' temel ' katmanını kullandığından, bir kuyruk oluşturulurken *' özellik adı '* özelliği ayarlanamaz. Bu işlem yalnızca ' standart ' veya ' Premium ' katmanda destekleniyor. | Azure Service Bus temel katmanda, aşağıdaki Özellikler ayarlanamaz veya güncelleştirilemez- <ul> <li> RequiresDuplicateDetection </li> <li> Oto Delete OnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Konu başlıkları </li> </ul> | Bu işlevselliği kullanmak için temel bilgisayardan standart veya Premium katmana yükseltmeyi düşünün. |
| Hatalı İstek | 40000 | Alt kod = 40000. Varolan bir kuyruğun (veya konusunun) ' requiresDuplicateDetection ' özelliğinin değeri değiştirilemez. | Yinelenen algılama, varlık oluşturma sırasında etkinleştirilmelidir/devre dışı bırakılmalıdır. Yinelenen algılama yapılandırma parametresi oluşturulduktan sonra değiştirilemez. | Daha önce oluşturulmuş bir kuyrukta/konuda yinelenen saptamayı etkinleştirmek için, yinelenen algılama ile yeni bir kuyruk/konu oluşturabilir ve ardından orijinal kuyruktan yeni kuyruğa/konuya iletebilirsiniz. |
| Hatalı İstek | 40000 | Alt kod = 40000. Belirtilen 16384 değeri geçersiz. ' Maxsizeınmegabayt ' özelliği şu değerlerden biri olmalıdır: 1024; 2048; 3072; 4096; 5120. | Maxsizeınmegabayt değeri geçersiz. | Maxsizeınmegabayt 'nin aşağıdaki-1024, 2048, 3072, 4096, 5120 değerinden biri olduğundan emin olun. |
| Hatalı İstek | 40000 | Alt kod = 40000. Kuyruk/konu için bölümleme değiştirilemiyor. | Varlık için bölümleme değiştirilemez. | Yeni bir varlık (kuyruk veya konu) oluşturun ve bölümleri etkinleştirin. | 
| Hatalı İstek | yok | *' Namespace Name '* ad alanı yok. | Ad alanı Azure aboneliğinizde yok. | Bu hatayı çözmek için lütfen aşağıdaki adımları deneyin <ul> <li> Azure aboneliğinin doğru olduğundan emin olun. </li> <li> Ad alanının mevcut olduğundan emin olun. </li> <li> Ad alanı adının doğru olduğundan emin olun (yazım hatası veya null dizeler yok). </li> </ul> | 
| Hatalı İstek | 40400 | Alt kod = 40400. Otomatik iletme hedefi varlığı yok. | Oto iletme hedef varlığının hedefi yok. | Kaynak oluşturulmadan önce hedef varlık (kuyruk veya konu) var olmalıdır. Hedef varlığı oluşturduktan sonra yeniden deneyin. |
| Hatalı İstek | 40000 | Alt kod = 40000. Sağlanan kilit zamanı, izin verilen en fazla ' 5 ' dakika sınırını aşıyor. | İletinin kilitlenebileceği zaman 1 dakika (en az) ve 5 dakika (en fazla) arasında olmalıdır. | Verilen kilit zamanının 1 dakika ile 5 dakika arasında olduğundan emin olun. |
| Hatalı İstek | 40000 | Alt kod = 40000. Hem Delayedkalıcılık hem de RequiresDuplicateDetection özelliği birlikte etkinleştirilemez. | Yinelenen algılama özelliği etkin olan varlıkların kalıcı olması gerekir, bu nedenle Kalıcılık gecikmemelidir. | [Yinelenen algılama](duplicate-detection.md) hakkında daha fazla bilgi edinin |
| Hatalı İstek | 40000 | Alt kod = 40000. Mevcut bir kuyruğun RequiresSession özelliğinin değeri değiştirilemez. | Varlık oluşturma sırasında oturum desteği etkinleştirilmelidir. Oluşturulduktan sonra, var olan bir varlıkta (kuyruk veya abonelik) oturumları etkinleştiremez/devre dışı bırakamezsiniz | "RequiresSession" özelliği etkinken yeni bir kuyruğu (veya aboneliği) silin ve yeniden oluşturun. |
| Hatalı İstek | 40000 | Alt kod = 40000. ' URI_PATH ', Service Bus izin verilmeyen karakter (ler) içeriyor. Varlık kesimleri yalnızca harf, sayı, nokta (.), kısa çizgi (-) ve alt çizgi (_) içerebilir. | Varlık kesimleri yalnızca harf, sayı, nokta (.), kısa çizgi (-) ve alt çizgi (_) içerebilir. Diğer tüm karakterler isteğin başarısız olmasına neden olur. | URI yolunda geçersiz karakter bulunmadığından emin olun. |


## <a name="error-code-429"></a>Hata kodu: 429

HTTP içinde olduğu gibi, "hata kodu 429", "çok fazla istek" anlamına gelir. Bu kaynakta çok fazla istek (veya çakışan işlemler nedeniyle) nedeniyle belirli bir kaynağın (ad alanı) kısıtlandığı anlamına gelir.

| Hata kodu | Hata alt kodu | Hata iletisi | Açıklama | Öneri |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | Alt kod = 50004. *Ad* alanınız kısıtlandığı için istek sonlandırıldı. | Bu hata koşulu, gelen istek sayısı kaynağın sınırlamasını aştığında oluşur. | Birkaç saniye bekleyip yeniden deneyin. <br/> <br/> [Kotalar](service-bus-quotas.md) ve [Azure Resource Manager istek limitleri](../azure-resource-manager/resource-manager-request-limits.md) hakkında daha fazla bilgi edinin|
| 429 | 40901 | Alt kod = 40901. Başka bir çakışan işlem devam ediyor. | Aynı kaynak/varlık üzerinde başka bir çakışan işlem devam ediyor | Yeniden denemeden önce geçerli devam eden işlemin tamamlanmasını bekleyin. |
| 429 | 40900 | Alt kod = 40900. Uzantıları. Kaynağın geçerli durumunda izin verilmeyen bir işlem isteğinde bulunuyoruz. | Aynı anda aynı varlık (kuyruk, konu, abonelik veya kural) üzerinde işlemleri gerçekleştirmek için birden çok istek yapıldığında bu koşul çıkabilir. | Birkaç saniye bekleyip yeniden deneyin |
| 429 | 40901 | *' Varlık adı '* varlığındaki istek başka bir istekle çakışıyor | Aynı kaynak/varlık üzerinde başka bir çakışan işlem devam ediyor | Yeniden denemeden önce önceki işlemin tamamlanmasını bekleyin |
| 429 | 40901 | *' Varlık adı '* varlığı için başka bir güncelleştirme isteği devam ediyor. | Aynı kaynak/varlık üzerinde başka bir çakışan işlem devam ediyor | Yeniden denemeden önce önceki işlemin tamamlanmasını bekleyin |
| 429 | yok | Kaynak çakışması oluştu. Başka bir çakışan işlem devam ediyor olabilir. Bu, başarısız olan bir işlem için yeniden denemeye devam ederse, arka planda temizlik hala bekliyor. Daha sonra yeniden deneyin. | Aynı varlığa karşı bekleyen bir işlem olduğunda bu durum görünebilir. | Yeniden denemeden önce önceki işlemin tamamlanmasını bekleyin. |


## <a name="error-code-not-found"></a>Hata kodu: bulunamadı

Bu hata sınıfı, kaynağın bulunamadığını gösterir.

| Hata kodu | Hata alt kodu | Hata iletisi | Açıklama | Öneri |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Bulunamadı | yok | *' Varlık adı '* varlığı bulunamadı. | İşlemin bulunamadığı varlık. | Varlığın mevcut olup olmadığını denetleyin ve işlemi yeniden deneyin. |
| Bulunamadı | yok | Bulunamadı. Işlem yok. | Gerçekleştirmeye çalıştığınız işlem yok. | İşlemi denetleyip yeniden deneyin. |
| Bulunamadı | yok | Gelen istek, bir ad alanı ilkesi PUT isteği olarak tanınmıyor. | Gelen istek gövdesi null ve bu nedenle bir PUT isteği olarak yürütülemez. | Lütfen, null olmadığından emin olmak için istek gövdesini kontrol edin. | 
| Bulunamadı | yok | *' Varlık adı '* mesajlaşma varlığı bulunamadı. | İşlemi yürütmeye çalıştığınız varlık bulunamadı. | Lütfen varlığın mevcut olup olmadığını denetleyin ve işlemi yeniden deneyin. |

## <a name="error-code-internal-server-error"></a>Hata kodu: Iç sunucu hatası

Bu hata sınıfı bir iç sunucu hatası olduğunu belirtir

| Hata kodu | Hata alt kodu | Hata iletisi | Açıklama | Öneri |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| İç sunucu hatası | 50000 | Alt kod = 50000. İç sunucu hatası| Çeşitli nedenlerden kaynaklanabilir. Belirtilerden bazıları- <ul> <li> İstemci isteği/gövdesi bozuk ve bir hataya yol açar. </li> <li> Hizmette işleme sorunları nedeniyle istemci isteği zaman aşımına uğradı. </li> </ul> | Bu sorunu çözmek için <ul> <li> İstek parametrelerinin null veya hatalı biçimlendirilmiş olmadığından emin olun. </li> <li> İsteği yeniden deneyin. </li> </ul> |

## <a name="error-code-unauthorized"></a>Hata kodu: yetkisiz

Bu hata sınıfı, komutu çalıştırmak için Yetkilendirme yokluğunu gösterir.

| Hata kodu | Hata alt kodu | Hata iletisi | Açıklama | Öneri |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Yetkisiz | yok | Ikincil ad alanında geçersiz işlem. İkincil ad alanı salt okunurdur. | İşlem, salt okunur bir ad alanı olarak kurulum olan ikincil ad alanına karşı gerçekleştirildi. | Komutu birincil ad alanına karşı yeniden deneyin. [İkincil ad alanı](service-bus-geo-dr.md) hakkında daha fazla bilgi |
| Yetkisiz | yok | MissingToken: yetkilendirme üst bilgisi bulunamadı. | Bu hata, yetkilendirmede null veya hatalı değerler olduğunda oluşur. | Yetkilendirme üstbilgisinde belirtilen belirteç değerinin doğru olmadığından ve null olmadığından emin olun. |