---
title: Kubernetes küme düğümlerinde bir PostgreSQL hiper ölçek sunucu grubunun yerleştirilmesi
description: PostgreSQL hiper ölçek sunucu grubu oluşturan PostgreSQL örneklerinin Kubernetes küme düğümlerine nasıl yerleştirileceğini açıklar
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b88b36ba8ec1d2d612adbbf19a6cf1e91fbb2cfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377763"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Azure Arc etkin PostgreSQL hiper ölçek sunucu grubu yerleşimi

Bu makalede, Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunun PostgreSQL örneklerinin, kendilerini barındıran Kubernetes kümesinin fiziksel düğümlerine nasıl yerleştirileceğini gösteren bir örnek sunuyoruz. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Yapılandırma

Bu örnekte, dört fiziksel düğüme sahip bir Azure Kubernetes hizmeti (AKS) kümesi kullanacağız. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="Azure portal 4 düğüm AKS kümesi":::

Kubernetes kümesinin fiziksel düğümlerini listeleyin. Şu komutu çalıştırın:

```console
kubectl get nodes
```

`kubectl` Kubernetes kümesi içinde dört fiziksel düğüm döndürür:

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

Mimari şöyle gösterilebilir:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="Bir Kubernetes kümesinde gruplanmış 4 düğümün mantıksal temsili":::

Kubernetes kümesi bir Azure Arc veri denetleyicisi ve bir Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunu barındırır. Bu sunucu grubu üç PostgreSQL örneklerinden oluşur: bir düzenleyici ve iki çalışan.

Şu komutla birlikte Pod 'leri listeleyin:

```console
kubectl get pods -n arc3
```
`kubectl` döndürdüğü

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          9h
postgres01w-0         3/3     Running   0          9h
postgres01w-1         3/3     Running   0          9h
```
Bu ayırımların her biri bir PostgreSQL örneği barındırır. Birlikte, Pod Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunu oluşturur:

```output
Pod name        Role in the server group
postgres01c-0 Coordinator
postgres01w-0   Worker
postgres01w-1   Worker
```

## <a name="placement"></a>Yerleştirme
Kubernetes 'in, sunucu grubunun sayısını nasıl yerleştirtiğine bakalım. Her bir pod 'ı tanıtın ve Kubernetes kümesinin hangi fiziksel düğümüne yerleştirileceğini belirler. Örneğin, düzenleyici için aşağıdaki komutu çalıştırın:

```console
kubectl describe pod postgres01c-0 -n arc3
```

`kubectl` döndürdüğü

```output
Name:         postgres01c-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

Bu komutu her bir pods için çalıştırdığımızda şu şekilde geçerli yerleşimi özetliyoruz:

| Sunucu grubu rolü|Sunucu grubu Pod|Pod 'u barındıran Kubernetes fiziksel düğümü |
|--|--|--|
| Indan|postgres01-1|aks-agentpool-42715708-vmss000002 |
| Indan|postgres01-2|aks-agentpool-42715708-vmss000003 |

Ayrıca, KADS 'nin açıklamasında her bir pod 'un barındırdığı kapsayıcıların adlarını da unutmayın. Örneğin, ikinci çalışan için aşağıdaki komutu çalıştırın:

```console
kubectl describe pod postgres01w-1 -n arc3
```

`kubectl` döndürdüğü

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunun parçası olan her Pod, aşağıdaki üç kapsayıcıyı barındırır:

|Kapsayıcılar|Description
|----|----|
|`Fluentbit` |Veri * günlük toplayıcısı: https://fluentbit.io/
|`Postgres`|Azure Arc etkin PosgreSQL hiper ölçek sunucu grubunun PostgreSQL örneği parçası
|`Telegraf` |Ölçüm toplayıcısı: https://www.influxdata.com/time-series-platform/telegraf/

Mimari şöyle görünür:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="her biri ayrı düğümlere yerleştirilmiş 3 Dizin":::

Bu noktada, Azure Arc etkin PostgreSQL hiper ölçek sunucu grubu 'nun her PostgreSQL örneği constituting, Kubernetes kapsayıcısının içindeki belirli fiziksel ana bilgisayarda barındırıldığı anlamına gelir. Bu yapılandırma, her bir rol (düzenleyici ve çalışanlar) her fiziksel düğümün kaynaklarını kullandığından, Azure Arc etkin PostgreSQL hiper ölçek sunucu grubundan en iyi performansı sağlar. Bu kaynaklar, birkaç PostgreSQL rolü arasında paylaşılmaz.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc etkin PostgreSQL hiper ölçeğini genişletme

Şimdi, sunucu grubuna üçüncü bir çalışan düğümü eklemek ve ne olduğunu gözlemlemek için ölçeği ölçeklendirelim. Dördüncü Pod 'da barındırılacak dördüncü bir PostgreSQL örneği oluşturur.
Ölçeği genişletmek için komutunu çalıştırın:

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

Şu çıktıyı üretir:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Azure Arc veri denetleyicisinde dağıtılan sunucu gruplarını listeleyin ve sunucu grubunun şimdi üç çalışan ile çalıştığını doğrulayın. Şu komutu çalıştırın:

```console
azdata arc postgres server list
```

Ve iki çalışandan üç çalışana kadar ölçeklendirdiğini gözlemleyin:

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Daha önce yaptığımız gibi, sunucu grubunun artık toplam dört adet pods kullandığını gözlemleyin:

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          11h
postgres01w-0         3/3     Running   0          11h
postgres01w-1         3/3     Running   0          11h
postgres01w-2         3/3     Running   0          5m2s
```

Ve Kubernetes kümesinin hangi fiziksel düğümlerinden barındırıldığını belirlemek için yeni Pod 'u açıkla.
Şu komutu çalıştırın:

```console
kubectl describe pod postgres01w-2 -n arc3
```

Barındırma düğümünün adını belirlemek için:

```output
Name:         postgres01w-2
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

Kümeden fiziksel düğümlere PostgreSQL örneklerinin yerleştirilmesi Şu anda:

|Sunucu grubu rolü|Sunucu grubu Pod|Pod 'u barındıran Kubernetes fiziksel düğümü
|-----|-----|-----
|Yöneticisidir|postgres01-0|aks-agentpool-42715708-vmss000000
|Indan|postgres01-1|aks-agentpool-42715708-vmss000002
|Indan|postgres01-2|aks-agentpool-42715708-vmss000003
|Indan|postgres01-3|aks-agentpool-42715708-vmss000000

Ve yeni çalışan Pod (postgres01w-2), düzenleyicile aynı düğüme yerleştirildiğine dikkat edin. 

Mimari şöyle görünür:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="Koordinatör ile aynı düğümdeki dördüncü Pod":::

Yeni çalışan/Pod neden, Kubernetes kümesinin aks-agentpool-42715708-vmss000003 kalan fiziksel düğümüne yerleştirilmiyor?

Bunun nedeni, Kubernetes kümesinin son fiziksel düğümünün aslında Azure Arc etkin veri hizmetlerini çalıştırmak için gereken ek bileşenleri barındıran birkaç düğüm barındırmakta olma nedenidir. Kubernetes en iyi aday olduğunu değerlendirdi; zamanlama sırasında, ek çalışanı barındırmak için aks-agentpool-42715708-vmss000000 fiziksel düğümüdür. 

Yukarıdaki komutlarla aynı komutları kullanma; hangi fiziksel düğümlerin barındırdığını görüyoruz:

|Diğer Pod adları\* |Kullanım|Kubernetes Pod 'leri barındıran fiziksel düğüm
|----|----|----
|önyükleyici-jh48b|SQL yönetilen örnekler, PostgreSQL hiper ölçek sunucu grupları ve veri denetleyicileri gibi özel kaynakları oluşturma, düzenleme ve silmeye yönelik gelen istekleri işleyen bir hizmet|aks-agentpool-42715708-vmss000003
|Denetim-gwmb||aks-agentpool-42715708-vmss000002
|controldb-0|Veri denetleyicisinin yapılandırmasını ve durumunu depolamak için kullanılan denetleyici veri deposu.|aks-agentpool-42715708-vmss000001
|controlwd-zzjp7|Veri denetleyicisinin kullanılabilirliğine göz önünde tutan denetleyici "Gözcü" hizmeti.|aks-agentpool-42715708-vmss000000
|logsdb-0|Tüm Arc veri Hizmetleri yığınlarını kapsayan tüm günlükleri depolamak için kullanılan elastik arama örneği. Elaun Search, `Fluentbit` her Pod 'un kapsayıcısından veri alır|aks-agentpool-42715708-vmss000003
|logsui-5fzv5|Bir Log Analytics GUI 'si sunmak için elastik arama veritabanının en üstünde yer alan bir kibana örneği.|aks-agentpool-42715708-vmss000003
|metricsdb-0|Tüm Arc veri Hizmetleri yığınlarını kapsayan tüm ölçümleri depolamak için kullanılan bir etkileyen bir örnek. Etkileyen `Telegraf` her Pod 'un kapsayıcısından verileri alır|aks-agentpool-42715708-vmss000000
|metricsdc-47d47|Düğümler hakkında düğüm düzeyi ölçümleri toplamak için kümedeki tüm Kubernetes düğümlerine dağıtılan bir Daemon kümesi.|aks-agentpool-42715708-vmss000002
|metricsdc-864kj|Düğümler hakkında düğüm düzeyi ölçümleri toplamak için kümedeki tüm Kubernetes düğümlerine dağıtılan bir Daemon kümesi.|aks-agentpool-42715708-vmss000001
|metricsdc-l8jkf|Düğümler hakkında düğüm düzeyi ölçümleri toplamak için kümedeki tüm Kubernetes düğümlerine dağıtılan bir Daemon kümesi.|aks-agentpool-42715708-vmss000003
|metricsdc-nxm4l|Düğümler hakkında düğüm düzeyi ölçümleri toplamak için kümedeki tüm Kubernetes düğümlerine dağıtılan bir Daemon kümesi.|aks-agentpool-42715708-vmss000000
|metricsui-4fb7l|Bir izleme panosu GUI 'si sunmak için etkileyen bir Grafana örneği.|aks-agentpool-42715708-vmss000003
|mgmtproxy-4qppp|Grafana ve kibana örneklerinin önünde yer alan bir Web uygulaması proxy katmanı.|aks-agentpool-42715708-vmss000002

> \* Pod adlarındaki sonek diğer dağıtımlarda farklılık gösterecektir. Ayrıca, burada yalnızca Azure Arc veri denetleyicisinin Kubernetes ad alanı içinde barındırılan Pod 'leri listeliyoruz.

Mimari şöyle görünür:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="Çeşitli düğümlerde ad alanındaki tüm düğüm 'ler":::

Yukarıda açıklandığı gibi, Azure Arc etkin Postgres Hyperscale sunucu grubu 'nun düzenleyici düğümleri (pod 1), sunucu grubunun üçüncü çalışan düğümü (pod 4) ile aynı fiziksel kaynakları paylaşır. Bu, düzenleyici düğümü genellikle bir çalışan düğümünün kullanılmasına kıyasla çok az kaynak kullandığından, bu kabul edilebilir. Bu nedenle dikkatle seçin:
- Kubernetes kümesinin boyutu ve fiziksel düğümlerinin her birinin özellikleri (bellek, sanal çekirdek)
- Kubernetes kümesi içindeki fiziksel düğümlerin sayısı
- Kubernetes kümesinde ana bilgisayar oluşturduğunuz uygulamalar veya iş yükleri.

Kubernetes kümesinde çok fazla iş yükünün barındırılması, Azure Arc etkin PostgreSQL hiper ölçek sunucu grubu için azaltma olabilir. Böyle bir durumla karşılaşırsanız, özelliği yatay ölçeklendirme özelliğinden daha fazla avantaj elde etmeyecektir. Sistemden aldığınız performans, yalnızca fiziksel düğümlerin veya depolama sisteminin yerleşimi veya fiziksel özellikleri hakkında değildir. Aldığınız performans Ayrıca, Kubernetes kümesinde çalışan her bir kaynağın (Azure Arc etkin PostgreSQL hiper ölçek dahil), bellek ve sanal çekirdek için ayarladığınız istek ve sınırlara yönelik olarak nasıl yapılandırılacağı hakkında da bulunur. Belirli bir Kubernetes kümesinde barındırabilecek iş yükü miktarı Kubernetes kümesinin özellikleriyle, iş yüklerinin doğası, Kullanıcı sayısı, Kubernetes kümesi işlemlerinin nasıl yapıldığı...

## <a name="scale-out-aks"></a>AKS ölçeğini genişletme

Yatay ölçeklendirmeyi hem AKS kümesi hem de Azure Arc etkin PostgreSQL hiper ölçek sunucusu, Azure Arc etkin PostgreSQL hiper ölçeklendirmesinin en yüksek performansından faydalanmak için bir yol olduğunu gösterelim.
AKS kümesine beşinci bir düğüm ekleyelim:

:::row:::
    :::column:::
        Önce
    :::column-end:::
    :::column:::
        Sonra
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="Önce Azure portal düzeni":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="Sonra Azure portal düzeni":::
    :::column-end:::
:::row-end:::

Mimari şöyle görünür:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="Güncelleştirmeden sonra Kubernetes kümesinde mantıksal düzen":::

Şu komutu çalıştırarak, Arc veri denetleyicisi ad alanının en fazla yeni AKS fiziksel düğümünde barındırılıp barındırılmadığını göz atalım:

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

Ve sistemimizin mimarisinin temsilini güncelleştirelim:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="Kümenin mantıksal diyagramında tüm Pod":::

Kubernetes kümesinin yeni fiziksel düğümünün yalnızca Azure Arc veri Hizmetleri için gerekli olan ölçüm Pod 'u barındırdığını gözlemlebiliriz. Bu örnekte, yalnızca Arc veri denetleyicisinin ad alanına odaklandığımızda, diğer yığınların temsil etmedik.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Azure Arc etkin PostgreSQL hiper ölçeğini yeniden ölçeklendirin

Beşinci fiziksel düğüm henüz hiçbir iş yükünü barındırmıyor. Azure yay etkin PostgreSQL hiper ölçeğini ölçeklendirirken, Kubernetes yeni PostgreSQL Pod 'un yerleşimini en iyi hale getirecek ve daha önce daha fazla iş yükü barındıran fiziksel düğümlerde onu birlikte konumlandırmamalıdır. Azure yay etkin PostgreSQL hiper ölçeğini 3 ' ten 4 çalışan olarak ölçeklendirmek için aşağıdaki komutu çalıştırın. İşlemin sonunda, sunucu grubu, beş PostgreSQL örneğine, tek bir düzenleyici ve dört çalışana dağıtılır ve dağıtılacaktır.

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

Şu çıktıyı üretir:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Veri denetleyicisinde dağıtılan sunucu gruplarını listeleyin ve sunucu grubunun şimdi dört çalışan ile çalıştığını doğrulayın:

```console
azdata arc postgres server list
```

Ve üç ile dört çalışanı ölçeklendirdiğini gözlemleyin. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

Daha önce yaptığımız gibi, sunucu grubunun şimdi dört pods kullandığını gözlemleyin:

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          13h
postgres01w-0         3/3     Running   0          13h
postgres01w-1         3/3     Running   0          13h
postgres01w-2         3/3     Running   0          179m
postgres01w-3         3/3     Running   0          3m13s
```

Sunucu grubunun şekli Şu anda:

|Sunucu grubu rolü|Sunucu grubu Pod
|----|-----
|Yöneticisidir|postgres01c-0
|Indan|postgres01w-0
|Indan|postgres01w-1
|Indan|postgres01w-2
|Indan|postgres01w-3

Hangi fiziksel düğümde barındırıldığını belirlemek için postgres01w-3 Pod öğesini açıklayalım:

```console
kubectl describe pod postgres01w-3 -n arc3
```

Ve ne kadar çalıştığını gözlemleyin:

|Sunucu grubu rolü|Sunucu grubu Pod| Ayak
|----|-----|------
|Yöneticisidir|postgres01c-0|aks-agentpool-42715708-vmss000000
|Indan|postgres01w-0|aks-agentpool-42715708-vmss000002
|Indan|postgres01w-1|aks-agentpool-42715708-vmss000003
|Indan|postgres01w-2|aks-agentpool-42715708-vmss000000
|Indan|postgres01w-3|aks-agentpool-42715708-vmss000004

Mimari şöyle görünür:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="Kubernetes en düşük kullanımı olan düğümde en yeni Pod 'yi zamanlar":::

Kubernetes, yeni PostgreSQL Pod öğesini Kubernetes kümesinin en az yüklü fiziksel düğümünde zamanlayamıyor.

## <a name="summary"></a>Özet

Azure Arc etkin sunucu grubunu yatay olarak ölçeklendirmenin avantajlarından ve performansından en iyi şekilde yararlanmak için Kubernetes kümesi içinde kaynak çekişmesini önleyin:
- Azure Arc etkin PostgreSQL hiper ölçek sunucu grubu ve aynı Kubernetes kümesinde barındırılan diğer iş yükleri arasında
- Azure yay etkin PostgreSQL hiper ölçek sunucu grubunu oluşturan tüm PostgreSQL örnekleri arasında

Bunu çeşitli yollarla elde edebilirsiniz:
- Hem Kubernetes hem de Azure yay etkin Postgres hiper ölçeğini ölçeklendirin: Kubernetes kümesini yatay olarak ölçeklendirerek, Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunu ölçeklendirdiğinizde aynı şekilde ölçeği değerlendirin. Sunucu grubuna eklediğiniz her çalışan için kümeye bir fiziksel düğüm ekleyin.
- Kubernetes 'i ölçeklendirmeden Azure Arc etkin Postgres hiper ölçeğini ölçeklendirin. Kubernetes (Azure Arc etkin PostgreSQL hiper ölçek dahil) içinde barındırılan iş yükleri üzerinde doğru kaynak kısıtlamalarını (bellek ve sanal çekirdek üzerinde istek ve sınırlar) ayarlayarak, Kubernetes üzerinde iş yüklerini birlikte bulundurma ve kaynak çekişmenin riskini azaltmanız gerekir. Kubernetes kümesinin fiziksel düğümlerinin fiziksel özelliklerinin tanımladığınız kaynak kısıtlamalarını kabul edebilir emin olmanız gerekir. Ayrıca, iş yüklerinin zaman içinde geliştikçe veya Kubernetes kümesine daha fazla iş yükü eklendiğinden, eş zamanlı olarak kalmaya devam edin.
- Kubernetes mekanizmalarını (pod seçici, benzeşim, benzeşim önleme) kullanarak pods 'nin yerleşimini etkiler.

## <a name="next-steps"></a>Sonraki adımlar

[Daha fazla çalışan düğümü ekleyerek Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunu ölçeklendirin](scale-out-postgresql-hyperscale-server-group.md)
