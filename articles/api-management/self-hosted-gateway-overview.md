---
title: Şirket içinde barındırılan ağ geçidine genel bakış | Microsoft Docs
description: Azure API Management şirket içinde barındırılan ağ geçidi özelliğinin, kuruluşların karma ve çok yüksek ortamlarda API 'Leri yönetmesine yardımcı olduğunu öğrenin.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/25/2021
ms.author: apimpm
ms.openlocfilehash: 48abce693ca22163c0a1742ba71faf36fc6156a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99989096"
---
# <a name="self-hosted-gateway-overview"></a>Şirket içinde barındırılan ağ geçidine genel bakış

Bu makalede, Azure API Management 'in şirket içinde barındırılan ağ geçidi özelliğinin karma ve çok bulut API yönetimini nasıl sağladığından, üst düzey mimarisini nasıl sunduğu ve özelliklerini vurguladığı açıklanır.

## <a name="hybrid-and-multi-cloud-api-management"></a>Karma ve çoklu bulut API Yönetimi

Şirket içinde barındırılan ağ geçidi özelliği, karma ve çok bulut ortamları için API Management destekler ve kuruluşların şirket içinde ve bulutta barındırılan API 'Leri Azure 'daki tek bir API Management hizmetinden verimli ve güvenli bir şekilde yönetmesine olanak sağlar.

Şirket içinde barındırılan ağ geçidiyle, müşteriler, API Management ağ geçidi bileşeninin kapsayıcılı bir sürümünü, API 'Lerini barındırdıkları ortamlara dağıtma esnekliği sunar. Şirket içinde barındırılan tüm ağ geçitleri, tüm iç ve dış API 'Ler genelinde görünürlük ve Birleşik yönetim deneyimi sunarak müşterilere, içinde yer aldığı API Management hizmetten yönetilir. Ağ geçitlerinin API 'lere yerleştirilmesi, müşterilerin API trafik akışlarını iyileştirmelerine ve güvenlik ve uyumluluk gereksinimlerini karşıçalıştırmasına izin verir.

Her bir API Management hizmeti aşağıdaki anahtar bileşenlerinden oluşur:

-   Bir API olarak sunulan yönetim düzlemi, Azure portal, PowerShell ve diğer desteklenen mekanizmalar aracılığıyla hizmeti yapılandırmak için kullanılır.
-   Ağ Geçidi (veya veri düzlemi), API isteklerinin proxy isteklerini, ilkelerin uygulanmasını ve Telemetriyi toplamayı sağlamaktan sorumludur
-   Geliştiriciler tarafından API 'Leri bulmayı, öğrenmeyi ve kullanmayı öğrenmek için kullanılan geliştirici portalı

Varsayılan olarak, tüm bu bileşenler Azure 'da dağıtılır ve API 'Lerin alt uçlarının barındırıldığından bağımsız olarak, tüm API trafiğinin Azure üzerinden akmasını sağlar. Bu modelin operasyonel basitliği, artan gecikme süresi, uyumluluk sorunları ve bazı durumlarda ek veri aktarımı ücretleri üzerinden gelir.

![Kendi kendine barındırılan ağ geçitleri olmadan API trafik akışı](media/self-hosted-gateway-overview/without-gateways.png)

Şirket içinde barındırılan ağ geçitlerini arka uç API uygulamalarının barındırıldığı ortamlara dağıtmak, API trafiğinin doğrudan arka uç API 'Lerine akmasını sağlar, gecikme süresini iyileştirir, veri aktarım maliyetlerini iyileştirir ve uygulamaların barındırıldığı yere bakılmaksızın kuruluştaki tüm API 'lerin tek bir yönetimi, Observability ve keşfedilmesi avantajlarını korurken uyumluluk sağlar.

![Şirket içinde barındırılan ağ geçitleriyle API trafik akışı](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Paketleme ve Özellikler

Şirket içinde barındırılan ağ geçidi, her API Management hizmetin bir parçası olarak Azure 'a dağıtılan yönetilen ağ geçidinin Kapsayıcılı, işlevsel olarak denk bir sürümüdür. Şirket içinde barındırılan ağ geçidi, Microsoft Container Registry Linux tabanlı bir Docker [kapsayıcısı](https://aka.ms/apim/sputnik/dhub) olarak kullanılabilir. Docker, Kubernetes veya Şirket içindeki bir sunucu kümesinde çalışan, bulut altyapısındaki veya değerlendirme ve geliştirme amaçları için kişisel bir bilgisayardaki herhangi bir kapsayıcı düzenleme çözümüne dağıtılabilir.

Yönetilen ağ geçitlerinde bulunan aşağıdaki işlevsellik, şirket içinde barındırılan ağ geçitlerinde **kullanılamaz** :

- Azure İzleyici günlükleri
- Yukarı akış (arka uç tarafı) TLS sürümü ve şifre yönetimi
- API Management hizmetine yüklenen [CA kök sertifikalarını](api-management-howto-ca-certificates.md) kullanarak sunucu ve istemci sertifikalarının doğrulanması. Daha fazla bilgi için, bkz. [Şirket içinde barındırılan ağ geçidinde sertifika doğrulaması](api-management-howto-mutual-certificates-for-clients.md#certificate-validation-in-self-hosted-gateway).
- [Service Fabric](../service-fabric/service-fabric-api-management-overview.md) ile tümleştirme
- TLS oturum sürdürme
- İstemci sertifikası yeniden anlaşması. Diğer bir deyişle, [istemci sertifikası kimlik doğrulamasının](api-management-howto-mutual-certificates-for-clients.md) iş API tüketicileri, ilk TLS el sıkışma kapsamında sertifika sunması gerekir. Bunun emin olmak için, şirket içinde barındırılan bir ağ geçidi özel ana bilgisayar adı yapılandırırken istemci sertifikası ayarını etkinleştirin.
- Yerleşik önbellek. Şirket içinde barındırılan ağ geçitlerinde dış önbellek kullanma hakkında bilgi edinmek için bu [belgeye](api-management-howto-cache-external.md) bakın.

## <a name="connectivity-to-azure"></a>Azure ile bağlantı

Şirket içinde barındırılan ağ geçitleri 443 numaralı bağlantı noktasında Azure 'a giden TCP/IP bağlantısı gerektirir. Şirket içinde barındırılan her ağ geçidinin tek bir API Management hizmetiyle ilişkilendirilmesi ve yönetim düzlemi aracılığıyla yapılandırılması gerekir. Şirket içinde barındırılan ağ geçidi, için Azure bağlantısını kullanır:

-   Her dakikada sinyal mesajları göndererek durumunu raporlama
-   Düzenli olarak (10 saniyede bir) denetleniyor ve her kullanılabilir olduğunda yapılandırma güncelleştirmeleri uygulanıyor
-   Bu şekilde yapılandırıldıysa, Azure Izleyici 'ye istek günlükleri ve ölçümleri gönderme
-   Application Insights olarak ayarlandıysa olayları gönderme

Azure ile bağlantı kesildiğinde, şirket içinde barındırılan ağ geçidi yapılandırma güncelleştirmelerini alamıyor, durumunu bildiremeyecek veya Telemetriyi karşıya yükleyemeyecektir.

Şirket içinde barındırılan ağ geçidi, "başarısız statik" olarak tasarlanmıştır ve Azure ile geçici bağlantı kaybını da sürdürmektedir. Yerel yapılandırma yedeklemesi olmadan veya olmadan dağıtılabilir. Önceki durumda, şirket içinde barındırılan ağ geçitleri, en son indirilen yapılandırmanın bir yedek kopyasını, kapsayıcısına veya Pod 'a bağlı kalıcı bir birimde düzenli olarak kaydeder.

Yapılandırma Yedeklemesi devre dışı bırakıldığında ve Azure bağlantısı kesintiye uğradığında:

-   Şirket içinde barındırılan ağ geçitlerinin çalıştırılması, yapılandırmanın bellek içi bir kopyasını kullanarak çalışmaya devam edecektir
-   Kendi kendine barındırılan ağ geçitleri başlatılamaz

Yapılandırma Yedeklemesi açık olduğunda ve Azure ile bağlantı kesildiğinde:

-   Şirket içinde barındırılan ağ geçitlerinin çalıştırılması, yapılandırmanın bellek içi bir kopyasını kullanarak çalışmaya devam edecektir
-   Şirket içinde barındırılan ağ geçitleri, yapılandırmanın bir yedek kopyasını kullanmaya başlayabilecektir

Bağlantı geri yüklendiğinde, kesintiden etkilenen her bir şirket içinde barındırılan ağ geçidi, ilişkili API Management hizmetine otomatik olarak yeniden bağlanır ve ağ geçidi "çevrimdışı" iken gerçekleşen tüm yapılandırma güncelleştirmelerini indirir.

## <a name="next-steps"></a>Sonraki adımlar

-   [Bu konuda ek arka plan için bir teknik incelemeyi okuyun](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Şirket içinde barındırılan ağ geçidini Docker 'a dağıtma](how-to-deploy-self-hosted-gateway-docker.md)
-   [Kubernetes 'e şirket içinde barındırılan ağ geçidi dağıtma](how-to-deploy-self-hosted-gateway-kubernetes.md)
