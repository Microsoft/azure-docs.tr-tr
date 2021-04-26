---
title: Azure Güvenlik Duvarı için FQDN etiketlerine genel bakış
description: Bir FQDN etiketi, iyi bilinen Microsoft hizmetleriyle ilişkili bir tam etki alanı adları (FQDN) grubunu temsil eder.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c0bd2f6a021baae80dafcc3d544e1062d8e022e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94653343"
---
# <a name="fqdn-tags-overview"></a>FQDN etiketlerine genel bakış

Bir FQDN etiketi, iyi bilinen Microsoft hizmetleriyle ilişkili bir tam etki alanı adları (FQDN) grubunu temsil eder. Güvenlik duvarınız aracılığıyla gerekli giden ağ trafiğine izin vermek için uygulama kurallarında bir FQDN etiketi kullanabilirsiniz.

Örneğin, güvenlik duvarınız aracılığıyla Windows Update ağ trafiğine el ile izin vermek için, Microsoft belgeleri başına birden çok uygulama kuralı oluşturmanız gerekir. FQDN etiketlerini kullanarak bir uygulama kuralı oluşturabilir, **Windows Updates** etiketini dahil edebilir ve artık Microsoft Windows Update uç noktalarına ağ trafiği güvenlik duvarınız üzerinden akabilir.

Kendi FQDN etiketlerinizi oluşturamaz veya bir etiket içinde hangi FQDN 'lerin dahil edileceğini belirtebilirsiniz. Microsoft, FQDN etiketi ile çevrelenmiş FQDN 'leri yönetir ve etiketi FQDN 'Ler değiştiğinde güncelleştirir. 

<!--- screenshot of application rule with a FQDN tag.-->

Aşağıdaki tabloda kullanabileceğiniz geçerli FQDN etiketleri gösterilmektedir. Microsoft bu etiketleri korur ve ek etiketlerin düzenli aralıklarla eklenmesini bekleyebilir.

## <a name="current-fqdn-tags"></a>Geçerli FQDN etiketleri

|FQDN etiketi  |Description  |
|---------|---------|
|Windows Update     |[Yazılım güncelleştirmeleri Için güvenlik duvarı yapılandırma](/mem/configmgr/sum/get-started/install-a-software-update-point)bölümünde açıklandığı gibi Microsoft Update giden erişime izin verin.|
|Windows Tanılama Özellikleri|Tüm [Windows Tanılama uç noktalarına](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints)giden erişime izin verin.|
|Microsoft Etkin Koruma Hizmeti (HARITALAR)|[Haritalara](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/)giden erişime izin verin.|
|App Service Ortamı (Ao)|ASE platformu trafiği için giden erişime izin verir. Bu etiket, asa tarafından oluşturulan müşteriye özgü depolama ve SQL uç noktalarını kapsamıyor. Bunların [hizmet uç noktaları](../virtual-network/tutorial-restrict-network-access-to-resources.md) aracılığıyla etkinleştirilmesi veya el ile eklenmesi gerekir.<br><br>Azure Güvenlik duvarını as ile tümleştirme hakkında daha fazla bilgi için bkz. [App Service ortamı kilitleme](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Azure Backup hizmetlerine giden erişime izin verir.|
|Azure HDInsight|HDInsight platform trafiği için giden erişime izin verir. Bu etiket, HDInsight 'tan müşteriye özgü depolamayı veya SQL trafiğini kapsamaz. [Hizmet uç noktalarını](../virtual-network/tutorial-restrict-network-access-to-resources.md) kullanarak bunları etkinleştirin veya el ile ekleyin.|
|WindowsVirtualDesktop (WVD)|Giden Windows sanal masaüstü platformu trafiğine izin verir. Bu etiket, WVD tarafından oluşturulan dağıtıma özgü depolamayı ve Service Bus uç noktalarını kapsamıyor. Ayrıca, DNS ve KMS ağ kuralları gereklidir. Azure Güvenlik duvarını WVD ile tümleştirme hakkında daha fazla bilgi için bkz. [Azure Güvenlik Duvarı 'Nı kullanarak Windows sanal masaüstü dağıtımlarını koruma](protect-windows-virtual-desktop.md).|
|Azure Kubernetes Service (AKS)|AKS 'e giden erişime izin verir. Daha fazla bilgi için bkz. Azure [Kubernetes hizmeti (AKS) dağıtımlarını korumak Için Azure Güvenlik duvarını kullanma](protect-azure-kubernetes-service.md).|

> [!NOTE]
> Bir uygulama kuralında FQDN etiketi seçilirken protokol: bağlantı noktası alanı **https** olarak ayarlanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik duvarının nasıl dağıtılacağını öğrenmek için bkz. [öğretici: Azure Güvenlik duvarını Azure Portal kullanarak dağıtma ve yapılandırma](tutorial-firewall-deploy-portal.md).