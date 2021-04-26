---
title: Kavramlar-Azure Kubernetes hizmetlerinde (AKS) depolama
description: Azure Kubernetes Service 'teki (AKS) birimler, kalıcı birimler, depolama sınıfları ve talepler dahil depolama hakkında bilgi edinin
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: a1f68b06c31597a1d2d044686274e86a79e6e9a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105791"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içindeki uygulamalar için depolama seçenekleri

Azure Kubernetes Service (AKS) içinde çalışan uygulamaların veri depolaması ve alması gerekebilir. Bazı uygulama iş yükleri gereksiz ve boşaltılan düğümlerde yerel, hızlı depolama kullanabilir, diğerleri ise Azure platformunda daha düzenli veri birimlerinde devam eden bir depolama alanı gerektirir. 

Birden çok Pod şunlar olabilir:
* Aynı veri birimlerini paylaşma. 
* Pod, farklı bir düğümde yeniden zamanlanırsa veri birimlerini yeniden bağlayın. 

Son olarak, önemli verileri veya uygulama yapılandırma bilgilerini pods 'ye eklemeniz gerekebilir.

Bu makalede AKS 'de uygulamalarınıza depolama sağlayan temel kavramlar tanıtılmaktadır:

- [Birimler](#volumes)
- [Kalıcı birimler](#persistent-volumes)
- [Depolama sınıfları](#storage-classes)
- [Kalıcı birim talepleri](#persistent-volume-claims)

![Azure Kubernetes Services (AKS) kümesindeki uygulamalar için depolama seçenekleri](media/concepts-storage/aks-storage-options.png)

## <a name="volumes"></a>Birimler

Kubernetes tipik olarak tek tek kaynakları, atılabilir kaynakları olarak değerlendirir. Uygulamalar, veri kullanımı ve kalıcı veriler için kullanılabilir farklı yaklaşımlara sahiptir. Bir *birim* , veri ve uygulama yaşam döngüsü boyunca verileri depolamanın, almanın ve kalıcı hale getirmenin bir yolunu temsil eder.

Geleneksel birimler, Azure depolama tarafından desteklenen Kubernetes kaynakları olarak oluşturulur. Doğrudan Pod 'ye atanacak veri birimlerini el ile oluşturabilir veya Kubernetes onları otomatik olarak oluşturabilir. Veri birimleri, Azure disklerini veya Azure dosyalarını kullanabilir.

### <a name="azure-disks"></a>Azure Diskleri

*Azure disklerini* kullanarak bir Kubernetes *veri diski* kaynağı oluşturun. Diskler şunları kullanabilir:
* Yüksek performanslı SSD 'Ler tarafından desteklenen Azure Premium Depolama veya 
* Normal HDD 'Ler tarafından desteklenen Azure Standart depolama. 

> [!TIP]
>Çoğu üretim ve geliştirme iş yükleri için Premium depolama kullanın. 

Azure diskleri *Readwriteonce* olarak bağlandığından, yalnızca tek bir pod tarafından kullanılabilir. Aynı anda birden çok dizin tarafından erişilebilen depolama birimlerinde Azure dosyalarını kullanın.

### <a name="azure-files"></a>Azure Dosyaları
Azure depolama hesabı tarafından desteklenen bir SMB 3,0 paylaşımının pods 'ye bağlanması için *Azure dosyaları* 'nı kullanın. Dosyalar, verileri birden çok düğümde ve düğüm genelinde paylaşmanızı sağlar ve şunları kullanabilir:
* Yüksek performanslı SSD 'Ler tarafından desteklenen Azure Premium Depolama veya 
* Normal HDD 'Ler tarafından desteklenen Azure Standart depolama.

### <a name="volume-types"></a>Birim türleri
Kubernetes birimleri, bilgileri depolamak ve almak için yalnızca geleneksel bir diskten daha fazlasını temsil eder. Kubernetes birimleri, kapsayıcılar tarafından kullanılmak üzere bir pod 'a veri eklemenin bir yolu olarak da kullanılabilir. 

Kubernetes içindeki ortak birim türleri şunlardır:

#### <a name="emptydir"></a>emptyDir

Yaygın olarak bir pod için geçici alan olarak kullanılır. Pod içindeki tüm kapsayıcılar birimdeki verilere erişebilir. Bu birim türüne yazılan veriler yalnızca Pod 'un kullanım ömrü için devam ettirir. Pod 'u sildikten sonra birim silinir. Bu birim genellikle temel alınan yerel düğüm Disk depolamayı kullanır, ancak yalnızca düğümün belleğinde de bulunabilir.

#### <a name="secret"></a>gizli dizi

Gizli verileri, parolalar gibi yığınlara eklemek için *gizli* birimleri kullanabilirsiniz. 
1. Kubernetes API 'sini kullanarak bir gizli dizi oluşturun. 
1. Pod veya dağıtımınızı tanımlayın ve belirli bir gizli anahtar isteyin. 
    * Gizli diziler yalnızca, zamanlanmış Pod olan düğümlere sağlanır.
    * Gizli anahtar, diske yazılmadı ve *tmpfs*'de depolanır. 
1. Gizli dizi gerektiren bir düğümdeki son Pod 'yi sildiğinizde, gizli dizi, düğümün tmpfs 'den silinir. 
   * Gizli diziler belirli bir ad alanı içinde depolanır ve yalnızca aynı ad alanında yer alan Pod tarafından erişilebilir.

#### <a name="configmap"></a>configMap

Uygulama yapılandırma bilgileri gibi anahtar-değer çifti özelliklerini parçalara eklemek için *Configmap* kullanabilirsiniz. Uygulama yapılandırma bilgilerini bir Kubernetes kaynağı olarak tanımlayın, bunlar dağıtıldığı şekilde kolayca güncelleştirilir ve yeni düğüm örneklerine uygulanır. 

Gizli anahtar kullanma gibi:
1. Kubernetes API 'sini kullanarak bir ConfigMap oluşturun. 
1. Pod veya Deployment tanımladığınızda ConfigMap iste. 
   * Configmaps, belirli bir ad alanı içinde depolanır ve yalnızca aynı ad alanında yer alan Pod tarafından erişilebilir.

## <a name="persistent-volumes"></a>Kalıcı birimler

Pod yaşam döngüsünün bir parçası olarak tanımlanan ve oluşturulan birimler yalnızca Pod 'u silinceye kadar bulunur. Bir pod, özellikle de StatefulSets 'de bakım olayı sırasında farklı bir konakta yeniden zamanlanırsa, genellikle depolama alanının kalmasını bekler. *Kalıcı bir birim* (BD), Kubernetes API 'si tarafından oluşturulan ve yönetilen bir depolama kaynağıdır ve tek bir pod 'ın yaşam süresinden daha fazla olabilir.

PersistentVolume sağlamak için Azure disklerini veya dosyalarını kullanabilirsiniz. [Birimler](#volumes) bölümünde belirtildiği gibi, disk veya dosya seçimi genellikle verilere veya performans katmanına eşzamanlı erişim gereksinimi ile belirlenir.

![Azure Kubernetes Services (AKS) kümesindeki kalıcı birimler](media/concepts-storage/persistent-volumes.png)

PersistentVolume bir küme yöneticisi tarafından *statik* olarak veya Kubernetes API sunucusu tarafından *dinamik olarak* oluşturulabilir. Bir pod zamanlanırsa ve şu anda kullanılamayan depolama alanı istiyorsa Kubernetes, temel alınan Azure disk veya dosya depolama alanını oluşturabilir ve pod 'a ekler. Dinamik sağlama, hangi tür Azure depolama biriminin oluşturulması gerektiğini belirlemek için bir *Storageclass* kullanır.

## <a name="storage-classes"></a>Depolama sınıfları

Premium ve standart gibi farklı depolama katmanlarını tanımlamak için bir *Storageclass* oluşturabilirsiniz. 

StorageClass Ayrıca *reclaimPolicy* tanımlar. Pod 'u sildiğinizde ve kalıcı birim artık gerekmiyorsa, reclaimPolicy temel Azure depolama kaynağının davranışını denetler. Temel alınan depolama kaynağı silinebilir veya gelecekteki bir pod ile kullanım için saklanabilir.

AKS 'de, ağaç içi `StorageClasses` depolama eklentileri kullanılarak küme için dört başlangıç oluşturulur:

| İzin | Nedeni |
|---|---|
| `default` | Yönetilen bir disk oluşturmak için Azure Standartssd depolama kullanır. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure diskinin silinmesini sağlar. |
| `managed-premium` | Yönetilen bir disk oluşturmak için Azure Premium depolama kullanır. Geri kazanma ilkesi, kendisini kullanan kalıcı birim silindiğinde temeldeki Azure diskinin silinmesini sağlar. |
| `azurefile` | Azure Standart depolama kullanarak bir Azure dosya paylaşımının oluşturulmasını sağlar. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure dosya paylaşımının silinmesini sağlar. |
| `azurefile-premium` | Azure Premium Storage kullanarak bir Azure dosya paylaşımının oluşturulmasını sağlar. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure dosya paylaşımının silinmesini sağlar.|

Yeni kapsayıcı depolama arabirimi (CSı) dış eklentilerini (Önizleme) kullanan kümeler için aşağıdaki ek `StorageClasses` oluşturulur:

| İzin | Nedeni |
|---|---|
| `managed-csi` | Yönetilen bir disk oluşturmak için Azure Standartssd yerel olarak yedekli depolama (LRS) kullanır. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure diskinin silinmesini sağlar. Depolama sınıfı ayrıca kalıcı birimleri genişletilebilir olacak şekilde yapılandırır, yalnızca yeni boyutla kalıcı birim talebi düzenlemeniz gerekir. |
| `managed-csi-premium` | Yönetilen bir disk oluşturmak için Azure Premium yerel olarak yedekli depolama (LRS) kullanır. Geri kazanma ilkesi, kendisini kullanan kalıcı birim silindiğinde temeldeki Azure diskinin silinmesini sağlar. Benzer şekilde, bu depolama sınıfı kalıcı birimlerin genişletilmesini sağlar. |
| `azurefile-csi` | Azure Standart depolama kullanarak bir Azure dosya paylaşımının oluşturulmasını sağlar. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure dosya paylaşımının silinmesini sağlar. |
| `azurefile-csi-premium` | Azure Premium Storage kullanarak bir Azure dosya paylaşımının oluşturulmasını sağlar. Geri kazanma ilkesi, kullanılan kalıcı birim silindiğinde temel alınan Azure dosya paylaşımının silinmesini sağlar.|

Kalıcı bir birim için bir StorageClass belirtmediğiniz takdirde, varsayılan StorageClass kullanılır. Birimlerin kalıcı birimler istenirken gereken uygun depolama alanını kullandığından emin olun. 

Kullanarak ek gereksinimler için bir StorageClass oluşturabilirsiniz `kubectl` . Aşağıdaki örnek, Premium yönetilen diskleri kullanır ve pod 'u sildiğinizde temel alınan Azure diskinin *korunması* gerektiğini belirtir:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

> [!NOTE]
> AKS varsayılan depolama sınıflarını uzlaştırır ve bu depolama sınıflarında yaptığınız tüm değişikliklerin üzerine yazar.

## <a name="persistent-volume-claims"></a>Kalıcı birim talepleri

PersistentVolumeClaim, belirli bir StorageClass, erişim modu ve boyutun disk veya dosya depolamasını ister. Kubernetes API sunucusu, mevcut bir kaynak tanımlı StorageClass temelinde talebi karşılayamazsa, temel alınan Azure depolama kaynağını dinamik olarak sağlayabilir. 

Pod tanımı, birim Pod 'a bağlandıktan sonra birim bağlama içerir.

![Azure Kubernetes Services (AKS) kümesinde kalıcı birim talepleri](media/concepts-storage/persistent-volume-claims.png)

Depolama isteyen Pod 'a kullanılabilir bir depolama kaynağı atandıktan sonra, PersistentVolume bir PersistentVolumeClaim 'e *bağlanır* . Kalıcı birimler 1:1 talebe eşlenir.

Aşağıdaki örnek YAML bildirimi, *yönetilen-Premium* storageclass kullanan kalıcı bir birim talebi gösterir ve şu boyutta bir disk *5 gi* ister:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Pod tanımı oluşturduğunuzda şunları da belirtirsiniz:
* İstenen depolamayı istemek için kalıcı birim talebi. 
* Verilerinize veri okuma ve yazma için *birimsiz bağlama* . 

Aşağıdaki örnek YAML bildirimi, önceki kalıcı birim talebinin, */mnt/Azure*' da bir birimi bağlamak için nasıl kullanılabileceğini gösterir:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Bir Windows kapsayıcısında bir birimi bağlamak için, sürücü harfini ve yolunu belirtin. Örnek:

```yaml
...      
       volumeMounts:
        - mountPath: "d:"
          name: volume
        - mountPath: "c:\k"
          name: k-dir
...
```

## <a name="next-steps"></a>Sonraki adımlar

İlişkili en iyi uygulamalar için bkz. [AKS 'de depolama ve yedeklemeler Için en iyi uygulamalar][operator-best-practices-storage].

Azure diskleri veya Azure dosyaları kullanan dinamik ve statik birimler oluşturmayı öğrenmek için aşağıdaki nasıl yapılır makalelerine bakın:

- [Azure disklerini kullanarak statik birim oluşturma][aks-static-disks]
- [Azure dosyalarını kullanarak statik birim oluşturma][aks-static-files]
- [Azure disklerini kullanarak dinamik birim oluşturma][aks-dynamic-disks]
- [Azure dosyalarını kullanarak dinamik birim oluşturma][aks-dynamic-files]

Temel Kubernetes ve AKS kavramları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Kubernetes/AKS kümeleri ve iş yükleri][aks-concepts-clusters-workloads]
- [Kubernetes/AKS kimliği][aks-concepts-identity]
- [Kubernetes/AKS güvenliği][aks-concepts-security]
- [Kubernetes/AKS sanal ağları][aks-concepts-network]
- [Kubernetes/AKS ölçeği][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
