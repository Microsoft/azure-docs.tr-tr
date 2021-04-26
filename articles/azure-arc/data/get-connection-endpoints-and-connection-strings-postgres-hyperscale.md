---
title: Arc etkin PostgreSQL hiper ölçek sunucu grubu için bağlantı uç noktaları ve form bağlantı dizeleri alın
titleSuffix: Azure Arc enabled data services
description: Arc etkin PostgreSQL hiper ölçek sunucu grubu için bağlantı uç noktaları ve form bağlantı dizeleri alın
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: de7d23689ae984ea0abece5edb03cf8a0c3a9be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670350"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Arc etkin PostgreSQL hiper ölçek sunucu grubu için bağlantı uç noktaları ve form bağlantı dizeleri alın

Bu makalede, sunucu grubunuz için bağlantı uç noktalarını nasıl alabileceğiniz ve uygulamalarınız ve/veya araçlarınızla kullanacağınız bağlantı dizelerini nasıl oluşturabileceğiniz açıklanmaktadır.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Bağlantı uç noktalarını al:

### <a name="from-cli-with-azdata"></a>Azdata ile CLı 'dan
#### <a name="1-connect-to-your-arc-data-controller"></a>1. Arc veri denetleyicinize bağlanın:
- Arc veri denetleyicisinin ana bilgisayarında zaten açılmış bir oturumunuz varsa: aşağıdaki komutu çalıştırın:
```console
azdata login
```

- Arc veri denetleyicisinin konağında açık bir oturumunuz yoksa: aşağıdaki komutu çalıştırın 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. bağlantı uç noktalarını göster
Aşağıdaki komutu çalıştırın:
```console
azdata arc postgres endpoint list -n <server group name>
```
Örnek:
```console
azdata arc postgres endpoint list -n postgres01
```

Uç noktaların listesini gösterir: uygulamanızı bağlamak ve Log Analytics ve izleme için veritabanını, kibana ve Grafana uç noktalarını kullanmak için kullandığınız PostgreSQL uç noktası. Örnek: 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
Bu uç noktaları şu şekilde kullanın:
- Bağlantı dizelerinizi biçimlendirmek ve istemci araçlarınızla veya uygulamalarınızla bağlantı kurmak
- Tarayıcınızdan Grafana ve Kibana panolarına erişin

Örneğin, _PostgreSQL örneği_ adlı bitiş noktasını, psql ile sunucu grubunuza bağlamak için kullanabilirsiniz. Örnek:
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - "_PostgreSQL örneği_" adlı bitiş noktasında gösterilen _Postgres_ kullanıcısının parolası, sunucu grubunu dağıttığınızda seçtiğiniz paroladır.
> - Azdata hakkında: bağlantınızla ilişkili kira yaklaşık 10 saat sürer. Bundan sonra yeniden bağlanmanız gerekir. Kiranın süresi dolmuşsa, azdata (azdata oturum açma dışında) ile bir komut yürütmeye çalıştığınızda aşağıdaki hata iletisini alırsınız: _hata: (401)_ 
>  _Neden: yetkisiz_ 
>  _http yanıtı üstbilgileri: httpheaderdict ({' Date ': ' güneş, 06 Eyl 2020 16:58:38 GMT ', ' Content-Length ': ' 0 ', ' www-Authenticate ': '_ 
>  _temel bölge = "oturum açma_ kimlik bilgileri gerekli", taşıyıcı hatası = "invalid_token", error_description = "belirtecin süresi dolmuştur" '}) _ Bu durumda, yukarıda açıklandığı gibi azdata ile yeniden bağlanmanız gerekir.

## <a name="from-cli-with-kubectl"></a>Kubectl ile CLı 'dan
- Sunucu grubunuz Postgres sürüm 12 ' dir (varsayılan), aşağıdaki komut:
```console
kubectl get postgresql-12/<server group name> -n <namespace name>
```
- Sunucu grubunuz Postgres sürüm 11 ise aşağıdaki komut:
```console
kubectl get postgresql-11/<server group name> -n <namespace name>
```

Bu komutlar, aşağıdaki gibi bir çıktı oluşturacaktır. Bu bilgileri, bağlantı dizelerinizi oluşturmak için kullanabilirsiniz:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>Form bağlantı dizeleri:
Sunucu grubunuz için bağlantı dizeleri şablonlarının aşağıdaki tablosunu kullanın. Daha sonra bunları kopyalayabilir/yapıştırabilir ve daha sonra istediğiniz şekilde özelleştirebilirsiniz:

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>Sonraki adımlar
- Ölçek Genişletme [(çalışan düğümleri ekleme)](scale-out-postgresql-hyperscale-server-group.md) hakkında bilgi edinmek için sunucu grubunuz
- Sunucu grubunuzu [ölçeği artırma veya azaltma (bellek/sanal çekirdekleri artırma/azaltma)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) hakkında bilgi edinin


