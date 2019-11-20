---
title: Azure Service Fabric kümesi oluşturma | Microsoft Docs
description: Azure 'da Azure Resource Manager kullanarak güvenli bir Service Fabric kümesi ayarlamayı öğrenin.  Varsayılan bir şablon kullanarak veya kendi küme şablonunuzu kullanarak bir küme oluşturabilirsiniz.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: atsenthi
ms.openlocfilehash: 241349724929845afa2fd2a4bacabf9b5017cc7c
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901560"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Azure Resource Manager kullanarak Service Fabric kümesi oluşturma 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure portalda](service-fabric-cluster-creation-via-portal.md)
>
>

[Azure Service Fabric kümesi](service-fabric-deploy-anywhere.md) , mikro hizmetlerinizin dağıtıldığı ve yönetildiği, ağa bağlı bir sanal makineler kümesidir.  Azure 'da çalışan bir Service Fabric kümesi, bir Azure kaynağıdır ve Azure Resource Manager kullanılarak dağıtılır. Bu makalede, Azure 'da Kaynak Yöneticisi kullanarak güvenli bir Service Fabric kümesinin nasıl dağıtılacağı açıklanır. Varsayılan bir küme şablonu veya özel şablon kullanabilirsiniz.  Zaten özel şablonunuz yoksa, [bir tane oluşturma hakkında bilgi](service-fabric-cluster-creation-create-template.md)edinebilirsiniz.

Kümenin güvenliğini sağlamak için seçilen güvenlik türü (örn.: Windows kimliği, x509 vb.) kümenin ilk oluşturulması için belirtilmelidir ve bundan sonra değiştirilemez. Bir küme ayarlamadan önce [Service Fabric küme güvenliği senaryolarını][service-fabric-cluster-security]okuyun. Azure 'da Service Fabric, kümenizin ve uç noktalarının güvenliğini sağlamak, istemcilerin kimliğini doğrulamak ve verileri şifrelemek için x509 sertifikası kullanır. Yönetim uç noktalarına erişimin güvenliğini sağlamak için de Azure Active Directory önerilir. Daha fazla bilgi için, [istemcilerin kimliğini doğrulamak üzere Azure AD ayarlama](service-fabric-cluster-creation-setup-aad.md)makalesini okuyun.

Üretim iş yüklerini çalıştırmak için bir üretim kümesi oluşturuyorsanız, önce [Üretim hazırlığı denetim listesini](service-fabric-production-readiness-checklist.md)okumanız önerilir.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar 
Bu makalede, bir kümeyi dağıtmak için Service Fabric RM PowerShell veya Azure CLı modüllerini kullanın:

* [Azure PowerShell 4,1 ve üzeri][azure-powershell]
* [Azure CLı sürüm 2,0 ve üzeri][azure-CLI]

Service Fabric modülleriyle ilgili başvuru belgelerini buradan bulabilirsiniz:
* [Az. ServiceFabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [az SF CLı modülü](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Azure'da oturum açın

Bu makaledeki komutlardan birini çalıştırmadan önce önce Azure 'da oturum açın.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Sistemin ürettiği otomatik olarak imzalanan sertifika kullanarak yeni bir küme oluşturma

Sistemin ürettiği otomatik olarak imzalanan bir sertifika ile güvenliği sağlanmış bir küme oluşturmak için aşağıdaki komutları kullanın. Bu komut, küme güvenliği için kullanılan bir birincil küme sertifikası ayarlar ve bu sertifikayı kullanarak yönetim işlemlerini gerçekleştirmek üzere yönetici erişimi ayarlamak için kullanılır.  Otomatik olarak imzalanan sertifikalar, test kümelerinin güvenliğini sağlamak için faydalıdır.  Üretim kümelerinin bir sertifika yetkilisinden (CA) bir sertifika ile güvenliği sağlanmalıdır.

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Modülde birlikte gelen varsayılan küme şablonunu kullanın

Varsayılan şablonu kullanarak en az parametre belirterek bir kümeyi hızlıca oluşturmak için aşağıdaki komutu kullanın.

Kullanılan şablon [Azure Service Fabric şablonu örnekleri üzerinde kullanılabilir: Windows şablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) ve [Ubuntu şablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Aşağıdaki komut Windows veya Linux kümeleri oluşturabilir, işletim sistemini uygun şekilde belirtmeniz gerekir. PowerShell/CLı komutları Ayrıca, belirtilen *Certificateoutputfolder*'da sertifikayı da çıktı. Ancak, Sertifika klasörünün zaten oluşturulduğundan emin olun. Komut, VM SKU 'SU gibi diğer parametreleri de alır.

> [!NOTE]
> Aşağıdaki PowerShell komutu yalnızca Azure PowerShell `Az` modülüyle birlikte kullanılabilir. Azure Resource Manager PowerShell sürümünün geçerli sürümünü denetlemek için, aşağıdaki "Get-Module az" PowerShell komutunu çalıştırın. Azure Resource Manager PowerShell sürümünüzü yükseltmek için [Bu bağlantıyı](/powershell/azure/install-Az-ps) izleyin. 
>
>

PowerShell kullanarak kümeyi dağıtma:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Azure CLı kullanarak kümeyi dağıtma:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Kendi özel şablonunuzu kullanın

Gereksinimlerinize uyacak özel bir şablon yazmak gerekirse, [Azure Service Fabric şablonu örneklerinde](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)bulunan şablonlardan biriyle başlamanız önemle önerilir. [Küme şablonunuzu özelleştirmeyi][customize-your-cluster-template]öğrenin.

Zaten özel bir şablonunuz varsa, şablondaki ve parametre dosyasındaki üç sertifika ile ilgili parametrelerin şu şekilde adlandırıldığını ve değerlerin null olduğunu aşağıdaki gibi denetleyin:

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

PowerShell kullanarak kümeyi dağıtma:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Azure CLı kullanarak kümeyi dağıtma:

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Kendi X. 509.440 sertifikanızı kullanarak yeni bir küme oluşturun

Kümenizin güvenliğini sağlamak için kullanmak istediğiniz bir sertifikanız varsa, küme oluşturmak için aşağıdaki komutu kullanın.

Bu, diğer amaçlar için kullanarak da kullanacağınız CA imzalı bir sertifikasa, anahtar kasanıza özel olarak ayrı bir kaynak grubu sağlamanız önerilir. Anahtar kasasını kendi kaynak grubuna yerleştirmenizi öneririz. Bu eylem, anahtar ve gizli dizileri kaybetmeden Service Fabric kümenizi içeren kaynak grubu dahil olmak üzere işlem ve depolama kaynak gruplarını kaldırmanızı sağlar. **Anahtar kasanızı içeren kaynak grubunun, kendisini kullanan kümeyle *aynı bölgede olması gerekir* .**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Modülde birlikte gelen bir düğüm türü şablonu olan varsayılan beş düğümü kullanın
Kullanılan şablon [Azure örnekleri: Windows şablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) ve [Ubuntu şablonu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure) üzerinde kullanılabilir

PowerShell kullanarak kümeyi dağıtma:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Azure CLı kullanarak kümeyi dağıtma:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Kendi özel küme şablonunuzu kullanın
Gereksinimlerinize uyacak özel bir şablon yazmak gerekirse, [Azure Service Fabric şablonu örneklerinde](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)bulunan şablonlardan biriyle başlamanız önemle önerilir. [Küme şablonunuzu özelleştirmeyi][customize-your-cluster-template]öğrenin.

Zaten özel bir şablonunuz varsa, şablondaki ve parametre dosyasındaki üç sertifika ile ilgili parametrelerin aşağıdaki şekilde adlandırıldığını ve değerlerin null olduğunu aşağıdaki şekilde kontrol ettiğinizden emin olun.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

PowerShell kullanarak kümeyi dağıtma:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Azure CLı kullanarak kümeyi dağıtma:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Anahtar kasasında karşıya yüklenen gizli dizi için bir işaretçi kullanma

Mevcut bir anahtar kasasını kullanmak için, [dağıtım için](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI) anahtar kasasının, işlem kaynak sağlayıcısının bu kaynaktan sertifika almasını ve küme düğümlerine yüklemesini sağlamak için etkinleştirilmesi gerekir.

PowerShell kullanarak kümeyi dağıtma:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Azure CLı kullanarak kümeyi dağıtma:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

## <a name="next-steps"></a>Sonraki adımlar
Bu noktada, Azure 'da çalışan bir güvenli kümeniz vardır. Sonra, [kümenize bağlanın](service-fabric-connect-to-secure-cluster.md) ve [uygulama gizli dizilerini yönetmeyi](service-fabric-application-secret-management.md)öğrenin.

JSON sözdizimi ve özellikler için bir şablon kullanmak için bkz. [Microsoft. ServiceFabric/kümeler şablon başvurusu](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
