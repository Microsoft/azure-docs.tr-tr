---
title: Dağıtımlarla ilgili sorunları giderme
description: Azure Resource Manager şablonu dağıtımlarını izlemeyi ve sorun gidermeyi öğrenin. Etkinlik günlüklerini ve dağıtım geçmişini gösterir.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: be390b35a69f44aae38bec100deb2d3c07ad6355
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149340"
---
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>Öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme

Kaynak Yöneticisi şablonu dağıtım hatalarını giderme hakkında bilgi edinin. Bu öğreticide, bir şablonda iki hata ayarlarsınız ve sorunları çözmek için etkinlik günlüklerinin ve dağıtım geçmişinin nasıl kullanılacağını öğreneceksiniz.

Şablon dağıtımıyla ilgili iki tür hata vardır:

- Dağıtımdan önce belirlenebileceği senaryolardan **doğrulama hataları** oluşur. Bunlar şablonunuzdaki söz dizimi hataları veya abonelik kotalarınızı aşabilecek kaynak dağıtımı denemeleri olabilir. 
- Dağıtım işlemi sırasında oluşan koşullardan **dağıtım hataları** oluşur. Bu paralel olarak dağıtılan bir kaynağa erişme denemesi olabilir.

Her iki tür hata da dağıtım sorunlarını gidermek için kullanabileceğiniz bir hata kodu döndürür. Etkinlik günlüğünde her iki tür hata da görünür. Öte yandan doğrulama hataları dağıtım geçmişinizde görüntülenmez çünkü dağıtım hiç başlatılmamıştır.

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Sorunlu şablon oluşturma
> * Doğrulama hatalarında sorun giderme
> * Dağıtım hatalarını giderme
> * Kaynakları temizleme

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

- [Resource Manager Araçları uzantısı](https://code.visualstudio.com/) içeren [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="create-a-problematic-template"></a>Sorunlu şablon oluşturma

[Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/) [Standart depolama hesabı oluşturma](https://azure.microsoft.com/resources/templates/101-storage-account-create/) adlı bir şablon açın ve iki şablon sorunu kurun.

1. Visual Studio Code’dan **Dosya**>**Dosya Aç**’ı seçin.
2. **Dosya adı**’na şu URL’yi yapıştırın:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Dosyayı açmak için **Aç**’ı seçin.
4. **Apiversion** satırını şu satıra değiştirin:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** geçersiz öğe adı. Bu bir doğrulama hatasıdır.
    - API sürümü "2018-07-01" olacaktır.  Bu bir dağıtım hatasıdır.

5. Dosyayı yerel bilgisayarınıza **azuredeploy.json** olarak kaydetmek için >Dosya**Farklı Kaydet**’i seçin.

## <a name="troubleshoot-the-validation-error"></a>Doğrulama hatası sorunlarını giderme

Şablonu dağıtmak için [şablonu dağıtma](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) bölümüne bakın.

Kabuktan şuna benzer bir hata alacaksınız:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

Hata iletisi, sorunun **apiVersion1**olduğunu gösterir.

**ApiVersion1** to **apiversion**ile değiştirerek sorunu düzeltmek için Visual Studio Code kullanın ve şablonu kaydedin.

## <a name="troubleshoot-the-deployment-error"></a>Dağıtım hatası sorunlarını giderme

Şablonu dağıtmak için [şablonu dağıtma](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) bölümüne bakın.

Kabuktan şuna benzer bir hata alacaksınız:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Dağıtım hatası, aşağıdaki yordam kullanılarak Azure portal bulunabilir:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Kaynak **grupları** ' nı ve ardından kaynak grubu adı ' nı seçerek kaynak grubunu açın. **Dağıtım**altında **1 başarısız** olduğunu göreceksiniz.

    ![Kaynak Yöneticisi öğretici sorunlarını giderme](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. **Hata ayrıntılarını**seçin.

    ![Kaynak Yöneticisi öğretici sorunlarını giderme](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    Hata iletisi, daha önce gösterilenle aynıdır:

    ![Kaynak Yöneticisi öğretici sorunlarını giderme](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

Ayrıca, etkinlik günlüklerinden hatayı bulabilirsiniz:

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **İzleme** > **etkinlik günlüğü**' nü seçin.
3. Günlüğü bulmak için filtreleri kullanın.

    ![Kaynak Yöneticisi öğretici sorunlarını giderme](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Sorunu düzeltmek için Visual Studio Code kullanın ve ardından şablonu yeniden dağıtın.

Yaygın hataların listesi için bkz. [Azure Resource Manager ile yaygın Azure dağıtım hatalarıyla Ilgili sorunları giderme](./resource-manager-common-deployment-errors.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portalda, sol menüden **Kaynak grubu**’nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.  Kaynak grubundaki toplam altı kaynak görüyor olmalısınız.
4. Üstteki menüden **Kaynak grubunu sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Kaynak Yöneticisi şablonu dağıtım hatalarını giderme hakkında öğrenirsiniz.  Daha fazla bilgi için bkz. [Azure Resource Manager ile yaygın Azure dağıtım hatalarıyla Ilgili sorunları giderme](./resource-manager-common-deployment-errors.md).
