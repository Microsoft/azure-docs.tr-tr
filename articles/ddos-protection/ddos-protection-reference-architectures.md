---
title: Azure DDoS koruması başvuru mimarileri
description: Azure DDoS koruması başvuru mimarilerini öğrenin.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: b74ebf332790fd9a08840c8c76d99e2b014dac43
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103088"
---
# <a name="ddos-protection-reference-architectures"></a>DDoS koruması başvuru mimarileri

DDoS koruma standardı, [sanal bir ağa dağıtılan hizmetler için](../virtual-network/virtual-network-for-azure-services.md)tasarlanmıştır. Diğer hizmetler için, varsayılan DDoS koruması temel hizmeti geçerlidir. Aşağıdaki başvuru mimarileri, mimari desenleriyle birlikte gruplanmış senaryolar tarafından düzenlenir.

## <a name="virtual-machine-windowslinux-workloads"></a>Sanal makine (Windows/Linux) iş yükleri

### <a name="application-running-on-load-balanced-vms"></a>Yük dengeli VM 'lerde çalışan uygulama

Bu başvuru mimarisi, kullanılabilirliği ve ölçeklenebilirliği artırmak için bir yük dengeleyicinin arkasındaki ölçek kümesinde birden çok Windows sanal makinesi çalıştırmaya yönelik kanıtlanmış bir yöntemler kümesi gösterir. Bu mimari, bir Web sunucusu gibi durum bilgisi olmayan herhangi bir iş yükü için kullanılabilir.

![Yük dengeli VM 'lerde çalışan bir uygulama için başvuru mimarisinin diyagramı](./media/ddos-best-practices/image-9.png)

Bu mimaride, bir iş yükü birden çok VM örneğine dağıtılır. Tek bir genel IP adresi vardır ve internet trafiği VM 'lere yük dengeleyici aracılığıyla dağıtılır. DDoS koruma standardı, ortak IP ile ilişkili olan Azure (internet) yük dengeleyicinin sanal ağı üzerinde etkinleştirilmiştir.

Yük dengeleyici, gelen internet isteklerini VM örneklerine dağıtır. Sanal Makine Ölçek Kümeleri, sanal makinelerin sayısının el ile veya dışarı veya ön tanımlı kurallara göre otomatik olarak ölçeklendirilmesine olanak tanır. Kaynak DDoS saldırısının altındaysa bu önemlidir. Bu başvuru mimarisi hakkında daha fazla bilgi için [Bu makaleye](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)bakın.

### <a name="application-running-on-windows-n-tier"></a>Windows N katmanında çalışan uygulama

N katmanlı mimari uygulamanın birçok yolu vardır. Aşağıdaki diyagramda tipik bir üç katmanlı Web uygulaması gösterilmektedir. Bu mimari, [ölçeklenebilirlik ve kullanılabilirlik için yük dengeli VM 'Ler çalıştırma](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)makalesinde oluşturulur. Web ve iş katmanları yük dengeli VM’leri kullanır.

![Windows N katmanında çalışan bir uygulama için başvuru mimarisinin diyagramı](./media/ddos-best-practices/image-10.png)

Bu mimaride, DDoS koruma standardı sanal ağ üzerinde etkinleştirilmiştir. Sanal ağdaki tüm genel IP 'Ler 3 ve 4. katman için DDoS koruması 'nı alır. Katman 7 koruması için WAF SKU 'sunda Application Gateway dağıtın. Bu başvuru mimarisi hakkında daha fazla bilgi için [Bu makaleye](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)bakın.

> [!NOTE]
> Tek bir VM 'nin genel IP 'nin arkasında çalıştığı senaryolar desteklenmez.

### <a name="paas-web-application"></a>PaaS web uygulaması

Bu başvuru mimarisi, tek bir bölgede Azure App Service uygulamasının çalıştığını gösterir. Bu mimari, [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) ve [Azure SQL veritabanı](https://azure.microsoft.com/documentation/services/sql-database/)kullanan bir Web uygulaması için kanıtlanmış bir uygulamalar kümesi gösterir.
Yük devretme senaryoları için bir bekleme bölgesi ayarlanır.

![PaaS Web uygulaması için başvuru mimarisinin diyagramı](./media/ddos-best-practices/image-11.png)

Azure Traffic Manager, gelen istekleri bölgelerden birinde Application Gateway yönlendirir. Normal işlemler sırasında, istekleri etkin bölgede Application Gateway yönlendirir. Bu bölge kullanılamaz duruma gelirse, Traffic Manager bekleme bölgesinde Application Gateway devreder.

İnternet 'ten Web uygulamasına giden tüm trafik, Traffic Manager üzerinden [Application Gateway genel IP adresine](../application-gateway/application-gateway-web-app-overview.md) yönlendirilir. Bu senaryoda, App Service (Web App) doğrudan dışarıdan verilmez ve Application Gateway tarafından korunur. 

Katman 7 (HTTP/HTTPS/WebSocket) saldırılarına karşı korumaya yardımcı olmak için Application Gateway WAF SKU 'SU (mod engelleme) yapılandırmanızı öneririz. Ayrıca, Web Apps [yalnızca Application Gateway IP adresinden gelen trafiği kabul](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) edecek şekilde yapılandırılmıştır.

Bu başvuru mimarisi hakkında daha fazla bilgi için [Bu makaleye](/azure/architecture/reference-architectures/app-service-web-app/multi-region)bakın.

## <a name="protecting-on-premises-resources"></a>Şirket içi kaynakları koruma

Azure 'da genel bir IP adresi barındırarak ve trafiği şirket içi ortamınıza arka uç kaynağına yönlendirerek şirket içi kaynaklarınızı korumak için Azure DDoS koruma standardı 'nın ölçeklendirilmesi, kapasitesi ve verimliliğinden yararlanabilirsiniz.

![Şirket içi kaynakları koruma](./media/reference-architectures/ddos-on-prem.png)

Internet 'ten trafik alan bir Web uygulamanız varsa, Web uygulamasını Application Gateway arkasında barındırabilirsiniz ve sonra SQL ekleme ve Yavaşsus gibi katman 7 Web saldırılarına karşı WAF ile koruyabilirsiniz. Uygulamanızın arka uç kaynakları, VPN üzerinden bağlanan şirket içi ortamınızda yer alır. 

Şirket içi ortamdaki arka uç kaynakları, genel İnternet 'e gösterilmez. Yalnızca AppGW/WAF genel IP 'si Internet 'e açıktır ve uygulamanızın DNS adı bu genel IP adresine eşlenir. 

DDoS koruma standardı, AppGW/WAF içeren sanal ağ üzerinde etkinleştirildiğinde, DDoS koruma standardı, hatalı trafiği azaltarak uygulamanızı savunarak uygulamanıza gereken Temizleme trafiğini yönlendirerek uygulamanızı savunacaktır. 

Bu [makalede](https://docs.microsoft.com/azure/azure-vmware/protect-azure-vmware-solution-with-application-gateway) , Azure VMware çözümünde çalışan bir Web uygulamasını korumak için Application Gateway birlikte DDoS koruma standardı 'nı nasıl kullanabileceğiniz gösterilmektedir.

## <a name="mitigation-for-non-web-paas-services"></a>Web dışı PaaS hizmetleri için risk azaltma

### <a name="hdinsight-on-azure"></a>Azure 'da HDInsight

Bu başvuru mimarisinde, [Azure HDInsight kümesi](../hdinsight/index.yml)Için DDoS koruma standardı yapılandırma gösterilmektedir. HDInsight kümesinin bir sanal ağa bağlı olduğundan ve sanal ağ üzerinde DDoS korumasının etkinleştirildiğinden emin olun.

![Sanal ağ ayarlarına sahip "HDInsight" ve "Gelişmiş ayarlar" bölmeleri](./media/ddos-best-practices/image-12.png)

![DDoS korumasını etkinleştirme seçimi](./media/ddos-best-practices/image-13.png)

Bu mimaride, Internet 'ten HDInsight kümesine giden trafik, HDInsight ağ geçidi yük dengeleyicisiyle ilişkili genel IP 'ye yönlendirilir. Ağ Geçidi yük dengeleyici daha sonra trafiği baş düğümlere veya çalışan düğümlerine doğrudan gönderir. HDInsight sanal ağında DDoS koruması standardı etkinleştirildiğinden, sanal ağdaki tüm genel IP 'Ler 3. ve 4. katman için DDoS koruması 'nı alır. Bu başvuru mimarisi, N katmanlı ve çok bölgeli başvuru mimarileri ile birleştirilebilir.

Bu başvuru mimarisi hakkında daha fazla bilgi için Azure [sanal ağ kullanarak Azure HDInsight 'ı genişletme](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) bölümüne bakın.


> [!NOTE]
> PowerApps veya API Management 'ın ortak IP içeren bir sanal ağda Azure App Service Ortamı, yerel olarak desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

- [DDoS koruma planı oluşturma](manage-ddos-protection.md)hakkında bilgi edinin.