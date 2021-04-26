---
title: Azure Arc etkin veri hizmetleri sorunlarını gidermek için günlükleri alın
description: Azure Arc etkin veri hizmetleri sorunlarını gidermek için bir veri denetleyicisinden günlük dosyaları almayı öğrenin.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93234060"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Azure Arc etkin veri hizmetleri sorunlarını gidermek için günlükleri alın

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

Devam etmeden önce şunları yapmanız gerekir:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. Daha fazla bilgi için bkz. [Azure Arc veri hizmetlerini dağıtmak ve yönetmek için istemci araçlarını kurma](./install-client-tools.md).
* Azure Arc etkin veri denetleyicisinde oturum açmak için bir yönetici hesabı.

## <a name="get-log-files"></a>Günlük dosyalarını al

Sorun giderme amacıyla tüm yığınlara veya belirli bir FID genelinde hizmet günlüklerini alabilirsiniz. Tek yönlü, komutu gibi standart Kubernetes araçlarını kullanmaktır `kubectl logs` . Bu makalede, [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] Tüm günlüklerin aynı anda daha kolay bir şekilde alınacağını sağlayan aracını kullanacaksınız.

1. Veri denetleyicisinde bir yönetici hesabıyla oturum açın.

   ```console
   azdata login
   ```

2. Günlüklerin dökümünü almak için aşağıdaki komutu çalıştırın:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Örnek:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

Veri denetleyicisi, adlı bir alt dizinde geçerli çalışma dizininde bulunan günlük dosyalarını oluşturur `logs` . 

## <a name="options"></a>Seçenekler

`azdata arc dc debug copy-logs`Komutu çıktıyı yönetmek için aşağıdaki seçenekleri sağlar:

* Parametresini kullanarak günlük dosyalarını farklı bir dizine çıktı `--target-folder` .
* Parametreyi atlayarak dosyaları sıkıştırın `--skip-compress` .
* ' İ atlayarak bellek dökümlerini tetikleyin ve dahil edin `--exclude-dumps` . Bellek dökümlerini Microsoft Desteği istemediği müddetçe bu yöntemi önermiyoruz. Bellek dökümünden yararlanmak için veri denetleyicisi ayarının `allowDumps` veri denetleyicisi oluşturulduğunda olarak ayarlanmış olması gerekir `true` .
* Yalnızca belirli bir pod ( `--pod` ) veya Container () için bir ada göre günlükleri toplamak üzere filtreleyin `--container` .
* Ve parametrelerini geçirerek belirli bir özel kaynağın günlüklerini toplamak için filtreleyin `--resource-kind` `--resource-name` . `resource-kind`Parametre değeri özel kaynak tanımı adlarından biri olmalıdır. Bu adları komutunu kullanarak alabilirsiniz `kubectl get customresourcedefinition` .

Bu parametrelerle, `<parameters>` Aşağıdaki örnekte öğesini değiştirebilirsiniz: 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Örnek:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

Aşağıdaki klasör hiyerarşisi bir örnektir. Bu, Pod adı, ardından kapsayıcı ve sonra kapsayıcı içindeki dizin hiyerarşisine göre düzenlenmiştir.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Sonraki adımlar

[azdata yay DC hata ayıklama kopyası-Günlükler](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)
