---
title: 'Öğretici: Azure Traffic Manager coğrafi trafik yönlendirmeyi yapılandırma'
description: Bu öğreticide Azure Traffic Manager kullanarak coğrafi trafik yönlendirme yönteminin nasıl yapılandırılacağı açıklanmaktadır.
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96188675"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Öğretici: Traffic Manager kullanarak coğrafi trafik yönlendirme yöntemini yapılandırma

Coğrafi trafik yönlendirme yöntemi, isteklerin gerçekleştiği coğrafi konuma göre trafiği belirli uç noktalara yönlendirmenizi sağlar. Bu öğreticide, bu yönlendirme yöntemiyle bir Traffic Manager profili oluşturma ve uç noktaları belirli coğrafi cihazlardan trafik alacak şekilde yapılandırma gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Coğrafi yönlendirmeye sahip bir Traffic Manager profili oluşturun.
> - Iç Içe geçmiş bir uç nokta yapılandırın.
> - Traffic Manager profilini kullanın.
> - Traffic Manager profilini silin.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma

1. Bir tarayıcıdan [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol tarafta **+ kaynak oluştur** ' u seçin. **Traffic Manager profili** araması yapın ve **Oluştur**' u seçin.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Traffic Manager profili oluşturma":::

1. *Traffic Manager profili oluştur* sayfasında, aşağıdaki ayarları tanımlayın:

    | Ayar         | Değer                                              |
    | ---             | ---                                                |
    | Ad            | Profiliniz için bir ad girin. Bu adın trafficmanager.net bölgesi içinde benzersiz olması gerekir. Traffic Manager profilinize erişmek için DNS adını kullanırsınız `<profilename>.trafficmanager.net` . |    
    | Yönlendirme yöntemi  | **Coğrafi** seçin. |
    | Abonelik    | Aboneliğinizi seçin. |
    | Kaynak grubu   | Bu profili yerleştirmek için mevcut bir kaynak grubunu kullanın veya yeni bir kaynak grubu oluşturun. Yeni bir kaynak grubu oluşturmayı seçerseniz, kaynak grubunun konumunu belirtmek için *kaynak grubu konumu* açılan listesini kullanın. Bu ayar, kaynak grubunun konumunu ifade eder ve genel olarak dağıtılan Traffic Manager profilini etkilemez. |

1. Traffic Manager profilinizi dağıtmak için **Oluştur** ' u seçin.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="Traffic Manager profili sayfası oluşturma":::

## <a name="add-endpoints"></a>Uç nokta Ekle

1. Listeden Traffic Manager profilini seçin.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Traffic Manager coğrafi liste":::

1. *Ayarlar* altındaki **uç noktaları** seçin ve yeni bir uç nokta eklemek için **+ Ekle** ' yi seçin.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="Uç nokta Ekle":::

1. Aşağıdaki ayarları seçin veya girin: 

    | Ayar                | Değer                                              |
    | ---                    | ---                                                |
    | Tür                   | Uç nokta türünü seçin. Üretimde kullanılan coğrafi yönlendirme profilleri için, birden fazla uç noktaya sahip bir alt profil içeren iç içe geçmiş uç nokta türlerini kullanmanızı önemle öneririz. Daha fazla bilgi için bkz. [coğrafi trafik yönlendirme yöntemleri hakkında SSS](traffic-manager-FAQs.md). |    
    | Name                   | Bu uç noktayı tanımlayacak bir ad verin. |
    | Hedef kaynak türü   | Hedef için kaynak türünü seçin. |
    | Hedef kaynak        | Listeden kaynağı seçin. |

    > [!Note]
    > Bu sayfadaki bazı alanlar, eklediğiniz uç noktanın türüne bağlıdır:
    > 1. Azure uç noktası ekliyorsanız, trafiği yönlendirmek istediğiniz kaynak temelinde **hedef kaynak türünü** ve **hedefi** seçin
    > 1. **Dış** uç nokta ekliyorsanız, uç noktanız için **tam etki alanı adını (FQDN)** sağlayın.
    > 1. **Iç Içe geçmiş bir uç nokta** ekliyorsanız, kullanmak istediğiniz alt profile karşılık gelen **hedef kaynağı** seçin ve **En düşük alt uç nokta sayısını** belirtin.

1. *Coğrafi eşleme* bölümünde, trafiğin bu uç noktaya gönderilmesini istediğiniz bölgeleri eklemek için açılan eklentiyi kullanın. En az bir bölge eklenmelidir. Birden fazla bölgenin eşleştirilmiş olması olabilir.

1. Bu profil altına eklemek istediğiniz tüm uç noktalar için son adımı tekrarlayın ve ardından **Kaydet**' i seçin.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Traffic Manager uç noktası ekleme":::

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager profilini kullanma

1.  Portalın arama çubuğunda, önceki bölümde oluşturduğunuz **Traffic Manager profili** adını arayın ve görüntülenen sonuçlarda Traffic Manager profili ' ni seçin.
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Traffic Manager profili ara":::

1. **Traffic Manager profili** penceresinde yeni oluşturduğunuz Traffic Manager profilinin DNS adı görüntülenir. Ad, yönlendirme türü tarafından belirlendiği şekilde doğru uç noktaya yönlendirilmek için herhangi bir istemci tarafından (örneğin, bir Web tarayıcısı kullanılarak gezinilirken) kullanılabilir. Coğrafi yönlendirme ile, Traffic Manager gelen isteğin kaynak IP 'sini arar ve kaynaklandığı bölgeyi belirler. Bu bölge bir uç noktayla eşlenmişse trafik buraya yönlendirilir. Bu bölge bir uç noktayla eşlenmemişse, Traffic Manager bir NODATA sorgu yanıtı döndürür.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="Coğrafi Traffic Manager genel bakış":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Traffic Manager profile artık ihtiyacınız yoksa, profili bulun ve **Profili Sil**' i seçin.

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Traffic Manager profilini Sil":::

## <a name="next-steps"></a>Sonraki adımlar

Coğrafi yönlendirme yöntemi hakkında daha fazla bilgi için bkz.:

> [!div class="nextstepaction"]
> [Coğrafi trafik yönlendirme yöntemi](traffic-manager-routing-methods.md#geographic)
