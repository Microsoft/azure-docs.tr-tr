---
title: Azure Data Factory kimlik bilgilerini şifreleyin
description: Şirket içi veri depolarınız için kimlik bilgilerini kendi kendine barındırılan tümleştirme çalışma zamanına sahip bir makinede şifrelemeyi ve depolamayı öğrenin.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 41e353931fb2d9fe26c0a6bd73d5085495ad7b78
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73675052"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Azure Data Factory içindeki şirket içi veri depoları için kimlik bilgilerini şifreleyin
Şirket içi veri depolarınız (hassas bilgilerle bağlantılı hizmetler) için kimlik bilgilerini, şirket içinde barındırılan tümleştirme çalışma zamanına sahip bir makinede şifreleyebilir ve saklayabilirsiniz. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kimlik bilgileriyle bir JSON tanım dosyası geçirin <br/>Şifrelenmiş kimlik bilgileriyle bir çıktı JSON tanım dosyası oluşturmak için [**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet 'i. Ardından, bağlantılı hizmetleri oluşturmak için güncelleştirilmiş JSON tanımını kullanın.

## <a name="author-sql-server-linked-service"></a>Bağlı hizmet SQL Server yazar
Aşağıdaki içeriğe sahip herhangi bir klasörde **Sqlserverlinkedservice. JSON** ADLı bir JSON dosyası oluşturun:  

Dosyayı kaydetmeden önce `<servername>`, `<databasename>`, `<username>`ve `<password>` SQL Server değerlerle değiştirin. `<integration runtime name>`, tümleştirme çalışma zamanının adıyla değiştirin. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Kimlik bilgilerini şifreleme
Şirket içi şirket içinde barındırılan tümleştirme çalışma zamanındaki JSON yükünün gizli verilerini şifrelemek için **New-AzDataFactoryV2LinkedServiceEncryptedCredential**' ı ÇALıŞTıRıN ve JSON yükünde geçiş yapın. Bu cmdlet, kimlik bilgilerinin DPAPI kullanılarak şifrelenmesini ve şirket içinde barındırılan tümleştirme çalışma zamanı düğümüne yerel olarak depolanmasını sağlar. Kimlik bilgisine şifreli başvuruyu içeren çıkış yükü başka bir JSON dosyasına yönlendirilebilir (Bu durumda, ' encryptedLinkedService. json ').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>JSON 'ı şifreli kimlik bilgileriyle kullanma
Şimdi, **Sqlserverlinkedservice**öğesini ayarlamak için şifrelenmiş kimlik bilgilerini içeren önceki KOMUTTAN çıkış json dosyasını kullanın.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Sonraki adımlar
Veri taşıma hakkında güvenlik konuları hakkında bilgi için bkz. [veri taşıma güvenliği konuları](data-movement-security-considerations.md).

