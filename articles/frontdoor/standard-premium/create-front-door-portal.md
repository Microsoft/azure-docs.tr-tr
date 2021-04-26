---
title: 'Hızlı başlangıç: Azure ön kapısı Standart/Premium profili oluşturma-Azure portal'
description: Bu hızlı başlangıçta, Azure portal kullanarak yüksek oranda kullanılabilir ve yüksek performanslı Global Web uygulamanız için Azure ön kapısının Standart/Premium hizmetinin nasıl kullanılacağı gösterilmektedir.
services: frontdoor
author: duongau
manager: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: duau
ms.openlocfilehash: 8c6fe355621b4f096814268dea8fd2fc8e1166a7
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552878"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>Hızlı başlangıç: Azure ön kapısı Standart/Premium profili oluşturma-Azure portal

> [!Note]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Azure ön kapı belgelerini](../front-door-overview.md)görüntüleyin.

Bu hızlı başlangıçta, Azure portal kullanarak bir Azure ön kapısı Standart/Premium profili oluşturmayı öğreneceksiniz. Temel yapılandırmalarda *hızlı oluşturma* aracılığıyla veya daha gelişmiş yapılandırmalara sahip *özel oluştur* aracılığıyla Azure ön kapısının Standart/Premium profilini oluşturabilirsiniz. *Özel oluşturma* ile iki Web Apps dağıtın. Ardından, kaynak olarak iki Web Apps kullanarak Azure ön kapı Standart/Premium profilini oluşturursunuz. Daha sonra Azure ön kapısı Standart/Premium ön uç ana bilgisayar adını kullanarak Web Apps bağlantısını doğrulayacaksınız.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-front-door-profile---quick-create"></a>Ön kapı profili oluşturma-hızlı oluşturma

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Giriş sayfasından veya Azure menüsünde **+ kaynak oluştur**' u seçin.  *Ön kapı Standart/Premium (Önizleme)* Için arama yapın. Ardından **Oluştur**’u seçin.

1. **Teklifleri Karşılaştır** sayfasında **hızlı oluştur**' u seçin. Ardından **bir ön kapı oluşturmak Için devam**' ı seçin.

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="Karşılaştırma tekliflerinin ekran görüntüsü.":::

1. **Ön kapı profili oluştur** sayfasında, aşağıdaki ayarları girin veya seçin.

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Ön kapı hızlı oluşturma sayfasının ekran görüntüsü.":::    

    | Ayarlar | Değer |
    | --- | --- |
    | **Abonelik**  | Aboneliğinizi seçin. |
    | **Kaynak grubu**  | **Yeni oluştur** ' u seçin ve metin kutusuna *contoso-appservice* yazın.|
    | **Ad** | Profilinize bir ad verin. Bu örnek **contoso-AFD-quickcreate** kullanır. |
    | **Katman** | Standart veya Premium SKU seçin. Standart SKU, içerik teslimi iyileştirilir. Premium SKU, standart SKU üzerinde oluşturulur ve güvenlik altına odaklanılmıştır. Bkz. [Katman karşılaştırması](tier-comparison.md).  |
    | **Uç nokta adı** | Uç noktanız için genel olarak benzersiz bir ad girin. |
    | **Kaynak türü** | Kaynağı için kaynak türünü seçin. Bu örnekte, özel bağlantı etkin olan kaynak olarak bir App Service seçeceğiz. |
    | **Kaynak ana bilgisayar adı** | Başlangıcının ana bilgisayar adını girin. |
    | **Özel bağlantıyı etkinleştir** | Azure ön kapılarınız ve Origin 'niz arasında özel bir bağlantıya sahip olmak istiyorsanız. Daha fazla ayrıntı için lütfen [özel bağlantı kılavuzuna](concept-private-link.md) başvurun ve [özel bağlantıyı etkinleştirin](./how-to-enable-private-link-web-app.md).
    | **Önbelleğe Alma** | Azure ön kapısının Edge pop 'Ları ve Microsoft ağını kullanarak kullanıcılara içerikleri daha yakın bir şekilde önbelleğe almak istiyorsanız onay kutusunu işaretleyin. |
    | **WAF ilkesi** | Bu özelliği etkinleştirmek istiyorsanız, **Yeni oluştur** ' u seçin veya açılan listeden var olan bir WAF ilkesini seçin. |

    > [!NOTE]
    > Bir Azure ön kapısının Standart/Premium profilini oluştururken, ön kapıda oluşturulduğu abonelikteki bir kaynak seçmeniz gerekir.

1. Ön kapı profilinizi çalışır duruma getirmek için **gözden geçir + oluştur** ' u seçin.

   > [!NOTE]
   > Yapılandırmaların tüm kenar pop 'Lara yayılması birkaç dakika sürebilir.

1. Ardından, ön kapı profilinizi dağıtıp çalıştırmak için **Oluştur** ' a tıklayın.

1. Özel bağlantıyı etkinleştirdiyseniz, kaynağına gidin (Bu örnekteki App Service). **Ağ**  >  **yapılandırması özel bağlantısını** seçin. Ardından Azure ön kapıdan bekleyen isteği seçin ve Onayla ' ya tıklayın. Birkaç saniye sonra, uygulamanıza Azure ön kapısı aracılığıyla güvenli bir şekilde erişilebilecektir.

## <a name="create-front-door-profile---custom-create"></a>Ön kapı profili oluşturma-özel oluştur

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>Kaynak olarak iki örnek içeren bir Web uygulaması oluşturma

Zaten yapılandırılmış bir Origin veya Origin grubunuz varsa, uygulamanız için bir ön kapı standardı/Premium (Önizleme) oluşturmak üzere atlayın.

Bu örnekte, farklı Azure bölgelerinde çalışan iki örneğe sahip bir Web uygulaması oluşturacağız. Her iki Web uygulaması örneği de *etkin/etkin* modda çalışır, bu nedenle bir tane trafik alabilir. Bu yapılandırma, biri yük devretme görevi gören bir *etkin/bağımsız* yapılandırmadan farklıdır.

Henüz bir Web uygulamanız yoksa, örnek bir Web uygulaması ayarlamak için aşağıdaki adımları kullanın.

1. https://portal.azure.com adresinden Azure portalında oturum açın.

1. Ekranın sol üst kısmında **kaynak oluştur**  >  **WebApp** öğesini seçin.

1. **Web uygulaması oluşturma** sayfasının **temel bilgiler** sekmesinde, aşağıdaki bilgileri girin veya seçin.

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | **Abonelik**               | Aboneliğinizi seçin. |
    | **Kaynak grubu**       | **Yeni oluştur** ' u seçin ve metin kutusuna *FrontDoorQS_rg1* girin.|
    | **Ad**                   | Web uygulamanız için benzersiz bir **ad** girin. Bu örnek, *Webappcontoso-001* kullanır. |
    | **Yayımla** | **Kod**’u seçin. |
    | **Çalışma zamanı yığını**         | **.NET Core 2,1 (LTS)** seçeneğini belirleyin. |
    | **İşletim Sistemi**          | **Windows**' u seçin. |
    | **Bölge**           | **Orta ABD** seçin. |
    | **Windows Planı** | **Yeni oluştur** ' u seçin ve metin kutusuna *Myappserviceplanmerkezileştirmi* yazın. |
    | **Sku ve boyut** | **Standart S1 100 total ACU, 1,75-GB bellek ' i** seçin. |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Azure portal hızlı bir şekilde ön kapı Premium SKU 'SU oluşturun":::

1. **Gözden geçir + oluştur**' u seçin, Özeti gözden geçirin ve ardından **Oluştur**' u seçin. Bir öğesine dağıtılması birkaç dakika sürebilir

Dağıtımınız tamamlandıktan sonra ikinci bir Web uygulaması oluşturun. Aşağıdaki ayarlar dışında yukarıdaki ayarlarla aynı ayarları kullanın:

| Ayar          | Değer     |
| ---              | ---  |
| **Kaynak grubu**   | **Yeni oluştur** ' u seçin ve *FrontDoorQS_rg2* girin. |
| **Ad**             | Web uygulamanız için benzersiz bir ad girin, bu örnekte *Webappcontoso-002*.  |
| **Bölge**           | Farklı bir bölge, bu örnekte *Orta Güney ABD* |
| **App Service planı**  >  **Windows planı**         | **Yeni** ' yi seçin ve *Myappserviceplangüneydoğu US*' i girip **Tamam**' ı seçin. |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>Uygulamanız için bir ön kapı standardı/Premium (Önizleme) oluşturun

İki Web Apps sunucusu arasındaki en düşük gecikme süresine göre Kullanıcı trafiğini yönlendirmek için Azure ön kapısı Standart/Premium (Önizleme) yapılandırma. Ayrıca ön kapılarınızın Web uygulaması güvenlik duvarı ile güvenliğini sağlayın. 

1. [Azure portalında](https://portal.azure.com) oturum açın.
 
1. Giriş sayfasından veya Azure menüsünde **+ kaynak oluştur**' u seçin.  *Ön kapı Standart/Premium (Önizleme)* Için arama yapın. Ardından **Oluştur**’u seçin.

1. **Teklifleri Karşılaştır** sayfasında, **özel oluştur**' u seçin. Ardından **bir ön kapı oluşturmak Için devam**' ı seçin.

1.  **Temel bilgiler**   sekmesinde aşağıdaki bilgileri girin veya seçin ve ardından **İleri: gizli** öğesini seçin. 

    | Ayar | Değer |
    | --- | --- |
    | **Abonelik** | Aboneliğinizi seçin. |
    | **Kaynak grubu** | **Yeni oluştur** ' u seçin ve metin kutusuna *FrontDoorQS_rg0* girin. |
    | **Kaynak grubu konumu** | **Doğu ABD** seçin |
    | **Profil adı** | Bu abonelikte benzersiz bir ad girin **WebApp-contoso-AFD** |
    | **Katman** | **Premium**' u seçin. |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Ön kapı profili oluşturma":::

1. *Isteğe bağlı*: **gizlilikler**. Yönetilen sertifikaları kullanmayı planlıyorsanız, bu adım isteğe bağlıdır. Azure 'da, özel etki alanı için kendi sertifikanızı getirmek için kullanmayı planladığınız mevcut bir Key Vault varsa, **sertifika ekle**' yi seçin. Ayrıca, oluşturulduktan sonra yönetim deneyimine sertifika ekleyebilirsiniz.

    > [!NOTE]
    > Sertifikayı Kullanıcı olarak Azure Key Vault eklemek için doğru izninizin olması gerekir. 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="Özel oluşturmaya gizli dizi ekleme ekran görüntüsü.":::

1. **Uç** nokta sekmesinde **bitiş noktası Ekle** ' yi seçin ve uç noktanıza genel olarak benzersiz bir ad verin. Oluşturma deneyimini tamamladıktan sonra, Azure ön kapısı Standart/Premium profilinizde birden fazla uç nokta oluşturabilirsiniz. Bu örnek *contoso-ön uç* kullanır. Kaynak yanıt zaman aşımını (saniye cinsinden) ve durumu varsayılan olarak bırakın. Uç noktayı eklemek için **Ekle** ' yi seçin.
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="Uç nokta Ekle ekran görüntüsü.":::

1. Sonra, iki Web uygulaması içeren bir kaynak grubu ekleyin.  **+ Ekle**' yi seçerek    **kaynak grubu Ekle** sayfasını açın. Ad için *Myorigingroup* girin ve **+ Kaynak Ekle**' yi seçin.
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="Kaynak grubu ekleme ekranının ekran görüntüsü.":::

1.  **Kaynak Ekle**   sayfasında, aşağıdaki bilgileri girin veya seçin. Ardından **Ekle**'yi seçin.

    | Ayar | Değer |
    | --- | --- |
    | **Ad** | **WebApp1** girin |
    | **Kaynak türü** | **Uygulama hizmetlerini** seçin |
    | **Konak adı** | `WebAppContoso-001.azurewebsites.net` seçeneğini belirleyin |
    | **Kaynak barındırma üst bilgisi** | `WebAppContoso-001.azurewebsites.net` seçeneğini belirleyin |
    | **Diğer alanlar** | Diğer tüm alanları varsayılan olarak bırakın. |

    > [!NOTE]
    > Azure ön kapısının Standart/Premium profilini oluştururken, Azure ön kapısı Standart/Premium 'un içinde oluşturulduğu abonelikten bir kaynak seçmeniz gerekir.
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="Daha fazla çıkış Ekle ekran görüntüsü.":::

1. İkinci kaynak webapp002 eklemek için 8. adımı tekrarlayın. `webappcontoso-002.azurewebsite.net` **Kaynak ana bilgisayar adı** ve **kaynak ana bilgisayar üst bilgisi** olarak öğesini seçin.

1. **Kaynak grubu Ekle** sayfasında, iki çıkış eklendiğini görürsünüz, diğer tüm alanları varsayılan olarak bırakın.
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="Kaynak grubu ekleme sayfasının ekran görüntüsü.":::

1. Ardından, ön uç uç noktanızı kaynak grubuna eşlemek için bir yol ekleyin. Bu rota istekleri uç noktadan myOriginGroup 'e iletir. Rota yapılandırmak için **+ rotada + Ekle** ' yi seçin.  

1. **Yol Ekle** sayfasında, aşağıdaki bilgileri girin veya seçin. Ardından **Ekle**'yi seçin.
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="Önbelleğe alma olmadan rota Ekle":::

    | Ayar | Değer |
    | --- | --- |
    | **Ad** | **Myroute** girin |    
    | **Etki alanı** | `contoso-frontend.z01.azurefd.net` seçeneğini belirleyin | 
    | **Konak adı** | `WebAppContoso-001.azurewebsites.net` seçeneğini belirleyin |
    | **Eşleştirilecek desenler** | Varsayılan değerde bırakın. |
    | **Kabul edilen protokoller** | Varsayılan değerde bırakın. | 
    | **Yeniden yönlendirme** | **Tüm TRAFIĞI HTTPS kullanacak şekilde yeniden yönlendirmek** için varsayılan olarak bırakın. | 
    | **Kaynak grubu** | **Myorigingroup** öğesini seçin. | 
    | **Kaynak yolu** | Varsayılan değerde bırakın. | 
    | **İletme Protokolü** | **Gelen Isteği Eşleştir**' i seçin. | 
    | **Önbelleğe Alma** | Bu hızlı başlangıçta işaretlenmemiş olarak bırakın. İçerikleri kenarlarda önbelleğe almak istiyorsanız, **önbelleğe almayı etkinleştir** onay kutusunu işaretleyin. |
    | **Kurallar** | Varsayılan değerde bırakın. Ön kapı profilinizi oluşturduktan sonra özel kurallar oluşturabilir ve bunları yollara uygulayabilirsiniz. |  
       
    >[!WARNING]
    > Her uç nokta için bir yol olduğundan **emin olun** . Bir yolun yokluğu, bir uç noktanın başarısız olmasına neden olabilir.

1. Ardından, bir WAF ilkesi eklemek için güvenlik üzerindeki **+ Ekle** ' yi seçin. Yeni **Ekle** ' yi seçin ve ilkenize benzersiz bir ad verin. **Bot koruması Ekle** onay kutusunu seçin. **Etki alanları**' nda uç noktayı seçip **Ekle**' yi seçin.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="WAF İlkesi Ekle":::

1.  **Gözden geçir + oluştur**' u ve ardından **Oluştur**' u seçin. Yapılandırmaların tüm kenar pop 'Lara yayılması birkaç dakika sürer. Artık ilk bir ön kapı profiliniz ve uç noktanız vardır.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="Özel oluşturmayı İncele":::

## <a name="verify-azure-front-door"></a>Azure ön kapısını doğrulama

Azure ön kapısının Standart/Premium profilini oluşturduğunuzda yapılandırmanın küresel olarak dağıtılması birkaç dakika sürer. Tamamlandıktan sonra, oluşturduğunuz ön uç konağına erişebilirsiniz. Bir tarayıcıda adresine gidin `contoso-frontend.z01.azurefd.net` . İsteğiniz, kaynak grubundaki belirtilen sunuculardan en yakın sunucuya otomatik olarak yönlendirilir.

Bu uygulamaları bu hızlı başlangıçta oluşturduysanız bir bilgi sayfası görürsünüz.

Hızlı genel yük devretmeyi test etmek için aşağıdaki adımları kullanacağız:

1. Yukarıda açıklandığı gibi bir tarayıcı açın ve ön uç adresine gidin: `contoso-frontend.azurefd.net` .

1. Azure portal, *uygulama hizmetleri*' ni arayıp seçin. Web uygulamalarınızdan birini bulmak için aşağı kaydırın, bu örnekte **Webappcontoso-001** .

1. Web uygulamanızı seçin ve ardından **Durdur**' u ve ardından doğrulamak için **Evet** ' i seçin.

1. Tarayıcınızı yenileyin. Aynı bilgi sayfasını görmeniz gerekir.

   >[!TIP]
   >Bu eylemler için biraz gecikme vardır. Yeniden yenilemeniz gerekebilir.

1. Diğer Web uygulamasını bulun ve bunu da durdurun.

1. Tarayıcınızı yenileyin. Bu kez bir hata iletisi görmeniz gerekir.

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="Her iki Web uygulaması örneği de durduruldu":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

İşiniz bittiğinde, oluşturduğunuz tüm öğeleri kaldırabilirsiniz. Kaynak grubunun silinmesi, içeriğini de siler. Bu ön kapıyı kullanmayı düşünmüyorsanız gereksiz ücretlerden kaçınmak için kaynakları kaldırmanız gerekir.

1. Azure portal, **kaynak gruplarını** arayıp seçin ya da Azure Portal menüsünden **kaynak grupları** ' nı seçin.

1. **FrontDoorQS_rg0** gibi bir kaynak grubu bulmak için filtre uygulayın veya aşağı kaydırın.

1. Kaynak grubunu seçin ve **kaynak grubunu sil**' i seçin.

   >[!WARNING]
   >Bu eyleme geri döndürülemez.

1. Doğrulanacak kaynak grubu adını yazın ve ardından **Sil**' i seçin.

Diğer iki grup için yordamı tekrarlayın.

## <a name="next-steps"></a>Sonraki adımlar

Ön kapıya özel bir etki alanı ekleme hakkında bilgi edinmek için sonraki makaleye ilerleyin.
> [!div class="nextstepaction"]
> [Özel etki alanı ekleme](how-to-add-custom-domain.md)