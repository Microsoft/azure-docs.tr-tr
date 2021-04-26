---
title: Kuruluşunuz dışında paylaşma (ARM şablonu)-Azure veri paylaşımının hızlı başlangıç
description: Bu hızlı başlangıçta Azure veri paylaşımından ve bir Azure Resource Manager şablonu (ARM şablonu) kullanarak müşterilerle ve iş ortaklarıyla veri paylaşmayı öğrenin.
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/19/2020
ms.openlocfilehash: 5abe92120c8b822ac86ced90658869a0858d4ff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487696"
---
# <a name="quickstart-share-data-using-azure-data-share-and-arm-template"></a>Hızlı başlangıç: Azure veri paylaşma ve ARM şablonunu kullanarak veri paylaşma

Bir Azure depolama hesabından bir Azure Resource Manager şablonu (ARM şablonu) kullanarak yeni bir Azure veri paylaşımının nasıl ayarlanacağını öğrenin. Ve Azure kuruluşunuzun dışındaki müşteriler ve iş ortakları ile verilerinizi paylaşmaya başlayın. Desteklenen veri depolarının bir listesi için bkz. [Azure veri paylaşımında desteklenen veri depoları](./supported-data-stores.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

Aşağıdaki kaynaklar şablonda tanımlanmıştır:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts):
* [Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft. DataShare/hesapları](/azure/templates/microsoft.datashare/accounts)
* [Microsoft. DataShare/hesaplar/paylaşımlar](/azure/templates/microsoft.datashare/accounts/shares)
* [Microsoft. Storage/storageAccounts/Providers/Roleatamaları](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft. DataShare/hesaplar/paylaşımlar/veri kümeleri](/azure/templates/microsoft.datashare/accounts/shares/datasets)
* [Microsoft. DataShare/hesaplar/paylaşımlar/davetler](/azure/templates/microsoft.datashare/accounts/shares/invitations)
* [Microsoft. DataShare/hesaplar/paylaşımlar/synchronizationSettings](/azure/templates/microsoft.datashare/accounts/shares/synchronizationsettings)

Şablon aşağıdaki görevleri gerçekleştirir:

* Veri paylaşma veri kaynağı olarak kullanılan bir depolama hesabı ve kapsayıcı oluşturun.
* Veri paylaşma hesabı ve veri paylaşma oluşturun.
* Kaynak veri paylaşma kaynağına Depolama Blobu veri okuyucusu rolünü vermek için bir rol ataması oluşturun. Bkz. [Azure veri paylaşımıyla Ilgili roller ve gereksinimler](./concepts-roles-permissions.md).
* Tarih paylaşımında bir veri kümesi ekleyin.
* Veri paylaşımında alıcı ekleyin.
* Veri paylaşımında bir anlık görüntü zamanlaması etkinleştirin.

Bu şablon, öğrenme amacıyla oluşturulur. Uygulamada, genellikle var olan bir depolama hesabında bazı verileriniz olur. Veri kümesini oluşturmak için bir şablon veya komut dosyası çalıştırmadan önce rol atamasını oluşturmanız gerekir. Bazen, şablonu dağıtırken aşağıdaki hata iletisini alabilirsiniz:

```plaintext
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

Bunun nedeni, dağıtımın Azure rolü atamasının sonlandırılmadan önce veri kümesini oluşturmaya çalışıyor olmasından kaynaklanır. Hata iletisine rağmen dağıtım başarılı olabilir. [Dağıtılan kaynakları gözden geçirmeyi](#review-deployed-resources)hala öğreneceksiniz.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Azure 'da oturum açmak ve şablonu açmak için aşağıdaki görüntüyü seçin.

    [![Azure’a dağıtın](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. Aşağıdaki değerleri seçin veya girin:

    * **Abonelik**: veri paylaşımının ve diğer kaynakların oluşturulması için kullanılan bir Azure aboneliğini seçin.
    * **Kaynak grubu**: yeni bir kaynak grubu oluşturmak Için **Yeni oluştur** ' u seçin veya var olan bir kaynak grubunu seçin.
    * **Konum**: kaynak grubu için bir konum seçin.
    * **Proje adı**: bir proje adı girin.  Proje adı, kaynak adları oluşturmak için kullanılır.  Önceki şablondaki değişken tanımlarına bakın.
    * **konum**: kaynaklar için bir konum seçin.  Kaynak grubu için aynı konumu kullanabilirsiniz.
    * **Davet e-postası**: veri paylaşımında alıcının Azure oturum açma e-posta adresini girin.  E-posta diğer adı çalışmıyor.

    Geri kalan ayarlar için varsayılan değeri kullanın.
1. **yukarıda belirtilen hüküm ve koşulları** ve **satın alma** seçimini kabul ediyorum ' u seçin.

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Oluşturduğunuz veri paylaşma hesabını açın.
1. Sol menüden **paylaşımları gönder**' i seçin.  Listelenen depolama hesabı görüntülenir.
1. Depolama hesabını seçin.  **Ayrıntılar**' ın altında, şablonda yapılandırdığınız eşitleme ayarını görürsünüz.

    ![Azure veri paylaşma depolama hesabı eşitleme ayarları](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. Üstten **davetleri** seçin. Şablonu dağıtırken belirttiğiniz e-posta adresini görürsünüz. **Durum** **Beklemede** olacaktır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse kaynak grubundaki kaynakları silen kaynak grubunu silin.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure veri paylaşımının nasıl oluşturulacağını ve alıcıların nasıl davet alınacağını öğrenirsiniz. Bir veri tüketicisinin bir veri paylaşımının nasıl kabul edileceği ve alabileceği hakkında daha fazla bilgi edinmek için, [verileri kabul etme ve alma](subscribe-to-data-share.md) öğreticisine geçin.
