---
title: Azure ön kapısı Standart/Premium 'da dosya sıkıştırma sorunlarını giderme
description: Azure ön kapılarında dosya sıkıştırma ile ilgili sorunları nasıl giderebileceğinizi öğrenin. Bu makalede çeşitli olası nedenler ele alınmaktadır.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100582"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Azure ön kapısının Standart/Premium dosya sıkıştırması sorunlarını giderme

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Bu makale, Azure ön kapısının Standart/Premium dosya sıkıştırma sorunlarını gidermenize yardımcı olur.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="symptom"></a>Belirti

Yönlendirmenize yönelik sıkıştırma etkin, ancak dosyalar sıkıştırılmamış olarak döndürülüyor.

> [!TIP]
> Dosyalarınızın sıkıştırılmış döndürülüp döndürülmediğini denetlemek için [Fiddler](https://www.telerik.com/fiddler) veya tarayıcınızın [Geliştirici Araçları](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)gibi bir araç kullanmanız gerekir.  Önbelleğe alınmış CDN içeriğinizdeki geri döndürülen HTTP yanıt üstbilgilerini denetleyin.  `Content-Encoding` **Gzip**, **bzip2** veya **söndür** değeri ile adlandırılmış bir üst bilgi varsa, içeriğiniz sıkıştırılır.
> 
> ![Content-Encoding üst bilgisi](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>Nedeni

Aşağıdakiler de dahil olmak üzere birkaç olası nedeni vardır:

* İstenen içerik sıkıştırma için uygun değil.
* İstenen dosya türü için sıkıştırma etkin değil.
* HTTP isteği, geçerli bir sıkıştırma türü isteyen bir üst bilgi içermiyor.
* Kaynak, öbekli içerik gönderiyor.

## <a name="troubleshooting-steps"></a>Sorun giderme adımları

> [!TIP]
> Yeni uç noktalar dağıtımında olduğu gibi, AFD yapılandırma değişikliklerinin ağ üzerinden yayılması biraz zaman alır.  Genellikle, değişiklikler 90 dakika içinde uygulanır.  CDN uç noktanız için ilk kez sıkıştırmayı ayarladıysanız, sıkıştırma ayarlarının pop 'Lara yayıldığından emin olmak için 1-2 saat beklemeyi göz önünde bulundurmanız gerekir. 
> 

### <a name="verify-the-request"></a>İsteği doğrulama

İlk olarak, isteği iki kez denetliyoruz. Gerçekleştirilen istekleri görüntülemek için tarayıcınızın **[geliştirici araçlarını](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** kullanabilirsiniz.

* İsteğin noktanıza değil, uç nokta URL 'nize gönderildiğini doğrulayın `<endpointname>.z01.azurefd.net` .
* İsteğin bir **Accept-Encoding** üst bilgisi içerdiğini ve bu üstbilginin değeri **gzip**, **söndür** veya **bzip2** içerdiğini doğrulayın.

![CDN istek üstbilgileri](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>Sıkıştırma ayarlarını doğrulama

[Azure Portal](https://portal.azure.com) uç noktanıza gidin ve rotalar panelinde **Yapılandır** düğmesini seçin. Sıkıştırmanın **etkin** olduğunu doğrulayın.

![CDN sıkıştırma ayarları](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Kaynak sunucudaki isteği bir **Via** üstbilgisi için denetleyin

**VIA** http üstbilgisi, isteğin bir ara sunucu tarafından geçtiğini Web sunucusuna gösterir.  İstek bir **Via** üstbilgisi içerdiğinde, varsayılan olarak Microsoft IIS Web sunucuları yanıtları sıkıştırmaz.  Bu davranışı geçersiz kılmak için aşağıdakileri yapın:

* **IIS 6**: IIS metatabanı özelliklerinde Hcnocompressionforproxy = "false" ayarını yapın. Bilgi için bkz. [IIS 6 sıkıştırması](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90)).
* **IIS 7 ve yukarı**: sunucu yapılandırmasında hem **NoCompressionForHttp10** hem de **Nocompressionforproxy** değerlerini *false* olarak ayarlayın. Daha fazla bilgi için bkz. [HTTP sıkıştırması](https://www.iis.net/configreference/system.webserver/httpcompression).
