---
title: 'Hızlı başlangıç: Azure PowerShell/Azure CLı (Önizleme) kullanarak Azure purview hesabı oluşturma'
description: Bu hızlı başlangıçta Azure PowerShell/Azure CLı kullanarak Azure purview hesabının nasıl oluşturulacağı açıklanmaktadır.
author: hophanms
ms.author: hophan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-api
ms.openlocfilehash: 6266aedaec8f171a1a6ff3e0d15abdad0263767a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530880"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Hızlı başlangıç: Azure PowerShell/Azure CLı kullanarak Azure purview hesabı oluşturma

> [!IMPORTANT]
> Azure takip görünümü şu anda ÖNIZLEME aşamasındadır. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) , Beta, önizleme veya başka bir şekilde genel kullanıma sunulmayan Azure özelliklerine uygulanan ek yasal koşulları içerir.

Bu hızlı başlangıçta Azure PowerShell/Azure CLı kullanarak bir Azure purview hesabı oluşturacaksınız.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Azure 'da oturum açmak için kullandığınız kullanıcı hesabı, katkıda bulunan veya sahip rolünün bir üyesi ya da Azure aboneliğinin Yöneticisi olmalıdır.

* Kendi [Azure Active Directory kiracınız](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Şablonu dağıtmak için istemci makinenize Azure PowerShell ya da Azure CLı 'yı yüklemek: [komut satırı dağıtımı](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla [Azure portalında](https://portal.azure.com) oturum açın.

## <a name="configure-your-subscription"></a>Aboneliğinizi yapılandırma

Gerekirse, aboneliğinizi Azure purview 'ın aboneliğinizde çalışacak şekilde yapılandırmak için aşağıdaki adımları izleyin:

   1. Azure portal, **abonelikleri** arayıp seçin.

   1. Abonelikler listesinden, kullanmak istediğiniz aboneliği seçin. Abonelik için yönetici erişim izni gerekiyor.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Azure portal aboneliğin nasıl seçileceğini gösteren ekran görüntüsü.":::

   1. Aboneliğiniz için **kaynak sağlayıcıları**' nı seçin. **Kaynak sağlayıcıları** bölmesinde, üç kaynak sağlayıcısı için arama yapın ve kaydedin: 
       1. **Microsoft. purview**
       1. **Microsoft.Storage**
       1. **Microsoft. EventHub** 
      
      Bunlar kayıtlı değilse **Kaydet**' i seçerek kaydolun.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Microsoft nokta Azure purview kaynak sağlayıcısının Azure portal nasıl kaydedeceğinizi gösteren ekran görüntüsü.":::

## <a name="create-an-azure-purview-account-instance"></a>Azure purview hesap örneği oluşturma

1. Azure kimlik bilgileriniz ile oturum açın

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Birden çok Azure aboneliğiniz varsa, kullanmak istediğiniz aboneliği seçin:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Purview hesabınız için bir kaynak grubu oluşturun. Zaten bir tane varsa, bu adımı atlayabilirsiniz:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Gibi bir takip görünümü şablon dosyası oluşturun `purviewtemplate.json` . `name`, `location` , Ve `capacity` ( `4` veya `16` ) güncelleştirebilirsiniz:

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Purview şablonu dağıtma

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Bu dağıtım komutunu çalıştırmak için Azure CLI’nın [en son sürümüne](/cli/azure/install-azure-cli) sahip olmanız gerekir.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. Dağıtım komutu sonuçları döndürür. `ProvisioningState`Dağıtımın başarılı olup olmadığını görmek için bölümüne bakın.
    
## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Azure purview hesabı oluşturmayı öğrendiniz.

Kullanıcıların Azure purview hesabınıza erişmesine nasıl izin vereceğinizi öğrenmek için sonraki makaleye ilerleyin. 

> [!div class="nextstepaction"]
> [Azure purview hesabınıza kullanıcı ekleme](catalog-permissions.md)
