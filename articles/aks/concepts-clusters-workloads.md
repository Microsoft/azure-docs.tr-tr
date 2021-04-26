---
title: Kavramlar-Azure Kubernetes Hizmetleri (AKS) için Kubernetes temelleri
description: Kubernetes 'in temel kümesini ve iş yükü bileşenlerini ve bunların Azure Kubernetes Service (AKS) özellikleriyle ilişkisini öğrenin
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 5e505ed44d221b20178ea5ffb1d9125fb2bddd4c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105944"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmeti (AKS) için Kubernetes temel kavramları

Uygulama geliştirme, kapsayıcı tabanlı bir yaklaşıma geçmeye devam eder ve kaynakları düzenleme ve yönetme gereksinimimizi artırır. Önde gelen platform olarak Kubernetes, hataya dayanıklı uygulama iş yüklerinin güvenilir bir şekilde planlanmasını sağlar. Yönetilen bir Kubernetes teklifi olan Azure Kubernetes hizmeti (AKS), kapsayıcı tabanlı uygulama dağıtımını ve yönetimini de basitleştirir.

Bu makale şunları tanıtır:
* Temel Kubernetes altyapısı bileşenleri:
    * *Denetim düzlemi*
    * *düğümlerini*
    * *düğüm havuzları*
* İş yükü kaynakları: 
    * *Pod*
    * *dağıtımlar*
    * *yapar* 
* Kaynakları *ad alanlarına* gruplama.

## <a name="what-is-kubernetes"></a>Kubernetes nedir?

Kubernetes kapsayıcı tabanlı uygulamaları ve bunların ilişkili ağ ve depolama bileşenlerini yöneten hızlı bir şekilde gelişen platformudur. Kubernetes, temel altyapı bileşenlerine değil uygulama iş yüklerine odaklanır. Kubernetes, yönetim işlemlerine yönelik güçlü bir API kümesiyle desteklenen dağıtımlar için bildirime dayalı bir yaklaşım sağlar.

Uygulama bileşenlerinin kullanılabilirliğini düzenlemek ve yönetmek için Kubernetes kullanarak modern, taşınabilir ve mikro hizmet tabanlı uygulamalar oluşturabilir ve çalıştırabilirsiniz. Kubernetes hem durum bilgisi olmayan hem de durum bilgisi olan uygulamaları destekleyerek, bu, mikro hizmet tabanlı uygulamaların benimsenmesiyle devam etmektedir.

Açık bir platform olarak Kubernetes, uygulamalarınızı tercih ettiğiniz programlama dili, işletim sistemi, kitaplıklar veya mesajlaşma veri yolu ile oluşturmanıza olanak sağlar. Mevcut sürekli tümleştirme ve sürekli teslim (CI/CD) araçları, sürümleri planlamak ve dağıtmak için Kubernetes ile tümleştirilebilir.

AKS, yükseltme koordinasyonu gibi dağıtım ve temel yönetim görevlerinin karmaşıklığını azaltan yönetilen bir Kubernetes hizmeti sağlar. Azure platformu AKS denetim düzlemi 'ni yönetir ve yalnızca uygulamalarınızı çalıştıran AKS düğümleri için ödeme yaparsınız. AKS, açık kaynaklı Azure Kubernetes hizmet altyapısının üzerine kurulmuştur: [aks-Engine][aks-engine].

## <a name="kubernetes-cluster-architecture"></a>Kubernetes kümesi mimarisi

Bir Kubernetes kümesi iki bileşene ayrılmıştır:

- *Denetim düzlemi*: uygulama iş yüklerinin temel Kubernetes hizmetlerini ve düzenlemesini sağlar.
- *Düğümler*: uygulama iş yüklerinizi çalıştırın.

![Kubernetes denetim düzlemi ve düğüm bileşenleri](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Kontrol düzlemi

Bir AKS kümesi oluşturduğunuzda, otomatik olarak bir denetim düzlemi oluşturulup yapılandırılır. Bu denetim düzlemi, Kullanıcı tarafından soyutlandığından yönetilen bir Azure kaynağı olarak ücretsiz olarak sunulmaktadır. Yalnızca AKS kümesine eklenen düğümler için ödeme yaparsınız. Denetim düzlemi ve kaynakları yalnızca kümeyi oluşturduğunuz bölgede bulunur.

Denetim düzlemi aşağıdaki temel Kubernetes bileşenlerini içerir:

| Bileşen | Açıklama |  
| ----------------- | ------------- |  
| *kuin-apiserver*                                                                                 | API sunucusu, temeldeki Kubernetes API 'Lerinin sunulma şekli olur. Bu bileşen, `kubectl` veya Kubernetes panosu gibi yönetim araçları için etkileşim sağlar.                                                        |  
| *etcd* | Kubernetes kümenizin ve yapılandırmanızın durumunu korumak için, yüksek oranda kullanılabilir *etcd* , Kubernetes içindeki bir anahtar değer deposudur.                                      |  
| *kuin-Scheduler*                                                                            | Uygulamaları oluşturduğunuzda veya ölçeklendirirseniz, Zamanlayıcı hangi düğümlerin iş yükünü çalıştırabileceğini belirler ve bunları başlatır.                                                                                    |  
| *kuin-Controller-Manager*                                                                            | Denetleyici Yöneticisi, Pod 'yi çoğaltma ve düğüm işlemlerini işleme gibi eylemleri gerçekleştiren bir dizi küçük denetleyiciyi fazla görür.                                                                  |  

AKS, adanmış bir API sunucusu, Zamanlayıcı vb. ile tek kiracılı bir denetim düzlemi sağlar. Düğümlerin sayısını ve boyutunu tanımlarsınız ve Azure platformu denetim düzlemi ve düğümleri arasındaki güvenli iletişimi yapılandırır. Denetim düzlemi ile etkileşim, ya da Kubernetes panosu gibi Kubernetes API 'Leri aracılığıyla oluşur `kubectl` .

Bu yönetilen denetim düzlemi ile bileşenleri (yüksek oranda kullanılabilir bir *etcd* deposu gibi) yapılandırmanız gerekmiyorsa, denetim düzlemini doğrudan erişemezsiniz. Kubernetes denetim düzlemi ve düğüm yükseltmeleri, Azure CLı veya Azure portal aracılığıyla düzenlenir. Olası sorunları gidermek için, Azure Izleyici günlükleri aracılığıyla denetim düzlemi günlüklerini gözden geçirebilirsiniz.

Bir denetim düzlemini yapılandırmak veya doğrudan erişmek için [aks-Engine][aks-engine]kullanarak kendi Kubernetes kümenizi dağıtın.

İlişkili en iyi uygulamalar için bkz. [AKS 'de küme güvenliği ve yükseltmeleri Için en iyi uygulamalar][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Düğümler ve düğüm havuzları

Uygulamalarınızı ve destekleyici hizmetleri çalıştırmak için bir Kubernetes *düğümüne* ihtiyacınız vardır. AKS kümesinde, Kubernetes düğüm bileşenlerini ve kapsayıcı çalışma zamanını çalıştıran bir Azure sanal makinesi (VM) en az bir düğüm bulunur.

| Bileşen | Açıklama |  
| ----------------- | ------------- |  
| `kubelet`                                                                                 | Denetim düzleminden düzenleme isteklerini işleyen ve istenen kapsayıcıları çalıştırmanın zamanlayan Kubernetes Aracısı.                                                        |  
| *kuin-proxy* | Her düğümdeki sanal ağı işler. Proxy, ağ trafiğini yönlendirir ve hizmetler ve pods için IP adresini yönetir.                                      |  
| *kapsayıcı çalışma zamanı*                                                                            | Kapsayıcılı uygulamaların sanal ağ ve depolama gibi ek kaynaklarla çalışmasına ve bunlarla etkileşime geçmesini sağlar. Kubernetes sürüm 1.19 + node havuzlarını kullanan AKS kümeleri `containerd` kapsayıcı çalışma zamanı olarak kullanır. Düğüm havuzları için düğüm havuzu sürüm 1,19 ' den önce Kubernetes kullanan AKS kümeleri, kapsayıcı çalışma zamanı olarak [Moby](https://mobyproject.org/) (yukarı akış Docker) kullanır.                                                                                    |  


![Bir Kubernetes düğümü için Azure sanal makinesi ve destekleyici kaynaklar](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Düğümleriniz için Azure VM boyutu depolama CPU 'Ları, bellek, boyut ve kullanılabilir (yüksek performanslı SSD veya normal HDD gibi) türünü tanımlar. Uygulamalarınızın büyük miktarda CPU ve bellek ya da yüksek performanslı depolama ihtiyacı olup olmayacağını çevreleyen düğüm boyutunu planlayın. Talepleri karşılamak için AKS kümenizdeki düğüm sayısını ölçeklendirin.

AKS 'de, kümenizin düğümlerine yönelik VM görüntüsü Ubuntu Linux veya Windows Server 2019 ' i temel alır. Bir AKS kümesi oluşturduğunuzda veya düğüm sayısını ölçeklendirirseniz, Azure platformu istenen sayıda VM 'yi otomatik olarak oluşturur ve yapılandırır. Aracı düğümleri standart VM 'Ler olarak faturalandırılır, bu nedenle tüm VM boyut iskontoları ( [Azure ayırmaları][reservation-discounts]dahil) otomatik olarak uygulanır.

Farklı bir konak işletim sistemi, kapsayıcı çalışma zamanı veya farklı özel paketler dahil olmak üzere [aks-Engine][aks-engine] Ile kendi Kubernetes kümenizi dağıtın. Yukarı akış `aks-engine` sürümleri, ve AKS kümelerinde desteğin önünde yapılandırma seçenekleri sunar. Bu nedenle, veya Moby dışında bir kapsayıcı çalışma zamanı kullanmak `containerd` isterseniz [](https://mobyproject.org/), `aks-engine` geçerli Ihtiyaçlarınızı karşılayan bir Kubernetes kümesini yapılandırmak ve dağıtmak için komutunu çalıştırabilirsiniz.

### <a name="resource-reservations"></a>Kaynak ayırmaları

AKS, düğümün kümenizin bir parçası olarak çalışmasını sağlamak için düğüm kaynakları kullanır. Bu kullanım, düğümünüz içindeki toplam kaynaklar ile AKS 'deki allocatable kaynakları arasında bir tutarsızlık oluşturabilir. Kullanıcı tarafından dağıtılan pods 'lerin isteklerini ve sınırlarını ayarlarken bu bilgileri unutmayın.

Bir düğümün allocatable kaynaklarını bulmak için şunu çalıştırın:
```kubectl
kubectl describe node [NODE_NAME]
```

Düğüm performansını ve işlevselliğini sürdürmek için, AKS her bir düğümdeki kaynakları ayırır. Düğüm, kaynaklarda daha büyük bir süre büyüdükçe, Kullanıcı tarafından dağıtılan yığınların yönetimine yönelik daha yüksek olması nedeniyle kaynak ayırma artar.

>[!NOTE]
> Container Insights (OMS) gibi AKS eklentilerinin kullanılması ek düğüm kaynakları kullanacaktır.

İki tür kaynak ayrılır:

- **CPU**  
    Ayrılmış CPU düğüm türüne ve küme yapılandırmasına bağımlıdır, bu da ek özellikleri çalıştırmak nedeniyle daha az ayrılamayabilir.

   | Konaktaki CPU çekirdekleri | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kule ayrılmış (miliçekirdekler)|60|100|140|180|260|420|740|

- **Bellek**  
    AKS tarafından kullanılan bellek, iki değerin toplamını içerir.

   1. **`kubelet` inin**   
       `kubelet`Arka plan programı, kapsayıcı oluşturma ve sonlandırmayı yönetmek için tüm Kubernetes aracı düğümlerine yüklenir. 
   
        AKS ' de varsayılan olarak, `kubelet` Daemon *belleği vardır. kullanılabilir<750Mı* çıkarma kuralı, bir düğümün her zaman en az 750 mi ayrılaşmasını sağlamak için gereklidir. Bir ana bilgisayar, kullanılabilir bellek eşiğinin altındaysa, çalışan bir `kubelet` yığından birini sonlandırmak ve ana makinede belleği boşaltmak için tetiklenecek.

   2. Kubelet arka plan programının düzgün çalışması için (*kuuya-ayrılmış*) **bellek ayırmalarının gerileme oranı** .
      - ilk 4 GB belleğin %25 ' i
      - sonraki 4 GB belleğin %20 ' si (8 GB 'a kadar)
      - Sonraki 8 GB belleğin %10 ' ından (16 GB 'a kadar)
      - sonraki 112 GB belleğin %6 ' ından (128 GB 'a kadar)
      - 128 GB üzerinde herhangi bir belleğin %2 ' i

Bellek ve CPU ayırma kuralları:
* Küme durumu için bazı barındırma sistemi yığınlarını dahil, aracı düğümlerini sağlıklı tutun. 
* Düğüm, Kubernetes kümesinin bir parçası olmasaydı daha az ayrılanmasa belleği ve CPU raporlamasına neden olur. 

Yukarıdaki kaynak ayırmaları değiştirilemez.

Örneğin, bir düğüm 7 GB sunuyorsa, bu, 750Mı sabit çıkarma eşiği dahil olmak üzere bellek ayırıcıdan oluşan %34 ' ı rapor eder.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Kubernetes 'in kendine yönelik ayırmaların yanı sıra, temeldeki düğüm IŞLETIM sistemi, işletim sistemi işlevlerini sürdürmek için bir miktar CPU ve bellek kaynağı da ayırır.

İlişkili en iyi uygulamalar için bkz. [AKS 'deki temel Zamanlayıcı özellikleri Için en iyi uygulamalar][operator-best-practices-scheduler].

### <a name="node-pools"></a>Düğüm havuzları

Aynı yapılandırmanın düğümleri, *düğüm havuzlarında* birlikte gruplandırılır. Bir Kubernetes kümesi en az bir düğüm havuzu içerir. Düğüm ve boyutun ilk sayısı, *varsayılan bir düğüm havuzu* oluşturan bir aks kümesi oluşturduğunuzda tanımlanır. AKS 'deki bu varsayılan düğüm havuzu, aracı düğümlerinizi çalıştıran temel VM 'Leri içerir.

> [!NOTE]
> Kümenizin güvenilir bir şekilde çalıştığından emin olmak için varsayılan düğüm havuzunda en az iki (2) düğüm çalıştırmalısınız.

Bir AKS kümesini varsayılan düğüm havuzunda ölçeklendirerek veya yükseltitirsiniz. Belirli bir düğüm havuzunu ölçeklendirmeye veya yükseltmeye seçim yapabilirsiniz. Yükseltme işlemleri için, tüm düğümler başarıyla yükseltilene kadar, çalışan kapsayıcılar düğüm havuzundaki diğer düğümlerde zamanlanır.

AKS 'de birden çok düğüm havuzu kullanma hakkında daha fazla bilgi için bkz. [AKS 'deki bir küme için birden çok düğüm havuzu oluşturma ve yönetme][use-multiple-node-pools].

### <a name="node-selectors"></a>Düğüm seçicileri

Birden çok düğüm havuzu içeren bir AKS kümesinde, belirli bir kaynak için hangi düğüm havuzunun kullanılacağını Kubernetes Scheduler 'a bildirmeniz gerekebilir. Örneğin, giriş denetleyicileri Windows Server düğümlerinde çalıştırılmamalıdır. 

Düğüm seçicileri, bir pod 'ın zamanlanması gereken yeri denetlemek için düğüm işletim sistemi gibi çeşitli parametreleri tanımlamanıza olanak sağlar.

Aşağıdaki temel örnek, *"Beta.Kubernetes.io/OS"* düğüm seçicisini kullanarak bir Linux düğümündeki NGINX örneğini zamanlar: Linux:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Yığınların nerede zamanlandığını denetleme hakkında daha fazla bilgi için bkz. [AKS 'de gelişmiş Zamanlayıcı özellikleri Için en iyi uygulamalar][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Pod

Kubernetes, uygulamanızın bir örneğini çalıştırmak için *Pod* 'yi kullanır. Pod, uygulamanızın tek bir örneğini temsil eder. 

Pods 'nin genellikle kapsayıcı ile 1:1 eşlemesi vardır. Gelişmiş senaryolarda, Pod birden çok kapsayıcı içerebilir. Birden çok Kapsayıcılı dizin aynı düğümde birlikte zamanlanır ve kapsayıcıların ilgili kaynakları paylaşmasına izin verir.

Pod oluşturduğunuzda, belirli miktarda CPU veya bellek kaynağı istemek için *kaynak istekleri* tanımlayabilirsiniz. Kubernetes Zamanlayıcı, kaynakları kullanılabilir kaynaklarla bir düğümde çalıştırmayı planlayarak isteği karşılamaları için dener. Ayrıca, bir pod 'ın temel alınan düğümden çok fazla bilgi işlem kaynağı kullanmasını engellemek için maksimum kaynak sınırları belirtebilirsiniz. Kubernetes Scheduler 'ın gerekli, izin verilen kaynakları belirlemesine yardımcı olmak için tüm yığınların kaynak sınırlarını dahil etmek en iyi uygulamadır.

Daha fazla bilgi için bkz. [Kubernetes Pod][kubernetes-pods] ve [Kubernetes Pod yaşam döngüsü][kubernetes-pod-lifecycle].

Pod bir mantıksal kaynaktır, ancak uygulama iş yükleri kapsayıcılar üzerinde çalışır. Pods genellikle kısa ömürlü, atılabilir kaynaklarıdır. Yüksek kullanılabilirlik ve artıklık Kubernetes özelliklerinin bazılarını ayrı ayrı zamanlanmış bir şekilde kaçırmıştır. Bunun yerine, Pod 'ler, dağıtım denetleyicisi gibi Kubernetes *denetleyicileri* tarafından dağıtılır ve yönetilir.

## <a name="deployments-and-yaml-manifests"></a>Dağıtımlar ve YAML bildirimleri

*Dağıtım* , Kubernetes dağıtım denetleyicisi tarafından yönetilen özdeş Pod 'yi temsil eder. Dağıtım, oluşturulacak Pod *çoğaltmaları* sayısını tanımlar. Kubernetes Zamanlayıcı, düğüm veya düğümlerde sorun yaşarsanız sağlıklı düğümlerde ek yığınların zamanlanmasını sağlar.

Pads yapılandırmalarını, kullanılan kapsayıcı görüntüsünü veya bağlı depolamayı değiştirmek için dağıtımları güncelleştirebilirsiniz. Dağıtım denetleyicisi:
* Belirli sayıda kopyayı boşaltır ve sonlandırır.
* Yeni dağıtım tanımından çoğaltmalar oluşturur.
* Dağıtımdaki tüm çoğaltmalar güncelleştirilene kadar işlemi devam ettirir.

AKS 'teki çoğu durum bilgisiz uygulamaların, tek tek DS zamanlaması yerine dağıtım modelini kullanması gerekir. Kubernetes, gerekli sayıda çoğaltmanın küme içinde çalıştığından emin olmak için dağıtım durumunu ve durumunu izleyebilir. Tek tek zamanlandıklarında, bir sorunla karşılaşmaları halinde Pod yeniden başlatılmaz ve geçerli düğümleri bir sorunla karşılaştığında sağlıklı düğümlerde yeniden planlanmaz.

Uygulamanız en az sayıda kullanılabilir örnek gerektiriyorsa, bir güncelleştirme işlemiyle yönetim kararlarını kesintiye uğratabilir. *Pod kesinti bütçeleri* , bir güncelleştirme veya düğüm yükseltmesi sırasında bir dağıtımda kaç çoğaltmanın alınacağını tanımlar. Örneğin, dağıtımınızda *beş (5)* çoğaltmalarınız varsa, tek seferde yalnızca bir çoğaltmanın silinmesine veya yeniden zamanlanmasını sağlamak için 4 ' ün *(dört)* Pod kesintisini tanımlayabilirsiniz. Pod kaynak limitleriyle birlikte en iyi yöntem, her zaman en az sayıda çoğaltma olmasını gerektiren uygulamalarda Pod kesinti bütçeleri tanımlamaktır.

Dağıtımlar genellikle veya ile oluşturulur ve yönetilir `kubectl create` `kubectl apply` . YAML biçiminde bir bildirim dosyası tanımlayarak bir dağıtım oluşturun. 

Aşağıdaki örnek NGıNX Web sunucusunun temel bir dağıtımını oluşturur. Dağıtım, oluşturulacak *üç (3)* yinelemeyi belirtir ve bağlantı noktası *80* ' nin kapsayıcıda açık olmasını gerektirir. Kaynak istekleri ve sınırları, CPU ve bellek için de tanımlanır.

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
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
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

YAML bildiriminde hizmetler (örneğin, yük dengeleyiciler) eklenerek daha karmaşık uygulamalar oluşturulabilir.

Daha fazla bilgi için bkz. [Kubernetes dağıtımları][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Held ile paket yönetimi

[Helk][helm] genellikle Kubernetes içindeki uygulamaları yönetmek için kullanılır. Uygulama kodu ve Kubernetes YAML bildirimlerinin paketlenmiş bir sürümünü içeren mevcut ortak Held *grafiklerini* oluşturup kullanarak kaynakları dağıtabilirsiniz. HELI grafiklerini yerel olarak veya [Azure Container Registry Held grafik][acr-helm]deposu gibi uzak bir depoda saklayabilirsiniz.

Held 'yi kullanmak için, hele istemcisini bilgisayarınıza yükler veya [Azure Cloud Shell][azure-cloud-shell]Held istemcisini kullanın. HELI grafikleri arayın veya oluşturun ve ardından onları Kubernetes kümenize yükler. Daha fazla bilgi için bkz. [AKS 'de Held ile var olan uygulamaları yüklemeyin][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets ve DaemonSets

Kubernetes Zamanlayıcı 'yı kullanarak, dağıtım denetleyicisi kullanılabilir kaynaklarla kullanılabilir olan herhangi bir düğümdeki çoğaltmaları çalıştırır. Bu yaklaşım durum bilgisiz uygulamalar için yeterli olabilir, ancak dağıtım denetleyicisi şunları gerektiren uygulamalar için ideal değildir:
* Kalıcı bir adlandırma kuralı veya depolama alanı. 
* Bir küme içindeki her bir SELECT düğümünde bulunan bir çoğaltma.

Ancak iki Kubernetes kaynağı, bu tür uygulamaları yönetmenizi sağlar:

- *Statefulsets* , depolama gibi tek bir pod yaşam döngüsünün ötesinde uygulamaların durumunu korur.
- *DaemonSets* , Kubernetes Bootstrap işleminin başlarında, her düğümde çalışan bir örnek olmasını sağlar.

### <a name="statefulsets"></a>StatefulSets

Modern uygulama geliştirme genellikle durum bilgisiz uygulamalar için amaçlar. Veritabanı bileşenleri dahil olanlar gibi durum bilgisi olan uygulamalarda *Statefulsets*' i kullanabilirsiniz. Dağıtımlar gibi, bir StatefulSet, en az bir özdeş Pod oluşturur ve yönetir. Bir StatefulSet içindeki çoğaltmalar düzgün bir şekilde, dağıtım, ölçeklendirme, yükseltme ve sonlandırma için ardışık bir yaklaşım izler. Çoğaltma olarak adlandırma kuralı, ağ adları ve depolama kalıcı olarak korunur, bir StatefulSet ile yeniden çizelgelenir.

Kullanarak uygulamayı YAML biçiminde tanımlayın `kind: StatefulSet` . Bundan sonra, StatefulSet denetleyicisi gerekli çoğaltmaların dağıtımını ve yönetimini işler. Veriler, Azure yönetilen diskler veya Azure dosyaları tarafından belirtilen kalıcı depolama alanına yazılır. StatefulSets ile, StatefulSet silindiğinde bile temeldeki kalıcı depolama alanı kalır.

Daha fazla bilgi için bkz. [Kubernetes StatefulSets][kubernetes-statefulsets].

Bir StatefulSet içindeki çoğaltmalar zamanlanır ve bir AKS kümesindeki tüm kullanılabilir düğümlerde çalıştırılır. Bir düğümde küme üzerinde en az bir pod 'ın çalıştığından emin olmak için, bunun yerine bir DaemonSet kullanırsınız.

### <a name="daemonsets"></a>DaemonSets

Belirli günlük koleksiyonu veya izleme için, tüm düğümlerde veya seçili düğümlerde bir pod çalıştırmanız gerekebilir. Bir veya daha fazla özdeş pods dağıtımı *DaemonSet* kullanabilirsiniz, ancak DaemonSet denetleyicisi, belirtilen her düğümün Pod 'ın bir örneğini çalıştırmasını sağlar.

DaemonSet denetleyicisi, varsayılan Kubernetes Scheduler başlatılmadan önce küme önyükleme işlemindeki düğümlerde düğüm zamanlayabilir. Bu özellik, bir dağıtımdaki geleneksel köler veya StatefulSet zamanlanmadan önce bir DaemonSet içindeki yığınların başlatılmasını sağlar.

StatefulSets gibi, bir DaemonSet, kullanılarak YAML tanımının bir parçası olarak tanımlanır `kind: DaemonSet` .

Daha fazla bilgi için bkz. [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> [Sanal düğümler eklentisi](virtual-nodes-cli.md#enable-virtual-nodes-addon)kullanılıyorsa, DaemonSets sanal düğümde Pod oluşturmaz.

## <a name="namespaces"></a>Ad alanları

Pod ve dağıtımlar gibi Kubernetes kaynakları, bir aks kümesini bölmek ve kaynaklara erişimi oluşturma, görüntüleme veya yönetme işlemlerini kısıtlamak için mantıksal olarak bir *ad alanı* halinde gruplandırılır. Örneğin, iş gruplarını ayırmak için ad alanları oluşturabilirsiniz. Kullanıcılar yalnızca atanan ad alanları içindeki kaynaklarla etkileşime girebilirler.

![Kaynak ve uygulamaları mantıksal olarak bölmek için Kubernetes ad alanları](media/concepts-clusters-workloads/namespaces.png)

Bir AKS kümesi oluşturduğunuzda, aşağıdaki ad alanları kullanılabilir:

| Ad Alanı | Description |  
| ----------------- | ------------- |  
| *default*                                                                                 | Hiçbiri sağlanmadıysa, varsayılan olarak Pod ve dağıtımlar oluşturulur. Daha küçük ortamlarda, ek mantıksal ayırmalar oluşturmadan uygulamaları doğrudan varsayılan ad alanına dağıtabilirsiniz. İle gibi Kubernetes API 'siyle etkileşim kurarken `kubectl get pods` , hiçbiri belirtilmediğinde varsayılan ad alanı kullanılır.                                                        |  
| *kube-system* | DNS ve proxy gibi ağ özellikleri ya da Kubernetes panosu gibi çekirdek kaynakları vardır. Genellikle kendi uygulamalarınızı bu ad alanına dağıtmazsınız.                                      |  
| *kube-public*                                                                            | Genellikle kullanılmaz, ancak kaynakların tüm küme genelinde görünebilmeleri için kullanılabilir ve herhangi bir kullanıcı tarafından görüntülenebilir.                                                                                    |  


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
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
