---
title: Azure sanal ağ NAT ağ geçidiyle Azure Işlevleri giden IP 'yi denetleme
description: Azure sanal ağına bağlı bir işlev için NAT yapılandırma hakkında yönergeler sunan adım adım öğretici
ms.topic: tutorial
ms.author: kyburns
ms.date: 2/26/2021
ms.openlocfilehash: 5bb491e367ed813f09197a193745c231261c88c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658247"
---
# <a name="tutorial-control-azure-functions-outbound-ip-with-an-azure-virtual-network-nat-gateway"></a>Öğretici: Azure sanal ağ NAT ağ geçidiyle Azure Işlevleri giden IP 'yi denetleme

Sanal ağ adresi çevirisi (NAT), sanal ağlar için yalnızca giden internet bağlantısını basitleştirir. Bir alt ağda yapılandırıldığında, tüm giden bağlantılar belirttiğiniz statik genel IP adreslerini kullanır. Bir NAT, bir güvenlik önlemi olarak bir IP adresi izin kullanan bir üçüncü taraf hizmeti kullanması gereken Azure işlevleri veya Web Apps için yararlı olabilir. Daha fazla bilgi edinmek için bkz. [sanal ağ NAT nedir?](../virtual-network/nat-overview.md).

Bu öğreticide, HTTP ile tetiklenen bir işlevden giden trafiği yönlendirmek için sanal ağ NAT 'ları kullanma işlemi gösterilmektedir. Bu işlev kendi giden IP adresini denetlemenizi sağlar. Bu öğreticide şunları yapabilirsiniz:

> [!div class="checklist"]
> * Sanal ağ oluşturma
> * Premium plan işlev uygulaması oluşturma
> * Genel IP adresi oluşturma
> * NAT ağ geçidi oluşturma
> * İşlev uygulamasını, giden trafiği NAT ağ geçidi aracılığıyla yönlendirmek üzere yapılandırma

## <a name="topology"></a>Topoloji

Aşağıdaki diyagramda, oluşturduğunuz çözümün mimarisi gösterilmektedir:

![NAT Gateway tümleştirmesi için Kullanıcı arabirimi](./media/functions-how-to-use-nat-gateway/topology.png)

Premium planda çalışan işlevler, VNet tümleştirme özelliği de dahil olmak üzere Azure App Service Web Apps ile aynı barındırma özelliklerine sahiptir. Sorun giderme ve gelişmiş yapılandırma dahil VNet tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. [uygulamanızı bir Azure sanal ağı Ile tümleştirme](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide IP adresleme ve alt ağ oluşturma hakkında bilgi almanız önemlidir. [Bu makaleyle, adresleme ve alt ağ oluşturma temellerini kapsayan bir](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics)başlangıç yapabilirsiniz. Daha birçok makale ve video çevrimiçi olarak kullanılabilir.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[Tümleştirme işlevlerini bir Azure sanal ağ öğreticisiyle](./functions-create-vnet.md) zaten tamamladıysanız, [bir http tetikleyici işlevi oluşturma](#create-function)' ya atlayabilirsiniz.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

1. Azure portalı menüsünden **Kaynak oluştur**'u seçin. Azure Marketi ' nden **ağ**  >  **sanal ağı**' nı seçin.

1. **Sanal ağ oluştur**' da, aşağıdaki tabloda gösterildiği gibi belirtilen ayarları girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Abonelik | Aboneliğinizi seçin.|
    | Kaynak grubu | **Yeni oluştur**' u seçin, *myresourcegroup* yazın ve ardından **Tamam**' ı seçin. |
    | Name | *Myresourcegroup-VNET* girin. |
    | Konum | **Doğu ABD**’yi seçin.|

1. Ileri ' yi seçin **: IP adresleri** ve **IPv4 adres alanı** için *10.10.0.0/16* girin.

1. Alt **ağ ekle**' yi seçin ve alt ağ **adres aralığı** için *10.10.1.0/24* alt **ağ adı** için *öğretici-net* girin.

    ![VNET oluşturmak için IP adresleri sekmesi](./media/functions-how-to-use-nat-gateway/create-vnet-2-ip-space.png)

1. **Ekle**' yi ve ardından **gözden geçir + oluştur**' u seçin. Rest 'i varsayılan olarak bırakın ve **Oluştur**' u seçin.

1. **Sanal ağ oluştur**' da **Oluştur**' u seçin.

Ardından, [Premium planda](functions-premium-plan.md)bir işlev uygulaması oluşturursunuz. Bu plan, sanal ağ tümleştirmesini desteklerken sunucusuz ölçek sağlar.

## <a name="create-a-function-app-in-a-premium-plan"></a>Premium planda bir işlev uygulaması oluşturma

> [!NOTE]  
> Bu öğreticideki en iyi deneyim için çalışma zamanı yığını için .NET ' i seçin ve işletim sistemi için Windows ' u seçin. Ayrıca, sanal ağınızla aynı bölgede işlev uygulaması oluşturun.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-the-virtual-network"></a>İşlev uygulamanızı sanal ağa bağlama

Artık işlev uygulamanızı sanal ağa bağlayabilirsiniz.

1. İşlev uygulamanızda, sol taraftaki menüden **ağ** ' ı ve ardından **VNET tümleştirmesi**' nin altında, **yapılandırmak için buraya tıklayın ' ı** seçin.

    :::image type="content" source="./media/functions-how-to-use-nat-gateway/networking-0.png" alt-text="İşlev uygulamasındaki ağı seçin":::

1. **Sanal ağ tümleştirmesi** sayfasında **VNET Ekle**' yi seçin.

1. **Ağ özelliği durumu**' nda, görüntünün altındaki tabloda bulunan ayarları kullanın:

    ![App Virtual Network işlevini tanımlayın](./media/functions-how-to-use-nat-gateway/networking-3.png)

    | Ayar      | Önerilen değer  | Açıklama      |
    | ------------ | ---------------- | ---------------- |
    | **Sanal Ağ** | MyResourceGroup-VNET | Bu sanal ağ, daha önce oluşturduğunuz bir tane. |
    | **Alt ağ** | Yeni alt ağ oluştur | İşlev uygulamanızın kullanması için sanal ağda bir alt ağ oluşturun. VNet tümleştirmesi boş bir alt ağ kullanacak şekilde yapılandırılmalıdır. |
    | **Alt ağ adı** | Function-Net | Yeni alt ağın adı. |
    | **Sanal ağ adres bloğu** | 10.10.0.0/16 | Yalnızca bir adres bloğu tanımlanmış olmalıdır. |
    | **Alt ağ adres bloğu** | 10.10.2.0/24   | Alt ağ boyutu, Premium plan işlev uygulamanızın ölçeklenebilmesini sağlayan toplam örnek sayısını kısıtlar. Bu örnek `/24` , 254 kullanılabilir ana bilgisayar adresi olan bir alt ağ kullanır. Bu alt ağ daha fazla sağlanmış, ancak hesaplama kolaydır. |

1. Alt ağı eklemek için **Tamam ' ı** seçin. İşlev uygulaması sayfanıza dönmek için **VNET tümleştirme** ve **ağ özelliği durum** sayfalarını kapatın.

İşlev uygulaması artık sanal ağa erişebilir. Sonra, işlev uygulamasına HTTP ile tetiklenen bir işlev ekleyeceksiniz.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP tetikleyici işlevi oluşturma

1. **İşlevler** penceresinin sol menüsünde **işlevler**' i seçin ve ardından üst menüden **Ekle** ' yi seçin. 
 
1. **Yeni işlev** penceresinden **http tetikleyicisi** ' ni seçin ve **yeni işlev** için varsayılan adı kabul edin veya yeni bir ad girin. 

1. **Kod + test**' te, şablon tarafından oluşturulan C# betiği (. CSX) kodunu aşağıdaki kodla değiştirin: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
    
        var client = new HttpClient();
        var response = await client.GetAsync(@"https://ifconfig.me");
        var responseMessage = await response.Content.ReadAsStringAsync();
    
        return new OkObjectResult(responseMessage);
    }
    ```

    Bu kod, çağıranın IP adresini döndüren bir dış Web sitesi çağırır, bu durumda bu işlev budur. Bu yöntem, işlev uygulamanız tarafından kullanılan giden IP adresini kolayca belirlemenizi sağlar.

Artık işlevi çalıştırmaya ve geçerli giden IP 'Leri denetlemeye hazır olursunuz.

## <a name="verify-current-outbound-ips"></a>Geçerli giden IP 'Leri doğrula

Şimdi, işlevini çalıştırabilirsiniz. Ancak önce portalı iade edin ve işlev uygulaması tarafından hangi giden IP 'Lerin kullanıldığını görün.  

1. İşlev uygulamanızda **Özellikler** ' i seçin ve **giden IP adresleri** alanını gözden geçirin.

    ![İşlev uygulaması giden IP adreslerini görüntüle](./media/functions-how-to-use-nat-gateway/function-properties-ip.png)

1. Şimdi HTTP tetikleyici işlevimize dönün, **kod + test** ' i seçin ve ardından **Test/Çalıştır**' ı seçin.

    ![Test işlevi](./media/functions-how-to-use-nat-gateway/function-code-test.png)

1. İşlevi yürütmek için **Çalıştır** ' ı seçin ve ardından **çıkışa** geçiş yapın. 

    ![Test işlevi çıkışı](./media/functions-how-to-use-nat-gateway/function-test-1-output.png)

1. HTTP yanıt gövdesindeki IP adresinin, daha önce görüntülediğiniz giden IP adreslerinden değerlerden biri olduğunu doğrulayın.

Artık genel IP oluşturabilir ve bu giden IP adresini değiştirmek için bir NAT ağ geçidi kullanabilirsiniz.

## <a name="create-public-ip"></a>Genel IP oluştur

1. Kaynak grubunuzda, **Ekle**' yi seçin, **genel IP adresi** için Azure Marketi ' ni arayın ve **Oluştur**' u seçin. Görüntünün altındaki tabloda bulunan ayarları kullanın:

    ![Genel IP adresi oluştur](./media/functions-how-to-use-nat-gateway/create-public-ip.png)

    | Ayar      | Önerilen değer  |
    | ------------ | ---------------- |
    | **IP sürümü** | IPv4 |
    | **SKU** | Standart |
    | **Katman** | Bölgesel |
    | **Ad** | Giden-IP |
    | **Abonelik** | aboneliğinizin görüntülendiğinden emin olun | 
    | **Kaynak grubu** | myResourceGroup (veya kaynak grubunuza atadığınız ad) |
    | **Konum** | Doğu ABD (veya diğer kaynaklarınıza atadığınız konum) |
    | **Kullanılabilirlik Alanı** | Bölge yok |

1. Dağıtımı göndermek için **Oluştur** ' u seçin.

1. Dağıtım tamamlandıktan sonra, yeni oluşturulan genel IP adresi kaynağına gidin ve **genel BAKıŞTA** IP adresini görüntüleyin.

    ![Genel IP adresini görüntüle](./media/functions-how-to-use-nat-gateway/public-ip-overview.png)

## <a name="create-nat-gateway"></a>NAT ağ geçidi oluşturma

Şimdi NAT ağ geçidini oluşturalım. [Önceki sanal ağ öğreticisiyle](functions-create-vnet.md)başladığınızda, `Function-Net` Önerilen alt ağ adı idi ve `MyResourceGroup-vnet` Bu öğreticide önerilen sanal ağ adı vardı.

1. Kaynak grubunuzda, **Ekle**' yi seçin, **NAT Gateway** için Azure Marketi ' ni arayın ve **Oluştur**' u seçin. **Temel bilgiler** sekmesini doldurmak için görüntünün altındaki tablodaki ayarları kullanın:

    ![NAT ağ geçidi oluşturma](./media/functions-how-to-use-nat-gateway/create-nat-1-basics.png)

    | Ayar      | Önerilen değer  |
    | ------------ | ---------------- |  
    | **Abonelik** | Aboneliğiniz | 
    | **Kaynak grubu** | myResourceGroup (veya kaynak grubunuza atadığınız ad) |
    | **NAT ağ geçidi adı** | myNatGateway |
    | **Bölge** | Doğu ABD (veya diğer kaynaklarınıza atadığınız konum) |
    | **Kullanılabilirlik Alanı** | Yok |

1. **İleri ' yi seçin: gıden IP**. **Genel IP adresleri** alanında, önceden oluşturulmuş genel IP adresini seçin. **Genel IP öneklerini** seçilmemiş olarak bırakın.

1. **İleri ' yi seçin: alt ağ**. **Sanal ağ** alanındaki *myresourcegroup-VNET* kaynağını ve *Function-net* alt ağını seçin.

    ![Alt ağ seçin](./media/functions-how-to-use-nat-gateway/create-nat-3-subnet.png)

1. Dağıtımı **göndermek Için** **İncele + oluştur** ' u seçin.

Dağıtım tamamlandıktan sonra, NAT ağ geçidi, işlev uygulaması alt ağından Internet 'e trafik yönlendirmek için kullanılır.

## <a name="update-function-configuration"></a>İşlev yapılandırmasını Güncelleştir

Şimdi, bir değerine ayarlanmış bir uygulama ayarı eklemeniz gerekir `WEBSITE_VNET_ROUTE_ALL` `1` .  Bu ayar, giden trafiği sanal ağ ve ilişkili NAT ağ geçidiyle zorlar. Bu ayar olmadan, Internet trafiği tümleşik sanal ağ üzerinden yönlendirilmez ve aynı giden IP 'Leri görürsünüz. 

1. Azure portal işlev uygulamanıza gidin ve sol taraftaki menüden **yapılandırma** ' yı seçin.

1. **Uygulama ayarları**' nın altında **+ Yeni uygulama ayarı** ' nı seçin ve aşağıdaki değerleri kullanarak alanları doldurun:

    |Alan Adı  |Değer |
    |---|---|
    |**Ad**    |WEBSITE_VNET_ROUTE_ALL|
    |**Değer**   |1|

1. Yeni uygulama ayarı iletişim kutusunu kapatmak için **Tamam ' ı** seçin.

1. **Kaydet** ' i seçin ve ardından ayarları kaydetmeye **devam edin** .

İşlev uygulaması, trafiği, ilişkili sanal ağı aracılığıyla yönlendirmek üzere yapılandırıldı.

## <a name="verify-new-outbound-ips"></a>Yeni giden IP 'Leri doğrula

İşlevi yeniden çalıştırmak için [önceki adımları](#verify-current-outbound-ips) tekrarlayın. Artık işlev çıkışında gösterilen NAT 'da yapılandırdığınız giden IP adresini görmeniz gerekir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticiyi tamamlayabilmeniz için kaynaklar oluşturdunuz. [Hesap durumunuz](https://azure.microsoft.com/account/) ve [hizmet fiyatlandırmasına](https://azure.microsoft.com/pricing/)bağlı olarak bu kaynaklar için faturalandırılırsınız. Ek maliyetlerin oluşmasını önlemek için, daha fazla ihtiyaç duymanız durumunda kaynakları silin. 

[!INCLUDE [functions-quickstart-cleanup-inner](../../includes/functions-quickstart-cleanup-inner.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure İşlevleri ağ seçenekleri](functions-networking-options.md)
