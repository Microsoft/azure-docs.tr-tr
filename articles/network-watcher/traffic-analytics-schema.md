---
title: Azure Trafik Analizi şeması | Microsoft Docs
description: Azure ağ güvenlik grubu akış günlüklerini çözümlemek için Trafik Analizi şemasını anlayın.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: bd83d915b51ab44d4287987e3da7113722910262
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020248"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Trafik Analizi şema ve veri toplama

Trafik Analizi, bulut ağlarında Kullanıcı ve uygulama etkinliğine görünürlük sağlayan bulut tabanlı bir çözümdür. Trafik Analizi, Azure bulutunuzda trafik akışına yönelik Öngörüler sağlamak için ağ Izleyicisi ağ güvenlik grubu (NSG) akış günlüklerini analiz eder. Trafik Analizi ile şunları yapabilirsiniz:

- Azure abonelikleriniz genelinde ağ etkinliğini görselleştirin ve etkin noktaları belirlersiniz.
- Açık bağlantı noktaları, internet erişimi yapılmaya çalışan uygulamalar ve standart dışı ağlara bağlanan sanal makineler (VM) gibi bilgilerle ağınızı güvenli hale getirin ve güvenliğini sağlayın.
- Performans ve kapasite için ağ dağıtımınızı iyileştirmek üzere Azure bölgeleri ve İnternet genelinde trafik akışı düzenlerini anlayın.
- Ağ yapılandırması hataları ağınızdaki başarısız bağlantıların başında.
- Ağ kullanımını bayt, paket veya akışlarda öğrenin.

### <a name="data-aggregation"></a>Veri toplama

1. "FlowIntervalStartTime_t" ve "FlowIntervalEndTime_t" arasındaki NSG 'lerdeki tüm akış günlükleri, Trafik Analizi tarafından işlenmeden önce blob olarak depolama hesabındaki bir dakikalık aralıklarla yakalanır.
2. Trafik Analizi varsayılan işleme aralığı 60 dakikadır. Bu, her 60 dakikalık Trafik Analizi, toplama için Blobların depolama alanından çekmeyeceği anlamına gelir. Seçilen Aralık 10 dakika ise, Trafik Analizi her 10 dakikada bir depolama hesabından blob 'ları seçer.
3. Aynı kaynak IP, hedef IP, hedef bağlantı noktası, NSG adı, NSG kuralı, akış yönü ve Aktarım Katmanı Protokolü (TCP veya UDP) olan akışlar (unutmayın: Kaynak bağlantı noktası toplama için hariç tutulur) Trafik Analizi tarafından tek bir akışta kümelendirilebilir
4. Bu tek kayıt, Trafik Analizi tarafından Log Analytics kaydedilir (aşağıdaki bölümde bulunan Ayrıntılar). Bu işlem, en fazla 1 saat sürer.
5. FlowStartTime_t alanı, "FlowIntervalStartTime_t" ve "FlowIntervalEndTime_t" arasındaki akış günlüğü işleme aralığına bu tür toplanmış akışın (aynı dört demet) ilk oluşumunu gösterir.
6. TA 'daki herhangi bir kaynak için, Kullanıcı ARABIRIMINDE gösterilen akışlar NSG tarafından görülen toplam akışlardır, ancak Log Analytics Kullanıcı yalnızca tek, azaltılan kaydı görür. Tüm akışları görmek için, depolama alanından başvurulabilen blob_id alanını kullanın. Bu kayıt için toplam akış sayısı, blob 'da görülen bireysel akışlarla eşleşmeyecektir.

Aşağıdaki sorgu, son 30 gün içinde şirket içi tüm akış günlüklerine bakmanıza yardımcı olur.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Yukarıdaki belirtilen sorgudaki akışlara ait blob yolunu görüntülemek için aşağıdaki sorguyu kullanın:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

Yukarıdaki sorgu, blob 'a doğrudan erişmek için bir URL oluşturur. Yer tutucuları olan URL aşağıda verilmiştir:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Trafik Analizi şemasında kullanılan alanlar
  > [!IMPORTANT]
  > Trafik Analizi şeması 22 Ağustos 2019 tarihinde güncelleştirilmiştir. Yeni şema, kaynakları daha basit hale getiren FlowDirection alanını ayrıştırma gereksinimini ortadan kaldırmak için kaynak ve hedef IP 'Leri de sağlar. </br>
  > FASchemaVersion_s, 1 ' den 2 ' ye güncelleştirildi. </br>
  > Kullanım dışı alanlar: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Yeni alanlar: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Kullanım dışı bırakılan alanlar 22 Kasım 2019 tarihine kadar kullanılabilir olacaktır.

Trafik Analizi, Log Analytics üzerine kurulmuştur, böylece Trafik Analizi tarafından düzenlenmiş veriler üzerinde özel sorgular çalıştırabilir ve aynı üzerinde uyarılar ayarlayabilirsiniz.

Şema içindeki alanlar ve bunların işaret ettikleri yer aşağıda listelenmiştir

| Alan | Biçimi | Açıklamalar |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Trafik Analizi verileri tablosu
| SubType_s | FlowLog | Akış günlüklerinin alt türü. Yalnızca "FlowLog" kullanın, SubType_s 'in diğer değerleri ürünün iç işleyişi içindir |
| FASchemaVersion_s |   2   | Şema sürümü. NSG akış günlüğü sürümünü yansıtmıyor |
| TimeProcessed_t   | UTC olarak tarih ve saat  | Trafik Analizi depolama hesabından ham akış günlüklerinin işlendiği zaman |
| FlowIntervalStartTime_t | UTC olarak tarih ve saat |  Akış günlüğü işleme aralığının başlangıç saati. Bu, akış aralığının ölçülmüş olduğu süredir |
| FlowIntervalEndTime_t | UTC olarak tarih ve saat | Akış günlüğü işleme aralığının bitiş saati |
| FlowStartTime_t | UTC olarak tarih ve saat |  "FlowIntervalStartTime_t" ve "FlowIntervalEndTime_t" arasındaki akış günlüğü işleme aralığında akışın ilk oluşumu (toplanacak). Bu akış toplama mantığına göre toplanmış |
| FlowEndTime_t | UTC olarak tarih ve saat | Akış günlüğü işleme aralığı içinde "FlowIntervalStartTime_t" ve "FlowIntervalEndTime_t" arasındaki son akış (toplanacak) olayı. Akış günlüğü v2 ' de, bu alan, en son akışın aynı dört demet ile başladığı saati (ham akış kaydında "B" olarak işaretlenir) içerir |
| FlowType_s |  * IntraVNet <br> * Intervnet <br> * S2S <br> * P2S <br> * Azucumhuriyeti <br> * ExternalPublic <br> * MaliciousFlow <br> * Bilinmeyen özel <br> * Bilinmiyor | Tablonun altındaki notlardaki tanım |
| SrcIP_s | Kaynak IP adresi | Azucumhuriyeti ve ExternalPublic akışlar söz konusu olduğunda boş olacaktır |
| DestIP_s | Hedef IP adresi | Azucumhuriyeti ve ExternalPublic akışlar söz konusu olduğunda boş olacaktır |
| VMIP_s | VM 'nin IP 'si | Azucumhuriyeti ve ExternalPublic akışları için kullanılır |
| PublicIP_s | Genel IP adresleri | Azucumhuriyeti ve ExternalPublic akışları için kullanılır |
| DestPort_d | Hedef Bağlantı Noktası | Trafiğin gelen bağlantı noktası |
| L4Protocol_s  | * T <br> * U  | Aktarım Protokolü. T = TCP <br> U = UDP |
| L7Protocol_s  | Protokol adı | Hedef bağlantı noktasından türetilmiş |
| FlowDirection_s | * I = gelen<br> * O = giden | Akış günlüğü başına NSG/çıkış akışı yönü |
| FlowStatus_s  | * A = NSG kuralına Izin verilir <br> * D = NSG kuralı tarafından reddedildi  | Akış günlüğü başına NSG tarafından engellenen akışın durumu |
| NSGList_s | \<SUBSCRİPTİONID >\/< RESOURCEGROUP_NAME >\/< NSG_NAME > | Flow ile ilişkili ağ güvenlik grubu (NSG) |
| NSGRules_s | \<Dizin değeri 0) >\|\<NSG_RULENAME >\|\<akış yönü >\|Flowdurumu\|>\<flowcountprocessedbyrule\<> |  Bu akışa izin verilen veya reddedilen NSG kuralı |
| NSGRule_s | NSG_RULENAME |  Bu akışa izin verilen veya reddedilen NSG kuralı |
| NSGRuleType_s | * Kullanıcı tanımlı * varsayılan |   Akış tarafından kullanılan NSG kuralının türü |
| MACAddress_s | MAC adresi | Akışın yakalandığı NIC 'in MAC adresi |
| Subscription_s | Bu alana Azure sanal ağ/ağ arabirimi/sanal makine aboneliği doldurulur | Yalnızca FlowType = S2S, P2S, Azucumhuriyeti, ExternalPublic, MaliciousFlow ve UnknownPrivate akış türleri için geçerlidir (yalnızca bir tarafın Azure olduğu akış türleri) |
| Subscription1_s | Abonelik Kimliği | Akıştaki kaynak IP 'nin ait olduğu sanal ağ/ağ arabiriminin/sanal makinenin abonelik KIMLIĞI |
| Subscription2_s | Abonelik Kimliği | Akıştaki hedef IP 'nin ait olduğu sanal ağ/ağ arabiriminin/sanal makinenin abonelik KIMLIĞI |
| Region_s | Akıştaki IP 'nin ait olduğu sanal ağ/ağ arabiriminin/sanal makinenin Azure bölgesi | Yalnızca FlowType = S2S, P2S, Azucumhuriyeti, ExternalPublic, MaliciousFlow ve UnknownPrivate akış türleri için geçerlidir (yalnızca bir tarafın Azure olduğu akış türleri) |
| Region1_s | Azure Bölgesi | Akıştaki kaynak IP 'nin ait olduğu sanal ağ/ağ arabiriminin/sanal makinenin Azure bölgesi |
| Region2_s | Azure Bölgesi | Akıştaki hedef IP 'nin ait olduğu sanal ağın Azure bölgesi |
| NIC_s | \<resourcegroup_Name >\/\<networkınterfacename > |  Trafiği gönderen veya alan VM ile ilişkilendirilen NIC |
| NIC1_s | < resourcegroup_Name >/\<networkarabirimadı > | Akıştaki kaynak IP ile ilişkili NIC |
| NIC2_s | < resourcegroup_Name >/\<networkarabirimadı > | Akıştaki hedef IP ile ilişkili NIC |
| VM_s | < resourcegroup_Name >\/\<networkınterfacename > | Ağ arabirimiyle ilişkili sanal makine NIC_s |
| VM1_s | < resourcegroup_Name >/\<virtualmachinename > | Akıştaki kaynak IP ile ilişkili sanal makine |
| VM2_s | < resourcegroup_Name >/\<virtualmachinename > | Akıştaki hedef IP ile ilişkili sanal makine |
| Subnet_s | < ResourceGroup_Name >/< VNET_Name >/\<subnetName > | NIC_s ile ilişkili alt ağ |
| Subnet1_s | < ResourceGroup_Name >/< VNET_Name >/\<subnetName > | Akıştaki kaynak IP ile ilişkili alt ağ |
| Subnet2_s | < ResourceGroup_Name >/< VNET_Name >/\<subnetName >    | Akıştaki hedef IP ile ilişkili alt ağ |
| ApplicationGateway1_s | \<SubscriptionID >/\<resourcegroupname >/\<applicationgatewayname > | Akıştaki kaynak IP ile ilişkili uygulama ağ geçidi |
| ApplicationGateway2_s | \<SubscriptionID >/\<resourcegroupname >/\<applicationgatewayname > | Akıştaki hedef IP ile ilişkili uygulama ağ geçidi |
| LoadBalancer1_s | \<SubscriptionID >/\<resourcegroupname >/\<loadbalancername > | Akıştaki kaynak IP ile ilişkili yük dengeleyici |
| LoadBalancer2_s | \<SubscriptionID >/\<resourcegroupname >/\<loadbalancername > | Akıştaki hedef IP ile ilişkili yük dengeleyici |
| LocalNetworkGateway1_s | \<SubscriptionID >/\<resourcegroupname >/\<localnetworkgatewayname > | Akıştaki kaynak IP ile ilişkili yerel ağ geçidi |
| LocalNetworkGateway2_s | \<SubscriptionID >/\<resourcegroupname >/\<localnetworkgatewayname > | Akıştaki hedef IP ile ilişkili yerel ağ geçidi |
| ConnectionType_s | Olası değerler Vneteşleme, VpnGateway ve ExpressRoute şeklindedir |    Bağlantı türü |
| ConnectionName_s | \<SubscriptionID >/\<resourcegroupname >/\<connectionName > | Bağlantı Adı |
| ConnectingVNets_s | Sanal ağ adlarının boşlukla ayrılmış listesi | Hub ve bağlı bileşen topolojisi söz konusu olduğunda, hub sanal ağları buraya doldurulacak |
| Country_s | İki harfli ülke kodu (ISO 3166-1 Alpha-2) | ExternalPublic akış türü için dolduruldu. PublicIPs_s alanındaki tüm IP adresleri aynı ülke kodunu paylaşacaktır |
| AzureRegion_s | Azure bölge konumları | Akış türü Azucumhuriyet için dolduruldu. PublicIPs_s alanındaki tüm IP adresleri Azure bölgesini paylaşacaktır |
| AllowedInFlows_d | | İzin verilen gelen akış sayısı. Bu, akışın yakalandığı ağ arabirimine gelen aynı dört tanımlama grubunu paylaşan akış sayısını temsil eder |
| DeniedInFlows_d |  | Reddedilen gelen akış sayısı. (Akışın yakalandığı ağ arabirimine gelen) |
| AllowedOutFlows_d | | İzin verilen giden akış sayısı (akışın yakalandığı ağ arabirimine giden) |
| DeniedOutFlows_d  | | Reddedilen giden akış sayısı (akışın yakalandığı ağ arabirimine giden) |
| FlowCount_d | Kullanım dışı. Aynı dört demet ile eşleşen toplam akış. ExternalPublic ve Azucumhurflow türünde, say çeşitli Publicıp adreslerinden akışları da içerecektir.
| InboundPackets_d | NSG kuralının uygulandığı ağ arabiriminde yakalanan olarak alınan paketler | Bu yalnızca NSG akış günlüğü şemasının 2. sürümü için doldurulur |
| OutboundPackets_d  | NSG kuralının uygulandığı ağ arabiriminde yakalanan olarak gönderilen paketler | Bu yalnızca NSG akış günlüğü şemasının 2. sürümü için doldurulur |
| InboundBytes_d |  NSG kuralının uygulandığı ağ arabiriminde yakalanan bayt alındı | Bu yalnızca NSG akış günlüğü şemasının 2. sürümü için doldurulur |
| OutboundBytes_d | NSG kuralının uygulandığı ağ arabiriminde yakalanan olarak gönderilen bayt sayısı | Bu yalnızca NSG akış günlüğü şemasının 2. sürümü için doldurulur |
| CompletedFlows_d  |  | Bu, yalnızca NSG akış günlüğü şemasının 2. sürümü için sıfır olmayan değer ile doldurulur |
| PublicIPs_s | < PUBLIC_IP >\|\<FLOW_STARTED_COUNT >\|FLOW_ENDED_COUNT>\|OUTBOUND_PACKETS>\<INBOUND_PACKETS >\|\<\<\| \<OUTBOUND_BYTES>\|INBOUND_BYTES>\< | Çubuklar ile ayrılmış girişler |
| SrcPublicIPs_s | < SOURCE_PUBLIC_IP >\|\<FLOW_STARTED_COUNT >\|FLOW_ENDED_COUNT>\|OUTBOUND_PACKETS>\<INBOUND_PACKETS\|\<\< >\|OUTBOUND_BYTES\<>\|INBOUND_BYTES>\< | Çubuklar ile ayrılmış girişler |
| DestPublicIPs_s | < DESTINATION_PUBLIC_IP >\|\<FLOW_STARTED_COUNT >\|FLOW_ENDED_COUNT>\|OUTBOUND_PACKETS>\<INBOUND_\|\<\< PAKETLER >\|\<OUTBOUND_BYTES >\|INBOUND_BYTES\<> | Çubuklar ile ayrılmış girişler |

### <a name="notes"></a>Notlar

1. Azucumhuriyeti ve ExternalPublic akışları söz konusu olduğunda, müşterinin sahip olduğu Azure VM IP 'si VMIP_s alanına girilir, ancak genel IP adresleri PublicIPs_s alanında doldurulur. Bu iki akış türü için SrcIP_s ve DestIP_s alanları yerine VMIP_s ve PublicIPs_s kullanılmalıdır. Azucumhuriyeti ve Externalpublicıp adresleri için, müşteri günlüğü Analizi çalışma alanına alınan kayıt sayısının en az olması için daha fazla topladık. (Bu alan yakında kullanımdan kalktı ve Azure VM 'nin akışta kaynak mi yoksa hedef mi olduğunu bağlı olarak SrcIP_ ve DestIP_s kullanılması gerekir)
1. Akış türleri için Ayrıntılar: Akışta yer alan IP adreslerine bağlı olarak, içindeki akışları aşağıdaki akış türlerine göre sınıflandırıyoruz:
1. Invnet: her Iki akıştaki IP adresi aynı Azure sanal ağında yer alır.
1. Akışdaki ıntervnet-IP adresleri iki farklı Azure sanal ağında yer alır.
1. S2S – (siteden siteye) IP adreslerinden biri Azure sanal ağına aittir, diğer bir deyişle IP adresi, VPN Gateway veya Express Route aracılığıyla Azure sanal ağına bağlı olan müşteri ağına (site) bağlanır.
1. P2S-(site üzerine gelin) IP adreslerinden biri Azure sanal ağına aittir, diğer bir deyişle diğer IP adresleri ise Azure sanal ağına VPN ağ geçidi üzerinden bağlı olan müşteri ağına (site) bağlanır.
1. Azucumhuriyet-IP adreslerinden biri Azure sanal ağına aittir, diğer bir deyişle, diğer IP adresleri Microsoft 'un sahip olduğu Azure Iç genel IP adreslerine aittir. Müşterinin sahip olduğu genel IP adresleri, bu akış türünün bir parçası olmayacaktır. Örneğin, bir Azure hizmetine (depolama uç noktası) trafik gönderen herhangi bir müşterinin sahip olduğu sanal makine, bu akış türü altında kategorilere ayrılır.
1. ExternalPublic-IP adreslerinden biri Azure sanal ağına aittir, ancak diğer IP adresi Azure 'da olmayan bir genel IP olduğunda, işlem aralığı için tükettiği Trafik Analizi " FlowIntervalStartTime_t "ve" FlowIntervalEndTime_t ".
1. MaliciousFlow-IP adreslerinden biri Azure sanal ağına aittir, diğer IP adresi Azure 'da olmayan bir genel IP ve bu işlem aralığı için Trafik Analizi tüketen ASC akışlarında kötü amaçlı olarak bildirildi FlowIntervalStartTime_t "ve" FlowIntervalEndTime_t ".
1. UnknownPrivate-IP adreslerinden biri Azure sanal ağına aittir, ancak diğer IP adresleri, RFC 1918 ' de tanımlandığı şekilde özel IP aralığına aittir ve müşterinin sahip olduğu bir siteye veya Azure sanal ağına Trafik Analizi tarafından eşlenemedi.
1. Bilinmiyor – akışlardaki IP adreslerinden birinin Azure 'daki müşteri topolojisi ile şirket içi (site) arasında eşleme yapılamıyor.
1. Bazı alan adları s veya \_ \_d ile eklenir. Bunlar kaynak ve hedefi işaret etmez ancak sırasıyla veri türleri dize ve ondalık olduğunu gösterir.

### <a name="next-steps"></a>Sonraki Adımlar
Sık sorulan soruların yanıtlarını almak için bkz. [Traffic ANALYTICS SSS](traffic-analytics-faq.md) , işlevlerle ilgili ayrıntıları görmek için bkz. [Trafik Analizi belgeleri](traffic-analytics.md)
