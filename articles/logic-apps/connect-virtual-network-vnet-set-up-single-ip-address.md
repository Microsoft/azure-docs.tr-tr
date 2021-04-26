---
title: Açık bir giden IP adresini, sesleri için ayarlama
description: Azure Logic Apps 'de tümleştirme hizmeti ortamları (sesleri) için tek bir genel giden IP adresi ayarlamayı öğrenin
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: 7ec495dd52607f2f65e0bef50489dd182c2a3253
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874198"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Azure Logic Apps bir veya daha fazla tümleştirme hizmeti ortamı için tek bir IP adresi ayarlayın

Azure Logic Apps ile çalışırken, bir [Azure sanal ağındaki](../virtual-network/virtual-networks-overview.md)kaynaklara erişmesi gereken mantıksal uygulamaları barındırmak için bir [ *tümleştirme hizmeti ortamı* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ayarlayabilirsiniz. IP kısıtlamalarına sahip diğer uç noktalara erişmesi gereken birden çok ıSE örneğiniz varsa, sanal ağınıza bir [Azure Güvenlik Duvarı](../firewall/overview.md) veya [ağ sanal](../virtual-network/virtual-networks-overview.md#filter-network-traffic) gereci dağıtın ve giden trafiği bu güvenlik duvarı veya ağ sanal gereci üzerinden yönlendirin. Daha sonra sanal ağınızdaki tüm ıSE örneklerinin istediğiniz hedef sistemlerle iletişim kurmak için tek, genel, statik ve öngörülebilir bir IP adresi kullanmasını sağlayabilirsiniz. Bu şekilde, her bir ıSE için hedef sistemlerinizde ek güvenlik duvarı açılışlarını ayarlamanız gerekmez.

Bu konu başlığı altında, giden trafiğin bir Azure Güvenlik Duvarı üzerinden nasıl yönlendirileceği gösterilmektedir, ancak Azure Marketi 'nden üçüncü taraf güvenlik duvarı gibi bir ağ sanal gerecine benzer kavramlar uygulayabilirsiniz. Bu konu, birden çok ıSE örneğine yönelik kuruluma odaklanırken, senaryonuza erişmesi gereken IP adresi sayısını sınırlamayı gerektirdiğinde tek bir ıSE için de bu yaklaşımı kullanabilirsiniz. Güvenlik duvarının veya sanal ağ gerecinin ek maliyetlerinin senaryonuz için anlamlı olup olmadığını göz önünde bulundurun. [Azure Güvenlik Duvarı fiyatlandırması](https://azure.microsoft.com/pricing/details/azure-firewall/)hakkında daha fazla bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

* ISE ile aynı sanal ağda çalışan bir Azure Güvenlik Duvarı. Güvenlik duvarınız yoksa, önce sanal ağınıza adlı [bir alt ağ ekleyin](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) `AzureFirewallSubnet` . Daha sonra, sanal ağınızda [bir güvenlik duvarı oluşturabilir ve dağıtabilirsiniz](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) .

* Bir Azure [yol tablosu](../virtual-network/manage-route-table.md). Bir tane yoksa, önce [bir rota tablosu oluşturun](../virtual-network/manage-route-table.md#create-a-route-table). Yönlendirme hakkında daha fazla bilgi için bkz. [sanal ağ trafiği yönlendirme](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Rota tablosu ayarlama

1. [Azure Portal](https://portal.azure.com), yol tablosunu seçin, örneğin:

   ![Giden trafiği yönlendiren kuralla rota tablosu seçin](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. [Yeni bir yol eklemek](../virtual-network/manage-route-table.md#create-a-route)için yol tablosu menüsünde **rotalar**  >  **Ekle**' yi seçin.

   ![Giden trafiği yönlendirmek için rota ekleyin](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. **Yol Ekle** bölmesinde, hedef sisteme giden tüm trafiğin bu davranışı takip eden bir kuralla [yeni yolu ayarlayın](../virtual-network/manage-route-table.md#create-a-route) :

   * Sonraki atlama türü olarak [**Sanal Gereç**](../virtual-network/virtual-networks-udr-overview.md#user-defined) kullanır.

   * Sonraki atlama adresi olarak güvenlik duvarı örneği için özel IP adresine gider.

     Bu IP adresini bulmak için, güvenlik duvarı menünüzde **genel bakış**' ı seçin, **özel IP adresi** altında adresi bulun, örneğin:

     ![Güvenlik Duvarı özel IP adresi bulma](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Bu tür bir kuralın nasıl görünebileceğini gösteren bir örnek aşağıda verilmiştir:

   ![Giden trafiği yönlendirmek için kural ayarla](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Yol adı** | <*benzersiz-rota adı*> | Yol tablosundaki yol için benzersiz bir ad |
   | **Adres ön eki** | <*Hedef adres*> | Giden trafiğin gitmesini istediğiniz hedef sisteminizin adres ön eki. Bu adres için [sınıfsız Inter-Domain yönlendirme (CIDR) gösterimi](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) kullandığınızdan emin olun. Bu örnekte, bu adres ön [Eki, bölümünde](#set-up-network-rule)açıklanan bir SFTP sunucusu içindir. |
   | **Sonraki atlama türü** | **Sanal gereç** | Giden trafik tarafından kullanılan [atlama türü](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) |
   | **Sonraki atlama adresi** | <*Güvenlik Duvarı-özel-IP-adres*> | Güvenlik duvarınızın özel IP adresi |
   |||

<a name="set-up-network-rule"></a>

## <a name="set-up-network-rule"></a>Ağ kuralını ayarlama

1. Azure portal, güvenlik duvarınızı bulun ve seçin. Güvenlik Duvarı menüsünde, **Ayarlar**' ın altında **kurallar**' ı seçin. Kurallar bölmesinde, **ağ kuralı koleksiyonu**  >  **ağ kuralı koleksiyonu Ekle**' yi seçin.

   ![Güvenlik duvarına ağ kuralı koleksiyonu Ekle](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Koleksiyonda, hedef sisteme giden trafiğe izin veren bir kural ekleyin.

   Örneğin, bir ıSE 'de çalışan ve bir SFTP sunucusuyla iletişim kurması gereken bir mantıksal uygulamanız olduğunu varsayalım. Adında bir ağ kuralı içeren adlı bir ağ kuralı koleksiyonu oluşturursunuz `LogicApp_ISE_SFTP_Outbound` `ISE_SFTP_Outbound` . Bu kural, güvenlik duvarınızın özel IP adresini kullanarak, ıSE 'nin sanal ağınızda hedef SFTP sunucusuna çalıştırıldığı herhangi bir alt ağın IP adresinden gelen trafiğe izin verir.

   ![Güvenlik Duvarı için ağ kuralı ayarlama](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Ağ kuralı koleksiyonu özellikleri**

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Ad** | <*Ağ kuralı-koleksiyon-adı*> | Ağ kuralı koleksiyonunuzun adı |
   | **Priority** | <*öncelik düzeyi*> | Kural koleksiyonunu çalıştırmak için kullanılacak öncelik sırası. Daha fazla bilgi için bkz. [Azure Güvenlik Duvarı kavramları nelerdir](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)? |
   | **Eylem** | **İzin Ver** | Bu kural için gerçekleştirilecek eylem türü |
   |||

   **Ağ kuralı özellikleri**

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Ad** | <*Ağ kuralı-adı*> | Ağ kuralınız için ad |
   | **Protokol** | <*bağlantı-protokoller*> | Kullanılacak bağlantı protokolleri. Örneğin, NSG kurallarını kullanıyorsanız **, yalnızca TCP** değil **TCP** ve **UDP**' yi seçin. |
   | **Kaynak adresler** | <*ISE-alt ağ-adresler*> | ISE 'nin çalıştığı ve mantıksal uygulamanızdan gelen trafiğin kaynaklandığı alt ağ IP adresleri |
   | **Hedef adresler** | <*hedef-IP adresi*> | Giden trafiğin gitmesini istediğiniz hedef sisteminizin IP adresi. Bu örnekte, bu IP adresi SFTP sunucusu içindir. |
   | **Hedef bağlantı noktaları** | <*hedef bağlantı noktaları*> | Hedef sisteminizin gelen iletişim için kullandığı bağlantı noktaları |
   |||

   Ağ kuralları hakkında daha fazla bilgi için şu makalelere bakın:

   * [Ağ kuralını yapılandırma](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Azure Güvenlik Duvarı kural işleme mantığı](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Azure Güvenlik Duvarı hakkında SSS](../firewall/firewall-faq.yml)
   * [Azure PowerShell: New-AzFirewallNetworkRule](/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLı: az Network Firewall Network-Rule](/cli/azure/network/firewall/network-rule#az_network_firewall_network_rule_create)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Logic Apps Azure sanal ağlarına bağlanma](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
