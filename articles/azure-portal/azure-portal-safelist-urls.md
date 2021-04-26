---
title: Güvenlik duvarınızda veya ara sunucunuzda Azure portalı URL'lerini güvenli listeye alma
description: Azure portal ve hizmetleriyle iletişim kurmak için bu URL 'Leri ara sunucu geçişine ekleyin
ms.date: 04/10/2020
ms.topic: conceptual
ms.openlocfilehash: 7d9c8222ee85c0c16ec1e1926335ac06e0389797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745885"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Güvenlik duvarınızda veya ara sunucunuzda Azure portalı URL'lerini güvenli listeye alma

Şirket içi güvenlik cihazlarını, Azure portal URL 'Lerinin güvenlik kısıtlamalarını atlayacak şekilde yapılandırabilirsiniz. Bu yapılandırma, yerel veya geniş alan ağınız ile Azure bulutu arasındaki performansı ve bağlantıyı iyileştirebilir.

Ağ yöneticileri genellikle proxy sunucuları, güvenlik duvarları veya diğer cihazları dağıtır. Bu cihazlar güvenli hale getirmeye yardımcı olur ve kullanıcıların İnternet 'e nasıl erişkullanılacağına yönelik denetim sağlar. Kullanıcıları korumak için tasarlanan kurallar, işle ilgili yasal internet trafiğini bazen engelleyebilir veya yavaşlatır. Bu trafik, siz ve Azure arasındaki iletişimleri içerir. Ağınızla Azure portal ve hizmetleri arasındaki bağlantıyı iyileştirmek için, SafeList verilerinize Azure portal URL eklemenizi öneririz.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Proxy atlama için Azure portal URL 'Leri

Azure portal için güvenli listeye yönelik URL uç noktaları, kuruluşunuzun dağıtıldığı Azure bulutuna özgüdür. Bu uç noktalara yönelik ağ trafiğine kısıtlama atlama izni vermek için bulutunuzu seçin. Ardından, proxy sunucunuza veya güvenlik duvarına URL listesi ekleyin.

#### <a name="public-cloud"></a>[Genel bulut](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azurefd.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[ABD kamu bulutu](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[Çin Devlet bulutu](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> Bu uç noktalara giden trafik, HTTP (80) ve HTTPS (443) için standart TCP bağlantı noktalarını kullanır.
>
>
