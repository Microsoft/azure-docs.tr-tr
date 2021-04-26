---
title: Azure Key Vault sertifikası yenileme hakkında
description: Bu makalede Azure Key Vault sertifikalarının nasıl yenileneceği açıklanır.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 082b0fd4d3324502516dcd2b45b9ad16a919c773
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749231"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Azure Key Vault sertifikalarınızı yenileme

Azure Key Vault ile ağınız için dijital sertifikaları kolayca sağlayabilir, yönetebilir ve dağıtabilir ve uygulamalarınız için güvenli iletişim sağlayabilirsiniz. Sertifikalar hakkında daha fazla bilgi için bkz. [Azure Key Vault sertifikaları hakkında](./about-certificates.md).

Kısa süreli sertifikaları kullanarak veya sertifika döndürme sıklığını artırarak, yetkisiz kullanıcılar tarafından uygulamalarınıza erişimi önlemeye yardımcı olabilirsiniz.

Bu makalede Azure Key Vault sertifikalarınızın nasıl yenileneceği açıklanır.

## <a name="get-notified-about-certificate-expiration"></a>Sertifika süre sonu hakkında bildirim alın
Sertifika yaşam olayları hakkında bildirim almak için sertifika kişisi eklemeniz gerekir. Sertifika kişileri, sertifika ömrü olayları tarafından tetiklenen bildirimleri göndermek için iletişim bilgilerini içerir. Kişi bilgileri, anahtar kasasındaki tüm sertifikalar tarafından paylaşılır. Anahtar kasasındaki tüm sertifikalar için bir olay için belirtilen tüm kişilere bir bildirim gönderilir.

### <a name="steps-to-set-certificate-notifications"></a>Sertifika bildirimlerini ayarlama adımları:
İlk olarak, anahtar kasanıza bir sertifika kişisi ekleyin. Azure portal veya PowerShell cmdlet 'ini kullanarak ekleyebilirsiniz [`Add-AzureKeyVaultCertificateContact`](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact) .

İkincisi, sertifika süre sonu hakkında ne zaman bilgilendirilmek istediğinizi yapılandırın. Sertifikanın yaşam döngüsü özniteliklerini yapılandırmak için, bkz. [Key Vault sertifika oto döndürmeyi yapılandırma](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

Bir sertifikanın ilkesi otomatik olarak yenilemeye ayarlandıysa, aşağıdaki olaylara bir bildirim gönderilir.

- Sertifika yenilemeden önce
- Sertifika yenileme sonrasında, sertifikanın başarıyla yenilendiğini veya bir hata olup olmadığını ve sertifikanın el ile yenilenmesini gerektirme.  

  El ile yenilenmek üzere ayarlanmış bir sertifika ilkesi (yalnızca e-posta) olduğunda, sertifikayı yenileme zamanı olduğunda bir bildirim gönderilir.  

Key Vault, üç sertifika kategorisi vardır:
-    DigiCert veya GlobalSign gibi tümleşik bir sertifika yetkilisi (CA) ile oluşturulan sertifikalar
-    Tümleşik olmayan bir CA ile oluşturulan sertifikalar
-    Otomatik olarak imzalanan sertifikalar

## <a name="renew-an-integrated-ca-certificate"></a>Tümleşik CA sertifikasını yenileme 
Azure Key Vault, güvenilen Microsoft sertifika yetkilileri DigiCert ve GlobalSign tarafından verilen sertifikaların uçtan uca bakımını gerçekleştirir. [Güvenilir bir CA 'yı Key Vault ile tümleştirmeyi](./how-to-integrate-certificate-authority.md)öğrenin.

## <a name="renew-a-nonintegrated-ca-certificate"></a>Tümleşik olmayan bir CA sertifikasını yenileme 
Azure Key Vault kullanarak, çeşitli Azure kaynaklarıyla tümleştirmenize ve dağıtımı kolaylaştırmanıza olanak tanıyan bir avantaj olan herhangi bir CA 'dan sertifikaları içeri aktarabilirsiniz. Sertifika sona erme tarihlerinin izlenmesini kaybetmekten endişelendiyseniz veya daha kötü bir sertifikanın zaten süresinin dolduğunu fark ediyorsanız, anahtar kasanızın sizi güncel tutmaya yardımcı olabilir. Tümleşik olmayan CA sertifikaları için, Anahtar Kasası neredeyse sona erme e-posta bildirimleri ayarlamanıza olanak sağlar. Bu tür bildirimler, birden fazla kullanıcı için de ayarlanabilir.

> [!IMPORTANT]
> Sertifika, sürümlü bir nesnedir. Geçerli sürümün süresi dolduğunda yeni bir sürüm oluşturmanız gerekir. Kavramsal olarak, her yeni sürüm, bir anahtardan oluşan yeni bir sertifikadır ve bu anahtarı bir kimliğe bağlayan Blobun. İş ortağı olmayan bir CA kullandığınızda, Anahtar Kasası bir anahtar/değer çifti oluşturur ve bir sertifika imzalama isteği (CSR) döndürür.

Tümleşik olmayan bir CA sertifikasını yenilemek için aşağıdakileri yapın:

1. Azure portal oturum açın ve ardından yenilemek istediğiniz sertifikayı açın.
1. Sertifika bölmesinde **Yeni sürüm**' ü seçin.
1. **Sertifika işlemi** seçin.
1. Yerel sürücünüze bir CSR dosyası indirmek için **CSR 'Yi indir** ' i seçin.
1. İsteği imzalamak için CSR 'yi seçtiğiniz CA 'ya gönderin.
1. İmzalanmış isteği geri getirin ve aynı sertifika işlemi bölmesinde **CSR 'Yi Birleştir** ' i seçin.

> [!NOTE]
> İmzalı CSR 'yi, oluşturduğunuz aynı CSR isteğiyle birleştirmek önemlidir. Aksi takdirde, anahtar eşleşmez.

Yeni bir CSR oluşturma hakkında daha fazla bilgi için, bkz. [Key Vault BIR CSR oluşturma ve birleştirme]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal).

## <a name="renew-a-self-signed-certificate"></a>Kendinden imzalı bir sertifikayı yenileme

Azure Key Vault Ayrıca otomatik olarak imzalanan sertifikaların otomatik yenileme de işler. Verme ilkesini değiştirme ve bir sertifikanın yaşam döngüsü özniteliklerini güncelleştirme hakkında daha fazla bilgi edinmek için bkz. [Key Vault sertifika oto döndürmeyi yapılandırma](./tutorial-rotate-certificates.md#update-lifecycle-attributes-of-a-stored-certificate).

## <a name="troubleshoot"></a>Sorun giderme
* Verilen sertifika Azure portal *devre dışı bırakılmışsa* , sertifikanın hata iletisini görüntülemek Için **sertifika işleme** ' ya gidin.
* Hata türü "sertifikanızı almak için kullanılan CSR zaten kullanıldı. Lütfen yeni bir CSR ile yeni bir sertifika oluşturmayı deneyin. "
  Sertifikanın ' Gelişmiş Ilke ' bölümüne gidin ve **' yenileme sırasında anahtarı yeniden kullan '** seçeneğinin kapalı olup olmadığını denetleyin.


## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**Sertifikanın oto döndürme özelliğini nasıl test edebilirim?**

**1 aylık** bir geçerliliği olan kendinden imzalı bir sertifika oluşturun ve ardından **%1**' deki döndürme için ömür eylemini ayarlayın. Sonraki birkaç gün içinde oluşturulan sertifika sürümü geçmişini görüntüleyebilmelisiniz.
  
**Etiketler sertifikanın otomatik yenileme sonra çoğaltılacaktır mi?**

Evet, Etiketler otomatik yenileme sonra çoğaltılır.

## <a name="next-steps"></a>Sonraki adımlar
*    [Key Vault DigiCert sertifika yetkilisi ile tümleştirin](how-to-integrate-certificate-authority.md)
*    [Öğretici: Key Vault sertifika oto döndürmeyi yapılandırma](tutorial-rotate-certificates.md)
