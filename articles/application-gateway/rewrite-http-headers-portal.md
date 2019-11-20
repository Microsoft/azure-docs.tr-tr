---
title: Portalda HTTP isteği ve yanıt üst bilgilerini yeniden yazma-Azure Application Gateway
description: Azure portal kullanarak, ağ geçidinden gelen isteklerinize ve yanıtlara HTTP üstbilgilerini yeniden yazmak üzere bir Azure Application Gateway yapılandırma hakkında bilgi edinin
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012851"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>HTTP isteği ve yanıt üst bilgilerini Azure Application Gateway yeniden yazma-Azure portal

Bu makalede, istek ve yanıtlara HTTP üstbilgilerini yeniden yazmak üzere bir [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) örneği yapılandırmak için Azure Portal nasıl kullanılacağı açıklanır.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki adımları tamamlayabilmeniz için bir Application Gateway v2 SKU örneğiniz olması gerekir. V1 SKU 'sunda üstbilgileri yeniden yazma desteklenmiyor. V2 SKU 'SU yoksa, başlamadan önce bir [Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) örneği oluşturun.

## <a name="create-required-objects"></a>Gerekli nesneleri oluştur

HTTP üstbilgisini yeniden yazmayı yapılandırmak için, bu adımları gerçekleştirmeniz gerekir.

1. HTTP üstbilgisi yeniden yazma için gereken nesneleri oluşturun:

   - **Yeniden yazma eylemi**: yeniden yazmayı düşündüğünüz istek ve istek üst bilgisi alanlarını ve üst bilgilerin yeni değerini belirtmek için kullanılır. Yeniden yazma eylemiyle bir veya daha fazla yeniden yazma koşulu ilişkilendirebilirsiniz.

   - **Yeniden yazma koşulu**: isteğe bağlı bir yapılandırma. Yeniden yazma koşulları, HTTP (S) isteklerinin ve yanıtlarının içeriğini değerlendirir. HTTP (S) isteği veya yanıtı yeniden yazma koşuluyla eşleşiyorsa, yeniden yazma eylemi gerçekleşir.

     Birden fazla koşulu bir eylemle ilişkilendirirseniz, eylem yalnızca tüm koşullar karşılandığında oluşur. Diğer bir deyişle, işlem mantıksal ve işlemdir.

   - **Yeniden yazma kuralı**: birden çok yeniden yazma eylemi/yeniden yazma koşulu kombinasyonu içeriyor.

   - **Kural sırası**: yeniden yazma kurallarının yürütülme sırasını belirlemesine yardımcı olur. Bu yapılandırma, bir yeniden yazma kümesinde birden fazla yeniden yazma kuralına sahip olduğunuzda yararlıdır. Daha düşük bir kural sırası değeri olan bir yeniden yazma kuralı önce çalışır. Aynı kural sırası değerini iki yeniden yazma kuralına atarsanız, yürütme sırası belirleyici değildir.

   - **Yeniden yazma kümesi**: bir istek yönlendirme kuralıyla ilişkilendirilecek çoklu yeniden yazma kuralları içerir.

2. Yeniden yazma kümesini bir yönlendirme kuralına ekleyin. Yeniden yazma yapılandırması, kaynak dinleyicisine yönlendirme kuralı aracılığıyla eklenir. Temel bir yönlendirme kuralı kullandığınızda, üst bilgi yeniden yazma yapılandırması bir kaynak dinleyicisi ile ilişkilendirilir ve genel üst bilgi yeniden yazma işlemi olur. Yol tabanlı bir yönlendirme kuralı kullandığınızda, üst bilgi yeniden yazma yapılandırması URL yol eşlemesinde tanımlanmıştır. Bu durumda, yalnızca bir sitenin belirli yol alanı için geçerlidir.

Birden çok HTTP üst bilgisi yeniden yazma kümesi oluşturabilir ve her bir yeniden yazma kümesini birden çok dinleyiciyle uygulayabilirsiniz. Ancak belirli bir dinleyiciye yalnızca bir yeniden yazma kümesi uygulayabilirsiniz.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla [Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="configure-header-rewrite"></a>Üst bilgi yeniden yazmayı yapılandırma

Bu örnekte, bir arka uç uygulaması tarafından gönderilen HTTP yanıtında konum üst bilgisini yeniden yazarak bir yeniden yönlendirme URL 'sini değiştireceksiniz.

1. **Tüm kaynaklar**' ı seçin ve ardından uygulama ağ geçidinizi seçin.

2. Sol bölmedeki yeniden **yazma ' yı** seçin.

3. **Yeniden yazma kümesi**seçin:

   ![Yeniden yazma kümesi Ekle](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Yeniden yazma kümesi için bir ad girin ve bunu bir yönlendirme kuralıyla ilişkilendirin:

   - **Ad** kutusuna yeniden yazma kümesinin adını girin.
   - **İlişkili yönlendirme kuralları** listesinde listelenen kurallardan birini veya daha fazlasını seçin. Yalnızca diğer yeniden yazma kümeleriyle ilişkili kuralların seçimini yapabilirsiniz. Diğer yeniden yazma kümeleriyle zaten ilişkilendirilmiş olan kurallar soluk.
   - **İleri**’yi seçin.
   
     ![Ad ve ilişkilendirme Ekle](media/rewrite-http-headers-portal/name-and-association.png)

5. Yeniden yazma kuralı oluştur:

   - **Yeniden yazma kuralı ekle**' yi seçin.

     ![Yeniden yazma kuralı ekle](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Yeniden yazma kuralı **adı** kutusuna yeniden yazma kuralı için bir ad girin. **Kural sırası** kutusuna bir sayı girin.

     ![Yeniden yazma kuralı adı Ekle](media/rewrite-http-headers-portal/rule-name.png)

6. Bu örnekte, konum üstbilgisini yalnızca azurewebsites.net başvurusu içerdiğinde yeniden yazacağız. Bunu yapmak için, yanıttaki konum üstbilgisinin azurewebsites.net içerip içermediğini değerlendirmek için bir koşul ekleyin:

   - **Koşul Ekle** ' yi seçin ve ardından **genişletme yönergelerini içeren** kutuyu seçin.

     ![Koşul ekleme](media/rewrite-http-headers-portal/add-condition.png)

   - **Denetlenecek değişken türü** listesinde **http üstbilgisi**' ni seçin.

   - **Üstbilgi türü** listesinde **Yanıt**' ı seçin.

   - Bu örnekte, ortak bir üst bilgi olan konum üst bilgisini değerlendiriyoruz, **üst bilgi adı**altında **ortak üst bilgi** ' yi seçin.

   - **Ortak üst bilgi** listesinde **konum**' u seçin.

   - **Büyük/küçük harf duyarlı**altında **Hayır**' ı seçin.

   - **İşleç** listesinde, **eşittir (=)** seçeneğini belirleyin.

   - Normal ifade deseninin girin. Bu örnekte, `(https?):\/\/.*azurewebsites\.net(.*)$`desenler kullanacağız.

   - **Tamam**’ı seçin.

     ![Bir If koşulu yapılandırma](media/rewrite-http-headers-portal/condition.png)

7. Konum üst bilgisini yeniden yazmak için bir eylem ekleyin:

   - **Eylem türü** listesinde **Ayarla**' yı seçin.

   - **Üstbilgi türü** listesinde **Yanıt**' ı seçin.

   - **Üstbilgi adı**altında **ortak üst bilgi**' yi seçin.

   - **Ortak üst bilgi** listesinde **konum**' u seçin.

   - Üst bilgi değerini girin. Bu örnekte, `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` üst bilgi değeri olarak kullanacağız. Bu değer, *azurewebsites.net* konum üstbilgisindeki *contoso.com* ile değiştirilir.

   - **Tamam**’ı seçin.

     ![Eylem ekleme](media/rewrite-http-headers-portal/action.png)

8. Yeniden yazma kümesini oluşturmak için **Oluştur** ' u seçin:

   ![Oluştur’u seçin](media/rewrite-http-headers-portal/create.png)

9. Yeniden yazma kümesi görünümü açılır. Oluşturduğunuz yeniden yazma kümesinin, yeniden yazma kümeleri listesinde olduğunu doğrulayın:

   ![Küme görünümü yeniden yaz](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Sonraki adımlar

Bazı yaygın kullanım örneklerini ayarlama hakkında daha fazla bilgi edinmek için bkz. [ortak üst bilgi yeniden yazma senaryoları](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).