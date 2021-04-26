---
title: Machine Learning Services için önemli farklılıklar
description: Bu makalede, Azure SQL yönetilen örneği ve SQL Server Machine Learning Services arasındaki temel farklılıklar açıklanmaktadır Machine Learning Services.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599553"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Azure SQL Yönetilen Örneği ve SQL Server içindeki Machine Learning Services özellikleri arasındaki temel farklar

Bu makalede, [Azure SQL yönetilen örneği 'nde Machine Learning Services](machine-learning-services-overview.md) ve [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning)arasındaki işlevlerde bazı önemli farklılıklar açıklanmaktadır.

## <a name="language-support"></a>Dil desteği

Hem SQL yönetilen örneği Machine Learning Services hem de Python ve R [genişletilebilirlik çerçevesini](/sql/machine-learning/concepts/extensibility-framework)desteklemek SQL Server. SQL yönetilen örneğindeki temel farklılıklar şunlardır:

- Yalnızca Python ve R desteklenir. Java gibi dış diller eklenemez.

- Python ve R 'nin ilk sürümleri farklıdır:

  | Platform                   | Python çalışma zamanı sürümü           | R çalışma zamanı sürümleri                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Azure SQL Yönetilen Örnek | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 ve 3.7.2 (CU22 ve üzeri) | 3.3.3 ve 3.5.2 (CU22 ve üzeri)     |
  | SQL Server 2016            | Kullanılamaz                    | 3.2.2 ve 3.5.2 (SP2 CU14 ve üzeri) |

## <a name="python-and-r-packages"></a>Python ve R paketleri

Dış çalışma zamanları (Java gibi) bağlı olan veya yükleme ya da kullanım için işletim sistemi API 'Lerine erişmesi gereken paketler için SQL yönetilen örneği desteklenmez.

Python ve R paketlerinin yönetilmesi hakkında daha fazla bilgi için bkz.:

- [Python paket bilgilerini alma](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [R paket bilgilerini alma](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Kaynak idaresi

SQL yönetilen örneği 'nde, [Resource Governor](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true)ile R kaynaklarını kısıtlamak mümkün değildir ve dış kaynak havuzları desteklenmez.

Varsayılan olarak, genişletilebilirlik etkinleştirildiğinde R kaynakları kullanılabilir SQL yönetilen örnek kaynaklarının en fazla %20 ' si olarak ayarlanır. Bu varsayılan yüzdeyi değiştirmek için, konumunda bir Azure destek bileti oluşturun [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) .

Genişletilebilirlik aşağıdaki SQL komutlarıyla etkinleştirildi (SQL yönetilen örneği yeniden başlatılır ve birkaç saniye boyunca kullanılamaz):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Genişletilebilirliği devre dışı bırakmak ve bellek ve CPU kaynaklarının %100 ' ı SQL Server için geri yüklemek için aşağıdaki komutları kullanın:

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

SQL yönetilen örneği için kullanılabilen toplam kaynaklar hangi hizmet katmanını seçdiğinize bağlıdır. Daha fazla bilgi için bkz. [Azure SQL veritabanı satın alma modelleri](/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Yetersiz bellek hatası

Bellek kullanımı, R betiklerinizde kullanılan bellek miktarına ve yürütülen paralel sorgu sayısına göre değişir. R için yeterli kullanılabilir bellek yoksa bir hata iletisi alırsınız. Yaygın hata iletileri:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Bu hatalardan birini alırsanız, veritabanınızı daha yüksek bir hizmet katmanına ölçeklendirerek bu sorunu çözebilirsiniz.

## <a name="sql-managed-instance-pools"></a>SQL yönetilen örnek havuzları

Machine Learning Services Şu anda [Azure SQL yönetilen örnek havuzları (Önizleme)](instance-pools-overview.md)üzerinde desteklenmiyor.

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. genel bakış, [Azure SQL yönetilen örneği Machine Learning Services](machine-learning-services-overview.md).
- Machine Learning Services 'de Python kullanmayı öğrenmek için bkz. [Python betiklerini çalıştırma](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Machine Learning Services 'de R 'yi nasıl kullanacağınızı öğrenmek için bkz. [Run r Scripts](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
