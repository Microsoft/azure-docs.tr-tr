---
title: Azure SQL kaynaklarını oluşturmak veya değiştirmek için T-SQL komutlarını engelle
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: Bu makalede, Azure yöneticilerinin Azure SQL kaynaklarını oluşturmak veya değiştirmek için T-SQL komutlarının engellenmesine izin veren bir özellik ayrıntılı olarak anlatılmaktadır
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 4ecaa8a3ee1d11ea13563ae5c74835b8d62fd960
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556202"
---
# <a name="what-is-block-t-sql-crud-feature"></a>Blok T-SQL CRUD özelliği nedir?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


Bu özellik, Azure yöneticilerinin T-SQL aracılığıyla Azure SQL kaynaklarının oluşturulmasını veya değiştirilmesini engellemeyi sağlar. Bu, tüm Azure SQL veritabanı veya yönetilen örnekteki SQL kaynaklarını etkileyen T-SQL komutlarının engellemek için abonelik düzeyinde zorlanır.

## <a name="overview"></a>Genel Bakış

T-SQL aracılığıyla kaynakların oluşturulmasını veya değiştirilmesini engellemek ve belirli bir abonelik için Azure Resource Manager şablonu (ARM şablonu) aracılığıyla kaynak yönetimini zorlamak için, Azure portal içindeki abonelik düzeyi Önizleme özellikleri kullanılabilir. Bu özellikle, ARM şablonları aracılığıyla kurumsal standartları zorlamak için [Azure ilkeleri](/azure/governance/policy/overview) kullanırken faydalıdır. T-SQL Azure Ilkelerine bağlı olmadığından, T-SQL oluşturma veya değiştirme işlemlerinde bir blok uygulanabilir. Engellenen sözdizimi, Azure SQL, özellikle `CREATE DATABASE` , ve deyimlerinde veritabanları IÇIN CRUD (oluşturma, güncelleştirme, silme) deyimleri içerir `ALTER DATABASE` `DROP DATABASE` . 

T-SQL CRUD işlemleri Azure portal, [PowerShell](/powershell/module/az.resources/register-azproviderfeature)veya [Azure CLI](/cli/azure/feature#az_feature_register)aracılığıyla engellenebilir.

## <a name="permissions"></a>İzinler

Bu özelliği kaydetmek veya kaldırmak için, Azure kullanıcısı aboneliğin sahibi veya katkıda bulunan rolünün bir üyesi olmalıdır.

## <a name="examples"></a>Örnekler

Aşağıdaki bölümde, Azure portal ' de Microsoft. SQL kaynak sağlayıcısı ile bir önizleme özelliğinin nasıl kaydedileceği veya kaydını silebileceğini açıklanmaktadır: 

### <a name="register-block-t-sql-crud"></a>Kayıt blok T-SQL CRUD

1. Azure portal aboneliğinizden gidin.
2. **Önizleme özellikleri** sekmesinde öğesini seçin. 
3. **Blok T-SQL CRUD** öğesini seçin.
4. **Blok T-SQL CRUD** üzerinde seçeneğini belirledikten sonra, bu bloğu Microsoft. SQL kaynak sağlayıcısı ile kaydettirmek için yeni bir pencere açılır, **Kaydet**' i seçin.

![Önizleme özellikleri listesindeki "blok T-SQL CRUD" yi seçin](./media/block-tsql-crud/block-tsql-crud.png)

!["Blok T-SQL CRUD" işaretli olarak Kaydet ' i seçin](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>Microsoft. SQL kaynak sağlayıcısını yeniden Kaydet 
T-SQL CRUD bloğunu Microsoft. SQL kaynak sağlayıcısı ile kaydettikten sonra, değişikliklerin etkili olması için Microsoft. SQL kaynak sağlayıcısını yeniden kaydetmeniz gerekir. Microsoft. SQL kaynak sağlayıcısını yeniden kaydetmek için:

1. Azure portal aboneliğinizden gidin.
2. **Kaynak sağlayıcıları** sekmesinde öğesini seçin.
3. **Microsoft. SQL** kaynak sağlayıcısı 'nı arayın ve seçin.
4. **Yeniden kaydet**' i seçin. 

> [!NOTE]
> Yeniden kayıt adımı, T-SQL bloğunun aboneliğinize uygulanması için zorunludur. 

![Microsoft. SQL kaynak sağlayıcısı 'nı yeniden kaydetme](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>Blok T-SQL CRUD kaldırılıyor
Aboneliğinizi T-SQL oluşturma veya değiştirme işlemlerini aboneliğinizden kaldırmak için önce önceden kaydedilmiş T-SQL bloğunun kaydını silin. Sonra, T-SQL bloğunun kaldırılması için yukarıda gösterildiği gibi Microsoft. SQL kaynak sağlayıcısını yeniden kaydedin. 


## <a name="next-steps"></a>Sonraki adımlar

- [Azure SQL veritabanı güvenlik özelliklerine genel bakış](security-overview.md)
- [Azure SQL veritabanı güvenlik en iyi uygulamaları](security-best-practice.md)
