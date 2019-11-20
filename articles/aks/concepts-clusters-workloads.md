---
title: Kavramlar-Azure Kubernetes Hizmetleri (AKS) için Kubernetes temelleri
description: Kubernetes 'in temel kümesini ve iş yükü bileşenlerini ve bunların Azure Kubernetes Service (AKS) özellikleriyle ilişkisini öğrenin
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: mlearned
ms.openlocfilehash: 78fb06c7ecd20d8ed2af40bcc294f2fb1b166d96
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74120629"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmeti (AKS) için Kubernetes temel kavramları

Uygulama geliştirme, kapsayıcı tabanlı bir yaklaşıma doğru şekilde taşındıkça, kaynakları düzenleme ve yönetme gereksinimi önemlidir. Kubernetes, hataya dayanıklı uygulama iş yüklerinin güvenilir bir şekilde planlanmasına olanak sağlayan önde gelen platformudur. Azure Kubernetes hizmeti (AKS), kapsayıcı tabanlı uygulama dağıtımını ve yönetimini kolaylaştıran, yönetilen bir Kubernetes sunumudur.

Bu makalede, *Denetim düzlemi*, *düğümler*ve *düğüm havuzları*gibi temel Kubernetes altyapı bileşenleri tanıtılmaktadır. *Pod*, *dağıtımlar*ve *kümeler* gibi iş yükü kaynakları, kaynakların *ad alanlarına*nasıl gruplandırılmasına yönelik olarak da sunulmuştur.

## <a name="what-is-kubernetes"></a>Kubernetes nedir?

Kubernetes kapsayıcı tabanlı uygulamaları ve bunların ilişkili ağ ve depolama bileşenlerini yöneten hızlı bir şekilde gelişen platformudur. Odak, temel altyapı bileşenlerinde değil uygulama iş yüklerinde yer alır. Kubernetes, yönetim işlemlerine yönelik güçlü bir API kümesiyle desteklenen dağıtımlar için bildirime dayalı bir yaklaşım sağlar.

Kubernetes 'in sunduğu ve bu uygulama bileşenlerinin kullanılabilirliğini yöneten modern, taşınabilir ve mikro hizmet tabanlı uygulamalar oluşturabilir ve çalıştırabilirsiniz. Kubernetes hem durum bilgisi olmayan hem de durum bilgisi olan uygulamaları destekleyerek, bu, mikro hizmet tabanlı uygulamaların benimsenmesiyle devam etmektedir.

Açık bir platform olarak Kubernetes, uygulamalarınızı tercih ettiğiniz programlama dili, işletim sistemi, kitaplıklar veya mesajlaşma veri yolu ile oluşturmanıza olanak sağlar. Mevcut sürekli tümleştirme ve sürekli teslim (CI/CD) araçları, sürümleri planlamak ve dağıtmak için Kubernetes ile tümleştirilebilir.

Azure Kubernetes hizmeti (AKS), dağıtım ve temel yönetim görevlerinin karmaşıklığını azaltan, yükseltme, yükseltme dahil, yönetilen bir Kubernetes hizmeti sağlar. AKS denetim düzlemi, Azure platformu tarafından yönetilir ve yalnızca uygulamalarınızı çalıştıran AKS düğümleri için ödeme yaparsınız. AKS 'ler, açık kaynaklı Azure Kubernetes hizmet altyapısının ([aks-Engine][aks-engine]) üzerine kurulmuştur.

## <a name="kubernetes-cluster-architecture"></a>Kubernetes kümesi mimarisi

Bir Kubernetes kümesi iki bileşene ayrılmıştır:

- *Denetim düzlemi* düğümleri, uygulama iş yüklerinin temel Kubernetes hizmetlerini ve düzenlemesini sağlar.
- *Düğümler* , uygulama iş yüklerinizi çalıştırır.

![Kubernetes denetim düzlemi ve düğüm bileşenleri](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Denetim düzlemi

Bir AKS kümesi oluşturduğunuzda, otomatik olarak bir denetim düzlemi oluşturulup yapılandırılır. Bu denetim düzlemi, kullanıcıdan soyutlandığından yönetilen bir Azure kaynağı olarak sağlanır. Denetim düzlemi için herhangi bir maliyet yoktur, yalnızca AKS kümesinin bir parçası olan düğümler vardır.

Denetim düzlemi aşağıdaki temel Kubernetes bileşenlerini içerir:

- *kuin-apiserver* -API sunucusu, temeldeki Kubernetes API 'lerinin sunulma şekli. Bu bileşen, `kubectl` veya Kubernetes panosu gibi yönetim araçları için etkileşim sağlar.
- *etcd* -Kubernetes kümenizin ve yapılandırmanızın durumunu korumak için, yüksek oranda kullanılabilir *Etcd* , Kubernetes içindeki bir anahtar değer deposudur.
- *kuin-Scheduler* -uygulamaları oluştururken veya ölçeklendirerek, Zamanlayıcı hangi düğümlerin iş yükünü çalıştırabileceğini belirler ve bunları başlatır.
- *kuin-Controller-Manager* -Denetleyici Yöneticisi, Pod 'yi çoğaltma ve düğüm işlemlerini işleme gibi eylemleri gerçekleştiren bir dizi küçük denetleyiciyi fazla görür.

AKS, adanmış bir API sunucusu, Zamanlayıcı vb. ile tek kiracılı bir denetim düzlemi sağlar. Düğümlerin sayısını ve boyutunu tanımlarsınız ve Azure platformu denetim düzlemi ve düğümleri arasındaki güvenli iletişimi yapılandırır. Denetim düzlemi ile etkileşim, `kubectl` veya Kubernetes panosu gibi Kubernetes API 'Leri aracılığıyla oluşur.

Bu yönetilen denetim düzlemi, yüksek oranda kullanılabilir bir *etcd* deposu gibi bileşenleri yapılandırmanız gerekmediği anlamına gelir, ancak aynı zamanda denetim düzlemini doğrudan erişemeyeceğiniz anlamına gelir. Kubernetes yükseltmeleri, denetim düzlemini ve sonra düğümleri yükselten Azure CLı veya Azure portal aracılığıyla düzenlenir. Olası sorunları gidermek için, Azure Izleyici günlükleri aracılığıyla denetim düzlemi günlüklerini gözden geçirebilirsiniz.

Denetim düzlemi 'ni belirli bir şekilde yapılandırmanız veya buna doğrudan erişmeniz gerekiyorsa, [aks-Engine][aks-engine]kullanarak kendi Kubernetes kümenizi dağıtabilirsiniz.

İlişkili en iyi uygulamalar için bkz. [AKS 'de küme güvenliği ve yükseltmeleri Için en iyi uygulamalar][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Düğümler ve düğüm havuzları

Uygulamalarınızı ve destekleyici hizmetleri çalıştırmak için bir Kubernetes *düğümüne*ihtiyacınız vardır. AKS kümesi, Kubernetes düğüm bileşenlerini ve kapsayıcı çalışma zamanını çalıştıran bir Azure sanal makinesi (VM) olan bir veya daha fazla düğüme sahiptir:

- `kubelet`, denetim düzleminden ve istenen kapsayıcıları çalıştırmanın zamanlarından düzenleme isteklerini işleyen Kubernetes aracısıdır.
- Sanal ağ, her düğümde *kug-proxy* tarafından işlenir. Proxy, ağ trafiğini yönlendirir ve hizmetler ve pods için IP adresini yönetir.
- *Kapsayıcı çalışma zamanı* , Kapsayıcılı uygulamaların sanal ağ ve depolama gibi ek kaynaklarla çalışmasına ve bunlarla etkileşime geçmesini sağlayan bileşendir. AKS 'de, Moby kapsayıcı çalışma zamanı olarak kullanılır.

![Bir Kubernetes düğümü için Azure sanal makinesi ve destekleyici kaynaklar](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Düğümleriniz için Azure VM boyutu, kaç tane CPU, bellek miktarı ve kullanılabilir depolama alanı boyutu ve türü (yüksek performanslı SSD veya normal HDD gibi) tanımlar. Büyük miktarlarda CPU ve bellek veya yüksek performanslı depolama gerektiren uygulamalar gereksinimini tahmin ediyorsanız, düğüm boyutunu uygun şekilde planlayın. Ayrıca, talepleri karşılamak için AKS kümenizdeki düğüm sayısını da ölçeklendirebilirsiniz.

AKS 'de, kümenizdeki düğümlerin VM görüntüsü şu anda Ubuntu Linux veya Windows Server 2019 ' i temel alır. Bir AKS kümesi oluşturduğunuzda veya düğüm sayısını ölçeklendirirseniz, Azure platformu istenen sayıda VM oluşturur ve bunları yapılandırır. Yapmanız için el ile yapılandırma yoktur. Aracı düğümleri standart sanal makineler olarak faturalandırılır. bu nedenle, kullanmakta olduğunuz VM boyutu ( [Azure ayırmaları][reservation-discounts]dahil) üzerinde bulunan tüm indirimler otomatik olarak uygulanır.

Farklı bir konak işletim sistemi, kapsayıcı çalışma zamanı kullanmanız veya özel paketler eklemeniz gerekiyorsa, [aks-Engine][aks-engine]kullanarak kendi Kubernetes kümenizi dağıtabilirsiniz. Yukarı akış `aks-engine` özellikleri yayınlar ve AKS kümelerinde resmi olarak desteklenmeye başlamadan önce yapılandırma seçenekleri sağlar. Örneğin, Moby dışında bir kapsayıcı çalışma zamanı kullanmak isterseniz, geçerli ihtiyaçlarınızı karşılayan bir Kubernetes kümesini yapılandırmak ve dağıtmak için `aks-engine` kullanabilirsiniz.

### <a name="resource-reservations"></a>Kaynak ayırmaları

Düğüm kaynakları, kümenin bir parçası olarak düğüm işlevini yapmak için AKS tarafından kullanılır. Bu, AKS 'te kullanıldığında, düğümünüz için toplam kaynak ve kaynak ayırıcılar tablosu arasında bir tutarsızlık oluşturabilir. Bu, Kullanıcı tarafından dağıtılan pods 'lerin istek ve sınırlarını ayarlarken dikkat etmeniz önemlidir.

Bir düğümün allocatable kaynaklarını bulmak için şunu çalıştırın:
```kubectl
kubectl describe node [NODE_NAME]

```

Düğüm performansını ve işlevselliğini sürdürmek için, kaynaklar her bir düğüme AKS tarafından ayrılır. Düğüm, kaynaklarda daha büyük bir süre büyüdükçe, daha yüksek miktarda Kullanıcı tarafından dağıtılan yük olması nedeniyle kaynak ayırma artar.

>[!NOTE]
> Container Insights (OMS) gibi AKS eklentilerinin kullanılması ek düğüm kaynakları kullanacaktır.

- **CPU** Ile ayrılmış CPU, ek özellikleri çalıştırmak nedeniyle daha az ayrılamayabilir CPU türüne ve küme yapılandırmasına bağımlıdır

| Konaktaki CPU çekirdekleri | 1 | 2 | 4 | 8 | 16 | 32|64|
|---|---|---|---|---|---|---|---|
|Kule ayrılmış (miliçekirdekler)|60|100|140|180|260|420|740|

- **Bellek** için ayrılan bellek, iki değerin toplamını içerir

1. Kubelet arka plan programı, kapsayıcı oluşturma ve sonlandırmayı yönetmek için tüm Kubernetes aracı düğümlerine yüklenir. AKS ' de varsayılan olarak, bu arka plan programı aşağıdaki çıkarma kuralına sahiptir: bellek. kullanılabilir < 750Mı, bu da bir düğümün her zaman en az 750 mi ayrıma göre olması gerektiği anlamına gelir.  Bir ana bilgisayar kullanılabilir bellek eşiğinin altındaysa, kubelet konak makinede belleği boşaltmak ve korumak için çalışan bir yığından birini sonlandırır.

2. İkinci değer, kubelet arka plan programının düzgün şekilde çalışması için (kuuya-ayrılmış) bir aşamalı bellek hızı değeridir.
    - ilk 4 GB belleğin %25 ' i
    - sonraki 4 GB belleğin %20 ' si (8 GB 'a kadar)
    - Sonraki 8 GB belleğin %10 ' ından (16 GB 'a kadar)
    - sonraki 112 GB belleğin %6 ' ından (128 GB 'a kadar)
    - 128 GB üzerinde herhangi bir belleğin %2 ' i

Kubernetes ve aracı düğümlerini sağlıklı tutmak için uygulanan bu iki tanımlı kuralın sonucu olarak, ayrılanmasa CPU 'SU ve bellek miktarı düğümün kendisinden daha az görünür. Yukarıda tanımlanan kaynak ayırmaları değiştirilemez.

Örneğin, bir düğüm 7 GB sunuyorsa, bellek ayırıcıdan %34 ' ı rapor eder:

`750Mi + (0.25*4) + (0.20*3) = 0.786GB + 1 GB + 0.6GB = 2.386GB / 7GB = 34% reserved`

Kubernetes için ayırmaların yanı sıra, temeldeki düğüm işletim sistemi, işletim sistemi işlevlerini sürdürmek için CPU ve bellek kaynaklarının bir miktarını da ayırır.

İlişkili en iyi uygulamalar için bkz. [AKS 'deki temel Zamanlayıcı özellikleri Için en iyi uygulamalar][operator-best-practices-scheduler].

### <a name="node-pools"></a>Düğüm havuzları

Aynı yapılandırmanın düğümleri, *düğüm havuzlarında*birlikte gruplandırılır. Bir Kubernetes kümesi bir veya daha fazla düğüm havuzu içerir. Düğüm ve boyutun ilk sayısı, *varsayılan bir düğüm havuzu*oluşturan bir aks kümesi oluşturduğunuzda tanımlanır. AKS 'deki bu varsayılan düğüm havuzu, aracı düğümlerinizi çalıştıran temel VM 'Leri içerir.

> [!NOTE]
> Kümenizin güvenilir bir şekilde çalışmasını sağlamak için varsayılan düğüm havuzunda en az 2 (iki) düğüm çalıştırmanız gerekir.

Bir AKS kümesini ölçeklendirerek veya yükselttiğinizde, eylem varsayılan düğüm havuzunda gerçekleştirilir. Ayrıca belirli bir düğüm havuzunu ölçeklendirmeyi veya yükseltmeyi de tercih edebilirsiniz. Yükseltme işlemleri için, tüm düğümler başarıyla yükseltilene kadar, çalışan kapsayıcılar düğüm havuzundaki diğer düğümlerde zamanlanır.

AKS 'de birden çok düğüm havuzu kullanma hakkında daha fazla bilgi için bkz. [AKS 'deki bir küme için birden çok düğüm havuzu oluşturma ve yönetme][use-multiple-node-pools].

### <a name="node-selectors"></a>Düğüm seçicileri

Birden çok düğüm havuzu içeren bir AKS kümesinde, belirli bir kaynak için hangi düğüm havuzunun kullanılacağını Kubernetes Scheduler 'a bildirmeniz gerekebilir. Örneğin, giriş denetleyicileri Windows Server düğümlerinde çalıştırılmamalıdır (Şu anda AKS 'de önizlemededir). Düğüm seçicileri, bir pod 'ın zamanlanması gereken yeri denetlemek için düğüm işletim sistemi gibi çeşitli parametreleri tanımlamanıza olanak sağlar.

Aşağıdaki temel örnek, *"Beta.Kubernetes.io/OS"* düğüm seçicisini kullanarak bir Linux düğümündeki NGINX örneğini zamanlar: Linux:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Yığınların nerede zamanlandığını denetleme hakkında daha fazla bilgi için bkz. [AKS 'de gelişmiş Zamanlayıcı özellikleri Için en iyi uygulamalar][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Podlar

Kubernetes, uygulamanızın bir örneğini çalıştırmak için *Pod* 'yi kullanır. Pod, uygulamanızın tek bir örneğini temsil eder. Bir pod 'un birden çok kapsayıcı içerebileceği gelişmiş senaryolar olsa da, pods 'nin genellikle kapsayıcı ile 1:1 eşlemesi vardır. Bu çok Kapsayıcılı FID 'ler aynı düğümde birlikte zamanlanır ve kapsayıcıların ilgili kaynakları paylaşmasına izin verir.

Pod oluşturduğunuzda, belirli miktarda CPU veya bellek kaynağı istemek için *kaynak sınırlarını* tanımlayabilirsiniz. Kubernetes Zamanlayıcı, isteği karşılamak için kaynakları kullanılabilir kaynaklarla bir düğümde çalışacak şekilde zamanlamaya çalışır. Ayrıca, belirli bir pod 'un temeldeki düğümden çok fazla bilgi işlem kaynağı tüketmesini önleyen en fazla kaynak sınırı belirtebilirsiniz. Kubernetes Scheduler 'ın hangi kaynakların gerekli ve izin verileceğini anlamasına yardımcı olmak için tüm yığınların kaynak sınırlarını içermesi en iyi uygulamadır.

Daha fazla bilgi için bkz. [Kubernetes Pod][kubernetes-pods] ve [Kubernetes Pod yaşam döngüsü][kubernetes-pod-lifecycle].

Pod, mantıksal bir kaynaktır, ancak kapsayıcı (ler) uygulama iş yüklerinin çalıştığı yerdir. Kubernetes 'in sağladığı yüksek kullanılabilirlik ve artıklık özelliklerinden bazıları genellikle kısa ömürlü, atılabilir kaynaklardır ve tek bir zamanlanmış Pod tarafından kaçırılabilirdi. Bunun yerine, Pod genellikle dağıtım denetleyicisi gibi Kubernetes *denetleyicileri*tarafından dağıtılır ve yönetilir.

## <a name="deployments-and-yaml-manifests"></a>Dağıtımlar ve YAML bildirimleri

*Dağıtım* , Kubernetes dağıtım denetleyicisi tarafından yönetilen bir veya daha fazla özdeş Pod 'yi temsil eder. Dağıtım, oluşturulacak *çoğaltma* sayısını (pod) tanımlar ve Kubernetes Zamanlayıcı, düğüm veya düğümlerin sorun yaşadığında, sağlıklı düğümlerde ek yığınların zamanlanmasını sağlar.

Pads yapılandırmalarını, kullanılan kapsayıcı görüntüsünü veya bağlı depolamayı değiştirmek için dağıtımları güncelleştirebilirsiniz. Dağıtım denetleyicisi belirtilen sayıda kopyayı boşaltır ve sonlandırır, yeni dağıtım tanımından çoğaltmalar oluşturur ve dağıtımdaki tüm çoğaltmalar güncelleştirilene kadar işlemi devam ettirir.

AKS 'teki çoğu durum bilgisiz uygulamaların, tek tek DS zamanlaması yerine dağıtım modelini kullanması gerekir. Kubernetes, küme içinde gerekli sayıda çoğaltmanın çalıştığından emin olmak için dağıtımların durumunu ve durumunu izleyebilir. Yalnızca tek tek Pod 'yi zamanladığınızda, bir sorunla karşılaştıklarında, düğüm yeniden başlatılmaz ve geçerli düğümleri bir sorunla karşılaştığında sağlıklı düğümlerde yeniden planlanmazlar.

Bir uygulama, yönetim kararlarının yapılabilmesi için her zaman kullanılabilir bir örnek çekirdeği gerektiriyorsa, bu özelliği bir güncelleştirme işleminin kesintiye uğramasını istemezsiniz. *Pod kesinti bütçeleri* , bir güncelleştirme veya düğüm yükseltmesi sırasında bir dağıtımda kaç çoğaltmanın alınacağını belirlemek için kullanılabilir. Örneğin, dağıtımınızda *5* çoğaltmalarınız varsa, tek seferde yalnızca bir çoğaltmanın silinmesini/yeniden zamanlanmasını sağlamak için *4* ' ün Pod kesintisini tanımlayabilirsiniz. Pod kaynak limitleriyle birlikte, en iyi uygulama, her zaman en az sayıda çoğaltma olmasını gerektiren uygulamalarda Pod kesinti bütçeleri tanımlamaktır.

Dağıtımlar genellikle `kubectl create` veya `kubectl apply`ile oluşturulur ve yönetilir. Bir dağıtım oluşturmak için, YAML (YAML Aini biçimlendirme dili) biçiminde bir bildirim dosyası tanımlarsınız. Aşağıdaki örnek NGıNX Web sunucusunun temel bir dağıtımını oluşturur. Dağıtım, oluşturulacak *3* çoğaltma ve bağlantı noktası *80* ' nin kapsayıcıda açık olduğunu belirtir. Kaynak istekleri ve sınırları, CPU ve bellek için de tanımlanır.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

YAML bildiriminde yük dengeleyiciler gibi hizmetler de dahil olmak üzere daha karmaşık uygulamalar oluşturulabilir.

Daha fazla bilgi için bkz. [Kubernetes dağıtımları][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Held ile paket yönetimi

Kubernetes 'te uygulamaları yönetmeye yönelik yaygın bir yaklaşım [Held][helm]ile yapılır. Kaynak dağıtmak için uygulama kodunun paketlenmiş bir sürümünü ve Kubernetes YAML bildirimlerini içeren mevcut genel Held *grafiklerini* oluşturabilir ve kullanabilirsiniz. Bu HELI grafikleri yerel olarak veya [Azure Container Registry Held grafik deposu][acr-helm]gibi bir uzak depoda depolanabilir.

Held 'yi kullanmak için Kubernetes kümenize *Tiller* adlı bir sunucu bileşeni yüklenir. Tiller, küme içindeki grafiklerin yüklenmesini yönetir. HELI istemcisinin kendisi bilgisayarınıza yerel olarak yüklenir veya [Azure Cloud Shell][azure-cloud-shell]içinde kullanılabilir. İstemcisiyle hele grafikleri arayabilir veya oluşturabilir, sonra bunları Kubernetes kümenize yükleyebilirsiniz.

![Held, Kubernetes kümesi içinde kaynak oluşturan bir istemci bileşeni ve sunucu tarafı Tiller bileşeni içerir](media/concepts-clusters-workloads/use-helm.png)

Daha fazla bilgi için bkz. [Azure Kubernetes hizmeti 'nde (AKS) Held ile uygulama yüklemesi][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets ve DaemonSets

Dağıtım denetleyicisi, kullanılabilir kaynaklarla kullanılabilir olan herhangi bir düğümde verilen sayıda kopyayı çalıştırmak için Kubernetes zamanlayıcısını kullanır. Dağıtımları kullanmanın bu yaklaşımı, durum bilgisi olmayan uygulamalar için yeterli olabilir, ancak kalıcı bir adlandırma kuralı veya depolama gerektiren uygulamalar için kullanılamaz. Bir küme içindeki her düğümde veya seçili düğümlerde bir çoğaltmanın mevcut olmasını gerektiren uygulamalar için, dağıtım denetleyicisi çoğaltmaların düğümlerde nasıl dağıtıldığını göz atalım.

Bu tür uygulamaları yönetmenize olanak sağlayan iki Kubernetes kaynağı vardır:

- *Statefulsets* -depolama gibi tek bir pod yaşam döngüsünün ötesinde uygulamaların durumunu koruyun.
- *DaemonSets* -Kubernetes Bootstrap işleminin başlarında, her düğümde çalışan bir örnek olduğundan emin olun.

### <a name="statefulsets"></a>StatefulSets

Modern uygulama geliştirme genellikle durum bilgisiz uygulamalar için amaçlar, ancak *statefulsets* , veritabanı bileşenlerini içeren uygulamalar gibi durum bilgisi olan uygulamalar için kullanılabilir. Statefulset, bir veya daha fazla özdeş Pod 'nin oluşturulup yönetildiği bir dağıtıma benzer. Bir StatefulSet içindeki çoğaltmalar, dağıtıma, ölçeğe, yükseltmelere ve sonlandırmalara yönelik düzgün, sıralı bir yaklaşıma uyar. Bir StatefulSet ile, çoğaltma yeniden zamanlanırsa, adlandırma kuralı, ağ adları ve depolama korunur.

Uygulamayı `kind: StatefulSet`kullanarak YAML biçiminde tanımlarsınız ve StatefulSet denetleyicisi, gerekli çoğaltmaların dağıtımını ve yönetimini işler. Veriler, Azure yönetilen diskler veya Azure dosyaları tarafından belirtilen kalıcı depolama alanına yazılır. StatefulSets ile, StatefulSet silindiğinde bile temeldeki kalıcı depolama alanı kalır.

Daha fazla bilgi için bkz. [Kubernetes StatefulSets][kubernetes-statefulsets].

Bir StatefulSet içindeki çoğaltmalar zamanlanır ve bir AKS kümesindeki tüm kullanılabilir düğümlerde çalıştırılır. Bir düğümde, küme içindeki en az bir pod 'ın çalıştığından emin olmanız gerekiyorsa, bunun yerine bir DaemonSet kullanabilirsiniz.

### <a name="daemonsets"></a>DaemonSets

Belirli günlük koleksiyonu veya izleme ihtiyaçları için, belirli bir pod 'ı tüm veya seçili düğümlerde çalıştırmanız gerekebilir. Bir veya daha fazla özdeş pods dağıtmak için bir *DaemonSet* kullanılır, ancak DaemonSet denetleyicisi, belirtilen her düğümün Pod 'ın bir örneğini çalıştırmasını sağlar.

DaemonSet denetleyicisi, varsayılan Kubernetes Scheduler başlatılmadan önce küme önyükleme işlemindeki düğümlerde düğüm zamanlayabilir. Bu özellik, bir dağıtımdaki geleneksel köler veya StatefulSet zamanlanmadan önce bir DaemonSet içindeki yığınların başlatılmasını sağlar.

StatefulSets gibi, bir DaemonSet, `kind: DaemonSet`kullanılarak YAML tanımının bir parçası olarak tanımlanır.

Daha fazla bilgi için bkz. [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> [Sanal düğümler eklentisi](virtual-nodes-cli.md#enable-virtual-nodes-addon)kullanılıyorsa, DaemonSets sanal düğümde Pod oluşturmaz.

## <a name="namespaces"></a>Ad Alanları

Pod ve dağıtımlar gibi Kubernetes kaynakları, mantıksal olarak bir *ad alanı*halinde gruplandırılır. Bu gruplandırmalar, bir AKS kümesini mantıksal olarak bölmek ve kaynakları oluşturmak, görüntülemek veya yönetmek için erişimi kısıtlamak için bir yol sağlar. Örneğin, iş gruplarını ayırmak için ad alanları oluşturabilirsiniz. Kullanıcılar yalnızca atanan ad alanları içindeki kaynaklarla etkileşime girebilirler.

![Kaynak ve uygulamaları mantıksal olarak bölmek için Kubernetes ad alanları](media/concepts-clusters-workloads/namespaces.png)

Bir AKS kümesi oluşturduğunuzda, aşağıdaki ad alanları kullanılabilir:

- *varsayılan* -bu ad alanı, hiçbir değer sağlanmadıysa, varsayılan olarak Pod ve dağıtımların oluşturulduğu yerdir. Daha küçük ortamlarda, ek mantıksal ayırmalar oluşturmadan uygulamaları doğrudan varsayılan ad alanına dağıtabilirsiniz. `kubectl get pods`gibi Kubernetes API 'siyle etkileşim kurarken, hiçbiri belirtilmediğinde varsayılan ad alanı kullanılır.
- *kuas-System* -bu ad alanı, DNS ve proxy gibi ağ özellikleri veya Kubernetes panosu gibi çekirdek kaynakların bulunduğu yerdir. Genellikle kendi uygulamalarınızı bu ad alanına dağıtmazsınız.
- *Kuto-public* -bu ad alanı genellikle kullanılmaz, ancak kaynakların tüm küme genelinde görünür olması için kullanılabilir ve herhangi bir kullanıcı tarafından görüntülenebilir.

Daha fazla bilgi için bkz. [Kubernetes ad alanları][kubernetes-namespaces].

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bazı çekirdek Kubernetes bileşenleri ve AKS kümelerine nasıl uygulandığı ele alınmaktadır. Temel Kubernetes ve AKS kavramları hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [Kubernetes/AKS erişimi ve kimliği][aks-concepts-identity]
- [Kubernetes/AKS güvenliği][aks-concepts-security]
- [Kubernetes/AKS sanal ağları][aks-concepts-network]
- [Kubernetes/AKS depolaması][aks-concepts-storage]
- [Kubernetes/AKS ölçeği][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md