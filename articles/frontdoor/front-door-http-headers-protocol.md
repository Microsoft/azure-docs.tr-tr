---
title: Azure ön kapılarında HTTP üstbilgileri için protokol desteği | Microsoft Docs
description: Bu makalede, ön kapıların desteklediği HTTP üst bilgi protokolleri açıklanır.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2020
ms.author: duau
ms.openlocfilehash: 2ad97656b822bc5ffc957469842436ec84d9e812
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785766"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Azure ön kapılarında HTTP üstbilgileri için protokol desteği
Bu makalede, ön kapıların çağrı yolunun bölümleriyle desteklediği protokol (bkz. görüntü) özetlenmektedir. Aşağıdaki bölümlerde, ön kapıda desteklenen HTTP üstbilgileri hakkında daha fazla bilgi sağlanmaktadır.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure ön kapılı HTTP üstbilgileri Protokolü":::

>[!IMPORTANT]
>Ön kapı burada belgelenmemiş HTTP üstbilgilerini onaylayamaz.

## <a name="client-to-front-door"></a>İstemciden ön kapıya
Ön kapı, gelen istek için tüm üst bilgileri değiştirmeden kabul eder. X-FD-* ön ekine sahip üstbilgiler dahil, gönderildiyse, bazı ayrılmış üst bilgiler gelen istekten kaldırılır.

## <a name="front-door-to-backend"></a>Arka uca ön kapı

Ön kapı, kısıtlamalar nedeniyle kaldırılmadığı takdirde gelen bir istek için üst bilgiler içerir. Ön kapı aşağıdaki üst bilgileri de ekler:

| Üst bilgi  | Örnek ve açıklama |
| ------------- | ------------- |
| Yazıcısıyla |  *Aracılığıyla: 1,1 Azure* </br> Ön kapı, istemci HTTP sürümünü ve ardından *Azure* 'un arkasından, Via üstbilgisi için değer olarak ekler. Bu üst bilgi, istemcinin HTTP sürümünü ve bu ön kapıın istemci ile arka uç arasındaki istek için bir ara alıcı olduğunu gösterir.  |
| X-Azure-Clienentip | *X-Azure-Clienentip: 127.0.0.1* </br> İşlenmekte olan istekle ilişkili istemci IP adresini temsil eder. Örneğin, bir proxy 'den gelen bir istek, özgün çağıranın IP adresini belirtmek için X-Iletilmiş-for üst bilgisini ekleyebilir. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Geçerli isteğin kaynaklandığı TCP bağlantısıyla ilişkili yuva IP adresini temsil eder. İstemci IP 'si bir kullanıcı tarafından rastgele üzerine yazılabileceğinden, bir isteğin istemci IP adresi, yuva IP adresine eşit olmayabilir.|
| X-Azure-ref | *X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Ön kapıya göre sunulan isteği tanımlayan benzersiz bir başvuru dizesi. Bu, erişim günlüklerini aramak için kullanılır ve sorun giderme için kritik öneme sahiptir.|
| X-Azure-Requestzincirine | *X-Azure-Requestzincirine: atlama = 1* </br> Ön kapı, istek döngülerini algılamak için kullanılan üst bilgi ve kullanıcıların buna bir bağımlılığı olmaz. |
| X-Azure-FDıD | *X-Azure-FDıD: 55ce4ed1-4B06-4BF1-B40E-4638452104da* <br/> Belirli bir ön kapı kaynağından gelen isteği tanımlayan bir başvuru dizesi. Değer Azure portal görünebilir veya yönetim API 'SI kullanılarak alınabilir. Uç noktanızı yalnızca belirli bir ön kapı kaynağından gelen istekleri kabul etmek üzere kilitlemek için bu üstbilgiyi IP ACL 'lerle birlikte kullanabilirsiniz. [Daha ayrıntılı bilgi](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) için SSS bölümüne bakın |
| X-Iletilmiş-Için | *X-Iletildi-for: 127.0.0.1* </br> X-Iletilmiş-for (XFF) HTTP üstbilgisi alanı, genellikle bir HTTP proxy veya yük dengeleyici aracılığıyla bir Web sunucusuna bağlanan bir istemcinin kaynak IP adresini belirler. Var olan bir XFF üst bilgisi varsa, ön kapı istemci yuvasını IP 'ye ekler veya XFF üstbilgisini istemci yuvası IP 'si ile ekler. |
| X-Iletilen-konak | *X-Iletilen-ana bilgisayar: contoso.azurefd.net* </br> X-Iletilen ana bilgisayar HTTP üst bilgisi alanı, istemci tarafından istenen özgün Konağı tanımlamak için kullanılan ortak bir yöntemdir, ana bilgisayar HTTP istek üst bilgisi. Bunun nedeni, ön kapıdan ana bilgisayar adının isteği işleyen arka uç sunucusu için farklı olabilir. |
| X-Iletilen-proto | *X-Iletilen-proto: http* </br> X-Iletilmiş-proto HTTP üst bilgisi alanı, genellikle bir HTTP isteğinin kaynak protokolünü belirlemek için kullanılır. Yapılandırmaya dayalı ön kapı, HTTPS kullanarak arka uca iletişim kurabilir. Bu, ters proxy 'ye yönelik istek HTTP olsa bile geçerlidir. |
| X-FD-Healtharaştırması | X-FD-Healtharaştırması HTTP üstbilgisi alanı, ön kapıdan sistem durumu araştırmasını belirlemek için kullanılır. Bu üstbilgi 1 olarak ayarlandıysa, istek sistem durumu araştırmasına sahip olur. X ile Iletilen ana bilgisayar üst bilgisi alanı ile belirli ön kapıdan katı erişim sağlamak istediğinizde ' i kullanabilirsiniz. |
| X-Azure-FDıD | *X-Azure-FDıD üst bilgisi: 437c82cd-360A-4a54-94c3-5ff707647783* </br> Bu alan, gelen isteğin hangi ön kapıdan olduğunu belirlemek için kullanılabilecek frontdoorID içerir. Bu alan, ön kapı hizmeti tarafından doldurulur. | 

## <a name="front-door-to-client"></a>İstemciye ön kapı

Arka uçtan ön kapıya gönderilen tüm üstbilgiler da istemciye geçirilir. Ön kapıdan istemcilere gönderilen üstbilgiler aşağıda verilmiştir.

| Üst bilgi  | Örnek ve açıklama |
| ------------- | ------------- |
| X-Azure-ref |  *X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Bu, ön kapıya göre sunulan bir isteği tanımlayan, erişim günlüklerini aramak için kullanıldığı için önemli olan, sorun giderme açısından kritik olan bir benzersiz başvuru dizesidir.|
| X önbelleği | *X-önbellek: TCP_HIT* </br> Bu üst bilgi, isteğin önbellek durumunu açıklar ve bu, yanıt içeriğinin ön kapı önbelleğinden sunulduğunu tanımlamanızı sağlar. |

Aşağıdaki isteğe bağlı yanıt üstbilgilerini etkinleştirmek için "X-Azure-DebugInfo: 1" istek üst bilgisini göndermeniz gerekir.

| Üst bilgi  | Örnek ve açıklama |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Bu üst bilgi, arka uç tarafından döndürülen HTTP durum kodunu içerir. Bu üstbilgiyi kullanarak, arka uç günlüklerine geçmeden arka uçta çalışan uygulama tarafından döndürülen HTTP durum kodunu belirleyebilirsiniz. Bu durum kodu, istemciye ön kapıya gönderilen yanıttaki HTTP durum kodundan farklı olabilir. Bu üst bilgi, arka ucun yanlış davranıp kalmadığını veya sorunun ön kapı hizmeti ile mi olduğunu belirlemenizi sağlar. |
| X-Azure-InternalError | Bu üst bilgi, isteği işlerken ön kapıın üzerinde geldiği hata kodunu içerir. Bu hata, sorunun ön kapı hizmeti/altyapısına iç olduğunu gösterir. Desteğe sorun bildirin.  |
| X-Azure-ExternalError | *X-Azure-ExternalError: 0x830c1011, sertifika yetkilisi tanıdık değil.* </br> Bu üst bilgi, bir isteği işlemek için arka uç sunucusuna bağlantı kurarken ön kapı sunucularının içinde geldiği hata kodunu gösterir. Bu üst bilgi, ön kapılı ve arka uç uygulaması arasındaki bağlantıdaki sorunları belirlemenize yardımcı olur. Bu üst bilgi, arka ucunuza yönelik bağlantı sorunlarını (örneğin, DNS çözümlemesi, geçersiz sertifika vb.) belirlemenize yardımcı olacak ayrıntılı bir hata iletisi içerir. |

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturma](quickstart-create-front-door.md)
- [Ön kapı nasıl kullanılır?](front-door-routing-architecture.md)
