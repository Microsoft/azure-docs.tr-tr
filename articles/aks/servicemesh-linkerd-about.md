---
title: Linkerd 'ye Genel Bakış
description: Linkerd 'ye genel bakış edinme
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 9d0a65f2cf557120ec5a551494e697c876ad5c59
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530218"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>Genel Bakış

[Linkerd][linkerd] , kullanımı kolay ve hafif bir hizmet ağı.

## <a name="architecture"></a>Mimari

Linkerd, Ultralight [Linkerd][linkerd-proxy] özelleştirilmiş proxy 'lerden oluşan bir veri düzlemi sağlar. Bu akıllı proxy 'ler, yerleşik uygulamalarınızın ve iş yüklerinizin içindeki ve içindeki tüm ağ trafiğini denetler. Proxy 'ler, [Prometheus][prometheus] ölçüm uç noktaları aracılığıyla ölçümleri de kullanıma sunar.

Denetim düzlemi, aşağıdaki [Bileşenler][linkerd-architecture]aracılığıyla yapılandırmayı ve toplu Telemetriyi yönetir:

- **Controller** -Linkerd CLI ve panosunu yönlendiren API sağlar. Proxy 'ler için yapılandırma sağlar.

- **Tap** -istekler ve yanıtlar hakkında gerçek zamanlı izler oluşturun.

- **Kimlik** -hizmetler arasında MTLS 'ye izin veren kimlik ve güvenlik özellikleri sağlar.

- **Web** -Linkerd panosunu sağlar.


Aşağıdaki mimari diyagramda, veri düzlemi ve denetim düzlemi içindeki çeşitli bileşenlerin nasıl etkileşimde bulunduğu gösterilmektedir.


![Linkerd bileşenlerine ve mimarisine genel bakış.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Seçim ölçütü

İş yükleriniz için Linkerd değerlendirilirken aşağıdaki alanların anlaşılması ve dikkate alınması önemlidir:

- [Tasarım Ilkeleri](#design-principles)
- [Yetenek](#capabilities)
- [Senaryolar](#scenarios)


### <a name="design-principles"></a>Tasarım ilkeleri

Aşağıdaki tasarım ilkeleri Linkerd projesini [gösterir][design-principles] :

- **Basit tutun** ; kullanımı ve anlaşılması kolay olmalıdır.

- **Kaynak gereksinimlerini en aza indirin** -minimum performans ve kaynak maliyeti yapın.

- **Yalnızca çalışma** -var olan uygulamaları bozmayın ve karmaşık yapılandırma gerektirmez.


### <a name="capabilities"></a>Yetenekler

Linkerd aşağıdaki yetenek kümesini sağlar:

- **Ağ** – hata ayıklama için yerleşik seçeneği

- **Trafik yönetimi** – bölme, zaman aşımları, yeniden denemeler, giriş

- **Güvenlik** – şifreleme (MTLS), her 24 saatte bir bir sertifika autodöndürüldüğü

- **Observability** – altın ölçümler, dokunma, izleme, hizmet profilleri ve yol ölçümleri başına, topoloji grafikleri ile Web panosu, Prometheus, grafana


### <a name="scenarios"></a>Senaryolar

Linkerd, aşağıdaki senaryolar için uygundur ve önerilir:

- Yalnızca temel özellik gereksinimleri kümesiyle kullanımı basittir

- Observability ve basit trafik yönetimine odaklanarak düşük gecikme süresi, düşük yük


## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki belgeler Azure Kubernetes Service (AKS) üzerinde Linkerd 'yi nasıl yükleyebileceğinizi anlatmaktadır:

> [!div class="nextstepaction"]
> [Azure Kubernetes Service (AKS) içinde Linkerd 'yi kurma][linkerd-install]

Ayrıca, Linkerd özelliklerini ve mimarisini de inceleyebilirsiniz:

- [Linkerd özellikleri][linkerd-features]
- [Linkerd mimarisi][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md