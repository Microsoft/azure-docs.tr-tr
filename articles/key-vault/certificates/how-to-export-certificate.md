---
title: Sertifikaları Azure Key Vault dışarı aktarma
description: Azure Key Vault sertifikaların nasıl dışarı aktarılacağını öğrenin.
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: 0efe0164d8e1a4e5bc3b9d6d7313855740afd316
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767916"
---
# <a name="export-certificates-from-azure-key-vault"></a>Sertifikaları Azure Key Vault dışarı aktarma

Azure Key Vault sertifikaların nasıl dışarı aktarılacağını öğrenin. Sertifikaları Azure CLı, Azure PowerShell veya Azure portal kullanarak dışarı aktarabilirsiniz. 

## <a name="about-azure-key-vault-certificates"></a>Azure Key Vault sertifikaları hakkında

Azure Key Vault ağınız için dijital sertifikaları kolayca sağlamanıza, yönetmenize ve dağıtmanıza olanak tanır. Ayrıca, uygulamalar için güvenli iletişimler da sunar. Daha fazla bilgi için bkz. [Azure Key Vault sertifikaları](./about-certificates.md) .

### <a name="composition-of-a-certificate"></a>Sertifikanın birleşimi

Bir Key Vault sertifikası oluşturulduğunda, aynı ada sahip bir adreslenebilir *anahtar* ve *gizli* dizi oluşturulur. Key Vault anahtar, anahtar işlemlerine izin verir. Key Vault parolası, sertifika değerinin gizli olarak alınmasına izin verir. Bir Key Vault sertifikası ortak x509 sertifika meta verilerini de içerir. Daha fazla bilgi için [bir sertifikanın kompozisyonunu](./about-certificates.md#composition-of-a-certificate) ziyaret edin.

### <a name="exportable-and-non-exportable-keys"></a>Dışarı aktarılabilir ve dışarı aktarılamayan anahtarlar

Bir Key Vault sertifikası oluşturulduktan sonra, özel anahtarla adreslenebilir gizli alanından alabilirsiniz. PFX veya pek biçimindeki sertifikayı alın.

- **Dışarı aktarılabilir**: sertifikayı oluşturmak için kullanılan ilke, anahtarın dışarı aktarılabilir olduğunu gösterir.
- **Dışarı aktarılabilir** değil: sertifikayı oluşturmak için kullanılan ilke, anahtarın dışarı aktarılamaz olduğunu gösterir. Bu durumda, özel anahtar bir gizli dizi olarak alındığında değerin bir parçası değildir.

Desteklenen KeyTypes: RSA, RSA-HSM, EC, EC-HSM, Eki ( [burada](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)listelenen) dışarı AKTARıLABILIR yalnızca RSA, EC ile kullanılabilir. HSM anahtarları dışarı aktarılabilir değildir.

Daha fazla bilgi için bkz. [Azure Key Vault sertifikaları hakkında](./about-certificates.md#exportable-or-non-exportable-key) .

## <a name="export-stored-certificates"></a>Depolanan sertifikaları dışarı aktar

Depolanan sertifikaları Azure CLI, Azure PowerShell veya Azure portal kullanarak Azure Key Vault'a aktarabilirsiniz.

> [!NOTE]
> Sertifikayı anahtar kasasında içeri aktardığınızda yalnızca bir sertifika parolası gerektir. Key Vault ilişkili parolayı kaydetmez. Sertifikayı dışa aktardığınızda parola boştur.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bir Key Vault sertifikasının **ortak bölümünü** Indirmek IÇIN Azure CLI 'de aşağıdaki komutu kullanın.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```

Daha fazla bilgi için [örnekleri ve parametre tanımlarını](/cli/azure/keyvault/certificate#az_keyvault_certificate_download) görüntüleyin.

Sertifika olarak indirme, genel bölümü elde etmek anlamına gelir. Hem özel anahtar hem de genel meta verileri isterseniz, parolayı gizli olarak indirebilirsiniz.

```azurecli
az keyvault secret download -–file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```

Daha fazla bilgi için bkz. [parametre tanımları](/cli/azure/keyvault/secret#az_keyvault_secret_download).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

**ContosoKV01** adlı anahtar kasasından **TestCert01** adlı sertifikayı almak için Azure PowerShell ' de bu komutu kullanın. Sertifikayı PFX dosyası olarak indirmek için aşağıdaki komutu çalıştırın. Bu komutlar **Secretıd**'ye erişir ve sonra IÇERIĞI bir PFX dosyası olarak kaydeder.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$secret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $cert.Name
$secretValueText = '';
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
    $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
$secretByte = [Convert]::FromBase64String($secretValueText)
$x509Cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509Cert.Import($secretByte, "", "Exportable,PersistKeySet")
$type = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx
$pfxFileByte = $x509Cert.Export($type, $password)

# Write to a file
[System.IO.File]::WriteAllBytes("KeyVault.pfx", $pfxFileByte)
```

Bu komut, tüm sertifika zincirini özel anahtarla (örn. içeri aktarıldıkları gibi) dışa aktarır. Sertifika, parola korumalı.
**Get-azkeyvaultcertificate** komutu ve parametreleri hakkında daha fazla bilgi için bkz. [Get-azkeyvaultcertificate-örnek 2](/powershell/module/az.keyvault/Get-AzKeyVaultCertificate).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal sertifika dikey penceresinde bir sertifikayı oluşturduktan/içeri aktardıktan sonra **, sertifikanın başarıyla** oluşturulduğunu belirten bir bildirim alırsınız. İndirme seçeneğini görmek için sertifikayı ve geçerli sürümü seçin.

Sertifikayı indirmek için, **cer biçiminde indir** veya **PFX/ped biçiminde indir**' i seçin.

![Sertifika indirme](../media/certificates/quick-create-portal/current-version-shown.png)

**Azure App Service sertifikalarını dışarı aktarma**

Azure App Service sertifikalar, SSL sertifikaları satın almanın kolay bir yoludur. Bunları portalın içinden Azure uygulamalarına atayabilirsiniz. İçeri aktardıktan sonra App Service sertifikaları **gizli** dizileri altında bulunur.

Daha fazla bilgi için [Azure App Service sertifikaları dışarı aktarma](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)adımlarına bakın.

---

## <a name="read-more"></a>Daha fazla bilgi edinin
* [Çeşitli sertifika dosyası türleri ve tanımları](/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)
