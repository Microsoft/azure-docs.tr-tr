---
title: Depolama ve yedekleme için en iyi uygulamalar
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) ' de depolama, veri şifreleme ve yedeklemeler için küme işletmeni en iyi yöntemlerini öğrenin
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 9b3ee6fd7eea958a573743b21bf8940458e2a965
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104924"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde depolama ve yedeklemeler için en iyi uygulamalar

Azure Kubernetes Service (AKS) içinde kümeler oluşturup yönetirken, uygulamalarınız genellikle depolamaya ihtiyaç duyar. Uygulamanızın en iyi depolama alanını seçebilmeniz için pod performans ihtiyaçlarını ve erişim yöntemlerini anladığınızdan emin olun. AKS düğüm boyutu, depolama seçimlerinizi etkileyebilir. Bağlı depolama için geri yükleme sürecini yedekleme ve test etme yollarını planlayın.

Bu en iyi yöntemler makalesi, küme işleçleri için depolama konularına odaklanır. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Hangi tür depolama alanı kullanılabilir.
> * Depolama performansı için AKS düğümlerini doğru şekilde boyutlandırma.
> * Birimlerin dinamik ve statik sağlanması arasındaki farklar.
> * Veri hacimlerini yedekleme ve güvenli hale getirme yolları.

## <a name="choose-the-appropriate-storage-type"></a>Uygun depolama türünü seçin

> **En iyi yöntemler kılavuzu**
> 
> Doğru depolamayı seçmek için uygulamanızın ihtiyaçlarını anlayın. Üretim iş yükleri için yüksek performanslı, SSD ile desteklenen depolama alanı kullanın. Birden çok eş zamanlı bağlantıya ihtiyacınız olduğunda ağ tabanlı depolamayı planlayın.

Uygulamalar genellikle depolama alanının farklı türlerini ve hızlarını gerektirir. Aşağıdaki soruları sorarak en uygun depolama türünü saptayın. 
* Uygulamalarınızın tek tek depolara bağlanan bir depolamaya ihtiyacı var mı?
* Uygulamalarınızın birden çok pods arasında paylaşılan depolama alanı olması gerekiyor mu? 
* Depolama, verilere salt okuma erişimi için mi?
* Depolama alanı, büyük miktarlarda yapılandırılmış verileri yazmak için kullanılır mi? 

Aşağıdaki tabloda kullanılabilir depolama türleri ve bunların özellikleri özetlenmektedir:

| Kullanım örneği | Birim eklentisi | Bir kez oku/yaz | Salt okunurdur | Okuma/yazma çok | Windows Server kapsayıcısı desteği |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Paylaşılan yapılandırma       | Azure Dosyaları   | Yes | Yes | Yes | Yes |
| Yapılandırılmış uygulama verileri        | Azure Diskleri   | Yes | Hayır  | Hayır  | Yes |
| Yapılandırılmamış veriler, dosya sistemi işlemleri | [Blobsigortası][blobfuse] | Yes | Yes | Yes | Hayır |

AKS, Azure diskleri veya Azure dosyaları tarafından desteklenen birimler için iki birincil güvenli depolama türü sağlar. Her ikisi de, bekleyen verileri şifreleyen varsayılan Azure Depolama Hizmeti Şifrelemesi (SSE) kullanır. Diskler, AKS düğüm düzeyinde Azure disk şifrelemesi kullanılarak şifrelenemez.

Hem Azure dosyaları hem de Azure diskleri standart ve Premium performans katmanlarında kullanılabilir:

- *Premium* diskler
    - Yüksek performanslı katı hal diskleri (SSD 'Ler) tarafından desteklenir. 
    - Tüm üretim iş yükleri için önerilir.
- *Standart* diskler
    - Düzenli olarak dönen diskler (HDD 'Ler) tarafından desteklenir.
    - Arşivleme veya seyrek erişilen veriler için iyi.

Uygun depolama katmanını seçmek için uygulama performansı ihtiyaçlarını ve erişim düzenlerini anlayın. Yönetilen disk boyutları ve performans katmanları hakkında daha fazla bilgi için bkz. [Azure yönetilen disklere genel bakış][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Uygulama gereksinimlerini tanımlamak için depolama sınıfları oluşturma ve kullanma

Kubernetes *depolama sınıflarını* kullanarak istediğiniz depolama türünü tanımlayın. Depolama sınıfına daha sonra Pod veya Deployment belirtiminde başvurulur. Depolama sınıfı tanımları birlikte çalışarak uygun depolamayı oluşturabilir ve bunları pods 'ye bağlayın. 

Daha fazla bilgi için bkz. [AKS 'de Depolama sınıfları][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Depolama ihtiyaçlarına yönelik düğümleri Boyutlandır

> **En iyi yöntemler kılavuzu**
> 
> Her düğüm boyutu en fazla disk sayısını destekler. Farklı düğüm boyutları farklı miktarda yerel depolama ve ağ bant genişliği de sağlar. Doğru düğümlerin boyutunu dağıtmak için uygulamanızın taleplerini uygun şekilde planlayın.

AKS düğümleri çeşitli Azure VM türleri ve boyutları olarak çalışır. Her VM boyutu şunları sağlar:
* CPU ve bellek gibi farklı çekirdek kaynakları miktarı. 
* İliştirilebilecek en fazla disk sayısı. 

Depolama performansı Ayrıca, en yüksek yerel ve bağlı disk ıOPS (saniye başına giriş/çıkış işlemleri) için VM boyutları arasında da farklılık gösterir.

Uygulamalarınız depolama çözümü olarak Azure diskleri gerektiriyorsa, uygun bir düğüm VM boyutunu stratejik hale getirin. Depolama özellikleri ve CPU ve bellek miktarları, VM boyutuyla ilgili karar verirken ana bir rol oynar. 

Örneğin, hem *Standard_B2ms* hem de *Standard_DS2_v2* VM boyutları benzer bir CPU ve bellek kaynakları içerirken, olası depolama performansı farklıdır:

| Düğüm türü ve boyutu | Sanal işlemci | Bellek (GiB) | Maksimum veri diskleri | Önbelleğe alınmamış disk ıOPS üst sınırı | Önbelleğe alınmamış maksimum üretilen iş (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1.920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6.400                  | 96                             |

Bu örnekte *Standard_DS2_v2* , çok sayıda bağlı DISK ve IOPS ve disk aktarım hızı miktarına üç ile dört katına daha sunulur. Yalnızca temel işlem kaynaklarını ve karşılaştırılan maliyetleri karşılaştırdıysanız, yetersiz depolama performansı ve sınırlamalarıyla *Standard_B2ms* VM boyutunu seçmiş olabilirsiniz. 

Depolama kapasitesini ve performans ihtiyaçlarını anlamak için uygulama geliştirme ekibinizle birlikte çalışın. AKS düğümleri için, performans ihtiyaçlarını karşılayacak veya aşacak uygun VM boyutunu seçin. VM boyutunu gerektiği şekilde ayarlamak için düzenli olarak temel uygulamalar.

Kullanılabilir VM boyutları hakkında daha fazla bilgi için bkz. [Azure 'Da Linux sanal makineleri Için boyutlar][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dinamik olarak birimleri sağlama

> **En iyi yöntemler kılavuzu** 
>
> Yönetim yükünü azaltmak ve ölçeklendirmeyi etkinleştirmek için kalıcı birimleri statik olarak oluşturma ve atama kullanmaktan kaçının. Dinamik sağlamayı kullanın. Depolama sınıflarınızda, bir dizin silindikten sonra gereksiz depolama maliyetlerini en aza indirmek için uygun geri kazanma ilkesini tanımlayın.

Depolara depolama eklemek için kalıcı birimler kullanın. Kalıcı birimler el ile veya dinamik olarak oluşturulabilir. Kalıcı birimler oluşturmak yönetim yükünü el ile ekler ve ölçeklendirme yeteneğinizi kısıtlar. Bunun yerine, depolama yönetimini basitleştirmek ve uygulamalarınızın gerektikçe büyümesine ve ölçeklendirilmesine olanak tanımak için kalıcı birimi dinamik olarak sağlayın.

![Azure Kubernetes Services (AKS) kümesinde kalıcı birim talepleri](media/concepts-storage/persistent-volume-claims.png)

Kalıcı bir birim talebi (PVC) gerektiğinde dinamik olarak depolama oluşturmanızı sağlar. Temel alınan Azure diskleri, bir pod tarafından talep olarak oluşturulur. Pod tanımında, oluşturulacak ve belirlenmiş bir bağlama yoluna eklenen bir birim isteyin.

Birimlerin dinamik olarak oluşturulması ve kullanılması hakkındaki kavramlar için bkz. [kalıcı birimler talepleri][aks-concepts-storage-pvcs].

Bu birimleri eylemde görmek için bkz. [Azure diskleri][dynamic-disks] veya [Azure dosyaları][dynamic-files]ile kalıcı bir birimi dinamik olarak oluşturma ve kullanma.

Depolama sınıfı tanımlarınızın bir parçası olarak, uygun *reclaimPolicy* ayarlayın. Bu reclaimPolicy, Pod silindiğinde temeldeki Azure depolama kaynağının davranışını denetler. Alttaki depolama kaynağı silinebilir veya gelecekteki Pod kullanımı için korunabilir. ReclaimPolicy veya *Sil* olarak  ayarlayın. 

Uygulama gereksinimlerinizi anlayın ve kullanılmayan ve faturalandırılan depolama miktarını en aza indirmek için düzenli olarak tutulan depolama alanı denetimlerini uygulayın.

Depolama sınıfı seçenekleri hakkında daha fazla bilgi için bkz. [depolama geri kazanma ilkeleri][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Verilerinizin güvenliğini sağlama ve yedekleme

> **En iyi yöntemler kılavuzu** 
> 
> Velero veya Azure Backup gibi depolama türü için uygun bir aracı kullanarak verilerinizi yedekleyin. Bu yedeklemelerin bütünlüğünü ve güvenliğini doğrulayın.

Uygulamalarınız disklerde veya dosyalarda kalıcı olarak verileri depolayıp tükettiğinizde, bu verilerin düzenli yedeklemelerini veya anlık görüntülerini almanız gerekir. Azure diskleri, yerleşik anlık görüntü teknolojilerini kullanabilir. Anlık görüntü işlemini gerçekleştirmeden önce uygulamalarınızın yazma işlemlerini diske temizlemesi gerekebilir. [Velero][velero] , ek küme kaynakları ve yapılandırmalarının yanı sıra kalıcı birimleri yedekleyebilir. [Uygulamalarınızdan durum kaldıramazsa][remove-state], verileri kalıcı birimlerden yedekleyin ve veri bütünlüğünü ve gerekli işlemleri doğrulamak için geri yükleme işlemlerini düzenli olarak test edin.

Veri yedeklemelerine yönelik farklı yaklaşımların sınırlamalarını ve anlık görüntüden önce verilerinizi sessiz moda almanız gerekiyorsa anlayın. Veri yedeklemeleri, küme dağıtımının uygulama ortamınızı geri yüklemenize gerek kalmaz. Bu senaryolar hakkında daha fazla bilgi için bkz. [AKS 'de iş sürekliliği ve olağanüstü durum kurtarma Için en iyi uygulamalar][best-practices-multi-region].

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS 'teki en iyi depolama uygulamalarına odaklanılmıştır. Kubernetes 'te depolama temelleri hakkında daha fazla bilgi için bkz. [AKS 'teki uygulamalar Için depolama kavramları][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
