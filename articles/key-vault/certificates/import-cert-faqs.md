---
title: Sık sorulan sorular-Azure Key Vault sertifika içeri aktarma
description: Azure Key Vault sertifikalarını içeri aktarma hakkında sık sorulan soruların yanıtlarını alın.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 97dfc2db837f728b8cb4ece9a064f99006c9996b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767826"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Azure Key Vault sertifikaları içeri aktarma hakkında SSS

Bu makalede Azure Key Vault sertifikalarını içeri aktarma hakkında sık sorulan sorular yanıtlanmaktadır.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Azure Key Vault sertifikayı nasıl içeri aktarabilirim?

Bir sertifika içeri aktarma işlemi için Azure Key Vault iki sertifika dosyası biçimini kabul eder: pek ve PFX. Yalnızca ortak bölümü olan ped dosyaları olsa da, Key Vault yalnızca özel anahtarı olan bir ped veya PFX dosyası gerektirir. Daha fazla bilgi için bkz. [Key Vault bir sertifikayı Içeri aktarma](./tutorial-import-certificate.md#import-a-certificate-to-key-vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Parola korumalı bir sertifikayı Key Vault aldıktan sonra karşıdan yükledikten sonra, onunla ilişkili parolayı neden göremiyorum?
     
Bir sertifika içeri aktarıldıktan ve Key Vault bir şekilde korunduktan sonra, ilişkili parolası kaydedilmez. Parola, içeri aktarma işlemi sırasında yalnızca bir kez gereklidir. Bu tasarıma göre yapılır, ancak [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)aracılığıyla parolayı ekleyerek sertifikayı her zaman gizli dizi olarak alabilir ve Base64 'den PFX 'e dönüştürebilirsiniz.

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>"Hatalı parametre" hatasını nasıl çözebilirim? Key Vault aktarmak için desteklenen sertifika biçimleri nelerdir?

Bir sertifikayı içeri aktardığınızda, anahtarın dosyaya eklendiğinden emin olmanız gerekir. Ayrı olarak farklı biçimde depolanan bir özel anahtarınız varsa, anahtarı sertifikayla birleştirmeniz gerekir. Bazı sertifika yetkilileri (CA 'Lar) diğer biçimlerde sertifika sağlar. Bu nedenle, sertifikayı içeri aktarmadan önce, bunun pek veya PFX dosya biçiminde olduğundan ve anahtarın Rivest – Shamir – Adleman (RSA) veya eliptik-eğri şifreleme (ECC) şifrelemesi kullandığından emin olun. 

Daha fazla bilgi için bkz. [sertifika gereksinimleri](./certificate-scenarios.md#formats-of-import-we-support) ve [sertifika anahtarı gereksinimleri](../keys/about-keys.md).

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>ARM şablonunu kullanarak bir sertifikayı içeri aktarabilir miyim?

Hayır, bir Azure Resource Manager (ARM) şablonu kullanarak sertifika işlemleri yapmak mümkün değildir. Önerilen bir geçici çözüm, Azure API 'SI, Azure CLı veya PowerShell 'deki sertifika içeri aktarma yöntemlerini kullanmaktır. Mevcut bir sertifikanız varsa, parolayı gizli dizi olarak içeri aktarabilirsiniz.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Azure portal aracılığıyla bir sertifikayı içeri aktardığımda, "sorun oluştu" hatası alıyorum. Nasıl daha fazla araştırma yapabilirim?
     
Daha açıklayıcı bir hata görüntülemek için, [Azure CLI](/cli/azure/keyvault/certificate#az_keyvault_certificate_import) veya [PowerShell](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate)kullanarak sertifika dosyasını içeri aktarın.

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>"Hata türü: erişim engellendi veya Kullanıcı sertifikayı içeri aktarma yetkisi yok" hatasını nasıl çözebilirim?
    
İçeri aktarma işlemi, erişim ilkeleri altına sertifikayı içeri aktarmak için kullanıcıya izin vermenizi gerektirir. Bunu yapmak için, anahtar kasanıza gidin, **erişim ilkeleri**  >  **Ekle erişim ilkesi Ekle**  >  **sertifika izinleri**  >  **sorumlusu** seçin, Kullanıcı için arama yapın ve ardından kullanıcının e-posta adresini ekleyin. 

Sertifikayla ilgili erişim ilkeleri hakkında daha fazla bilgi için bkz. [Azure Key Vault sertifikaları hakkında](./about-certificates.md#certificate-access-control).


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>"Hata türü: sertifika oluştururken çakışma" hatasını nasıl çözebilirim?
    
Her sertifika adının benzersiz olması gerekir. Aynı ada sahip bir sertifika, geçici olarak silinmiş bir durumda olabilir. Ayrıca, [bir sertifikanın](./about-certificates.md#composition-of-a-certificate)oluşturulmasına göre, yeni sertifika oluşturulduğunda aynı ada sahip bir adreslenebilir gizli dizi oluşturur. bu nedenle, sertifikanız için belirtmeye çalıştığınız ada sahip anahtar kasasında başka bir anahtar veya gizli dizi varsa, sertifika oluşturma başarısız olur ve bu anahtarı veya parolayı kaldırmanız ya da sertifikanız için farklı bir ad kullanmanız gerekir. 

Daha fazla bilgi için bkz. [sertifika silme işlemi](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate).

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Neden "hata türü: karakter uzunluğu çok uzun" alıyorum?
Bu hata iki nedenden kaynaklanabilir:    
* Sertifika konu adı 200 karakterle sınırlıdır.
* Sertifika parolası 200 karakterle sınırlıdır.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>Hata "belirtilen pek X. 509.440 sertifika içeriği beklenmeyen bir biçimde. Lütfen sertifikanın geçerli pek biçiminde olup olmadığını denetleyin. "
Lütfen ped dosyasındaki içeriğin UNIX stili çizgi ayırıcıları kullandığını doğrulayın `(\n)`

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Azure Key Vault, zaman aşımına uğradı bir sertifikayı içeri aktarabilir miyim?
    
Hayır, zaman aşımına memiş PFX sertifikaları Key Vault içine aktarılamaz.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Sertifikamı doğru biçime nasıl dönüştürebilirim?

CA 'nız sertifikayı gerekli biçimde vermesini isteyebilirsiniz. Ayrıca, sertifikayı doğru biçime dönüştürmenize yardımcı olabilecek üçüncü taraf araçlar da vardır.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>İş ortağı olmayan CA 'lardan sertifika alabilir miyim?
Evet, herhangi bir CA 'dan sertifika içeri aktarabilirsiniz, ancak anahtar kasanızın bunları otomatik olarak yenileyemeyecektir. Anımsatıcıları, sertifikanın süre sonu hakkında bildirim almak için ayarlayabilirsiniz.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Bir sertifikayı bir iş ortağı CA 'dan içeri aktardığımda, otomatik yenileme özelliği hala çalışır mı?
Evet. Sertifikayı karşıya yükledikten sonra, sertifikanın verme ilkesinde, oto döndürmeyi belirttiğinizden emin olun. Bir sonraki döngüye veya sertifika sürümü yayımlanıncaya kadar ayarlarınız geçerli olur.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Key Vault içeri aktardığım App Service sertifikayı neden göremiyorum? 
Sertifikayı başarıyla aldıysanız, **gizli** dizi bölmesine giderek doğrulayabilmelisiniz.


## <a name="next-steps"></a>Sonraki adımlar

- [Azure Key Vault sertifikaları](./about-certificates.md)
