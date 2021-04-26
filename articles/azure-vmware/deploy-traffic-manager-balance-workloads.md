---
title: Azure VMware Çözüm iş yüklerini dengelemek için Traffic Manager dağıtma
description: Farklı bölgelerde birden fazla uç nokta arasında uygulama iş yüklerini dengelemek için Traffic Manager Azure VMware çözümü ile tümleştirmeyi öğrenin.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 029bb9512bd19effd1c7aeb5104c7bb6d7ccdca5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876931"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Azure VMware Çözüm iş yüklerini dengelemek için Traffic Manager dağıtma

Bu makalede Azure [Traffic Manager](../traffic-manager/traffic-manager-overview.md) Azure VMware çözümüyle tümleştirme adımlarında izlenecek yol gösterilmektedir. Tümleştirme, uygulama iş yüklerini birden çok uç nokta genelinde dengeler. Bu makalede ayrıca, çeşitli Azure VMware Çözüm bölgelerini kapsayan üç [azure Application Gateway](../application-gateway/overview.md) arasında trafiği yönlendirmek için Traffic Manager yapılandırma adımları gösterilmektedir. 

Ağ geçitlerinde, gelen katman 7 isteklerinin yükünü dengelemek için arka uç havuzu üyeleri olarak yapılandırılmış Azure VMware Çözüm sanal makineleri (VM 'Ler) vardır. Daha fazla bilgi için bkz. Azure [Application Gateway kullanarak Web uygulamalarınızı Azure VMware çözümünde koruma](protect-azure-vmware-solution-with-application-gateway.md)

Diyagramda, bölgesel uç noktalar arasındaki DNS düzeyindeki uygulamalar için Traffic Manager yük dengelemenin nasıl sağladığı gösterilmektedir. Ağ geçitlerinde IIS sunucusu olarak yapılandırılmış ve Azure VMware Solution dış uç noktaları olarak başvurulan arka uç havuzu üyeleri vardır. İki özel bulut bölgesi arasında sanal ağ bağlantısı, bir ExpressRoute ağ geçidi kullanır.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Azure VMware çözümüyle Traffic Manager tümleştirmesinin mimari diyagramı" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Başlamadan önce, önce [önkoşulları](#prerequisites) gözden geçirin ve ardından şu işlemleri yapmak için yordamları izlenecek:

> [!div class="checklist"]
> * Uygulama ağ geçitlerinizin ve NSX-T segmentinin yapılandırmasını doğrulayın
> * Traffic Manager profilinizi oluşturma
> * Traffic Manager profilinize dış uç noktalar ekleyin

## <a name="prerequisites"></a>Önkoşullar

- Farklı Azure VMware Çözüm bölgelerinde çalışan Microsoft IIS sunucuları olarak yapılandırılmış üç VM: 
   - Batı ABD
   - West Europe
   - Doğu ABD (Şirket içi) 

- Yukarıda bahsedilen Azure VMware Çözüm bölgelerinde dış uç noktalara sahip bir uygulama ağ geçidi.

- Doğrulama için internet bağlantısı olan ana bilgisayar. 

- [Azure VMware çözümünde oluşturulmuş bir NSX-T ağ kesimi](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Uygulama ağ geçitleri yapılandırmanızı doğrulama

Aşağıdaki adımlar uygulama ağ geçitlerinizin yapılandırmasını doğrular.

1. Azure portal, geçerli uygulama ağ geçitlerinizin listesini görüntülemek için **uygulama ağ geçitleri** ' ni seçin:

   - AVS-GW-WUS
   - AVS-EUS (Şirket içi)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Yapılandırılmış uygulama ağ geçitlerinin listesini gösteren uygulama ağ geçidi sayfasının ekran görüntüsü." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Önceden dağıtılan uygulama ağ geçitlerinden birini seçin. 

   Uygulama ağ geçidinde çeşitli bilgileri gösteren bir pencere açılır. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Seçili uygulama ağ geçidinin ayrıntılarını gösteren uygulama ağ geçidi sayfasının ekran görüntüsü." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Arka uç havuzlarından birinin yapılandırmasını doğrulamak için **arka uç havuzlarını** seçin. 172.29.1.10 IP adresine sahip bir Web sunucusu olarak yapılandırılmış bir VM arka uç havuzu üyesini görürsünüz.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Hedef IP adresi vurgulanmış şekilde arka uç havuzunu düzenleme sayfasının ekran görüntüsü.":::

1. Diğer uygulama ağ geçitlerinin ve arka uç havuz üyelerinin yapılandırmasını doğrulayın. 

## <a name="verify-the-nsx-t-segment-configuration"></a>NSX-T segmenti yapılandırmasını doğrulama

Aşağıdaki adımlar, Azure VMware Çözüm ortamındaki NSX-T segmentinin yapılandırmasını doğrular.

1. Yapılandırılmış segmentlerinizi görüntülemek için **segmentleri** seçin.  Contoso-SEGMENT1, 1. katman esnek yönlendirici olan contoso-T01 Gateway 'e bağlı olduğunu görürsünüz.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="NSX-T Manager 'daki segment profillerini gösteren ekran görüntüsü." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Katman 1 ağ geçitlerinin bir listesini, bağlantılı parçaların sayısıyla birlikte görmek için **Katman 1 ağ geçitleri** ' ni seçin. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Seçilen segmentin ağ geçidi adresini gösteren ekran görüntüsü.":::    

1. Contoso-T01 ile bağlantılı segmenti seçin. Katman-01 yönlendiricisinde yapılandırılan mantıksal arabirimi gösteren bir pencere açılır. Kesime bağlı arka uç havuzu üye sanal makinesine bir ağ geçidi görevi görür.

1. VSphere istemcisinde, ayrıntılarını görüntülemek için VM 'yi seçin. 

   >[!NOTE]
   >IP adresi önceki bölümden bir Web sunucusu olarak yapılandırılmış VM arka uç havuzu üyesiyle eşleşiyor: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="VSphere Istemcisinde VM ayrıntılarını gösteren ekran görüntüsü." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. VM 'yi seçin ve ardından NSX-T segmentiyle bağlantıyı doğrulamak için **eylemler > Ayarları Düzenle** ' yi seçin.

## <a name="create-your-traffic-manager-profile"></a>Traffic Manager profilinizi oluşturma

1. [Azure portalında](https://rc.portal.azure.com/#home) oturum açın. **Azure hizmetleri > ağ** altında **Traffic Manager profiller**' i seçin.

2. Yeni bir Traffic Manager profili oluşturmak için **+ Ekle** ' yi seçin.
 
3. Aşağıdaki bilgileri girip **Oluştur**' u seçin:

   - Profil adı
   - Yönlendirme yöntemi ( [ağırlıklı](../traffic-manager/traffic-manager-routing-methods.md) kullanın
   - Abonelik
   - Kaynak grubu

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Traffic Manager profiline dış uç noktalar ekleme

1. Arama sonuçları bölmesinden Traffic Manager profilini seçin, **uç noktalar**' ı ve sonra **+ Ekle**' yi seçin.

1. Farklı bölgelerdeki dış uç noktaların her biri için gerekli ayrıntıları girin ve ardından **Ekle**' yi seçin: 
   - Tür
   - Name
   - Tam etki alanı adı (FQDN) veya IP
   - Ağırlık (her bitiş noktası için 1 ağırlığı atayın). 

   Oluşturulduktan sonra, Traffic Manager profilinde üçü de görüntülenir. Üçünün izleme durumu **çevrimiçi** olmalıdır.

3. **Genel bakış** ' ı seçin ve **DNS adı** altında URL 'yi kopyalayın.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="DNS adı vurgulanmış bir Traffic Manager uç noktaya genel bakış gösteren ekran görüntüsü." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. DNS adı URL 'sini bir tarayıcıya yapıştırın. Ekran görüntüsünde Batı Avrupa bölgesine yönlendiren trafik gösterilmektedir.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Batı Avrupa yönlendirilen trafiği gösteren tarayıcı penceresinin ekran görüntüsü."::: 

5. Tarayıcınızı yenileyin. Ekran görüntüsünde, Batı ABD bölgesindeki başka bir arka uç havuzu üyeleri kümesine yönlendiren trafik gösterilmektedir.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Batı ABD yönlendirilen trafiği gösteren tarayıcı penceresinin ekran görüntüsü."::: 

6. Tarayıcınızı yeniden yenileyin. Ekran görüntüsünde, şirket içi en son arka uç havuzu üyeleri kümesine yönlendiren trafik gösterilmektedir.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Şirket içine yönlendirilen trafiği gösteren tarayıcı penceresinin ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

Azure Traffic Manager Tümleştirmesini Azure VMware çözümüyle Tümleştirdiğiniz artık hakkında bilgi edinmek isteyebilirsiniz:

- [Azure VMware çözümünde Azure Application Gateway kullanma](protect-azure-vmware-solution-with-application-gateway.md)
- [Traffic Manager yönlendirme yöntemleri](../traffic-manager/traffic-manager-routing-methods.md)
- [Yük Dengeleme hizmetlerini Azure 'da birleştirme](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Traffic Manager performansını ölçme](../traffic-manager/traffic-manager-performance-considerations.md)
