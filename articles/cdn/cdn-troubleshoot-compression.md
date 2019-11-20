---
title: Azure cdn'de dosya sıkıştırma sorunlarını giderme | Microsoft Docs
description: Azure CDN dosya sıkıştırma sorunlarını giderme.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5195dc3c47d2a4377147b2ef49b23bab6b3fee77
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593316"
---
# <a name="troubleshooting-cdn-file-compression"></a>CDN dosya sıkıştırma sorunlarını giderme
Bu makale ile ilgili sorunları gidermenize yardımcı olur. [CDN dosya sıkıştırma](cdn-improve-performance.md).

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/forums/). Alternatif olarak, bir Azure destek olayına dosya. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) tıklatıp **destek al**.

## <a name="symptom"></a>Belirti
Uç noktanız için sıkıştırmanın etkin, ancak dosyalar sıkıştırılmamış döndürülür.

> [!TIP]
> Dosyaları sıkıştırılmış döndürülür olup olmadığını denetlemek için gibi bir araç kullanmanız gerekir [Fiddler](https://www.telerik.com/fiddler) veya tarayıcınızın [Geliştirici Araçları](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Onay HTTP yanıt üst bilgileri, önbelleğe alınan CDN ile içerik döndürdü.  Adlı bir üstbilgi olup olmadığını `Content-Encoding` değeriyle **gzip**, **bzıp2**, veya **deflate**, içerik sıkıştırılır.
> 
> ![Üst bilgi içeriği kodlama](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Nedeni
Birkaç olası nedeni vardır:

* İstenen içerik sıkıştırma için uygun değil.
* Sıkıştırma istenen dosya türü için etkin değil.
* HTTP isteği, geçerli sıkıştırma türü isteyen bir üst bilgisi içermiyordu.

## <a name="troubleshooting-steps"></a>Sorun giderme adımları
> [!TIP]
> Yeni uç nokta dağıtırken olduğu gibi ile ağ üzerinden yayılması biraz zaman CDN yapılandırma değişiklikleri yararlanın.  Genellikle, değişiklikler 90 dakika içinde uygulanır.  Bu, CDN uç noktanız için sıkıştırmayı ayarlama ayarlamış olduğunuz ilk kez kullanıyorsanız, 1-2 saat ayarları Pop'lere yayıldıktan sıkıştırma emin olmak için bekleyen düşünmelisiniz. 
> 
> 

### <a name="verify-the-request"></a>İsteği doğrulama
İlk olarak bir hızlı sağlamlık onay isteğini yapmamız gerekir.  Tarayıcınızın kullanabileceğiniz [Geliştirici Araçları](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) yapılan istekleri görüntülemek için.

* İstek gönderiliyor uç nokta URL'nizi doğrulayın `<endpointname>.azureedge.net`ve kaynağınıza değil.
* İstek içerdiğini doğrulayın bir **Accept-Encoding** üst bilgi ve bu üstbilgisi değeri içeren **gzip**, **deflate**, veya **bzıp2**.

> [!NOTE]
> **Akamai'den Azure CDN** yalnızca destek profilleri **gzip** kodlama.
> 
> 

![CDN istek üstbilgileri](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Sıkıştırma ayarları (standart CDN profilleri) doğrulayın
> [!NOTE]
> Bu adım yalnızca, CDN profiliniz ise geçerlidir bir **Azure CDN standart Microsoft gelen**, **verizon'dan Azure CDN standart**, veya **akamai'den Azure CDN standart** profili. 
> 
> 

Uç noktanıza içinde gezinmek [Azure portalında](https://portal.azure.com) tıklatıp **yapılandırma** düğmesi.

* Sıkıştırmanın etkin doğrulayın.
* Sıkıştırılacak içerik MIME türü sıkıştırma biçimlerinin listesinde bulunan doğrulayın.

![CDN sıkıştırma ayarları](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Sıkıştırma ayarları (Premium CDN profilleri) doğrulayın
> [!NOTE]
> Bu adım yalnızca, CDN profiliniz ise geçerlidir bir **verizon'dan Azure CDN Premium** profili.
> 
> 

Uç noktanız gidin [Azure portalında](https://portal.azure.com) tıklatıp **Yönet** düğmesi.  Ek portalı açılır.  Üzerine **HTTP büyük** sekmesine ve ardından üzerine **önbellek ayarları** açılır öğesi.  Tıklayın **sıkıştırma**. 

* Sıkıştırmanın etkin doğrulayın.
* Doğrulama **dosya türleri** listesini içeren bir virgülle ayrılmış listesi (boşluksuz) MIME türleri.
* Sıkıştırılacak içerik MIME türü sıkıştırma biçimlerinin listesinde bulunan doğrulayın.

![CDN premium sıkıştırma ayarları](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Önbelleğe alınan (Verizon CDN profilleri) içerik olduğunu doğrulayın
> [!NOTE]
> Bu adım yalnızca, CDN profiliniz ise geçerlidir bir **verizon'dan Azure CDN standart** veya **verizon'dan Azure CDN Premium** profili.
> 
> 

Tarayıcınızın Geliştirici Araçları'nı kullanarak yanıt üstbilgilerini dosyası burada istenmektedir bölgede önbelleğe emin olmak için kontrol edin.

* Denetleme **sunucu** yanıtı üstbilgisi.  Üst bilgi biçimi olmalıdır **Platformu (POP/sunucu kimliği)** , aşağıdaki örnekte görüldüğü gibi.
* Denetleme **X-Cache** yanıtı üstbilgisi.  Üst bilgiyi okumalısınız **İSABET**.  

![CDN yanıt üstbilgileri](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Dosya boyutu gereksinimleri (Verizon CDN profilleri) karşıladığını doğrulayın
> [!NOTE]
> Bu adım yalnızca, CDN profiliniz ise geçerlidir bir **verizon'dan Azure CDN standart** veya **verizon'dan Azure CDN Premium** profili.
> 
> 

Sıkıştırma için uygun olması için bir dosya aşağıdaki boyut gereksinimlerini karşılaması gerekir:

* 128 bayt daha büyük.
* 1 MB'tan küçük.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>İstek için kaynak sunucuda denetleyin bir **aracılığıyla** üstbilgisi
**Aracılığıyla** HTTP üst bilgisi, istek bir proxy sunucusu tarafından geçirilen web sunucusuna gösterir.  Varsayılan olarak Microsoft IIS web sunucuları sıkıştırma yanıtları istek içerdiğinde bir **aracılığıyla** başlığı.  Bu davranışı geçersiz kılmak için aşağıdakileri gerçekleştirin:

* **IIS 6**: [HcNoCompressionForProxies ayarlama IIS Metabase özelliklerinde = "FALSE"](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 ve üstü**: [Hem **noCompressionForHttp10** ve **noCompressionForProxies** false sunucu yapılandırması](https://www.iis.net/configreference/system.webserver/httpcompression)

