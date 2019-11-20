---
title: 'Öğretici: Azure depolama Azure CDN kullanarak statik bir Web sitesinde SSL ile özel etki alanını etkinleştirme'
description: Statik Web sitesi barındırmak için özel bir etki alanı yapılandırma konusunda bilgi edinin.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: a65c0e677182eb224f6bfa7ed834740458b97098
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847013"
---
# <a name="tutorial-use-azure-cdn-to-enable-a-custom-domain-with-ssl-for-a-static-website"></a>Öğretici: Statik bir Web sitesi için SSL ile özel etki alanını etkinleştirmek üzere Azure CDN kullanma

Bu öğretici, bir dizinin ikinci bölümüdür. İçinde statik bir Web siteniz için SSL ile bir özel etki alanı endpoint etkinleştirmeyi öğrenin. 

Öğreticinin nasıl kullanılacağını gösterir [Azure CDN](../../cdn/cdn-overview.md) özel etki alanı için uç nokta statik Web sitenizi yapılandırmak için. Azure CDN ile özel SSL sertifikalarını sağlama, özel bir etki alanı kullanın ve tümünü aynı anda özel yeniden yazma kuralları yapılandırın. Azure CDN yapılandırma ek ücretlere neden olur, ancak tutarlı düşük gecikme süreleriyle Web sitenize yerden sağlar dünyanın. Azure CDN, ayrıca kendi sertifikanızı ile SSL şifrelemesi sağlar. Azure CDN fiyatlandırması hakkında daha fazla bilgi için bkz: [Azure CDN fiyatlandırması](https://azure.microsoft.com/pricing/details/cdn/).

Serinin ikinci bölümünde şunları öğrenirsiniz:

> [!div class="checklist"]
> * Statik Web sitesi uç noktada bir CDN uç noktası oluşturma
> * Özel etki alanı ve SSL etkinleştir

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce birinci kısmı izleyin, [öğretici: Blob depolamada](storage-blob-static-website-host.md)statik bir Web sitesi barındırın. 

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Oturum [Azure portalında](https://portal.azure.com/) kullanmaya başlamak için.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Statik Web sitesi uç noktada bir CDN uç noktası oluşturma

1. Azure portal depolama hesabınızı bulun ve hesaba genel bakış ' ı görüntüleyin.
1. Seçin **Azure CDN** altında **Blob hizmeti** Azure CDN yapılandırmak için menüsünde.
1. **CDN profili** bölümünde yeni veya var olan bir CDN profili belirtin. Daha fazla bilgi için bkz [. hızlı başlangıç: Azure CDN profili ve uç noktası](../../cdn/cdn-create-new-endpoint.md)oluşturun.
1. CDN uç noktası için bir fiyatlandırma katmanı belirtin. Bu öğretici, genellikle birkaç dakika içinde hızla yaydığı için **Standart Akamai** fiyatlandırma katmanını kullanır. Diğer fiyatlandırma katmanlarının yayılması daha uzun sürebilir, ancak başka avantajlar da sunabilir. Daha fazla bilgi için bkz. [Azure CDN ürün özelliklerini karşılaştırma](../../cdn/cdn-features.md).
1. **CDN uç noktası adı** alanında, CDN uç noktanız için bir ad belirtin. CDN uç noktası Azure genelinde benzersiz olmalıdır.
1. **Kaynak ana bilgisayar adı** alanında, sizin için statik Web sitesi uç noktası olduğunuzu belirtin. Statik Web sitesi uç noktanızı bulmak için, depolama hesabınız için **statik Web sitesi** ayarları ' na gidin. Birincil uç noktayı kopyalayın ve CDN yapılandırmasına yapıştırarak protokol tanımlayıcısını (*örn.* https) kaldırır.

    Aşağıdaki görüntüde örnek bir uç nokta yapılandırması gösterilmektedir:

    ![Örnek CDN uç noktası yapılandırmasını gösteren ekran görüntüsü](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. CDN uç noktasını oluşturun ve yaymasını bekleyin.
1. CDN uç noktasının doğru şekilde yapılandırıldığını doğrulamak için, ayarlarına gitmek üzere uç noktaya tıklayın. Depolama hesabınız için CDN genel görünümünde, uç nokta ana bilgisayar adını bulun ve aşağıdaki görüntüde gösterildiği gibi uç noktaya gidin. CDN uç noktanızın biçimi şuna benzer `https://staticwebsitesamples.azureedge.net`olacaktır.

    ![CDN uç noktasına genel bakış gösteren ekran görüntüsü](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    CDN uç noktası yayma işlemi tamamlandıktan sonra, CDN uç noktasına gitmek daha önce statik Web sitenize yüklediğiniz index. html dosyasının içeriğini görüntüler.

1. CDN uç noktanıza ait kaynak ayarlarını gözden geçirmek için, CDN uç noktanıza ilişkin **Ayarlar** bölümünde **kaynak** ' a gidin. **Kaynak türü** alanının *özel kaynak* olarak ayarlandığını ve **kaynak konak adı** alanının statik Web sitesi uç noktasını görüntülediğini görürsünüz.

    ![CDN uç noktası için kaynak ayarlarını gösteren ekran görüntüsü](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Özel etki alanı ve SSL etkinleştir

1. Özel etki alanınızı CDN uç noktasına yönlendirmek için etki alanı adı sağlayıcınızla bir CNAME kaydı oluşturun. CNAME kaydı *www* alt etki alanı aşağıdakine benzer olmalıdır:

    ![Www alt etki alanı için CNAME kaydı belirtin](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Azure portal, CDN uç noktanız için ayarları görüntüleyin. Özel etki alanı ve SSL sertifikasını yapılandırmak için **Ayarlar** altında **özel etki alanları** ' na gidin.
1. Seçin **özel etki alanı Ekle** ve etki alanı adınızı girin ve ardından tıklayın **Ekle**.
1. Bir SSL sertifikası sağlamak için yeni özel etki alanı eşlemesini seçin.
1. **Özel etki alanı https** 'yi **Açık**olarak ayarlayın ve **Kaydet**' e tıklayın. Özel etki alanınızı yapılandırmak birkaç saat sürebilir. Portal, aşağıdaki görüntüde gösterildiği gibi ilerlemeyi gösterir.

    ![Özel etki alanı yapılandırmasının ilerlemesini gösteren ekran görüntüsü](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Özel etki alanınız için URL 'ye erişerek, statik Web sitenizin özel etki alanınızı eşlemesini test edin.

Özel etki alanları için https 'yi etkinleştirme hakkında daha fazla bilgi [için bkz. Öğretici: Azure CDN özel bir etki alanında](../../cdn/cdn-custom-ssl.md)https 'yi yapılandırın.

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu iki, statik Web siteniz için SSL Azure CDN ile özel bir etki alanı yapılandırma hakkında bilgi edindiniz.

Azure CDN yapılandırma ve kullanma hakkında daha fazla bilgi için bkz. [Azure CDN nedir?](../../cdn/cdn-overview.md).