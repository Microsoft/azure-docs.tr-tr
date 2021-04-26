---
title: Azure Application Gateway ile URL ve sorgu dizesi yeniden yazma Azure portal
description: URL 'YI ve sorgu dizesini yeniden yazmak üzere bir Azure Application Gateway yapılandırmak için Azure portal nasıl kullanacağınızı öğrenin
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: how-to
ms.date: 4/05/2021
ms.author: azhussai
ms.openlocfilehash: b8ddc5e57b9ce56d6bce7e220bc840ba0fa43ae2
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384766"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal"></a>URL 'YI Azure Application Gateway yeniden yazma-Azure portal

Bu makalede, URL 'YI yeniden yazmak için [Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md) örneğini yapılandırmak üzere Azure Portal nasıl kullanılacağı açıklanır.

>[!NOTE]
> URL yeniden yazma özelliği yalnızca Application Gateway Standard_v2 ve WAF_v2 SKU 'SU için kullanılabilir. Bir WAF etkin ağ geçidinde URL yeniden yazma yapılandırıldığında, yeniden yazma isteği üst bilgileri ve URL 'SI üzerinde WAF değerlendirmesi gerçekleşmeyecektir. [Daha fazla bilgi edinin](rewrite-http-headers-url.md#using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki adımları tamamlayabilmeniz için bir Application Gateway v2 SKU örneğiniz olması gerekir. V1 SKU 'sunda URL yeniden yazma desteklenmiyor. V2 SKU 'SU yoksa, başlamadan önce bir [Application Gateway v2 SKU](tutorial-autoscale-ps.md) örneği oluşturun.

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla [Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="configure-url-rewrite"></a>URL yeniden yazmayı yapılandırma

Aşağıdaki örnekte, istek URL 'si */article*' ı her IÇERDIĞINDE, URL yolu ve URL sorgu dizesi yeniden yazılır

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. **Tüm kaynaklar**' ı seçin ve ardından uygulama ağ geçidinizi seçin.

2. Sol bölmedeki yeniden **yazma ' yı** seçin.

3. **Yeniden yazma kümesi** seçin:

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="Yeniden yazma kümesi Ekle":::

4. Yeniden yazma kümesi için bir ad girin ve bunu bir yönlendirme kuralıyla ilişkilendirin:

    a. **Ad** kutusuna yeniden yazma kümesinin adını girin.
    
    b. **İlişkili yönlendirme kuralları** listesinde listelenen kurallardan birini veya daha fazlasını seçin. Bu, yeniden yazma yapılandırmasını yönlendirme kuralı aracılığıyla kaynak dinleyicisiyle ilişkilendirmek için kullanılır. Yalnızca diğer yeniden yazma kümeleriyle ilişkilendirilmemiş olan yönlendirme kurallarını seçebilirsiniz. Diğer yeniden yazma kümeleriyle zaten ilişkilendirilmiş olan kurallar gri renkte.
    
    c. **İleri**’yi seçin.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="Bir kuralla ilişkilendir":::

5. Yeniden yazma kuralı oluştur:

    a. **Yeniden yazma kuralı ekle**' yi seçin.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="Vurguları yeniden yazma kuralı Ekle ' nin ekran görüntüsü.":::
    
    b. Yeniden yazma kuralı **adı** kutusuna yeniden yazma kuralı için bir ad girin. **Kural sırası** kutusuna bir sayı girin.

6. Bu örnekte, URL yolunu ve URL sorgu dizesini yalnızca yol */article* içerdiğinde yeniden yazacağız. Bunu yapmak için, URL yolunun */article* içerip içermediğini değerlendirmek için bir koşul ekleyin

    a. **Koşul Ekle** ' yi seçin ve ardından **genişletme yönergelerini içeren** kutuyu seçin.
    
    b. Bu örnekte, URL yolundaki */article* stilini denetlemek istiyoruz, **denetlenecek değişken türü** listesinde **sunucu değişkeni**' ni seçin.
    
    c. **Sunucu değişkeni** listesinde uri_path ' yi seçin.
    
    d. **Büyük/küçük harf duyarlı** altında **Hayır**' ı seçin.
    
    e. **İşleç** listesinde, **eşittir (=)** seçeneğini belirleyin.
    
    f. Normal ifade deseninin girin. Bu örnekte, bu kalıbı kullanacağız `.*article/(.*)/(.*)`
    
      (), URL yolunu yeniden yazmak için ifadeyi oluştururken daha sonra kullanmak üzere alt dizeyi yakalamak için kullanılır. Daha fazla bilgi için [buraya](rewrite-http-headers-url.md#capturing)bakın.

    örneğin: **Tamam**’ı seçin.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="Condition":::

 

7. URL 'yi ve URL yolunu yeniden yazmak için bir eylem ekleyin

   a. **Yeniden yazma türü** listesinde **URL**' yi seçin.

   b. **Eylem türü** listesinde **Ayarla**' yı seçin.

   c. **Bileşenler** altında **URL yolunu ve URL sorgu dizesini** seçin

   d. **URL yol değeri** alanına yolun yeni değerini girin. Bu örnekte, **/article.aspx** kullanacağız 

   e. **URL sorgu dizesi DEĞERINDE** URL sorgu dizesinin yeni değerini girin. Bu örnekte, **ID = {var_uri_path_1} &title = {var_uri_path_2}** kullanacağız
    
    `{var_uri_path_1}` ve `{var_uri_path_1}` Bu ifadede koşul değerlendirilirken yakalanan alt dizeleri getirmek için kullanılır `.*article/(.*)/(.*)`
    
   f. **Tamam**’ı seçin.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="Eylem":::

8. Yeniden yazma kümesini oluşturmak için **Oluştur** ' a tıklayın.

9. Yeni yeniden yazma kümesinin yeniden yazma kümeleri listesinde göründüğünü doğrulayın

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="Yeniden yazma kuralı ekle":::

## <a name="verify-url-rewrite-through-access-logs"></a>Erişim günlükleri aracılığıyla URL yeniden yazmayı doğrula

URL yeniden yazma beklentilerinizi uyarınca gerçekleştiyse emin olmak için, erişim günlüklerine aşağıdaki alanları gözlemleyin.

* **Originalrequesturiwithargs**: Bu alan, özgün istek URL 'sini içerir
* **RequestUri**: Bu alan, Application Gateway yeniden yazma işleminden sonraki URL 'yi içerir

Erişim günlüklerindeki tüm alanlarla ilgili daha fazla bilgi için [buraya](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku)bakın.

##  <a name="next-steps"></a>Sonraki adımlar

Bazı yaygın kullanım örnekleri için yeniden yazma ayarlama hakkında daha fazla bilgi edinmek için bkz. [ortak yeniden yazma senaryoları](rewrite-http-headers.md).
