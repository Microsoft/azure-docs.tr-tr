---
title: Azure ön kapısı Standart/Premium uç noktasını Endpoint Manager ile yapılandırma
description: Bu makalede Endpoint Manager ile bir uç noktanın nasıl yapılandırılacağı gösterilmektedir.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100411"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>Endpoint Manager ile bir Azure ön kapı Standart/Premium (Önizleme) uç noktası yapılandırma

> [!NOTE]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? **[Azure ön kapı belgelerini](../front-door-overview.md)** görüntüleyin.

Bu makalede, Endpoint Manager ile mevcut bir Azure ön kapısı Standart/Premium profili için bir uç nokta oluşturma gösterilmektedir.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Endpoint Manager ile bir Azure ön kapısının Standart/Premium uç noktası oluşturabilmeniz için önce en az bir Azure ön kapısı profili oluşturmuş olmanız gerekir. Profilde en az bir veya daha fazla Azure ön kapısı Standart/Premium uç noktası olmalıdır. Azure ön kapı Standart/Premium uç noktalarınızı İnternet etki alanı, Web uygulaması veya diğer ölçütlere göre düzenlemek için birden çok profil kullanabilirsiniz. 

Azure ön kapı profili oluşturmak için bkz. [yeni Azure ön kapısı Standart/Premium profili oluşturma](create-front-door-portal.md).

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>Yeni bir Azure ön kapısı Standart/Premium uç noktası oluşturma

1. [Azure Portal](https://portal.azure.com) oturum açın ve Azure ön kapısı Standart/Premium profilinize gidin.

1. **Uç nokta Yöneticisi**' ni seçin. Sonra yeni bir uç nokta oluşturmak için **uç nokta Ekle** ' yi seçin.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="Endpoint Manager aracılığıyla uç nokta ekleme ekran görüntüsü.":::

1. **Uç nokta Ekle** sayfasında, girin ve aşağıdaki ayarları seçin.
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="Uç nokta ekleme sayfasının ekran görüntüsü.":::

    | Ayarlar | Değer |
    | -------- | ----- |
    | Ad | Yeni Azure ön kapısı Standart/Premium uç noktası için benzersiz bir ad girin. Bu ad, etki alanındaki önbelleğe alınmış kaynaklarınıza erişmek için kullanılır `<endpointname>.az01.azurefd.net` |
    | Kaynak yanıt zaman aşımı (saniye) | Azure ön kapısının, Origin ile bağlantının zaman aşımına uğramadan önce bekleyeceği saniye cinsinden bir zaman aşımı değeri girin. |
    | Durum | Bu uç noktayı etkinleştirmek için onay kutusunu seçin. |

## <a name="add-domains-origin-group-routes-and-security"></a>Etki alanları, kaynak grubu, rotalar ve güvenlik ekleme

1. Rotayı yapılandırmak için uç noktada **uç noktayı Düzenle** ' yi seçin.

1. **Uç noktayı Düzenle** sayfasında, etki alanları altında **+ Ekle** ' yi seçin.
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="Uç noktayı düzenleme sayfasında etki alanı seçin sayfasında ekran görüntüsü.":::

### <a name="add-domain"></a>Etki alanı Ekle

1. **Etki alanı Ekle** sayfasında, *Azure ön kapısının* bir etki alanını ilişkilendirmeyi seçin veya *Yeni bir etki alanı ekleyin*. Yepyeni bir etki alanı oluşturma hakkında daha fazla bilgi için bkz. [yeni Azure ön kapısı Standart/Premium özel etki alanı oluşturma](how-to-add-custom-domain.md).

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="Etki alanı ekleme sayfasının ekran görüntüsü.":::

1. Etki alanını geçerli uç noktaya eklemek için **Ekle** ' yi seçin. Seçilen etki alanı, etki alanı panelinde görünmelidir.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="Etki alanı görünümündeki etki alanlarının ekran görüntüsü.":::

### <a name="add-origin-group"></a>Kaynak Grubu Ekle

1. Kaynak grupları görünümünde **Ekle** ' yi seçin. **Kaynak grubu Ekle** sayfası görüntülenir 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="Kaynak grubu ekleme sayfasının ekran görüntüsü":::

1. **Ad** için yeni kaynak grubu için benzersiz bir ad girin

1. Geçerli gruba yeni bir kaynak eklemek için **Kaynak Ekle** ' yi seçin.
 
#### <a name="health-probes"></a>Durum araştırmaları
Ön kapı, her kaynak için düzenli HTTP/HTTPS araştırma istekleri gönderir. Araştırma istekleri, Son Kullanıcı isteklerinizin yük dengelenmesi için her bir başlangıcının yakınlığını ve sistem durumunu tespit edin. Kaynak grubunun sistem durumu araştırma ayarları, uygulama başlangıcının sistem durumunu nasıl yokladığımızda tanımlar. Yük dengeleme yapılandırması için aşağıdaki ayarlar kullanılabilir:

> [!WARNING]
> Ön kapıda genel olarak çok uç ortamları olduğundan, kaynak için sistem durumu araştırma birimi, yapılandırılan durum araştırma sıklığı uyarınca dakikada her dakikada en fazla 1200 istek kadar yüksek olabilir. Varsayılan araştırma sıklığı 30 saniye ile, kaynak üzerindeki araştırma birimi dakikada yaklaşık 200 istek olmalıdır.

* **Durum**: sistem durumu araştırıp 'nin etkinleştirilip etkinleştirilmeyeceğini belirtin. Kaynak grubunuzda tek bir kaynağınız varsa, uygulamanızın arka ucunuzdaki yükü azaltan sistem durumu araştırmalarını devre dışı bırakmayı seçebilirsiniz. Grupta birden fazla çıkış olsa da, ancak bunlardan yalnızca biri etkin durumdaysa, sistem durumu araştırmalarını devre dışı bırakabilirsiniz.
   
* **Yol**: Bu kaynak grubundaki tüm kaynaklar için araştırma istekleri IÇIN kullanılan URL. Örneğin, kaynaklardan biri contoso-westus.azurewebsites.net ise ve yol/Probe/test.aspx olarak ayarlandıysa, ön kapı ortamları, protokolün HTTP olarak ayarlandığı varsayıldığında, durum araştırma isteklerini öğesine gönderir `http://contoso-westus.azurewebsites.net/probe/test.aspx` . 
   
* **Protokol**: durum araştırma Isteklerinin ön kapıdan, http veya https Protokolü ile kaynağına gönderileceğini tanımlar.
   
* **Araştırma yöntemi**: sistem durumu araştırmaları göndermek IÇIN kullanılacak http yöntemi. Seçenekler GET veya HEAD (varsayılan) içerir.

    > [!NOTE]
    > Ön kapıda daha düşük yük ve maliyet için sistem durumu araştırmalarının baş isteklerini kullanmanızı önerir.

* **Aralık (saniye)**: kaynağınıza yönelik sistem durumu araştırmalarının sıklığını veya ön kapı ortamlarının her birinin bir araştırma gönderdiği aralıkları tanımlar.
   
    >[!NOTE]
    >Daha hızlı yük devretme için, aralığı daha düşük bir değere ayarlayın. Değer ne kadar düşükse, sistem durumu araştırma hacmi, kaynak alma birimidir. Örneğin, sıklık, 100 ön kapı pop 'Ları ile 30 saniyeye ayarlandıysa, her arka uç dakikada yaklaşık 200 araştırma isteği alır.

#### <a name="load-balancing"></a>Yük dengeleme
Kaynak grubu için Yük Dengeleme ayarları, sistem durumu araştırmalarının nasıl değerlendirileceğini tanımlar. Bu ayarlar, arka ucun sağlıklı mı yoksa sağlıksız mi olduğunu belirlenir. Ayrıca, kaynak grubundaki farklı kaynaklar arasında trafiğin yük dengelemesini de denetler. Yük dengeleme yapılandırması için aşağıdaki ayarlar kullanılabilir:

- **Örnek boyutu**. Kaynak sistem durumu değerlendirmesi için göz önünde bulundurmanız gereken sistem durumu araştırmalarının kaç örnek olduğunu tanımlar.

- **Başarılı örnek boyutu**. Daha önce belirtilen örnek boyutunu, kaynak sağlıklı durumunu çağırmak için gereken başarılı örnek sayısını tanımlar. Örneğin, ön kapılı bir sistem durumu araştırma aralığının 30 saniye, örnek boyutu 5 olduğunu ve başarılı örnek boyutunun 3 olduğunu varsayalım. Kaynağınıza yönelik sistem durumu araştırmalarını her değerlendirdiğiniz her seferinde, 150 saniyelik son beş örnek (5 x 30) üzerinde bakıyoruz. Arka ucu sağlıklı olarak bildirmek için en az üç başarılı araştırma gereklidir.

- **Gecikme süresi duyarlılığı (ekstra gecikme)**. Ön kapısının isteği gecikme süresi ölçü duyarlılığı aralığı içinde kaynağa göndermesini veya isteği en yakın arka uca iletmesinin gerekip gerekmediğini tanımlar.

Kaynak grubunu geçerli uç noktaya eklemek için **Ekle** ' yi seçin. Kaynak grubu, kaynak grup paneli içinde görünmelidir

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="Kaynak grubundaki çıkış ekran görüntüsü.":::

### <a name="add-route"></a>Rota Ekle

Rotalar görünümünde **Ekle** ' yi seçtiğinizde **yol Ekle** sayfası görüntülenir. Etki alanı ve kaynak grubunu ilişkilendirme hakkında daha fazla bilgi için bkz. [yeni Azure ön kapı yolu oluşturma](how-to-configure-route.md)

### <a name="add-security"></a>Güvenlik Ekle

1. Güvenlik görünümünde **Ekle** ' yi seçin, **bir WAF İlkesi Ekle** sayfası görüntülenir
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="WAF ilkesi ekleme sayfasının ekran görüntüsü.":::

1. **WAF ilkesi**: Bu uç nokta içindeki seçili etki alanı için istediğiniz bir WAF ilkesi seçin. 
  
   Yeni **Oluştur** ' u seçerek yepyeni bir WAF ilkesi oluşturun.

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="Yeni bir WAF ilkesi oluşturma ekranının ekran görüntüsü.":::
   
    **Ad**: yenı WAF ilkesi için benzersiz bir ad girin. Bu ilkeyi, Web uygulaması güvenlik duvarı sayfasından daha fazla yapılandırmayla düzenleyebilirsiniz.

    **Etki alanları**: WAF ilkesini uygulamak için etki alanını seçin.

1. **Ekle** düğmesini seçin. WAF ilkesi güvenlik paneli içinde görünmelidir

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="Güvenlik görünümündeki WAF ilkesinin ekran görüntüsü.":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli olmadığında bir uç noktayı silmek için uç nokta satırının sonundaki **uç noktayı Sil** ' i seçin. 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="Uç noktanın nasıl silineceği ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

Özel etki alanları hakkında bilgi edinmek için [özel etki alanı eklemeye](how-to-add-custom-domain.md)devam edin.
