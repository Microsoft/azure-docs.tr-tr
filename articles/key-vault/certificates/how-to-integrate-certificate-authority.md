---
title: Key Vault'u DigiCert Sertifika Yetkilisiyle Tümleştirme
description: DigiCert sertifika yetkilisi ile Key Vault tümleştirme
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 4351526c77961856b118bdeae07cecf48340f5fe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749321"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Key Vault'u DigiCert Sertifika Yetkilisiyle Tümleştirme

Azure Key Vault ağınız için dijital sertifikaları kolayca sağlamanıza, yönetmenize ve dağıtmanıza ve uygulamalar için güvenli iletişimleri etkinleştirmenize olanak tanır. Dijital sertifika, elektronik bir işlemde kimlik kanıtı oluşturmak için elektronik bir kimlik bilgileridir. 

Azure Anahtar Kasası kullanıcıları, Key Vault doğrudan bir şekilde DigiCert sertifikaları oluşturabilir. Key Vault, DigiCert tarafından verilen bu sertifikalar için, DigiCert sertifika yetkilisi ile Key Vault güvenilen ortaklığı aracılığıyla uçtan uca sertifika yaşam döngüsü yönetimine olanak sağlar.

Sertifikalar hakkında daha fazla genel bilgi için bkz. [Azure Key Vault sertifikaları](./about-certificates.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu kılavuzu gerçekleştirmek için aşağıdaki kaynaklara sahip olmanız gerekir.
* Bir Anahtar Kasası. Mevcut bir anahtar kasasını kullanabilir veya şu hızlı başlangıçlardan birindeki adımları izleyerek yeni bir tane oluşturabilirsiniz:
   - [Azure CLı ile Anahtar Kasası oluşturma](../general/quick-create-cli.md)
   - [Azure PowerShell ile bir Anahtar Kasası oluşturma](../general/quick-create-powershell.md)
   - [Azure Portal bir Anahtar Kasası oluşturun](../general/quick-create-portal.md).
*   DigiCert CertCentral hesabını etkinleştirmeniz gerekir. CertCentral hesabınız için [kaydolun](https://www.digicert.com/account/signup/) .
*   Hesaplarınızdaki yönetici düzeyi izinleri.


### <a name="before-you-begin"></a>Başlamadan önce

DigiCert CertCentral hesabınızdan aşağıdaki bilgilere sahip olduğunuzdan emin olun:
-   CertCentral hesap KIMLIĞI
-   Kuruluş Kimliği
-   API anahtarı

## <a name="adding-certificate-authority-in-key-vault"></a>Key Vault sertifika yetkilisi ekleniyor 
DigiCert CertCentral hesabından bilgi topladıktan sonra artık anahtar kasasındaki sertifika yetkilisi listesine DigiCert ekleyebilirsiniz.

### <a name="azure-portal"></a>Azure portalı

1.  DigiCert sertifika yetkilisi eklemek için, DigiCert eklemek istediğiniz anahtar kasasına gidin. 
2.  Key Vault Özellikler sayfalarında, **Sertifikalar**' ı seçin.
3.  **Sertifika yetkilileri** sekmesini seçin. ![ sertifika yetkililerini seçin](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  **Ekle** seçeneğini belirleyin.
 ![Sertifika Yetkilisi Ekle](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  **Sertifika yetkilisi oluştur** ekranında aşağıdaki değerleri seçin:
    -   **Ad**: tanımlayıcı veren bir ad ekleyin. Örnek Digiccertca
    -   **Sağlayıcı**: menüden DigiCert öğesini seçin.
    -   **Hesap kimliği**: DigiCert Certcentral hesap kimliğinizi girin
    -   **Hesap parolası**: DigiCert certcentral HESABıNıZDA oluşturduğunuz API anahtarını girin
    -   **Kuruluş kimliği**: DigiCert Certcentral hesabından toplanan OrgID girin 
    -   **Oluştur**’a tıklayın.
   
6.  Digiccertca 'Nın artık sertifika yetkilileri listesine eklendiğini görürsünüz.


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell komutları veya betikleri kullanarak Azure kaynakları oluşturmak ve yönetmek için kullanılır. Azure, tarayıcıda Azure portal aracılığıyla kullanabileceğiniz etkileşimli bir kabuk ortamı olan Azure Cloud Shell.

PowerShell 'i yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici, Azure PowerShell modülü sürümü 1.0.0 veya üzerini gerektirir. `$PSVersionTable.PSVersion`Sürümü bulmak için yazın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Login-AzAccount` komutunu da çalıştırmanız gerekir.

```azurepowershell-interactive
Login-AzAccount
```

1.  **Kaynak grubu** oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Ile bir Azure Kaynak grubu oluşturun. Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. **Key Vault** oluşturma

Anahtar kasanız için benzersiz bir ad kullanmanız gerekir. Burada "contoso-Vaultname", bu kılavuz boyunca Key Vault adıdır.

- **Kasa adı** Contoso-Vaultname.
- **Kaynak grubu adı** ContosoResourceGroup.
- **Konum** EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. DigiCert CertCentral hesabından toplanan bilgiler için değişkenleri tanımlayın.

- **Hesap kimliği** değişkenini tanımla
- **Kuruluş kimliği** değişkenini tanımla
- **API anahtar** değişkenini tanımla

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. **Veren** ayarla. Bu işlem, anahtar kasasında bir sertifika yetkilisi olarak DigiCert ekler. Parametreler hakkında daha fazla bilgi edinmek için [buradan okuyun](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Sertifika Için Ilke ayarlanıyor ve** DigiCert 'den doğrudan Key Vault içinde sertifika verme.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Sertifika artık bu tümleştirme aracılığıyla belirtilen Key Vault içindeki DigiCert CA 'sı tarafından başarıyla verildi.

## <a name="troubleshoot"></a>Sorun giderme

Verilen sertifika, Azure portal ' devre dışı ' durumunda ise, bu sertifika için DigiCert hata iletisini gözden geçirmek üzere **sertifika işlemini** görüntüleme adımına geçin.

 ![Sertifika işlemi](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Hata iletisi ' Bu sertifika isteğini tamamlamak için lütfen bir birleştirme işlemi gerçekleştirin. '
Bu isteği gerçekleştirmek için CA tarafından imzalanan CSR 'yi birleştirmeniz gerekir. [Burada](./create-certificate-signing-request.md) daha fazla bilgi edinin

Daha fazla bilgi için [Key Vault REST API başvurusu Içindeki sertifika işlemlerine](/rest/api/keyvault)bakın. İzinleri oluşturma hakkında bilgi için bkz. [kasa-oluşturma veya güncelleştirme](/rest/api/keyvault/vaults/createorupdate) ve [kasa-güncelleştirme erişim ilkesi](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

- Keykasadan bir DigiCert joker belgesi oluşturabilir miyim? 
   Evet. Bu, DigiCert hesabınızı nasıl yapılandırdığınıza bağlıdır.
- DigiCert ile **ov-SSL veya KD SSL** sertifikası nasıl oluşturabilirim? 
   Anahtar Kasası, OV ve EV SSL sertifikaları oluşturmayı destekler. Bir sertifika oluştururken Gelişmiş Ilke yapılandırması ' na tıklayın ve ardından sertifika türünü belirtin. Desteklenen değerler şunlardır: OV-SSL, KD-SSL
   
   DigiCert hesabınız izin veriyorsa bu tür bir sertifikayı Anahtar Kasası 'nda oluşturabilirsiniz. Bu tür bir sertifika için doğrulama, DigiCert tarafından gerçekleştirilir ve doğrulama başarısız olursa, destek ekibi çözüm konusunda en iyi şekilde yardım edebilir. Bir sertifika oluştururken subjectName ' de tanımlayarak daha fazla bilgi ekleyebilirsiniz.

Örnek
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- Tümleştiricert aracılığıyla sertifika edinerek tümleştirme aracılığıyla DigiCert sertifikası oluşturmada bir gecikme süresi var mı?
   Hayır. Bir sertifika oluştururken, bu doğrulama işlemi zaman alabilir ve doğrulamanın aşağıdaki işlem DigiCert 'e bağlı olduğunu belirten bir işlemdir.


## <a name="next-steps"></a>Sonraki adımlar

- [Kimlik doğrulama, istekler ve yanıtlar](../general/authentication-requests-and-responses.md)
- [Key Vault Geliştirici Kılavuzu](../general/developers-guide.md)