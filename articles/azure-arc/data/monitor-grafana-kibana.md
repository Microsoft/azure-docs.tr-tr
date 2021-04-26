---
title: Kibana ve Grafana kullanarak günlükleri ve ölçümleri görüntüleme
description: Kibana ve Grafana kullanarak günlükleri ve ölçümleri görüntüleme
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: how-to
ms.openlocfilehash: cb53aba300b933c78d9ac2f5fc5cf8054f3413e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670010"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Kibana ve Grafana kullanarak günlükleri ve ölçümleri görüntüleme

Kibana ve Grafana web panoları, Azure Arc özellikli veri hizmetleri tarafından kullanılan Kubernetes ad alanlarına içgörü ve netlik getirmek için sağlanır.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Azure Arc 'da Azure SQL yönetilen örneklerini izleme

Arc etkin SQL yönetilen örneği için günlüklere ve izleme panolarına erişmek için aşağıdaki `azdata` CLI komutunu çalıştırın

```bash

azdata arc sql endpoint list -n <name of SQL instance>

```
İlgili Grafana panoları şunlardır:

* "Azure SQL yönetilen örnek ölçümleri"
* "Ana düğüm ölçümleri"
* "Konak pods ölçümleri"


> [!NOTE]
>  Bir Kullanıcı adı ve parola girmeniz istendiğinde, Azure Arc veri denetleyicisi 'ni oluşturduğunuz sırada verdiğiniz kullanıcı adını ve parolayı girin.

> [!NOTE]
>  Önizlemede kullanılan sertifikalar otomatik olarak imzalanan sertifikalar olduğundan, sizden bir sertifika uyarısıyla ilgili olarak uyarılırsınız.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Azure Arc 'da PostgreSQL için Azure veritabanı hiper ölçek 'i izleme

PostgreSQL hiper ölçeğinde günlüklere ve izleme panolarına erişmek için aşağıdaki `azdata` CLI komutunu çalıştırın

```bash

azdata arc postgres endpoint list -n <name of postgreSQL instance>

```

İlgili postgreSQL panoları şunlardır:

* "Postgres ölçümleri"
* "Postgres tablo ölçümleri"
* "Ana düğüm ölçümleri"
* "Konak pods ölçümleri"


## <a name="additional-firewall-configuration"></a>Ek güvenlik duvarı yapılandırması

Veri denetleyicisinin dağıtıldığı yere bağlı olarak, kibana ve Grafana uç noktalarına erişmek için güvenlik duvarınızdaki bağlantı noktalarını açmanız gerektiğini görebilirsiniz.

Bunun bir Azure VM için nasıl yapılacağını gösteren bir örnek aşağıda verilmiştir. Betiği kullanarak Kubernetes dağıttıysanız bunu yapmanız gerekir.

Aşağıdaki adımlarda, kibana ve Grafana uç noktaları için NSG kuralının nasıl oluşturulacağı vurgulanacak:

### <a name="find-the-name-of-the-nsg"></a>NSG'nin adını bulma

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>NSG kuralını ekleme

NSG adına sahip olduktan sonra aşağıdaki komutu kullanarak bir kural ekleyebilirsiniz:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>Sonraki adımlar
- [Ölçümleri ve günlükleri Azure izleyici 'ye yüklemeyi](upload-metrics-and-logs-to-azure-monitor.md) deneyin
- Grafana hakkında bilgi edinin:
   - [Başlarken](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grafana temelleri](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Grafana öğreticileri](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Kibana hakkında bilgi edinin
   - [Giriş](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana Kılavuzu](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Kibana 'teki veri görselleştirmeleriyle Dashboard inmenize 'a giriş](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Kibana panoları oluşturma](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

