---
title: Azure Kubernetes Service (AKS) içindeki Windows Server düğüm havuzlarının sınırlamaları
description: Azure Kubernetes Service 'te (AKS) Windows Server düğüm havuzlarını ve uygulama iş yüklerini çalıştırırken oluşan bilinen sınırlamalar hakkında bilgi edinin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 2cec24e5ea50360531e247b37369802f3ef4e336
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990291"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki Windows Server düğüm havuzları ve uygulama iş yükleri için geçerli sınırlamalar

Azure Kubernetes Service (AKS) ' de, düğümlerde Konuk işletim sistemi olarak Windows Server çalıştıran bir düğüm havuzu oluşturabilirsiniz. Bu düğümler, .NET Framework yerleşik olanlar gibi yerel Windows kapsayıcı uygulamaları çalıştırabilir. Linux ve Windows işletim sisteminin kapsayıcı desteği sağladığı önemli farklılıklar söz konusu olduğunda, bazı yaygın Kubernetes ve pod ile ilgili özellikler Şu anda Windows düğüm havuzları için kullanılamaz.

Bu makalede, AKS 'deki Windows Server düğümlerine yönelik bazı sınırlamalar ve işletim sistemi kavramları özetlenmektedir. Windows Server için düğüm havuzları Şu anda önizlemededir.

> [!IMPORTANT]
> AKS Önizleme özellikleri self servis kabul etme sürecindedir. Önizlemeler, "olduğu gibi" ve "kullanılabilir olarak" verilmiştir ve hizmet düzeyi sözleşmelerinden ve sınırlı garantiden çıkarılır. AKS önizlemeleri, müşteri desteğinin en iyi çaba temelinde kısmen ele alınmıştır. Bu nedenle, bu özellikler üretim kullanımı için tasarlanmamıştır. Ek bilgi için lütfen aşağıdaki destek makalelerine bakın:
>
> * [AKS destek Ilkeleri][aks-support-policies]
> * [Azure desteği SSS][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Hangi Windows işletim sistemleri destekleniyor?

AKS, ana bilgisayar işletim sistemi sürümü olarak Windows Server 2019 kullanır ve yalnızca işlem yalıtımını destekler. Diğer Windows Server sürümleri kullanılarak oluşturulan kapsayıcı görüntüleri desteklenmez. [Windows kapsayıcı sürümü uyumluluğu][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Kubernetes, Windows ve Linux 'ta farklı midir?

Pencere sunucusu düğüm havuzu desteği, Kubernetes projesinde yukarı akış Windows Server 'ın bir parçası olan bazı sınırlamalar içerir. Bu sınırlamalar AKS 'e özgü değildir. Kubernetes 'te Windows Server için bu yukarı akış desteği hakkında daha fazla bilgi için, Kubernetes projesinden [Kubernetes belgesinde Windows desteği 'Ne giriş][intro-windows] konusunun [desteklenen işlevsellik ve sınırlamalar][upstream-limitations] bölümüne bakın.

Kubernetes, geçmişte Linux odaklı. Yukarı akış [Kubernetes.io][kubernetes] Web sitesinde kullanılan birçok örnek, Linux düğümlerinde kullanılmak üzere tasarlanmıştır. Windows Server kapsayıcıları kullanan dağıtımlar oluşturduğunuzda, işletim sistemi düzeyinde aşağıdaki noktalar geçerlidir:

- **Identity** -Linux bir kullanıcıyı bir tamsayı Kullanıcı TANıMLAYıCıSıNA (UID) göre tanımlar. Kullanıcının oturum açmak için alfasayısal bir Kullanıcı adı da vardır. Bu, Linux 'un Kullanıcı UID 'ye çeviri anlamına gelir. Benzer şekilde Linux bir kullanıcı grubunu bir tamsayı grubu tanımlayıcısı (GID) ile tanımlar ve bir grup adını karşılık gelen GID 'ye çevirir.
    - Windows Server, Windows güvenlik erişimi Yöneticisi (SAM) veritabanında depolanan daha büyük bir ikili güvenlik tanımlayıcısı (SID) kullanır. Bu veritabanı, konak ve kapsayıcılar ile ya da kapsayıcılar arasında paylaşılmaz.
- **Dosya izinleri** -Windows Server, bir bit MASKESI ve UID + GID yerine SID 'lere dayalı bir erişim denetim listesi kullanır
- **Dosya yolları** -Windows Server 'daki kural/yerine \ ' i kullanmaktır.
    - Birimleri takmaya yönelik Pod özellikleri, Windows Server kapsayıcıları için yolu doğru belirtin. Örneğin, bir Linux kapsayıcısında */mnt/Volume* bağlama noktası yerine, *K:* sürücüsü olarak takılacak bir sürücü harfi ve */k/birimi* gibi bir konum belirtin.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows için ne tür diskler destekleniyor?

Azure diskleri ve Azure dosyaları, Windows Server kapsayıcısında NTFS birimleri olarak erişilen desteklenen birim türleridir.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>AKS 'de yalnızca Windows kümelerini çalıştırabilir miyim?

Bir AKS kümesindeki ana düğümler (denetim düzlemi) hizmet tarafından barındırılır, ana bileşenleri barındıran düğümlerin işletim sistemine maruz olmayacaktır. Tüm AKS kümesi, Linux tabanlı olan varsayılan ilk düğüm havuzu ile oluşturulur. Bu düğüm havuzu, kümenin çalışması için gerekli olan sistem hizmetlerini içerir. Kümenizin güvenilirliğini ve küme işlemlerini yapma yeteneğini sağlamak için ilk düğüm havuzunda en az iki düğüm çalıştırmanız önerilir. AKS kümesinin kendisi silinmediği takdirde ilk Linux tabanlı düğüm havuzu silinemez.

## <a name="what-network-plug-ins-are-supported"></a>Hangi ağ eklentileri destekleniyor?

Windows node havuzlarıyla AKS kümelerinin Azure CNı (Gelişmiş) ağ modelini kullanması gerekir. Kubenet (temel) ağı desteklenmez. Ağ modellerindeki farklılıklar hakkında daha fazla bilgi için bkz. [AKS 'teki uygulamalar Için ağ kavramları][azure-network-models]. -Azure CNı ağ modeli, IP adresi yönetimi için ek planlama ve dikkat edilecek noktalar gerektirir. Azure CNı 'nin nasıl planlanacağı ve uygulanacağı hakkında daha fazla bilgi için bkz. [AKS 'de Azure CNI ağını yapılandırma][configure-azure-cni].

## <a name="can-i-change-the-min--of-pods-per-node"></a>Düğüm başına en az sayıda Pod 'yi değiştirebilir miyim?

Kümelerinizin güvenilirliğini güvence altına almak için şu anda en az 30 Pod olarak ayarlanması gereksinimidir.

## <a name="how-do-patch-my-windows-nodes"></a>Windows düğümlerimi nasıl yaalabilirim?

En son düzeltme eki düzeltmelerini ve güncelleştirmelerini almak için AKS 'deki Windows Server düğümlerinin *yükseltilmesi* gerekir. Windows güncelleştirmeleri AKS içindeki düğümlerde etkin değildir. AKS yeni düğüm havuzu görüntülerini yayınlar, ancak düzeltme ekleri kullanılabilir olduğunda, bu durumda müşterilerin düğüm havuzlarını yükselterek düzeltme ekleri ve düzeltmede güncel kalmasını sağlar. Bu, aynı zamanda kullanılan Kubernetes sürümü için de geçerlidir. AKS sürüm notları, yeni sürümlerin ne zaman kullanılabilir olduğunu gösterir. Bir Windows Server düğüm havuzunu yükseltme hakkında daha fazla bilgi için bkz. [AKS 'de düğüm havuzunu yükseltme][nodepool-upgrade].

> [!NOTE]
> Güncelleştirilmiş Windows Server görüntüsü, düğüm havuzu yükseltmeden önce bir küme yükseltmesi (denetim düzlemi yükseltmesi) gerçekleştirildiğinde kullanılacaktır
>

## <a name="how-many-node-pools-can-i-create"></a>Kaç düğüm havuzu oluşturabilirim?

AKS kümesinde en fazla sekiz (8) düğüm havuzu olabilir. Bu düğüm havuzlarının üzerinde en fazla 400 düğümünüz olabilir. [Düğüm havuzu sınırlamaları][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Windows node havuzlarımı nasıl kullanabilirim?

Adı en fazla 6 (altı) karaktere saklamanız gerekir. Bu, AKS 'in geçerli bir sınırlamasıdır.

## <a name="are-all-features-supported-with-windows-nodes"></a>Tüm özellikler Windows düğümleriyle destekleniyor mu?

Ağ ilkeleri ve Kubernetes kullanan Şu anda Windows düğümleri ile desteklenmemektedir. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Giriş denetleyicilerini Windows düğümlerinde çalıştırabilir miyim?

Evet, Windows Server kapsayıcıları destekleyen bir giriş denetleyicisi AKS 'deki Windows düğümlerinde çalıştırılabilir.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Windows düğümleri ile Azure Dev Spaces kullanabilir miyim?

Azure Dev Spaces Şu anda yalnızca Linux tabanlı düğüm havuzları için kullanılabilir.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Windows Server kapsayıcılarım gMSA kullanıyor mu?

Grup tarafından yönetilen hizmet hesapları (gMSA) desteği şu anda AKS 'de kullanılamıyor.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Windows düğümleri ve kapsayıcılarıyla kapsayıcılar için Azure Izleyici kullanabilir miyim?

Evet, ancak Azure Izleyici, Windows kapsayıcılarından günlükleri (STDOUT) toplamaz. Yine de bir Windows kapsayıcısından stdout günlüklerinin canlı akışına iliştirebilirsiniz.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Desteklenmeyen bir özelliğe ihtiyacım varsa ne olur?

AKS 'de Windows 'a ihtiyacınız olan tüm özellikleri getirmek için çok çalıştık, ancak boşluklar yaşarsanız açık kaynaklı, yukarı akış [aks-motor][aks-engine] projesi, Windows desteği dahil olmak üzere Azure 'Da Kubernetes 'in kolay ve tamamen özelleştirilebilir bir yolunu sağlar. Lütfen kendi [aks yol haritasını][aks-roadmap]inceleyin.

## <a name="next-steps"></a>Sonraki adımlar

AKS 'de Windows Server kapsayıcıları kullanmaya başlamak için [AKS 'de Windows Server çalıştıran bir düğüm havuzu oluşturun][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility#windows-server-2019-host-os-compatibility
