---
title: Ön kapı, Azure portalını kullanarak HTTPS yeniden yönlendirmesi için HTTP ile oluşturma
description: HTTP yeniden yönlendirilen trafiğin Azure portalını kullanarak HTTPS ile ön kapısı oluşturmayı öğrenin.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 7fabc1e3445d3dbd357700ffde3caeb985cc60c4
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67601968"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Ön kapı, Azure portalını kullanarak HTTPS yeniden yönlendirmesi için HTTP ile oluşturma

Azure portalından oluşturmak için kullanabileceğiniz bir [ön kapısı](front-door-overview.md) ile SSL sonlandırma için bir sertifika. Yönlendirme kuralı, HTTP trafiğini HTTPS'ye yönlendirmek için kullanılır.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Bir ön kapısı ile mevcut bir Web uygulaması kaynağı oluşturma
> * SSL sertifikası ile özel bir etki alanı ekleme 
> * Özel etki alanı üzerinde HTTPS yeniden yönlendirmesi Kurulumu

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Bir ön kapısı ile mevcut bir Web uygulaması kaynağı oluşturma

1. [https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.
2. Azure portalının sol üst köşesinde bulunan **Kaynak oluştur** öğesine tıklayın.
3. Arama **ön kapısı** çubuk ve kaynak türünü bulduktan sonra tıklayın arama'yı kullanarak **Oluştur**.
4. Bir abonelik seçin ve ardından mevcut bir kaynak grubunu kullanın veya yeni bir tane oluşturun. Unutmayın kullanıcı Arabiriminde sorulan yalnızca kaynak grubu için konumdur. Tüm arasında ön kapısı yapılandırmanızı dağıtılacak [Azure ön kapısı'nın POP konumları](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service).

    ![Yeni ön kapısı temel yapılandırma](./media/front-door-url-redirect/front-door-create-basics.png)

5. Tıklayın **sonraki** yapılandırma sekmesinde girmek için. Varsayılan frontend ana bilgisayar ekleme, arka uçları bir arka uç havuzuna ekleme ve ardından ön uç ana bilgisayar yönlendirme davranışını eşlemek için yönlendirme kuralları oluşturma üç adımda - ön kapısı yapılandırmasını gerçekleşir. 

     ![Ön kapısı yapılandırma Tasarımcısı](./media/front-door-url-redirect/front-door-designer.png)

6. Tıklatın ' **+** ' simgesine _Frontend ana_ frontend ana bilgisayar oluşturmak için ön kapısı varsayılan ön uç konağınız için genel olarak benzersiz bir ad girin (`\<**name**\>.azurefd.net`). Tıklayın **Ekle** sonraki adıma geçin.

     ![Frontend ana bilgisayar ekleme](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Tıklatın ' **+** ' simgesine _arka uç havuzları_ bir arka uç havuzu oluşturmak için. Arka uç havuzu için bir ad belirtin ve ardından '**bir arka uç eklemenizi**'.
8. Arka uç ana bilgisayar türü olarak seçin _App Service'e_. Web uygulamanızın barındırıldığı aboneliğini seçin ve belirli bir web uygulaması için açılan listeden seçip **arka uç ana bilgisayar adı**.
9. Tıklayın **Ekle** arka uç kaydedip tıklayın **Ekle** arka uç havuzu yapılandırması yeniden kaydetmek için.   ![Bir arka uç havuzuna bir arka ucu ekleyin](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Tıklatın ' **+** ' simgesine _yönlendirme kuralları_ rota oluşturulacak. Söyleyin 'HttpToHttpsRedirect' yolu için bir ad belirtin ve ardından _kabul protokolleri_ alanı **'Yalnızca HTTP'** . Uygun olduğundan emin olun _frontend ana bilgisayar_ seçilir.  
11. Üzerinde _rota ayrıntıları_ bölümünde, _Rota türü_ için **yeniden yönlendirme**, emin _yeniden yönlendirme türü_ ayarlanır  **Bulundu (302)** ve _Yönlendirme Protokolü_ ayarlanır **yalnızca HTTPS**. 
12. Yönlendirme kuralı için HTTP, HTTPS yeniden yönlendirmesi için kaydetmek için Ekle'ye tıklayın.
     ![Bir HTTP, HTTPS yeniden yönlendirme yolu ekleyin](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. HTTPS trafiğini işlemek için başka bir yönlendirme kuralı ekleyin. Tıklatın ' **+** ' oturum açma _yönlendirme kuralları_ söyleyin 'DefaultForwardingRoute' yolu için bir ad belirtin ve ardından _kabul protokolleri_ alan için **'Yalnızca HTTPS'** . Uygun olduğundan emin olun _frontend ana bilgisayar_ seçilir.
14. Yol ayrıntıları bölümünü ayarlamak _Rota türü_ için **İleri**, doğru arka uç havuzu'nın seçili olduğundan emin olun ve _Yönlendirme Protokolü_ ayarlanır  **Yalnızca HTTPS**. 
15. İstek yönlendirme için yönlendirme kuralını kaydetmek için Ekle'ye tıklayın.
     ![HTTPS trafiği için ileriye doğru bir yol ekleyin](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Tıklayın **gözden geçir + Oluştur** ardından **Oluştur**ön kapısı profilinizi oluşturmak için. Oluşturulduktan sonra kaynağa gidin.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Özel bir etki alanı için ön kapı ekleyin ve üzerinde HTTPS'yi etkinleştirme
Aşağıdaki adımlar, nasıl özel etki alanı üzerinde var olan bir ön kapısı kaynağı ekleyin ve ardından üzerindeki HTTPS yeniden yönlendirmesi için HTTP etkinleştirme SERGİLEYİN. 

### <a name="add-a-custom-domain"></a>Özel etki alanı ekleme

Bu örnekte, bir CNAME kaydı eklediğiniz `www` alt etki alanı (örneğin, `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>CNAME kaydı oluşturma

Bir alt etki alanı ön kapısı'nın varsayılan ön uç konağa eşlenecek bir CNAME kaydı ekleyin (`<name>.azurefd.net`burada `<name>` ön kapısı profilinizi adıdır).

İçin `www.contoso.com` etki alanı, örnek olarak, ad eşleyen bir CNAME kaydı ekleyin `www` için `<name>.azurefd.net`.

CNAME kaydını ekledikten sonra, DNS kayıtları sayfası aşağıdaki örnekte gösterildiği gibi görünür:

![CNAME ön kapı için özel etki alanı](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Yerleşik özel etki alanınızı ön kapısı

1. Ön kapısı Tasarımcısı sekmesindeki tıklayarak '+' simgesi ön uç konaklardaki bölümünde yeni bir özel etki alanı eklemek için. 
2. Tam özel DNS adını özel ana bilgisayar adı alanına örnek `www.contosonews.com`. 
3. Etki alanı arasında CNAME eşlemesi için ön kapısı doğrulandıktan sonra tıklayarak **Ekle** özel etki alanı eklemek için.
4. Tıklayın **Kaydet** değişiklikleri göndermek için.

![Özel etki alanı menüsü](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Özel etki alanınızda HTTPS'yi etkinleştirme

1. Eklenmiş olan özel bir etki alanında bulunan ve bölümünde **özel etki alanı HTTPS**, durumunu değiştir **etkin**.
2. Bırakabilirsiniz **Sertifika Yönetim türü** kümesine _ön kapısı yönetilen_ ücretsiz sertifikası tutulan ve yönetilen ve autorotated ön kapısı tarafından. Azure anahtar kasası ile kendi özel SSL sertifikanızı kullanmayı seçebilirsiniz. Bu öğreticide, ön kapısı kullanımını sertifika yönetilen varsayılır.
![Özel etki alanı için HTTPS'yi etkinleştirme](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Tıklayarak **güncelleştirme** seçimi kaydedin ve ardından **Kaydet**.
4. Tıklayın **Yenile** sonra birkaç dakika ve ardından tekrar sertifika hazırlama işleminizin ilerleme durumunu görmek için özel etki alanı üzerinde. 

> [!WARNING]
> Özel bir etki alanı birkaç sürebilir için HTTPS'yi etkinleştirme ve dakikada bir CNAME doğrudan ön kapısı konağınız eşleşmişse ayrıca etki alanı sahipliğini doğrulama bağlıdır `<name>.azurefd.net`. Daha fazla bilgi edinin [için özel bir etki alanı HTTPS etkinleştirme](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Özel etki alanı için yönlendirme kurallarını yapılandırma

1. Daha önce oluşturulan yeniden yönlendirme yönlendirme kuralı tıklayın.
2. Ön uç konakları için üzerindeki açılır menüye tıklayın ve etki alanınız için de bu rota uygulamak için özel etki alanınızı seçin.
3. Tıklayın **güncelleştirme**.
4. Diğer yönlendirme kuralı için de aynı işlemi, diğer bir deyişle, özel etki alanı eklemek iletme rotanız için yapın.
5. Tıklayın **Kaydet** yaptığınız değişiklikleri göndermek için.

## <a name="next-steps"></a>Sonraki adımlar

- [Front Door oluşturmayı](quickstart-create-front-door.md) öğrenin.
- [Front Door’un nasıl çalıştığını](front-door-routing-architecture.md) öğrenin.
- Daha fazla bilgi edinin [URL'sini yeniden yönlendirme ön kapısı](front-door-url-redirect.md).
