---
title: Traffic Manager-Azure Traffic Manager bir Internet etki alanı noktası oluşturma
description: Bu makale, şirketinizin etki alanı adını Traffic Manager etki alanı adına yönlendirmenize yardımcı olacaktır.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: duau
ms.openlocfilehash: e0e2acfb0ec0068dcd08ae660e397f65e039a665
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183753"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Bir şirketin İnternet etki alanını Azure Traffic Manager etki alanına yönlendirme

Traffic Manager profili oluşturduğunuzda, Azure bu profil için otomatik olarak bir DNS adı atar. DNS bölgenizdeki bir adı kullanmak için Traffic Manager profilinizin etki alanı adıyla eşleşen bir CNAME DNS kaydı oluşturun. Traffic Manager etki alanı adını, Traffic Manager profilinin Yapılandırma sayfasındaki **Genel** bölümünde bulabilirsiniz.

Örneğin, `www.contoso.com` adıyla Traffic Manager `contoso.trafficmanager.net` DNS adına işaret etmek için, aşağıdaki DNS kaynak kaydını oluşturursunuz:

`www.contoso.com IN CNAME contoso.trafficmanager.net.`

*Www \. contoso.com* 'e giden tüm trafik istekleri *contoso.trafficmanager.net*'e yönlendirilir.

> [!IMPORTANT]
> *contoso.com* gibi ikinci düzey bir etki alanını Traffic Manager etki alanına yönlendiremezsiniz. DNS protokolü standartları, ikinci düzey etki alanı adları için CNAME kayıtlarına izin vermez.

## <a name="next-steps"></a>Sonraki adımlar

* [Traffic Manager yönlendirme yöntemleri](traffic-manager-routing-methods.md)
* [Traffic Manager - Bir profili devre dışı bırakma, etkinleştirme veya silme](./traffic-manager-manage-profiles.md)
* [Traffic Manager - Bir uç noktayı devre dışı bırakma veya etkinleştirme](./traffic-manager-manage-endpoints.md)