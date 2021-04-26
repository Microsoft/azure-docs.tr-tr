---
title: Özel bağlantıyla özel erişimi etkinleştir (Önizleme)-Portal
titleSuffix: Azure Digital Twins
description: Azure portal kullanarak, özel bağlantıyla Azure dijital TWINS çözümleri için özel erişimi etkinleştirme makalesine bakın.
author: baanders
ms.author: baanders
ms.date: 1/25/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2545915edf9e39b63100a2bb16bd34fa6777675c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100418261"
---
# <a name="enable-private-access-with-private-link-preview-azure-portal"></a>Özel bağlantıyla özel erişimi etkinleştir (Önizleme): Azure portal

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

Bu makalede, [bir Azure dijital TWINS örneği (Şu anda önizleme aşamasında) için özel bir uç nokta Ile özel bağlantının etkinleştirilmesi](concepts-security.md#private-network-access-with-azure-private-link-preview) için farklı yollar açıklanmaktadır. Azure dijital TWINS örneğiniz için özel bir uç nokta yapılandırmak, Azure dijital TWINS örneğinizi güvenli hale getirmenizi ve genel pozlamayı ortadan kaldırmanıza ve [Azure sanal ağınızdan (VNet)](../virtual-network/virtual-networks-overview.md)veri alımını önlemenize olanak sağlar.

Bu makale [**Azure Portal**](https://portal.azure.com)kullanarak işlemi adım adım açıklar.

Bu makalede ele alınan adımlar aşağıda verilmiştir: 
1. Özel bağlantıyı açın ve bir Azure dijital TWINS örneği için özel bir uç nokta yapılandırın.
1. Yalnızca özel bağlantı bağlantılarına yönelik API erişimini kısıtlamak için ortak ağ erişim bayraklarını devre dışı bırakın veya etkinleştirin.

## <a name="prerequisites"></a>Önkoşullar

Özel bir uç nokta ayarlamadan önce, uç noktanın dağıtılabileceği bir [**Azure sanal ağı (VNet)**](../virtual-network/virtual-networks-overview.md) gerekir. Zaten bir sanal ağınız yoksa, bunu ayarlamak için Azure sanal ağ [hızlı](../virtual-network/quick-create-portal.md) başlangıçlarından birini izleyebilirsiniz.

## <a name="add-a-private-endpoint-for-an-azure-digital-twins-instance"></a>Azure dijital TWINS örneği için özel uç nokta ekleme 

[Azure Portal](https://portal.azure.com)kullanırken, bir Azure dijital TWINS örneği için özel bir uç nokta Ile özel bağlantıyı, örneğin ilk kurulumunun bir parçası olarak açmayı veya daha sonra zaten var olan bir örnekte etkinleştirmeyi seçebilirsiniz. 

Bu oluşturma yöntemlerinden herhangi biri, örneğiniz için aynı yapılandırma seçeneklerini ve aynı nihai sonucu verecektir. Bu bölümde, her birinin nasıl yapılacağı açıklanmaktadır.

>[!TIP]
> Ayrıca, Azure dijital TWINS örneğiniz yerine özel bağlantı hizmeti aracılığıyla özel bağlantı uç noktası da ayarlayabilirsiniz. Bu aynı zamanda aynı yapılandırma seçeneklerini ve aynı nihai sonucu verir.
>
> Özel bağlantı kaynaklarını ayarlama hakkında daha fazla bilgi için bkz. [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure CLI](../private-link/create-private-endpoint-cli.md), [ARM](../private-link/create-private-endpoint-template.md)veya [PowerShell](../private-link/create-private-endpoint-powershell.md)için özel bağlantı belgeleri.

### <a name="add-a-private-endpoint-during-instance-creation"></a>Örnek oluşturma sırasında özel bir uç nokta ekleme

Bu bölümde, şu anda oluşturulmakta olan bir Azure dijital TWINS örneğinde özel bir uç nokta ile özel bağlantı etkinleştireceksiniz. Bu bölüm, oluşturma işleminin ağ adımına odaklanır; Yeni bir Azure dijital TWINS örneği oluşturma hakkında tam bir anlatım için bkz. [*nasıl yapılır: örnek ve kimlik doğrulama ayarlama*](how-to-set-up-instance-portal.md).

Özel bağlantı seçenekleri, örnek kurulumunun **ağ** sekmesinde bulunur.

Bu sekmede, **bağlantı yöntemi** için **Özel uç nokta** seçeneğini belirleyerek özel uç noktaları etkinleştirebilirsiniz.

Bu, Özel uç noktanın ayrıntılarını yapılandırabileceğiniz **Özel uç nokta bağlantıları** adlı bir bölüm ekler. Devam etmek için **+ Ekle** düğmesini seçin.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-1.png" alt-text="Azure dijital TWINS için kaynak oluştur iletişim kutusunun ağ sekmesini gösteren Azure portal ekran görüntüsü. Yeni bir özel uç nokta bağlantısı oluşturmak için sekme adı, bağlantı yöntemi için özel uç nokta seçeneği ve + Ekle düğmesi etrafında bir vurgulama vardır." lightbox="media/how-to-enable-private-link/create-instance-networking-1.png":::

Bu, yeni bir özel bitiş noktasının ayrıntılarını girmek için bir sayfa açar.

:::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-full.png" alt-text="Özel uç nokta Oluştur sayfasının gösterildiği Azure portal ekran görüntüsü. Aşağıda açıklanan alanları içerir.":::

1. **Aboneliğiniz** ve **kaynak grubunuz** için seçimleri girin. **Konumu** , kullanacağınız VNET ile aynı konuma ayarlayın. Uç nokta için bir **ad** seçin ve **hedef alt kaynaklar** için *API*'yi seçin.

1. Ardından, uç noktayı dağıtmak için kullanmak istediğiniz **sanal ağı** ve **alt ağı** seçin.

1. Son olarak, **özel DNS bölgesi Ile tümleştirme** yapılıp yapılmayacağını seçin. Varsayılan değer olan **Evet** ' i veya bu seçenekle ilgili yardım için, [özel DNS tümleştirmesi hakkında daha fazla bilgi edinmek](../private-link/private-endpoint-overview.md#dns-configuration)üzere portaldaki bağlantıyı izleyebilirsiniz.

Yapılandırma seçeneklerini doldurduktan sonra, bitmek üzere **Tamam** ' a basın.

Bu, sizi Azure Digital TWINS örnek kurulumunun **ağ** sekmesine döndürür; burada yeni uç noktanızın * * özel uç nokta bağlantıları altında görünür olması gerekir.

:::image type="content" source="media/how-to-enable-private-link/create-instance-networking-2.png" alt-text="Azure dijital TWINS için kaynak oluştur iletişim kutusunun ağ sekmesini gösteren Azure portal ekran görüntüsü. Yeni özel uç nokta bağlantısını ve gezinti düğmelerini (gözden geçir + oluştur, önceki, Ileri: Gelişmiş) etrafında bir vurgulama var." lightbox="media/how-to-enable-private-link/create-instance-networking-2.png":::

Daha sonra, örnek kurulumunun geri kalanı ile devam etmek için alt gezinti düğmelerini kullanabilirsiniz.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Var olan bir örneğe özel bir uç nokta ekleme

Bu bölümde, zaten var olan bir Azure dijital TWINS örneği için özel bir uç nokta ile özel bağlantı etkinleştireceksiniz.

1. İlk olarak, bir tarayıcıda [Azure Portal](https://portal.azure.com) gidin. Portal arama çubuğunda adını arayarak Azure dijital TWINS örneğinizi getirin.

1. Sol taraftaki menüde **ağ (Önizleme)** öğesini seçin.

1. **Özel uç nokta bağlantıları** sekmesine geçin.

1. **Özel uç nokta oluşturma** kurulumunu açmak Için **+ Özel uç nokta** ' ı seçin.

    :::image type="content" source="media/how-to-enable-private-link/add-endpoint-digital-twins.png" alt-text="Bir Azure dijital TWINS örneği için ağ (Önizleme) sayfasını gösteren Azure portal ekran görüntüsü. Özel uç nokta bağlantıları sekmesi vurgulanır ve + özel uç nokta düğmesi de vurgulanır." lightbox="media/how-to-enable-private-link/add-endpoint-digital-twins.png":::

1.  **Temel bilgiler**   sekmesinde, projenizin **abonelik** ve **kaynak grubunu** ve uç noktanız için bir **ad** ve **bölge** girin veya seçin. Bölgenin, kullanmakta olduğunuz VNet 'in bölgesiyle aynı olması gerekir.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-1.png" alt-text="Özel uç nokta oluştur iletişim kutusunun ilk (temel bilgiler) sekmesini gösteren Azure portal ekran görüntüsü. Yukarıda açıklanan alanları içerir.":::

    İşiniz bittiğinde sonraki sekmeye gitmek için **Sonraki: kaynak >** düğmesini seçin.

1. **Kaynak** sekmesinde, bu bilgileri girin veya seçin: 
    * **Bağlantı yöntemi**: Azure dijital TWINS örneğinizi aramak için **dizinimde bir Azure kaynağına bağlan '** ı seçin.
    * **Abonelik**: aboneliğinizi girin.
    * **Kaynak türü**: **Microsoft. digitaltwins/Digitaltwınsınstances** seçin
    * **Kaynak**: Azure dijital TWINS örneğinizin adını seçin.
    * **Hedef alt kaynak**: **API 'yi** seçin.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-2.png" alt-text="Özel uç nokta oluştur iletişim kutusunun ikinci (kaynak) sekmesini gösteren Azure portal ekran görüntüsü. Yukarıda açıklanan alanları içerir.":::

    İşiniz bittiğinde sonraki sekmeye gitmek için **Sonraki: yapılandırma >** düğmesini seçin.    

1.  **Yapılandırma** sekmesinde, bu bilgileri girin veya seçin:
    * **Sanal ağ**: Sanal ağınızı seçin.
    * **Alt ağ**: sanal ağınızdan bir alt ağ seçin.
    * **Özel DNS bölgesi Ile tümleştirin**: **özel DNS bölgesi ile tümleştirme** yapılıp yapılmayacağını seçin. Varsayılan değer olan **Evet** ' i veya bu seçenekle ilgili yardım için, [özel DNS tümleştirmesi hakkında daha fazla bilgi edinmek](../private-link/private-endpoint-overview.md#dns-configuration)üzere portaldaki bağlantıyı izleyebilirsiniz.
    **Evet**' i seçerseniz varsayılan yapılandırma bilgilerini bırakabilirsiniz.

    :::image type="content" source="media/how-to-enable-private-link/create-private-endpoint-3.png" alt-text="Özel uç nokta oluştur iletişim kutusunun üçüncü (yapılandırma) sekmesini gösteren Azure portal ekran görüntüsü. Yukarıda açıklanan alanları içerir.":::

    İşiniz bittiğinde, kurulumu tamamladıktan sonra **gözden geçir + oluştur** düğmesini seçebilirsiniz. 

1. **Gözden geçir + oluştur** sekmesinde seçimlerinizi gözden geçirin ve  **Oluştur** düğmesini seçin. 

Uç noktanın dağıtımı tamamlandığında, Azure dijital TWINS örneğinizin özel uç nokta bağlantılarında gösterilmesi gerekir.

>[!TIP]
> Uç nokta, Azure portal özel bağlantı merkezinden de görüntülenebilir.

## <a name="disable--enable-public-network-access-flags"></a>Ortak ağ erişim bayraklarını devre dışı bırak/etkinleştir

Azure dijital TWINS örneğinizi tüm genel bağlantıları reddedecek şekilde yapılandırabilir ve yalnızca ağ güvenliğini geliştirmek için özel uç noktalar aracılığıyla bağlantılara izin verebilirsiniz. Bu eylem, bir **genel ağ erişim bayrağıyla** yapılır. 

Bu ilke yalnızca özel bağlantı bağlantılarına yönelik API erişimini kısıtlayabilmeniz için izin verir. Ortak ağ erişim bayrağı *devre dışı* olarak ayarlandığında, tüm REST API ortak buluttan Azure Digital TWINS örnek veri düzlemine çağrı yapılır `403, Unauthorized` . Alternatif olarak, ilke *devre dışı* olarak ayarlandığında ve bir istek özel bir uç nokta üzerinden YAPıLDıĞıNDA, API çağrısı başarılı olur.

Bu makalede, [Azure Portal](https://portal.azure.com)kullanarak ağ bayrağının değerini güncelleştirme gösterilmektedir. Azure CLı veya ARMClient komut aracı ile nasıl yapılacağı hakkında yönergeler için, bu makalenin [CLI sürümüne](how-to-enable-private-link-cli.md) bakın.

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

[Azure Portal](https://portal.azure.com)ortak ağ erişimini devre dışı bırakmak veya etkinleştirmek için, portalı açın ve Azure dijital TWINS örneğinize gidin.

1. Sol taraftaki menüde **ağ (Önizleme)** öğesini seçin.

1. **Genel erişim** sekmesinde, **devre dışı** veya **tüm ağlara** **genel ağ erişimine izin ver** seçeneğini belirleyin.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-private-link/network-flag-portal.png" alt-text="Bir Azure dijital TWINS örneği için ağ (Önizleme) sayfasını gösteren Azure portal ekran görüntüsü. Ortak erişim sekmesi vurgulanır ve genel ağ erişimine izin verilip verilmeyeceğini seçme seçeneği de vurgulanır." lightbox="media/how-to-enable-private-link/network-flag-portal.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

    **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Azure için özel bağlantı hakkında daha fazla bilgi edinin: 
* [*Azure özel bağlantı hizmeti nedir?*](../private-link/private-link-service-overview.md)