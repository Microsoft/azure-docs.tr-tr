---
title: Azure portal URL 'Lerini güvenli listeleyin | Microsoft Docs
description: Azure portal ve hizmetleriyle iletişim kurmak için bu URL 'Leri ara sunucu geçişine ekleyin
services: azure-portal
keywords: ''
author: kfollis
ms.author: kfollis
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 9618d99495e65231de22eb719355e2ca75a17600
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71218047"
---
# <a name="safelist-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>Güvenlik duvarınızdaki veya proxy sunucunuzdaki Azure portal URL 'Lerini listeleyin

Yerel veya geniş alan ağınız ile Azure bulutu arasında iyi performans ve bağlantı sağlamak için şirket içi güvenlik cihazlarını, Azure portal URL 'Lerinin güvenlik kısıtlamalarını atlayacak şekilde yapılandırın. Ağ yöneticileri, kullanıcıların İnternet 'e nasıl erişebileceği konusunda güvenli hale getirmek ve denetim sağlamak için genellikle proxy sunucuları, güvenlik duvarları veya diğer cihazları dağıtır. Ancak, kullanıcıları korumak için tasarlanan kurallar, siz ve Azure arasındaki iletişimler de dahil olmak üzere, işletmeyle ilgili yasal internet trafiğini engelleyebilir veya yavaşlatır. Ağınızla Azure portal ve hizmetleri arasındaki bağlantıyı iyileştirmek için, SafeList verilerinize Azure portal URL eklemenizi öneririz.

## <a name="azure-portal-urls-for-proxy-bypass"></a>Proxy atlama için Azure portal URL 'Leri

Azure portal için güvenli listeye yönelik URL uç noktaları, kuruluşunuzun dağıtıldığı Azure bulutuna özgüdür. Bulutunuzu seçin, sonra bu uç noktalara yönelik ağ trafiğinin kısıtlamaları atlamasına izin vermek için proxy sunucunuza veya güvenlik duvarına URL listesini ekleyin.

#### <a name="public-cloudtabpublic-cloud"></a>[Genel bulut](#tab/public-cloud)
```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azureafd.net
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

#### <a name="us-government-cloudtabus-government-cloud"></a>[ENGELL Kamu Bulutu](#tab/us-government-cloud)
```
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloudtabchina-government-cloud"></a>[Çin Devlet bulutu](#tab/china-government-cloud)
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
## <a name="next-steps"></a>Sonraki adımlar

IP adreslerini güvenli listeye almanız mı gerekiyor? Bulutunuz için Microsoft Azure veri merkezi IP aralıklarının listesini indirin:

* [Dünya çapında](https://www.microsoft.com/download/details.aspx?id=56519)
* [ENGELL Devlet](https://www.microsoft.com/download/details.aspx?id=57063)
* [Almanya](https://www.microsoft.com/download/details.aspx?id=57064)
* [Çin](https://www.microsoft.com/download/details.aspx?id=57062)

Diğer Microsoft Hizmetleri, bağlantı için ek URL 'Ler ve IP adresleri kullanır. Microsoft 365 hizmetleri için ağ bağlantısını iyileştirmek için bkz. [ağınızı Office 365 Için ayarlama](/office365/enterprise/set-up-network-for-office-365).
