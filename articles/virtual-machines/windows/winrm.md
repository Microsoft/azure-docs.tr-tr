---
title: Azure VM için WinRM erişimi ayarlama
description: Kaynak Yöneticisi dağıtım modelinde oluşturulan bir Azure sanal makinesiyle kullanılmak üzere WinRM erişimi ayarlayın.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: ca52a458104b4de0f7b3ed2aa3f76109a5623c97
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74067323"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Azure Resource Manager 'de sanal makineler için WinRM erişimi ayarlanıyor

WinRM bağlantısı ile bir VM kurmak için gerçekleştirmeniz gereken adımlar şunlardır

1. Anahtar kasası oluşturma
2. Otomatik olarak imzalanan sertifika oluşturma
3. Otomatik olarak imzalanan sertifikanızı Key Vault karşıya yükleyin
4. Key Vault otomatik olarak imzalanan sertifikanızın URL 'sini alın
5. VM oluştururken otomatik olarak imzalanan sertifika URL 'nize başvurma

 

## <a name="step-1-create-a-key-vault"></a>1\. Adım: Key Vault oluşturma
Key Vault oluşturmak için aşağıdaki komutu kullanabilirsiniz

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>2\. Adım: otomatik olarak imzalanan sertifika oluşturma
Bu PowerShell betiğini kullanarak kendinden imzalı bir sertifika oluşturabilirsiniz

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>3\. Adım: Key Vault otomatik olarak imzalanan sertifikanızı karşıya yükleyin
Sertifikayı adım 1 ' de oluşturulan Key Vault yüklemeden önce, Microsoft. COMPUTE kaynak sağlayıcısı 'nın anlayabilmesi için bir biçime dönüştürülmesi gerekir. Aşağıdaki PowerShell betiği şunları yapmanıza izin verir

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>4\. Adım: Key Vault otomatik olarak imzalanan sertifikanızın URL 'sini alın
Microsoft. COMPUTE kaynak sağlayıcısı, VM sağlanırken Key Vault içindeki gizliliğe bir URL 'ye ihtiyaç duyuyor. Bu, Microsoft. COMPUTE kaynak sağlayıcısı 'nın gizli anahtarı indirmesini ve sanal makinede eşdeğer sertifikayı oluşturmasını sağlar.

> [!NOTE]
> Gizli dizinin URL 'sinin sürümü de içermesi gerekir. Https aşağıdaki gibi bir örnek URL:\//contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Şablonlar
Aşağıdaki kodu kullanarak şablondaki URL bağlantısını edinebilirsiniz

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Aşağıdaki PowerShell komutunu kullanarak bu URL 'YI edinebilirsiniz

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>5\. Adım: VM oluştururken otomatik olarak imzalanan sertifika URL 'nize başvurma
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager şablonları
Şablonlar aracılığıyla bir VM oluştururken, sertifikaya gizli diziler bölümünde ve winRM bölümünde şu şekilde başvurulur:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Yukarıdaki bir örnek şablon, burada [201-VM-WinRM-keykasasında](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows) bulunabilir.

Bu şablon için kaynak kodu [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows) 'da bulabilirsiniz

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>6\. Adım: VM 'ye bağlanma
VM 'ye bağlanabilmeniz için makinenizin WinRM uzaktan yönetimi için yapılandırıldığından emin olmanız gerekir. PowerShell 'i yönetici olarak başlatın ve ayarladığınızdan emin olmak için aşağıdaki komutu yürütün.

    Enable-PSRemoting -Force

> [!NOTE]
> Yukarıdaki işlem çalışmazsa WinRM hizmetinin çalıştığından emin olmanız gerekebilir. Bunu `Get-Service WinRM` kullanarak yapabilirsiniz
> 
> 

Kurulum tamamlandıktan sonra aşağıdaki komutu kullanarak VM 'ye bağlanabilirsiniz

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
