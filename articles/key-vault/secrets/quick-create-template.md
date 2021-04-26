---
title: Azure hızlı başlangıç-Azure Resource Manager şablonu kullanarak bir Azure Anahtar Kasası ve gizli dizi oluşturma | Microsoft Docs
description: Azure Anahtar kasaları oluşturmayı ve Azure Resource Manager şablonu kullanarak kasalarına gizli dizileri eklemeyi gösteren hızlı başlangıç.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 214c86eb7272c87e067b1d5f6df0b09ce9e7095c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814145"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>Hızlı başlangıç: ARM şablonunu kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma

[Azure Key Vault](../general/overview.md) , anahtarlar, parolalar, sertifikalar ve diğer gizli diziler gibi gizli dizileri için güvenli bir depo sağlayan bir bulut hizmetidir. Bu hızlı başlangıç, bir anahtar kasası ve gizli dizi oluşturmak için bir Azure Resource Manager şablonu dağıtma işlemine odaklanır (ARM şablonu).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ortamınız önkoşulları karşılıyorsa ve ARM şablonlarını kullanma hakkında bilginiz varsa, **Azure’a dağıtma** düğmesini seçin. Şablon Azure portalda açılır.

[![Azure’a dağıtma](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Ön koşullar

Bu makaleyi gerçekleştirmek için:

* Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

* Şablonda izinlerin yapılandırılması için Azure AD kullanıcı nesnesi kimliğiniz gerekir. Aşağıdaki yordam nesne KIMLIĞINI (GUID) alır.

    1. Aşağıdaki Azure PowerShell veya Azure CLı komutunu çalıştırarak **deneyin**' i seçin ve ardından betiği kabuk bölmesine yapıştırın. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından **Yapıştır**' ı seçin.

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Nesne KIMLIĞINI yazın. Bu hızlı başlangıç bölümünün sonraki bölümünde olması gerekir.

## <a name="review-the-template"></a>Şablonu gözden geçirme

Bu hızlı başlangıçta kullanılan şablon [Azure Hızlı Başlangıç Şablonlarından](https://azure.microsoft.com/resources/templates/101-key-vault-create/) alınmıştır.

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

Şablonda iki Azure kaynağı tanımlanmıştır:

* [**Microsoft. keykasası/Vaults**](/azure/templates/microsoft.keyvault/vaults): bir Azure Anahtar Kasası oluşturun.
* [**Microsoft. Keykasası/kaults/gizlilikler**](/azure/templates/microsoft.keyvault/vaults/secrets): Anahtar Kasası gizli anahtarı oluşturma.

Daha fazla Azure Key Vault şablon örneği, [Azure hızlı başlangıç şablonlarında](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)bulunabilir.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon bir anahtar kasası ve gizli dizi oluşturur.

    [![Azure’a dağıtma](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Aşağıdaki değerleri seçin veya girin.

    ![ARM şablonu, Key Vault tümleştirme, Portal dağıtma](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Belirtilmediği takdirde, anahtar kasasını ve bir parolayı oluşturmak için varsayılan değeri kullanın.

    * **Abonelik**: Bir Azure aboneliği seçin.
    * **Kaynak grubu**: **Yeni oluştur**' u seçin, kaynak grubu için benzersiz bir ad girin ve ardından **Tamam**' a tıklayın.
    * **Konum**: Bir konum seçin. Örneğin **Orta ABD**.
    * **Key Vault adı**: Anahtar Kasası için,. Vault.Azure.net ad alanı içinde genel olarak benzersiz olması gereken bir ad girin. Dağıtımı doğruladığınızda, sonraki bölümde adı kullanmanız gerekir.
    * **Kiracı kimliği**: şablon IşLEVI kiracı kimliğinizi otomatik olarak alır. Varsayılan değeri değiştirmeyin.
    * **Ad Kullanıcı kimliği**: [ön koşullardan](#prerequisites)aldığınız Azure AD Kullanıcı nesne kimliğinizi girin.
    * **Gizli dizi adı**: anahtar kasasında depoladığınız gizli dizi için bir ad girin. Örneğin, **AdminPassword**.
    * **Gizli dizi değeri**: gizli değeri girin. Bir parolayı depolukarşılaşırsanız, ön koşullarda oluşturduğunuz oluşturulan parolayı kullanmanız önerilir.
    * **Yukarıda belirtilen hüküm ve koşulları kabul ediyorum**: Bunu seçin.
3. **Satın al**'ı seçin. Anahtar Kasası başarıyla dağıtıldıktan sonra bir bildirim alırsınız:

    ![ARM şablonu, Key Vault tümleştirme, Portal dağıtma bildirimi](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Şablonu dağıtmak için Azure portalı kullanılır. Azure portal ek olarak, Azure PowerShell, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Dağıtılan kaynakları gözden geçirme

Anahtar kasasını ve parolayı denetlemek için Azure portal kullanabilir ya da oluşturulan gizli anahtarı listelemek için aşağıdaki Azure CLı veya Azure PowerShell betiğini kullanabilirsiniz.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

Çıktı şuna benzer:

# <a name="cli"></a>[CLI](#tab/CLI)

![CLı 'de Portal doğrulama çıkışını Dağıt ' ı gösteren ekran görüntüsü.](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![ARM şablonu, Key Vault tümleştirme, Portal doğrulama çıkışını dağıtma](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Key Vault hızlı başlangıçları ve öğreticileri bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıç ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakmanız yararlı olabilir.
Artık gerek kalmadığında kaynak grubunu silin; bunu yaptığınızda Key Vault ve ilgili kaynaklar silinir. Azure CLı veya Azure PowerShell kullanarak kaynak grubunu silmek için:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir ARM şablonu kullanarak bir anahtar kasası ve gizli dizi oluşturdunuz ve dağıtımı doğruladı. Key Vault ve Azure Resource Manager hakkında daha fazla bilgi edinmek için aşağıdaki makalelere devam edin.

- [Azure Key Vault genel bakışını](../general/overview.md) okuyun
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md) hakkında daha fazla bilgi edinin
- [Key Vault güvenliğine genel bakış](../general/security-features.md) konusunu gözden geçirin
