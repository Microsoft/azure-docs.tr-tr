---
title: Traffic Manager Azure App Service kullanan uygulamalar için DNS adlarını yapılandırma
description: Yük Dengeleme için Traffic Manager içeren Azure App Service bir Web uygulaması için özel bir etki alanı adı kullanın.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e6a59d519ae8bb515ab16632bd39509682959f50
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074023"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Azure App Service Traffic Manager kullanarak bir Web uygulaması için özel etki alanı adı yapılandırma
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Bu makale, yük dengeleme için [Traffic Manager](../traffic-manager/traffic-manager-overview.md) tümleştirilmiş [App Service](overview.md) bir uygulamayla özel bir etki alanı adının kullanılmasına yönelik genel yönergeler sağlar.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>DNS kayıtlarını anlama
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Web uygulamalarınızı Standart mod için yapılandırma
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Özel etki alanınız için bir DNS kaydı ekleyin
> [!NOTE]
> Azure App Service aracılığıyla etki alanı satın aldıysanız Web Apps aşağıdaki adımları atlayın ve [Web Apps makale Için etki alanı satın alma](manage-custom-dns-buy-domain.md) adımının son adımına bakın.
> 
> 

Özel etki alanınızı Azure App Service bir Web uygulamasıyla ilişkilendirmek için, özel etki alanınız için DNS tablosuna yeni bir giriş eklemeniz gerekir. Bunu, etki alanı sağlayıcınızdan yönetim araçlarını kullanarak yapabilirsiniz.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Her etki alanı sağlayıcının özellikleri farklılık gösterdiğinden, özel etki alanı adınızla (örneğin, **contoso.com**) Web uygulamanızla tümleştirilmiş Traffic Manager etki alanı adına (**contoso.trafficmanager.net**) eşleyebilirsiniz.

> [!NOTE]
> Bir kayıt zaten kullanımda ise ve uygulamalarınızı buna preemptively bağlamanız gerekiyorsa, ek bir CNAME kaydı oluşturabilirsiniz. Örneğin, preemptively to Web uygulamanıza **bağlamak\.** için, **awverify. www** öğesinden **contoso.trafficmanager.net**'e bir CNAME kaydı oluşturun. "Www" CNAME kaydını değiştirmeden\.Web uygulamanıza "www contoso.com" ekleyebilirsiniz. Daha fazla bilgi için bkz. [özel bir etki alanında bir Web uygulaması IÇIN DNS kayıtları oluşturma][CREATEDNS].

Etki alanı sağlayıcınızda DNS kayıtlarını ekleme veya değiştirme işlemi tamamlandıktan sonra, değişiklikleri kaydedin.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Traffic Manager etkinleştir
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için bkz. [Node.js Geliştirici Merkezi](https://azure.microsoft.com/develop/nodejs/).

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
