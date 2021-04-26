---
title: Şablon dağıtım betikleri kullanma | Microsoft Docs
description: Azure Resource Manager şablonlarda (ARM şablonları) dağıtım betikleri kullanmayı öğrenin.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/16/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 36fb54b4b6521d87c7461936c84a644bf22f7e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963972"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate"></a>Öğretici: otomatik olarak imzalanan sertifika oluşturmak için dağıtım betikleri kullanın

Azure Resource Manager şablonlarda (ARM şablonları) dağıtım betikleri kullanmayı öğrenin. Dağıtım betikleri, ARM şablonları tarafından yapılamadığını özel adımlar gerçekleştirmek için kullanılabilir. Örneğin, otomatik olarak imzalanan bir sertifika oluşturma. Bu öğreticide, bir Azure Anahtar Kasası dağıtmak için bir şablon oluşturup, bir `Microsoft.Resources/deploymentScripts` sertifika oluşturmak ve ardından sertifikayı anahtar kasasına eklemek için aynı şablonda bir kaynak kullanırsınız. Dağıtım betiği hakkında daha fazla bilgi edinmek için bkz. [ARM şablonlarındaki Dağıtım betiklerini kullanma](./deployment-script-template.md).

> [!IMPORTANT]
> Bir depolama hesabı ve bir kapsayıcı örneği olmak üzere iki dağıtım betiği kaynağı, betik yürütme ve sorun giderme için aynı kaynak grubunda oluşturulur. Bu kaynaklar genellikle betik yürütme bir terminal durumunda olduğunda betik hizmeti tarafından silinir. Kaynaklar silinene kadar kaynaklar için faturalandırılırsınız. Daha fazla bilgi için bkz. [dağıtım betiği kaynaklarını Temizleme](./deployment-script-template.md#clean-up-deployment-script-resources).

Bu öğretici aşağıdaki görevleri kapsar:

> [!div class="checklist"]
> * Hızlı başlangıç şablonunu açma
> * Şablonu düzenleme
> * Şablonu dağıtma
> * Başarısız komut dosyasında hata ayıkla
> * Kaynakları temizleme

Dağıtım betiklerini içeren bir Microsoft Learn modülü için bkz. [Dağıtım betiklerini kullanarak ARM şablonlarını genişletme](/learn/modules/extend-resource-manager-template-deployment-scripts/).

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlamak için gerekenler:

* **Kaynak Yöneticisi Araçları uzantısıyla [Visual Studio Code](https://code.visualstudio.com/)**. Bkz. [hızlı başlangıç: VISUAL STUDIO Code ARM şablonları oluşturma](./quickstart-create-templates-use-visual-studio-code.md).

* **Kullanıcı tarafından atanan yönetilen kimlik**. Bu kimlik, betikte Azure 'a özgü eylemler gerçekleştirmek için kullanılır. Bir tane oluşturmak için bkz. [Kullanıcı tarafından atanan yönetilen kimlik](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Şablonu dağıtırken kimlik KIMLIĞININ olması gerekir. Kimliğin biçimi:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Kaynak grubu adını ve kimlik adını sağlayarak KIMLIĞI almak için aşağıdaki CLı betiğini kullanın.

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  az identity list -g $resourceGroupName
  ```

## <a name="open-a-quickstart-template"></a>Hızlı başlangıç şablonunu açma

Sıfırdan şablon oluşturmak yerine, [Azure Hızlı Başlangıç Şablonları](https://azure.microsoft.com/resources/templates/)’ndan bir şablon açarsınız. Azure hızlı başlangıç şablonları, ARM şablonları için bir depodur.

Bu hızlı başlangıçta kullanılan şablona [Azure Key Vault oluştur ve gizli anahtar](https://azure.microsoft.com/resources/templates/101-key-vault-create/)adı verilir. Şablon bir Anahtar Kasası oluşturur ve sonra anahtar kasasına bir gizli dizi ekler.

1. Visual Studio Code **Dosya**  >  **Aç dosya**' yı seçin.
2. **Dosya adı**’na şu URL’yi yapıştırın:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Dosyayı açmak için **Aç**’ı seçin.
4. Dosyayı   >  yerel bilgisayarınızaazuredeploy.jsolarak kaydetmek için dosya **farklı kaydet** _'_ i seçin.

## <a name="edit-the-template"></a>Şablonu düzenleme

Şablonda aşağıdaki değişiklikleri yapın:

### <a name="clean-up-the-template-optional"></a>Şablonu Temizleme (isteğe bağlı)

Özgün şablon, anahtar kasasına bir gizli dizi ekler. Öğreticiyi basitleştirmek için aşağıdaki kaynağı kaldırın:

* `Microsoft.KeyVault/vaults/secrets`

Aşağıdaki iki parametre tanımını kaldırın:

* `secretName`
* `secretValue`

Bu tanımları kaldırmayı tercih ederseniz, dağıtım sırasında parametre değerlerini belirtmeniz gerekir.

### <a name="configure-the-key-vault-access-policies"></a>Anahtar Kasası erişim ilkelerini yapılandırma

Dağıtım betiği, anahtar kasasına bir sertifika ekler. Yönetilen kimliğe izin vermek için Anahtar Kasası erişim ilkelerini yapılandırın:

1. Yönetilen kimlik KIMLIĞINI almak için bir parametre ekleyin:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Visual Studio Code Kaynak Yöneticisi şablonu uzantısı henüz dağıtım betikleri biçimlendirme yeteneğine sahip değil. Aşağıdaki gibi, kaynakları biçimlendirmek için SHIFT + alt + F kullanmayın `deploymentScripts` .

1. Yönetilen kimliğin anahtar kasasına sertifika ekleyebilmesi için Anahtar Kasası erişim ilkelerini yapılandırmak için bir parametre ekleyin:

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Mevcut Anahtar Kasası erişim ilkelerini şu şekilde güncelleştirin:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Biri oturum açmış kullanıcı ve diğeri yönetilen kimlik için olmak üzere iki ilke tanımlanmış. Oturum açmış kullanıcı yalnızca dağıtımı doğrulamak için *liste* iznine ihtiyaç duyuyor. Öğreticiyi basitleştirmek için, aynı sertifika hem yönetilen kimliğe hem de oturum açmış kullanıcılara atanır.

### <a name="add-the-deployment-script"></a>Dağıtım betiğini ekleyin

1. Dağıtım betiği tarafından kullanılan üç parametre ekleyin:

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Kaynak ekleme `deploymentScripts` :

    > [!NOTE]
    > Satır içi dağıtım betikleri çift tırnak içine alındığından, dağıtım betiklerinin içindeki dizelerin tek tırnak içine alınması gerekir. [PowerShell kaçış karakteri](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#single-and-double-quoted-strings) geri değer () ' dir `` ` `` .

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2020-10-01",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    `deploymentScripts`Kaynak, Anahtar Kasası kaynağına ve rol atama kaynağına bağlıdır. Şu özelliklere sahiptir:

    * `identity`: Dağıtım betiği, komut dosyasındaki işlemleri gerçekleştirmek için Kullanıcı tarafından atanan bir yönetilen kimlik kullanır.
    * `kind`: Betiğin türünü belirtin. Şu anda yalnızca PowerShell betikleri desteklenir.
    * `forceUpdateTag`: Betik kaynağı değiştirilmese de dağıtım betiğinin yürütülüp yürütülmeyeceğini belirleme. Geçerli zaman damgası veya bir GUID olabilir. Daha fazla bilgi için bkz. [betiği birden çok kez çalıştırma](./deployment-script-template.md#run-script-more-than-once).
    * `azPowerShellVersion`: Kullanılacak Azure PowerShell modülü sürümünü belirtir. Dağıtım betiği Şu anda 2.7.0, 2.8.0 ve 3.0.0 sürümünü destekler.
    * `timeout`: [Iso 8601 biçiminde](https://en.wikipedia.org/wiki/ISO_8601)belirtilen izin verilen en fazla betik yürütme süresini belirtin. Varsayılan değer **P1D**' dir.
    * `arguments`: Parametre değerlerini belirtin. Değerler boşluklarla ayrılır.
    * `scriptContent`: Betik içeriğini belirtin. Bir dış betik çalıştırmak için `primaryScriptURI` bunun yerine kullanın. Daha fazla bilgi için bkz. [dış betik kullanma](./deployment-script-template.md#use-external-scripts).
        Bildirim `$DeploymentScriptOutputs` yalnızca yerel bir makinede betiği test edilirken gereklidir. Değişkeni bildirmek, betiğin değişiklik yapmak zorunda kalmadan yerel bir makinede ve bir kaynakta çalıştırılmasını sağlar `deploymentScript` . Öğesine atanan değer, `$DeploymentScriptOutputs` dağıtımlarda çıkış olarak kullanılabilir. Daha fazla bilgi için bkz. [PowerShell dağıtım betiklerinden çıkışlarla çalışma](./deployment-script-template.md#work-with-outputs-from-powershell-script) veya [CLI dağıtım betiklerinden çıkışlarla çalışma](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * `cleanupPreference`: Dağıtım betiği kaynaklarının ne zaman silineceğini belirtin. Varsayılan değer **her zaman** ve dağıtım betiği kaynaklarının, Terminal durumuna (başarılı, başarısız, iptal edildi) rağmen silindiği anlamına gelir. Bu öğreticide, komut dosyası yürütme sonuçlarını görüntülemenin bir şansı elde etmeniz için **onSuccess** kullanılır.
    * `retentionInterval`: Bir Terminal durumuna ulaştıktan sonra hizmetin betik kaynaklarını koruduğunu belirten aralığı belirtin. Bu süre sona erdiğinde kaynaklar silinir. Süre, ISO 8601 düzenine göre belirlenir. Bu öğretici, bir gün anlamına gelen **P1D** kullanır. Bu özellik `cleanupPreference` **onexpiration** olarak ayarlandığında kullanılır. Bu özellik şu anda etkin değil.

    Dağıtım betiği üç parametre alır: `keyVaultName` , `certificateName` ve `subjectName` . Bir sertifika oluşturur ve ardından sertifikayı anahtar kasasına ekler.

    `$DeploymentScriptOutputs` çıkış değerini depolamak için kullanılır. Daha fazla bilgi edinmek için bkz. [PowerShell dağıtım betiklerinden çıkışlarla çalışma](./deployment-script-template.md#work-with-outputs-from-powershell-script) veya [CLI dağıtım betiklerinden çıkışlarla çalışma](./deployment-script-template.md#work-with-outputs-from-cli-script).

    Tamamlanan şablon [burada](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)bulunabilir.

1. Hata ayıklama işlemini görmek için dağıtım betiğine aşağıdaki satırı ekleyerek koda bir hata koyun:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    Doğru komut `Write-Output` yerine `Write-Output1` .

1. Dosyayı   >  kaydetmek için dosya **Kaydet** ' i seçin.

## <a name="deploy-the-template"></a>Şablonu dağıtma

1. [Azure Cloud Shell](https://shell.azure.com) oturum açın

1. Sol üst köşedeki **PowerShell** veya **Bash** (CLI için) öğesini seçerek tercih ettiğiniz ortamı seçin. Geçiş yaptığınızda kabuğun yeniden başlatılması gerekir.

    ![Dosyayı karşıya yükleme Cloud Shell Azure portal](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **Dosyaları karşıya yükle/indir**'i seçin ve sonra da **Karşıya Yükle**'yi seçin. Önceki ekran görüntüsüne bakın.  Önceki bölümde kaydettiğiniz dosyayı seçin. Dosyayı karşıya yükledikten sonra, `ls` `cat` dosyanın başarıyla karşıya yüklendiğini doğrulamak için komutunu ve komutunu kullanabilirsiniz.

1. Şablonu dağıtmak için aşağıdaki PowerShell betiğini çalıştırın.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
    $identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

    $adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
    $resourceGroupName = "${projectName}rg"
    $keyVaultName = "${projectName}kv"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

    Write-Host "Press [ENTER] to continue ..."
    ```

    Dağıtım betiği hizmetinin betik yürütme için ek dağıtım betiği kaynakları oluşturması gerekir. Hazırlama ve Temizleme işleminin tamamlanması, gerçek betik yürütme süresine ek olarak bir dakika kadar sürebilir.

    Komut dosyasında geçersiz komut kullanıldığından dağıtım başarısız oldu `Write-Output1` . Şunu söyleyen bir hata alacaksınız:

    ```error
    The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
    program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
    ```

    Dağıtım betiği yürütme sonucu, sorun giderme amacıyla dağıtım betiği kaynaklarında depolanır.

## <a name="debug-the-failed-script"></a>Başarısız komut dosyasında hata ayıkla

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Kaynak grubunu açın. **RG** eklenmiş olan proje adı. Kaynak grubunda iki ek kaynak görürsünüz. Bu kaynaklara *dağıtım betiği kaynakları* denir.

    ![Kaynak Yöneticisi şablonu dağıtım betiği kaynakları](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Her iki dosya da _azscripts_ sonekine sahiptir. Biri bir depolama hesabıdır ve diğeri bir kapsayıcı örneğidir.

    Kaynağı listelemek için **gizli türleri göster** ' i seçin `deploymentScripts` .

1. _Azscripts_ sonekine sahip depolama hesabını seçin.
1. **Dosya paylaşımları** kutucuğunu seçin. Dağıtım betiği yürütme dosyalarını içeren bir _azscripts_ klasörü görürsünüz.
1. _Azscripts_ seçin. İki klasör _azscriptınput_ ve _azscriptoutput_ görürsünüz. Giriş klasörü bir sistem PowerShell betik dosyası ve kullanıcı dağıtımı komut dosyalarını içerir. Çıkış klasörü, üzerinde bir _executionresult.js_ ve betik çıkış dosyası içerir. Hata iletisini _üzerindeexecutionresult.js_ görebilirsiniz. Yürütme başarısız olduğundan çıkış dosyası yok.

Satırı kaldırın `Write-Output1` ve şablonu yeniden dağıtın.

İkinci Dağıtım başarıyla çalıştırıldığında, `cleanupPreference` özellik **onSuccess** olarak ayarlandığından dağıtım betiği kaynakları betik hizmeti tarafından kaldırılır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portal, sol menüden **kaynak grubu** ' nu seçin.
2. **Ada göre filtrele** alanına kaynak grubu adını girin.
3. Kaynak grubu adını seçin.  Kaynak grubunda toplam altı kaynak görürsünüz.
4. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, ARM şablonlarında bir dağıtım betiğini kullanmayı öğrendiniz. Azure kaynaklarını koşullara bağlı olarak dağıtmayı öğrenin:

> [!div class="nextstepaction"]
> [Koşulları kullanma](./template-tutorial-use-conditions.md)
