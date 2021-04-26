---
title: Azure Data Factory kimlik bilgilerini şifreleyin
description: Şirket içi veri depolarınız için kimlik bilgilerini kendi kendine barındırılan tümleştirme çalışma zamanına sahip bir makinede şifrelemeyi ve depolamayı öğrenin.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 59d177aa3baf25f185201f1b6c4738cfce9c25a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392655"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Azure Data Factory içindeki şirket içi veri depoları için kimlik bilgilerini şifreleyin

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Şirket içi veri depolarınız (hassas bilgilerle bağlantılı hizmetler) için kimlik bilgilerini, şirket içinde barındırılan tümleştirme çalışma zamanına sahip bir makinede şifreleyebilir ve saklayabilirsiniz. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kimlik bilgileriyle bir JSON tanım dosyası geçirin <br/>Şifrelenmiş kimlik bilgileriyle bir çıktı JSON tanım dosyası oluşturmak için [**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet 'i. Ardından, bağlantılı hizmetleri oluşturmak için güncelleştirilmiş JSON tanımını kullanın.

## <a name="author-sql-server-linked-service"></a>Bağlı hizmet SQL Server yazar
Aşağıdaki içeriğe sahip herhangi bir klasörde **SqlServerLinkedService.js** ADLı bir JSON dosyası oluşturun:  

`<servername>` `<databasename>` Dosyayı kaydetmeden önce,,, `<username>` ve `<password>` SQL Server değerleri ile değiştirin. Ve öğesini `<integration runtime name>` tümleştirme çalışma zamanının adıyla değiştirin. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
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
Şirket içi şirket içinde barındırılan tümleştirme çalışma zamanındaki JSON yükünün gizli verilerini şifrelemek için **New-AzDataFactoryV2LinkedServiceEncryptedCredential**' ı ÇALıŞTıRıN ve JSON yükünde geçiş yapın. Bu cmdlet, kimlik bilgilerinin DPAPI kullanılarak şifrelenmesini ve şirket içinde barındırılan tümleştirme çalışma zamanı düğümüne yerel olarak depolanmasını sağlar. Kimlik bilgisine şifreli başvuruyu içeren çıkış yükü başka bir JSON dosyasına yönlendirilebilir (Bu durumda, ' encryptedLinkedService.json ').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>JSON 'ı şifreli kimlik bilgileriyle kullanma
Şimdi, **Sqlserverlinkedservice** öğesini ayarlamak için şifrelenmiş kimlik bilgilerini içeren önceki KOMUTTAN çıkış json dosyasını kullanın.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Sonraki adımlar
Veri taşıma hakkında güvenlik konuları hakkında bilgi için bkz. [veri taşıma güvenliği konuları](data-movement-security-considerations.md).

