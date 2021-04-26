---
title: Kayıt defteri performansı sorunlarını giderme
description: Bir kayıt defterinin performansı ile ilgili genel sorunların belirtileri, nedenleri ve çözümlenmesi
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 15129ebe1da2e52fac106a34863f609c440549ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148404"
---
# <a name="troubleshoot-registry-performance"></a>Kayıt defteri performansı sorunlarını giderme

Bu makale, bir Azure Container Registry 'nin performansıyla karşılaşacağınız sorunları gidermenize yardımcı olur. 

## <a name="symptoms"></a>Belirtiler

Aşağıdakilerden birini veya daha fazlasını içerebilir:

* Docker CLı ile çekme veya anında iletme görüntüleri beklenenden uzun sürüyor
* Görüntülerin Azure Kubernetes hizmeti gibi bir hizmete dağıtılması beklenenden uzun sürüyor
* Beklenen sürede çok sayıda eş zamanlı çekme veya gönderme işlemini tamamlayamıyoruz
* Coğrafi olarak çoğaltılan bir kayıt defterindeki çekme veya gönderme işlemleri beklenenden uzun sürüyor veya gönderim hata ile başarısız oluyor `Error writing blob` veya `Error writing manifest`

## <a name="causes"></a>Nedenler

* Ağ bağlantı hızlarınız, kayıt defteri işlemlerini yavaşlatabilir- [çözüm](#check-expected-network-speed)
* İstemci [çözümünde](#check-client-hardware) görüntü katmanı sıkıştırması veya ayıklama yavaş olabilir  
* Kayıt defteri hizmet katmanınızda veya ortamınızda yapılandırılmış bir sınıra ulaşıyoruz- [çözüm](#review-configured-limits)
* Coğrafi olarak çoğaltılan kayıt defterinizin yakın bölgelerde çoğaltmaları vardır- [çözüm](#configure-geo-replicated-registry)
* Coğrafi olarak uzak bir kayıt defteri çoğaltmasından çekiyoruz- [çözüm](#configure-dns-for-geo-replicated-registry)

Sorununuzu burada çözemezseniz, bkz. [Gelişmiş sorun giderme](#advanced-troubleshooting) ve diğer seçenekler için [sonraki adımlar](#next-steps) .

## <a name="potential-solutions"></a>Olası çözümler

### <a name="check-expected-network-speed"></a>Beklenen ağ hızını denetleyin

İnternet karşıya yükleme ve indirme hızınızı denetleyin veya Azure Blob depolama alanından [karşıya yükleme](https://www.azurespeed.com/Azure/Uploadß) ve [indirmeyi](https://www.azurespeed.com/Azure/Download) test etmek için, kayıt defteri görüntü katmanlarını barındıran bir araç kullanın.

Görüntü boyutunuzu desteklenen maksimum boyut ve desteklenen indirme ya da kayıt defteri hizmet katmanınız için karşıya yükleme bant genişliğine karşı denetleyin. Kayıt defteriniz temel veya standart katmanındaysa, performansı artırmak için yükseltmeyi göz önünde bulundurun. 

Diğer hizmetlere görüntü dağıtımı için, kayıt defteri ve hedefin bulunduğu bölgeleri kontrol edin. Performansı geliştirmek için kayıt defterini ve dağıtım hedefini aynı veya ağ kapalı bölgelerinde konumlandırmayı göz önünde bulundurun.

İlgili bağlantılar:

* [Azure Container Registry hizmet katmanları](container-registry-skus.md)    
* [Kapsayıcı kayıt defteri SSS](container-registry-faq.md)
* [Azure Blob depolama için performans ve ölçeklenebilirlik hedefleri](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>İstemci donanımını denetle

Docker istemcisindeki disk türü ve CPU, çekme veya anında iletme işlemlerinin bir parçası olarak istemcideki Görüntü katmanlarını ayıklama veya sıkıştırma hızını etkileyebilir. Örneğin, bir sabit disk sürücüsünde katman ayıklama, katı hal diskinden daha uzun sürer. Azure Container Registry 'nizden ve Docker Hub gibi genel bir kayıt defterinde bulunan karşılaştırılabilir görüntüler için çekme işlemlerini karşılaştırın.

### <a name="review-configured-limits"></a>Yapılandırılan sınırları gözden geçir

Aynı anda birden çok katmanlı görüntüyü Kayıt defterinize bir araya getiriyorsanız veya çekerek, kayıt defteri hizmet katmanı için desteklenen ReadOps ve WriteOps sınırlarını gözden geçirin. Kayıt defteriniz temel veya standart katmanındaysa, sınırları artırmak için yükseltmeyi göz önünde bulundurun. Birçok eşzamanlı işlemde oluşabilecek ağ daraltma hakkında da ağ sağlayıcınızla görüşün. 

İstemci üzerindeki her gönderim veya çekme işlemi için en fazla eşzamanlı karşıya yükleme veya indirme işlemleri için Docker Daemon yapılandırmanızı gözden geçirin. Gerekirse daha yüksek limitleri yapılandırın.

Her görüntü katmanı için ayrı bir kayıt defteri okuma veya yazma işlemi gerektiğinden, görüntülerinizin katman sayısını denetleyin. Görüntü katmanı sayısını azaltmak için stratejileri göz önünde bulundurun.

İlgili bağlantılar:

* [Azure Container Registry hizmet katmanları](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>Coğrafi olarak çoğaltılan kayıt defterini yapılandırma

Bir görüntüyü coğrafi olarak çoğaltılan bir kayıt defterine ileten bir Docker istemcisi, tüm görüntü katmanlarını ve bildirimini tek bir çoğaltılan bölgeye gönderemeyebilir. Azure Traffic Manager kayıt defteri isteklerini ağa en yakın çoğaltılan kayıt defterine yönlendirtiğinden bu durum ortaya çıkabilir. Kayıt defterinde yakın iki çoğaltma bölgesi varsa, görüntü katmanları ve bildirim iki siteye dağıtılabilir ve bildirim doğrulandığında gönderme işlemi başarısız olur.

Görüntüleri gönderirken en yakın çoğaltma ile DNS çözümlemesini iyileştirmek için, çekme işlemlerinin kaynağıyla aynı Azure bölgelerinde coğrafi olarak çoğaltılan bir kayıt defteri veya Azure dışında çalışırken en yakın bölgeyi yapılandırın.

Coğrafi olarak çoğaltılan bir kayıt defteriyle ilgili sorunları gidermek için, bir veya daha fazla çoğaltma için Traffic Manager yönlendirmeyi geçici olarak devre dışı bırakabilirsiniz.

İlgili bağlantılar:

* [Azure Container Registry coğrafi çoğaltma](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>Coğrafi olarak çoğaltılan kayıt defteri için DNS yapılandırma

Coğrafi olarak çoğaltılan bir kayıt defterindeki çekme işlemleri yavaş görünüyorsa, istemcideki DNS yapılandırması coğrafi olarak uzak bir DNS sunucusuna çözümleyebilir. Bu durumda, Traffic Manager DNS sunucusuna ağ yakın, ancak istemciden uzak olan bir çoğaltmaya yönlendirme istekleri olabilir. `nslookup` `dig` Kayıt defteri isteklerini yönlendirdiğini Traffic Manager çoğaltmayı öğrenmek için veya (Linux üzerinde) gibi bir araç çalıştırın. Örnek:

```console
nslookup myregistry.azurecr.io
```

Olası bir çözüm, daha yakın bir DNS sunucusu yapılandırmaktır.

İlgili bağlantılar:

* [Azure Container Registry coğrafi çoğaltma](container-registry-geo-replication.md)
* [Coğrafi olarak çoğaltılan kayıt defterlerine gönderim işlemleri sorunlarını giderme](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [Çoğaltmaya yönlendirmeyi geçici olarak devre dışı bırak](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Traffic Manager SSS](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>Gelişmiş sorun giderme

Kayıt defteri kaynakları için izinleriniz izin veriyor ise, [kayıt defteri ortamının sistem durumunu kontrol](container-registry-check-health.md)edin. Hatalar raporlandıysanız, olası çözümler için [hata başvurusunu](container-registry-health-error-reference.md) gözden geçirin.

Kayıt defterinde [kaynak günlüklerinin toplanması](container-registry-diagnostics-audit-logs.md) etkinse, Kirınterregistrydepotoryevents günlüğünü gözden geçirin. Bu günlük, gönderim veya çekme olayları gibi işlemlerle ilgili bilgileri depolar. [Depolama düzeyi işlem hatalarıyla](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures)ilgili günlüğü sorgulayın. 

İlgili bağlantılar:

* [Tanılama değerlendirmesi ve denetimi günlükleri](container-registry-diagnostics-audit-logs.md)
* [Kapsayıcı kayıt defteri SSS](container-registry-faq.md)
* [Azure Container Registry için en iyi yöntemler](container-registry-best-practices.md)

## <a name="next-steps"></a>Sonraki adımlar

Sorununuzu burada çözemezseniz, aşağıdaki seçeneklere bakın.

* Diğer kayıt defteri sorunlarını giderme konuları şunları içerir:
  * [Kayıt defteri oturum açma sorunlarını giderme](container-registry-troubleshoot-login.md)
  * [Kayıt defteri ile ağ sorunlarını giderme](container-registry-troubleshoot-access.md)
* [Topluluk desteği](https://azure.microsoft.com/support/community/) seçenekleri
* [Microsoft Soru-Cevap](/answers/products/)
* [Destek bileti açma](https://azure.microsoft.com/support/create-ticket/)