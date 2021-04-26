---
title: Eğitim-şirket içi ortamlarından özel bir buluta
description: Azure VMware çözümünde özel buluta ExpressRoute Global Reach eşlemesi oluşturmayı öğrenin.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 798b822989127ccbb00e971de2cc4147ac234259
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449571"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Öğretici: şirket içi ortamlar için özel bir buluta

ExpressRoute Global Reach şirket içi ortamınızı Azure VMware çözümünüz özel bulutuna bağlar. ExpressRoute Global Reach bağlantısı, özel bulut ExpressRoute devresi ve şirket içi ortamlarınızla mevcut bir ExpressRoute bağlantısı arasında oluşturulur. 

[Azure 'Da VMware özel bulutunuz için ağı yapılandırırken](tutorial-configure-networking.md) kullandığınız ExpressRoute bağlantı hattı, yetkilendirme anahtarları oluşturmanızı ve kullanmanızı gerektirir.  ExpressRoute bağlantı hattı tarafından zaten bir yetkilendirme anahtarı kullandınız ve bu öğreticide, şirket içi ExpressRoute devrenizi ile eş için ikinci bir yetkilendirme anahtarı oluşturacaksınız.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Özel bulut ExpressRoute devresi için, _devre 2_ için ikinci bir yetkilendirme anahtarı oluşturun.
> * Şirket içinden özel bulut ExpressRoute Global Reach eşlemesini etkinleştirmek için, Azure portal veya _devre_ dışı bırakmak için Cloud Shell bir YÖNTEMDE Azure CLI kullanın.


## <a name="before-you-begin"></a>Başlamadan önce

ExpressRoute Global Reach kullanarak iki ExpressRoute bağlantı hattı arasında bağlantıyı etkinleştirmeden önce, [farklı Azure aboneliklerinde bağlantının nasıl etkinleştirileceği](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)hakkındaki belgeleri gözden geçirin.  

## <a name="prerequisites"></a>Önkoşullar

- Azure VMware Çözüm özel bulutuyla, ExpressRoute bağlantı hattı, bir Azure sanal ağı 'nda (VNet) bir ExpressRoute bağlantı hattı ile, eşleme yordamlarından itibaren devre 2 olan bağlantı kurdu.
- Şirket içi ortamları Azure 'a bağlamak için kullanılan, eşleme yordamlarının perspektifinden devre dışı olan ayrı, çalışan bir ExpressRoute devresi.
- ExpressRoute Global Reach eşlemesi için/29 çakışmayan bir [ağ adresi bloğu](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) .
- ExpressRoute sağlayıcısının hizmeti de dahil olmak üzere tüm ağ geçitlerinin 4 baytlık otonom sistem numarası 'nı (ASN) desteklemesini sağlayın. Azure VMware çözümü, reklam rotaları için 4 baytlık genel ASNs kullanır.

>[!IMPORTANT]
>Bu önkoşulların bağlamında, şirket içi ExpressRoute bağlantı hattı devre _1_' dir ve özel bulut ExpressRoute devreniz farklı bir abonelikte ve _devre 2_' etiketlidir.

## <a name="create-an-expressroute-authorization-key-in-the-private-cloud-expressroute-circuit"></a>Özel bulut ExpressRoute devresinde bir ExpressRoute yetkilendirme anahtarı oluşturma

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>Yetkilendirme anahtarıyla eşler arası özel bulut şirket içi
Özel bulut ExpressRoute bağlantı hattı için bir yetkilendirme anahtarı oluşturduğunuza göre, bunu şirket içi ExpressRoute bağlantı hattınızla birlikte kullanabilirsiniz. Eşleme, **Azure Portal** şirket Içi ExpressRoute bağlantı hattı perspektifinden ya da **Azure clı 'yi bir Cloud Shell** kullanılarak yapılır. Her iki yöntemde de eşlemeyi tamamlaması için özel bulut ExpressRoute bağlantı hattının kaynak KIMLIĞI ve yetkilendirme anahtarını kullanırsınız.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Şirket içi ExpressRoute devresi ile aynı aboneliği kullanarak [Azure Portal](https://portal.azure.com) oturum açın.

1. Özel buluta **genel bakış**' dan Yönet ' in altında **bağlantı**  >  **ExpressRoute Global Reach**  >  **Ekle**' yi seçin.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Azure VMware Çözüm özel bulutu 'nda ExpressRoute Global Reach sekmesini gösteren ekran görüntüsü.":::

1. Şirket içi bulut bağlantısı oluşturun. Aşağıdakilerden birini yapın ve ardından **Oluştur**' u seçin:

   - Listeden **ExpressRoute devresini** seçin veya
   - Devre KIMLIĞINIZ varsa, alana yapıştırın ve yeni oluşturduğunuz yetkilendirme anahtarını sağlayın.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="ExpressRoute KIMLIĞI ve yetkilendirme anahtarını girip oluştur ' u seçin.":::   
   
   Yeni bağlantı, şirket içi bulut bağlantıları listesinde gösterilir.

>[!TIP]
>**Daha fazla** seçim yaparak bir bağlantıyı silebilir veya listeden bağlantısını kesebilirsiniz.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Şirket içi bağlantının bağlantısını kesme veya silme":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[CLI komutlarını](../expressroute/expressroute-howto-set-global-reach-cli.md) , şirket içi ortamlar arasında Azure VMware Çözüm özel bulutuna yönelik expressroute Global Reach eşlemeyi yapılandırmanıza yardımcı olacak belirli Ayrıntılar ve örneklerle genişlettik.

>[!TIP]
>Azure CLı komut çıkışında bu yönergeler, yalnızca gerekli sonuçları göstermek için bir JMESPath sorgusu yürütmek üzere bir [ `–query` bağımsız değişken](/cli/azure/query-azure-cli) kullanabilir.

1. Şirket içi ExpressRoute devresi ile aynı aboneliği kullanarak [Azure Portal](https://portal.azure.com) oturum açın. 

1. Bir Cloud Shell açın ve kabuğu Bash olarak bırakın.

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Azure portal Cloud Shell gösteren ekran görüntüsü.":::

1. Devre 2 ' nin kaynak KIMLIĞI ve yetkilendirme anahtarı ' na geçerek devre 1 ' de eşleme oluşturun. 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Devre 1 ile devre 2 arasındaki başarılı bir eşlemenin sonucunu ve komutunu gösteren ekran görüntüsü.":::

Şirket içi ortamlarından ExpressRoute Global Reach eşlemesi üzerinden özel bulutunuzun bağlantısını yapabilirsiniz.

>[!TIP]
>[Şirket içi ağlarınızla ilgili yönergeler arasında bağlantıyı devre dışı bırak bağlantısını](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) izleyerek eşlemeyi silebilirsiniz.


---

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şirket içi-özel Cloud ExpressRoute Global Reach eşlemeyi nasıl etkinleştireceğinizi öğrendiniz. 

Azure VMware çözümünüz özel bulutunuz için VMware HCX çözümünü dağıtma ve yapılandırma hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [VMware HCX’i dağıtma ve yapılandırma](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
