---
title: Azure Cosmos DB'de veritabanı hesaplarını yönetmeyi öğrenin
description: Azure Cosmos DB'de veritabanı hesaplarını yönetmeyi öğrenin
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 049be390403fe984ed4f8f38a4cdc86e24060e49
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582628"
---
# <a name="manage-an-azure-cosmos-account"></a>Azure Cosmos hesabını yönetme

Bu makalede, Azure Cosmos hesabında Azure portal, Azure PowerShell, Azure CLı ve Azure Resource Manager şablonları kullanılarak çeşitli görevlerin nasıl yönetileceği açıklanır.

## <a name="create-an-account"></a>Hesap oluşturma

### <a id="create-database-account-via-portal"></a>Azure portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

Lütfen bkz. [Azure CLI ile Azure Cosmos DB hesabı oluşturma](manage-with-cli.md#create-an-azure-cosmos-db-account)

### <a id="create-database-account-via-ps"></a>Azure PowerShell

Lütfen bkz. [PowerShell ile Azure Cosmos DB hesabı oluşturma](manage-with-powershell.md#create-account)

### <a id="create-database-account-via-arm-template"></a>Azure Resource Manager şablonu

Bu Azure Resource Manager şablonu, tutarlılık düzeyi, otomatik yük devretme ve çoklu yönetici seçeneklerini belirlemek için iki bölge ve seçenek ile yapılandırılmış SQL API 'SI için bir Azure Cosmos hesabı oluşturur. Bu şablonu dağıtmak için Benioku sayfasında Azure 'a Dağıt ' a tıklayın, [Azure Cosmos hesabı oluşturun](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-sql)

## <a name="addremove-regions-from-your-database-account"></a>Veritabanı hesabınızda bölge ekleme/çıkarma işlemi gerçekleştirme

### <a id="add-remove-regions-via-portal"></a>Azure portal

1. [Azure portalda](https://portal.azure.com) oturum açın.

1. Azure Cosmos hesabınıza gidin ve **verileri genel olarak Çoğalt** menüsünü açın.

1. Bölge eklemek için, istediğiniz bölge (ler) e ait **+** etiketiyle haritada altıons 'yi seçin. Alternatif olarak, bir bölge eklemek için **+ bölge Ekle** seçeneğini belirleyip açılan menüden bir bölge seçin.

1. Bölgeleri kaldırmak için, bir veya daha fazla bölgeyi onay işaretleri içeren mavi altıgenler seçerek haritalardan kaldırın. Ya da sağ taraftaki bölgenin yanındaki "wastesepet" (🗑) simgesini seçin.

1. Değişikliklerinizi kaydetmek için **Tamam**' ı seçin.

   ![Bölge Ekle veya Kaldır menüsü](./media/how-to-manage-database-account/add-region.png)

Tek bölgeli yazma modunda, yazma bölgesini kaldıramazsınız. Geçerli yazma bölgesini silebilmeniz için önce farklı bir bölgeye yük devretmek zorundasınız.

Çok bölgeli yazma modunda, en az bir bölgeniz varsa herhangi bir bölge ekleyebilir veya kaldırabilirsiniz.

### <a id="add-remove-regions-via-cli"></a>Azure CLI

Lütfen bkz. [Azure CLI ile bölge ekleme veya kaldırma](manage-with-cli.md#add-or-remove-regions)

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

Lütfen bkz. [PowerShell ile bölge ekleme veya kaldırma](manage-with-powershell.md#update-account)

## <a id="configure-multiple-write-regions"></a>Çoklu yazma bölgelerini yapılandırma

### <a id="configure-multiple-write-regions-portal"></a>Azure portal

**Verileri genel olarak Çoğalt** sekmesini açın ve çok bölgeli yazmaları etkinleştirmek için **Etkinleştir** ' i seçin. Çok bölgeli yazmaları etkinleştirdikten sonra, şu anda hesapta yer alan tüm okuma bölgeleri okuma ve yazma bölgeleri olur.

![Azure Cosmos hesabı, çok yöneticili ekran görüntüsünü yapılandırır](./media/how-to-manage-database-account/single-to-multi-master.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

Lütfen bkz. [Azure CLI ile birden çok yazma bölgesini etkinleştirme](manage-with-cli.md#enable-multiple-write-regions)

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

Lütfen bkz. [PowerShell ile birden çok yazma bölgelerini etkinleştirme](manage-with-powershell.md#multi-master)

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager şablonu

Hesap oluşturmak için kullanılan Kaynak Yöneticisi şablonunu dağıtarak ve `enableMultipleWriteLocations: true`ayarlamak için bir hesap tek ASILDAN çoklu ana 'e geçirilebilir. Aşağıdaki Azure Resource Manager şablonu, iki bölgeyle SQL API için bir Azure Cosmos hesabı ve birden çok yazma konumu etkinleştirilmiş olan en az bir şablondur.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Azure Cosmos hesabınız için otomatik yük devretmeyi etkinleştirin

Otomatik yük devretme seçeneği, bir bölgenin kullanılamaz duruma gelmesi için en yüksek yük devretme önceliğine sahip bölgeye yük devretmesinin Azure Cosmos DB sağlar. Otomatik yük devretme etkinleştirildiğinde bölge önceliği değiştirilebilir. Otomatik yük devretmeyi etkinleştirmek için hesabın iki veya daha fazla bölgesi olmalıdır.

### <a id="enable-automatic-failover-via-portal"></a>Azure portal

1. Azure Cosmos hesabınızdan **verileri genel olarak Çoğalt** bölmesini açın.

2. Bölmenin en üstünde **otomatik yük devretme**' yı seçin.

   ![Verileri genel olarak çoğaltma menüsü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **Otomatik yük devretme** bölmesinde, **otomatik yük devretmeyi etkinleştir** ' in **Açık**olarak ayarlandığından emin olun. 

4. **Kaydet**’i seçin.

   ![Otomatik yük devretme portal menüsü](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

Lütfen bkz. [Azure CLI ile otomatik yük devretmeyi etkinleştirme](manage-with-cli.md#enable-automatic-failover)

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

Lütfen bkz. [PowerShell ile otomatik yük devretmeyi etkinleştirme](manage-with-powershell.md#enable-automatic-failover)

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Azure Cosmos hesabınız için yük devretme önceliklerini ayarlama

Bir Cosmos hesabı otomatik yük devretme için yapılandırıldıktan sonra, bölgeler için yük devretme önceliği değiştirilebilir.

> [!IMPORTANT]
> Hesap otomatik yük devretme için yapılandırıldığında yazma bölgesini (sıfır yük devretme önceliği) değiştiremezsiniz. Yazma bölgesini değiştirmek için otomatik yük devretmeyi devre dışı bırakıp el ile yük devretme yapmanız gerekir.

### <a id="set-failover-priorities-via-portal"></a>Azure portal

1. Azure Cosmos hesabınızdan **verileri genel olarak Çoğalt** bölmesini açın.

2. Bölmenin en üstünde **otomatik yük devretme**' yı seçin.

   ![Verileri genel olarak çoğaltma menüsü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **Otomatik yük devretme** bölmesinde, **otomatik yük devretmeyi etkinleştir** ' in **Açık**olarak ayarlandığından emin olun.

4. Yük devretme önceliğini değiştirmek için, okuma bölgelerini, üzerine geldiğinizde görüntülenen satırın sol tarafındaki üç nokta ile sürükleyin.

5. **Kaydet**’i seçin.

   ![Otomatik yük devretme portal menüsü](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

Lütfen bkz. [Azure CLI ile yük devretme önceliğini ayarlama](manage-with-cli.md#set-failover-priority)

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

Lütfen bkz. [PowerShell ile yük devretme önceliğini ayarlama](manage-with-powershell.md#modify-failover-priority)

## <a id="manual-failover"></a>Azure Cosmos hesabında el ile yük devretme gerçekleştirme

> [!IMPORTANT]
> Bu işlemin başarılı olması için Azure Cosmos hesabının el ile yük devretme için yapılandırılmış olması gerekir.

El ile yük devretme gerçekleştirme süreci, hesabın yazma bölgesinin (yük devretme önceliği = 0) hesap için yapılandırılmış başka bir bölgeye değiştirilmesini gerektirir.

> [!NOTE]
> Çoklu yönetici hesaplara el ile yük devredilemez. Azure Cosmos SDK 'sını kullanan uygulamalar için SDK, bir bölgenin ne zaman kullanılamaz hale geldiğini algılar ve ardından SDK 'da çoklu barındırma API 'SI kullanılıyorsa otomatik olarak sonraki en yakın bölgeye yeniden yönlendirilir.

### <a id="enable-manual-failover-via-portal"></a>Azure portal

1. Azure Cosmos hesabınıza gidin ve **verileri genel olarak Çoğalt** menüsünü açın.

2. Menünün üst kısmında **El Ile yük devretme**' yı seçin.

   ![Verileri genel olarak çoğaltma menüsü](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **El Ile yük devretme** menüsünde Yeni yazma bölgenizi seçin. Bu seçeneği, yazma bölgenizi değiştirdiğinize işaret eden onay kutusunu seçin.

4. Yük devretmeyi tetiklemek için **Tamam**' ı seçin.

   ![El ile yük devretme portal menüsü](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

Lütfen bkz. [Azure CLI ile el ile yük devretmeyi tetikleme](manage-with-cli.md#trigger-manual-failover)

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

Lütfen bkz. [PowerShell ile el ile yük devretmeyi tetikleme](manage-with-powershell.md#trigger-manual-failover)

## <a name="next-steps"></a>Sonraki adımlar

Azure Cosmos hesabının yanı sıra veritabanı ve kapsayıcıları yönetme hakkında daha fazla bilgi ve örnek için aşağıdaki makaleleri okuyun:

* [Azure PowerShell kullanarak Azure Cosmos DB yönetme](manage-with-powershell.md)
* [Azure CLI kullanarak Azure Cosmos DB'yi yönetme](manage-with-cli.md)
