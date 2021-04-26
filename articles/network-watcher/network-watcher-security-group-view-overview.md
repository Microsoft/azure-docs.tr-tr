---
title: Azure ağ Izleyicisi 'nde etkin güvenlik kuralları görünümüne giriş | Microsoft Docs
description: Bu sayfada ağ Izleyicisi-etkin güvenlik kuralları görünümü özelliğine genel bir bakış sunulmaktadır
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 54f1ef8f135c16b841df55094327c6bc5be521be
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778615"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Azure ağ Izleyicisi 'nde etkin güvenlik kuralları görünümüne giriş

Ağ güvenlik grupları bir alt ağ düzeyiyle veya NIC düzeyinde ilişkilendirilir. Alt ağ düzeyinde ilişkilendirildiğinde, alt ağdaki tüm VM örnekleri için geçerli olur. Etkin güvenlik kuralları görünümü, yapılandırma hakkında bilgi sağlayan bir sanal makine için NIC ve alt ağ düzeyinde ilişkili tüm yapılandırılmış NSG 'leri ve kuralları döndürür. Ayrıca, etkin güvenlik kuralları bir VM 'deki her NIC için döndürülür. Etkin güvenlik kuralları görünümünü kullanarak, açık bağlantı noktaları gibi ağ güvenlik açıkları için bir sanal makineyi değerlendirebilirsiniz. Ayrıca [, yapılandırılmış ve onaylanmış güvenlik kuralları arasındaki bir karşılaştırmaya](network-watcher-nsg-auditing-powershell.md)göre ağ güvenlik grubunuzun beklenen şekilde çalışıp çalışmadığını doğrulayabilirsiniz.

Daha fazla Genişletilmiş kullanım durumu güvenlik uyumluluğu ve denetiminde olur. Kuruluşunuzda güvenlik yönetimi için bir model olarak, bir güvenlik kuralları kümesi tanımlayabilirsiniz. Düzenli bir uyumluluk denetimi, her bir sanal makine için, her bir sanal makine için etkili kurallarla ilgili kurallı kuralları karşılaştırarak programlı bir şekilde uygulanabilir.

Portal kuralları ' nda her bir ağ arabirimi için görüntülenir ve gelen vs giden ile gruplandırılır. Bu, bir sanal makineye uygulanan kurallara basit bir görünüm sağlar. Bir CSV dosyasına bakılmaksızın tüm güvenlik kurallarını kolayca indirmek için bir indirme düğmesi sağlanır.

![güvenlik grubu görünümü][1]

Kurallar seçilebilir ve ağ güvenlik grubunu ve kaynak ve hedef öneklerini göstermek için yeni bir dikey pencere açılır. Bu dikey pencerede doğrudan ağ güvenlik grubu kaynağına gidebilirsiniz.

!['i][2]

### <a name="next-steps"></a>Sonraki adımlar

*Geçerli güvenlik grupları* özelliğini aşağıda listelenen diğer yöntemlerle de kullanabilirsiniz:
* [REST API](/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [PowerShell](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)
* [Azure CLI](/cli/azure/network/nic#az_network_nic_list_effective_nsg)

[PowerShell Ile ağ güvenlik grubu ayarlarını denetleyin](network-watcher-nsg-auditing-powershell.md) ' i ziyaret ederek ağ güvenlik grubu ayarlarınızı nasıl denetleyeceğinizi öğrenin

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png