---
title: Azure Arc etkin SQL yönetilen örneğini yapılandırma
description: Azure Arc etkin SQL yönetilen örneğini yapılandırma
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e2443014f6788504a11784945078187a5a72de4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98985878"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Azure Arc etkin SQL yönetilen örneğini yapılandırma

Bu makalede, Azure Arc etkin SQL yönetilen örneğinin nasıl yapılandırılacağı açıklanmaktadır.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources"></a>Kaynakları yapılandırma

### <a name="configure-using-azure-data-cli-azdata"></a>Kullanarak yapılandırma [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Azure Arc etkin SQL yönetilen örneklerinin yapılandırmasını ile düzenleyebilirsiniz [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] . Yapılandırma seçeneklerini görmek için aşağıdaki komutu çalıştırın. 

```
azdata arc sql mi edit --help
```

Aşağıdaki örnek, CPU çekirdeğini ve bellek isteklerini ve sınırlarını ayarlar.

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

SQL yönetilen örneğinde yapılan değişiklikleri görüntülemek için aşağıdaki komutları kullanarak yapılandırma YAML dosyasını görüntüleyebilirsiniz:

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>Sunucu seçeneklerini yapılandırma

Oluşturma zamanından sonra, Azure Arc etkin SQL yönetilen örneği için sunucu yapılandırma ayarlarını yapılandırabilirsiniz. Bu makalede, MSSQL aracısının etkinleştirilmesi veya devre dışı bırakılması gibi ayarların nasıl yapılandırılacağı açıklanır. sorun giderme senaryoları için belirli izleme bayraklarını etkinleştirin.

Bu ayarlardan herhangi birini değiştirmek için şu adımları izleyin:

1. `mssql-custom.conf`Hedeflenen ayarları içeren özel bir dosya oluşturun. Aşağıdaki örnekte SQL Aracısı etkinleştirilir ve izleme bayrağı 1204 etkinleştirilir.:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. `mssql-custom.conf`Dosyayı `/var/opt/mssql` `mssql-miaa` Pod 'daki kapsayıcıda kopyalayın `master-0` . `<namespaceName>`Büyük veri kümesi adıyla değiştirin.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. SQL Server örneğini yeniden başlatın.  `<namespaceName>`Büyük veri kümesi adıyla değiştirin.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Bilinen sınırlamalar**
- Yukarıdaki adımlarda Kubernetes Küme Yöneticisi izinleri gerekir
- Bu, önizleme genelinde değişebilir
