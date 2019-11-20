---
title: Azure hızlı başlangıç-Azure Resource Manager şablonu kullanarak bir Azure Anahtar Kasası ve gizli dizi oluşturma | Microsoft Docs
description: Azure Anahtar kasaları oluşturmayı ve Azure Resource Manager şablonu kullanarak kasalarına gizli dizileri eklemeyi gösteren hızlı başlangıç.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/17/2019
ms.author: jgao
ms.openlocfilehash: 1e993ec50694d8672fbe05f982d5dbc45a815abb
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71161468"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-resource-manager-template"></a>Hızlı Başlangıç: Kaynak Yöneticisi şablonu kullanarak Azure Key Vault bir gizli dizi ayarlama ve alma

[Azure Key Vault](./key-vault-overview.md) , anahtarlar, parolalar, sertifikalar ve diğer gizli diziler gibi gizli dizileri için güvenli bir depo sağlayan bir bulut hizmetidir. Bu hızlı başlangıç, bir anahtar kasası ve gizli dizi oluşturmak için bir Kaynak Yöneticisi şablonu dağıtma işlemine odaklanmaktadır.

[Kaynak Yöneticisi Şablon](../azure-resource-manager/template-deployment-overview.md) , projeniz için altyapıyı ve yapılandırmayı tanımlayan bir JAVASCRIPT nesne GÖSTERIMI (JSON) dosyasıdır. Şablon bildirim temelli sözdizimini kullanır, bu, oluşturmak için programlama komutlarının dizisini yazmak zorunda kalmadan ne dağıtmayı istediğinizi belirtmenize olanak tanır. Kaynak Yöneticisi şablonları geliştirme hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [Kaynak Yöneticisi belgeleri](/azure/azure-resource-manager/) ve [şablon başvurusu](/azure/templates/microsoft.keyvault/allversions).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* Şablonda izinlerin yapılandırılması için Azure AD kullanıcı nesnesi kimliğiniz gerekir. Aşağıdaki yordam nesne KIMLIĞINI (GUID) alır.

    1. Aşağıdaki Azure PowerShell veya Azure CLı komutunu çalıştırarak **deneyin**' i seçin ve ardından betiği kabuk bölmesine yapıştırın. Betiği yapıştırmak için, kabuğa sağ tıklayın ve ardından **Yapıştır**' ı seçin.

        # <a name="clitabcli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Nesne KIMLIĞINI yazın. Bu hızlı başlangıç bölümünün sonraki bölümünde olması gerekir.

## <a name="create-a-vault-and-a-secret"></a>Kasa ve gizli dizi oluşturma

Bu hızlı başlangıçta kullanılan şablon [Azure hızlı başlangıç şablonlarından](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

[!code-json[<Azure Resource Manager template create key vault>](~/quickstart-templates/101-key-vault-create/azuredeploy.json)]

Şablonda iki Azure kaynağı tanımlanmıştır:

* **Microsoft. keykasası/Vaults**: bir Azure Anahtar Kasası oluşturun.
* **Microsoft. Keykasası/kaults/gizlilikler**: Anahtar Kasası gizli anahtarı oluşturma.

Daha fazla Azure Key Vault şablon örneği [burada](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)bulunabilir.

1. Aşağıdaki görüntüyü seçerek Azure'da oturum açıp bir şablon açın. Şablon bir anahtar kasası ve gizli dizi oluşturur.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Aşağıdaki değerleri seçin veya girin.

    ![Kaynak Yöneticisi şablonu, Key Vault tümleştirme, Portal dağıtma](./media/quick-create-template/create-key-vault-using-template-portal.png)

    Belirtilmediği takdirde, anahtar kasasını ve bir parolayı oluşturmak için varsayılan değeri kullanın.

    * **Abonelik**: Bir Azure aboneliği seçin.
    * **Kaynak grubu**: **Yeni oluştur**' u seçin, kaynak grubu için benzersiz bir ad girin ve ardından **Tamam**' a tıklayın.
    * **Konum**: Bir konum seçin.  Örneğin, **Orta ABD**.
    * **Key Vault adı**: Anahtar Kasası için,. Vault.Azure.net ad alanı içinde genel olarak benzersiz olması gereken bir ad girin. Dağıtımı doğruladığınızda, sonraki bölümde adı kullanmanız gerekir.
    * **Kiracı kimliği**: şablon işlevi kiracı kimliğinizi otomatik olarak alır.  Varsayılan değeri değiştirmeyin.
    * **Ad Kullanıcı kimliği**: [ön koşullardan](#prerequisites)aldığınız Azure AD Kullanıcı nesne kimliğinizi girin.
    * **Gizli dizi adı**: anahtar kasasında depoladığınız gizli dizi için bir ad girin.  Örneğin, **AdminPassword**.
    * **Gizli dizi değeri**: gizli değeri girin.  Bir parola depolukarşılaşırsanız, Önkoşullarda oluşturduğunuz oluşturulan parolanın kullanılması önerilir.
    * **Yukarıdaki hüküm ve koşullar durumunu kabul ediyorum**: Seçin.
3. **Satın al**'ı seçin. Anahtar Kasası başarıyla dağıtıldıktan sonra bir bildirim alırsınız:

    ![Kaynak Yöneticisi şablonu, Key Vault tümleştirme, Portal dağıtma bildirimi](./media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure portal, şablonu dağıtmak için kullanılır. Azure portal ek olarak, Azure PowerShell, Azure CLı ve REST API de kullanabilirsiniz. Diğer dağıtım yöntemlerini öğrenmek için bkz. [şablonları dağıtma](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="validate-the-deployment"></a>Dağıtımı doğrulama

Anahtar kasasını ve parolayı denetlemek için Azure portal kullanabilir ya da oluşturulan gizli anahtarı listelemek için aşağıdaki Azure CLı veya Azure PowerShell betiğini kullanabilirsiniz.

# <a name="clitabcli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

Çıktı şuna benzer:

# <a name="clitabcli"></a>[CLI](#tab/CLI)

![Kaynak Yöneticisi şablonu, Key Vault tümleştirme, Portal doğrulama çıkışını dağıtma](./media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

![Kaynak Yöneticisi şablonu, Key Vault tümleştirme, Portal doğrulama çıkışını dağıtma](./media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---
## <a name="clean-up-resources"></a>Kaynakları temizleme

Diğer Key Vault hızlı başlangıçları ve öğreticileri bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıç ve öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu kaynakları yerinde bırakmanız yararlı olabilir.
Artık gerek kalmadığında kaynak grubunu silin; bunu yaptığınızda Key Vault ve ilgili kaynaklar silinir. Azure CLı veya Azure PowerShell kullanarak kaynak grubunu silmek için:

# <a name="clitabcli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir Azure Resource Manager şablonu kullanarak bir anahtar kasası ve gizli dizi oluşturdunuz ve dağıtımı doğruladı. Key Vault ve Azure Resource Manager hakkında daha fazla bilgi edinmek için aşağıdaki makalelere devam edin.

- [Azure Key Vault genel bakışını](key-vault-overview.md) okuyun
- [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) hakkında daha fazla bilgi edinin
- [Anahtarlar, gizlilikler ve sertifikalar](about-keys-secrets-and-certificates.md) hakkında daha fazla bilgi alın
- [En iyi uygulamaları](key-vault-best-practices.md) gözden geçirin Azure Key Vault