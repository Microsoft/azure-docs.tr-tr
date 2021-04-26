---
title: Azure ön kapısı Standart/Premium SKU yapılandırmasındaki özel etki alanınız için HTTPS 'yi yapılandırma
description: Bu makalede, Azure ön kapısının Standart/Premium SKU 'suna özel bir etki alanı eklemeyi öğreneceksiniz.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 3f3b4d3e431d9e24549bdb8caa4b2d17f547c82c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064061"
---
# <a name="configure-https-on-a-front-door-standardpremium-sku-preview-custom-domain-using-the-azure-portal"></a>Ön kapı Standart/Premium SKU (Önizleme) özel etki alanında HTTPS 'yi Azure portal kullanarak yapılandırın

> [!NOTE]
> Bu belge, Azure ön kapısının Standart/Premium (Önizleme) içindir. Azure ön kapısı hakkında bilgi mi arıyorsunuz? [Burada](../front-door-overview.md)görüntüleyin.

Azure ön kapısının Standart/Premium, özel bir etki alanı eklendiğinde uygulamalarınıza varsayılan olarak güvenli TLS teslimi sağlar. Özel etki alanında HTTPS protokolünü kullanarak, hassas verilerinizin internet üzerinden gönderildiğinde TLS/SSL şifrelemesiyle güvenli bir şekilde teslim edildiğini güvence altına almalısınız. Web tarayıcınız HTTPS üzerinden bir web sitesine bağlanırken, web sitesinin güvenlik sertifikasını onaylar ve bu sertifikanın yasal bir sertifika yetkilisi tarafından verildiğini doğrular. Bu işlem güvenlik sağlar ve web uygulamalarınızı saldırılara karşı korur.

Azure ön kapısının Standart/Premium, hem Azure tarafından yönetilen sertifikayı hem de müşteri tarafından yönetilen sertifikaları destekler. Azure ön kapısı, varsayılan olarak Azure yönetilen sertifikaları kullanarak tüm özel etki alanlarınızda HTTPS 'yi otomatik olarak sağlar. Azure yönetilen sertifikasını almak için ek adım gerekmez. Etki alanı doğrulama işlemi sırasında bir sertifika oluşturulur. Azure ön kapısı Standart/Premium 'u Key Vault ile tümleştirerek kendi sertifikanızı da kullanabilirsiniz.

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [**Microsoft Azure önizlemeleri Için ek kullanım koşulları**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

* Özel etki alanınız için HTTPS 'yi yapılandırmadan önce, önce bir Azure ön kapısı Standart/Premium profili oluşturmanız gerekir. Daha fazla bilgi için bkz. [hızlı başlangıç: Azure ön kapısı Standart/Premium profili oluşturma](create-front-door-portal.md).

* Zaten özel bir etki alanınız yoksa, önce bir etki alanı sağlayıcısıyla bir tane satın almanız gerekir. Örneğin bkz. [Özel etki alanı adı satın alma](../../app-service/manage-custom-dns-buy-domain.md).

* [DNS etki alanlarınızı](../../dns/dns-overview.md)barındırmak için Azure kullanıyorsanız, etki alanı sağlayıcının etki alanı adı sistemi 'NI (DNS) bir Azure DNS temsilci olarak oluşturmanız gerekir. Daha fazla bilgi için bkz. [Bir etki alanını Azure DNS'ye devretme](../../dns/dns-delegate-domain-azure-dns.md). Aksi takdirde, DNS etki alanınızı işlemek için bir etki alanı sağlayıcısı kullanıyorsanız, istenen DNS TXT kayıtlarını girerek etki alanını el ile doğrulamanız gerekir.

## <a name="azure-managed-certificates"></a>Azure yönetilen sertifikaları

1. Azure ön kapısının Standart/Premium profilinizin ayarları ' nın altında **etki alanları** ' nı seçin ve ardından **+ Ekle** ' yi seçerek yeni bir etki alanı ekleyin.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-new-custom-domain.png" alt-text="Etki alanı yapılandırma giriş sayfasının ekran görüntüsü.":::

1. **Etki alanı Ekle** sayfasında, *DNS yönetimi* için **Azure yönetilen DNS** seçeneğini belirleyin. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-domain-azure-managed.png" alt-text="Azure Managed DNS seçiliyken etki alanı Ekle sayfasının ekran görüntüsü.":::

1. Özel [etki alanını](how-to-add-custom-domain.md)etkinleştirme bölümündeki adımları izleyerek özel etki alanını doğrulayıp bir uç noktayla ilişkilendirin.

1. Özel etki alanı bitiş noktasıyla başarıyla ilişkilendirildiğinde, bir Azure yönetilen sertifikası ön kapıya dağıtılır. Bu işlemin tamamlanması birkaç dakika sürebilir.

## <a name="using-your-own-certificate"></a>Kendi sertifikanızı kullanma

Ayrıca, kendi TLS sertifikanızı kullanmayı da seçebilirsiniz. Bu sertifika, Azure ön kapısının Standart/Premium ile kullanabilmeniz için bir Azure Key Vault içeri aktarılmalıdır. Bkz. Azure Key Vault [sertifikayı içeri aktarma](../../key-vault/certificates/tutorial-import-certificate.md) . 

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure Key Vault hesabınızı ve sertifikanızı hazırlama
 
1. Özel HTTPS 'yi etkinleştirmek istediğiniz Azure ön kapısı Standart/Premium ile aynı abonelikte çalışan bir Azure Key Vault hesabınız olmalıdır. Azure Key Vault hesabınız yoksa oluşturun.

    > [!WARNING]
    > Azure ön kapısı şu anda yalnızca ön kapı yapılandırmasıyla aynı abonelikteki Key Vault hesaplarını desteklemektedir. Azure ön kapısından veya Premium 'dan farklı bir abonelik altında Key Vault seçilmesi hataya neden olur.

1. Zaten bir sertifikanız varsa, bunu doğrudan Azure Key Vault hesabınıza yükleyebilirsiniz. Aksi takdirde, Azure Key Vault tümleştirilebilen ortak sertifika yetkililerinden birinden Azure Key Vault doğrudan yeni bir sertifika oluşturun. Sertifikanızı **gizli** değil bir **sertifika** nesnesi olarak karşıya yükleyin.

    > [!NOTE]
    > Kendi TLS/SSL sertifikanız için, ön kapı EC şifreleme algoritmalarına sahip sertifikaları desteklemez.

#### <a name="register-azure-front-door"></a>Azure ön kapısını Kaydet

Azure ön kapısının hizmet sorumlusunu PowerShell aracılığıyla Azure Active Directory bir uygulama olarak kaydedin.

> [!NOTE]
> Bu eylem, genel yönetici izinleri gerektirir ve her kiracı için yalnızca **bir kez** gerçekleştirilmesi gerekir.

1. Gerekirse yerel makinenizdeki PowerShell’de [Azure PowerShell](/powershell/azure/install-az-ps)’i yükleyin.

1. PowerShell’de aşağıdaki komutu çalıştırın:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Anahtar kasanıza Azure ön kapısına erişim izni verin
 
Azure Key Vault hesabınızdaki sertifikalara erişmek için Azure ön kapısına izin verin.

1. Anahtar Kasası hesabınızda, Ayarlar ' ın altında, **erişim ilkeleri**' ni seçin. Yeni bir ilke oluşturmak için **Yeni Ekle** ' yi seçin.

1. **Asıl seçin** bölümünde, **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** araması yapın ve * * Microsoft. Azurefrontkapısı-CDN * * öğesini seçin. **Seç**’e tıklayın.

1. **Gizli izinler**' de, sertifikayı almak Için ön kapıya izin vermek için **Al** ' ı seçin.

1. Sertifika **izinleri**' nde, sertifikayı almak Için ön kapıya izin vermek için **Al** ' ı seçin.

1. **Tamam**’ı seçin. 

#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Dağıtılacak Azure ön kapısının sertifikasını seçin
 
1. Portalda Azure ön kapı Standart/Premium 'a geri dönün.

1. *Ayarlar* altındaki **gizlilikler** ' a gidin ve **sertifika ekle**' yi seçin.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate.png" alt-text="Azure ön kapı gizli giriş sayfasının ekran görüntüsü.":::

1. **Sertifika ekle** sayfasında, Azure ön kapısı Standart/Premium 'a eklemek istediğiniz sertifika onay kutusunu seçin. Sürüm seçimini "en son" olarak bırakın ve **Ekle**' yi seçin. 

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-certificate-page.png" alt-text="Sertifika Ekle sayfasının ekran görüntüsü.":::

1. Sertifika başarıyla sağlandığında, yeni bir özel etki alanı eklediğinizde kullanabilirsiniz.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/successful-certificate-provisioned.png" alt-text="Gizli dizi listesine başarıyla eklenen sertifika ekran görüntüsü.":::

1. *Ayar* altındaki **etki alanları** ' na gidin ve yeni bir özel etki alanı eklemek için **+ Ekle** ' yi seçin. **Etki alanı Ekle** sayfasında, *https* Için "kendi sertifikanızı getir (BYOC)" öğesini seçin. *Gizli* dizi için, açılan listeden kullanmak istediğiniz sertifikayı seçin. 

    > [!NOTE]
    > Seçili sertifikanın, eklenmekte olan özel etki alanı ile aynı ortak bir adı (CN) olmalıdır.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/add-custom-domain-https.png" alt-text="HTTPS ile özel etki alanı ekleme sayfasının ekran görüntüsü.":::

1. Sertifikayı doğrulamak için ekrandaki adımları izleyin. Ardından, yeni oluşturulan özel etki alanını [özel bir etki alanı kılavuzu oluşturma](how-to-add-custom-domain.md) bölümünde açıklandığı gibi bir uç noktayla ilişkilendirin.

#### <a name="change-from-azure-managed-to-bring-your-own-certificate-byoc"></a>Kendi sertifikanızı getirmek için Azure 'un yönettiği değişiklikler (BYOC)

1. Sertifika **ayrıntıları** sayfasını açmak için sertifika durumunu seçerek, mevcut bir Azure yönetilen sertifikasını Kullanıcı tarafından yönetilen bir sertifikayla değiştirebilirsiniz.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/domain-certificate.png" alt-text="Etki alanları giriş sayfasında sertifika durumunun ekran görüntüsü." lightbox="../media/how-to-configure-https-custom-domain/domain-certificate-expanded.png":::

1. **Sertifika ayrıntıları** sayfasında, "Azure yönetilen" Iken "kendi sertifikanızı getir (BYOC)" seçeneğini değiştirebilirsiniz. Daha sonra bir sertifika seçmek için aynı adımları izleyin. İlişkili sertifikayı bir etki alanıyla değiştirmek için **Güncelleştir** ' i seçin.

    :::image type="content" source="../media/how-to-configure-https-custom-domain/certificate-details-page.png" alt-text="Sertifika Ayrıntıları sayfasının ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

[Azure ön kapısı Standart/Premium ile önbelleğe alma](concept-caching.md)hakkında bilgi edinin.
