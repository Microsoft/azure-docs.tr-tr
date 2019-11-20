---
title: Azure Key Vault sertifikası oluşturma | Azure Marketi
description: Azure tarafından dağıtılan bir VHD 'den bir sanal makinenin nasıl kaydedileceği açıklanmaktadır.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 4adc6f716050e2d792e0a5c022972e4340d2846a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823122"
---
# <a name="create-certificates-for-azure-key-vault"></a>Azure Key Vault için sertifikalar oluşturma

Bu makalede, Azure 'da barındırılan bir sanal makineye (VM) bir Windows Uzaktan Yönetimi (WinRM) bağlantısı kurmak için gereken kendinden imzalı sertifikaların nasıl sağlanacağı açıklanmaktadır. Bu işlem üç adımdan oluşur:

1.  Güvenlik sertifikası oluşturun. 
2.  Bu sertifikayı depolamak için Azure Key Vault oluşturun. 
3.  Bu anahtar kasasında sertifikaları depolayın. 

Bu iş için yeni veya var olan bir Azure Kaynak grubu kullanabilirsiniz.  Aşağıdaki açıklamada, önceki yaklaşım kullanılır.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Sertifikayı oluşturma

Sertifika dosyasını (. pfx) yerel bir klasörde oluşturmak için aşağıdaki Azure PowerShell betiğini düzenleyin ve çalıştırın.  Aşağıdaki parametrelerin değerlerini değiştirmeniz gerekir:

|  **Parametresinin**        |   **Açıklama**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | . Pfx dosyasının kaydedileceği yerel klasör  |
| `$location`    | Azure Standart coğrafi konumlarından biri  |
| `$vmName`      | Planlı Azure sanal makinesinin adı   |
| `$certname`    | Sertifikanın adı; planlı VM 'nin tam etki alanı adıyla eşleşmelidir  |
| `$certpassword` | Sertifikalar için parola, planlanan VM için kullanılan parolayla aynı olmalıdır  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
    $location = "westus"
    
    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"
    
    # Certification name - should match with FQDN of Windows Azure creating VM 
    $certname = "$vmName.$location.cloudapp.azure.com"
    
    # Certification password - should be match with password of Windows Azure creating VM 
    $certpassword = "SecretPassword@123"
    
    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb 

```
> [!TIP]
> Farklı parametrelerin değerlerinin korunabilmesi için, bu adımlar sırasında aynı PowerShell konsolu oturumunu etkin tutun.

> [!WARNING]
> Bu betiği kaydederseniz, güvenlik bilgileri (parola) içerdiğinden yalnızca güvenli bir konumda saklayın.


## <a name="create-the-key-vault"></a>Anahtar Kasası oluşturma

[Anahtar Kasası dağıtım şablonunun](./cpp-key-vault-deploy-template.md) içeriğini yerel makinenizde bir dosyaya kopyalayın. (Aşağıdaki örnek betikte bu kaynak `C:\certLocation\keyvault.json`.)  Bir Azure Key Vault örneği ve ilişkili kaynak grubu oluşturmak için aşağıdaki Azure PowerShell betiğini düzenleyin ve çalıştırın.  Aşağıdaki parametrelerin değerlerini değiştirmeniz gerekir:

|  **Parametresinin**        |   **Açıklama**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Dağıtım tanımlayıcılarına eklenen rastgele sayısal dize                     |
| `$rgName`             | Oluşturulacak Azure Kaynak grubu (RG) adı                                        |
|  `$location`          | Azure Standart coğrafi konumlarından biri                                  |
| `$kvTemplateJson`     | Anahtar Kasası için Kaynak Yöneticisi şablonu içeren dosyanın yolu (keykasa. JSON) |
| `$kvname`             | Yeni anahtar kasasının adı                                                       |
|  |  |

```powershell
    # Creating Key vault in resource group
    
    # "Random" number for deployment identifiers
    $postfix = "0101048"
    
    # Resource group name
    $rgName = "TestRG$postfix"
    
    # Location of Resource Group
    $location = "westus"
    
    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"
    
    # Key vault name
    $kvname = "isvkv$postfix"
    
    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host 
        
        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }
        
        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message    
      Break
      } 

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message    
        Break
        }
    
    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>Sertifikayı depolayın

Artık,. pfx dosyasında yer alan sertifikaları aşağıdaki betiği çalıştırarak yeni anahtar kasasına kaydedebilirsiniz. 

```powershell
    #push certificate to key vault secret

     $fileName =$certroopath+"\$certname"+".pfx" 
      
     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Sonraki adımlar

Ardından, [Kullanıcı VM görüntüsünden BIR VM dağıtacaksınız](./cpp-deploy-vm-user-image.md).
