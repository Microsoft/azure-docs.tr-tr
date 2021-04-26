---
title: Azure ön kapısının Standart/Premium SKU yapılandırmasına özel bir etki alanı ekleme
description: Bu öğreticide, Azure ön kapısının Standart/Premium SKU 'suna özel bir etki alanı eklemeyi öğreneceksiniz.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 69e216c8893f9361a18354e5d165ecc0499601aa
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387930"
---
# <a name="create-a-custom-domain-on-azure-front-door-standardpremium-sku-preview-using-the-azure-portal"></a>Azure ön kapısı Standart/Premium SKU 'da (Önizleme) Azure portal kullanarak özel bir etki alanı oluşturun

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Uygulama teslimi için Azure ön kapısının Standart/Premium kullandığınızda, kendi etki alanı adınızın Son Kullanıcı istekleriniz içinde görünmesini istiyorsanız özel bir etki alanı gereklidir. Görünür bir etki alanınızın olması, müşterileriniz için kolaylık sağlar ve markalama için faydalıdır.

Azure ön kapısının Standart/Premium profilini oluşturduktan sonra varsayılan ön uç konağın azurefd.net alt etki alanı olacaktır. Bu alt etki alanı, Azure ön kapısı Standart/Premium, varsayılan olarak arka UCUNUZDAN içerik sunarak URL 'ye dahildir. Örneğin, `https://contoso-frontend.azurefd.net/activeusers.htm`. Size kolaylık olması için Azure Front Door, varsayılan ana bilgisayar adı özel etki alanı ile ilişkilendirme seçeneği sunar. Bu seçenekle, içeriğinizi Azure ön kapısı Standart/Premium 'un sahip olduğu etki alanı adı yerine özel bir etki alanı ile birlikte suntırabilirsiniz. Örneğin, ' https://www.contoso.com/photo.png '.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [**Microsoft Azure önizlemeleri Için ek kullanım koşulları**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar
* Bu öğreticideki adımları tamamlayabilmeniz için öncelikle bir Front Door oluşturmanız gerekir. Daha fazla bilgi için bkz. [hızlı başlangıç: ön kapı Standart/Premium oluşturma](create-front-door-portal.md).

* Zaten özel bir etki alanınız yoksa, önce bir etki alanı sağlayıcısıyla bir tane satın almanız gerekir. Örneğin bkz. [Özel etki alanı adı satın alma](../../app-service/manage-custom-dns-buy-domain.md).

* [DNS etki alanlarınızı](../../dns/dns-overview.md)barındırmak için Azure kullanıyorsanız, etki alanı sağlayıcının etki alanı adı sistemi 'NI (DNS) bir Azure DNS temsilci olarak oluşturmanız gerekir. Daha fazla bilgi için bkz. [Bir etki alanını Azure DNS'ye devretme](../../dns/dns-delegate-domain-azure-dns.md). Aksi takdirde, DNS etki alanınızı işlemek için bir etki alanı sağlayıcısı kullanıyorsanız, istenen DNS TXT kayıtlarını girerek etki alanını el ile doğrulamanız gerekir.

## <a name="add-a-new-custom-domain"></a>Yeni bir özel etki alanı Ekle

> [!NOTE]
> Genel önizlemede, tepesinde etki alanları oluşturmak için Azure DNS kullanılması, Azure ön kapı Standart/Premium üzerinde desteklenmez. TEPESINDE etki alanlarının Azure ön kapısının Standart/Premium için kullanılmasına izin veren CNAME düzleştirme veya DNS ile izleme desteği sunan başka DNS sağlayıcıları vardır.

Özel bir etki alanı, portalda etki alanları tarafından yönetilir. Bir uç noktayla ilişkilendirmeden önce özel bir etki alanı oluşturulup doğrulanabilir. Özel etki alanı ve alt etki alanları aynı anda yalnızca tek bir uç nokta ile ilişkilendirilebilir. Ancak, farklı ön kapılar için aynı özel etki alanından farklı alt etki alanlarını kullanabilirsiniz. Ayrıca, özel etki alanlarını aynı ön kapı uç noktasına farklı alt etki alanlarıyla eşleyebilirsiniz.

1. Azure ön kapılarınızın ayarları ' nın altında etki *alanları*  ' nı ve ardından **etki alanı Ekle** düğmesini seçin.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-button.png" alt-text="Etki alanı giriş sayfasında etki alanı Ekle düğmesinin ekran görüntüsü.":::

1. **Etki alanı Ekle** sayfası, özel etki alanı hakkında bilgi girebileceğiniz görüntülenir. Azure tarafından yönetilen DNS ' yi seçebilirsiniz, bu önerilir veya kendi DNS sağlayıcınızı kullanmayı seçebilirsiniz. Azure tarafından yönetilen DNS ' yi seçerseniz, var olan bir DNS bölgesi seçin ve ardından özel bir alt etki alanı seçin veya yeni bir alt etki alanı oluşturun. Başka bir DNS sağlayıcısı kullanıyorsanız, özel etki alanı adını el ile girin. Özel etki alanınızı eklemek için **Ekle** ' yi seçin.

    :::image type="content" source="../media/how-to-add-custom-domain/add-domain-page.png" alt-text="Etki alanı ekleme sayfasının ekran görüntüsü.":::

    Yeni bir özel etki alanı **gönderme** doğrulama durumuyla oluşturulur.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-submitting.png" alt-text="Etki alanı doğrulama durumu gönderme ekran görüntüsü.":::

    Doğrulama durumu **bekliyor** olarak değişene kadar bekleyin. Bu işlem birkaç dakika sürebilir.

    :::image type="content" source="../media/how-to-add-custom-domain/validation-state-pending.png" alt-text="Bekleyen etki alanı doğrulama durumunun ekran görüntüsü.":::

1. **Bekleyen** doğrulama durumunu seçin. Özel etki alanını doğrulamak için gereken DNS TXT kayıt bilgileri ile yeni bir sayfa görüntülenir. TXT kaydı biçiminde olur `_dnsauth.<your_subdomain>` . Azure DNS tabanlı bölge kullanıyorsanız, **Ekle** düğmesini seçin ve Azure DNS bölgesinde, görüntülenmiş kayıt değeri ile yenı bir TXT kaydı oluşturulur. Başka bir DNS sağlayıcısı kullanıyorsanız, `dnsauth.<your_subdomain>` sayfada gösterildiği gibi kayıt değeri ile el ile yeni BIR TXT kaydı oluşturun.

    :::image type="content" source="../media/how-to-add-custom-domain/validate-custom-domain.png" alt-text="Özel etki alanı doğrulama sayfasının ekran görüntüsü.":::

1. Yenileme durumunu seçin. Etki alanı DNS TXT kaydı kullanılarak doğrulandıktan sonra doğrulama durumu **doğrulandı** olarak değişir. Bu işlemin doğrulanması birkaç dakika sürebilir.

    :::image type="content" source="../media/how-to-add-custom-domain/domain-status-verified.png" alt-text="Özel etki alanı doğrulanmış ekran görüntüsü.":::

1. Özel etki alanları listesi giriş sayfasına dönmek için sayfayı kapatın. Özel etki alanının sağlama durumu, **sağlanan** olarak ve doğrulama durumunun **Onaylandı** olarak değiştirilmesi gerekir.

    :::image type="content" source="../media/how-to-add-custom-domain/provisioned-approved-status.png" alt-text="Sağlanan ve onaylanan durumunun ekran görüntüsü.":::

## <a name="associate-the-custom-domain-with-your-front-door-endpoint"></a>Özel etki alanını ön kapılı uç noktanızla ilişkilendirin

Özel etki alanınızı doğruladıktan sonra Azure ön kapı Standart/Premium uç noktanıza ekleyebilirsiniz.

1. Özel etki alanı doğrulandıktan sonra, mevcut bir Azure ön kapısı Standart/Premium uç noktası ve rotayla ilişkilendirebilirsiniz. Uç nokta **ve rotaları ilişkilendir** sayfasını açmak Için **uç nokta ilişkilendirme** bağlantısını seçin. İlişkilendirmek istediğiniz bir uç nokta ve rotalar seçin. Ardından **ilişkilendir**' i seçin. İlişkilendirme işlemi tamamlandıktan sonra sayfayı kapatın.

    :::image type="content" source="../media/how-to-add-custom-domain/associate-endpoint-routes.png" alt-text="Uç nokta ve rotaları ilişkilendir sayfasının ekran görüntüsü.":::

    Uç nokta ilişkilendirme durumu, özel etki alanının ilişkili olduğu bitiş noktasını yansıtacak şekilde değişmelidir. 

    :::image type="content" source="../media/how-to-add-custom-domain/endpoint-association-status.png" alt-text="Uç nokta ilişkilendirme bağlantısının ekran görüntüsü.":::

1. DNS durumu bağlantısını seçin.

    :::image type="content" source="../media/how-to-add-custom-domain/dns-state-link.png" alt-text="DNS durum bağlantısının ekran görüntüsü.":::

1. **CNAME kaydı ekleme veya güncelleştirme** sayfası görünür ve trafiğin akışa başlayabilmesi için SAĞLANMASı gereken CNAME kayıt bilgilerini görüntüler. Azure DNS barındırılan bölgeleri kullanıyorsanız, CNAME kayıtları sayfadaki **Ekle** düğmesi seçilerek oluşturulabilir. Başka bir DNS sağlayıcısı kullanıyorsanız, CNAME kayıt adını ve değerini sayfada gösterildiği gibi el ile girmeniz gerekir.

    :::image type="content" source="../media/how-to-add-custom-domain/add-update-cname-record.png" alt-text="CNAME kaydı ekleme veya güncelleştirme ekran görüntüsü.":::

1. CNAME kaydı oluşturulduktan ve özel etki alanı Azure ön kapı uç noktasıyla ilişkilendirildiğinde trafik akışı akışa başlar.

    > [!NOTE]
    > HTTPS etkinse, tüm kenar konumlarında yayma yapıldığından, sertifika sağlama ve yayma işlemi birkaç dakika sürebilir. 

## <a name="verify-the-custom-domain"></a>Özel etki alanını doğrulama

Özel etki alanını doğruladıktan ve ilişkilendirdikten sonra, özel etki alanının uç noktanıza doğru şekilde başvurulduğundan emin olun.

:::image type="content" source="../media/how-to-add-custom-domain/verify-configuration.png" alt-text="Doğrulanan ve ilişkili özel etki alanının ekran görüntüsü.":::

Son olarak, uygulama içeriğinizin bir tarayıcı kullanılarak sunulduğunu doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Özel etki alanınız için HTTPS 'yi etkinleştirme hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Özel bir etki alanı için HTTPS'yi etkinleştirme]()
