---
title: Azure Key Vault 'de CSR oluşturma ve birleştirme
description: Azure Key Vault içinde bir CSR oluşturma ve birleştirme hakkında bilgi edinin.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: a4d079855e5aa05adb84b62d686d9f386608f7bb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752147"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Key Vault 'de CSR oluşturma ve birleştirme

Azure Key Vault, herhangi bir sertifika yetkilisi (CA) tarafından verilen dijital sertifikaların depolanmasını destekler. Özel/ortak anahtar çifti ile bir sertifika imzalama isteği (CSR) oluşturmayı destekler. CSR, herhangi bir CA (bir iç kuruluş CA 'sı veya bir dış genel CA) tarafından imzalanabilir. CSR, dijital bir sertifika istemek için bir CA 'ya göndereceğiniz bir iletidir.

Sertifikalar hakkında daha fazla genel bilgi için bkz. [Azure Key Vault sertifikaları](./about-certificates.md).

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>İş ortağı CA 'Lar tarafından verilen Key Vault sertifika ekleme

Sertifika oluşturmayı basitleştirmek için aşağıdaki sertifika yetkililerine sahip iş ortakları Key Vault.

|Sağlayıcı|Sertifika türü|Yapılandırma kurulumu  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault DigiCert ile OV veya EV SSL sertifikaları sunar| [Tümleştirme Kılavuzu](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault GlobalSign ile OV veya EV SSL sertifikaları sunar| [Tümleştirme Kılavuzu](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>İş ortağı olmayan CA 'Lar tarafından verilen Key Vault sertifika ekleme

Key Vault ile iş ortağı olmayan CA 'lardan bir sertifika eklemek için aşağıdaki adımları izleyin. (Örneğin, GoDaddy güvenilen bir Key Vault CA değildir.)

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Sertifikayı eklemek istediğiniz anahtar kasasına gidin.
1. Özellikler sayfasında, **Sertifikalar**' ı seçin.
1. **Oluştur/al** sekmesini seçin.
1. **Sertifika oluştur** ekranında, aşağıdaki değerleri seçin:
    - **Sertifika oluşturma yöntemi**: oluştur.
    - **Sertifika adı**: contosomanualcsrcertificate.
    - **Sertifika yetkilisinin (CA) türü**: tümleşik olmayan bir CA tarafından verilen sertifika.
    - **Konu**: `"CN=www.contosoHRApp.com"` .
     > [!NOTE]
     > Değerde virgül (,) olan göreli ayırt edici bir ad (RDN) kullanıyorsanız, özel karakteri içeren değeri çift tırnak içinde sarın. 
     >
     > **Konuya** örnek giriş:`DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > Bu örnekte, RDN `OU` adında virgül olan bir değer içerir. İçin elde edilen çıktı `OU` **docs, contoso**.
1. Diğer değerleri istediğiniz şekilde seçin ve sertifikayı **Sertifikalar** listesine eklemek için **Oluştur** ' u seçin.

    ![Sertifika özelliklerinin ekran görüntüsü](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. **Sertifikalar** listesinde, yeni sertifikayı seçin. Sertifikanın geçerli durumu henüz sertifika YETKILISI tarafından verilmediğinden **devre dışı bırakıldı** .
1. **Sertifika işlemi** SEKMESINDE, **CSR 'yi indir**' i seçin.

   ![CSR 'yi Indir düğmesini vurgulayan ekran görüntüsü.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. CA 'nın CSR 'yi (. CSR) imzalamasını sağlayabilirsiniz.
1. İstek imzalandıktan sonra, Key Vault imzalı sertifikayı eklemek için **sertifika işlemi** sekmesinde **imzalı isteği Birleştir** ' i seçin.

Sertifika isteği artık başarıyla birleştirildi.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Sertifika ilkesi oluşturun. Bu senaryoda seçilen CA iş ortağı olmadığından, **IssuerName** **bilinmiyor** olarak ayarlanır ve Key Vault sertifikayı kaydetmez veya yenilemez.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Değerde virgül (,) olan bir göreli ayırt edici ad (RDN) kullanıyorsanız, tam değer veya değer kümesi için tek tırnak kullanın ve özel karakteri içeren değeri çift tırnak içinde sarın. 
     >
     >**SubjectName** öğesine örnek giriş: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown` . Bu örnekte, `OU` değer **Belgeler, contoso** olarak okur. Bu biçim virgül içeren tüm değerler için geçerlidir.
     > 
     > Bu örnekte, RDN `OU` adında virgül olan bir değer içerir. İçin elde edilen çıktı `OU` **docs, contoso**.

1. CSR 'yi oluşturun.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. CA 'nın CSR 'yi imzalamasını sağlayabilirsiniz. , `$csr.CertificateSigningRequest` Sertifika için temel KODLANMıŞ CSR 'dir. Bu Blobun, verenin sertifika isteği Web sitesinde dökümünü yapabilirsiniz. Bu adım CA 'dan CA 'ya farklılık gösterir. Bu adımın nasıl yürütüleceğini öğrenmek için CA 'nın yönergelerine bakın. Ayrıca, CSR veya OpenSSL gibi araçları kullanarak, CSR 'nin imzalanmasını alabilir ve sertifika oluşturma işlemini tamamlayabilirsiniz.

1. Key Vault imzalı isteği birleştirin. Sertifika isteği imzalandıktan sonra, Azure Key Vault içinde oluşturulan ilk özel/ortak anahtar çifti ile birleştirebilirsiniz.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

Sertifika isteği artık başarıyla birleştirildi.

---

## <a name="add-more-information-to-the-csr"></a>CSR 'ye daha fazla bilgi ekleme

CSR 'yi oluştururken daha fazla bilgi eklemek istiyorsanız, bunu **SubjectName** içinde tanımlayın. Şöyle bir bilgi eklemek isteyebilirsiniz:
- Ülke
- Şehir/yerleşim yeri
- İl
- Kuruluş
- Kurum birimi

Örnek

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> Ek bilgiler içeren bir etki alanı doğrulama (DV) sertifikası isteğinde bulunduğsanız, istekte bulunan tüm bilgileri doğrulayamaması durumunda CA isteği reddedebilirler. Kuruluş doğrulaması (OV) sertifikası isteğinde bulunduğunuzda ek bilgiler daha uygun olabilir.

## <a name="faqs"></a>SSS

- Nasıl yaparım?, CSR 'mi izlemek veya yönetmek mı istiyorsunuz?

     Bkz. [sertifika oluşturmayı izleme ve yönetme](./create-certificate-scenarios.md).

- **Hata türü ' ne görüyorsanız, belirtilen X içindeki son varlık sertifikasının ortak anahtarı. 509.440 sertifika içeriği belirtilen özel anahtarın ortak bölümüyle eşleşmez. Lütfen sertifikanın geçerli** olup olmadığını denetleyin.

     Bu hata, imzalanmış CSR 'yi, açtığınız aynı CSR isteğiyle birleştirmemişse oluşur. Oluşturduğunuz her yeni CSR, imzalanmış isteği birleştirdiğinizde eşleşmesi gereken özel bir anahtara sahiptir.

- Bir CSR birleştirildiğinde, tüm zinciri birleştirilecektir mi?

     Evet, kullanıcının birleştirmek üzere bir. p7b dosyası geri aldığı belirtilen tüm zinciri birleştirilecektir.

- Verilen sertifika Azure portal devre dışı durumunda olduğunda ne olur?

     Bu sertifikanın hata iletisini gözden geçirmek için **sertifika işlemi** sekmesini görüntüleyin.

- Hata türünü görmem halinde, **belirtilen konu adı geçerli bir X500 adı değil** mi?

     Bu hata, **SubjectName** özel karakterler içeriyorsa meydana gelebilir. Azure portal ve PowerShell yönergelerindeki notlara bakın.

---

## <a name="next-steps"></a>Sonraki adımlar

- [Kimlik doğrulama, istekler ve yanıtlar](../general/authentication-requests-and-responses.md)
- [Key Vault Geliştirici Kılavuzu](../general/developers-guide.md)
- [Azure Key Vault REST API başvurusu](/rest/api/keyvault)
- [Kasa-oluştur veya güncelleştir](/rest/api/keyvault/vaults/createorupdate)
- [Kasa-erişim Ilkesini güncelleştirme](/rest/api/keyvault/vaults/updateaccesspolicy)