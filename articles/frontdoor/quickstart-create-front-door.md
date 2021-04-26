---
title: 'Hızlı başlangıç: Azure ön kapı hizmeti ile yüksek kullanılabilirliği ayarlama-Azure portal'
description: Bu hızlı başlangıçta, Azure portal kullanarak yüksek oranda kullanılabilir ve yüksek performanslı Global Web uygulamanız için Azure ön kapı hizmeti 'nin nasıl kullanılacağı gösterilmektedir.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-portal
ms.openlocfilehash: 74b7c16904c86751076d40056027999fe44cb868
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727876"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Hızlı başlangıç: Yüksek oranda kullanılabilir global web uygulaması için Front Door oluşturma

Bir Web uygulaması için yüksek kullanılabilirlik ayarlamak üzere Azure portal kullanarak Azure ön kapısını kullanmaya başlayın.

Bu hızlı başlangıçta Azure ön kapısı, farklı Azure bölgelerinde çalışan bir Web uygulamasının iki örneğini havuzlar. Eşit ağırlıklı ve aynı öncelikli arka uçlara dayalı bir ön kapı yapılandırması oluşturursunuz. Bu yapılandırma, trafiği uygulamayı çalıştıran en yakın siteye yönlendirir. Azure ön kapısı, Web uygulamasını sürekli izler. Hizmet, en yakın site kullanılamadığında, kullanılabilir bir sonraki siteye otomatik yük devretme sağlar.

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="Azure portal kullanarak ön kapı dağıtım ortamının diyagramı." border="false":::

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-two-instances-of-a-web-app"></a>Bir Web uygulamasının iki örneğini oluşturma

Bu hızlı başlangıç, farklı Azure bölgelerinde çalışan bir Web uygulamasının iki örneğini gerektirir. Her iki Web uygulaması örneği de *etkin/etkin* modda çalışır, bu nedenle bir tane trafik alabilir. Bu yapılandırma, biri yük devretme görevi gören bir *etkin/bağımsız* yapılandırmadan farklıdır.

Henüz bir Web uygulamanız yoksa, örnek Web uygulamaları ayarlamak için aşağıdaki adımları kullanın.

1. https://portal.azure.com adresinden Azure portalında oturum açın.

1. Ekranın sol üst kısmında **kaynak oluştur**  >   **WebApp** öğesini seçin.

    :::image type="content" source="media/quickstart-create-front-door/front-door-create-web-app.png" alt-text="Azure portalında web uygulaması oluşturma":::

1. **Web uygulaması oluşturma** sayfasının **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin.

    | Ayar                 | Değer                                              |
    | ---                     | ---                                                |
    | **Abonelik**               | Aboneliğinizi seçin. |    
    | **Kaynak grubu**       | **Yeni oluştur** ' u seçin ve metin kutusuna *FrontDoorQS_rg1* girin.|
    | **Ad**                   | Web uygulamanız için benzersiz bir **ad** girin. Bu örnek *Webappcontoso-1*' i kullanır. |
    | **Yayımla** | **Kod**’u seçin. |
    | **Çalışma zamanı yığını**         | **.NET Core 2,1 (LTS)** seçeneğini belirleyin. |
    | **İşletim Sistemi**          | **Windows**' u seçin. |
    | **Bölge**           | **Orta ABD** seçin. |
    | **Windows Planı** | **Yeni oluştur** ' u seçin ve metin kutusuna *Myappserviceplanmerkezileştirmi* yazın. |
    | **Sku ve boyut** | **Standart S1 100 total acu, 1,75 GB bellek ' i** seçin. |

1. **Gözden geçir + oluştur**' u seçin, **Özeti** gözden geçirin ve ardından **Oluştur**' u seçin. Dağıtımın tamamlanması birkaç dakika sürebilir.

    :::image type="content" source="media/quickstart-create-front-door/create-web-app.png" alt-text="Web uygulaması için Özeti gözden geçirme":::

Dağıtımınız tamamlandıktan sonra ikinci bir Web uygulaması oluşturun. Aynı yordamı, aşağıdaki değerler dışında aynı değerlerle kullanın:

| Ayar          | Değer     |
| ---              | ---  |
| **Kaynak grubu**   | **Yeni oluştur** ' u seçin ve *FrontDoorQS_rg2* girin |
| **Ad**             | Web uygulamanız için benzersiz bir ad girin, bu örnekte *Webappcontoso-2*  |
| **Bölge**           | Farklı bir bölge, bu örnekte *Doğu ABD* |
| **App Service planı**  >  **Windows planı**         | **Yeni** ' yi seçin ve *myAppServicePlanEastUS* girin ve ardından **Tamam** ' ı seçin. |

## <a name="create-a-front-door-for-your-application"></a>Uygulamanız için Front Door oluşturma

İki Web Apps sunucusu arasındaki en düşük gecikme süresine göre Kullanıcı trafiğini yönlendirmek için Azure ön kapısını yapılandırın. Başlamak için, Azure ön kapısının ön uç konağını ekleyin.

1. Giriş sayfasından veya Azure menüsünde **kaynak oluştur**' u seçin. **Ağ**,  >  **Tüm**  >  **ön kapıyı** göster ' i seçin.

1. **Ön kapı oluştur** sayfasındaki **temel bilgiler** sekmesinde aşağıdaki bilgileri girin veya seçin ve ardından **İleri: yapılandırma**' yı seçin.

    | Ayar | Değer |
    | --- | --- |
    | **Abonelik** | Aboneliğinizi seçin. |    
    | **Kaynak grubu** | **Yeni oluştur** ' u seçin ve metin kutusuna *FrontDoorQS_rg0* girin.|
    | **Kaynak grubu konumu** | **Orta ABD** seçin. |

1.  **+** **Ön uç konak Ekle**' yi açmak için ön uçlar/etki alanları ' nı seçin.

1. **Ana bilgisayar adı** için, genel olarak benzersiz bir ana bilgisayar adı girin. Bu örnek *contoso-ön uç* kullanır. **Add (Ekle)** seçeneğini belirleyin.

    :::image type="content" source="media/quickstart-create-front-door/add-frontend-host-azure-front-door.png" alt-text="Azure ön kapısı için bir ön uç konağı ekleme":::

Sonra, iki Web uygulaması içeren bir arka uç havuzu oluşturun.

1. Hala **ön kapı oluşturun**, **arka uç havuzlarında**, **+** **arka uç Havuzu Ekle**' yi seçin.

1. **Ad** Için *Mybackendpool* yazın ve ardından **arka uç Ekle**' yi seçin.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool.png" alt-text="Arka uç Havuzu Ekle":::

1. **Arka uç Ekle** dikey penceresinde aşağıdaki bilgileri seçin ve **Ekle**' yi seçin.

    | Ayar | Değer |
    | --- | --- |
    | **Arka uç konak türü** | **App Service**'i seçin. |   
    | **Abonelik** | Aboneliğinizi seçin. |    
    | **Arka uç konak adı** | Oluşturduğunuz ilk Web uygulamasını seçin. Bu örnekte, Web uygulaması *Webappcontoso-1* idi. |

    **Tüm diğer alanları varsayılan olarak bırakın.*

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-backend.png" alt-text="Ön kapıya bir arka uç konağı ekleme":::

1. **Arka uç Ekle** ' yi seçin. Aşağıdaki bilgileri seçin ve **Ekle**' yi seçin.

    | Ayar | Değer |
    | --- | --- |
    | **Arka uç konak türü** | **App Service**'i seçin. |   
    | **Abonelik** | Aboneliğinizi seçin. |    
    | **Arka uç konak adı** | Oluşturduğunuz ikinci Web uygulamasını seçin. Bu örnekte, Web uygulaması *Webappcontoso-2* idi. |

    **Tüm diğer alanları varsayılan olarak bırakın.*

1. Arka uç havuzu yapılandırmasını gerçekleştirmek için **arka uç Havuzu Ekle** dikey penceresinde **Ekle** ' yi seçin.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool-complete.png" alt-text="Azure ön kapısı için bir arka uç havuzu ekleme":::

Son olarak, bir yönlendirme kuralı ekleyin. Bir yönlendirme kuralı, ön uç konunuzu arka uç havuzuna eşler. Kural, için bir isteği `contoso-frontend.azurefd.net` **Mybackendpool** öğesine iletir.

1. Hala **ön kapı oluştur**' da, **yönlendirme kuralları**' nda **+** bir yönlendirme kuralı yapılandırmak için öğesini seçin.

1. **Kural Ekle**' de, **ad** için *locationrule* yazın. Varsayılan değerleri kabul edin ve sonra yönlendirme kuralını eklemek için **Ekle** ' yi seçin.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-rule.png" alt-text="Ön Kapıınıza bir kural ekleyin":::

   >[!WARNING]
   > Ön kapıdaki her bir ön uç konağın, kendisiyle ilişkilendirilmiş bir yönlendirme kuralına sahip olduğundan emin **olmanız gerekir** `\*` . Diğer bir deyişle, tüm yönlendirme kurallarınızın tamamında, varsayılan yolda () tanımlanan ön uç konaklarınızın her biri için en az bir yönlendirme kuralı olmalıdır `\*` . Bunun başarısız olması, Son Kullanıcı trafiğinizin doğru şekilde yönlendirilmemesi sonucunu verebilir.

1. **Gözden geçir + oluştur**' u ve ardından **Oluştur**' u seçin.

    :::image type="content" source="media/quickstart-create-front-door/configuration-azure-front-door.png" alt-text="Yapılandırılmış Azure ön kapısı":::

## <a name="view-azure-front-door-in-action"></a>Azure ön kapısını görüntüleme işlemi

Ön kapı oluşturduktan sonra yapılandırmanın küresel olarak dağıtılması birkaç dakika sürer. Tamamlandıktan sonra oluşturduğunuz ön uç konağına erişin. Bir tarayıcıda adresine gidin `contoso-frontend.azurefd.net` . İsteğiniz, arka uç havuzundaki belirtilen sunuculardan sizin için en yakın sunucuya otomatik olarak yönlendirilir.

Bu uygulamaları bu hızlı başlangıçta oluşturduysanız bir bilgi sayfası görürsünüz.

Hızlı genel yük devretmeyi işlem sırasında test etmek için aşağıdaki adımları deneyin:

1. Yukarıda açıklandığı gibi bir tarayıcı açın ve ön uç adresine gidin: `contoso-frontend.azurefd.net` .

1. Azure portal, *uygulama hizmetleri*' ni arayıp seçin. Bu örnekte Web uygulamalarınızdan birini bulmak için aşağı kaydırın, **Webappcontoso-1** .

1. Web uygulamanızı seçin ve ardından **Durdur**' u ve ardından doğrulamak için **Evet** ' i seçin.

1. Tarayıcınızı yenileyin. Aynı bilgi sayfasını görmeniz gerekir.

   >[!TIP]
   >Bu eylemler için biraz gecikme vardır. Yeniden yenilemeniz gerekebilir.

1. Diğer Web uygulamasını bulun ve bunu da durdurun.

1. Tarayıcınızı yenileyin. Bu kez bir hata iletisi görmeniz gerekir.

   :::image type="content" source="media/quickstart-create-front-door/web-app-stopped-message.png" alt-text="Her iki Web uygulaması örneği de durduruldu":::

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
> [Özel etki alanı ekleme](front-door-custom-domain.md)
