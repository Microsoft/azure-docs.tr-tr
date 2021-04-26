---
title: Azure Kubernetes Service (AKS) üzerinde gizli kapsayıcılar
description: Gizli kapsayıcılarda değiştirilmemiş kapsayıcı desteği hakkında bilgi edinin.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 2/11/2020
ms.author: amgowda
ms.service: container-service
ms.subservice: confidential-computing
ms.openlocfilehash: 280f75e8d18d16dd76d0730a90755774af34d6f6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933585"
---
# <a name="confidential-containers"></a>Gizli kapsayıcılar

## <a name="overview"></a>Genel Bakış

Geliştiricilerin, **var olan bir Docker uygulamasını (yeni veya var olan)** getirmek ve gizli bilgi işlem düğümleri desteğiyle Azure Kubernetes hizmeti 'NDE (aks) güvenli bir şekilde çalıştırmak için etkinleştirin.

Gizli kapsayıcılar korumaya yardımcı olur:

- veri bütünlüğü 
- veri gizliliği
- kod bütünlüğü
- Şifreleme aracılığıyla kapsayıcı kodu koruması
- donanım tabanlı sigortaları
- Mevcut uygulamaların çalıştırılmasına izin ver
- güvenin donanım kökünü oluşturma
- Ana bilgisayar yöneticisini, Kubernetes yöneticisini, güven sınırından hiper yöneticiye kaldır

Donanım tabanlı güvenilir bir yürütme ortamı (t), güvenilir bilgi işlem tabanı (TCB) bileşenlerinden gelen donanım ve yazılım ölçümleri aracılığıyla güçlü bir güvenlik sağlamak için kullanılan önemli bir bileşendir. Bu ölçülerin doğrulamaları beklenen hesaplamanın doğrulanmasına yardımcı olur ve kapsayıcı uygulamalarının tüm üzerinde değişiklik yapılmasını doğrular.

Gizli kapsayıcılar; **Python, Java, Node js, vb. ve NGıNX, Redis Cache, MemCache gibi paketlenmiş kapsayıcı uygulamaları** ve gizli bilgi işlem düğümleri ile aks 'de değiştirilmemiş olarak çalıştırılmak üzere geliştirilmiş özel uygulamaları destekler.

Gizli kapsayıcılar, kapsayıcı gizliliğine en hızlı yoldur ve yalnızca mevcut Docker kapsayıcı uygulamalarının yeniden paketlenmesi gerekir ve uygulama kodu değişiklikleri gerektirmez. Gizli kapsayıcılar, bir t 'de çalışacak şekilde paketlenmiş Docker kapsayıcı uygulamalardır. Bazı gizli kapsayıcı etkinleştiricileri, depolama ve taşıma sırasında ve konakta bağlıyken kapsayıcı kodunu korumaya yardımcı olabilecek kapsayıcı şifrelemeyi de sunmaktadır. Kapsayıcı şifreleme, kapsayıcıda paketlenmiş kodu/modeli, şifre çözme anahtarı olan bir e-koda bağlı olarak daha fazla kullanmanıza ve korumanıza olanak sağlar.

Aşağıda, geliştirmede gizli kapsayıcılardan ![ , gizli kapsayıcının nasıl işlenmesi ile ilgili gizli kapsayıcılar için bir işlem verilmiştir.](./media/confidential-containers/how-to-confidential-container.png)

## <a name="confidential-container-enablers"></a>Gizli kapsayıcı etkinleştiricileri
Var olan bir Docker kapsayıcısını değiştirilmemiş bir şekilde çalıştırmak için, uygulama çağrılarının iliştirme yüzeyi alanını düşürmek ve konuk işletim sistemi üzerinde hiçbir bağımlılığı olmaması için, bir SGX yazılım gerekir. Bu, SGX çalışma zamanı yazılımıyla Sarmalanan bir kez otomatik olarak, Konuk işletim sistemini, ana bilgisayar işletim sistemini veya hiper yöneticiyi güven sınırından kaldırır. Donanım tarafından desteklenen bir düğümde (sanal makine) bulunan bu yalıtılmış yürütme, genel yüzey saldırı alanlarının yanı sıra işletim sistemi veya hiper yönetici katmanlarında güvenlik açıklarını azaltır.

Gizli kapsayıcılar, AKS 'de tam olarak desteklenir ve Azure Iş ortakları ve Open-Source yazılım (OSS) projeleri aracılığıyla etkinleştirilir. Geliştiriciler, özellikleri temel alarak yazılım sağlayıcıları, Azure hizmetleri ile tümleştirme ve araçları destekleme desteğiyle seçim yapabilir.

## <a name="partner-enablers"></a>İş ortağı etkinleştiricileri
> [!NOTE]
> Aşağıdaki çözümler Azure Iş ortakları aracılığıyla sunulur ve lisans ücretleri uygulanabilir. Lütfen iş ortağı yazılım koşullarını bağımsız olarak doğrulayın. 

### <a name="anjuna"></a>Anjuna dili

[Anjuna](https://www.anjuna.io/) , aks üzerinde değiştirilmemiş kapsayıcılar ÇALıŞTıRMANıZı sağlayan SGX platform yazılımını sağlar. İşlevsellik hakkında daha fazla bilgi edinin ve örnek uygulamaları [buradan](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)inceleyin.

Örnek bir Redis Cache ve Python özel uygulamasını [buradan](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp) kullanmaya başlayın

![Anjuna Işlemi](./media/confidential-containers/anjuna-process-flow.png)

### <a name="fortanix"></a>Fortanx

[Fortanx](https://www.fortanix.com/) geliştiricilere, Kapsayıcılı uygulamaları getirmek ve uygulamayı değiştirmek ya da yeniden derlemek zorunda kalmadan bunları SGX özellikli gizli kapsayıcılara eklemek için bir portal ve CLI tabanlı deneyim sunmaktadır. Fortanx, mevcut uygulamalar, yeni encde yerel uygulamalar ve önceden paketlenmiş uygulamalar dahil olmak üzere çok sayıda uygulamayı çalıştırma ve yönetme esnekliği sağlar. Kullanıcılar, Azure Kubernetes hizmeti [hızlı başlangıç](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) kılavuzunu izleyerek gizli kapsayıcılar oluşturmak Için [gizli bilgi işlem yöneticisi](https://em.fortanix.com/) Kullanıcı arabirimi veya [REST API 'leri](https://www.fortanix.com/api/em/) ile başlayabilir.

![Fortanx dağıtım Işlemi](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Skoni (Siçerme)

[Skoni](https://scontain.com/index.html?lang=en) , sertifikalar, anahtarlar ve gizli dizileri oluşturabilen güvenlik ilkelerini destekler ve bunların yalnızca bir uygulamanın tarafından sınanmış hizmetlere görünür olmasını sağlar. Bu şekilde, bir uygulamanın Hizmetleri, uygulamaları veya TLS 'yi değiştirmeye gerek kalmadan, birbirini otomatik olarak TLS aracılığıyla onaylar. Bu, basit bir Flask uygulamasının yardımıyla burada açıklanmıştır: https://sconedocs.github.io/flask_demo/  

SKONI, var olan en ikilileri, uygulamayı değiştirmeye ya da uygulamayı yeniden derlemenize gerek kalmadan, şifreleme içinde çalışan uygulamalara dönüştürebilir. SKONI, hem veri dosyalarını hem de Python kod dosyalarını **şifreleyerek** Python gibi yorumlanan dilleri de korur. Bir SKONI güvenlik ilkesinin yardımıyla, birisi şifrelenmiş dosyaları yetkisiz erişimler, değişiklikler ve geri göndermeler ile koruyabilir. "Nasıl yapılır" var olan bir Python uygulaması [burada](https://sconedocs.github.io/sconify_image/) açıklanmaktadır

![Siçerme akışı](./media/confidential-containers/scone-workflow.png)

AKS ile gizli bilgi işlem düğümlerinde skoni dağıtımları, diğer Azure hizmetleriyle tam olarak desteklenir ve tümleşiktir. Burada örnek bir uygulama kullanmaya başlayın https://sconedocs.github.io/aks/


## <a name="oss-enablers"></a>OSS etkinleştiricileri 
> [!NOTE]
> Aşağıdaki çözümler Open-Source projeler aracılığıyla sunulur ve doğrudan Azure gizli bilgi Işlem (ACC) veya Microsoft ile bağlantılı değildir.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) , en düşük ana bilgisayar gereksinimleriyle tek bir Linux uygulaması çalıştıracak şekilde tasarlanan hafif bir konuk işletim sistemi. Graphene, tam bir işletim sistemi çalıştırmaya benzer avantajlar sayesinde uygulamaları yalıtılmış bir ortamda çalıştırabilir ve var olan Docker kapsayıcı uygulamasını Graphene korumalı kapsayıcılara (GSC) dönüştürmek için iyi araç desteğine sahiptir.

[Buradan](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks) bir örnek uygulama ve dağıtım ile çalışmaya başlayın

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) , Intel SGX için bellek açısından güvenli, çok işlem temelli bir kitaplık işletim sistemi (liboş). Eski uygulamaların, kaynak kodunda hiçbir değişiklik yapmadan SGX üzerinde çalışmasını sağlar. Occlum, mevcut Docker uygulamalarına kolay bir şekilde geçiş ve kaydırma yapılmasına izin verirken Kullanıcı iş yüklerinin gizliliğini saydam olarak korur.

Occlum, AKS dağıtımlarını destekler. [Burada](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md) çeşitli örnek uygulamalarla dağıtım talimatlarını izleyin


## <a name="confidential-containers-demo"></a>Gizli kapsayıcılar tanıtımı
Gizli kapsayıcılar ile gizli sağlık tanıtımına bakın. Örnek [burada](/azure/architecture/example-scenario/confidential/healthcare-inference)bulunabilir. 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Dokunarak al

Uygulamanız ile ilgili sorularınız mı var, yoksa bir Etkinleştirici olmak mı istiyorsunuz? Ürün ekibine e-posta gönderin **acconaks@microsoft.com**

## <a name="reference-links"></a>Başvuru bağlantıları

[Microsoft Azure Doğrulama](../attestation/overview.md)

[DCsv2 sanal makineleri](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
