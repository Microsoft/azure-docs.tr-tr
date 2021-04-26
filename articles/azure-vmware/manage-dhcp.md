---
title: Azure VMware çözümü için DHCP 'yi yönetme
description: Azure VMware çözümünüz özel bulutunuz için DHCP oluşturmayı ve yönetmeyi öğrenin.
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.date: 11/09/2020
ms.openlocfilehash: bcaba4274b0e6b423e9fa490c80fc57204d4e153
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97708560"
---
# <a name="manage-dhcp-for-azure-vmware-solution"></a>Azure VMware çözümü için DHCP 'yi yönetme

Özel bir bulut ortamında çalışan uygulamalar ve iş yükleri, IP adresi atamaları için DHCP hizmetleri gerektirir.  Bu makalede, Azure VMware çözümünde DHCP 'yi iki şekilde oluşturma ve yönetme konusu gösterilmektedir:

- DHCP sunucunuzu barındırmak için NSX-T kullanıyorsanız, [BIR DHCP sunucusu oluşturmanız](#create-a-dhcp-server) ve [Bu sunucuya geçiş](#create-dhcp-relay-service)yapmanız gerekir. DHCP sunucusunu oluşturduğunuzda bir ağ kesimi ekler ve DHCP IP adresi aralığını belirtmeniz gerekir.   

- Ağınızda üçüncü taraf bir dış DHCP sunucusu kullanıyorsanız, [DHCP geçiş hizmeti oluşturmanız](#create-dhcp-relay-service)gerekir. Bir DHCP sunucusuna geçiş oluştururken, NSX-T veya bir üçüncü taraf kullanarak DHCP sunucunuzu barındırıp, DHCP IP adresi aralığını belirtmeniz gerekir.

>[!IMPORTANT]
>DHCP sunucusu şirket içi veri merkezinde olduğunda VMware HCX L2 Esnetme ağındaki sanal makineler (VM) için DHCP çalışmaz.  NSX, varsayılan olarak, tüm DHCP isteklerinin L2 uzatılmasına geçiş yaptığı blokları engeller. Çözüm için, [DHCP isteklerini şirket ıçı DHCP sunucusuna gönderme](#send-dhcp-requests-to-the-on-premises-dhcp-server) yordamına bakın.


## <a name="create-a-dhcp-server"></a>DHCP sunucusu oluşturma

DHCP sunucunuzu barındırmak için NSX-T kullanmak istiyorsanız, bir DHCP sunucusu oluşturacaksınız. Daha sonra bir ağ kesimi ekler ve DHCP IP adresi aralığını belirtebilirsiniz.

1. NSX-T Yöneticisi ' nde **ağ**  >  **DHCP**' yi seçin ve ardından **Sunucu Ekle**' yi seçin.

1. **Sunucu türü** için **DHCP** ' yi seçin, sunucu adını ve IP adresini girip **Kaydet**' i seçin.

   :::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP sunucusu Ekle" border="true":::

1. **Katman 1 ağ geçitleri**' ni seçin, katman 1 ağ geçidinde dikey üç noktayı seçin ve ardından **Düzenle**' yi seçin.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="kullanılacak ağ geçidini seçin" border="true":::

1. Bir alt ağ eklemek için bir **IP ayırma kümesi** seçin.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="alt ağ ekleme" border="true":::

1. **Tür** Için **DHCP yerel sunucusu**' nu seçin. 
   
1. **DHCP sunucusu** IÇIN **varsayılan DHCP**' yi seçin ve ardından **Kaydet**' i seçin.

1. Yeniden **Kaydet** ' i seçin ve ardından **düzenlemeleri kapat**' ı seçin.

### <a name="add-a-network-segment"></a>Ağ kesimi ekleme

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]


## <a name="create-dhcp-relay-service"></a>DHCP geçiş hizmeti oluştur

Üçüncü taraf bir dış DHCP sunucusu kullanmak istiyorsanız, bir DHCP geçiş hizmeti oluşturmanız gerekir. Ayrıca, NSX-T Manager 'da DHCP IP adresi aralığını da belirtirsiniz. 

1. NSX-T Yöneticisi ' nde **ağ**  >  **DHCP**' yi seçin ve ardından **Sunucu Ekle**' yi seçin.

1. **Sunucu türü** Için **DHCP geçişi** ' ni seçin, sunucu adını ve IP adresini girip **Kaydet**' i seçin.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="DHCP geçiş hizmeti oluştur" border="true":::

1. **Katman 1 ağ geçitleri**' ni seçin, katman 1 ağ geçidinde dikey üç noktayı seçin ve ardından **Düzenle**' yi seçin.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="Katman 1 ağ geçidini Düzenle" border="true":::

1. IP adresi ayırmayı tanımlamak için bir **IP ayırma kümesi** seçin.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="IP adresi ayırmayı Düzenle" border="true":::

1. **Tür** Için **DHCP sunucusu**' nu seçin. 
   
1. **DHCP sunucusu** Için **DHCP geçişi**' ni seçin ve ardından **Kaydet**' i seçin.

1. Yeniden **Kaydet** ' i seçin ve ardından **düzenlemeleri kapat**' ı seçin.


## <a name="specify-the-dhcp-ip-address-range"></a>DHCP IP adresi aralığını belirtin

1. NSX-T Yöneticisi 'nde **ağ**  >  **kesimleri**' ni seçin. 
   
1. Segment adında dikey üç nokta simgesini seçin ve **Düzenle**' yi seçin.
   
1. Alt ağ için DHCP IP adresini belirtmek üzere **alt ağları ayarla** ' yı seçin. 
   
   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="ağ kesimleri" border="true":::
      
1. Gerekirse ağ geçidi IP adresini değiştirin ve DHCP aralığı IP 'sini girin. 
      
   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="alt ağları Düzenle" border="true":::
      
1. **Uygula**' yı ve ardından **Kaydet**' i seçin. Segmente bir DHCP sunucu havuzu atanır.
      
   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="Segmente atanan DHCP sunucusu havuzu" border="true":::


## <a name="send-dhcp-requests-to-the-on-premises-dhcp-server"></a>DHCP isteklerini şirket içi DHCP sunucusuna gönderme

L2 genişletilmiş segmentindeki Azure VMware Çözüm VM 'lerinden, şirket içi DHCP sunucusuna DHCP istekleri göndermek istiyorsanız, bir güvenlik segmenti profili oluşturacaksınız. 

1. Şirket içi vCenter oturumunuzu açın ve **giriş** bölümünde **HCX**' i seçin.

1. **Hizmetler** altında **ağ uzantısı** ' nı seçin.

1. Azure VMware çözümünden şirket içi DHCP isteklerini desteklemek istediğiniz ağ uzantısını seçin. 

1. Hedef ağ adını bir yere göz atın.  

   :::image type="content" source="media/manage-dhcp/hcx-find-destination-network.png" alt-text="VMware vSphere Istemcisinde bir ağ uzantısının ekran görüntüsü" lightbox="media/manage-dhcp/hcx-find-destination-network.png":::

1. Azure VMware çözümü NSX-T Yöneticisi ' nde **ağ**  >  **kesimleri**  >  **segment profilleri**' ni seçin. 

1. **Segment profili Ekle** ' yi ve ardından **segmenti güvenlik**' i seçin.

   :::image type="content" source="media/manage-dhcp/add-segment-profile.png" alt-text="NSX-T ' e bir segment profili ekleme ekran görüntüsü" lightbox="media/manage-dhcp/add-segment-profile.png":::

1. Bir ad ve etiket girin ve sonra **BPDU FILTRESINI** açık olarak ayarlayın ve tüm DHCP kapalı olarak değiştirir.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png" alt-text="BPDU filtresi açık ve DHCP 'nin devre dışı olduğu gösteren ekran görüntüsü" lightbox="media/manage-dhcp/add-segment-profile-bpdu-filter-dhcp-options.png":::

1. Varsa, **BPDU filtresi Izin verilenler listesinin** altındakı tüm MAC adreslerini kaldırın.  Sonra **Kaydet**'i seçin.

   :::image type="content" source="media/manage-dhcp/add-segment-profile-bpdu-filter-allow-list.png" alt-text="BPDU filtresi Izin verilenler listesindeki MAC adreslerini gösteren ekran görüntüsü":::

1. **Ağ**  >  **kesimi**  >  **kesimleri** bölümünde, arama alanına tanım ağ adını girin.

   :::image type="content" source="media/manage-dhcp/networking-segments-search.png" alt-text="Ağ > segmentleri filtre alanının ekran görüntüsü":::

1. Segment adında dikey üç nokta simgesini seçin ve **Düzenle**' yi seçin.

   :::image type="content" source="media/manage-dhcp/edit-network-segment.png" alt-text="Segment için düzenleme düğmesinin ekran görüntüsü" lightbox="media/manage-dhcp/edit-network-segment.png":::

1. **Segment güvenliğini** daha önce oluşturduğunuz segment profiliyle değiştirin.

   :::image type="content" source="media/manage-dhcp/edit-segment-security.png" alt-text="Segment güvenlik alanının ekran görüntüsü" lightbox="media/manage-dhcp/edit-segment-security.png":::

## <a name="next-steps"></a>Sonraki adımlar

[Konak bakımı ve yaşam döngüsü yönetimi](concepts-private-clouds-clusters.md#host-maintenance-and-lifecycle-management)hakkında daha fazla bilgi edinin.