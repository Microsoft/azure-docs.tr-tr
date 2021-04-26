---
title: Azure Kubernetes Service (AKS) içindeki küme otomatik Scaler 'ı kullanma
description: Bir Azure Kubernetes Service (AKS) kümesindeki uygulama taleplerini karşılamak üzere kümenizi otomatik olarak ölçeklendirmek için küme otomatik olarak nasıl kullanacağınızı öğrenin.
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: f02b91f73320786716e356639d8134280325dc19
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776006"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ile bir kümeyi uygulama taleplerini karşılayacak şekilde otomatik olarak ölçeklendirme

Azure Kubernetes Service 'te (AKS) uygulama taleplerine devam etmek için, iş yüklerinizi çalıştıran düğümlerin sayısını ayarlamanız gerekebilir. Küme otomatik Scaler bileşeni, kümenizde kaynak kısıtlamaları nedeniyle zamanlanabilecek Pod 'leri izleyebilir. Sorunlar algılandığında, bir düğüm havuzundaki düğümlerin sayısı uygulama talebini karşılayacak şekilde artmıştır. Düğümler, düğüm sayısıyla daha sonra gerektiği şekilde azaldıkça, bir yük eksikliği olmaması için düzenli olarak kontrol edilir. AKS kümenizdeki düğüm sayısını otomatik olarak ölçeklendirme veya azaltma yeteneği, verimli ve ekonomik bir küme çalıştırmanızı sağlar.

Bu makalede, bir AKS kümesinde Küme otomatik olarak nasıl etkinleştirileceği ve yönetileceği gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, Azure CLı sürüm 2.0.76 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>Küme otomatik yüklemesi hakkında

Workday ve akşam ya da bir hafta sonu arasındaki uygulama taleplerini değiştirmek üzere ayarlamak için kümeler genellikle otomatik olarak ölçeklendirilmesi için bir yönteme ihtiyaç duyar. AKS kümeleri, iki şekilde ölçeklendirebilir:

* **Küme otomatik yüklemesi** , kaynak kısıtlamaları nedeniyle düğümlerde zamanlanabilecek düğüm sayısını izler. Küme daha sonra düğümlerin sayısını otomatik olarak arttırır.
* **Yatay Pod otomatik Scaler** , Kubernetes kümesinde ölçüm sunucusunu kullanarak pods 'nin kaynak talebini izler. Bir uygulamanın daha fazla kaynağa ihtiyacı varsa, yığınların sayısı talebi karşılamak üzere otomatik olarak artar.

![Küme otomatik olarak ve yatay Pod otomatik Scaler, gerekli uygulama taleplerini desteklemek için genellikle birlikte çalışır](media/autoscaler/cluster-autoscaler.png)

Hem yatay Pod otomatik Scaler hem de Cluster otomatik Scaler, gereken düğüm sayısını ve düğümleri de azaltabilir. Küme otomatik yüklemesi, bir süre için kullanılmayan kapasiteden fazla düğüm sayısını düşürür. Küme otomatik olarak kaldırılacak bir düğümdeki düğüm, kümede başka bir yerde güvenle zamanlanır. Küme otomatik olarak, aşağıdaki durumlarda olduğu gibi, Pod taşınmadığı takdirde ölçeği ölçeklendiremeyebilir:

* Pod, doğrudan oluşturulur ve dağıtım veya çoğaltma kümesi gibi bir denetleyici nesnesi tarafından yedeklenmez.
* Pod kesinti bütçesi (pdb) çok kısıtlayıcıdır ve pod sayısının belirli bir eşiğin altına düşmeye izin vermez.
* Pod, farklı bir düğümde zamanlanırsa, düğüm seçicileri veya benzeşim önleme kullanır.

Küme otomatik olarak ölçeklendirilmesine nasıl ölçeklenebileceğinize ilişkin daha fazla bilgi için bkz [. küme otomatik Scaler 'ın bir düğümü kaldırmasını engelleyebilir][autoscaler-scaledown]

Küme otomatik yüklemesi, ölçek olayları ve kaynak eşikleri arasındaki zaman aralıkları gibi şeyler için başlangıç parametrelerini kullanır. Küme otomatik tarafından kullanılan parametreler hakkında daha fazla bilgi için bkz. [Otomatik Scaler profilini kullanma](#using-the-autoscaler-profile).

Küme ve yatay Pod otomatik scalers birlikte çalışabilir ve genellikle bir kümede dağıtılır. Birleştirildiğinde, yatay Pod otomatik Scaler, uygulama talebini karşılamak için gereken sayıda Pod çalıştırmaya odaklanılmıştır. Küme otomatik yüklemesi, zamanlanmış pods 'yi desteklemek için gereken düğüm sayısını çalıştırmaya odaklanır.

> [!NOTE]
> Küme otomatik ölçeklendirme kullandığınızda el ile ölçekleme devre dışıdır. Küme otomatik olarak gerekli düğüm sayısını belirlemesine izin verin. Kümenizi el ile ölçeklendirmek isterseniz, [küme otomatik Scaler ' ı devre dışı bırakın](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS kümesi oluşturma ve küme otomatik Scaler 'ı etkinleştirme

AKS kümesi oluşturmanız gerekiyorsa, [az aks Create][az-aks-create] komutunu kullanın. Küme için düğüm havuzunda küme otomatik Scaler özelliğini etkinleştirmek ve yapılandırmak için `--enable-cluster-autoscaler` parametresini kullanın ve bir düğüm `--min-count` ve belirtin `--max-count` .

> [!IMPORTANT]
> Küme otomatik yüklemesi, bir Kubernetes bileşenidir. AKS kümesi düğümler için bir sanal makine ölçek kümesi kullansa da, Azure portal ölçek kümesi otomatik ölçek ayarlarını el ile etkinleştirmeyin veya Azure CLı kullanarak ayarları düzenleyin. Kubernetes kümesi otomatik olarak gerekli ölçek ayarlarını yönetmesine izin verin. Daha fazla bilgi için bkz. [düğüm kaynak grubundaki AKS kaynaklarını değiştirebilir miyim?][aks-faq-node-resource-group]

Aşağıdaki örnek, bir sanal makine ölçek kümesi tarafından desteklenen tek düğümlü havuz içeren bir AKS kümesi oluşturur. Ayrıca küme için düğüm havuzunda küme otomatik Scaler 'ı ve en az *1* ve en fazla *3* düğüm ayarlar:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Kümeyi oluşturmak ve küme otomatik Scaler ayarlarını yapılandırmak birkaç dakika sürer.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Küme otomatik algılama özelliğini etkinleştirmek için mevcut bir AKS kümesini güncelleştirme

Mevcut küme için düğüm havuzunda küme otomatik Scaler 'sini etkinleştirmek ve yapılandırmak için [az aks Update][az-aks-update] komutunu kullanın. Parametresini kullanın `--enable-cluster-autoscaler` ve bir düğüm `--min-count` ve belirtin `--max-count` .

> [!IMPORTANT]
> Küme otomatik yüklemesi, bir Kubernetes bileşenidir. AKS kümesi düğümler için bir sanal makine ölçek kümesi kullansa da, Azure portal ölçek kümesi otomatik ölçek ayarlarını el ile etkinleştirmeyin veya Azure CLı kullanarak ayarları düzenleyin. Kubernetes kümesi otomatik olarak gerekli ölçek ayarlarını yönetmesine izin verin. Daha fazla bilgi için bkz. [düğüm kaynak grubundaki AKS kaynaklarını değiştirebilir miyim?][aks-faq-node-resource-group]

Aşağıdaki örnek, küme için düğüm havuzunda küme otomatik yapılandırmasını etkinleştirmek üzere mevcut bir AKS kümesini güncelleştirir ve en az *1* ve en fazla *3* düğüm ayarlar:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Kümeyi güncellemek ve küme otomatik Scaler ayarlarını yapılandırmak birkaç dakika sürer.

## <a name="change-the-cluster-autoscaler-settings"></a>Küme otomatik Scaler ayarlarını değiştirme

> [!IMPORTANT]
> AKS kümenizde birden fazla düğüm havuzunuz varsa, [birden çok aracı havuzu ile otomatik ölçeklendirmeyi](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)atlayın. Birden çok aracı havuzu içeren kümeler, `az aks nodepool` yerine düğüm havuzuna özgü özellikleri değiştirmek için komut kümesini kullanmayı gerektirir `az aks` .

Önceki adımda, bir AKS kümesi oluşturmak veya var olan bir düğüm havuzunu güncelleştirmek için, küme otomatik algılama en düşük düğüm sayısı *1* olarak ayarlanmıştır ve en fazla düğüm sayısı *3* olarak ayarlanmıştır. Uygulamanız değiştikçe değişiklik yaparken, küme otomatik Scaler düğüm sayısını ayarlamanız gerekebilir.

Düğüm sayısını değiştirmek için [az aks Update][az-aks-update] komutunu kullanın.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Yukarıdaki örnek, *Myakscluster* içindeki tek düğümlü havuzda küme otomatik Scaler ' nı en az *1* ve en fazla *5* düğüm olarak güncelleştirir.

> [!NOTE]
> Küme otomatik algılama, her düğüm havuzunda ayarlanan en düşük ve en yüksek sayımlar temelinde ölçekleme kararları verir, ancak en az veya en fazla sayımlar güncelleştirildikten sonra bunları zorlamaz. Örneğin, geçerli düğüm sayısı 3 olduğunda en az 5 sayısını ayarlamak, havuzu hemen 5 ' e kadar ölçeklendirmeyecektir. Düğüm havuzundaki minimum sayı, geçerli düğüm sayısından daha yüksek bir değere sahipse, yeni Min veya Max ayarları, 2 yeni ek düğüm gerektirecek ve bir otomatik Scaler olayı tetikleyen yeterli sayıda unschedulable Pod olduğunda dikkate alınır. Ölçek olayından sonra, yeni sayı limitleri dikkate alınır.

Uygulamalarınızın ve hizmetlerinizin performansını izleyin ve küme otomatik Scaler düğüm sayılarını gerekli performansla eşleşecek şekilde ayarlayın.

## <a name="using-the-autoscaler-profile"></a>Otomatik Scaler profilini kullanma

Küme genelindeki otomatik Scaler profilindeki varsayılan değerleri değiştirerek küme otomatik Scaler ' inin daha ayrıntılı ayrıntılarını da yapılandırabilirsiniz. Örneğin, düğümler 10 dakika sonra kullanıldıktan sonra bir ölçek azaltma olayı oluşur. 15 dakikada bir çalışan iş yükleriniz varsa, otomatik Scaler profilini, kullanılan düğümler altında 15 veya 20 dakika sonra ölçeklendirmek üzere değiştirmek isteyebilirsiniz. Küme otomatik ayarlarını etkinleştirdiğinizde, farklı ayarlar belirtmediğiniz takdirde varsayılan bir profil kullanılır. Küme otomatik Scaler profili, güncelleştirebilmeniz için aşağıdaki ayarlara sahiptir:

| Ayar                          | Açıklama                                                                              | Varsayılan değer |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| tarama-Aralık                    | Kümenin ölçeği artırma veya azaltma için ne sıklıkta yeniden değerlendirildiğinde                                    | 10 saniye    |
| ölçeği azaltma-sonrası-ekleme       | Ölçek azaltma sonrasında ölçeği izleyen ölçeği artırma sonrasında ne kadar süre sonra                               | 10 dakika    |
| ölçeği azaltma-sonrası-silme    | Düğüm silme işleminin ne kadar süre sonra değerlendirmeyi azaltımını sürdürür                          | tarama-Aralık |
| ölçek azaltma-başarısız-sonra gecikme   | Değerlendirme özgeçmişinin ölçeklendirilmesi için ölçek azaltma hatası ne kadar süre sonra                     | 3 dakika     |
| ölçeği azaltma-gereksiz-saat         | Düğüm ölçek azaltma için uygun hale gelmeden önce ne kadar süreyle gereksiz olması gerekir                  | 10 dakika    |
| ölçeği daraltma-önceden hazırlanma-zaman          | Uygun olmayan bir düğümün ölçek azaltma için uygun olmadan önce ne kadar süreyle gereksiz olması gerekir         | 20 dakika    |
| ölçeği azaltma-kullanım eşiği | Düğüm kullanım düzeyi, bir düğümün ölçek azaltma için kabul edileceği, kapasiteye göre bölünen istenen kaynakların toplamı olarak tanımlanır | 0,5 |
| en yüksek-düzgün kapanma-sn     | Küme otomatik olarak bir düğümü ölçeklendirmeye çalışırken en fazla kaç saniye daha pod sonlandırmasını beklediği | 600 saniye   |
| Bakiye-benzer düğüm grupları      | Benzer düğüm havuzlarını algılar ve aralarındaki düğüm sayısını dengeler                 | yanlış         |
| iyi                         | Ölçeği artırma için kullanılacak düğüm havuzu [Genişleticisi](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) türü. Olası değerler: `most-pods` , `random` , `least-waste` , `priority` | rastgele | 
| --Yerel depolama ile atla-düğümler    | True Cluster, otomatik olarak kümeleyici olan düğümleri yerel depolamayla hiçbir şekilde silmez, örneğin EmptyDir veya HostPath | true |
| Atla-düğümler-sistem-pods      | True Cluster, otomatik olarak al, Kuto-System ' d e olan düğümleri hiçbir şekilde silmez (DaemonSet veya yansıtma Pod hariç) | true | 
| maksimum-boş-toplu silme            | Aynı anda silinebilecek en fazla boş düğüm sayısı                       | 10 düğüm      |
| Yeni-Pod-ölçeği artırma gecikme           | CA 'nın, Kubernetes Scheduler 'ın tüm yığınlarını zamanlamadan önce hareket etmesini istemediğiniz, veri bloğu/Batch ölçeği gibi senaryolar için, CA 'nın belirli bir yaş olmadan önce zamanlanmamış Bob 'yi yoksaymasını söyleyebilirsiniz.                                                                                                                | 0 saniye    |
| en yüksek-toplam-önceden hazırlanmamış-yüzde     | Kümedeki izin verilen en fazla düğüm yüzdesi. Bu yüzde aşıldıktan sonra CA durdurma işlemleri | %45 |
| maksimum düğüm sağlama süresi          | Otomatik gizleme 'nin bir düğümün sağlanması için beklediği maksimum süre                           | 15 dakika    |   
| Tamam-toplam-Ready-Count           | En yüksek toplam-hazırlanmamış-yüzdeden bağımsız olarak izin verilen, izin verilmeyen düğüm sayısı            | 3 düğüm       |

> [!IMPORTANT]
> Küme otomatik Scaler profili, küme otomatik Scaler 'ı kullanan tüm düğüm havuzlarını etkiler. Düğüm havuzu başına bir otomatik Scaler profili ayarlayamazsınız.
>
> Küme otomatik Scaler profili, Azure CLı 'nin sürüm *2.11.1* veya üstünü gerektirir. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Mevcut bir AKS kümesinde Küme otomatik Scaler profilini ayarlama

Kümenizin küme otomatik Scaler profilini ayarlamak için, *cluster-otomatik Scaler-profile* parametresiyle [az aks Update][az-aks-update-preview] komutunu kullanın. Aşağıdaki örnek, tarama aralığı ayarını profilde 30 saniye olarak yapılandırır.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

Kümedeki düğüm havuzlarında küme otomatik Scaler 'ı etkinleştirdiğinizde, bu kümeler küme otomatik Scaler profilini de kullanacaktır. Örnek:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> Küme otomatik Scaler profilini ayarladığınızda, küme otomatik olarak etkinleştirilen tüm mevcut düğüm havuzları profili hemen kullanmaya başlar.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>AKS kümesi oluştururken küme otomatik Scaler profilini ayarlama

Kümenizi oluştururken *cluster-otomatik Scaler-profile* parametresini de kullanabilirsiniz. Örnek:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

Yukarıdaki komut bir AKS kümesi oluşturur ve küme genelinde otomatik Scaler profili için tarama aralığını 30 saniye olarak tanımlar. Bu komut, ilk düğüm havuzunda küme otomatik Scaler öğesini de sağlar, en düşük düğüm sayısını 1 olarak ve en fazla düğüm sayısını 3 olarak ayarlar.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Küme otomatik Scaler profilini varsayılan değerlere sıfırla

Kümenizdeki küme otomatik Scaler profilini sıfırlamak için [az aks Update][az-aks-update-preview] komutunu kullanın.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Küme otomatik Scaler 'ı devre dışı bırakma

Artık küme otomatik özelliğini kullanmak istemiyorsanız, parametreyi belirterek [az aks Update][az-aks-update-preview] komutunu kullanarak devre dışı bırakabilirsiniz `--disable-cluster-autoscaler` . Küme otomatik yüklemesi devre dışı bırakıldığında düğümler kaldırılmaz.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

[Az aks Scale][az-aks-scale] komutunu kullanarak küme otomatik Scaler 'ı devre dışı bıraktıktan sonra kümenizi el ile ölçekleyebilirsiniz. Yatay Pod otomatik Scaler ' ı kullanırsanız, bu özellik küme otomatik olarak devre dışı bırakılmış olarak çalışmaya devam eder, ancak tüm düğüm kaynakları kullanımda ise, Pod çalışmayabilir.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Devre dışı bırakılan bir kümeyi otomatik olarak yeniden etkinleştirin

Küme otomatik olarak var olan bir kümede yeniden etkinleştirmek istiyorsanız, [az aks Update][az-aks-update-preview] komutunu kullanarak `--enable-cluster-autoscaler` ,, `--min-count` ve parametrelerini belirterek yeniden etkinleştirebilirsiniz `--max-count` .

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Küme otomatik gizleme günlüklerini ve durumunu alma

Otomatik Scaler olaylarını tanılamak ve hatalarını ayıklamak için, Günlükler ve durum otomatik Scaler eklentisi 'nden alınabilir.

AKS, küme otomatik denetimini sizin adınıza yönetir ve yönetilen denetim düzlemine çalıştırır. CA 'dan günlükleri ve işlemleri görmek için denetim düzlemi düğümünü etkinleştirebilirsiniz.

Günlükleri küme otomatik Scaler 'dan Log Analytics gönderilecek şekilde yapılandırmak için aşağıdaki adımları izleyin.

1. Küme otomatik Scaler günlüklerini Log Analytics 'e göndermek için kaynak günlükleri için bir kural ayarlayın. [Yönergeler burada ayrıntılı olarak verilmiştir][aks-view-master-logs], `cluster-autoscaler` "Günlükler" için seçenekler seçerken kutuyu kontrol edin.
1. Azure portal aracılığıyla kümenizdeki "Günlükler" bölümünü seçin.
1. Aşağıdaki örnek sorguyu Log Analytics olarak girin:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Alınacak Günlükler olduğu sürece, aşağıdaki örneğe benzer Günlükler görmeniz gerekir.

![Log Analytics günlükleri](media/autoscaler/autoscaler-logs.png)

Küme otomatik olarak bir adlandırılmış sistem durumu da yazar `configmap` `cluster-autoscaler-status` . Bu günlükleri almak için aşağıdaki `kubectl` komutu yürütün. Küme otomatik olarak yapılandırılmış her düğüm havuzu için bir sistem durumu bildirilir.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Otomatik olarak kaydedilen öğeler hakkında daha fazla bilgi edinmek için [Kubernetes/otomatik Scaler GitHub PROJESINDE][kubernetes-faq]SSS makalesini okuyun.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Birden çok düğüm havuzu etkin olan küme otomatik Scaler 'ı kullanma

Küme otomatik Scaler, [birden çok düğüm havuzu][aks-multiple-node-pools] etkin olarak birlikte kullanılabilir. Birden çok düğüm havuzunun nasıl etkinleştirileceğini ve var olan bir kümeye ek düğüm havuzları nasıl ekleneceğini öğrenmek için bu belgeyi izleyin. Her iki özelliği birlikte kullanırken kümedeki her bir düğüm havuzunda küme otomatik Scaler ' ı etkinleştirir ve her birine benzersiz otomatik ölçeklendirme kuralları geçirebilir.

Aşağıdaki komut, bu belgede daha önce [ilk yönergeleri](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) izlediğinizi ve mevcut düğüm havuzunun en büyük sayısını *3* ' ten *5*' e kadar güncelleştirmek istediğinizi varsayar. Mevcut bir düğüm havuzunun ayarlarını güncelleştirmek için [az aks nodepool Update][az-aks-nodepool-update] komutunu kullanın.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Küme otomatik Scaler, [az aks nodepool Update][az-aks-nodepool-update] ile devre dışı bırakılabilir ve parametresi geçiliyor `--disable-cluster-autoscaler` .

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Küme otomatik yapılandırmasını var olan bir kümede yeniden etkinleştirmek istiyorsanız, [az aks nodepool Update][az-aks-nodepool-update] komutunu kullanarak `--enable-cluster-autoscaler` ,, ve parametrelerini belirterek yeniden etkinleştirebilirsiniz `--min-count` `--max-count` .

> [!NOTE]
> Birden çok bölgeye yayılan ve Volume topik zamanlaması gibi bölgelerle ilgili zamanlama özelliklerinden yararlanan nodepools ile küme otomatik Scaler kullanmayı planlıyorsanız, her bölge için bir nodepool ve `--balance-similar-node-groups` Otomatik Scaler profili aracılığıyla etkinleştirilir. Bu, otomatik olarak ölçeklenebilmesini sağlayacak ve nodepools dengeli olan boyutları denemeye devam eder.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, AKS düğümlerinin sayısını otomatik olarak ölçeklendirirsiniz. Ayrıca, uygulamanızı çalıştıran Pod sayısını otomatik olarak ayarlamak için yatay Pod otomatik Scaler ' yı da kullanabilirsiniz. Yatay Pod otomatik Scaler 'ı kullanma adımları için bkz. [aks 'de Uygulamaları ölçeklendirme][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why