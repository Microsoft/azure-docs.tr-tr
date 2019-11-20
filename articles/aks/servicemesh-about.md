---
title: Hizmet kafesleri hakkında
description: Hizmet kafeslerine, mimarisine ve özelliklerine ve dağıtım için bir tane seçerken göz önünde bulundurmanız gereken ölçütlere genel bir bakış elde edin.
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 226a4e5cf97be2e23ef13a95b80be07b7fbf5d7a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530712"
---
# <a name="about-service-meshes"></a>Hizmet kafesleri hakkında

Hizmet ağı, iş yüklerinize trafik yönetimi, dayanıklılık, ilke, güvenlik, güçlü kimlik ve Observability gibi yetenekler sağlar. Uygulamanız bu operasyonel yetenekler ' den ayrılır ve hizmet ağı, bunları uygulama katmanının dışına ve altyapı katmanına doğru şekilde taşımıştır.

## <a name="scenarios"></a>Senaryolar

Hizmet ağı kullandığınızda iş yükleriniz için etkinleştirilemeyen bazı senaryolar şunlardır:

- **Kümedeki tüm trafiği şifreleyin** -kümedeki belirtilen hizmetler arasında karşılıklı TLS 'yi etkinleştirin. Bu, ağ çevre ağındaki giriş ve çıkış için genişletilebilir. Uygulama kodu ve altyapısı için hiçbir değişiklik gerektirmeden, varsayılan olarak güvenli bir seçenek sağlar.

- **Canary ve aşamalı piyasaya çıkarma** -bir trafik alt kümesinin kümedeki yeni hizmetler kümesine yönlendirilme koşullarını belirtin. Kanarya sürümünün başarılı testi sırasında, koşullu yönlendirmeyi ve aşamayı, tüm trafiğin% yavaş bir şekilde yeni hizmete artırdığı şekilde kaldırın. Sonuç olarak tüm trafik yeni hizmete yönlendirilir.

- **Trafik yönetimi ve düzenleme** -bir hizmette, belirli bir kaynaktan bir hizmetin sürümüyle olan tüm trafiği sınırlamak için bir ilke oluşturun. Veya belirtilen hizmetler arasındaki başarısızlık sınıfları için yeniden deneme stratejisi uygulayan bir ilke. Geçiş sırasında veya hata ayıklama sırasında yeni hizmet sürümlerine canlı trafik yansıtın. Dayanıklılık testi için bir test ortamındaki hizmetler arasında hata ekleme.

- **Observability** -hizmetlerinizin aralarında akan trafiği nasıl bağladığınıza ilişkin Öngörüler elde edin. Kümedeki tüm trafiğe yönelik ölçümleri, günlükleri ve izlemeleri ve giriş/çıkış alma. Uygulamalarınıza dağıtılmış izleme becerileri ekleyin.

## <a name="architecture"></a>Mimari

Hizmet ağı genellikle bir denetim düzlemi ve veri düzleminden oluşur.

**Denetim düzlemi** , hizmet ağı yönetimini destekleyen bir dizi bileşene sahiptir. Bu, genellikle bir kullanıcı arabirimi veya API olabilecek bir yönetim arabirimi içerir. Ayrıca, hizmet ağı 'nın belirli özellikleri nasıl uygulaması gerektiğini tanımlayan kural ve ilke tanımlarını yöneten bileşenler de vardır. Ayrıca, mTLS için güçlü kimlik ve sertifikalar gibi güvenlik yönlerini yöneten bileşenler de vardır. Hizmet kafeslerinin ayrıca iş yüklerinden ölçümleri ve telemetri toplayan ve toplayan bir ölçümleri veya Observability bileşeni de olur.

**Veri düzlemi** genellikle iş yüklerinize bir sepet olarak eklenen bir ara sunucudan oluşur. Bu proxy, iş yükünüzü içeren Pod 'ın içindeki ve olmayan tüm ağ trafiğini denetlemek üzere yapılandırılmıştır. Bu, proxy 'nin mTLS aracılığıyla trafiği güvenli hale getirmek, trafiği dinamik olarak yönlendirmek, trafiğe ilke uygulamak ve ölçümleri ve izleme bilgilerini toplamak için yapılandırılmasını sağlar. 

![Tipik hizmet kafes mimarisi](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Yetenekler

Hizmet kafeslerinin her biri doğal olarak bir sığdırmaya sahiptir ve belirli senaryoları desteklemeye odaklanmaktadır, ancak genellikle en fazla bir olarak, aşağıdaki yetenekler için bir sayı uygulayacaksınız.

### <a name="traffic-management"></a>Trafik yönetimi 

- **Protokol** – katman 7 (http, GRPC)
- **Dinamik yönlendirme** – koşullu, ağırlık, yansıtma
- **Dayanıklılık** – zaman aşımları, yeniden denemeler, devre kesiciler
- **İlke** – erişim denetimi, oran limitleri, Kotalar
- **Test** -hata ekleme

### <a name="security"></a>Güvenlik

- **Şifreleme** – MTLS, sertifika yönetimi, dış CA
- **Güçlü kimlik** – spiffe veya benzeri
- **Kimlik doğrulaması – kimlik** doğrulama, yetkilendirme

### <a name="observability"></a>Observability

- **Ölçümler** : altın ölçümler, Prometheus, grafana
- **İzleme** -iş yükleri genelinde izlemeler
- **Trafik** – küme, giriş/çıkış

### <a name="mesh"></a>Ese

- **Desteklenen işlem** -Kubernetes, sanal makineler
- **Çoklu küme** geçitleri, Federasyon

## <a name="selection-criteria"></a>Seçim ölçütü

Bir hizmet ağı seçmeden önce, gereksinimlerinizi ve hizmet ağı yükleme nedenlerini anladığınızdan emin olun. Aşağıdaki soruları sormayı deneyin.

- **Bir giriş denetleyicisi, gereksinimlerim için yeterli mi?** -Bazen bir/b testi veya giriş sırasında trafiği bölme gibi bir özellik gerekli senaryoyu desteklemek için yeterlidir. Baş olmadan ortamınıza karmaşıklık eklemeyin.

- **İş yüklerim ve Ortamlarım ek fazla kafaları kabul edebilir mi?** -Hizmet kafesi desteklemek için gereken tüm ek bileşenler, CPU ve bellek gibi ek kaynaklar gerektirir. Ayrıca, tüm proxy 'ler ve ilişkili ilkesi, trafiğinizi gecikme süresi ekler. Gecikme süresine çok duyarlı olan veya hizmet kafesi bileşenlerini kapsayacak ek kaynakları sağlayamayan iş yükleriniz varsa yeniden değerlendirin.

- **Bu ek karmaşıklık gereksiz yere mi ekleniyor?** -Hizmet kafesi yükleme nedeni, iş veya operasyonel takımlar için kritik olmayan bir özellik kazanmak ise, yükleme, bakım ve yapılandırmanın ek karmaşıklığının buna değer olup olmadığını göz önünde bulundurun.

- **Bu, artımlı bir yaklaşımda mi benimsemelidir?** -Çok sayıda özellik sağlayan bazı hizmet kafesleri daha artımlı bir yaklaşımda benimsemeye devam edebilir. Yalnızca başarınızı sağlamak için ihtiyacınız olan bileşenleri yükleyebilirsiniz. Daha emin olduktan ve ek yetenekler gerekiyorsa, bunları keşfedebilirsiniz. Başlangıçtan itibaren *her şeyi* yüklemek için bağlantısını okumanızı tavsiye 'yı yeniden başlatın.

Dikkatli olduktan sonra, gereken özellikleri sağlamak için bir hizmet ağı gerekli olduğuna karar verirsiniz, sonra bir sonraki kararınız *hizmet ağı olur mu?*

Aşağıdaki alanlarda ve bunların en çok gereksinimlerinize göre hizalandığını göz önünde bulundurun. Bu, ortamınız ve iş yükleriniz için en uygun şekilde size kılavuzluk eder. [Sonraki adımlar](#next-steps) bölümü, belirli hizmet kafesleri ve bu alanlarla nasıl eşlendikleri hakkında daha ayrıntılı bilgi almak için sizi ele alır.

- **Teknik** trafik yönetimi, ilke, güvenlik, Observability

- **İş** -ticari destek, temel (cncf), OSS lisansı, idare

- **İşletimsel** – yükleme/yükseltme, kaynak gereksinimleri, performans gereksinimleri, tümleştirmeler (ölçümler, telemetri, panolar, Araçlar, SMI), karışık iş yükleri (Linux ve Windows düğüm havuzları), Işlem (Kubernetes, sanal makineler), Çoklu küme

- **Güvenlik** -kimlik doğrulama, kimlik, sertifika yönetimi ve döndürme, TAKıLABILIR harici CA


## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki belgelerde, Azure Kubernetes Service (AKS) üzerinde deneyebileceğiniz hizmet kafesleri hakkında daha fazla bilgi sağlanmaktadır:

> [!div class="nextstepaction"]
> [Istio hakkında daha fazla bilgi edinin...][istio-about]

> [!div class="nextstepaction"]
> [Linkerd hakkında daha fazla bilgi edinin...][linkerd-about]

> [!div class="nextstepaction"]
> [Tüketil hakkında daha fazla bilgi edinin...][consul-about]

Ayrıca, Kubernetes üzerinde hizmet kafesleri için standart arabirim olan hizmet kafesi arabirimini (SMı) araştırmak isteyebilirsiniz:

- [Hizmet kafesi arabirimi (SMı)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md