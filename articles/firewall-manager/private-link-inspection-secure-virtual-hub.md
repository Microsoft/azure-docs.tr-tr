---
title: Azure sanal WAN 'daki özel uç noktalara giden güvenli trafik
description: Azure sanal WAN 'daki özel uç noktalara giden trafiği güvenli hale getirmek için ağ kurallarını ve uygulama kurallarını nasıl kullanacağınızı öğrenin
services: firewall-manager
author: jocortems
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: jocorte
ms.openlocfilehash: 7322bab635d398fc7a5335546ba6fef327ff24b2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259362"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Azure sanal WAN 'daki özel uç noktalara giden güvenli trafik

> [!NOTE]
> Bu makale yalnızca güvenli sanal hub için geçerlidir. Bir hub sanal ağında Azure Güvenlik Duvarı 'nı kullanarak özel uç noktalara giden trafiği incelemek istiyorsanız, bkz. [özel bir uç noktaya giden trafiği incelemek Için Azure Güvenlik Duvarı 'Nı kullanma](../private-link/inspect-traffic-with-azure-firewall.md).

[Azure özel uç noktası](../private-link/private-endpoint-overview.md) , [Azure özel bağlantısı](../private-link/private-link-overview.md)için temel yapı taşıdır. Özel uç noktalar, bir sanal ağda dağıtılan Azure kaynaklarının özel bağlantı kaynaklarıyla özel olarak iletişim kurmasına olanak tanır.

Özel uç noktalar, kaynakların bir sanal ağda dağıtılan özel bağlantı hizmetine erişimine izin verir. Sanal ağ eşlemesi ve şirket içi ağ bağlantıları aracılığıyla özel uç noktaya erişim bağlantıyı genişletir.

Şirket içinde veya Azure 'da bulunan istemcilerden gelen trafiği, sanal WAN bağlantılı bir sanal ağdaki özel uç noktalar aracılığıyla kullanıma sunulan hizmetlere göre filtrelemeniz gerekebilir. Bu makalede, güvenlik sağlayıcısı olarak [Azure Güvenlik Duvarı](../firewall/overview.md) ile [güvenli sanal hub 'ı](../firewall-manager/secured-virtual-hub.md) kullanarak bu görevde adım adım gösterilmektedir.

Azure Güvenlik Duvarı aşağıdaki yöntemlerden herhangi birini kullanarak trafiği filtreler:

* TCP ve UDP protokolleri için [ağ kurallarında FQDN](../firewall/fqdn-filtering-network-rules.md)
* HTTP, HTTPS ve MSSQL için [uygulama kurallarında FQDN](../firewall/features.md#application-fqdn-filtering-rules) .
* [Ağ kurallarını](../firewall/features.md#network-traffic-filtering-rules) kullanarak kaynak ve hedef IP adresleri, bağlantı noktası ve protokol

Özel uç noktalara giden trafiği incelemek için ağ kuralları üzerinde uygulama kurallarını kullanın.
Güvenli bir sanal hub Microsoft tarafından yönetilir ve bir [özel DNS bölgesine](../dns/private-dns-privatednszone.md)bağlanamaz. [Özel bir bağlantı kaynağı](../private-link/private-endpoint-overview.md#private-link-resource) FQDN 'sini karşılık gelen özel uç nokta IP adresine çözümlemek için bu gereklidir.

SQL FQDN filtrelemesi yalnızca [proxy modunda](../azure-sql/database/connectivity-architecture.md#connection-policy) desteklenir (bağlantı noktası 1433). *Ara sunucu* modu, *yeniden yönlendirmeye* kıyasla daha fazla gecikme süresine neden olabilir. Azure 'da bağlanan istemciler için varsayılan olan yeniden yönlendirme modunu kullanmaya devam etmek istiyorsanız, güvenlik duvarı ağ kurallarında FQDN 'yi kullanarak erişimi filtreleyebilirsiniz.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>Ağ ve uygulama kurallarında FQDN 'yi kullanarak trafiği Filtreleme

Aşağıdaki adımlar, Azure Güvenlik duvarının trafiği ağ ve uygulama kurallarında FQDN kullanarak filtrelemesine olanak tanır:

1. Güvenli sanal hub 'a bağlı bir sanal ağda [DNS ileticisi](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) sanal makinesi dağıtın ve özel uç noktalar için bir kayıt türlerini barındıran özel DNS bölgelerine bağlanır.

2. [Özel DNS AYARLARıNı](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal) DNS ileticisi sanal makinesi IP adresini işaret etmek ve güvenli sanal hub 'Da dağıtılan Azure güvenlik duvarıyla ilişkili güvenlik DUVARı ilkesinde DNS ara sunucusunu etkinleştirmek üzere yapılandırın.

3. Güvenli sanal hub 'a bağlı sanal ağlar için [özel DNS sunucularını](../virtual-network/manage-virtual-network.md#change-dns-servers) , güvenli sanal hub 'Da dağıtılan Azure güvenlik duvarıyla ILIŞKILI özel IP adresini işaret etmek üzere yapılandırın.

4. Özel uç noktalar genel DNS bölgelerinin DNS sorgularını, güvenli sanal hub 'da dağıtılan Azure güvenlik duvarıyla ilişkili özel IP adresine iletmek için şirket içi DNS sunucularını yapılandırın.

5. *Hedef türü* FQDN 'si ve özel bağlantı kaynağı genel FQDN 'si *hedef olarak güvenli* sanal hub 'da dağıtılan Azure güvenlik duvarıyla ilişkili güvenlik duvarı ilkesinde bir [uygulama kuralı](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) veya [ağ kuralı](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule) yapılandırın.

6. Güvenli sanal hub 'da dağıtılan Azure güvenlik duvarıyla ilişkili güvenlik duvarı ilkesinde *güvenli sanal hub 'lara* gidin ve özel uç noktalara giden trafik filtrelemesinin yapılandırıldığı güvenli sanal hub 'ı seçin.

7. **Güvenlik Yapılandırması**' na gidin, **özel trafik** altında **Azure Güvenlik Duvarı aracılığıyla gönder** ' i seçin.

8. Güvenli sanal hub 'da Azure Güvenlik Duvarı aracılığıyla denetlenecek CıDR öneklerini düzenlemek için **özel trafik öneklerini** seçin ve her özel uç nokta için bir/32 ön eki aşağıdaki şekilde ekleyin:

   > [!IMPORTANT]
   > Bu/32 ön ekleri yapılandırılmamışsa, Özel uç noktalara giden trafik Azure Güvenlik duvarını atlar.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Güvenlik Duvarı Yöneticisi güvenlik yapılandırması" border="true":::

Bu adımlar yalnızca, istemcileri ve özel uç noktaları aynı güvenli sanal hub 'a ve şirket içi istemcilere bağlı farklı sanal ağlarda dağıtıldığında çalışır. İstemciler ve özel uç noktalar aynı sanal ağa dağıtılmışsa, Özel uç noktalar için bir UDR ve/32 yolları oluşturulmalıdır. Bu yolları, **sonraki atlama türü**  ile **Sanal Gereç** ve **sonraki atlama adresi** ayarı, güvenli sanal hub 'DA dağıtılan Azure Güvenlik duvarının özel IP adresine ayarlı şekilde yapılandırın. **Ağ geçidi yollarının yayılması** **Evet** olarak ayarlanmalıdır.

Aşağıdaki diyagramda, farklı istemcilerin Azure sanal WAN 'da dağıtılan özel bir uç noktaya bağlanması için DNS ve veri trafiği akışları gösterilmektedir:

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="Trafik akışları" border="true":::

## <a name="troubleshooting"></a>Sorun giderme

Güvenli sanal hub aracılığıyla özel uç noktalara giden trafiği filtrelemeye çalıştığınızda karşılaşabileceğiniz başlıca sorunlar şunlardır:

- İstemciler özel uç noktalara bağlanamaz.

- Azure Güvenlik Duvarı atlanır. Bu belirti, Azure Güvenlik duvarında ağ veya uygulama kuralları günlük girişlerinin yokluğu ile doğrulanabilir.

Çoğu durumda, bu sorunlar aşağıdaki sorunlardan biri nedeniyle oluşur:

- Yanlış DNS ad çözümlemesi

- Yanlış yönlendirme yapılandırması

### <a name="incorrect-dns-name-resolution"></a>Yanlış DNS ad çözümlemesi

1. Sanal ağ DNS sunucularının *özel* olarak AYARLANDıĞıNı ve IP adresinin, güvenli sanal hub 'Daki Azure Güvenlik DUVARıNıN özel IP adresi olduğunu doğrulayın.

   Azure CLı:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. DNS ileticisi sanal makinesi ile aynı sanal ağdaki istemcilerin, DNS ileticisi olarak yapılandırılmış sanal makineyi doğrudan sorgulayarak, Özel uç nokta genel FQDN 'sini karşılık gelen özel IP adresine çözümleyebildiğini doğrulayın.

   Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. *AzureFirewallDNSProxy* Azure Güvenlik Duvarı günlük girişlerini inceleyin ve ISTEMCILERDEN gelen DNS sorgularını alıp çözümleyebilmesini doğrulayın.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. *DNS proxy* 'nin ETKINLEŞTIRILDIĞINI ve DNS ileticisi sanal makinesi IP adresinin IP adresini Işaret eden *özel* bir DNS sunucusunun, güvenli sanal hub 'daki Azure güvenlik duvarıyla ilişkili güvenlik duvarı ilkesinde yapılandırıldığını doğrulayın.

   Azure CLı:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>Yanlış yönlendirme yapılandırması

1. Güvenli sanal hub 'da dağıtılan Azure güvenlik duvarıyla ilişkili güvenlik duvarı ilkesinde *güvenlik yapılandırmasını* doğrulayın. Trafiği filtrelemek istediğiniz tüm sanal ağ ve dal bağlantıları için **Azure Güvenlik Duvarı tarafından güvenli** şekilde gösterildiği gibi, **özel trafik** sütununun altında olduğundan emin olun.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Azure Güvenlik Duvarı tarafından güvenli hale getirilmiş özel trafik" border="true":::

2. Güvenli sanal hub 'da dağıtılan Azure güvenlik duvarıyla ilişkili güvenlik duvarı ilkesinde **güvenlik yapılandırmasını** doğrulayın. Özel **trafik ön ekleri** altındaki trafiği filtrelemek istediğiniz her özel uç nokta özel IP adresi için bir/32 girişi olduğundan emin olun.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Güvenlik Duvarı Yöneticisi güvenlik yapılandırması-özel trafik önekleri" border="true":::

3. Sanal WAN altındaki güvenli sanal hub 'da, trafiğini filtrelemek istediğiniz sanal ağlarla ve dallar bağlantılarıyla ilişkili yol tabloları için geçerli yolları inceleyin. Trafiğini filtrelemek istediğiniz her özel uç nokta özel IP adresi için/32 girişin bulunduğundan emin olun.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="Güvenli sanal hub geçerli yolları" border="true":::

4. Trafiğini filtrelemek istediğiniz sanal ağlarda dağıtılan sanal makinelere bağlı NIC 'lerde etkin yolları inceleyin. Trafiğini filtrelemek istediğiniz her özel uç nokta özel IP adresi için/32 girişin bulunduğundan emin olun.
 
   Azure CLı:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. Şirket içi yönlendirme cihazlarınızın yönlendirme tablolarını inceleyin. Özel uç noktaların dağıtıldığı sanal ağların adres alanlarını öğrendiğinizden emin olun.

   Azure sanal WAN, güvenlik duvarı ilkesi **güvenlik yapılandırmasındaki** **özel trafik ön ekleri** altında yapılandırılan önekleri şirket içinde duyurmuyor. /32 girişlerinin, şirket içi yönlendirme cihazlarınızın yönlendirme tablolarında gösterilmemesi beklenmektedir.

6. **AzureFirewallApplicationRule** ve **AzureFirewallNetworkRule** Azure Güvenlik Duvarı günlüklerini inceleyin. Özel uç noktalara hedeflenen trafiğin günlüğe kaydedilmesini sağlayın.

   **AzureFirewallNetworkRule** günlük girdileri FQDN bilgileri içermez. Ağ kuralları incelenirken IP adresine ve bağlantı noktasına göre filtreleyin.

   [Azure dosyaları](../storage/files/storage-files-introduction.md) özel uç noktalarına giden trafiği filtrelerken, **AzureFirewallNetworkRule** günlük girişleri yalnızca bir istemci ilk kez bağlanırken veya dosya paylaşımının bağlandığı zaman oluşturulacaktır. Azure Güvenlik Duvarı, dosya paylaşımındaki dosyalar için [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) işlemlerine yönelik Günlükler oluşturmaz. Bunun nedeni, istemci ilk olarak dosya paylaşımında bağlantı veya bağlama yapıldığında, CRUD işlemlerinin açılmış kalıcı TCP kanalı üzerinden taşınmasıdır.

   Uygulama kuralı günlük sorgusu örneği:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>Sonraki adımlar

- [Azure Güvenlik Duvarı'nı kullanarak özel uç noktaya giden trafiği inceleme](../private-link/inspect-traffic-with-azure-firewall.md)
