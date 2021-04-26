---
title: 'Öğretici: Azure Traffic Manager öncelikli trafik yönlendirmeyi yapılandırma'
description: Bu öğreticide, Traffic Manager ' de öncelik trafiği yönlendirme yönteminin nasıl yapılandırılacağı açıklanmaktadır
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1835377f4690097c8390957bf7d897242ba7aace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92208065"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Öğretici: Traffic Manager 'de öncelik trafiği yönlendirme yöntemini yapılandırma

Bu öğreticide, öncelik yönlendirme yöntemi kullanılarak kullanıcı trafiğini belirli uç noktalara yönlendirmek için Azure Traffic Manager nasıl kullanılacağı açıklanmaktadır. Bu yönlendirme yönteminde, Traffic Manager profil yapılandırmasına giden her bir uç noktanın sırasını tanımlayacaksınız. Kullanıcılardan gelen trafik, listelendikleri sırada uç noktaya yönlendirilir. Bu yönlendirme yöntemi, hizmet yük devretmesi için yapılandırmak istediğinizde yararlıdır. Birincil uç nokta, ' 1 ' öncelik sayısını alır ve tüm gelen isteklere hizmet eder. Düşük öncelikli uç noktalar yedekleme görevi görür.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> - Öncelik yönlendirmeye sahip bir Traffic Manager profili oluşturun.
> - Uç nokta ekleyin.
> - Uç noktaların önceliğini yapılandırın.
> - Traffic Manager profilini kullanın.
> - Traffic Manager profilini silin.

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="to-configure-the-priority-traffic-routing-method"></a>Öncelik trafiği yönlendirme yöntemini yapılandırmak için
1. Bir tarayıcıdan [Azure portalında](https://portal.azure.com) oturum açın.

1. Sol tarafta **+ kaynak oluştur** ' u seçin. **Traffic Manager profili** araması yapın ve **Oluştur**' u seçin.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="Traffic Manager öncelik profili oluşturma":::

1. *Traffic Manager profili oluştur* sayfasında, aşağıdaki ayarları tanımlayın:

    | Ayar         | Değer                                              |
    | ---             | ---                                                |
    | Ad            | Profiliniz için bir ad girin. Bu adın trafficmanager.net bölgesi içinde benzersiz olması gerekir. Traffic Manager profilinize erişmek için DNS adını kullanırsınız `<profilename>.trafficmanager.net` . |    
    | Yönlendirme yöntemi  | **Öncelik**' i seçin. |
    | Abonelik    | Aboneliğinizi seçin. |
    | Kaynak grubu   | Bu profili yerleştirmek için mevcut bir kaynak grubunu kullanın veya yeni bir kaynak grubu oluşturun. Yeni bir kaynak grubu oluşturmayı seçerseniz, kaynak grubunun konumunu belirtmek için *kaynak grubu konumu* açılan listesini kullanın. Bu ayar, kaynak grubunun konumunu ifade eder ve genel olarak dağıtılan Traffic Manager profilini etkilemez. |

1. Traffic Manager profilinizi dağıtmak için **Oluştur** ' u seçin.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="Traffic Manager profili önceliği oluşturma":::

## <a name="add-endpoints"></a>Uç nokta Ekle

1. Listeden Traffic Manager profilini seçin.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="Traffic Manager profili listesi":::

1. *Ayarlar* altındaki **uç noktaları** seçin ve yeni bir uç nokta eklemek için **+ Ekle** ' yi seçin.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="Uç nokta eklemek Traffic Manager":::

1. Aşağıdaki ayarları seçin veya girin: 

    | Ayar                | Değer                                              |
    | ---                    | ---                                                |
    | Tür                   | Uç nokta türünü seçin. |    
    | Name                   | Bu uç noktayı tanımlayacak bir ad verin. |
    | Hedef kaynak türü   | Hedef için kaynak türünü seçin. |
    | Hedef kaynak        | Listeden kaynağı seçin. |
    | Öncelik               | Bu uç nokta için bir öncelik numarası verin. 1 en yüksek önceliktir. |


1. Uç noktayı eklemek için **Ekle** ' yi seçin. Ek uç noktaları eklemek için adım 2 ve 3 ' ü tekrarlayın. Uygun öncelik numarasını ayarlamayı unutmayın.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="Öncelik 1 uç noktası ekle":::

1. **Uç noktalar** sayfasında, uç noktalarınız için öncelik sırasını gözden geçirin. **Öncelik** trafiği yönlendirme yöntemini seçtiğinizde, seçilen uç noktaların sırası önemlidir. Uç noktaların öncelik sırasını doğrulayın.  Birincil uç nokta en üstte. Görüntülenme sırasını iki kez kontrol edin. Tüm istekler ilk uç noktaya yönlendirilir ve Traffic Manager sağlıksız olduğunu algılarsa trafik otomatik olarak sonraki uç noktaya devreder. 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="Öncelik uç noktaları listesi":::

1. Uç nokta öncelik sırasını değiştirmek için uç noktayı seçin, öncelik değerini değiştirin ve uç nokta ayarlarını kaydetmek için **Kaydet** ' i seçin.

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager profilini kullanma

1.  Portalın arama çubuğunda, önceki bölümde oluşturduğunuz **Traffic Manager profili** adını arayın ve görüntülenen sonuçlarda Traffic Manager profili ' ni seçin.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="Traffic Manager profili ara":::

1.  **Traffic Manager profile** genel bakış sayfası, yeni oluşturduğunuz TRAFFIC Manager profilinizin DNS adını görüntüler. Bu, yönlendirme türü tarafından belirlendiği şekilde doğru uç noktaya yönlendirilmek için herhangi bir istemci tarafından (örneğin, bir Web tarayıcısı kullanılarak gezinilirken) kullanılabilir. Bu durumda, tüm istekler ilk uç noktaya yönlendirilir ve Traffic Manager sağlıksız olduğunu algılarsa trafik otomatik olarak sonraki uç noktaya devreder.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="Traffic Manager DNS adı":::

1. Traffic Manager profiliniz çalışmaya başladıktan sonra, yetkili DNS sunucunuzdaki DNS kaydını düzenleyerek şirket etki alanı adınızı Traffic Manager etki alanı adına getirin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Traffic Manager profile artık ihtiyacınız yoksa, profili bulun ve **Profili Sil**' i seçin.

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="Traffic Manager öncelik profilini Sil":::

## <a name="next-steps"></a>Sonraki adımlar

Öncelik yönlendirme yöntemi hakkında daha fazla bilgi için bkz.:

> [!div class="nextstepaction"]
> [Öncelik yönlendirme yöntemi](traffic-manager-routing-methods.md#priority-traffic-routing-method)
