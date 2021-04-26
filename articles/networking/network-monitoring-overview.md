---
title: Azure Izleyici günlüklerinde ağ Izleme hakkında | Microsoft Docs
description: Bulutta, şirket içinde ve hibrit ortamlarda ağları yönetmek için NPM dahil ağ izleme çözümlerine genel bakış.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: a92c6789723dc42ac8f3a9c471e494079dc19328
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233449"
---
# <a name="network-monitoring-solutions"></a>Ağ izleme çözümleri 

Azure, Ağ varlıklarınızı izlemek için bir çözüm ana bilgisayarı sunar. Azure 'da ağ bağlantısını izlemeye yönelik çözümler ve yardımcı programlar, ExpressRoute devrelerinin sistem durumu ve bulutta ağ trafiği analiz vardır.

## <a name="network-performance-monitor-npm"></a>Ağ Performansı İzleyicisi (NPM)

Ağ Performansı İzleyicisi (NPM), her biri ağınızın sistem durumunu izlemek, uygulamalarınıza yönelik ağ bağlantısı ve ağınızın performansına ilişkin Öngörüler sağlamak için sunulan bir yetenekler paketidir. NPM bulut tabanlıdır ve arasındaki bağlantıyı izleyen bir karma ağ izleme çözümü sağlar:
 
* Bulut dağıtımları ve şirket içi konumlar
* Birden çok veri merkezi ve şube ofisi
* Görev açısından kritik çok katmanlı uygulamalar/mikro hizmetler
* Kullanıcı konumları ve Web tabanlı uygulamalar (HTTP/HTTPs) 

Performans Izleyicisi, ExpressRoute Izleyicisi ve hizmet bağlantı Izleyicisi, NPM içindeki izleme olanaklardır ve aşağıda açıklanmıştır.

## <a name="performance-monitor"></a>Performans İzleyicisi

Performans Izleyicisi NPM 'nin bir parçasıdır ve bulut, karma ve şirket içi ortamlar için ağ izleme 'dir. Uzak dal ve alan ofisleri, mağaza konumları, veri merkezleri ve bulutlar genelinde ağ bağlantısını izleyebilirsiniz. Kullanıcılarınız şikayet etmeden önce ağ sorunlarını tespit edebilirsiniz. Temel avantajlar şunlardır:

* Çeşitli alt ağlarda kaybı ve gecikmeyi izleyin ve Uyarılar ayarlayın
* Ağdaki tüm yolları (gereksiz yollar dahil) izleme
* Geçici ve zaman içinde ağ sorunlarını giderme, bu da çoğaltma zor
* Ağ üzerinde, performansın düşmesine neden olan belirli bir segmenti belirleme
* SNMP gereksinimi olmadan ağın sistem durumunu izleme

![NPM topoloji eşlemesi](./media/network-monitoring-overview/npm-topology-map.png) 

Daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure Izleyici günlüklerinde bir Ağ Performansı İzleyicisi çözümü yapılandırma](../azure-monitor/insights/network-performance-monitor.md) 
* [Kullanım örnekleri](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor)
* Ürün güncelleştirmeleri:
  * [Şubat 2017](/archive/blogs/msoms/oms-network-performance-monitor-is-now-generally-available)
  * [Ağustos 2017](/archive/blogs/msoms/improvements-to-oms-network-performance-monitor)

## <a name="expressroute-monitor"></a>ExpressRoute Izleyicisi

ExpressRoute için NPM, Azure özel eşleme ve Microsoft eşleme bağlantıları için kapsamlı ExpressRoute izleme sağlar. ExpressRoute üzerinden şubeleriniz ve Azure arasındaki E2E bağlantısını ve performansını izleyebilirsiniz. Temel yetenekler şunlardır:

* Aboneliğinizle ilişkili ER devreleri için otomatik algılama
* Şirket içinden bulut uygulamalarınıza ağ topolojisini algılama
* Kapasite planlama, bant genişliği kullanım Analizi
* Birincil ve ikincil yollarda izleme ve uyarı verme
* Microsoft 365, Dynamics 365,... gibi Azure hizmetleriyle bağlantı izleme ExpressRoute üzerinden
* Sanal ağlara bağlantı düşüşünü Algıla

![Bölge genelinde trafiği gösteren coğrafi eşleme](./media/network-monitoring-overview/expressroute-topology-map.png) 

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

* [ExpressRoute için Ağ Performansı İzleyicisi’ni Yapılandırma](../expressroute/how-to-npm.md)
* [Blog gönderisi](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Hizmet Bağlantısı İzleyicisi

Hizmet bağlantısı izleme ile artık uygulamaların erişilebilirliği test edebilir ve şirket içi, taşıyıcı ağlarda ve bulut/özel veri merkezlerinde performans sorunlarını algılayabilirsiniz.

* Uygulamalara uçtan uca ağ bağlantılarını izleme
* Uygulama teslimatını ağ performansıyla ilişkilendirme, Kullanıcı ve uygulama arasındaki yol üzerinde düşme düşüşünün tam konumunu Algıla
* Dünya genelinde birden çok Kullanıcı konumundan uygulama erişilebilirliği
* İş kolu ve SaaS uygulamalarınız için ağ gecikme süresini ve paket kaybını belirleme
* Ağdaki etkin noktaları belirleme, bu durum kötü uygulama performansına neden olabilir
* Microsoft 365, Dynamics 365, Skype Kurumsal ve diğer Microsoft Hizmetleri için yerleşik testler kullanarak Microsoft 365 uygulamalara yönelik erişilebilirliği izleyin

Daha fazla bilgi için aşağıdaki makaleleri inceleyin:

* [İzleme hizmeti uç noktaları için Ağ Performansı İzleyicisi yapılandırma](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Blog gönderisi](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Trafik Analizi
Trafik Analizi bulut ağlarınızdaki Kullanıcı ve uygulama etkinliğine görünürlük sağlayan bulut tabanlı bir çözümdür. NSG akış günlükleri, hakkında Öngörüler sunacak şekilde çözümlenir:

* Azure ile Internet, genel bulut bölgeleri, sanal ağlar ve alt ağlar arasındaki ağlardaki trafik akışları
* Tanıcıların veya adanmış akış toplayıcı gereçlerine gerek olmadan ağınızdaki uygulamalar ve protokoller
* En iyi talranlar, geveze uygulamaları, buluttaki VM konuşmaları, trafik etkin noktaları
* VNET 'ler genelinde trafiğin kaynakları ve hedefleri, kritik iş hizmetleri ve uygulamalar arasındaki ilişkiler
* Güvenlik – kötü amaçlı trafik, internet 'e açık olan bağlantı noktaları, uygulamalar veya sanal makineler Internet erişimi girişiminde bulunuyor...
* Kapasite kullanımı-VPN ağ geçitlerinin ve diğer hizmetlerin kullanım eğilimlerini izleyerek aşırı sağlama veya yetersiz kullanım sorunlarını ortadan kaldırmanıza yardımcı olur

Trafik Analizi, kuruluşunuzun ağ etkinliğini denetlemenize, uygulamaları ve verileri güvenli hale getirmenize, iş yükü performansını iyileştirmenize ve uyumlu kalmanıza yardımcı olan, işlem yapılabilir bilgiler sağlar.

![Coğrafi eşleme, bölgeler arasında trafiği gösteriyor 2](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

İlgili bağlantılar:
* [Blog gönderisi](https://aka.ms/trafficanalytics), [Belgeler](../network-watcher/traffic-analytics.md), [SSS](../network-watcher/traffic-analytics-faq.md)

## <a name="dns-analytics"></a>DNS Analizi
DNS yöneticileri için tasarlanan bu çözüm, güvenlik, işlemler ve performansla ilgili Öngörüler sağlamak için DNS günlüklerini toplar, çözümler ve birbirleriyle ilişkilendirir.  Bazı yetenekler şunlardır:

* Kötü amaçlı etki alanlarına çözümlemeye çalışır olan istemcilerin tanımlanması
* Eski kaynak kayıtlarının tanımlanması
* Sık sorgulanan etki alanı adlarına ve bu DNS istemcilerine yönelik görünürlük
* DNS sunucularında istek yükünün görünürlüğü
* Dinamik DNS kayıt hatalarının izlenmesi

![DNS Analizi panosu](./media/network-monitoring-overview/dns-analytics-overview.png) 

İlgili bağlantılar:
* [Blog gönderisi](/archive/blogs/msoms/introducing-oms-dns-analytics), [Belgeler](../azure-monitor/insights/dns-analytics.md)

## <a name="miscellaneous"></a>Çeşitli

* [Yeni fiyatlandırma](../azure-monitor/insights/network-performance-monitor-pricing-faq.md)