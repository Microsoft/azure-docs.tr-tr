---
title: Coğrafi trafik yönlendirmeyi Yapılandırma-Azure Traffic Manager
description: Bu makalede, Azure Traffic Manager kullanarak coğrafi trafik yönlendirme yönteminin nasıl yapılandırılacağı açıklanmaktadır.
services: traffic-manager
author: asudbring
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: allensu
ms.openlocfilehash: f15871705b9839f1c7a7c7f04f6f4a88641673fd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031960"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Traffic Manager kullanarak coğrafi trafik yönlendirme yöntemini yapılandırma

Coğrafi trafik yönlendirme yöntemi, isteklerin gerçekleştiği coğrafi konuma göre trafiği belirli uç noktalara yönlendirmenizi sağlar. Bu öğreticide, bu yönlendirme yöntemiyle bir Traffic Manager profili oluşturma ve uç noktaları belirli coğrafi cihazlardan trafik alacak şekilde yapılandırma gösterilmektedir.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager profili oluşturma

1. Bir tarayıcıdan [Azure portalında](https://portal.azure.com) oturum açın. Henüz bir hesabınız yoksa, [bir aylık ücretsiz denemeye](https://azure.microsoft.com/free/) kaydolabilirsiniz.
2. **Kaynak oluştur** > **Ağ** > **Traffic Manager profili** > **Oluştur** seçeneğine tıklayın.
4. **Traffic Manager oluşturma profilinde**:
    1. Profiliniz için bir ad girin. Bu adın trafficmanager.net bölgesi içinde benzersiz olması gerekir. Traffic Manager profilinize erişmek için `<profilename>.trafficmanager.net`DNS adını kullanırsınız.
    2. **Coğrafi** yönlendirme yöntemini seçin.
    3. Bu profili oluşturmak istediğiniz aboneliği seçin.
    4. Bu profili yerleştirmek için mevcut bir kaynak grubunu kullanın veya yeni bir kaynak grubu oluşturun. Yeni bir kaynak grubu oluşturmayı seçerseniz, kaynak grubunun konumunu belirtmek için **kaynak grubu konumu** açılan listesini kullanın. Bu ayar, kaynak grubunun konumunu ifade eder ve genel olarak dağıtılan Traffic Manager profilini etkilemez.
    5. **Oluştur**' a tıkladıktan sonra Traffic Manager profiliniz Global olarak oluşturulur ve dağıtılır.

![Traffic Manager profili oluşturma](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Uç nokta Ekle

1. Portalın arama çubuğunda oluşturduğunuz Traffic Manager profili adını arayın ve gösterilen sonuca tıklayın.
2. Traffic Manager -> **Ayarlar** **uç noktalarına** gidin.
3. Ekleme **uç noktasını**göstermek için **Ekle** ' ye tıklayın.
3. **Ekle** ' ye tıklayın ve görüntülenen **uç nokta Ekle** ' ye tıkladıktan sonra aşağıdaki gibi tamamlanır:
4. Eklediğiniz uç noktanın türüne bağlı olarak **tür** ' ı seçin. Üretimde kullanılan coğrafi yönlendirme profilleri için, birden fazla uç noktaya sahip bir alt profil içeren iç içe geçmiş uç nokta türlerini kullanmanızı önemle öneririz. Daha fazla ayrıntı için bkz. [coğrafi trafik yönlendirme yöntemleri hakkında SSS](traffic-manager-FAQs.md).
5. Bu uç noktayı tanımak istediğiniz bir **Ad** belirtin.
6. Bu sayfadaki bazı alanlar, eklediğiniz uç noktanın türüne bağlıdır:
    1. Azure uç noktası ekliyorsanız, trafiği yönlendirmek istediğiniz kaynak temelinde **hedef kaynak türünü** ve **hedefi** seçin
    2. **Dış** uç nokta ekliyorsanız, uç noktanız için **tam etki alanı adını (FQDN)** sağlayın.
    3. **Iç Içe geçmiş bir uç nokta**ekliyorsanız, kullanmak istediğiniz alt profile karşılık gelen **hedef kaynağı** seçin ve **En düşük alt uç nokta sayısını**belirtin.
7. Coğrafi eşleme bölümünde, trafiğin bu uç noktaya gönderilmesini istediğiniz bölgeleri eklemek için açılan liste ' yi kullanın. En az bir bölge eklemeniz ve birden fazla bölgenin eşleştirilmiş olması gerekir.
8. Bu profil altına eklemek istediğiniz tüm uç noktalar için bunu tekrarlayın

![Traffic Manager uç noktası ekleme](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Traffic Manager profilini kullanma
1.  Portalın arama çubuğunda, önceki bölümde oluşturduğunuz **Traffic Manager profili** adını arayın ve görüntülenen sonuçlarda Traffic Manager profiline tıklayın.
2. **Genel Bakış**'a tıklayın.
3. **Traffic Manager profili** penceresinde yeni oluşturduğunuz Traffic Manager profilinin DNS adı görüntülenir. Bu, yönlendirme türü tarafından belirlendiği şekilde doğru uç noktaya yönlendirilmek için herhangi bir istemci tarafından (örneğin, bir Web tarayıcısı kullanılarak gezinilirken) kullanılabilir.  Coğrafi yönlendirme söz konusu olduğunda, Traffic Manager gelen isteğin kaynak IP 'sini arar ve kaynaklandığı bölgeyi belirler. Bu bölge bir uç noktayla eşlenmişse trafik buraya yönlendirilir. Bu bölge bir uç noktayla eşlenmişse, Traffic Manager bir NODATA sorgu yanıtı döndürür.

## <a name="next-steps"></a>Sonraki adımlar

- [Coğrafi trafik yönlendirme yöntemi](traffic-manager-routing-methods.md#geographic)hakkında daha fazla bilgi edinin.
- [Traffic Manager ayarlarını test](traffic-manager-testing-settings.md)etme hakkında bilgi edinin.
