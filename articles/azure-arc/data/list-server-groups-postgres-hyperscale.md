---
title: Azure Arc veri denetleyicisinde oluşturulan Azure Arc etkin PostgreSQL hiper ölçek sunucusu gruplarını listeleme
description: Azure Arc veri denetleyicisinde oluşturulan Azure Arc etkin PostgreSQL hiper ölçek sunucusu gruplarını listeleme
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90942146"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Azure Arc veri denetleyicisinde oluşturulan Azure Arc etkin PostgreSQL hiper ölçek sunucusu gruplarını listeleme

Bu makalede, Arc veri denetleyicinizde oluşturulan sunucu gruplarının listesini nasıl alabileceğiniz açıklanmaktadır.

Bu listeyi almak için, Arc veri denetleyicisine bağlandıktan sonra aşağıdaki yöntemlerden birini kullanın:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>Azdata ile CLı 'dan
Komutun genel biçimi:
```console
azdata arc postgres server list
```

Şu şekilde bir çıktı döndürür:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
Bu komut için kullanılabilir parametreler hakkında daha fazla ayrıntı için şunu çalıştırın:
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>Kubectl ile CLı 'dan
Aşağıdaki komutlardan birini çalıştırın.

**Postgres 'nin sürümünden bağımsız olarak sunucu gruplarını listelemek için şunu çalıştırın:**
```console
kubectl get postgresqls
```
Şu şekilde bir çıktı döndürür:
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Belirli bir Postgres sürümünün sunucu gruplarını listelemek için şunu çalıştırın:**
```console
kubectl get postgresql-12
```

Postgres 'nin 11 sürümünü çalıştıran sunucu gruplarını listelemek için _PostgreSQL-12_ ' yi _PostgreSQL-11_ ile değiştirin.

## <a name="next-steps"></a>Sonraki adımlar:

* [Bağlantı uç noktalarını alma ve sunucu grubunuza bağlanmak için bağlantı dizelerini oluşturma hakkındaki makaleyi okuyun](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Azure Arc etkin bir PostgreSQL hiper ölçek sunucu grubu yapılandırmasını gösterme hakkında makaleyi okuyun](show-configuration-postgresql-hyperscale-server-group.md)
