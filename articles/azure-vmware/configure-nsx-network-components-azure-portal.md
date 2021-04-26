---
title: Azure VMware çözümünde NSX ağ bileşenlerini yapılandırma
description: NSX-T ağ kesimlerini yapılandırmak için Azure VMware Çözüm konsolu 'nu nasıl kullanacağınızı öğrenin.
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a4702efe8ea26234d074f421d2e6eab871eba5f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105733147"
---
# <a name="configure-nsx-network-components-in-azure-vmware-solution"></a>Azure VMware çözümünde NSX ağ bileşenlerini yapılandırma

Azure VMware çözümü özel bulutu, varsayılan olarak, yazılım tanımlı ağ (SDDC) olarak NSX-T ile birlikte gelir. Etkin **/etkin** modda bir NSX-t katmanı 0 ağ geçidi ve etkin/bekleme modunda varsayılan NSX-t katman 1 ağ geçidi ile önceden sağlanmış olarak sunulur.  Bu ağ geçitleri, segmentleri (mantıksal anahtarlar) bağlamanıza ve East-West ve North-South bağlantısı sağlamanıza olanak tanır. 

Azure VMware çözümü özel bulutu dağıtıldıktan sonra, gerekli NSX-T nesnelerini Azure VMware Çözüm konsolundan **Iş yükü ağı** altında yapılandırabilirsiniz.  Konsol, bir VMware yöneticisinin her gün ihtiyacı olan ve NSX-T ' y i bilmediğiniz kullanıcılara hedeflenmiş NSX-T işlemlerinin Basitleştirilmiş görünümünü sunar.  

Azure VMware Çözüm konsolu 'nda NSX-T bileşenlerini yapılandırmak için dört seçeneğiniz vardır:
- **Segmentler** -NSX-T Yöneticisi ve vCenter içinde görüntülenen segmentleri oluşturun.
- **DHCP-DHCP** kullanmayı PLANLıYORSANıZ bir DHCP sunucusu veya DHCP geçişi oluşturun.
- **Bağlantı noktası yansıtma** – ağ sorunlarını gidermeye yardımcı olmak için bağlantı noktası yansıtma oluşturun.
- **DNS** – DNS isteklerini çözümlenmek üzere BELIRLENMIŞ bir DNS sunucusuna göndermek IÇIN bir DNS ileticisi oluşturun.  

>[!NOTE]
>Burada bahsedilen Gelişmiş ayarları ve diğer NSX-T özelliklerini kullanabileceğiniz NSX-T Manager konsoluna erişebilirsiniz. 
 
:::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking.png" alt-text="NSX-T ' i yapılandırmak için Azure VMware Çözüm konsolundaki dört seçeneği gösteren ekran görüntüsü.":::

## <a name="prerequisites"></a>Önkoşullar
Azure VMware çözümüne özel buluta oluşturulan veya geçirilen sanal makineler (VM) bir kesime eklenmelidir. 

## <a name="create-an-nsx-t-segment-in-the-azure-portal"></a>Azure portal bir NSX-T segmenti oluşturma
Azure portal Azure VMware Çözüm konsolundan bir NSX-T segmenti oluşturup yapılandırabilirsiniz.  Bu segmentler varsayılan katman 1 ağ geçidine bağlanır ve bu kesimlerdeki iş yükleri East-West ve bağlantı North-South alır. Segmenti oluşturduktan sonra NSX-T Yöneticisi ve vCenter ' de görüntülenir.

>[!NOTE]
>DHCP kullanmayı planlıyorsanız, bir NSX-T segmenti oluşturup yapılandırmadan önce [BIR DHCP sunucusu veya DHCP geçişi yapılandırmanız](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) gerekir.

1. Azure VMware Çözüm özel bulutunuzda **iş yükü ağı** altında, **segmentler**  >  **Ekle**' yi seçin. Yeni mantıksal segmentin ayrıntılarını girip **Tamam**' ı seçin.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-new-nsxt-segment.png" alt-text="Yeni bir segmentin nasıl ekleneceğini gösteren ekran görüntüsü.":::
   
   - **Segment adı** -vCenter 'da görünür olan mantıksal anahtarın adı.
   - Alt ağ **geçidi** -alt ağ maskesi ile mantıksal anahtarın alt ağı için ağ geçidi IP adresi. VM 'Ler bir mantıksal anahtara iliştirilir ve bu anahtara bağlanan tüm VM 'Ler aynı alt ağa aittir.  Ayrıca, bu mantıksal kesime eklenmiş tüm VM 'Lerin aynı kesimden bir IP adresi taşıması gerekir.
   - **DHCP** (isteğe bağlı)-mantıksal bir KESIM için DHCP aralıkları. Kesimlerde DHCP 'yi kullanmak için bir [DHCP sunucusu veya DHCP geçişinin](#create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal) yapılandırılması gerekir.  
   - **Bağlı ağ geçidi**  -  *Varsayılan olarak seçilidir ve salt okunurdur.*  Katman 1 ağ geçidi ve kesim bilgileri türü. 
      - **T1** -NSX-T Manager 'daki katman 1 ağ geçidinin adı. Azure VMware çözümü özel bulutu, etkin/etkin modda NSX-T katmanı 0 ağ geçidi ve etkin/bekleme modunda varsayılan NSX-T katman 1 ağ geçidi ile gelir.  Azure VMware Çözüm konsolu aracılığıyla oluşturulan segmentler yalnızca varsayılan katman 1 ağ geçidine bağlanır ve bu parçaların iş yükleri East-West ve North-South bağlantı alır. NSX-T Manager aracılığıyla yalnızca daha fazla katman-1 ağ geçidi oluşturabilirsiniz. NSX-T Manager konsolundan oluşturulan katman 1 ağ geçitleri, Azure VMware Çözüm konsolu 'nda görünmez. 
      - Azure VMware çözümü tarafından desteklenen **tür** -kaplama segmenti.

   Segment artık Azure VMware Çözüm konsolu, NSX-T Yöneticisi ve vCenter 'da görünür.

## <a name="create-a-dhcp-server-or-dhcp-relay-in-the-azure-portal"></a>Azure portal bir DHCP sunucusu veya DHCP geçişi oluşturun
Azure portal doğrudan Azure VMware Çözüm konsolundan bir DHCP sunucusu veya geçişi oluşturabilirsiniz. DHCP sunucusu veya geçişi, Azure VMware çözümünü dağıtırken oluşturulan katman 1 ağ geçidine bağlanır. DHCP aralıklarına verdiğiniz tüm segmentler bu DHCP 'nin bir parçası olacaktır.  Bir DHCP sunucusu veya DHCP geçişi oluşturduktan sonra, onu kullanmak için bir alt ağ ya da kesim düzeyinde bir Aralık tanımlamanız gerekir.

1. Azure VMware Çözüm özel bulutunuzda **iş yükü ağı** altında **DHCP**  >  **Ekle**' yi seçin.

2. **DHCP sunucusu** ya da **DHCP geçişi** ' ni seçin ve ardından sunucu veya geçiş için BIR ad ve üç IP adresi belirtin. 

   >[!NOTE]
   >DHCP geçişi için başarılı bir yapılandırma için yalnızca bir IP adresi gereklidir.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-dhcp-server-relay.png" alt-text="Azure VMware çözümlerinde bir DHCP sunucusu veya DHCP geçişinin nasıl ekleneceğini gösteren ekran görüntüsü.":::

4. [Mantıksal kesimlerde DHCP aralıkları sağlayarak](#create-an-nsx-t-segment-in-the-azure-portal) DHCP yapılandırmasını tamamlayıp **Tamam**' ı seçin.
 
## <a name="configure-port-mirroring-in-the-azure-portal"></a>Azure portal bağlantı noktası yansıtmayı yapılandırma
Bağlantı noktası yansıtmayı, her paketin bir kopyasını bir ağ anahtarı bağlantı noktasından diğerine iletmeyi içeren ağ trafiğini izlemek için yapılandırabilirsiniz. Bu seçenek, yansıtılmış verileri alan bağlantı noktasına bir protokol Çözümleyicisi koyar. Bir kaynaktan, VM 'den veya bir VM grubundan trafiği analiz eder ve ardından tanımlı bir hedefe gönderilir. 

Azure VMware Çözüm konsolu 'nda bağlantı noktası yansıtma ayarlamak için şunları yapmanız gerekir:

* [1. adım. Kaynak ve hedef VM 'Ler veya VM grupları oluşturma](#step-1-create-source-and-destination-vms-or-vm-groups) – kaynak grubun, trafiğin yansıtılmasının tek BIR VM veya birden çok VM 'si vardır.

* [2. adım. Bir bağlantı noktası yansıtma profili oluşturun](#step-2-create-a-port-mirroring-profile) – kaynak ve hedef VM grupları için trafik yönünü tanımlarsınız.

### <a name="step-1-create-source-and-destination-vms-or-vm-groups"></a>Adım 1. Kaynak ve hedef VM 'Ler veya VM grupları oluşturma

Bu adımda, bir kaynak VM grubu ve hedef VM grubu oluşturacaksınız.

1. Azure VMware Çözüm özel bulutunuzda **iş yükü ağı** altında, **bağlantı noktası yansıtma**  >  **VM grupları**  >  **Ekle**' yi seçin.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-vm-groups.png" alt-text="Bağlantı noktası yansıtması için bir VM grubu oluşturmayı gösteren ekran görüntüsü.":::

1. Yeni VM grubu için bir ad girin, listeden istediğiniz VM 'Leri seçin ve ardından **Tamam**' a tıklayın.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-vm-group.png" alt-text="VM grubuna eklenecek VM 'lerin listesini gösteren ekran görüntüsü.":::

1. Hedef VM grubunu oluşturmak için bu adımları tekrarlayın.

### <a name="step-2-create-a-port-mirroring-profile"></a>Adım 2. Bağlantı noktası yansıtma profili oluşturma

Bu adımda, kaynak ve hedef VM gruplarının trafik yönü için bir profil tanımlayacaksınız.

>[!NOTE]
>Hem kaynak hem de hedef VM gruplarının oluşturulduğundan emin olun.

1. **Bağlantı noktası yansıtma**  >  **Ekle** ' yi seçin ve ardından şunu girin:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/add-port-mirroring-profile.png" alt-text="Bağlantı noktası yansıtma profili için gereken bilgileri gösteren ekran görüntüsü.":::

   - **Bağlantı noktası yansıtma adı** -profil için tanımlayıcı ad.
   - **Yön** -giriş, çıkış veya çift yönlü seçim.
   - **Kaynak** -kaynak VM grubunu seçin.
   - **Hedef** -hedef VM grubunu seçin.
   - **Açıklama** -bağlantı noktası yansıtması için bir açıklama girin.

1. Profili tamamladıktan sonra **Tamam ' ı** seçin. 

   Profil ve VM grupları Azure VMware Çözüm konsolu 'nda görülebilir.

## <a name="configure-a-dns-forwarder-in-the-azure-portal"></a>Azure portal bir DNS ileticisi yapılandırma
Belirli DNS isteklerinin çözümlenmek üzere belirlenmiş bir DNS sunucusuna iletildiği bir DNS ileticisi yapılandıracaksınız.  DNS ileticisi, **Varsayılan BIR DNS bölgesi** ve en çok üç **FQDN bölgesi** ile ilişkilendirin.

>[!TIP]
>[BIR DNS ileticisi yapılandırmak Için NSX-T Yöneticisi konsolunu](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.5/administration/GUID-A0172881-BB25-4992-A499-14F9BE3BE7F2.html)da kullanabilirsiniz.

Azure VMware Çözüm konsolunda bir DNS ileticisi ayarlamak için şunları yapmanız gerekir:

* [1. adım. Varsayılan bir DNS bölgesi ve FQDN bölgesi yapılandırma](#step-1-configure-a-default-dns-zone-and-fqdn-zone) – BIR DNS sorgusu alındığında, DNS ileticisi etki alanı adını FQDN DNS bölgesindeki etki alanı adlarıyla karşılaştırır. 

* [2. adım. DNS hizmetini yapılandırma](#step-2-configure-dns-service) -DNS ileticisi hizmetini yapılandıracaksınız.

### <a name="step-1-configure-a-default-dns-zone-and-fqdn-zone"></a>Adım 1. Varsayılan bir DNS bölgesi ve FQDN bölgesi yapılandırma
Yukarı akış sunucusuna DNS sorguları göndermek için varsayılan bir DNS bölgesi ve FQDN bölgesi yapılandırırsınız.  Bir DNS sorgusu alındığında, DNS ileticisi, sorgudaki etki alanı adını FQDN DNS bölgelerinin etki alanı adlarıyla karşılaştırır. Bir eşleşme bulunursa sorgu, FQDN DNS bölgesinde belirtilen DNS sunucularına iletilir. Eşleşme bulunmazsa, sorgu varsayılan DNS bölgesinde belirtilen DNS sunucularına iletilir.

>[!NOTE]
>Bir FQDN bölgesi yapılandırmadan önce varsayılan bir DNS bölgesi tanımlanmalıdır. 

1. Azure VMware Çözüm özel bulutunuzda **iş yükü ağı** altında **DNS**  >  **DNS bölgeleri**  >  **Ekle**' yi seçin.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-dns-zones.png" alt-text="DNS bölgelerinin ve bir DNS hizmetinin nasıl ekleneceğini gösteren ekran görüntüsü.":::

1. **Varsayılan DNS bölgesi** ' ni seçin ve şunları belirtin:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-zones.png" alt-text="Varsayılan DNS bölgesinin nasıl ekleneceğini gösteren ekran görüntüsü.":::

   1. DNS bölgesi için bir ad.

   1. **8.8.8.8** biçiminde en fazla üç DNS sunucusu IP adresi.

1. **FQDN bölgesi** ' ni seçin ve şunları belirtin:

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-fqdn-zone.png" alt-text="Bir FQDN bölgesinin nasıl ekleneceğini gösteren ekran görüntüsü. ":::

   1. DNS bölgesi için bir ad.

   1. FQDN etki alanı.

   1. **8.8.8.8** biçiminde en fazla üç DNS sunucusu IP adresi.

1. Varsayılan DNS bölgesi ve DNS hizmetini eklemeyi bitirmeden **Tamam ' ı** seçin.

### <a name="step-2-configure-dns-service"></a>Adım 2. DNS hizmetini yapılandır

1. **DNS hizmeti** sekmesini seçin, **Ekle**' yi seçin. Ayrıntıları sağlayın ve **Tamam**' ı seçin.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service.png" alt-text="DNS hizmeti için gereken bilgileri gösteren ekran görüntüsü.":::

   >[!TIP]
   >**Katman 1 ağ geçidi** varsayılan olarak seçilidir ve Azure VMware çözümü dağıtıldığında oluşturulan ağ geçidini yansıtır.

   DNS hizmeti başarıyla eklendi.

   :::image type="content" source="media/configure-nsx-network-components-azure-portal/nsxt-workload-networking-configure-dns-service-success.png" alt-text="DNS hizmetinin başarıyla eklendiğini gösteren ekran görüntüsü.":::

