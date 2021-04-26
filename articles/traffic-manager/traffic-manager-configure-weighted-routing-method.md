---
title: 'Öğretici: Azure Traffic Manager ağırlıklı hepsini bir kez deneme trafik yönlendirmeyi yapılandırma'
description: Bu öğreticide, Traffic Manager ' de hepsini bir kez deneme yöntemi kullanarak trafiğin yükünü dengelemek açıklanmaktadır
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207848"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Öğretici: Traffic Manager ' de ağırlıklı trafik yönlendirme yöntemini yapılandırma

Ortak trafik yönlendirme yöntemi, bulut hizmetleri ve Web siteleri dahil olmak üzere aynı uç noktalar kümesi sağlamaktır ve her eşit trafiği eşit bir şekilde gönderir. Aşağıdaki adımlarda, bu tür trafik yönlendirme yönteminin nasıl yapılandırılacağı ana hatlarıyla gösterilmiştir.

> [!NOTE]
> Azure Web App, bir Azure bölgesindeki (birden çok veri merkezini kapsayan) Web siteleri için hepsini bir kez deneme Yük Dengeleme işlevi sunmaktadır. Traffic Manager, farklı veri merkezlerinde Web siteleri arasında trafik dağıtmanıza izin verir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Ağırlıklı yönlendirmeye sahip bir Traffic Manager profili oluşturun.
> - Traffic Manager profilini kullanın.
> - Traffic Manager profilini silin.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="configure-the-weighted-traffic-routing-method"></a>Ağırlıklı trafik yönlendirme yöntemini yapılandırma

1. Bir tarayıcıdan [Azure portalında](https://portal.azure.com) oturum açın.

1. Portalın arama çubuğunda, önceki bölümde oluşturduğunuz **Traffic Manager profili** adını arayın ve görüntülenen sonuçlarda Traffic Manager profili ' ni seçin.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="Traffic Manager profili arama":::

1. **Yapılandırma** ' yı seçin ve aşağıdaki ayarları seçin veya girin:

    | Ayar         | Değer                                              |
    | ---             | ---                                                |
    | Yönlendirme yöntemi            | **Ağırlıklı**' ı seçin. |    
    | DNS yaşam süresi (TTL) | Bu değer, istemcinin yerel önbelleğe alma adı sunucusunun, güncelleştirilmiş DNS girdileri için Traffic Manager sistemini ne sıklıkta sorgulayacağı hakkında denetler. Trafik yönlendirme yöntemi değişiklikleri veya eklenen uç noktaların kullanılabilirliğine ilişkin değişiklikler gibi Traffic Manager ile gerçekleşen herhangi bir değişiklik, DNS sunucularının genel sistemi boyunca yenilenmek üzere bu süre kadar sürer. |
    | Protokol    | Uç nokta izleme için bir protokol seçin. *Seçenekler: HTTP, HTTPS ve TCP* |
    | Bağlantı noktası | Bağlantı noktası numarasını belirtin. |
    | Yol | Uç noktaları izlemek için bir yol ve dosya adı belirtmeniz gerekir. Eğik çizgi "/", göreli yol için geçerli bir giriştir ve dosyanın kök dizinde (varsayılan) olduğunu gösterir. |
    | Özel üstbilgi ayarları | Ana bilgisayar:contoso. com, newheader: NewValue biçimindeki özel üst bilgileri yapılandırın. Desteklenen en fazla çift 8 ' dir. Http ve https Protokolü için geçerlidir. Profildeki tüm uç noktalar için geçerlidir |
    | Beklenen durum kodu aralıkları (varsayılan: 200) | Durum kodu aralıklarını 200-299301-301 biçiminde yapılandırın. Desteklenen Aralık üst sınırı 8 ' dir. Http ve https Protokolü için geçerlidir. Profildeki tüm uç noktalar için geçerlidir |
    | Yoklama aralığı | Uç nokta sistem durumu araştırmaları arasındaki zaman aralığını yapılandırın. 10 veya 30 saniye seçeneğini belirleyebilirsiniz. |
    | Başarısızlık sayısı | Bir uç nokta hatası tetiklenmeden önce toleranslı durum araştırma hatası sayısını yapılandırın. 0 ile 9 arasında bir sayı girebilirsiniz. | 
    | Araştırma zaman aşımı | Uç nokta sistem durumu araştırması zaman aşımına uğramadan önce gereken süreyi yapılandırın. Bu değer en az 5 ve yoklama aralığı değerinden küçük olmalıdır. |

1. Yapılandırmayı gerçekleştirmek için **Kaydet** ' i seçin.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="Traffic Manager ağırlıklı yapılandırma"::: 

1. **Uç nokta** ' ı seçin ve her bitiş noktasının ağırlığını yapılandırın. Ağırlık 1-1000 arasında olabilir. Ağırlığa göre daha yüksek olan öncelik o kadar yüksektir.  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="Traffic Manager ağırlıklı uç noktalar yapılandırması"::: 

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager profilini kullanma

**Traffic Manager profili** penceresinde yeni oluşturduğunuz Traffic Manager profilinin DNS adı görüntülenir. Ad, yönlendirme türü tarafından belirlendiği şekilde doğru uç noktaya yönlendirilmek için herhangi bir istemci tarafından (örneğin, bir Web tarayıcısı kullanılarak gezinilirken) kullanılabilir. Bu durumda, tüm istekler her bitiş noktasını hepsini bir kez kez yönlendirilir.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="Traffic Manager ağırlıklı genel bakış"::: 

## <a name="clean-up-resources"></a>Kaynakları temizleme

Traffic Manager profile artık ihtiyacınız yoksa, profili bulun ve **Profili Sil**' i seçin.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="Traffic Manager ağırlıklı profili sil":::

## <a name="next-steps"></a>Sonraki adımlar

Ağırlıklı yönlendirme yöntemi hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Ağırlıklı trafik yönlendirme yöntemi](traffic-manager-routing-methods.md#weighted)