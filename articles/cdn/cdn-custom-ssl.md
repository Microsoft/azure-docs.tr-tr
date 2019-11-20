---
title: 'Öğretici: Azure CDN özel etki alanı üzerinde HTTPS yapılandırma | Microsoft Docs'
description: Bu öğreticide, Azure CDN uç noktası özel etki alanınızda HTTPS’yi nasıl etkinleştirip devre dışı bırakacağınızı öğrenirsiniz.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/1/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: bacd26cdba24e7ad503a3ae58d5c77d5a3311537
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177757"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Öğretici: Azure CDN özel etki alanı üzerinde HTTPS yapılandırma

Bu öğreticide bir Azure CDN uç noktası ile ilişkili özel bir etki alanı için HTTPS protokolünün nasıl etkinleştirileceği gösterilir. Özel etki alanınızda HTTPS protokolünü kullanarak (örneğin, https:\//www.contoso.com), hassas veriler internet üzerinden gönderildiğinde bunların TLS/SSL şifrelemesi ile güvenli bir şekilde teslim edilmesini sağlarsınız. Web tarayıcınız HTTPS üzerinden bir web sitesine bağlanırken, web sitesinin güvenlik sertifikasını doğrular ve bu sertifikanın yasal bir sertifika yetkilisi tarafından verildiğini doğrular. Bu işlem güvenlik sağlar ve web uygulamalarınızı saldırılara karşı korur.

Azure CDN varsayılan olarak CDN uç noktası ana bilgisayar adı üzerinde HTTPS’yi destekler. Örneğin, bir CDN uç noktası (https:\//contoso.azureedge.net gibi) oluşturursanız HTTPS otomatik olarak etkinleştirilir.  

Özel HTTPS özelliğinin en önemli niteliklerinden bazıları şunlardır:

- Ek ücret yoktur: Sertifika edinme veya yenileme işlemleri için herhangi bir maliyet söz konusu değildir ve HTTPS trafiği için ek ücret alınmaz. Yalnızca CDN’den GB çıkışı için ücret ödersiniz.

- Basit etkinleştirme: Tek tıklamayla sağlama özelliği [Azure portalından](https://portal.azure.com) kullanılabilir. Özelliği etkinleştirmek için REST API’yi veya diğer geliştirici araçlarını kullanabilirsiniz.

- Eksiksiz sertifika yönetimi kullanılabilir: Sizin için tüm sertifika tedariki ve yönetimi gerçekleştirilir. Sertifikalar sona ermeden önce otomatik olarak sağlanır ve yenilenir. Bu da sertifika süre sonu nedeniyle hizmette yaşanabilecek kesinti risklerini ortadan kaldırır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Özel etki alanınızda HTTPS protokolünü etkinleştirme.
> - Yönetilen CDN sertifikasını kullanma 
> - Kendi sertifikanızı kullanma
> - Etki alanını doğrulama
> - Özel etki alanınızda HTTPS protokolünü devre dışı bırakma.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Bu öğreticideki adımları tamamlayabilmeniz için öncelikle bir CDN profili ve en az bir CDN uç noktası oluşturmanız gerekir. Daha fazla bilgi için bkz. [Hızlı Başlangıç: Azure CDN profili ve uç noktası oluşturma](cdn-create-new-endpoint.md).

Ayrıca CDN uç noktanızda bir Azure CDN özel etki alanını ilişkilendirebilirsiniz. Daha fazla bilgi için bkz. [Öğretici: Azure CDN uç noktanıza özel etki alanı ekleme](cdn-map-content-to-custom-domain.md) 

> [!IMPORTANT]
> CDN ile yönetilen sertifikalar, kök veya tepesinde etki alanları için kullanılamaz. Azure CDN özel etki alanınız bir kök veya tepesinde etki alanı ise kendi sertifikanızı getir özelliğini kullanmanız gerekir. 
>

---

## <a name="ssl-certificates"></a>SSL sertifikaları
Bir Azure CDN özel etki alanında içeriği güvenli bir şekilde teslim etmek üzere HTTPS protokolünü etkinleştirmek için SSL sertifikası kullanmanız gerekir. Azure CDN tarafından yönetilen bir sertifika kullanmayı seçebilir ya da kendi sertifikanızı kullanabilirsiniz.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificatetaboption-1-default-enable-https-with-a-cdn-managed-certificate"></a>[1. Seçenek (varsayılan): Yönetilen CDN sertifikasıyla HTTPS’yi etkinleştirme](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

CDN tarafından yönetilen bir sertifika kullandığınızda HTTPS özelliği sadece birkaç tıklama ile etkinleştirilebilir. Azure CDN, tedarik ve yenileme gibi sertifika yönetimi görevlerini tamamen gerçekleştirir. Özelliği etkinleştirmenizin ardından işlem hemen başlar. Özel etki alanı önceden CDN uç noktasına eşlendiyse başka bir eylem gerekmez. Azure CDN, adımları işler ve isteğinizi otomatik olarak tamamlar. Ancak özel etki alanınız başka bir yerde eşlendiyse, etki alanı sahipliğinizi doğrulamak için e-posta kullanmanız gerekir.

Özel bir etki alanı üzerinde HTTPS'yi etkinleştirmek için aşağıdaki adımları uygulayın:

1. [Azure Portal](https://portal.azure.com), **Microsoft 'un Azure CDN Standart** **Azure CDN**, Verizon profilinden Verizon veya **Azure CDN Premium** 'dan standart olan **Azure CDN** standart bir şekilde inceleyin.

2. CDN uç noktaları listesinde özel etki alanınızı içeren uç noktayı seçin.

    ![Uç noktalar listesi](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    **Uç Nokta** sayfası görünür.

3. Özel etki alanları listesinde, HTTPS'yi etkinleştirmek istediğiniz özel etki alanını seçin.

    ![Özel etki alanları listesi](./media/cdn-custom-ssl/cdn-custom-domain.png)

    **Özel etki alanı** sayfası görünür.

4. Sertifika yönetimi türü bölümünde **Yönetilen CDN**’yi seçin.

5. HTTPS’yi etkinleştirmek için **Açık** seçeneğini belirleyin.

    ![Özel etki alanı HTTPS durumu](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

6. [Etki alanını doğrulama](#validate-the-domain) adımına ilerleyin.


# <a name="option-2-enable-https-with-your-own-certificatetaboption-2-enable-https-with-your-own-certificate"></a>[2. Seçenek: Kendi sertifikanızla HTTPS’yi etkinleştirme](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Bu seçenek yalnızca **Microsoft 'tan Azure CDN** ve **Verizon profillerden Azure CDN** ile kullanılabilir. 
>
 
HTTPS özelliğini etkinleştirmek için kendi sertifikanızı kullanabilirsiniz. Bu işlem, sertifikalarınızı güvenli bir şekilde depolamanıza olanak tanıyan Azure Key Vault ile tümleştirme yoluyla gerçekleştirilir. Azure CDN, sertifikanızı almak için bu güvenli mekanizmayı kullanır ve birkaç ek adım gerektirir. SSL sertifikanızı oluştururken, bunu izin verilen bir sertifika yetkilisiyle (CA) oluşturmanız gerekir. Buna karşılık, izin verilmeyen bir CA kullanırsanız isteğiniz reddedilir. İzin verilen CA 'Ların bir listesi için bkz. [Azure CDN için özel https 'yi etkinleştirmek Için Izin verilen sertifika yetkilileri](cdn-troubleshoot-allowed-ca.md). **Verizon 'den Azure CDN**için geçerli CA 'lar kabul edilecektir. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure Key Vault hesabınızı ve sertifikanızı hazırlama
 
1. Azure Key Vault: Özel HTTPS’yi etkinleştirmek istediğiniz Azure CDN profili ve CDN uç noktalarıyla aynı abonelik altında çalışan bir Azure Key Vault hesabınız olması gerekir. Azure Key Vault hesabınız yoksa, oluşturun.
 
2. Azure Key Vault sertifikaları: Zaten bir sertifikanız varsa, bu sertifikayı doğrudan Azure Key Vault hesabınıza yükleyebilir veya doğrudan Azure Key Vault’un tümleştirildiği iş ortağı CA'lardan birinin Azure Key Vault’u üzerinden yeni bir sertifika oluşturabilirsiniz. 

### <a name="register-azure-cdn"></a>Azure CDN’yi kaydetme

PowerShell aracılığıyla Azure CDN’yi Azure Active Directory’nizdeki bir uygulama olarak kaydedin.

1. Gerekirse, yerel makinenize [Azure PowerShell](/powershell/azure/install-az-ps) ' yi yüklemelisiniz.

2. PowerShell’de aşağıdaki komutu çalıştırın:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![PowerShell’de Azure CDN’ye kaydolun](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Azure CDN’ye anahtar kasanıza erişim yetkisi verme
 
Azure CDN’ye, Azure Key Vault hesabınızdaki sertifikalara (gizli dizi) erişme yetkisi verin.

1. Anahtar kasası hesabınızda AYARLAR bölümünden **Erişim ilkeleri**’ni ve sonra **Yeni ekle**’yi seçip yeni bir ilke oluşturun.

    ![Yeni erişim ilkesi oluşturma](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. **Sorumlu seçin** alanında **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** araması yapın ve **Microsoft.Azure.Cdn**'yi seçin. **Seç**'e tıklayın.

    ![Erişim ilkesi ayarları](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. **Sertifika izinleri**' ni seçin ve ardından, CDN 'nin sertifikaları almak ve listelemek üzere bu izinleri gerçekleştirmesini sağlamak için **Al** ve **Listele** onay kutularını seçin.

4. **Gizli izinler**' i seçin ve ardından CDN 'nin gizli dizileri almak ve listelemek üzere bu izinleri gerçekleştirmesini sağlamak için **Al** ve **Listele** onay kutularını seçin.

5. **Tamam**’ı seçin. 

    Azure CDN artık bu anahtar kasasına ve bu anahtar kasasında depolanan sertifikalara (gizli diziler) erişebilir.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Azure CDN’nin dağıtacağı sertifikayı seçme
 
1. Azure CDN portalına geri dönün ve özel HTTPS’yi etkinleştirmek istediğiniz profili ve CDN uç noktasını seçin. 

2. Özel etki alanları listesinde, HTTPS'yi etkinleştirmek istediğiniz özel etki alanını seçin.

    **Özel etki alanı** sayfası görünür.

3. Sertifika yönetimi türü bölümünde **Kendi sertifikamı kullan**’ı seçin. 

    ![Sertifikanızı yapılandırma](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Bir anahtar kasası, sertifika (gizli gizi) ve sertifika sürümü seçin.

    Azure CDN, aşağıdaki bilgileri listeler: 
    - Abonelik kimliğiniz için anahtar kasası hesapları. 
    - Seçilen anahtar kasası altındaki sertifikalar (gizli diziler). 
    - Kullanılabilir sertifika sürümleri. 
 
5. HTTPS’yi etkinleştirmek için **Açık** seçeneğini belirleyin.
  
6. Kendi sertifikanızı kullanıyorsanız etki alanı doğrulaması gerekmez. [Yayılma için bekleme](#wait-for-propagation) adımına geçin.

---

## <a name="validate-the-domain"></a>Etki alanını doğrulama

CNAME kaydı kullanılarak özel uç noktanızla eşlenen ve kullanılmakta olan bir özel etki alanınız zaten varsa ya da kendi sertifikanızı kullanıyorsanız şu adıma geçin:  
[Özel etki alanı, CDN uç noktanızla eşlendi](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). Böyle bir etki alanınız yoksa uç noktanıza yönelik CNAME kaydı girişinin artık mevcut olmaması veya cdnverify alt etki alanını içeriyor olması durumunda [Özel etki alanı, CDN uç noktanızla eşlenmedi](#custom-domain-is-not-mapped-to-your-cdn-endpoint) adımına geçin.

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Özel etki alanı bir CNAME kaydı kullanılarak CDN uç noktanızla eşlendi

Uç noktanıza özel etki alanı eklediğinizde etki alanı kayıt yetkilinizin DNS tablosunda, CDN uç noktası ana bilgisayar adınızla eşlenecek bir CNAME kaydı oluşturmuş oldunuz. Bu CNAME kaydı hala mevcutsa ve cdnverify alt etki alanını içermiyorsa, DigiCert CA özel etki alanınızın sahipliğini otomatik olarak doğrulamak için bunu kullanır. 

Kendi sertifikanızı kullanıyorsanız etki alanı doğrulaması gerekmez.

CNAME kaydınız, *Ad*’ın özel etki alanınız, *Değer*’in ise CDN uç noktası ana bilgisayar adınız olduğu aşağıdaki biçimde olmalıdır:

| Adı            | Tür  | Değer                 |
|-----------------|-------|-----------------------|
| < www. contoso. com > | CNAME | contoso.azureedge.net |

CNAME kayıtları hakkında daha fazla bilgi için bkz. [CNAME DNS kaydı oluşturma](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

CNAME kaydınız doğru biçimdeyse DigiCert, özel etki alanı adınızı otomatik olarak doğrular ve etki alanı adınız için ayrılmış bir sertifika oluşturur. DigitCert size doğrulama e-postası göndermez ve isteğinizi onaylamanız gerekmez. Sertifika bir yıl süreyle geçerlidir ve süresi dolmadan önce otomatik olarak yenilenir. [Yayılma için bekleme](#wait-for-propagation) adımına geçin. 

Otomatik doğrulama genellikle birkaç saat sürer. Etki alanınızı 24 saat içinde doğrulanıp görmüyorsanız bir destek bileti açın.

>[!NOTE]
>DNS sağlayıcınız ile bir Sertifika Yetkilisi Yetkilendirme (CAA) kaydınız varsa bunun geçerli CA olarak DigiCert‘i içermesi gerekir. CAA kaydı; etki alanı sahiplerinin DNS sağlayıcıları ile hangi CA'ların, etki alanları için sertifika vermeye yetkili olduğunu belirtmesini sağlar. Bir CA, CAA kaydına sahip bir etki alanı için sertifika siparişi alırsa ve bu CA, sertifika vermeye yetkili olarak listelenmemişse bu CA’nın, söz konusu etki alanına veya alt etki alanına sertifika vermesi yasaklanır. CAA kayıtlarını yönetme ile ilgili bilgi için bkz. [CAA kayıtlarını yönetme](https://support.dnsimple.com/articles/manage-caa-record/). CAA kayıt aracı için bkz. [CAA Kayıt Yardımcısı](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Özel etki alanı, CDN uç noktanızla eşlenmedi

>[!NOTE]
>**Akamai ' dan Azure CDN**kullanıyorsanız, otomatik etki alanı doğrulamasını etkinleştirmek IÇIN aşağıdaki CNAME ayarlanmalıdır. "_acme-Challenge. &lt;Özel etki alanı ana bilgisayar adı @ no__t-1-> CNAME-> &lt;Özel etki alanı hostname&gt;.ak-acme-challenge.azureedge.net"

CNAME kayıt girişi cdnverify alt etki alanını içeriyorsa, bu adımdaki yönergelerin geri kalanını izleyin.

DigiCert aşağıdaki e-posta adreslerine bir doğrulama e-postası gönderir. Aşağıdaki adreslerden birini doğrudan onaylayabildiğinizi doğrulayın:

admin@&lt;etki-alanı-adınız.com&gt;  
administrator@&lt;etki-alanı-adınız.com&gt;  
webmaster@&lt;etki-alanı-adınız.com&gt;  
hostmaster@&lt;etki-alanı-adınız.com&gt;  
postmaster@&lt;.com&gt;  

Birkaç dakika içinde sizden isteği onaylamanızı isteyen, aşağıdaki örneğe benzer bir e-posta alırsınız. İstenmeyen posta filtresi kullanıyorsanız verification@digicert.com adresini bu filtrenin beyaz listesine ekleyin. E-postayı 24 saat içinde almazsanız Microsoft destek ekibine başvurun.
    
![Etki alanı doğrulama e-postası](./media/cdn-custom-ssl/domain-validation-email.png)

Onay bağlantısına tıkladığınızda, aşağıdaki çevrimiçi onay formuna yönlendirilirsiniz: 
    
![Etki alanı doğrulama formu](./media/cdn-custom-ssl/domain-validation-form.png)

Formdaki yönergeleri uygulayın. İki doğrulama seçeneğiniz vardır:

- contoso.com gibi aynı kök etki alanı için aynı hesap üzerinden verilen gelecekteki tüm siparişleri onaylayabilirsiniz. Aynı kök etki alanı için ek özel etki alanları eklemeyi planlıyorsanız bu yaklaşım önerilir.

- Yalnızca bu istekte kullanılan söz konusu ana bilgisayar adını onaylayabilirsiniz. Sonraki istekler için ek onay gereklidir.

DigiCert onaydan sonra özel etki alanı adınız için sertifika oluşturma işlemlerini tamamlar. Sertifika bir yıl süreyle geçerlidir ve süresi dolmadan önce otomatik olarak yenilenir.

## <a name="wait-for-propagation"></a>Yayılma için bekleme

Etki alanı doğrulandıktan sonra özel etki alanı HTTPS özelliğinin etkinleştirilmesi 6-8 saate kadar sürebilir. İşlem tamamlandığında, Azure portalındaki özel HTTPS durumu **Etkin** olarak ayarlanır ve özel etki alanı iletişim kutusundaki dört işlem adımı tamamlandı olarak işaretlenir. Özel etki alanınız artık HTTPS’yi kullanmak için hazırdır.

![HTTPS iletişim kutusunu etkinleştirme](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>İşlem ilerleme durumu

Aşağıdaki tabloda, HTTPS’yi etkinleştirdiğinizde oluşan işlem ilerleme durumunu gösterir. HTTPS’yi etkinleştirmenizin ardından özel etki alanı iletişim kutusunda dört işlem adımı görünür. Her adım etkin hale gelir ve ilerledikçe adımın altında ek alt adım ayrıntıları görüntülenir. Bu alt adımların hiçbiri gerçekleşmez. Bir adım başarıyla tamamlandıktan sonra adımın yanında yeşil bir onay işareti görünür. 

| İşlem adımı | İşlem alt adımı ayrıntıları | 
| --- | --- |
| 1 İstek gönderiliyor | İstek gönderiliyor |
| | HTTPS isteğiniz gönderiliyor. |
| | HTTPS isteğiniz başarıyla gönderildi. |
| 2 Etki alanı doğrulaması | CNAME için CDN Uç Noktası ile eşleme yapıldıysa etki alanı otomatik olarak doğrulanır. Eşleme yapılmadıysa etki alanınızın kayıt kaydında listelenen e-posta adresine (WHOIS kayıt şirketi) bir doğrulama isteği gönderilir. Lütfen etki alanını mümkün olan en kısa süre içinde doğrulayın. |
| | Etki alanı sahipliğiniz başarıyla doğrulandı. |
| | Etki alanı doğrulama isteğinin süresi doldu. (Müşteri büyük olasılıkla 6 gün içinde yanıt vermedi.) HTTPS, etki alanınızda etkinleştirilmeyecek. * |
| | Etki alanı sahipliğini doğrulama isteği, müşteri tarafından reddedildi. HTTPS, etki alanınızda etkinleştirilmeyecek. * |
| 3 Sertifika sağlanıyor | Sertifika yetkilisi şu anda etki alanınızdaki HTTPS'nin etkinleştirilmesi için gereken sertifikayı veriyor. |
| | Sertifika verildi ve şu anda CDN ağına dağıtılıyor. Bu işlem 6 saat kadar sürebilir. |
| | Sertifika, CDN ağına başarıyla dağıtıldı. |
| 4 Tamamlandı | HTTPS, etki alanınızda başarıyla etkinleştirildi. |

\* Hata oluşmadığı sürece bu ileti görüntülenmez. 

İstek gönderilmeden önce hata oluşursa, aşağıdaki hata iletisi görüntülenir:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Kaynakları temizleme - HTTPS’yi devre dışı bırakma

Önceki adımlarda özel etki alanınızda HTTPS protokolünü etkinleştirdiniz. Artık HTTPS ile özel etki alanınızı kullanmak istiyorsanız, aşağıdaki adımları uygulayarak HTTPS’yi devre dışı bırakabilirsiniz:

### <a name="disable-the-https-feature"></a>HTTPS özelliğini devre dışı bırakma 

1. [Azure portalında](https://portal.azure.com), **Microsoft’tan Azure CDN Standart**, **Verizon’dan Azure CDN Standart** veya **Verizon’dan Azure CDN Premium** profilinize gidin.

2. Uç noktalar listesinde, özel etki alanınızı içeren uç noktaya tıklayın.

3. HTTPS’yi devre dışı bırakmak istediğiniz özel etki alanına tıklayın.

    ![Özel etki alanları listesi](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. HTTPS’yi devre dışı bırakmak için **Kapalı**’ya ve sonra **Uygula**’ya tıklayın.

    ![Özel HTTPS iletişim kutusu](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Yayılma için bekleme

Özel etki alanı HTTPS özelliği devre dışı bırakıldıktan sonra bu işlemin geçerli olması 6-8 saate kadar sürebilir. İşlem tamamlandığında, Azure portalındaki özel HTTPS durumu **Devre Dışı** olarak ayarlanır ve özel etki alanı iletişim kutusundaki üç işlem adımı tamamlandı olarak işaretlenir. Özel etki alanınız artık HTTPS’yi kullanamaz.

![HTTPS iletişim kutusunu devre dışı bırakma](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>İşlem ilerleme durumu

Aşağıdaki tabloda, HTTPS’yi devre dışı bıraktığınızda oluşan işlem ilerleme durumunu gösterir. HTTPS’yi devre dışı bırakmanızın ardından özel etki alanı iletişim kutusunda üç işlem adımı görünür. Her adım etkin hale gelir ve adımın altında ek ayrıntılar görüntülenir. Bir adım başarıyla tamamlandıktan sonra adımın yanında yeşil bir onay işareti görünür. 

| İşlem ilerleme durumu | İşlem ayrıntıları | 
| --- | --- |
| 1 İstek gönderiliyor | İsteğiniz gönderiliyor |
| 2 Sertifika sağlaması kaldırılıyor | Sertifika siliniyor |
| 3 Tamamlandı | Sertifika silindi |

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

1. *Sertifika sağlayıcısı kimdir ve ne tür bir sertifika kullanılır?*

    Hem **Azure CDN from Verizon** hem de **Azure CDN from Microsoft** için özel etki alanınızda Digicert tarafından sağlanan ayrılmış/tek sertifika sağlanır. 

2. *IP tabanlı veya SNI TLS/SSL kullanıyor musunuz?*

    Hem **Azure CDN from Verizon** hem de **Azure CDN Standard from Microsoft** için SNI TLS/SSL kullanın.

3. *DigiCert’ten etki alanı doğrulama e-postası almazsam ne olur?*

    Özel etki alanınız için doğrudan uç nokta ana bilgisayar adına işaret eden bir CNAME girişiniz varsa (ve cdnverify alt etki alanı adını kullanmıyorsanız) etki alanı doğrulama e-postası almazsınız. Doğrulama otomatik olarak gerçekleşir. Ancak CNAME girişiniz yoksa ve e-postayı 24 saat içinde almazsanız Microsoft destek ekibine başvurun.

4. *Ayrılmış sertifika kullanmak, SAN sertifikasından daha mı güvenlidir?*
    
    SAN sertifikası, ayrılmış sertifika ile aynı şifreleme ve güvenlik standartlarını uygular. Verilen tüm SSL sertifikaları, gelişmiş sunucu güvenliği için SHA-256 standardını kullanır.

5. *DNS sağlayıcım ile Sertifika Yetkilisi Yetkilendirme kaydı kullanmam gerekir mi?*

    Hayır, Sertifika Yetkilisi Yetkilendirme kaydı şu anda gerekli değildir. Ancak, varsa, geçerli CA olarak DigiCert’i içermelidir.

6. *20 Haziran 2018 ' de, varsayılan olarak SNı TLS/SSL ile ayrılmış bir sertifika kullanarak Verizon 'tan Azure CDN başladı. Konu diğer adları (SAN) sertifikası ve IP tabanlı TLS/SSL kullanarak var olan özel etki alanlarıma ne olur?*

    Microsoft, uygulamanıza yalnızca SNI istemci isteklerinin gönderildiğini algılarda mevcut etki alanlarınız önümüzdeki aylarda kademeli olarak tek sertifikaya geçirilecektir. Microsoft, uygulamanıza SNI harici istemci isteklerinin gönderildiğini algılarsa etki alanlarınız IP tabanlı TLS/SSL ile SAN sertifikasında kalacaktır. Her durumda hizmetinizde veya SNI ya da SNI harici olduğuna bakılmaksızın istemci istekleriniz için sunulan destekte kesinti olmayacaktır.

7. *Sertifika yenilemesi kendi sertifikanızı getir ile nasıl çalışır?*

    Yeni bir sertifikanın PoP altyapısına dağıtılmasını sağlamak için yeni sertifikanızı Azure Anahtar Kasası 'na yüklemeniz, ardından Azure CDN SSL ayarlarınızda en yeni sertifika sürümünü seçip Kaydet ' e tıklayın. Azure CDN, yeni güncelleştirilmiş CERT propogate. 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> - Özel etki alanınızda HTTPS protokolünü etkinleştirme.
> - Yönetilen CDN sertifikasını kullanma 
> - Kendi sertifikanızı kullanma
> - Etki alanını doğrulama.
> - Özel etki alanınızda HTTPS protokolünü devre dışı bırakma.

CDN uç noktanızda önbelleğe almayı yapılandırma hakkında bilgi edinmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Öğretici: Azure CDN önbelleğe alma kurallarını ayarlama](cdn-caching-rules-tutorial.md)

