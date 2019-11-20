---
title: Azure Güvenlik Duvarı için FQDN etiketlerine genel bakış
description: Bir FQDN etiketi, iyi bilinen Microsoft hizmetleriyle ilişkili bir tam etki alanı adları (FQDN) grubunu temsil eder.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6396f8292a4c54f7fce237439f37c3e8156d59e8
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169046"
---
# <a name="fqdn-tags-overview"></a>FQDN etiketlerine genel bakış

Bir FQDN etiketi, iyi bilinen Microsoft hizmetleriyle ilişkili bir tam etki alanı adları (FQDN) grubunu temsil eder. Güvenlik duvarınız aracılığıyla gerekli giden ağ trafiğine izin vermek için uygulama kurallarında bir FQDN etiketi kullanabilirsiniz.

Örneğin, güvenlik duvarınız aracılığıyla Windows Update ağ trafiğine el ile izin vermek için, Microsoft belgeleri başına birden çok uygulama kuralı oluşturmanız gerekir. FQDN etiketlerini kullanarak bir uygulama kuralı oluşturabilir, **Windows Updates** etiketini dahil edebilir ve artık Microsoft Windows Update uç noktalarına ağ trafiği güvenlik duvarınız üzerinden akabilir.

Kendi FQDN etiketlerinizi oluşturamaz veya bir etiket içinde hangi FQDN 'lerin dahil edileceğini belirtebilirsiniz. Microsoft, FQDN etiketi ile çevrelenmiş FQDN 'leri yönetir ve etiketi FQDN 'Ler değiştiğinde güncelleştirir. 

<!--- screenshot of application rule with a FQDN tag.-->

Aşağıdaki tabloda kullanabileceğiniz geçerli FQDN etiketleri gösterilmektedir. Microsoft bu etiketleri korur ve ek etiketlerin düzenli aralıklarla eklenmesini bekleyebilir.

## <a name="current-fqdn-tags"></a>Geçerli FQDN etiketleri

|FQDN etiketi  |Açıklama  |
|---------|---------|
|Windows Update     |[Yazılım güncelleştirmeleri Için güvenlik duvarı yapılandırma](https://technet.microsoft.com/library/bb693717.aspx)bölümünde açıklandığı gibi Microsoft Update giden erişime izin verin.|
|Windows Tanılama Özellikleri|Tüm [Windows Tanılama uç noktalarına](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints)giden erişime izin verin.|
|Microsoft Etkin Koruma Hizmeti (MAPS)|[Haritalara](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/)giden erişime izin verin.|
|App Service Ortamı (Ao)|ASA platformu trafiğine giden erişime izin verir. Bu etiket, asa tarafından oluşturulan müşteriye özgü depolama ve SQL uç noktalarını kapsamıyor. Bunların [hizmet uç noktaları](../virtual-network/tutorial-restrict-network-access-to-resources.md) aracılığıyla etkinleştirilmesi veya el ile eklenmesi gerekir.<br><br>Azure Güvenlik duvarını as ile tümleştirme hakkında daha fazla bilgi için bkz. [App Service ortamı kilitleme](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Azure Backup hizmetlerine giden erişime izin verir.|
|Azure HDInsight|HDInsight platform trafiği için giden erişime izin verir. Bu etiket, HDInsight 'tan müşteriye özgü depolamayı veya SQL trafiğini kapsamaz. [Hizmet uç noktalarını](../virtual-network/tutorial-restrict-network-access-to-resources.md) kullanarak bunları etkinleştirin veya el ile ekleyin.|

> [!NOTE]
> Bir uygulama kuralında FQDN etiketi seçilirken protokol: bağlantı noktası alanı **https**olarak ayarlanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik duvarının nasıl dağıtılacağını öğrenmek için bkz. [öğretici: Azure Güvenlik duvarını Azure Portal kullanarak dağıtma ve yapılandırma](tutorial-firewall-deploy-portal.md).
