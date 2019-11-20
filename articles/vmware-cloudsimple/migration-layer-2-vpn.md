---
title: Cloudby CloudSimple tarafından Azure VMware çözümü-şirket içi bir katman 2 ağını özel buluta uzat
description: CloudSimple özel bulutu ve şirket içi tek başına NSX Edge istemcisinde NSX-T arasında bir katman 2 VPN ayarlamayı açıklar
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 37f337f158c3ca53170d963f1304801a12b732da
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053881"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>Katman 2 esnetilmiş ağlarını kullanarak iş yüklerini geçirme

Bu kılavuzda, katman 2 VPN 'yi (L2VPN) kullanarak şirket içi ortamınızdan CloudSimple özel bulutuna bir katman 2 ağını nasıl uzatılacağınızı öğreneceksiniz. Bu çözüm, şirket içi VMware ortamınızda çalışan iş yüklerinizin, iş yüklerinize yeniden IP adresleri olmadan aynı alt ağ adres alanı içinde Azure 'daki özel buluta geçirilmesini sağlar.

Katman 2 ağlarının L2VPN tabanlı uzatma, şirket içi VMware ortamınızda NSX tabanlı ağlarla veya bu ağlar olmadan çalışabilir. Şirket içi iş yükleri için NSX tabanlı ağlarınız yoksa, tek başına NSX Edge Hizmetleri ağ geçidi kullanabilirsiniz.

> [!NOTE]
> Bu kılavuzda, şirket içi ve özel bulut veri merkezlerinin siteden siteye VPN üzerinden bağlandığı senaryo ele alınmaktadır.

## <a name="deployment-scenario"></a>Dağıtım senaryosu

L2VPN kullanarak şirket içi ağınızı uzatmak için bir L2VPN sunucusu (hedef NSX-T Tier0 yönlendirici) ve bir L2VPN istemcisi (kaynak tek başına istemci) yapılandırmanız gerekir.  

Bu dağıtım senaryosunda, özel bulut, şirket içi yönetim ve vMotion alt ağlarının özel bulut yönetimi ve vMotion alt ağları ile iletişim kurmasına olanak tanıyan bir siteden siteye VPN tüneli aracılığıyla şirket içi ortamınıza bağlanır. Bu düzenleme, Cross vCenter vMotion (xVC-vMotion) için gereklidir. NSX-T Tier0 yönlendiricisi, özel bulutta bir L2VPN sunucusu olarak dağıtılır.

Tek başına NSX Edge, şirket içi ortamınızda bir L2VPN istemcisi olarak dağıtılır ve daha sonra L2VPN sunucusuyla eşleştirilmiştir. Her tarafta bir GRE tüneli bitiş noktası oluşturulur ve şirket içi katman 2 ağını özel bulutunuzun ' esnetmek üzere yapılandırılır. Bu yapılandırma aşağıdaki şekilde gösterilmiştir.

![Dağıtım senaryosu](media/l2vpn-deployment-scenario.png)

L2 VPN kullanarak geçiş hakkında daha fazla bilgi edinmek için bkz. VMware belgelerindeki [sanal özel ağlar](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) .

## <a name="prerequisites-for-deploying-the-solution"></a>Çözümü dağıtmaya yönelik önkoşullar

Çözümü dağıtmadan ve yapılandırmadan önce aşağıdakilerin yerinde olduğundan emin olun:

* Şirket içi vSphere sürümü 6.7 U1 + veya 6.5 P03 + ' dır.
* Şirket içi vSphere lisansı kurumsal artı düzeyindedir (vSphere dağıtılmış anahtarı için).
* Özel bulutunuzun esnetilbilmesi için iş yükü katman 2 ağını belirler.
* Şirket içi ortamınızda L2VPN istemci gerecinizi dağıtmaya yönelik bir katman 2 ağı belirler.
* [Özel bir bulut zaten oluşturuldu](create-private-cloud.md).
* Tek başına NSX-T Edge gereci sürümü, özel bulut ortamınızda kullanılan NSX-T Manager sürümü (NSX-T 2.3.0) ile uyumludur.
* Şirket içi vCenter 'da, sahte aktarımlar etkinleştirilmiş bir santral bağlantı noktası grubu oluşturulmuştur.
* NSX-T tek başına istemci yukarı IP adresi için kullanılmak üzere bir genel IP adresi ayrılmış ve iki adres arasındaki çeviri için 1:1 NAT yerinde.
* DNS iletme, az.cloudsimple.io etki alanı için şirket içi DNS sunucularında, özel bulut DNS sunucularını işaret etmek üzere ayarlanır.
* VMotion 'in iki sitede çalışması için, RTT gecikmesi 150 MS 'den az veya buna eşittir.

## <a name="limitations-and-considerations"></a>Sınırlamalar ve önemli noktalar

Aşağıdaki tabloda desteklenen vSphere sürümleri ve ağ bağdaştırıcısı türleri listelenmektedir.  

| vSphere sürümü | Kaynak vSwitch türü | Sanal NIC sürücüsü | Hedef vSwitch türü | Destekleniyor mu? |
------------ | ------------- | ------------ | ------------- | ------------- 
| Tümü | BÖLGENIZDE | Tümü | BÖLGENIZDE | Yes |
| vSphere 6.7 Kullanıcı arabirimi veya üzeri, 6.5 P03 veya üzeri | BÖLGENIZDE | VMXNET3 | N-VDS | Yes |
| vSphere 6.7 Kullanıcı arabirimi veya üzeri, 6.5 P03 veya üzeri | BÖLGENIZDE | E1000 | N-VDS | [, Her bir](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7 UI veya 6.5 P03, NSX-V veya NSX-T 2.2, 6.5 P03 veya üzeri sürümlerindeki sürümler | Tümü | Tümü | N-VDS | [, Her bir](https://kb.vmware.com/s/article/56991) |

VMware NSX-T 2,3 sürümünden itibaren:

* L2VPN üzerinden şirket içi olarak uzatılmış özel bulut tarafındaki mantıksal anahtar aynı anda yönlendirilemez. Uzatılmış mantıksal anahtar bir mantıksal yönlendiriciye bağlanamaz.
* L2VPN ve rota tabanlı ıPSEC VPN 'Leri yalnızca API çağrıları kullanılarak yapılandırılabilir.

Daha fazla bilgi için bkz. VMware belgelerindeki [sanal özel ağlar](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704) .

### <a name="sample-l2-vpn-deployment-addressing"></a>Örnek L2 VPN dağıtımı adresleme

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>Tek başına ESG 'nin (L2 VPN istemcisi) dağıtıldığı şirket içi ağ

| **Öğe** | **Değer** |
|------------|-----------------|
| Ağ adı | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CıDR| 10.250.0.0/24 |
| Tek başına uç gereç IP adresi | 10.250.0.111 |
| Tek başına uç gereç NAT IP adresi | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>Uzatılmak üzere şirket içi ağ

| **Öğe** | **Değer** |
|------------|-----------------|
| VLAN | 472 |
| CıDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>NSX-T Tier0 yönlendirici için özel bulut IP şeması (L2 VPN hizmeti)

| **Öğe** | **Değer** |
|------------|-----------------|
| Geri döngü arabirimi | 192.168.254.254/32 |
| Tünel arabirimi | 5.5.5.1/29 |
| Mantıksal anahtar (uzatılmış) | Stretch_LS |
| Geri döngü arabirimi (NAT IP adresi) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>Uzatılmış ağa eşlenecek özel bulut ağı

| **Öğe** | **Değer** |
|------------|-----------------|
| VLAN | 712 |
| CıDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>L2VPN için gereken mantıksal yönlendirici KIMLIĞINI getir

Aşağıdaki adımlarda, IPSec ve L2VPN Hizmetleri için Tier0 DR mantıksal yönlendirici örneğinin mantıksal yönlendirici KIMLIĞINI nasıl getirileceği gösterilmektedir. L2VPN uygularken mantıksal yönlendirici KIMLIĞI daha sonra gerekir.

1. NSX-T Manager https://*NSX-t-Manager-IP adresi* ' nde oturum açın ve **ağ** > **yönlendiricileri** > **sağlayıcı-LR** > **genel bakış**' ı seçin. **Yüksek kullanılabilirlik modu**Için **Etkin bekleme**' yı seçin. Bu eylem, Tier0 yönlendiricisinin etkin olduğu uç VM 'yi gösteren bir açılır pencere açar.

    ![Etkin bekleme seçin](media/l2vpn-fetch01.png)

2. **Doku** > **düğümleri** > **kenarları**' nı seçin. Önceki adımda tanımlanan etkin uç VM 'nin (Edge VM1) yönetim IP adresini bir yere göz önüne alın.

    ![Note yönetim IP 'si](media/l2vpn-fetch02.png)

3. Uç VM 'nin Yönetim IP adresine bir SSH oturumu açın. Kullanıcı adı **yönetici** ve parola **cloudsimple 123!** ile ```get logical-router``` komutunu çalıştırın.

    ![mantıksal yönlendirici çıkışı al](media/l2vpn-fetch03.png)

4. ' DR-Provider-LR ' girişi görmüyorsanız aşağıdaki adımları izleyin.

5. İki kaplama ile desteklenen mantıksal anahtar oluşturun. Bir mantıksal anahtar, geçişi yapılan iş yüklerinin bulunduğu şirket içi olarak uzatılır. Başka bir mantıksal anahtar, bir kukla anahtardır. Yönergeler için bkz. VMware belgelerinde [mantıksal anahtar oluşturma](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html) .

    ![Mantıksal anahtar oluştur](media/l2vpn-fetch04.png)

6. Bir bağlantı yerel IP adresi veya şirket içi veya özel bulutunuzun çakışmayan bir alt ağı olan Katman1 yönlendiricisine kukla anahtarı ekleyin. VMware belgelerindeki [Katman 1 mantıksal yönlendirici üzerinde downlink bağlantı noktası ekleme](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html) bölümüne bakın.

    ![Kukla anahtar Ekle](media/l2vpn-fetch05.png)

7. Uç VM 'nin SSH oturumunda `get logical-router` komutunu yeniden çalıştırın. ' DR-Provider-LR ' mantıksal yönlendiricisinin UUID 'SI görüntülenir. L2VPN yapılandırılırken gereken UUID 'yi bir yere unutmayın.

    ![mantıksal yönlendirici çıkışı al](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>L2VPN için gereken mantıksal anahtar KIMLIĞINI getir

1. [NSX-T Manager](https://nsx-t-manager-ip-address)'da oturum açın.
2. **Ağ** > **anahtarlama** > **anahtarlar** >  **< \Mantıksal anahtar\>** **Genel Bakış ' ı**seçin.
3. L2VPN yapılandırılırken gerekli olan Esnetme mantıksal anahtarının UUID 'sini bir yere unutmayın.

    ![mantıksal yönlendirici çıkışı al](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>L2VPN için Yönlendirme ve güvenlik değerlendirmeleri

NSX-T Tier0 yönlendiricisi ve tek başına NSX Edge istemcisi arasında IPSec rota tabanlı bir VPN oluşturmak için NSX-T Tier0 yönlendiricisinin geri döngü arabirimi UDP 500/4500 üzerinden şirket içi NSX tek başına istemcisinin genel IP adresi ile iletişim kurabilmelidir.

### <a name="allow-udp-5004500-for-ipsec"></a>IPSec için UDP 500/4500 'ye izin ver

1. CloudSimple portalında NSX-T Tier0 geri döngü arabirimi için [Genel BIR IP adresi oluşturun](public-ips.md) .

2. UDP 500/4500 gelen trafiğe izin veren ve güvenlik duvarı tablosunu NSX-T HostTransport alt ağına bağlayan durum bilgisi olan kurallar içeren [bir güvenlik duvarı tablosu oluşturun](firewall.md) .

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>Geri döngü arabirimi IP 'sini düşük düzenleme ağına bildirme

1. Geri döngü arabirimi ağı için bir boş yol oluşturun. NSX-T Manager 'da oturum açın ve **ağ** > **yönlendirme** > **yönlendirici** > **sağlayıcı-LR** > **yönlendirme** > **statik yolları**' nı seçin. **Ekle**'ye tıklayın. **Ağ**için geri döngü arabirimi IP adresini girin. **Sonraki atlamalar**Için, **Ekle**' ye tıklayın, sonraki atlama için ' null ' belirtin ve varsayılan 1 ' i yönetici uzaklığı olarak tutun.

    ![Statik yol Ekle](media/l2vpn-routing-security01.png)

2. Bir IP ön eki listesi oluşturun. NSX-T Manager 'da oturum açın ve **ağ** > **yönlendirme** > **yönlendirici** > **sağlayıcı-LR** > **yönlendirme** > **IP öneki listeleri**' ni seçin. **Ekle**'ye tıklayın. Listeyi tanımlamak için bir ad girin. **Ön**ekler için Iki kez **Ekle** ' ye tıklayın. İlk satırda, **ağ** için ' 0.0.0.0/0 ' ve **eylem**için ' Reddet ' girin. İkinci satırda, **ağ** ve **eyleme** **izin ver** ' **i seçin.**
3. IP öneki listesini hem BGP komşuları 'na (TOR) ekleyin. IP öneki listesini BGP komşusuyla eklemek, varsayılan yolun BGP 'de TOR anahtarlarına tanıtılmasını önler. Ancak, null yolu içeren diğer tüm yollar TOR anahtarlarına geri döngü arabirimi IP adresini duyuracaktır.

    ![IP ön eki listesi oluştur](media/l2vpn-routing-security02.png)

4. NSX-T Manager 'da oturum açın ve **ağ** > **yönlendirme** > **yönlendirici** > **sağlayıcı-LR** > **yönlendirme** > **BGP**1**komşuları**' nı seçin. İlk komşuyu seçin.  > **adresi ailelerini** **Düzenle** ' ye tıklayın. IPv4 ailesi için, **Çıkış filtresi** sütununu düzenleyin ve oluşturduğunuz IP öneki listesini seçin. **Kaydet** düğmesine tıklayın. İkinci komşu için bu adımı tekrarlayın.

    ![IP ön eki listesi 1](media/l2vpn-routing-security03.png) Ekle ![IP ön eki listesi 2](media/l2vpn-routing-security04.png)

5. Null statik yolu BGP 'ye yeniden dağıtın. Geri döngü arabirimi yolunu alt tabloya tanıtmak için null statik yolunu BGP 'ye yeniden dağıtmanız gerekir. NSX-T Manager 'da oturum açın ve **ağ** > **yönlendirme** > **yönlendirici** > **sağlayıcı-LR** > **yönlendirme** >  yönlendirme yeniden**dağıtımı**1**komşuları**' nı seçin. **Sağlayıcı-LR-Route_Redistribution** ' ı seçin ve **Düzenle**' ye tıklayın. **Statik** onay kutusunu seçin ve **Kaydet**' e tıklayın.

    ![Null statik yolu BGP 'ye yeniden Dağıt](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>NSX-T Tier0 yönlendiricisinde rota tabanlı bir VPN yapılandırma

NSX-T Tier0 yönlendiricisinde rota tabanlı bir VPN yapılandırma ayrıntılarını doldurmanız için aşağıdaki şablonu kullanın. Sonraki göndermelerde, her GÖNDERI çağrısındaki UUID 'ler gereklidir. L2VPN için geri döngü ve tünel arabirimleri için IP adresleri benzersiz olmalıdır ve şirket içi veya özel bulut ağları ile çakışmamalıdır.

L2VPN için kullanılan geri döngü ve tünel arabirimi için seçilen IP adreslerinin benzersiz olması ve şirket içi veya özel bulut ağları ile çakışmamak gerekir. Geri döngü arabirimi ağı her zaman/32 olmalıdır.

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section “Steps to fetch Logical-Router ID needed for L2VPN”
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

Aşağıdaki API çağrılarının tümünde IP adresini NSX-T Yöneticisi IP adresiniz ile değiştirin. Tüm bu API çağrılarını POSTMAN istemcisinden veya `curl` komutlarını kullanarak çalıştırabilirsiniz.

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>Mantıksal yönlendiricide IPSec VPN hizmetini etkinleştirin

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>Profil oluşturma: ıKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>Profil oluşturma: DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>Profil oluşturma: tünel

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>Yerel uç nokta oluşturma

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>Eş uç noktası oluşturma

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>Rota tabanlı VPN oturumu oluşturma

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>NSX-T Tier0 yönlendiricisinde L2VPN yapılandırma

Her GÖNDERI çağrısından sonra aşağıdaki bilgileri girin. Sonraki GÖNDERI çağrılarında kimlikler gereklidir.

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>L2VPN hizmetini oluşturma

Yapılandırma henüz tamamlanmadığından, aşağıdaki GET komutunun çıktısı boş olacaktır.

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

Aşağıdaki POST komutu için mantıksal yönlendirici KIMLIĞI, daha önce edinilen Tier0 DR mantıksal yönlendiricisinin UUID 'si olur.

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>L2VPN oturumu oluşturma

Aşağıdaki POST komutu için, L2VPN hizmet KIMLIĞI, az önce edindiğiniz KIMLIK ve IPSec VPN oturum KIMLIĞI, önceki bölümde elde edilen KIMLIĞIDIR.

``` 
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

Bu çağrılar bir GRE tüneli uç noktası oluşturur. Durumu denetlemek için aşağıdaki komutu çalıştırın.

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>Belirtilen tünel KIMLIĞIYLE mantıksal bağlantı noktası oluştur

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>NSX-T tarafında L2VPN için eşdüzey kodu alma

NSX-T uç noktasının eş kodunu alın. Uzak uç nokta yapılandırılırken eş kod gereklidir. L2VPN < oturum kimliği > önceki bölümden elde edilebilir. Daha fazla bilgi için bkz. [NSX-T 2,3 API Kılavuzu](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html).

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>NSX-T tek başına istemcisini (Şirket içi) dağıtma

Dağıtım öncesinde, şirket içi güvenlik duvarı kurallarınızın gelen ve giden UDP 500/4500 trafiğine, daha önce NSX-T T0 yönlendirici geri döngü arabirimi için ayrılmış olan CloudSimple genel IP adresine izin verildiğini doğrulayın. 

1. [Tek başına NSX Edge Istemcisini indirin](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230) OVF ve indirilen paketteki dosyaları bir klasöre ayıklayın.

    ![Tek başına NSX Edge istemcisini indirin](media/l2vpn-deploy-client01.png)

2. Tüm ayıklanan dosyaların bulunduğu klasöre gidin. Büyük gereç boyutu veya NSX-l2t-Client-xlarge. MF ve NSX-l2t-client-Xlarge. ovf için tüm VMDK (NSX-l2t-Client-Large. MF ve NSX-l2t-client-large. ovf 'yi seçerek daha büyük boyutlu gereç boyutu için. **İleri**’ye tıklayın.

    Şablon](media/l2vpn-deploy-client02.png) ![seçin ![şablon seçin](media/l2vpn-deploy-client03.png)

3. NSX-T bağımsız istemcisi için bir ad girin ve **İleri**' ye tıklayın.

    ![Şablon adı girin](media/l2vpn-deploy-client04.png)

4. Veri deposu ayarlarına ulaşmak için gerektiğinde **İleri** ' ye tıklayın. NSX-T tek başına istemcisi için uygun veri deposunu seçin ve **İleri**' ye tıklayın.

    ![Veri deposu Seç](media/l2vpn-deploy-client06.png)

5. NSX-T tek başına istemcisine yönelik doğru bağlantı noktası gruplarını (santral PG), genel (yukarı bağlantı) ve HA arabirimini (yukarı bağlantı) seçin. **İleri**’ye tıklayın.

    ![Bağlantı noktası gruplarını seçin](media/l2vpn-deploy-client07.png)

6. **Şablonu Özelleştir** ekranında aşağıdaki ayrıntıları girin ve **İleri**' ye tıklayın:

    L2T Genişlet:

    * **Eş adresi**. NSX-T Tier0 geri döngü arabirimi için Azure CloudSimple portalında ayrılmış IP adresini girin.
    * **Eş kod**. L2VPN Server dağıtımının son adımından elde edilen eşdüzey kodu yapıştırın.
    * **Alt ARABIRIMLER VLAN (tünel kimliği)** . Esnetilmiş VLAN KIMLIĞINI girin. Parantez () içinde, daha önce yapılandırılmış olan tünel KIMLIĞINI girin.

    Yukarı bağlantı arabirimini Genişlet:

    * **DNS IP adresi**. Şirket içi DNS IP adresini girin.
    * **Varsayılan ağ geçidi**.  Bu istemci için varsayılan ağ geçidi olarak görev yapacak olan VLAN 'ın varsayılan ağ geçidini girin.
    * **IP adresi**. Tek başına istemcinin yukarı bağlantı IP adresini girin.
    * **Ön ek uzunluğu**. Yukarı bağlantı VLAN/alt ağının ön ek uzunluğunu girin.
    * **CLI Yöneticisi/etkinleştir/kök kullanıcı parolası**. Admin/Enable/root hesabının parolasını ayarlayın.

      Şablon](media/l2vpn-deploy-client08.png)
      ![özelleştirme![şablonu özelleştirme-daha fazla](media/l2vpn-deploy-client09.png)

7. Ayarları gözden geçirin ve **son**' a tıklayın.

    ![Yapılandırmayı Tamam](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>Şirket içi havuz bağlantı noktası yapılandırma

VPN sitelerinden birinde NSX dağıtılmamışsa, bu sitede tek başına NSX Edge dağıtarak bir L2 VPN yapılandırabilirsiniz. Tek başına NSX Edge, NSX tarafından yönetilmeyen bir konakta OVF dosyası kullanılarak dağıtılır. Bu, bir NSX Edge Hizmetleri ağ geçidi gerecini bir L2 VPN istemcisi olarak çalışacak şekilde dağıtır.

Tek başına bir uç santral vNIC, vSphere dağıtılmış anahtarına bağlandıysa, L2 VPN işlevi için karışık mod veya bir havuz bağlantı noktası gerekir. Karışık mod kullanılması yinelenen pingler ve yinelenen yanıtlar oluşmasına neden olabilir. Bu nedenle, L2 VPN tek başına NSX Edge yapılandırmasında havuz bağlantı noktası modu ' nu kullanın. VMware belgelerindeki [Havuz bağlantı noktası yapılandırma](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html) bölümüne bakın.

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPSec VPN ve L2VPN doğrulaması

IPSec ve L2VPN oturumlarını tek başına NSX-T kenarından doğrulamak için aşağıdaki komutları kullanın.

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

NSX-T Tier0 yönlendiricisinde IPSec ve L2VPN oturumlarını doğrulamak için aşağıdaki komutları kullanın.

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

NSX-T tek başına istemci VM 'sinin şirket içi ortamda bulunduğu ESXi konağındaki havuz bağlantı noktasını doğrulamak için aşağıdaki komutları kullanın.

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
