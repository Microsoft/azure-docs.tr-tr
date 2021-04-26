---
title: Azure Kubernetes Service (AKS) üzerinde gizli bilgi işlem düğümleri
description: AKS üzerinde gizli bilgi işlem düğümleri
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 2/08/2021
ms.author: amgowda
ms.openlocfilehash: 203185d9f6def2204906b8722f1969b14eee8787
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933160"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Azure Kubernetes hizmetindeki gizli bilgi işlem düğümleri

[Azure gizli bilgi işlem](overview.md) , önemli verilerinizi kullanımda iken korumanıza olanak sağlar. Temel alınan gizli bilgi işlem altyapısı, bu verileri, donanım tarafından desteklenen güvenilir bir yürütme kapsayıcı ortamları ile diğer uygulamalardan, yöneticilerden ve bulut sağlayıcılarından korur. Gizli bilgi işlem düğümleri eklemek, kapsayıcı uygulamasının yalıtılmış, donanım korumalı ve attestable ortamında çalışmasını hedeflemesini sağlar.

## <a name="overview"></a>Genel Bakış

Azure Kubernetes hizmeti (AKS), Intel SGX tarafından desteklenen [DCsv2 gizli bilgi işlem düğümlerinin](confidential-computing-enclaves.md) eklenmesini destekler. Bu düğümler, kritik iş yüklerini donanım tabanlı bir güvenilir yürütme ortamında (t) çalıştırmanızı sağlar. T 'nin kapsayıcılardan kullanıcı düzeyindeki kodun, kodu doğrudan CPU ile yürütmek için özel bellek bölgelerini ayırmasını sağlar. Doğrudan CPU ile yürütülen bu özel bellek bölgelerine, enclaven denir. Şifreleme, aynı düğümlerde çalışan diğer işlemlerden veri gizliliğini, veri bütünlüğünü ve kod bütünlüğünü korumanıza yardımcı olur. Intel SGX yürütme modeli, Konuk işletim sistemi, ana bilgisayar IŞLETIM sistemi ve Hiper yöneticide bulunan ara katmanları da kaldırır, böylece saldırı yüzeyi alanını azaltır. Bir düğümdeki *kapsayıcı başına yalıtılmış yürütme modeli tabanlı donanım* , uygulamaların, kapsayıcı başına şifrelenmiş özel bellek bloğunu tutarken CPU ile doğrudan yürütülmesini sağlar. Gizli kapsayıcılarla gizli bilgi işlem düğümleri, sıfır güveninizin güvenlik planlaması ve derinlemesine savunma kapsayıcı stratejinize yönelik harika bir ektir.

![SGX düğümüne genel bakış](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>AKS gizli düğümleri özellikleri

- Intel SGX güvenilir yürütme ortamı (t) ile donanım tabanlı ve işlem düzeyi kapsayıcı yalıtımı 
- Heterojen düğüm havuzu kümeleri (gizli ve gizli olmayan düğüm havuzlarını karıştırma)
- Şifrelenmiş sayfa önbelleği (EPC) bellek tabanlı Pod zamanlaması (eklenti gerektirir)
- Intel SGX DCAP sürücüsü önceden yüklenmiş
- CPU tüketimi tabanlı yatay Pod otomatik ölçeklendirme ve küme otomatik ölçeklendirme
- Linux kapsayıcıları Ubuntu 18,04 Gen 2 VM çalışan düğümleri aracılığıyla desteklenir

## <a name="confidential-computing-add-on-for-aks"></a>AKS için gizli bilgi Işlem eklentisi
Eklenti özelliği, küme üzerinde gizli bilgi işlem düğüm havuzlarını çalıştırırken AKS üzerinde fazladan yetenek özelliği sunar. Bu eklenti aşağıdaki özellikleri sunar.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Intel SGX için Azure cihaz eklentisi <a id="sgx-plugin"></a>

Cihaz eklentisi, şifrelenmiş sayfa önbelleği (EPC) belleği için Kubernetes cihaz eklentisi arabirimini uygular ve cihaz sürücülerini düğümlerden kullanıma sunar. Bu eklenti etkin şekilde, Kubernetes 'te EPC belleğini başka bir kaynak türü olarak yapar. Kullanıcılar, bu kaynak üzerinde diğer kaynaklar gibi sınırlar belirtebilir. Zamanlama işlevinden ayrı olarak, cihaz eklentisi gizli iş yükü kapsayıcılarına Intel SGX cihaz sürücüsü izinleri atamanıza yardımcı olur. Bu eklenti geliştiricisi sayesinde, dağıtım dosyalarında Intel SGX sürücü birimlerinin takılmasını önleyebilirsiniz. EPC bellek tabanlı dağıtım () örneğinin örnek bir uygulamasını `kubernetes.azure.com/sgx_epc_mem_in_MiB` [burada bulabilirsiniz](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)


## <a name="programming-models"></a>Programlama modelleri

### <a name="confidential-containers"></a>Gizli kapsayıcılar

[Gizli kapsayıcılar](confidential-containers.md) , en **yaygın programlama dilleri** çalışma zamanlarının (Python, Node, Java vb.) confidentially var olan değiştirilmemiş kapsayıcı uygulamalarını çalıştırmanıza yardımcı olur. Bu paketleme modelinde hiçbir kaynak kodu değişikliği veya yeniden derleme gerekmez. Bu, standart Docker Kapsayıcılarınızı Open-Source projelerle veya Azure Iş ortağı çözümleriyle paketleyerek elde edilen gizlilik için en hızlı yöntemdir. Bu paketleme ve yürütme modelinde, kapsayıcı uygulamasının tüm parçaları güvenilir bir sınıra (Enclave) yüklenir. Bu model, genel amaçlı düğümlerde çalışmakta olan Pazar veya özel uygulamalarda bulunan raf kapsayıcısı uygulamaları için iyi çalışır.

### <a name="enclave-aware-containers"></a>Şifreleme kullanan kapsayıcılar
AKS üzerinde gizli bilgi işlem düğümleri, CPU 'dan kullanılabilen **özel yönerge kümesini** kullanmak için bir kuşakda çalışmak üzere programlanan kapsayıcıları destekler. Bu programlama modeli, yürütme akışlarınızın daha sıkı denetimini sağlar ve özel SDK 'ların ve çerçevelerin kullanılmasını gerektirir. Bu programlama modeli en düşük güvenilir bilgi Işlem tabanıyla (TCB) en fazla uygulama akışı denetimini sağlar. Kuşve duyarlı kapsayıcı geliştirmesi, kapsayıcı uygulamasına güvenilmeyen ve güvenilen bölümleri içerir ve bu sayede, kuşve 'nin yürütüldüğü normal bellek ve şifrelenmiş sayfa önbelleği (EPC) belleğini yönetebilirsiniz. Şifreleme kullanan kapsayıcılar hakkında [daha fazla bilgi edinin](enclave-aware-containers.md) .

## <a name="next-steps"></a>Sonraki Adımlar

[AKS kümesini gizli bilgi işlem düğümleri ile dağıtma](./confidential-nodes-aks-get-started.md)

[Hızlı başlangıç gizli kapsayıcı örnekleri](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU listesi](../virtual-machines/dcv2-series.md)

[Gizli kapsayıcılar için derinlemesine savunma Web semineri](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
