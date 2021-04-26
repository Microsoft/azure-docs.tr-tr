---
title: Azure portal Azure Güvenlik Duvarı Premium önizlemesi
description: Azure portal Azure Güvenlik Duvarı Premium önizlemesi hakkında bilgi edinin.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3d56fc73faeb0d48ba7e5b21449c61d6213afa40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100549833"
---
# <a name="azure-firewall-premium-preview-in-the-azure-portal"></a>Azure portal Azure Güvenlik Duvarı Premium önizlemesi

> [!IMPORTANT]
> Azure Güvenlik Duvarı Premium Şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Güvenlik Duvarı Premium önizleme, yüksek düzeyde hassas ve düzenlenmiş ortamlar için gereken özellikleri içeren bir sonraki nesil güvenlik duvarıdır. Aşağıdaki özellikleri içerir:

- **TLS incelemesi** -giden trafiğin şifresini çözer, verileri işler, ardından verileri şifreler ve hedefe gönderir.
- **IDPs** -ağ izinsiz giriş algılama ve önleme SISTEMI (IDPs), kötü amaçlı etkinlik için ağ etkinliklerini izlemenize, bu etkinlikle ilgili bilgileri günlüğe almanıza, raporlamayla ve isteğe bağlı olarak engellemeyi denemenize olanak tanır.
- **URL Filtresi** -Azure GÜVENLIK duvarının FQDN filtreleme özelliğini genişleterek tüm URL 'yi düşünün. Örneğin, `www.contoso.com/a/c` yerine `www.contoso.com` .
- **Web kategorileri** -Yöneticiler, kumar web siteleri, sosyal medya web siteleri ve diğerleri gibi web sitesi kategorilerine Kullanıcı erişimine izin verebilir veya erişimi reddedebilir.

Daha fazla bilgi için bkz. [Azure Güvenlik Duvarı Premium özellikleri](premium-features.md).

## <a name="deploy-the-firewall"></a>Güvenlik duvarını dağıtma

Azure Güvenlik Duvarı Premium önizlemesini dağıtmak, standart bir Azure Güvenlik Duvarı dağıtmaya benzer.

:::image type="content" source="media/premium-portal/premium-portal.png" alt-text="Portal dağıtımı":::

**Güvenlik duvarı katmanı** için **Premium (Önizleme)** seçeneğini belirleyin ve **güvenlik duvarı ilkesi** için mevcut bir Premium ilkesi seçin ya da yeni bir tane oluşturun.

## <a name="configure-the-premium-policy"></a>Premium ilkesini yapılandırma

Premium güvenlik duvarı ilkesini yapılandırmak, standart bir güvenlik duvarı ilkesini yapılandırmaya benzer. Premium ilkeyle, Premium özellikleri yapılandırabilirsiniz:

:::image type="content" source="media/premium-portal/premium-policy.png" alt-text="Premium ilke dağıtımı":::

### <a name="rule-configuration"></a>Kural yapılandırması

Uygulama kurallarını bir Premium ilkesinde yapılandırdığınızda, ek Premium özellikleri yapılandırabilirsiniz:

:::image type="content" source="media/premium-portal/premium-application-rule.png" alt-text="Premium kural":::

## <a name="next-steps"></a>Sonraki adımlar

Azure Güvenlik Duvarı Premium önizleme özelliklerini çalışırken görmek için bkz. [Azure Güvenlik Duvarı Premium önizlemesini dağıtma ve yapılandırma](premium-deploy.md).