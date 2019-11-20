---
title: Operatör en iyi uygulamalar-Azure Kubernetes hizmetlerinde depolama (AKS)
description: Azure Kubernetes Service (AKS) ' de depolama, veri şifreleme ve yedeklemeler için küme işletmeni en iyi yöntemlerini öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: mlearned
ms.openlocfilehash: 8e5f394987de06feaeb9a635face643eecc97cb9
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174218"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde depolama ve yedeklemeler için en iyi uygulamalar

Azure Kubernetes Service (AKS) içinde kümeler oluşturup yönetirken, uygulamalarınız genellikle depolamaya ihtiyaç duyar. Uygulamalara uygun depolamayı sağlayabilmeniz için, Pod 'nin performans ihtiyaçlarını ve erişim yöntemlerini anlamanız önemlidir. AKS düğüm boyutu bu depolama seçimlerini etkileyebilir. Ayrıca, ekli depolama için geri yükleme işlemini yedekleme ve test etme yollarını da planlamanız gerekir.

Bu en iyi yöntemler makalesi, küme işleçleri için depolama konularına odaklanır. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Hangi tür depolama alanı kullanılabilir?
> * Depolama performansı için AKS düğümlerini doğru şekilde boyutlandırma
> * Birimlerin dinamik ve statik sağlanması arasındaki farklılıklar
> * Veri hacimlerinizi yedekleme ve korumaya yönelik yollar

## <a name="choose-the-appropriate-storage-type"></a>Uygun depolama türünü seçin

**En iyi Yöntem Kılavuzu** -doğru depolamayı seçmek için uygulamanızın ihtiyaçlarını anlayın. Üretim iş yükleri için yüksek performanslı, SSD ile desteklenen depolama alanı kullanın. Birden çok eş zamanlı bağlantı gereksinimi olduğunda ağ tabanlı depolama için plan yapın.

Uygulamalar genellikle depolama alanının farklı türlerini ve hızlarını gerektirir. Uygulamalarınızın tek tek yığınlara bağlanan veya birden çok sayıda dizin üzerinde paylaşılan depolama alanına ihtiyacı var mı? Depolama, verilere salt okuma erişimi mi, yoksa büyük miktarlarda yapılandırılmış veriler mi? Bu depolama ihtiyacı, kullanılacak en uygun depolama türünü belirlemelidir.

Aşağıdaki tabloda kullanılabilir depolama türleri ve bunların özellikleri özetlenmektedir:

| Kullanım örneği | Birim eklentisi | Bir kez oku/yaz | Salt okunurdur | Okuma/yazma çok | Windows Server kapsayıcısı desteği |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Paylaşılan yapılandırma       | Azure dosyaları   | Evet | Evet | Evet | Evet |
| Yapılandırılmış uygulama verileri        | Azure diskleri   | Evet | Hayır  | Hayır  | Evet |
| Yapılandırılmamış veriler, dosya sistemi işlemleri | [Blobsigortası (Önizleme)][blobfuse] | Evet | Evet | Evet | Hayır |

AKS 'teki birimler için belirtilen iki birincil depolama türü, Azure diskleri veya Azure dosyaları tarafından desteklenir. Güvenliği artırmak için her iki depolama türü de, bekleyen verileri şifreleyen varsayılan olarak Azure Depolama Hizmeti Şifrelemesi (SSE) kullanır. Diskler Şu anda AKS düğüm düzeyinde Azure disk şifrelemesi kullanılarak şifrelenemez.

Azure dosyaları, standart performans katmanında Şu anda kullanılabilir. Azure diskleri standart ve Premium performans katmanlarında kullanılabilir:

- *Premium* diskler, yüksek performanslı katı hal diskleri (SSD 'ler) tarafından desteklenir. Premium diskler tüm üretim iş yükleri için önerilir.
- *Standart* diskler normal dönen diskler (HDD 'ler) tarafından desteklenir ve arşiv veya seyrek erişilen veriler için uygundur.

Uygun depolama katmanını seçmek için uygulama performansı ihtiyaçlarını ve erişim düzenlerini anlayın. Yönetilen disk boyutları ve performans katmanları hakkında daha fazla bilgi için bkz. [Azure yönetilen disklere genel bakış][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Uygulama gereksinimlerini tanımlamak için depolama sınıfları oluşturma ve kullanma

Kullandığınız depolamanın türü, Kubernetes *Depolama sınıfları*kullanılarak tanımlanır. Depolama sınıfına daha sonra Pod veya Deployment belirtiminde başvurulur. Bu tanımlar, uygun depolamayı oluşturmak ve onları pods 'ye bağlamak için birlikte çalışır. Daha fazla bilgi için bkz. [AKS 'de Depolama sınıfları][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Depolama ihtiyaçlarına yönelik düğümleri Boyutlandır

**En iyi Yöntem Kılavuzu** -her düğüm boyutu en fazla disk sayısını destekler. Farklı düğüm boyutları farklı miktarda yerel depolama ve ağ bant genişliği de sağlar. Uygun düğümlerin boyutunu dağıtmak için uygulamanızın taleplerini planlayın.

AKS düğümleri Azure VM 'Leri olarak çalışır. Farklı türlerdeki VM 'ler kullanılabilir. Her VM boyutu, CPU ve bellek gibi farklı bir çekirdek kaynak miktarı sağlar. Bu VM boyutları, eklenebilecek en fazla disk sayısına sahiptir. Depolama performansı Ayrıca, en yüksek yerel ve bağlı disk ıOPS (saniye başına giriş/çıkış işlemleri) için VM boyutları arasında da farklılık gösterir.

Uygulamalarınız depolama çözümü olarak Azure diskleri gerektiriyorsa, uygun bir düğüm VM boyutunu planlayın ve seçin. Bir VM boyutu seçtiğinizde, CPU ve bellek miktarı tek etken değildir. Depolama özellikleri de önemlidir. Örneğin, hem *Standard_B2ms* hem de *Standard_DS2_v2* VM BOYUTLARı, benzer bir CPU ve bellek kaynakları içerir. Olası depolama performansı, aşağıdaki tabloda gösterildiği gibi farklıdır:

| Düğüm türü ve boyutu | vCPU | Bellek (GiB) | En fazla veri diski | Önbelleğe alınmamış disk ıOPS üst sınırı | Önbelleğe alınmamış maksimum üretilen iş (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1\.920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6\.400                  | 96                             |

Burada, *Standard_DS2_v2* eklenen disklerin sayısının iki katına izin verır ve IOPS ve disk aktarım hızı için üç-dört kat sağlar. Yalnızca temel işlem kaynaklarına bakıyordu ve maliyetleri karşılaştırdıysanız, *Standard_B2ms* VM boyutunu seçebilir ve yetersiz depolama performansına ve sınırlamalara sahip olabilirsiniz. Depolama kapasitesini ve performans ihtiyaçlarını anlamak için uygulama geliştirme ekibinizle birlikte çalışın. AKS düğümleri için, performans ihtiyaçlarını karşılayacak veya aşacak uygun VM boyutunu seçin. VM boyutunu gerektiği şekilde ayarlamak için düzenli olarak temel uygulamalar.

Kullanılabilir VM boyutları hakkında daha fazla bilgi için bkz. [Azure 'Da Linux sanal makineleri Için boyutlar][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Dinamik olarak birimleri sağlama

**En iyi Yöntem Kılavuzu** -yönetim yükünü azaltmak ve ölçeklemenize olanak sağlamak için kalıcı birimleri statik olarak oluşturma ve atama. Dinamik sağlamayı kullanın. Depolama sınıflarınızda, bir dizin silindikten sonra gereksiz depolama maliyetlerini en aza indirmek için uygun geri kazanma ilkesini tanımlayın.

Depolara depolama eklemeniz gerektiğinde kalıcı birimler kullanırsınız. Bu kalıcı birimler el ile veya dinamik olarak oluşturulabilir. Kalıcı birimlerin el ile oluşturulması yönetim yükünü ekler ve ölçeklendirme yeteneğinizi kısıtlar. Depolama yönetimini basitleştirmek ve uygulamalarınızın gerektikçe büyümesine ve ölçeklendirilmesine izin vermek için dinamik kalıcı birim sağlamayı kullanın.

![Azure Kubernetes Services (AKS) kümesinde kalıcı birim talepleri](media/concepts-storage/persistent-volume-claims.png)

Kalıcı bir birim talebi (PVC) gerektiğinde dinamik olarak depolama oluşturmanızı sağlar. Temel alınan Azure diskleri, bir pod tarafından talep edilen olarak oluşturulur. Pod tanımında, oluşturulacak ve tasarlanan bir bağlama yoluna eklenen bir birim isteyin

Birimlerin dinamik olarak oluşturulması ve kullanılması hakkındaki kavramlar için bkz. [kalıcı birimler talepleri][aks-concepts-storage-pvcs].

Bu birimleri eylemde görmek için bkz. [Azure diskleri][dynamic-disks] veya [Azure dosyaları][dynamic-files]ile kalıcı bir birimi dinamik olarak oluşturma ve kullanma.

Depolama sınıfı tanımlarınızın bir parçası olarak, uygun *reclaimPolicy*ayarlayın. Bu reclaimPolicy, Pod silindiğinde ve kalıcı birim artık gerekmiyorsa, temel alınan Azure depolama kaynağının davranışını denetler. Temel alınan depolama kaynağı silinebilir veya gelecekteki bir pod ile kullanım için korunabilir. ReclaimPolicy, *sakla* veya *Sil*olarak ayarlanabilir. Uygulama gereksinimlerinizi anlayın ve kullanılan ve faturalandırılmamış kullanılmayan depolama miktarını en aza indirmek için saklanan depolama için düzenli denetimler uygulayın.

Depolama sınıfı seçenekleri hakkında daha fazla bilgi için bkz. [depolama geri kazanma ilkeleri][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Verilerinizin güvenliğini sağlama ve yedekleme

**En iyi Yöntem Kılavuzu** -depolama türü için Velero veya Azure Site Recovery gibi uygun bir aracı kullanarak verilerinizi yedekleyin. Bu yedeklemelerin bütünlüğünü ve güvenliğini doğrulayın.

Uygulamalarınız disklerde veya dosyalarda kalıcı olarak verileri depolayıp tükettiğinizde, bu verilerin düzenli yedeklemelerini veya anlık görüntülerini almanız gerekir. Azure diskleri, yerleşik anlık görüntü teknolojilerini kullanabilir. Anlık görüntü işlemini gerçekleştirmeden önce, uygulamanızın diske yazma işlemlerini boşaltmaya yönelik arama yapmanız gerekebilir. [Velero][velero] , ek küme kaynakları ve yapılandırmalarının yanı sıra kalıcı birimleri yedekleyebilir. [Uygulamalarınızdan durum kaldıramazsa][remove-state], verileri kalıcı birimlerden yedekleyin ve veri bütünlüğünü ve gerekli işlemleri doğrulamak için geri yükleme işlemlerini düzenli olarak test edin.

Veri yedeklemelerine yönelik farklı yaklaşımların sınırlamalarını ve anlık görüntüden önce verilerinizi sessiz moda almanız gerekiyorsa anlayın. Veri yedeklemeleri, küme dağıtımının uygulama ortamınızı geri yüklemenize gerek kalmaz. Bu senaryolar hakkında daha fazla bilgi için bkz. [AKS 'de iş sürekliliği ve olağanüstü durum kurtarma Için en iyi uygulamalar][best-practices-multi-region].

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS 'teki en iyi depolama uygulamalarına odaklanılmıştır. Kubernetes 'te depolama temelleri hakkında daha fazla bilgi için bkz. [AKS 'teki uygulamalar Için depolama kavramları][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/linux/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
