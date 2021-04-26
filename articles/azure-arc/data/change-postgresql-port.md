---
title: PostgreSQL bağlantı noktasını değiştirme
description: Azure Arc etkin PostgreSQL hiper ölçek sunucu grubunun dinlediği bağlantı noktasını değiştirin.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93328758"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>Sunucu grubunun dinlediği bağlantı noktasını değiştirme 

Bağlantı noktasını değiştirmek, sunucu grubunun standart bir düzenleme işlemidir. Bağlantı noktasını değiştirmek için aşağıdaki komutu çalıştırın:
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

Örneğin, sunucu grubunuzun adının _postgres01_ olduğunu ve _866_ numaralı bağlantı noktasında dinlenmesini istediğinizi varsayalım. Şu komutu çalıştırın:
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>Bağlantı noktasının değiştirildiğini doğrulama

Bağlantı noktasının değiştirildiğini doğrulamak için, sunucu grubunuzun yapılandırmasını göstermek üzere aşağıdaki komutu çalıştırın:
```console
azdata arc postgres server show -n <server group name>
```

Bu komutun çıkışında, sunucu grubunuzun belirtimlerinin "hizmet" bölümünde "bağlantı noktası" öğesi için görüntülenen bağlantı noktası numarasına bakın.
Alternatif olarak, sunucu grubunuzun belirtimlerinin durum bölümünün externalEndpoint öğesinde, IP adresinin, yapılandırdığınız bağlantı noktası numarası tarafından izlenen olduğunu doğrulayabilirsiniz.

Bir çizim olarak, yukarıdaki örneğe devam ediyoruz komutunu çalıştırın:
```console
azdata arc postgres server show -n postgres01
```

Ayrıca, 866 numaralı bağlantı noktasını burada bulabilirsiniz:

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
ve buraya bakın

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>Sonraki adımlar
- [Sunucu grubunuza bağlanma](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)hakkında bilgi edinin.
- Belgelerinin Bölüm How-to\Manage\Configure & ölçeği bölümünde sunucu grubunuzun diğer yönlerini nasıl yapılandırabilirim hakkında bilgi edinin.
