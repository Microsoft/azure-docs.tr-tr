---
title: Azure Cloud Services (klasik) NetworkConfiguration şeması | Microsoft Docs
description: Sanal ağ ve DNS değerlerini belirten hizmet yapılandırma dosyasının NetworkConfiguration öğesinin alt öğeleri hakkında bilgi edinin.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
'---thor': tagore
ms.openlocfilehash: e3e9d5ebc7e4e2aa1119f4b840aa1d8b94d93aa4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935133"
---
# <a name="azure-cloud-services-classic-config-networkconfiguration-schema"></a>Azure Cloud Services (klasik) config NetworkConfiguration şeması

> [!IMPORTANT]
> [Azure Cloud Services (genişletilmiş destek)](../cloud-services-extended-support/overview.md) , Azure Cloud Services ürünü için yeni bir Azure Resource Manager tabanlı dağıtım modelidir.Bu değişiklik ile Azure Service Manager tabanlı dağıtım modelinde çalışan Azure Cloud Services, Cloud Services (klasik) olarak yeniden adlandırıldı ve tüm Yeni dağıtımlar [Cloud Services kullanmalıdır (genişletilmiş destek)](../cloud-services-extended-support/overview.md).

`NetworkConfiguration`Hizmet yapılandırma dosyasının öğesi sanal ağ ve DNS değerlerini belirtir. Bu ayarlar, bulut hizmetleri için isteğe bağlıdır.

Sanal ağlar ve ilişkili şemalar hakkında daha fazla bilgi edinmek için aşağıdaki kaynağı kullanabilirsiniz:

- [Bulut hizmeti (klasik) yapılandırma şeması](schema-cscfg-file.md)
- [Bulut hizmeti (klasik) Tanım Şeması](schema-csdef-file.md)
- [Sanal ağ oluşturma (klasik)](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)

## <a name="networkconfiguration-element"></a>NetworkConfiguration öğesi
Aşağıdaki örnek `NetworkConfiguration` öğesi ve onun alt öğelerini gösterir.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

Aşağıdaki tablo, öğesinin alt öğelerini açıklar `NetworkConfiguration` .

| Öğe       | Açıklama |
| ------------- | ----------- |
| AccessControl | İsteğe bağlı. Bir bulut hizmetindeki uç noktalara erişim kurallarını belirtir. Erişim denetimi adı, özniteliği için bir dize tarafından tanımlanır `name` . `AccessControl`Öğe bir veya daha fazla `Rule` öğe içeriyor. Birden fazla `AccessControl` öğe tanımlanabilir.|
| Kural | İsteğe bağlı. Belirli bir alt ağ IP adresi aralığı için gerçekleştirilecek eylemi belirtir. Kuralın sırası, öznitelik için bir dize değeri tarafından tanımlanır `order` . Kural sayısı daha yüksek öncelik olur. Örneğin, kurallar 100, 200 ve 300 sıra numaralarıyla belirtilebilir. 100 sıra numarasına sahip kural, 200 sırasına sahip kurala göre önceliklidir.<br /><br /> Kural eylemi, özniteliği için bir dize tarafından tanımlanır `action` . Olası değerler şunlardır:<br /><br /> -   `permit` – Yalnızca belirtilen alt ağ aralığından gelen paketlerin uç noktayla iletişim kurabildiğini belirtir.<br />-   `deny` – Belirtilen alt ağ aralığındaki uç noktalara erişimin reddedildiğini belirtir.<br /><br /> Kuraldan etkilenen IP adreslerinin alt ağ aralığı, özniteliği için bir dize tarafından tanımlanır `remoteSubnet` . Kural açıklaması özniteliği için bir dize tarafından tanımlanır `description` .|
| EndpointAcl | İsteğe bağlı. Bir uç noktaya erişim denetim kuralları atamasını belirtir. Uç noktasını içeren rolün adı, öznitelik için bir dize tarafından tanımlanır `role` . Uç noktanın adı, öznitelik için bir dize tarafından tanımlanır `endpoint` . `AccessControl`Uç noktaya uygulanması gereken kurallar kümesinin adı, öznitelik için bir dize içinde tanımlanır `accessControl` . Birden fazla `EndpointAcl` öğe tanımlanabilir.|
| DnsServer | İsteğe bağlı. Bir DNS sunucusunun ayarlarını belirtir. Sanal ağ olmadan DNS sunucularının ayarlarını belirtebilirsiniz. DNS sunucusunun adı, özniteliği için bir dize tarafından tanımlanır `name` . DNS sunucusunun IP adresi, özniteliği için bir dize tarafından tanımlanır `IPAddress` . IP adresi geçerli bir IPv4 adresi olmalıdır.|
| Localnetworksite | İsteğe bağlı. Bulut hizmetinizi dağıtmak istediğiniz sanal ağ sitesinin adını belirtir. Bu ayar bir sanal ağ sitesi oluşturmaz. Sanal ağınız için Ağ dosyasında daha önce tanımlanan bir siteye başvurur. Bulut hizmeti yalnızca bir sanal ağın üyesi olabilir. Bu ayarı belirtmezseniz, bulut hizmeti sanal bir ağa dağıtılmaz. Sanal ağ sitesinin adı, öznitelik için bir dize tarafından tanımlanır `name` .|
| Instanceaddress | İsteğe bağlı. Sanal ağdaki bir alt ağ veya alt ağ kümesi için bir rolün ilişkilendirmesini belirtir. Bir rol adını bir örnek adresiyle ilişkilendirdiğinizde, bu rolün ilişkilendirilmesini istediğiniz alt ağları belirtebilirsiniz. , `InstanceAddress` Bir alt ağ öğesi içerir. Alt ağ veya alt ağlarla ilişkili rolün adı, öznitelik için bir dize tarafından tanımlanır `roleName` .|
| Alt ağ | İsteğe bağlı. Ağ yapılandırma dosyasındaki alt ağ adına karşılık gelen alt ağı belirtir. Alt ağın adı, öznitelik için bir dize tarafından tanımlanır `name` .|
| Ayrıl | İsteğe bağlı. Dağıtımla ilişkilendirilmesi gereken ayrılmış IP adresini belirtir. Ayrılmış IP adresini oluşturmak için Create Ayrılmış IP adresini kullanmanız gerekir. Bir bulut hizmetindeki her dağıtım, ayrılmış bir IP adresi ile ilişkilendirilebilir. Ayrılmış IP adresinin adı, öznitelik için bir dize tarafından tanımlanır `name` .|

## <a name="see-also"></a>Ayrıca Bkz.
[Bulut hizmeti (klasik) yapılandırma şeması](schema-cscfg-file.md)