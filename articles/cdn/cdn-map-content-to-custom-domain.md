---
title: 'Öğretici: uç noktanıza özel etki alanı ekleme'
titleSuffix: Azure Content Delivery Network
description: Etki alanı adınızın URL 'niz görünür olması için Azure Content Delivery Network uç noktasına özel bir etki alanı eklemek için bu öğreticiyi kullanın.
services: cdn
author: jessie-jyy
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 04/12/2021
ms.author: yuajia
ms.custom: mvc
ms.openlocfilehash: 63ca8cc981f7a192c35f7d1889187ae84773ba08
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308005"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>Öğretici: uç noktanıza özel etki alanı ekleme

Bu öğreticide bir Azure Content Delivery Network (CDN) uç noktasına özel etki alanı ekleme işlemi gösterilmektedir. 

CDN profilinizdeki uç nokta adı, azureedge.net öğesinin bir alt etki alanıdır. İçerik teslim edilirken varsayılan olarak, CDN profili etki alanı URL 'ye dahil edilir.

Örneğin, **https://contoso.azureedge.net/photo.png**.

Azure CDN, özel bir etki alanını CDN uç noktasıyla ilişkilendirme seçeneği sağlar. Bu seçenek, URL 'niz için varsayılan etki alanı yerine özel bir etki alanı içeren içerik sunar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - CNAME DNS kaydı oluşturun.
> - Özel etki alanını CDN uç noktanızla ilişkilendirin.
> - Özel etki alanını doğrulayın.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* Bu öğreticideki adımları tamamlayabilmeniz için önce bir CDN profili ve en az bir CDN uç noktası oluşturun. 
    * Daha fazla bilgi için bkz. [Hızlı Başlangıç: Azure CDN profili ve uç noktası oluşturma](cdn-create-new-endpoint.md).

* Özel bir etki alanınız yoksa, bir etki alanı sağlayıcısıyla satın alın. 
    * Daha fazla bilgi için bkz. [özel etki alanı adı satın alma](../app-service/manage-custom-dns-buy-domain.md).

* [DNS etki alanlarınızı](../dns/dns-overview.md)barındırmak için Azure kullanıyorsanız, Azure DNS için özel etki alanınızı atayın. 
    * Daha fazla bilgi için bkz. [Bir etki alanını Azure DNS'ye devretme](../dns/dns-delegate-domain-azure-dns.md).

* DNS etki alanınızı işlemek için bir etki alanı sağlayıcısı kullanıyorsanız, [BIR CNAME DNS kaydı oluşturmaya](#create-a-cname-dns-record)devam edin.


## <a name="create-a-cname-dns-record"></a>CNAME DNS kaydı oluşturma

Azure CDN uç noktası ile özel bir etki alanı kullanabilmeniz için önce Azure DNS veya DNS sağlayıcınıza sahip bir kurallı ad (CNAME) kaydı oluşturmanız gerekir ve bu, CDN uç noktanızı işaret edebilir. 

CNAME kaydı, bir kaynak etki alanı adını hedef etki alanı adıyla eşleyen bir DNS kaydıdır. 

Azure CDN için kaynak etki alanı adı, özel etki alanı adınızdır; hedef etki alanı adı ise CDN uç noktası ana bilgisayar adınızdır. 

Azure CDN, CNAME kaydını doğruladıktan sonra kaynak özel etki alanı ile hedef CDN uç noktası ana bilgisayar adına yönlendirilen trafiği yönlendirir.

Özel bir etki alanı ve alt etki alanı, aynı anda tek bir uç nokta ile ilişkilendirilebilir. 

Farklı Azure hizmetleri için aynı özel etki alanından farklı alt etki alanları için birden çok CNAME kaydı kullanın.

Farklı alt etki alanı olan özel bir etki alanını aynı CDN uç noktasına eşleyebilirsiniz.

> [!NOTE]
> Bu öğretici CNAME kayıt türünü kullanır. Ya da AAAA kayıt türleri kullanıyorsanız, aşağıdaki adımları uygulayın ve CNAME 'i istediğiniz kayıt türüyle değiştirin.

---
# <a name="azure-dns"></a>[**Azure DNS**](#tab/azure-dns)

Azure DNS, aynı abonelik içindeki Azure kaynakları için diğer ad kayıtlarını kullanır.

Azure CDN uç noktanız için bir diğer ad kaydı eklemek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Sol taraftaki menüden **tüm kaynaklar** ' ı ve ardından özel etki alanınız için Azure DNS bölgesini seçin.

3. Özel etki alanınız için DNS bölgesinde **+ kayıt kümesi**' ni seçin.

4. **Kayıt kümesi Ekle** ' de aşağıdaki bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Ad  | CDN uç noktanız için kullanmak istediğiniz diğer adı girin. Örneğin, **www**. |
    | Tür  | **CNAME**' i seçin. |
    | Diğer ad kayıt kümesi | **Evet**’i seçin. |
    | Diğer ad türü | **Azure kaynağı**' nı seçin. |
    | Abonelik seçin | Aboneliğinizi seçin. |
    | Azure kaynağı | Azure CDN uç noktanızı seçin. |

5. Kayıt için **TTL** değerini değer olarak değiştirin.

6. **Tamam**’ı seçin.

# <a name="dns-provider"></a>[**DNS sağlayıcısı**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>Geçici cdnverify alt etki alanını eşleme

Üretim aşamasındaki var olan bir etki alanını eşlerken göz önünde bulundurmanız gereken özel durumlar vardır. 

Özel etki alanınızı Azure portal kaydederken etki alanı için kısa bir kesinti süresi oluşabilir.

Web trafiğinin kesintiye uğramasını önlemek için, özel etki alanınızı Azure **cdnverify** alt etki alanı ile CDN uç nokta ana bilgisayar adı ile eşleyin. Bu işlem geçici bir CNAME eşlemesi oluşturur. 

Bu yöntemle kullanıcılar, DNS eşlemesi gerçekleşirken kesinti olmadan etki alanınıza erişebilir. 

Üretime kapalı kalma konusunda dikkat edilmesi gereken noktalar sorun değilse, özel etki alanınızı CDN uç noktanızla doğrudan eşleyebilirsiniz. [Kalıcı özel etki alanını eşlemeye](#map-the-permanent-custom-domain)devam edin.

cdnverify alt etki alanı ile bir CNAME kaydı oluşturmak için:

1. Özel etki alanınız için DNS sağlayıcısının Web sitesinde oturum açın.

2. Özel etki alanınız için bir CNAME kaydı girişi oluşturun ve alanları (alan adları değişebilir) aşağıdaki tabloda gösterildiği gibi tamamlayın:

    | Kaynak                    | Tür  | Hedef                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - Kaynak: cdnverify alt etki alanı da dahil olmak üzere özel etki alanı adınızı şu biçimde girin: **cdnverify. \<custom-domain-name>**
        - Örneğin: **cdnverify.www.contoso.com**

    - Tür: **CNAME** girin veya seçin.

    - Hedef: cdnverify alt etki alanı dahil olmak üzere CDN uç noktası ana bilgisayar adını şu biçimde girin: **cdnverify. \<endpoint-name> . azureedge.net**. 
        - Örneğin: **cdnverify.contoso.azureedge.net**

3. Yaptığınız değişiklikleri kaydedin.

## <a name="map-the-permanent-custom-domain"></a>Kalıcı özel etki alanını eşleme

Bu bölümde kalıcı özel etki alanını CDN uç noktasıyla eşlersiniz. 

Özel etki alanınıza yönelik bir CNAME kaydı oluşturmak için:

1. Özel etki alanınızın etki alanı sağlayıcısına ait web sitesinde oturum açın.

2. Özel etki alanınız için bir CNAME kaydı girişi oluşturun ve alanları (alan adları değişebilir) aşağıdaki tabloda gösterildiği gibi tamamlayın:

    | Kaynak          | Tür  | Hedef           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - Kaynak: özel etki alanı adınızı girin.
        - Örneğin: **www.contoso.com**

    - Tür: **CNAME** girin veya seçin.

    - Hedef: CDN uç noktası ana bilgisayar adını şu biçimde girin: **\<endpoint-name> . azureedge.net**. 
        - Örneğin: **contoso.azureedge.net**

3. Yaptığınız değişiklikleri kaydedin.

4. Daha önce geçici bir cdnverify alt etki alanı CNAME kaydı oluşturduysanız silin. 

5. Bu özel etki alanını üretimde ilk kez kullanıyorsanız, [özel etki ALANıNı CDN uç](#associate-the-custom-domain-with-your-cdn-endpoint) noktanızla ilişkilendirme ve [özel etki alanını doğrulama](#verify-the-custom-domain)adımlarını izleyin.

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>Özel etki alanını CDN uç noktanızla ilişkilendirme

Özel etki alanınızı kaydettikten sonra CDN uç noktanıza ekleyebilirsiniz. 


---
# <a name="azure-portal"></a>[**Azure portal**](#tab/azure-portal)

1. [Azure portalında](https://portal.azure.com/) oturum açın ve bir özel etki alanına eşlemek istediğiniz uç noktayı içeren CDN profiline göz atın.
    
2. **CDN profili** sayfasında, özel etki alanı ile ilişkilendirilecek CDN uç noktasını seçin.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="CDN uç noktası seçimi" border="true":::
    
3. **+ Özel etki alanı**' nı seçin. 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="Özel etki alanı Ekle düğmesi" border="true":::

4. **Özel etki alanı Ekle**' de, **uç nokta ana bilgisayar adı** önceden doldurulmuştur ve CDN uç nokta URL 'nizden türetilir: **\<endpoint-hostname>** . azureedge.net. Bu değer değiştirilemez.

5. **Özel ana bilgisayar adı** için, CNAME kaydınızın kaynak etki alanı olarak kullanılacak alt etki alanı dahil özel etki alanınızı girin. 
    1. Örneğin, **www.contoso.com** veya **CDN.contoso.com**. **Cdnverify alt etki alanı adını kullanmayın**.

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="Özel etki alanı Ekle" border="true":::

6. **Add (Ekle)** seçeneğini belirleyin.

   Azure, girdiğiniz özel etki alanı adı için CNAME kaydının bulunduğunu doğrular. CNAME doğruysa, özel etki alanınız doğrulanır. 

   Yeni özel etki alanı ayarlarının tüm CDN kenar düğümlerine yayılması biraz sürebilir: 
    - **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle 10 dakikada tamamlanır. 
    - **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
    - **Verizon’dan Azure CDN Standart** ve **Verizon’dan Azure CDN Premium** profilleri için yayma işlemi genellikle 10 dakika içinde tamamlanır.   

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

1. Azure PowerShell oturum açın:

```azurepowershell-interactive
    Connect-AzAccount

```
2. Özel etki alanını CDN uç noktanızla eşlemek için [New-AzCdnCustomDomain](/powershell/module/az.cdn/new-azcdncustomdomain) komutunu kullanın. 

    * **Myendpoint8675.azureedge.net** değerini Endpoint URL 'niz ile değiştirin.
    * **Myendpoint8675** değerini CDN uç nokta adınızla değiştirin.
    * **Www.contoso.com** değerini özel etki alanı adınızla değiştirin.
    * **Mycdn** 'yi CDN profil adınızla değiştirin.
    * **Myresourcegroupcdn** değerini kaynak grubu adınızla değiştirin.

```azurepowershell-interactive
    $parameters = @{
        Hostname = 'myendpoint8675.azureedge.net'
        EndPointName = 'myendpoint8675'
        CustomDomainName = 'www.contoso.com'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    New-AzCdnCustomDomain @parameters
```

Azure, girdiğiniz özel etki alanı adı için CNAME kaydının bulunduğunu doğrular. CNAME doğruysa, özel etki alanınız doğrulanır. 

   Yeni özel etki alanı ayarlarının tüm CDN kenar düğümlerine yayılması biraz sürebilir: 

- **Microsoft’tan Azure CDN Standart** profilleri için yayma işlemi genellikle 10 dakikada tamamlanır. 
- **Akamai’den Azure CDN Standart** profilleri için yayma işlemi genellikle bir dakika içinde tamamlanır. 
- **Verizon’dan Azure CDN Standart** ve **Verizon’dan Azure CDN Premium** profilleri için yayma işlemi genellikle 10 dakika içinde tamamlanır.   


---
## <a name="verify-the-custom-domain"></a>Özel etki alanını doğrulama

Özel etki alanınızı kaydınızı tamamladıktan sonra, özel etki alanının CDN uç noktanıza başvurduğundan emin olun.
 
1. Uç noktada önbelleğe alınan genel içeriğinizin olduğundan emin olun. Örneğin, CDN uç noktanız bir depolama hesabıyla ilişkiliyse, Azure CDN içeriği genel bir kapsayıcıda önbelleğe alır. Kapsayıcınızı ortak erişime izin verecek şekilde ayarlayın ve özel etki alanını sınamak için en az bir dosya içerir.

2. Tarayıcınızda, özel etki alanını kullanarak dosyanın adresine gidin. Örneğin, özel etki alanınız ise `www.contoso.com` , önbelleğe alınan dosyanın URL 'si AŞAĞıDAKI URL 'ye benzer olmalıdır: `http://www.contoso.com/my-public-container/my-file.jpg` . Sonuç olarak, CDN uç noktasına doğrudan. azureedge.net adresinden erişirken bu sonucun aynı olduğunu doğrulayın **\<endpoint-hostname>** .

## <a name="clean-up-resources"></a>Kaynakları temizleme

---
# <a name="azure-portal"></a>[**Azure portal**](#tab/azure-portal-cleanup)

Uç noktanızı artık özel bir etki alanıyla ilişkilendirmek istemiyorsanız, aşağıdaki adımları uygulayarak özel etki alanını kaldırın:
 
1. DNS sağlayıcınıza gidin, özel etki alanı için CNAME kaydını silin veya özel etki alanı için CNAME kaydını Azure CDN olmayan bir uç noktaya güncelleştirin.

    > [!Important]
    > DNS girişlerinin ve oluşturdukları güvenlik risklerini engellemek için, 2021 9 Nisan 'dan itibaren Azure CDN, kaynakların silinebilmesi için önce Azure CDN uç noktalara kaldırılması gerekir. Kaynaklar Azure CDN özel etki alanları, Azure CDN profiller/uç noktaları veya Azure CDN özel etki alanlarına sahip Azure Kaynak grupları içerir.

2. CDN profilinizde kaldırmak istediğiniz özel etki alanını içeren uç noktayı seçin.

3. **Uç nokta** sayfasındaki Özel etki alanları altında, kaldırmak istediğiniz özel etki alanına sağ tıklayın ve sonra açılır menüden **Sil**’i seçin. **Evet**’i seçin.

   Özel etki alanının uç noktanızla ilişkisi silinir.

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell-cleanup)

Uç noktanızı artık özel bir etki alanıyla ilişkilendirmek istemiyorsanız, aşağıdaki adımları uygulayarak özel etki alanını kaldırın:

1. DNS sağlayıcınıza gidin, özel etki alanı için CNAME kaydını silin veya özel etki alanı için CNAME kaydını Azure CDN olmayan bir uç noktaya güncelleştirin.

    > [!Important]
    > DNS girişlerinin ve oluşturdukları güvenlik risklerini engellemek için, 2021 9 Nisan 'dan itibaren Azure CDN, kaynakların silinebilmesi için önce Azure CDN uç noktalara kaldırılması gerekir. Kaynaklar Azure CDN özel etki alanları, Azure CDN profiller/uç noktaları veya Azure CDN özel etki alanlarına sahip Azure Kaynak grupları içerir.

2. Özel etki alanını uç noktadan kaldırmak için [Remove-AzCdnCustomDomain](/powershell/module/az.cdn/remove-azcdncustomdomain) komutunu kullanın:

    * **Myendpoint8675** değerini CDN uç nokta adınızla değiştirin.
    * **Www.contoso.com** değerini özel etki alanı adınızla değiştirin.
    * **Mycdn** 'yi CDN profil adınızla değiştirin.
    * **Myresourcegroupcdn** değerini kaynak grubu adınızla değiştirin.

    ```azurepowershell-interactive
        $parameters = @{
            CustomDomainName = 'www.contoso.com'
            EndPointName = 'myendpoint8675'
            ProfileName = 'myCDN'
            ResourceGroupName = 'myResourceGroupCDN'
        }
        Remove-AzCdnCustomDomain @parameters
    ```
---

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> - CNAME DNS kaydı oluşturun.
> - Özel etki alanını CDN uç noktanızla ilişkilendirin.
> - Özel etki alanını doğrulayın.

Bir Azure CDN özel etki alanı üzerinde HTTPS yapılandırma hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Öğretici: Azure CDN özel etki alanı üzerinde HTTPS yapılandırma](cdn-custom-ssl.md)