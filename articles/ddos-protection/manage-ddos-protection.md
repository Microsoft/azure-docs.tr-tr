---
title: Azure portal kullanarak Azure DDoS koruması standardını yönetme
description: Bir saldırıyı azaltmak için Azure DDoS koruma standardı 'nı kullanmayı öğrenin.
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: b38f2831136b0ccec4aa241135f2fd342c939845
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936895"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>Hızlı başlangıç: Azure DDoS koruma standardı oluşturma ve yapılandırma

Azure portal kullanarak Azure DDoS koruma standardı ile çalışmaya başlayın. 

DDoS koruma planı, aboneliklerde DDoS koruma standardı etkin olan bir sanal ağ kümesini tanımlar. Kuruluşunuz için bir DDoS koruma planı yapılandırabilir ve sanal ağları birden çok abonelikten aynı plana bağlayabilirsiniz. 

Bu hızlı başlangıçta bir DDoS koruma planı oluşturacak ve bunu bir sanal ağa bağlayacaksınız. 

## <a name="prerequisites"></a>Ön koşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.
- https://portal.azure.com adresinden Azure portalında oturum açın. Hesabınızın, [ağ katılımcısı](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) rolüne veya [izinleri](manage-permissions.md)hakkında nasıl yapılır kılavuzunda listelenen uygun eylemlere atanmış [özel bir role](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atandığından emin olun.

## <a name="create-a-ddos-protection-plan"></a>DDoS koruma planı oluşturma

1. Azure portal sol üst köşesinde **kaynak oluştur** ' u seçin.
2. *DDoS* terimini arayın. Ara sonuçlarda **DDoS koruma planı** göründüğünde, bunu seçin.
3. **Oluştur**’u seçin.
4. Aşağıdaki değerleri girin veya seçin ve ardından **Oluştur**' u seçin:

    |Ayar        |Değer                                              |
    |---------      |---------                                          |
    |Ad           | _Myddosprotectionplan_ yazın.                     |
    |Abonelik   | Aboneliğinizi seçin.                         |
    |Kaynak grubu | **Yeni oluştur** ' u seçin ve _myresourcegroup_ yazın.|
    |Konum       | _Doğu ABD_ girin.                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>Sanal ağ için DDoS korumasını etkinleştirme

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>Yeni bir sanal ağ için DDoS korumasını etkinleştir

1. Azure portal sol üst köşesinde **kaynak oluştur** ' u seçin.
2. **Ağ**’ı ve sonra **Sanal ağ**’ı seçin.
3. Aşağıdaki değerleri girin veya seçin, kalan Varsayılanları kabul edin ve **Oluştur**' u seçin:

    | Ayar         | Değer                                           |
    | ---------       | ---------                                       |
    | Ad            | _Myvnet_ girin.                                 |
    | Abonelik    | Aboneliğinizi seçin.                                    |
    | Kaynak grubu  | **Mevcut olanı kullan**' ı seçin ve ardından **myresourcegroup** öğesini seçin. |
    | Konum        | _Doğu ABD_ girin                                                    |
    | DDoS Koruması Standart | **Etkinleştir**’i seçin. Seçtiğiniz plan, sanal ağdan aynı veya farklı bir abonelikte olabilir, ancak her iki aboneliğin de aynı Azure Active Directory kiracısıyla ilişkilendirilmesi gerekir.|

Sanal ağ için DDoS standardı etkinleştirildiğinde bir sanal ağı başka bir kaynak grubuna veya aboneliğe taşıyamazsınız. DDoS standart özellikli bir sanal ağı taşımanız gerekiyorsa, önce DDoS standardını devre dışı bırakın, sanal ağı taşıyın ve ardından DDoS standardını etkinleştirin. Taşıma işleminden sonra, sanal ağ içindeki tüm korumalı genel IP adresleri için otomatik olarak ayarlanan ilke eşikleri sıfırlanır.

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>Mevcut bir sanal ağ için DDoS korumasını etkinleştir

1. DDoS koruma planı [oluşturma](#create-a-ddos-protection-plan)bölümündeki adımları tamamlayarak bir DDoS koruma planı oluşturun.
2. Azure portal en üstündeki **kaynakları, hizmetleri ve belgeleri ara kutusuna** Için DDoS koruması standardını etkinleştirmek istediğiniz sanal ağ adını girin. Arama sonuçlarında sanal ağın adı göründüğünde seçin.
3. **Ayarlar** altında **DDoS koruması**' nı seçin.
4. **Standart**' ı seçin. **DDoS koruma planı** altında var olan bir DDoS koruma planı veya 1. adımda oluşturduğunuz planı seçin ve ardından **Kaydet**' i seçin. Seçtiğiniz plan, sanal ağdan aynı veya farklı bir abonelikte olabilir, ancak her iki aboneliğin de aynı Azure Active Directory kiracısıyla ilişkilendirilmesi gerekir.

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>Tüm sanal ağlar için DDoS korumasını etkinleştir

Bu [ilke](https://aka.ms/ddosvnetpolicy) , DDoS koruma standardı etkin olmayan tanımlanmış bir kapsamdaki tüm sanal ağları algılayacak ve isteğe bağlı olarak VNET 'i korumak için ilişkilendirmeyi oluşturacak bir düzeltme görevi oluşturmaya çalışır. Bu ilkeyi dağıtma hakkında ayrıntılı adım adım yönergeler için bkz https://aka.ms/ddosvnetpolicy-techcommunity ..

## <a name="validate-and-test"></a>Doğrula ve test et

İlk olarak, DDoS koruma planınızın ayrıntılarını kontrol edin:

1. Portalın sol üst kısmında **Tüm hizmetler**'i seçin.
2. **Filtre** kutusuna *DDoS* girin. **DDoS koruma planları** sonuçlarda görüntülendiğinde, bunu seçin.
3. Listeden DDoS koruması planınızı seçin.

_Myvnet_ sanal ağının listelenmesi gerekir. 

### <a name="view-protected-resources"></a>Korumalı kaynakları görüntüleme
**Korunan kaynaklar** altında, korumalı sanal ağlarınızı ve genel IP adreslerini görüntüleyebilir veya DDoS koruma planınıza daha fazla sanal ağ ekleyebilirsiniz:

![Korumalı kaynakları görüntüleme](./media/manage-ddos-protection/ddos-protected-resources.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynaklarınızın bir sonraki öğreticide kalmasını sağlayabilirsiniz. Artık gerekmiyorsa, _Myresourcegroup_ kaynak grubunu silin. Kaynak grubunu sildiğinizde, DDoS koruma planını ve ilgili tüm kaynakları da silersiniz. Bu DDoS koruma planını kullanmayı düşünmüyorsanız gereksiz ücretlerden kaçınmak için kaynakları kaldırmanız gerekir.

   >[!WARNING]
   >Bu eyleme geri döndürülemez.

1. Azure portal, **kaynak gruplarını** arayıp seçin ya da Azure Portal menüsünden **kaynak grupları** ' nı seçin.

2. _Myresourcegroup_ kaynak grubunu bulmak için filtre uygulayın veya aşağı kaydırın.

3. Kaynak grubunu seçin ve **kaynak grubunu sil**' i seçin.

4. Doğrulanacak kaynak grubu adını yazın ve ardından **Sil**' i seçin.

Bir sanal ağ için DDoS korumasını devre dışı bırakmak için: 

1. Portalın üst kısmındaki **kaynakları, hizmetleri ve belgeleri arayın kutusuna** Için DDoS koruması standardını devre dışı bırakmak istediğiniz sanal ağın adını girin. Arama sonuçlarında sanal ağın adı göründüğünde seçin.
2. **DDoS koruma standardı** altında **devre dışı bırak**' ı seçin.

DDoS koruma planını silmek istiyorsanız, önce tüm sanal ağların ilişkilendirmesini kaldırmanız gerekir. 

## <a name="next-steps"></a>Sonraki adımlar

DDoS koruma planınız için telemetri görüntüleme ve yapılandırma hakkında bilgi edinmek için öğreticilere devam edin.

> [!div class="nextstepaction"]
> [DDoS konuma telemetrisini görüntüleme ve yapılandırma](telemetry.md)
