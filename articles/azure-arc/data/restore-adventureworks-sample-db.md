---
title: AdventureWorks örnek veritabanını SQL yönetilen örneğine geri yükleme
description: AdventureWorks örnek veritabanını SQL yönetilen örneğine geri yükleme
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a6b3577fe820c9dadcb3c8415aa7a951a3283284
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641814"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>AdventureWorks örnek veritabanını SQL yönetilen örneği-Azure yaya geri yükleme

[AdventureWorks](/sql/samples/adventureworks-install-configure) , genellikle öğreticiler ve örnekler IÇIN kullanılan OLTP veritabanını içeren örnek bir veritabanıdır. [SQL Server örnekleri GitHub deposunun](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)bir parçası olarak Microsoft tarafından sağlanır ve sürdürülür.

Bu belgede, AdventureWorks örnek veritabanını SQL yönetilen örneğiniz-Azure yaya geri yüklemek için basit bir işlem açıklanmaktadır.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>AdventureWorks yedekleme dosyasını indirin

AdventureWorks Backup (. bak) dosyasını SQL yönetilen örnek kapsayıcınıza indirin. Bu örnekte, `kubectl exec` . bak dosyasını kapsayıcıya indirmek IÇIN SQL yönetilen örnek kapsayıcısının içindeki bir komutu uzaktan yürütmek için komutunu kullanın. `wget`SQL yönetilen örnek kapsayıcısının içinde olmasını istediğiniz başka veritabanı yedekleme dosyalarınız varsa, bu dosyayı tarafından erişilebilen herhangi bir konumdan indirin. SQL yönetilen örnek kapsayıcısının içindeyken, Standart T-SQL kullanılarak geri yükleme kolaydır `RESTORE DATABASE` .

Çalıştırmadan önce pod adı ve ad alanı adının değeri yerine. bak dosyasını indirmek için aşağıdaki gibi bir komut çalıştırın.
> [!NOTE]
>  Dosyayı GitHub 'dan indirmek için Kapsayıcınızın 443 üzerinde internet bağlantısı olması gerekir

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

Örnek

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>AdventureWorks veritabanını geri yükleme

Benzer şekilde, `kubectl` `sqlcmd` veritabanını geri yüklemek için T-SQL komutunu yürütmek üzere SQL yönetilen örnek kapsayıcısına dahil edilen CLI aracını kullanmak için bir EXEC komutu çalıştırabilirsiniz.

Veritabanını geri yüklemek için aşağıdaki gibi bir komut çalıştırın. Çalıştırmadan önce pod adı, parola ve ad alanı adı değerlerini değiştirin.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
Örnek

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
