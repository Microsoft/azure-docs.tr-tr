---
title: Depolama yapılandırması
description: Azure Arc etkin veri Hizmetleri depolama yapılandırma seçeneklerini açıklar
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 7b683029b7fd05078755d4e8cd027f55c805f991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97107269"
---
# <a name="storage-configuration"></a>Depolama Yapılandırması

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Kubernetes depolama kavramları

Kubernetes, temel alınan sanallaştırma teknoloji yığını (isteğe bağlı) ve donanım üzerinde bir altyapı soyutlama katmanı sağlar. Kubernetes 'in, depolama alanı **[sınıfları](https://kubernetes.io/docs/concepts/storage/storage-classes/)** üzerinden soyutlanma şekli. Pod sağlama sırasında, her birim için kullanılmak üzere bir depolama sınıfı belirlenebilir. Pod 'ın sağlandığı zamanda depolama sınıfı **[hazırlayıcı](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** , depolamayı sağlamak için çağrılır ve ardından bu sağlanan depolamada **[kalıcı bir birim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** oluşturulur ve sonra, kalıcı birim **[talebi](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)** tarafından kalıcı birime bağlanır.

Kubernetes, depolama altyapısı sağlayıcılarının Kubernetes 'i genişleten sürücüleri ("Eklentiler" olarak da bilinir) takmaları için bir yol sağlar. Depolama eklentileri, **[kapsayıcı depolama arabirimi standardına](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)** uygun olmalıdır. Bu kesin olmayan bu **[CSI sürücüleri listesinde](https://kubernetes-csi.github.io/docs/drivers.html)** bulunabilir. Kullandığınız CSı sürücüsü, bulutta barındırılan, yönetilen bir Kubernetes hizmetinde ya da donanımınız için kullandığınız OEM sağlayıcısından çalıştırdığınız gibi etkenlere bağlı olacaktır.

Şu komutu çalıştırarak Kubernetes kümenizde hangi depolama sınıflarının yapılandırıldığını görüntüleyebilirsiniz:

```console
kubectl get storageclass
```

Azure Kubernetes Service (AKS) kümesinden alınan örnek çıktı:

```console
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

Şu komutu çalıştırarak bir depolama sınıfı hakkında ayrıntılı bilgi edinebilirsiniz:

```console
kubectl describe storageclass/<storage class name>
```

Örnek:

```console
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

Aşağıdaki komutları çalıştırarak, şu anda sağlanan kalıcı birimleri ve kalıcı birim taleplerini görebilirsiniz:

```console
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

Kalıcı birimleri gösterme örneği:

```console

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

Kalıcı birim taleplerini gösterme örneği:

```console

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>Depolama yapılandırmanızı seçerken göz önünde bulundurmanız gereken etmenler

Doğru depolama sınıfının seçilmesi veri dayanıklılığı ve performansı açısından önemlidir. Yanlış depolama sınıfının seçilmesi, bir donanım arızası durumunda verilerinizi toplam veri kaybı riskiyle elde edebilir veya en iyi performansa neden olabilir.

Genellikle iki tür depolama vardır:

- **Yerel depolama** -belirli bir düğümdeki yerel sabit sürücülerde sağlanan depolama alanı. Bu tür bir depolama, performans açısından ideal olabilir, ancak verileri birden çok düğüm arasında çoğaltarak veri yedekliği için özellikle tasarlanmasını gerektirir.
- **Uzak, paylaşılan depolama** (örneğin, bir uzak depolama cihazında sağlanan depolama alanı; Örneğin, EBS veya Azure dosyaları gıbı bir San, NAS veya bulut depolama hizmeti). Bu tür bir depolama genellikle otomatik olarak veri artıklığı sağlar, ancak yerel depolama alanı olabilir.

> [!NOTE]
> Şimdilik, NFS kullanıyorsanız, Azure Arc veri denetleyicisi 'ni dağıtılmadan önce, dağıtım profili dosyanızda allowRunAsRoot değerini true olarak ayarlamanız gerekir.

### <a name="data-controller-storage-configuration"></a>Veri denetleyicisi depolama yapılandırması

Veri Hizmetleri için Azure Arc 'daki bazı hizmetler, hizmetlerin verileri çoğaltabilme yeteneği olmadığından, uzak, paylaşılan depolama alanı kullanacak şekilde yapılandırılmaya bağımlıdır. Bu hizmetler, veri denetleyicisi dizin koleksiyonunda bulunur:

|**Hizmet**|**Kalıcı birim talepleri**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**Denetleyici SQL örneği**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**Denetleyici API hizmeti**|`<namespace>/data-controller`|

Veri denetleyicisinin sağlandığı zamanda, bu kalıcı birimlerin her biri için kullanılacak depolama sınıfı--Storage-Class ' ı geçirerek belirtilir | -SC parametresi, `azdata arc dc create` ve kullanılan dağıtım şablonu dosyasında control.jsdepolama sınıflarını ayarlayarak.

Kutudan çıkan dağıtım şablonlarının, hedef ortam için uygun bir varsayılan depolama sınıfı belirtilmiş, ancak dağıtım sırasında geçersiz kılınabilir. Dağıtım zamanında veri denetleyicisi yığınlarının depolama sınıfı yapılandırmasını değiştirmek için [dağıtım profilini](create-data-controller.md) değiştirme hakkında ayrıntılı adımlara bakın.

Depolama sınıfını--Storage-Class ile ayarlarsanız | -SC parametresi depolama sınıfı hem günlük hem de veri depolama sınıfları için kullanılacaktır. Dağıtım şablonu dosyasında depolama sınıflarını ayarlarsanız, Günlükler ve veriler için farklı depolama sınıfları belirtebilirsiniz.

Veri denetleyicisi için bir depolama sınıfı seçerken göz önünde bulundurmanız gereken önemli etmenler:

- Veri dayanıklılığı sağlamak için uzak, paylaşılan bir depolama sınıfı kullanmanız **gerekir** ve bu sayede Pod bir pod başlatıldığında bir pod veya düğüm kalıcı birime yeniden bağlanabilir.
- Denetleyici SQL örneğine, ölçüm VERITABANıNA ve günlük VERITABANıNA yazılan veriler genellikle oldukça düşüktür ve gecikme süresine duyarlı değildir, böylece ultra hızlı performans depolaması kritik değildir. Sık sık Grafana ve kibana arabirimlerini kullanan kullanıcılarınız varsa ve çok sayıda veritabanı örneği varsa, kullanıcılarınız daha hızlı depolama alanı üzerinde avantaj sağlayabilir.
- Her bir veritabanı örneği için Günlükler ve ölçümler toplandığından, gereken depolama kapasitesi, dağıttığınız veritabanı örneklerinin sayısıyla değişkendir. Veriler, temizlenmeden önce iki (2) hafta boyunca günlüklerde ve ölçüm VERITABANıNDA tutulur. 
- Depolama sınıfı dağıtımının değiştirilmesi zordur, belgelenmemiştir ve desteklenmez. Dağıtım zamanında depolama sınıfını doğru seçtiğinizden emin olun.

> [!NOTE]
> Hiçbir depolama sınıfı belirtilmemişse, varsayılan depolama sınıfı kullanılacaktır. Her bir Kubernetes kümesi için yalnızca bir varsayılan depolama sınıfı olabilir. [Varsayılan depolama sınıfını değiştirebilirsiniz](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

### <a name="database-instance-storage-configuration"></a>Veritabanı örneği depolama yapılandırması

Her veritabanı örneğinin veri, günlük ve yedek kalıcı birimleri vardır. Bu kalıcı birimler için depolama sınıfları dağıtım zamanında belirlenebilir. Hiçbir depolama sınıfı belirtilmemişse, varsayılan depolama sınıfı kullanılacaktır.

Ya da kullanarak bir örnek oluştururken `azdata arc sql mi create` `azdata arc postgres server create` , depolama sınıflarını ayarlamak için kullanılabilecek iki parametre vardır:

> [!NOTE]
> Bu parametrelerden bazıları geliştirme aşamasındadır ve `azdata arc sql mi create` yaklaşan sürümlerde ve üzerinde kullanılabilir hale gelir `azdata arc postgres server create` .

|Parametre adı, kısa ad|Kullanıldığı yerler|
|---|---|
|`--storage-class-data`, `-scd`|İşlem günlüğü dosyaları dahil tüm veri dosyaları için depolama sınıfını belirtmek için kullanılır|
|`--storage-class-logs`, `-scl`|Tüm günlük dosyaları için depolama sınıfını belirtmek için kullanılır|
|`--storage-class-data-logs`, `-scdl`|Veritabanı işlem günlüğü dosyaları için depolama sınıfını belirtmek için kullanılır. **Not: henüz kullanılamıyor.**|
|`--storage-class-backups`, `-scb`|Tüm yedekleme dosyaları için depolama sınıfını belirtmek için kullanılır. **Not: henüz kullanılamıyor.**|

Aşağıdaki tabloda, veri ve Günlükler için kalıcı birimle eşlenen Azure SQL yönetilen örnek kapsayıcısının içindeki yollar listelenmiştir:

|Parametre adı, kısa ad|MSSQL-mıaa kapsayıcısının içindeki yol|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|MSSQL yüklemesi ve diğer sistem işlemlerine yönelik dizinleri içerir. MSSQL dizini, varsayılan verileri (işlem günlükleri dahil), hata günlüğü & yedekleme dizinlerini içerir|
|`--storage-class-logs`, `-scl`|/var/log|Konsol çıkışını (stderr, STDOUT) ve kapsayıcı içindeki işlemlerin diğer günlük bilgilerini depolayan dizinleri içerir|

Aşağıdaki tabloda, veri ve Günlükler için kalıcı birimle eşlenen PostgreSQL örnek kapsayıcısının içindeki yollar listelenmiştir:

|Parametre adı, kısa ad|Postgres kapsayıcısının içindeki yol|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/seçenek/PostgreSQL|Postgres yüklemesi için veri ve günlük dizinleri içerir|
|`--storage-class-logs`, `-scl`|/var/log|Konsol çıkışını (stderr, STDOUT) ve kapsayıcı içindeki işlemlerin diğer günlük bilgilerini depolayan dizinleri içerir|

Her veritabanı örneğinin veri dosyaları, Günlükler ve yedeklemeler için ayrı bir kalıcı birimi olacak. Yani bu dosya türlerinin her biri için g/ç 'nin, toplu hazırlayıcı tarafından depolama sağlama şekli ile ilgili olarak ayrılması anlamına gelir. Her veritabanı örneğinin kendi kalıcı birim talepleri ve kalıcı birimleri vardır.

Belirli bir veritabanı örneğinde birden çok veritabanı varsa, tüm veritabanları aynı kalıcı birim talebi, kalıcı birim ve depolama sınıfını kullanır. Tüm yedeklemeler-hem fark günlüğü yedeklemeleri hem de tam yedeklemeler aynı kalıcı birim talebini ve kalıcı birimi kullanacaktır. Veritabanı örneği için kalıcı birim talepleri aşağıda gösterilmiştir:

|**Örnek**|**Kalıcı birim talepleri**|
|---|---|
|**Azure SQL Yönetilen Örnek**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**PostgreSQL için Azure veritabanı örneği**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure PostgreSQL hiper ölçek**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(Sıra sayısı 0 ile w arasında değişir; burada w, çalışan sayısıdır)*|

Veritabanı örneği için bir depolama sınıfı seçerken göz önünde bulundurmanız gereken önemli etmenler:

- Veritabanı örnekleri, tek bir pod düzeninde veya birden çok Pod düzeniyle dağıtılabilir. Tek Pod düzenine örnek olarak, Azure SQL yönetilen örneği veya genel amaçlı fiyatlandırma katmanı Azure SQL yönetilen örneği bir geliştirici örneğidir. Çoklu Pod düzenine bir örnek, yüksek oranda kullanılabilir bir iş açısından kritik fiyatlandırma katmanı Azure SQL yönetilen örneğidir. (Not: fiyatlandırma katmanları geliştirme aşamasındadır ve henüz müşteriler tarafından kullanılamaz.)  Tek Pod düzeniyle dağıtılan veritabanı örnekleri, veri dayanıklılığı sağlamak için uzak, paylaşılan bir depolama **sınıfı kullanmalıdır ve** bu sayede Pod, Pod tarafından yedeklenirken bir pod veya düğüm kalıcı birime yeniden bağlanabilir. Buna karşılık, yüksek oranda kullanılabilir bir Azure SQL yönetilen örneği, verileri zaman uyumlu veya zaman uyumsuz olarak bir örnekten diğerine çoğaltmak için Always on kullanılabilirlik grupları kullanır. Özellikle, verilerin zaman uyumlu olarak çoğaltılacağı durumlarda, verilerin her zaman birden çok kopyası vardır-genellikle üç (3) kopya. Bu nedenle, veri ve günlük dosyaları için yerel depolama veya uzak, paylaşılan depolama sınıflarını kullanmak mümkündür. Yerel depolama alanı kullanılıyorsa, veriler hatalı bir pod, düğüm veya depolama donanımı durumunda bile hala korunur. Bu esneklik verildiğinde, daha iyi performans için yerel depolamayı kullanmayı tercih edebilirsiniz.
- Veritabanı performansı büyük ölçüde belirli bir depolama cihazının g/ç verimlilik işlevidir. Veritabanınız ağır okumalar veya ağır yazmasa, bu iş yükü türü için tasarlanan donanımla bir depolama sınıfı seçmeniz gerekir. Örneğin, veritabanınız çoğunlukla yazma işlemlerinde kullanılıyorsa, RAID 0 ile yerel depolama seçebilirsiniz. Veritabanınız çoğunlukla az miktarda "etkin veri" okuma için kullanılırsa, ancak çok büyük bir toplam depolama hacmi olan çok sayıda soğuk veri depolama alanı varsa katmanlı depolama özellikli bir SAN cihazı seçebilirsiniz. Doğru depolama sınıfının seçilmesi, herhangi bir veritabanı için kullanacağınız depolama türünü seçmekten çok farklı değildir.
- Yerel bir depolama birimi hazırlayıcısı kullanıyorsanız, disk g/ç üzerinde çekişmeyi önlemek için veriler, Günlükler ve yedeklemeler için sağlanan Yerel birimlerin her bir temel depolama cihazında yer aldığından emin olun. İşletim sistemi ayrıca ayrı bir diske bağlı bir birimde olmalıdır. Bu, aslında fiziksel donanımda bir veritabanı örneği için de aynı kılavuzdan aynıdır.
- Belirli bir örnekteki tüm veritabanları kalıcı bir birim talebi ve kalıcı birimi paylaştığından, meşgul veritabanı örneklerini aynı veritabanı örneği üzerinde bulundurmayın emin olun. Mümkünse, g/ç çekişmesini önlemek için, mümkün olan yoğun veritabanlarını kendi veritabanı örneklerine ayırın. Ayrıca, genel g/ç trafiğini birden çok düğüme dağıtmak için veritabanı örneklerini ayrı düğümlere yönlendirerek düğüm etiketi hedefleme kullanın. Sanallaştırma kullanıyorsanız, belirli bir fiziksel konaktaki tüm düğüm VM 'lerinde g/ç trafiğini yalnızca düğüm düzeyinde değil, aynı zamanda Birleşik g/ç etkinliğini de göz önünde bulundurduğunuzdan emin olun.

## <a name="estimating-storage-requirements"></a>Depolama gereksinimlerini tahmin etme
Durum bilgisi olan veriler içeren her Pod, bu yayında iki kalıcı birim kullanır. veriler için bir kalıcı birim ve Günlükler için başka bir kalıcı birim. Aşağıdaki tabloda, tek bir veri denetleyicisi, Azure SQL yönetilen örneği, PostgreSQL için Azure veritabanı örneği ve Azure PostgreSQL hiper ölçek örneği için gereken kalıcı birim sayısı listelenmiştir:

|Kaynak Türü|Durum bilgisi olan Pod sayısı|Gereken kalıcı birim sayısı|
|---|---|---|
|Veri Denetleyicisi|4 ( `control` , `controldb` , `logsdb` , `metricsdb` )|4 * 2 = 8|
|Azure SQL Yönetilen Örnek|1|2|
|PostgreSQL için Azure veritabanı örneği|1| 2|
|Azure PostgreSQL hiper ölçek|1 + w (W = çalışan sayısı)|2 * (1 + W)|

Aşağıdaki tabloda, örnek dağıtım için gereken toplam kalıcı birim sayısı gösterilmektedir:

|Kaynak Türü|Örnek sayısı|Gereken kalıcı birim sayısı|
|---|---|---|
|Veri Denetleyicisi|1|4 * 2 = 8|
|Azure SQL Yönetilen Örnek|5|5 * 2 = 10|
|PostgreSQL için Azure veritabanı örneği|5| 5 * 2 = 10|
|Azure PostgreSQL hiper ölçek|2 (çalışan sayısı = örnek başına 4)|2 * 2 * (1 + 4) = 20|
|***Toplam kalıcı birim sayısı***||8 + 10 + 10 + 20 = 48|

Bu hesaplama, depolama hazırlayıcısı veya ortamına bağlı olarak Kubernetes kümeniz için depolamayı planlamak üzere kullanılabilir. Örneğin, beş (5) düğüm içeren bir Kubernetes kümesi için yerel depolama hazırlayıcısı kullanılıyorsa her düğümün üzerindeki örnek dağıtım için en az 10 kalıcı birim gerekir. Benzer şekilde, beş (5) düğüm içeren bir Azure Kubernetes hizmeti (AKS) kümesi sağlanırken, düğüm havuzu için 10 veri diski eklenebilecek uygun bir VM boyutu kullanıma alınırken önemlidir. AKS düğümlerine yönelik depolama ihtiyacı için düğümlerin nasıl boyutlandıralınacağını öğrenmek hakkında daha fazla ayrıntı [burada](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs)bulabilirsiniz.

## <a name="choosing-the-right-storage-class"></a>Doğru depolama sınıfını seçme

### <a name="on-premises-and-edge-sites"></a>Şirket içi ve uç siteler

Microsoft ve OEM, işletim sistemi ve Kubernetes iş ortakları, Azure Arc veri Hizmetleri için bir sertifika programı üzerinde çalışmaktadır. Bu program, müşterilere bir sertifika test araç setinde benzer test sonuçları sağlar. Testler Özellik uyumluluğunu, stres testi sonuçlarını ve performans ve ölçeklenebilirliği değerlendirir. Bu test sonuçlarının her biri, kullanılan işletim sistemi, Kubernetes dağıtımı, HW, kullanılan CSı eklentisi ve kullanılan depolama sınıflarını gösterir. Bu, müşterilerin gereksinimleri için en iyi depolama sınıfı, işletim sistemi, Kubernetes dağıtımı ve HW değerlerini seçmesini sağlar. Bu program ve ilk test sonuçları hakkında daha fazla bilgi, Azure Arc veri hizmetlerinin genel kullanılabilirliğine daha yakın bir şekilde sunulacaktır.

#### <a name="public-cloud-managed-kubernetes-services"></a>Genel bulut, yönetilen Kubernetes Hizmetleri

Genel bulut tabanlı, yönetilen Kubernetes Hizmetleri için aşağıdaki önerileri yapabiliriz:

|Genel bulut hizmeti|Öneri|
|---|---|
|**Azure Kubernetes Service (AKS)**|Azure Kubernetes hizmeti (AKS), iki tür depolama-Azure dosyası ve Azure yönetilen diski vardır. Her depolama türünde iki fiyatlandırma/performans katmanı (HDD) ve Premium (SSD) vardır. Bu nedenle, AKS içinde sunulan dört depolama sınıfı `azurefile` (Azure dosyaları standart katmanı), (Azure `azurefile-premium` dosyaları Premium katmanı), `default` (Azure diskleri standart katmanı) ve `managed-premium` (Azure diskleri Premium katmanı). Varsayılan depolama sınıfı `default` (Azure diskleri standart katmanı). Kararlarınız için bir araya getirilmeli türler ve katmanlar arasında önemli miktarda **[fiyatlandırma farkı](https://azure.microsoft.com/en-us/pricing/details/storage/)** vardır. Yüksek performanslı gereksinimlere sahip üretim iş yükleri için, `managed-premium` tüm depolama sınıfları için kullanmanızı öneririz. Geliştirme ve test iş yükleri, kavram provaları vb. bir değerlendirme için `azurefile` en az maliyetli bir seçenektir. Tüm dört seçenek, Azure 'da ağa bağlı tüm depolama cihazlarıyla uzak, paylaşılan depolama gerektiren durumlar için kullanılabilir. [Aks depolaması](../../aks/concepts-storage.md)hakkında daha fazla bilgi edinin.|
|**AWS Elastic Kubernetes Service (EKS)**| Amazon 'ın elastik Kubernetes hizmetinde, [EBS CSI depolama sürücüsüne](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html)göre bir birincil depolama sınıfı vardır. Bu, üretim iş yükleri için önerilir. Yeni bir depolama sürücüsü- [EFS CSI depolama sürücüsü](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) vardır. Bu, BIR EKS kümesine eklenebilir, ancak şu anda bir beta aşamasıdır ve değiştirilebilir. AWS, bu depolama sürücüsünün üretim için desteklendiğini söyseler de, hala beta sürümünde ve değişikliğe tabi olduğu için kullanılması önerilmez. EBS depolama sınıfı varsayılandır ve çağırılır `gp2` . [EKS depolama](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html)hakkında daha fazla bilgi edinin.|
|**Google Kubernetes Altyapısı (GKE)**|Google Kubernetes altyapısının (GKE) `standard` , [GCE kalıcı diskler](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk)için kullanılan yalnızca bir depolama sınıfına sahiptir. Tek bir tane olmak üzere varsayılan değer de vardır. GKE için doğrudan bağlı SSD 'Ler ile kullanabileceğiniz [yerel ve statik bir birim hazırlayıcı](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) olsa da, bunun beklendiği veya Google tarafından desteklenmediği için kullanılması önerilmez. [GKE depolama](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes)hakkında daha fazla bilgi edinin.
