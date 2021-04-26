---
title: Azure özel bağlantısı ile Redsıs için Azure önbelleği
description: Azure özel uç noktası, Azure özel bağlantısı tarafından desteklenen, özel olarak ve güvenli bir şekilde Azure önbelleğine bağlayan bir ağ arabirimidir. Bu makalede, Azure portal kullanarak bir Azure önbelleği, Azure sanal ağı ve özel bir uç nokta oluşturmayı öğreneceksiniz.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 3/31/2021
ms.openlocfilehash: 952f708d8f368b63f772e3af35f6fd441d65622d
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121668"
---
# <a name="azure-cache-for-redis-with-azure-private-link"></a>Azure özel bağlantısı ile Redsıs için Azure önbelleği
Bu makalede, Azure portal kullanarak özel bir uç nokta ile Redsıs örneği için bir sanal ağ ve Azure önbelleği oluşturmayı öğreneceksiniz. Ayrıca, Redsıs örneği için mevcut bir Azure önbelleğine özel bir uç nokta eklemeyi öğreneceksiniz.

Azure özel uç noktası, Azure özel bağlantısı tarafından desteklenen, özel olarak ve güvenli bir şekilde Azure önbelleğine bağlayan bir ağ arabirimidir. 

## <a name="prerequisites"></a>Önkoşullar
* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)

> [!IMPORTANT]
> Şu anda, bölge yedekliliği, Portal konsolu desteği ve güvenlik duvarı depolama hesaplarının kalıcılığı desteklenmez. 
>
>

## <a name="create-a-private-endpoint-with-a-new-azure-cache-for-redis-instance"></a>Redsıs örneği için yeni bir Azure önbelleği ile özel bir uç nokta oluşturma 

Bu bölümde, özel bir uç nokta ile Redsıs örneği için yeni bir Azure önbelleği oluşturacaksınız.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma 

1. [Azure Portal](https://portal.azure.com) oturum açın ve **kaynak oluştur**' u seçin.

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Kaynak oluştur ' u seçin.":::

2. **Yeni** sayfasında **ağ** ' ı seçin ve ardından **sanal ağ**' ı seçin.

3. Sanal ağ oluşturmak için **Ekle** ' yi seçin.

4. **Sanal ağ oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonelik** | Açılır ve aboneliğinizi seçin. | Bu sanal ağın oluşturulacağı abonelik. | 
   | **Kaynak grubu** | Açılır ve bir kaynak grubu seçin veya **Yeni oluştur** ' u seçin ve yeni bir kaynak grubu adı girin. | Sanal ağınızın ve diğer kaynaklarınızın oluşturulacağı kaynak grubunun adı. Tüm uygulama kaynaklarınızı tek bir kaynak grubuna yerleştirerek, bunları birlikte kolayca yönetebilir veya silebilirsiniz. | 
   | **Ad** | Bir sanal ağ adı girin. | Ad bir harf veya sayı ile başlamalı, harf, sayı veya alt çizgi ile bitmelidir ve yalnızca harf, sayı, alt çizgi, nokta veya kısa çizgi içerebilir. | 
   | **Bölge** | Açılır ve bir bölge seçin. | Sanal ağınızı kullanacak diğer hizmetlerin yakınında bir [bölge](https://azure.microsoft.com/regions/) seçin. |

5. **IP adresleri** sekmesini seçin veya sayfanın altındaki **Ileri: IP adresleri** düğmesine tıklayın.

6. **IP adresleri** sekmesinde, **ıPV4 adres alanını** CIDR gösteriminde bir veya daha fazla adres ön eki olarak belirtin (örneğin 192.168.1.0/24).

7. Alt ağ **adı** altında **varsayılan** ' a tıklayarak alt ağın özelliklerini düzenleyin.

8. **Alt ağı Düzenle** bölmesinde alt ağ **adını** ve **alt ağ adres aralığını** belirtin. Alt ağın adres aralığı CıDR gösteriminde (ör. 192.168.1.0/24) olmalıdır. Sanal ağın adres alanı tarafından içerilmelidir.

9. **Kaydet**’i seçin.

10. **Gözden geçir + oluştur** sekmesini seçin ya da **gözden geçir + oluştur** düğmesine tıklayın.

11. Tüm bilgilerin doğru olduğundan emin olun ve sanal ağı sağlamak için **Oluştur** ' a tıklayın.

### <a name="create-an-azure-cache-for-redis-instance-with-a-private-endpoint"></a>Özel bir uç nokta ile Redsıs örneği için Azure önbelleği oluşturma
Bir önbellek örneği oluşturmak için aşağıdaki adımları izleyin.

1. Azure portal giriş sayfasına dönün veya kenar çubuğu menüsünü açın, sonra **kaynak oluştur**' u seçin. 
   
1. **Yeni** sayfada **veritabanları** ' nı seçin ve ardından **redsıs için Azure önbelleği**' ni seçin.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Redsıs için Azure önbelleği ' ni seçin.":::
   
1. **Yeni Redis Cache** sayfasında, yeni önbelleğiniz için ayarları yapılandırın.
   
   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS adı** | Genel olarak benzersiz bir ad girin. | Önbellek adı, yalnızca rakam, harf veya kısa çizgi içeren 1 ile 63 karakter arasında bir dize olmalıdır. Ad bir sayı veya harfle başlamalı ve bitmeli ve ardışık kısa çizgi içeremez. Önbellek örneğinizin *ana bilgisayar adı* *\<DNS name> . Redis.cache.Windows.net* olacaktır. | 
   | **Abonelik** | Açılır ve aboneliğinizi seçin. | Redsıs örneği için bu yeni Azure önbelleğinin oluşturulacağı abonelik. | 
   | **Kaynak grubu** | Açılır ve bir kaynak grubu seçin veya **Yeni oluştur** ' u seçin ve yeni bir kaynak grubu adı girin. | Önbelleğinizin ve diğer kaynaklarınızın oluşturulacağı kaynak grubunun adı. Tüm uygulama kaynaklarınızı tek bir kaynak grubuna yerleştirerek, bunları birlikte kolayca yönetebilir veya silebilirsiniz. | 
   | **Konum** | Açılır ve bir konum seçin. | Önbelleğinizi kullanacak diğer hizmetlerin yakınında bir [bölge](https://azure.microsoft.com/regions/) seçin. |
   | **Fiyatlandırma katmanı** | Açılır ve bir [fiyatlandırma katmanı](https://azure.microsoft.com/pricing/details/cache/)seçin. |  Fiyatlandırma katmanı önbellek için kullanılabilen boyut, performans ve özellikleri belirler. Daha fazla bilgi için bkz. [redsıs Için Azure önbelleği 'Ne genel bakış](cache-overview.md). |

1. **Ağ** sekmesini seçin veya sayfanın altındaki **ağ** düğmesine tıklayın.

1. **Ağ** sekmesinde, bağlantı yöntemi Için **Özel uç nokta** ' ı seçin.

1. Özel uç noktanızı oluşturmak için **Ekle** düğmesine tıklayın.

    :::image type="content" source="media/cache-private-link/3-add-private-endpoint.png" alt-text="Ağ ' da özel bir uç nokta ekleyin.":::

1. **Özel uç nokta oluştur** sayfasında, Özel uç noktanızın ayarlarını, son bölümde oluşturduğunuz sanal ağ ve alt ağ ile yapılandırın ve **Tamam**' ı seçin. 

1. **İleri: Gelişmiş** sekmesini seçin veya sayfanın altındaki **İleri: Gelişmiş** düğmesine tıklayın.

1. Bir temel veya standart önbellek örneğinin **Gelişmiş** SEKMESINDE, TLS olmayan bir bağlantı noktasını etkinleştirmek istiyorsanız geçişi etkinleştir ' i seçin.

1. Premium önbellek örneğinin **Gelişmiş** SEKMESINDE, TLS olmayan bağlantı noktası, kümeleme ve veri kalıcılığı için ayarları yapılandırın.

1. **Sonraki: Etiketler** sekmesini seçin veya sayfanın altındaki **Sonraki: Etiketler** düğmesine tıklayın.

1. İsteğe bağlı olarak, **Etiketler** sekmesinde, kaynağı sınıflandırmak istiyorsanız ad ve değeri girin. 

1. **Gözden geçir ve oluştur**’u seçin. Azure 'un yapılandırmanızı doğruladığı, gözden geçir + Oluştur sekmesine götürülürsünüz.

1. Yeşil doğrulama başarılı iletisi göründüğünde **Oluştur**' u seçin.

Önbelleğin oluşturulması biraz zaman alır. Redsıs **genel bakış** sayfasında ilerlemeyi izleyebilirsiniz. **Durum** **çalışıyor** olarak görüntülendiğinde, önbellek kullanıma hazırdır. 
    
> [!IMPORTANT]
> 
> `publicNetworkAccess`Varsayılan olarak bir bayrak vardır `Disabled` . 
> Bu bayrak, isteğe bağlı olarak ayarlandıysa önbelleğe hem genel hem de özel uç nokta erişimine izin vermek için tasarlanmıştır `Enabled` . Olarak ayarlanırsa `Disabled` , yalnızca özel uç nokta erişimine izin verir. Değerini veya olarak ayarlayabilirsiniz `Disabled` `Enabled` . Değerin nasıl değiştirileceği hakkında daha fazla bilgi için bkz. [SSS](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)
>
>

## <a name="create-a-private-endpoint-with-an-existing-azure-cache-for-redis-instance"></a>Redsıs örneği için mevcut bir Azure önbelleği ile özel bir uç nokta oluşturma 

Bu bölümde, Redsıs örneği için mevcut bir Azure önbelleğine özel bir uç nokta ekleyeceksiniz. 

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma 
Bir sanal ağ oluşturmak için aşağıdaki adımları izleyin.

1. [Azure Portal](https://portal.azure.com) oturum açın ve **kaynak oluştur**' u seçin.

2. **Yeni** sayfasında **ağ** ' ı seçin ve ardından **sanal ağ**' ı seçin.

3. Sanal ağ oluşturmak için **Ekle** ' yi seçin.

4. **Sanal ağ oluştur**' da, **temel** bilgiler sekmesinde bu bilgileri girin veya seçin:

   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonelik** | Açılır ve aboneliğinizi seçin. | Bu sanal ağın oluşturulacağı abonelik. | 
   | **Kaynak grubu** | Açılır ve bir kaynak grubu seçin veya **Yeni oluştur** ' u seçin ve yeni bir kaynak grubu adı girin. | Sanal ağınızın ve diğer kaynaklarınızın oluşturulacağı kaynak grubunun adı. Tüm uygulama kaynaklarınızı tek bir kaynak grubuna yerleştirerek, bunları birlikte kolayca yönetebilir veya silebilirsiniz. | 
   | **Ad** | Bir sanal ağ adı girin. | Ad bir harf veya sayı ile başlamalı, harf, sayı veya alt çizgi ile bitmelidir ve yalnızca harf, sayı, alt çizgi, nokta veya kısa çizgi içerebilir. | 
   | **Bölge** | Açılır ve bir bölge seçin. | Sanal ağınızı kullanacak diğer hizmetlerin yakınında bir [bölge](https://azure.microsoft.com/regions/) seçin. |

5. **IP adresleri** sekmesini seçin veya sayfanın altındaki **Ileri: IP adresleri** düğmesine tıklayın.

6. **IP adresleri** sekmesinde, **ıPV4 adres alanını** CIDR gösteriminde bir veya daha fazla adres ön eki olarak belirtin (örneğin 192.168.1.0/24).

7. Alt ağ **adı** altında **varsayılan** ' a tıklayarak alt ağın özelliklerini düzenleyin.

8. **Alt ağı Düzenle** bölmesinde alt ağ **adını** ve **alt ağ adres aralığını** belirtin. Alt ağın adres aralığı CıDR gösteriminde (ör. 192.168.1.0/24) olmalıdır. Sanal ağın adres alanı tarafından içerilmelidir.

9. **Kaydet**’i seçin.

10. **Gözden geçir + oluştur** sekmesini seçin ya da **gözden geçir + oluştur** düğmesine tıklayın.

11. Tüm bilgilerin doğru olduğundan emin olun ve sanal ağı sağlamak için **Oluştur** ' a tıklayın.

### <a name="create-a-private-endpoint"></a>Özel uç nokta oluşturma 

Özel bir uç nokta oluşturmak için aşağıdaki adımları izleyin.

1. Azure portal, **Reda Için Azure önbelleği** araması yapın ve ENTER tuşuna basın veya arama önerilerinde seçin.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Redsıs için Azure önbelleği araması yapın.":::

2. Özel uç nokta eklemek istediğiniz önbellek örneğini seçin.

3. Ekranın sol tarafında **Özel uç nokta**' ı seçin.

4. Özel uç noktanızı oluşturmak için **Özel uç nokta** düğmesine tıklayın.

    :::image type="content" source="media/cache-private-link/5-add-private-endpoint.png" alt-text="Özel uç nokta ekleyin.":::

5. **Özel uç nokta Oluştur sayfasında**, Özel uç noktanız için ayarları yapılandırın.

   | Ayar      | Önerilen değer  | Açıklama |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonelik** | Açılır ve aboneliğinizi seçin. | Bu özel bitiş noktasının oluşturulacağı abonelik. | 
   | **Kaynak grubu** | Açılır ve bir kaynak grubu seçin veya **Yeni oluştur** ' u seçin ve yeni bir kaynak grubu adı girin. | Özel uç noktanızın ve diğer kaynaklarınızın oluşturulacağı kaynak grubunun adı. Tüm uygulama kaynaklarınızı tek bir kaynak grubuna yerleştirerek, bunları birlikte kolayca yönetebilir veya silebilirsiniz. | 
   | **Ad** | Özel bir uç nokta adı girin. | Ad bir harf veya sayı ile başlamalı, harf, sayı veya alt çizgi ile bitmelidir ve yalnızca harf, sayı, alt çizgi, nokta veya kısa çizgi içerebilir. | 
   | **Bölge** | Açılır ve bir bölge seçin. | Özel uç noktanızı kullanacak diğer hizmetlerin yakınında bir [bölge](https://azure.microsoft.com/regions/) seçin. |

6. Sayfanın alt kısmındaki **İleri: kaynak** düğmesine tıklayın.

7. **Kaynak** sekmesinde aboneliğinizi seçin, kaynak türünü olarak seçin `Microsoft.Cache/Redis` ve ardından özel uç noktasını bağlamak istediğiniz önbelleği seçin.

8. Sayfanın alt kısmındaki **İleri: yapılandırma** düğmesine tıklayın.

9. **Yapılandırma** sekmesinde, önceki bölümde oluşturduğunuz sanal ağı ve alt ağı seçin.

10. Sayfanın alt kısmındaki **İleri: Etiketler** düğmesine tıklayın.

11. İsteğe bağlı olarak, **Etiketler** sekmesinde, kaynağı sınıflandırmak istiyorsanız ad ve değeri girin.

12. **Gözden geçir ve oluştur**’u seçin. Azure 'un yapılandırmanızı doğruladığı, **gözden geçir + oluştur** sekmesine götürülürsünüz.

13. Yeşil **doğrulama başarılı** Iletisi göründüğünde **Oluştur**' u seçin.

> [!IMPORTANT]
> 
> `publicNetworkAccess`Varsayılan olarak bir bayrak vardır `Disabled` . 
> Bu bayrak, isteğe bağlı olarak ayarlandıysa önbelleğe hem genel hem de özel uç nokta erişimine izin vermek için tasarlanmıştır `Enabled` . Olarak ayarlanırsa `Disabled` , yalnızca özel uç nokta erişimine izin verir. Değerini veya olarak ayarlayabilirsiniz `Disabled` `Enabled` . Değerin nasıl değiştirileceği hakkında daha fazla bilgi için bkz. [SSS](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)
>
>


## <a name="faq"></a>SSS

### <a name="why-cant-i-connect-to-a-private-endpoint"></a>Özel bir uç noktaya neden bağlanamıyorum?
Önbelleğiniz zaten VNet 'e eklenmiş bir önbelleğiniz varsa, Özel uç noktalar önbellek örneğiniz ile kullanılamaz. Önbellek örneğiniz desteklenmeyen bir özellik kullanıyorsa (aşağıda listelenmiştir), Özel uç nokta örneğinize bağlanamazsınız.

### <a name="what-features-are-not-supported-with-private-endpoints"></a>Özel uç noktalarla hangi özellikler desteklenmez?
Şu anda, bölge yedekliliği, Portal konsolu desteği ve güvenlik duvarı depolama hesaplarının kalıcılığı desteklenmez. 

### <a name="how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access"></a>Özel uç noktadan devre dışı bırakmak veya genel ağ erişiminin etkinleştirilmesi için nasıl değiştirebilirim?
`publicNetworkAccess`Varsayılan olarak bir bayrak vardır `Disabled` . Bu bayrak, isteğe bağlı olarak ayarlandıysa önbelleğe hem genel hem de özel uç nokta erişimine izin vermek için tasarlanmıştır `Enabled` . Olarak ayarlanırsa `Disabled` , yalnızca özel uç nokta erişimine izin verir. Değeri, `Disabled` `Enabled` Azure Portal veya bir RESTSIZE API yaması isteğiyle ayarlayabilirsiniz. 

Azure portal değeri değiştirmek için aşağıdaki adımları izleyin.

1. Azure portal, **Reda Için Azure önbelleği** araması yapın ve ENTER tuşuna basın veya arama önerilerinde seçin.

2. Ortak ağ erişim değerini değiştirmek istediğiniz önbellek örneğini seçin.

3. Ekranın sol tarafında **Özel uç nokta**' ı seçin.

4. **Ortak ağ erişimini etkinleştir** düğmesine tıklayın.

Yeniden düzenlenecek bir API düzeltme eki isteğiyle değeri değiştirmek için, aşağıda bulunan ve önbelleğiniz için istediğiniz bayrağı yansıtmak üzere değeri düzenleme bölümüne bakın.

```http
PATCH  https://management.azure.com/subscriptions/{subscription}/resourceGroups/{resourcegroup}/providers/Microsoft.Cache/Redis/{cache}?api-version=2020-06-01
{    "properties": {
       "publicNetworkAccess":"Disabled"
   }
}
```

### <a name="how-can-i-have-multiple-endpoints-in-different-virtual-networks"></a>Farklı sanal ağlarda birden çok uç noktaya nasıl sahip olabilirim?
Farklı sanal ağlarda birden çok özel uç noktası olması için, özel bir uç nokta oluşturmadan _önce_ özel DNS bölgesinin birden çok sanal ağa el ile yapılandırılması gerekir. Daha fazla bilgi için bkz. [Azure Özel Uç Nokta DNS yapılandırması](../private-link/private-endpoint-dns.md). 

### <a name="what-happens-if-i-delete-all-the-private-endpoints-on-my-cache"></a>Önbelleğim tüm özel uç noktalarını silersem ne olur?
Önbelleğinizin özel uç noktalarını sildikten sonra, genel ağ erişimini açıkça etkinleştirene veya başka bir özel uç nokta eklediğiniz sürece önbellek örneğiniz ulaşılamaz hale gelebilir. `publicNetworkAccess`Azure Portal bayrağını veya yeniden BIR API düzeltme eki isteği aracılığıyla değiştirebilirsiniz. Değerin nasıl değiştirileceği hakkında daha fazla bilgi için bkz. [SSS](#how-can-i-change-my-private-endpoint-to-be-disabled-or-enabled-from-public-network-access)

### <a name="are-network-security-groups-nsg-enabled-for-private-endpoints"></a>Ağ güvenlik grupları (NSG) özel uç noktalar için etkin mi?
Hayır, Özel uç noktalar için devre dışı bırakılmıştır. Özel uç noktayı içeren alt ağlarda NSG ile ilişkili olabilir, kurallar özel uç nokta tarafından işlenen trafikte geçerli olmayacaktır. Bir alt ağda özel uç noktalar dağıtmak için [ağ ilkeleri zorlamasının devre dışı](../private-link/disable-private-endpoint-network-policy.md) olması gerekir. NSG aynı alt ağda barındırılan diğer iş yükleri üzerinde de zorlanır. Herhangi bir istemci alt ağındaki rotalar bir/32 öneki kullanacaktır, varsayılan yönlendirme davranışının değiştirilmesi benzer bir UDR gerektirir. 

Kaynak istemcilerde giden trafik için NSG kurallarını kullanarak trafiği denetleyin. Özel uç nokta yollarını geçersiz kılmak için/32 ön ekiyle tek tek yolları dağıtın. Giden bağlantılar için NSG akış günlükleri ve izleme bilgileri hala destekleniyor ve kullanılabilir

### <a name="since-my-private-endpoint-instance-is-not-in-my-vnet-how-is-it-associated-with-my-vnet"></a>Özel uç nokta örneğim VNet 'imde olmadığından, sanal ağım ile nasıl ilişkilendirilir?
Yalnızca VNet 'iniz ile bağlantılıdır. VNet 'iniz içinde olmadığından, NSG kurallarının bağımlı uç noktalar için değiştirilmesi gerekmez.

### <a name="how-can-i-migrate-my-vnet-injected-cache-to-a-private-endpoint-cache"></a>VNet 'e eklenen önbelleğinizi özel bir uç nokta önbelleğine nasıl geçirebilirim?
VNet eklenen önbelleğinizi silmeniz ve özel bir uç nokta ile yeni bir önbellek örneği oluşturmanız gerekir. Daha fazla bilgi için bkz. [redsıs Için Azure önbelleğine geçirme](cache-migration-guide.md)

## <a name="next-steps"></a>Sonraki adımlar
* Azure özel bağlantısı hakkında daha fazla bilgi edinmek için bkz. [Azure özel bağlantı belgeleri](../private-link/private-link-overview.md).
* Önbellek örneğiniz için çeşitli ağ yalıtımı seçeneklerini karşılaştırmak için bkz. [redsıs ağ yalıtımı seçenekleri Için Azure önbelleği](cache-network-isolation.md).
