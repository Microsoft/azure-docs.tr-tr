---
title: Azure CDN dosya sıkıştırması sorunlarını giderme | Microsoft Docs
description: Azure Content Delivery Network 'de dosya sıkıştırma ile ilgili sorunları nasıl giderebileceğinizi öğrenin. Bu makalede çeşitli olası nedenler ele alınmaktadır.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f49af1488a0c044639a72fc2ea52ba0a47727a24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996160"
---
# <a name="troubleshooting-cdn-file-compression"></a>CDN dosya sıkıştırma sorunlarını giderme
Bu makale, [CDN dosya sıkıştırması](cdn-improve-performance.md)ile ilgili sorunları gidermenize yardımcı olur.

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**'ı tıklatın.

## <a name="symptom"></a>Belirti
Uç noktanız için sıkıştırma etkin, ancak dosyalar sıkıştırılmamış olarak döndürülüyor.

> [!TIP]
> Dosyalarınızın sıkıştırılmış döndürülüp döndürülmediğini denetlemek için [Fiddler](https://www.telerik.com/fiddler) veya tarayıcınızın [Geliştirici Araçları](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)gibi bir araç kullanmanız gerekir.  Önbelleğe alınmış CDN içeriğinizdeki geri döndürülen HTTP yanıt üstbilgilerini denetleyin.  `Content-Encoding` **Gzip**, **bzip2** veya **söndür** değeri ile adlandırılmış bir üst bilgi varsa, içeriğiniz sıkıştırılır.
> 
> ![Content-Encoding üst bilgisi](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Nedeni
Aşağıdakiler de dahil olmak üzere birkaç olası nedeni vardır:

* İstenen içerik sıkıştırma için uygun değil.
* İstenen dosya türü için sıkıştırma etkin değil.
* HTTP isteği, geçerli bir sıkıştırma türü isteyen bir üstbilgi içermiyordu.
* Kaynak, öbekli içerik gönderiyor.

## <a name="troubleshooting-steps"></a>Sorun giderme adımları
> [!TIP]
> Yeni uç noktalar dağıtımında olduğu gibi, CDN yapılandırma değişikliklerinin ağ üzerinden yayılması biraz zaman alır.  Genellikle, değişiklikler 90 dakika içinde uygulanır.  CDN uç noktanız için ilk kez sıkıştırmayı ayarladıysanız, sıkıştırma ayarlarının pop 'Lara yayıldığından emin olmak için 1-2 saat beklemeyi göz önünde bulundurmanız gerekir. 
> 
> 

### <a name="verify-the-request"></a>İsteği doğrulama
İlk olarak, istek üzerinde hızlı bir göz atın.  Gerçekleştirilen istekleri görüntülemek için tarayıcınızın [geliştirici araçlarını](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) kullanabilirsiniz.

* İsteğin noktanıza değil, uç nokta URL 'nize gönderildiğini doğrulayın `<endpointname>.azureedge.net` .
* İsteğin bir **Accept-Encoding** üst bilgisi içerdiğini ve bu üstbilginin değeri **gzip**, **söndür** veya **bzip2** içerdiğini doğrulayın.

> [!NOTE]
> **Akamai profillerindeki Azure CDN** yalnızca **gzip** kodlamasını destekler.
> 
> 

![CDN istek üstbilgileri](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Sıkıştırma ayarlarını doğrulama (Standart CDN profilleri)
> [!NOTE]
> Bu adım, yalnızca CDN profiliniz **Microsoft 'tan Azure CDN Standart**, **Verizon 'ten standart Azure CDN** veya **Akamai profilinden Azure CDN Standart** olduğunda geçerlidir. 
> 
> 

[Azure Portal](https://portal.azure.com) uç noktanıza gidin ve **Yapılandır** düğmesine tıklayın.

* Sıkıştırmanın etkin olduğunu doğrulayın.
* Sıkıştırılan içeriğin MIME türünün sıkıştırılmış biçimler listesine dahil edildiğini doğrulayın.

![CDN sıkıştırma ayarları](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Sıkıştırma ayarlarını doğrulama (Premium CDN profilleri)
> [!NOTE]
> Bu adım yalnızca CDN profiliniz Verizon profilinden bir **Azure CDN Premium** olduğunda geçerlidir.
> 
> 

[Azure Portal](https://portal.azure.com) uç noktanıza gidin ve **Yönet** düğmesine tıklayın.  Ek portal açılır.  **Http büyük** sekmesinin üzerine gelin ve ardından **önbellek ayarları** açılır seçeneğinin üzerine gelin.  **Sıkıştırma**' ya tıklayın. 

* Sıkıştırmanın etkin olduğunu doğrulayın.
* **Dosya türleri** LISTESININ, MIME türleri için virgülle ayrılmış bir liste (boşluk yok) içerdiğini doğrulayın.
* Sıkıştırılan içeriğin MIME türünün sıkıştırılmış biçimler listesine dahil edildiğini doğrulayın.

![CDN Premium sıkıştırma ayarları](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>İçeriğin önbelleğe alındığını doğrulama (Verizon CDN profilleri)
> [!NOTE]
> Bu adım yalnızca CDN profiliniz Verizon **Azure CDN veya Verizon profilinden Premium Premium** **'Dan bir Azure CDN Standart** ise geçerlidir.
> 
> 

Tarayıcınızın geliştirici araçlarını kullanarak, dosyanın istenen bölgede önbelleğe alındığından emin olmak için yanıt üst bilgilerini denetleyin.

* **Sunucu** yanıt üst bilgisini denetleyin.  Üst bilgi, aşağıdaki örnekte görüldüğü gibi, biçim **platformuna (pop/sunucu kimliği)** sahip olmalıdır.
* **X-Cache** yanıt üst bilgisini denetleyin.  Üst bilgi **isabet** okuması gerekir.  

![CDN yanıt üstbilgileri](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Dosyanın boyut gereksinimlerini karşıladığından emin olun (Verizon CDN profilleri)
> [!NOTE]
> Bu adım yalnızca CDN profiliniz Verizon **Azure CDN veya Verizon profilinden Premium Premium** **'Dan bir Azure CDN Standart** ise geçerlidir.
> 
> 

Sıkıştırma için uygun olması için bir dosyanın aşağıdaki boyut gereksinimlerini karşılaması gerekir:

* 128 bayttan büyük (Content-Length: 128)
* 3 MB 'tan küçük

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Kaynak sunucudaki isteği bir **Via** üstbilgisi için denetleyin
**VIA** http üstbilgisi, isteğin bir ara sunucu tarafından geçtiğini Web sunucusuna gösterir.  Microsoft IIS Web sunucuları varsayılan olarak istek bir **Via** üstbilgisi içerdiğinde yanıtları sıkıştırmaz.  Bu davranışı geçersiz kılmak için aşağıdakileri yapın:

* **IIS 6**: [IIS metatabanı özelliklerinde Hcnocompressionforproxy = "false" ayarını](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90)) yapın
* **IIS 7 ve yukarı**: [sunucu yapılandırmasında hem **NoCompressionForHttp10** hem de **nocompressionforproxy** değerlerini false olarak ayarlayın](https://www.iis.net/configreference/system.webserver/httpcompression)

