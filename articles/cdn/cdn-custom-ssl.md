---
title: 'Öğretici: Azure CDN özel etki alanı üzerinde HTTPS yapılandırma'
description: Bu öğreticide, Azure CDN uç noktası özel etki alanınızda HTTPS’yi nasıl etkinleştirip devre dışı bırakacağınızı öğrenirsiniz.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 6f77bac93b7bb5e3319409c01e328c73cd08a9a0
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058961"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Öğretici: Azure CDN özel etki alanı üzerinde HTTPS yapılandırma

Bu öğreticide bir Azure CDN uç noktası ile ilişkili özel bir etki alanı için HTTPS protokolünün nasıl etkinleştirileceği gösterilir. 

Özel etki alanındaki HTTPS protokolü (örneğin, https: \/ /www.contoso.com), hassas VERILERINIZIN TLS/SSL aracılığıyla güvenli bir şekilde teslim edilmesini sağlar. Web tarayıcınız HTTPS aracılığıyla bağlandığında, tarayıcı web sitesinin sertifikasını doğrular. Tarayıcı, meşru bir sertifika yetkilisi tarafından verildiğini doğrular. Bu işlem güvenlik sağlar ve web uygulamalarınızı saldırılara karşı korur.

Azure CDN varsayılan olarak CDN uç noktası ana bilgisayar adı üzerinde HTTPS’yi destekler. Örneğin, bir CDN uç noktası (https:\//contoso.azureedge.net gibi) oluşturursanız HTTPS otomatik olarak etkinleştirilir.  

Özel HTTPS özelliğinin en önemli niteliklerinden bazıları şunlardır:

- Ek maliyet yok: sertifika alma veya yenileme maliyetleri yoktur ve HTTPS trafiği için ek maliyet yoktur. Yalnızca CDN’den GB çıkışı için ücret ödersiniz.

- Basit etkinleştirme: Tek tıklamayla sağlama özelliği [Azure portalından](https://portal.azure.com) kullanılabilir. Özelliği etkinleştirmek için REST API’yi veya diğer geliştirici araçlarını kullanabilirsiniz.

- Sertifika yönetiminin tamamı kullanılabilir: 
    * Tüm sertifika tedarik ve yönetimi sizin için gerçekleştirilir. 
    * Sertifikalar, süresi dolmadan otomatik olarak sağlanır ve yenilenir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> - Özel etki alanınızda HTTPS protokolünü etkinleştirme.
> - Yönetilen CDN sertifikasını kullanma 
> - Kendi sertifikanızı kullanma
> - Etki alanını doğrulama
> - Özel etki alanınızda HTTPS protokolünü devre dışı bırakma.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Bu öğreticideki adımları tamamlayabilmeniz için önce bir CDN profili ve en az bir CDN uç noktası oluşturun. Daha fazla bilgi için bkz. [Hızlı Başlangıç: Azure CDN profili ve uç noktası oluşturma](cdn-create-new-endpoint.md).

CDN uç noktanıza Azure CDN özel bir etki alanını ilişkilendirin. Daha fazla bilgi için bkz. [öğretici: Azure CDN uç noktanıza özel etki alanı ekleme](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> CDN ile yönetilen sertifikalar, kök veya tepesinde etki alanları için kullanılamaz. Azure CDN özel etki alanınız bir kök veya tepesinde etki alanı ise kendi sertifikanızı getir özelliğini kullanmanız gerekir. 
>

---

## <a name="tlsssl-certificates"></a>TLS/SSL sertifikaları

Azure CDN özel bir etki alanında HTTPS 'yi etkinleştirmek için bir TLS/SSL sertifikası kullanırsınız. Azure CDN tarafından yönetilen bir sertifika kullanmayı veya sertifikanızı kullanmayı tercih edersiniz.

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[1. Seçenek (varsayılan): Yönetilen CDN sertifikasıyla HTTPS’yi etkinleştirme](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Azure CDN, tedarik ve yenileme gibi sertifika yönetimi görevlerini işler. Özelliği etkinleştirmenizin ardından işlem hemen başlar. 

Özel etki alanı zaten CDN uç noktasıyla eşlenmişse, başka bir eylem gerekmez. Azure CDN, adımları işler ve isteğinizi otomatik olarak tamamlar.

Özel etki alanınız başka bir yerde eşlenmişse, etki alanı sahipliğini doğrulamak için e-posta ' yı kullanın.

Özel bir etki alanı üzerinde HTTPS'yi etkinleştirmek için aşağıdaki adımları uygulayın:

1. Azure CDN tarafından yönetilen bir sertifikayı bulmak için [Azure Portal](https://portal.azure.com) gidin. **CDN profillerini** arayın ve seçin. 

2. Profilinizi seçin:
    * **Microsoft 'tan Azure CDN Standart**
    * **Akamai 'ten standart Azure CDN**
    * **Verizon 'ten standart Azure CDN**
    * **Verizon 'ten Premium Azure CDN**

3. CDN uç noktaları listesinde özel etki alanınızı içeren uç noktayı seçin.

    ![Uç noktalar listesi](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    **Uç Nokta** sayfası görünür.

4. Özel etki alanları listesinde, HTTPS'yi etkinleştirmek istediğiniz özel etki alanını seçin.

    ![Ekran görüntüsü, kendi sertifikamı kullanma seçeneğiyle özel etki alanı sayfasını gösterir.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    **Özel etki alanı** sayfası görünür.

5. Sertifika yönetimi türü bölümünde **Yönetilen CDN**’yi seçin.

6. HTTPS’yi etkinleştirmek için **Açık** seçeneğini belirleyin.

    ![Özel etki alanı HTTPS durumu](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. [Etki alanını doğrulamaya](#validate-the-domain)devam edin.


# <a name="option-2-enable-https-with-your-own-certificate"></a>[2. Seçenek: Kendi sertifikanızla HTTPS’yi etkinleştirme](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Bu seçenek yalnızca **Microsoft 'tan Azure CDN** ve **Verizon profillerden Azure CDN** ile kullanılabilir. 
>
 
HTTPS özelliğini etkinleştirmek için kendi sertifikanızı kullanabilirsiniz. Bu işlem, sertifikalarınızı güvenli bir şekilde depolamanıza olanak tanıyan Azure Key Vault ile tümleştirme yoluyla gerçekleştirilir. Azure CDN, sertifikanızı almak için bu güvenli mekanizmayı kullanır ve birkaç ek adım gerektirir. TLS/SSL sertifikanızı oluştururken, bunu izin verilen bir sertifika yetkilisi (CA) ile oluşturmanız gerekir. Buna karşılık, izin verilmeyen bir CA kullanırsanız isteğiniz reddedilir. İzin verilen CA'ların listesi için bkz. [Azure CDN'de özel HTTPS'leri etkinleştirmek için izin verilen sertifika yetkilileri](cdn-troubleshoot-allowed-ca.md). **Verizon 'den Azure CDN** için geçerli CA 'lar kabul edilecektir. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure Key Vault hesabınızı ve sertifikanızı hazırlama
 
1. Azure Key Vault: Özel HTTPS’yi etkinleştirmek istediğiniz Azure CDN profili ve CDN uç noktalarıyla aynı abonelik altında çalışan bir Azure Key Vault hesabınız olması gerekir. Azure Key Vault hesabınız yoksa, oluşturun.
 
2. Azure Key Vault sertifikaları: bir sertifikanız varsa, doğrudan Azure Key Vault hesabınıza yükleyin. Sertifikanız yoksa, doğrudan Azure Key Vault aracılığıyla yeni bir sertifika oluşturun.

### <a name="register-azure-cdn"></a>Azure CDN’yi kaydetme

PowerShell aracılığıyla Azure CDN’yi Azure Active Directory’nizdeki bir uygulama olarak kaydedin.

1. Gerekirse, yerel makinenize [Azure PowerShell](/powershell/azure/install-az-ps) ' yi yüklemelisiniz.

2. PowerShell’de aşağıdaki komutu çalıştırın:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** **Microsoft. Azurefrontkapısı-CDN** için hizmet sorumlusu.

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Azure CDN’ye anahtar kasanıza erişim yetkisi verme
 
Azure CDN’ye, Azure Key Vault hesabınızdaki sertifikalara (gizli dizi) erişme yetkisi verin.

1. **Ayarlar** bölümündeki anahtar kasasında **erişim ilkeleri**' ni seçin. Sağ bölmede **+ erişim Ilkesi Ekle**' yi seçin:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="CDN için Anahtar Kasası erişim ilkesi oluşturma" border="true":::

2. **Erişim Ilkesi Ekle** sayfasında, **sorumlu Seç**' ın yanındaki **Seçili** değil ' i seçin. **Sorumlu** sayfasında, **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** yazın. **Microsoft. Azurefrontkapısı-CDN**' yi seçin.  **Seç ' i** seçin:

2. **Sorumlu Seç** bölümünde, **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** araması yapın, **Microsoft. azurefrontkapısı-CDN**' yi seçin. **Seç**’i seçin.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="Azure CDN hizmet sorumlusunu seçin" border="true":::
    
3. **Sertifika izinlerini** seçin. Sertifikaları almak ve listelemek için CDN izinlerinin kullanılmasına izin vermek üzere **Al** ve **Listele** onay kutularını seçin.

4. **Gizli izinleri** seçin. CDN izinlerinin gizli dizileri almasını ve listeme yapmasına izin vermek için **Al** ve **Listele** onay kutularını seçin:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="CDN 'den keykasaya yönelik izinleri seçin" border="true":::

5. **Add (Ekle)** seçeneğini belirleyin. 

> [!NOTE]
> Azure CDN artık bu anahtar kasasına ve bu anahtar kasasında depolanan sertifikalara (gizli diziler) erişebilir. Bu abonelikte oluşturulan herhangi bir CDN örneğinin bu anahtar kasasındaki sertifikalara erişimi olur. 

 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Azure CDN’nin dağıtacağı sertifikayı seçme
 
1. Azure CDN portalına geri dönün ve özel HTTPS’yi etkinleştirmek istediğiniz profili ve CDN uç noktasını seçin. 

2. Özel etki alanları listesinde, HTTPS'yi etkinleştirmek istediğiniz özel etki alanını seçin.

    **Özel etki alanı** sayfası görünür.

3. Sertifika yönetimi türü bölümünde **Kendi sertifikamı kullan**’ı seçin. 

    :::image type="content" source="./media/cdn-custom-ssl/cdn-configure-your-certificate.png" alt-text="CDN uç noktası için sertifika yapılandırma ekran görüntüsü.":::

4. Bir Anahtar Kasası, sertifika/gizli dizi ve sertifika/gizli sürümü seçin.

    Azure CDN, aşağıdaki bilgileri listeler: 
    - Abonelik kimliğiniz için anahtar kasası hesapları. 
    - Seçili anahtar kasasının altındaki sertifikalar/gizlilikler. 
    - Kullanılabilir sertifika/gizli sürümler.
 
    > [!NOTE]
    > Sertifikanın Key Vault daha yeni bir sürümü kullanıma hazır olduğunda sertifikanın en son sürüme otomatik olarak döndürülmesi için lütfen sertifika/parola sürümünü ' en son ' olarak ayarlayın. Belirli bir sürüm seçilirse, sertifika döndürme için yeni sürümü el ile yeniden seçmeniz gerekir. Sertifika/gizli dizi yeni sürümünün dağıtılması 24 saate kadar sürer. 
   
5. HTTPS’yi etkinleştirmek için **Açık** seçeneğini belirleyin.
  
6. Sertifikanızı kullandığınızda etki alanı doğrulaması gerekli değildir. [Yayılmayı beklemeye](#wait-for-propagation)devam edin.

---

## <a name="validate-the-domain"></a>Etki alanını doğrulama

Bir CNAME kaydıyla özel uç noktanıza eşlenmiş bir özel etki alanınız varsa veya kendi sertifikanızı kullanıyorsanız, [CDN uç noktanıza eşlenmiş özel etki alanı](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record)' na geçin. 

Aksi takdirde, uç noktanız için CNAME kayıt girişi artık mevcut değilse veya cdnverify alt etki alanını içeriyorsa, [CDN uç noktanıza eşlenmemiş özel etki alanına](#custom-domain-isnt-mapped-to-your-cdn-endpoint)devam edin.

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Özel etki alanı bir CNAME kaydı kullanılarak CDN uç noktanızla eşlendi

Uç noktanıza özel bir etki alanı eklediğinizde, CDN uç noktası ana bilgisayar adıyla eşlenen DNS etki alanı kaydedicisinde bir CNAME kaydı oluşturdunuz. 

Bu CNAME kaydı hala varsa ve cdnverify alt etki alanını içermiyorsa, DigiCert CA bunu özel etki alanınızın sahipliğini otomatik olarak doğrulamak için kullanır. 

Kendi sertifikanızı kullanıyorsanız, etki alanı doğrulaması gerekli değildir.

CNAME kaydınız aşağıdaki biçimde olmalıdır:

* *Ad* , özel etki alanı adıdır.
* *Değer* , CDN uç nokta ana bilgisayar adıdır.

| Ad            | Tür  | Değer                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

CNAME kayıtları hakkında daha fazla bilgi için bkz. [CNAME DNS kaydı oluşturma](./cdn-map-content-to-custom-domain.md).

CNAME kaydınız doğru biçimindeyse, DigiCert otomatik olarak özel etki alanı adınızı doğrular ve etki alanınız için bir sertifika oluşturur. DigitCert size doğrulama e-postası göndermez ve isteğinizi onaylamanız gerekmez. Sertifika bir yıl boyunca geçerlidir ve süresi dolmadan önce autorenewed olacaktır. [Yayılmayı beklemeye](#wait-for-propagation)devam edin. 

Otomatik doğrulama genellikle birkaç saat sürer. Etki alanınızı 24 saat içinde doğrulanıp görmüyorsanız bir destek bileti açın.

>[!NOTE]
>DNS sağlayıcınız ile bir Sertifika Yetkilisi Yetkilendirme (CAA) kaydınız varsa bunun geçerli CA olarak DigiCert‘i içermesi gerekir. CAA kaydı; etki alanı sahiplerinin DNS sağlayıcıları ile hangi CA'ların, etki alanları için sertifika vermeye yetkili olduğunu belirtmesini sağlar. Bir CA, CAA kaydına sahip bir etki alanı için sertifika siparişi alırsa ve bu CA, sertifika vermeye yetkili olarak listelenmemişse bu CA’nın, söz konusu etki alanına veya alt etki alanına sertifika vermesi yasaklanır. CAA kayıtlarını yönetme ile ilgili bilgi için bkz. [CAA kayıtlarını yönetme](https://support.dnsimple.com/articles/manage-caa-record/). CAA kayıt aracı için bkz. [CAA Kayıt Yardımcısı](https://sslmate.com/caa/).

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>Özel etki alanı, CDN uç noktanızla eşlenmedi

>[!NOTE]
>**Akamai ' dan Azure CDN** kullanıyorsanız, otomatik etki alanı doğrulamasını etkinleştirmek IÇIN aşağıdaki CNAME ayarlanmalıdır. "_acme-Challenge. &lt; Özel etki alanı konak adı &gt; -> CNAME-> &lt; özel etki alanı konak adı &gt; . ak-Acme-Challenge.azureedge.net "

CNAME kayıt girişi cdnverify alt etki alanını içeriyorsa, bu adımdaki yönergelerin geri kalanını izleyin.

DigiCert aşağıdaki e-posta adreslerine bir doğrulama e-postası gönderir. Aşağıdaki adreslerden birini doğrudan onaylayabildiğinizi doğrulayın:

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

İsteği onaylamanız için birkaç dakika içinde bir e-posta almalısınız. Bir istenmeyen posta filtresi kullanıyorsanız, verification@digicert.com allowlist öğesine ekleyin. E-postayı 24 saat içinde almazsanız Microsoft destek ekibine başvurun.
    
![Etki alanı doğrulama e-postası](./media/cdn-custom-ssl/domain-validation-email.png)

Onay bağlantısını seçtiğinizde, aşağıdaki çevrimiçi onay formuna yönlendirilirsiniz: 
    
![Etki alanı doğrulama formu](./media/cdn-custom-ssl/domain-validation-form.png)

Formdaki yönergeleri uygulayın. İki doğrulama seçeneğiniz vardır:

- contoso.com gibi aynı kök etki alanı için aynı hesap üzerinden verilen gelecekteki tüm siparişleri onaylayabilirsiniz. Aynı kök etki alanı için başka özel etki alanları eklemeyi planlıyorsanız bu yaklaşım önerilir.

- Yalnızca bu istekte kullanılan söz konusu ana bilgisayar adını onaylayabilirsiniz. Daha sonraki istekler için başka bir onay gerekir.

DigiCert onaydan sonra özel etki alanı adınız için sertifika oluşturma işlemlerini tamamlar. Sertifika bir yıl boyunca geçerlidir ve bu süre dolmadan önce autorenewed olacaktır.

## <a name="wait-for-propagation"></a>Yayılma için bekleme

Etki alanı doğrulandıktan sonra özel etki alanı HTTPS özelliğinin etkinleştirilmesi 6-8 saate kadar sürebilir. İşlem tamamlandığında, Azure portal özel HTTPS durumu **etkin** olarak değiştirilir. Özel etki alanı iletişim kutusundaki dört işlem adımı tamamlanmış olarak işaretlenir. Özel etki alanınız artık HTTPS’yi kullanmak için hazırdır.

![HTTPS iletişim kutusunu etkinleştirme](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>İşlem ilerleme durumu

Aşağıdaki tabloda, HTTPS’yi etkinleştirdiğinizde oluşan işlem ilerleme durumunu gösterir. HTTPS’yi etkinleştirmenizin ardından özel etki alanı iletişim kutusunda dört işlem adımı görünür. Her adım etkin hale geldiğinde, diğer alt adım ayrıntıları ilerledikçe adımın altında görünür. Bu alt adımların hiçbiri gerçekleşmez. Bir adım başarıyla tamamlandıktan sonra adımın yanında yeşil bir onay işareti görünür. 

| İşlem adımı | İşlem alt adımı ayrıntıları | 
| --- | --- |
| 1 İstek gönderiliyor | İstek gönderiliyor |
| | HTTPS isteğiniz gönderiliyor. |
| | HTTPS isteğiniz başarıyla gönderildi. |
| 2 Etki alanı doğrulaması | CNAME, CDN uç noktasıyla eşlenmişse, etki alanı otomatik olarak onaylanır. Eşleme yapılmadıysa etki alanınızın kayıt kaydında listelenen e-posta adresine (WHOIS kayıt şirketi) bir doğrulama isteği gönderilir.|
| | Etki alanı sahipliğiniz başarıyla doğrulandı. |
| | Etki alanı doğrulama isteğinin süresi doldu. (Müşteri büyük olasılıkla 6 gün içinde yanıt vermedi.) HTTPS, etki alanınız üzerinde etkinleştirilmez. * |
| | Etki alanı sahipliğini doğrulama isteği, müşteri tarafından reddedildi. HTTPS, etki alanınız üzerinde etkinleştirilmez. * |
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

Bu bölümde, özel etki alanınız için HTTPS 'yi devre dışı bırakmayı öğreneceksiniz.

### <a name="disable-the-https-feature"></a>HTTPS özelliğini devre dışı bırakma 

1. [Azure Portal](https://portal.azure.com), **CDN profilleri**' ni arayıp seçin. 

2. **Microsoft 'tan Azure CDN standardını**, **Verizon 'Ten standart** ve **Verizon profilinden Premium Azure CDN** Azure CDN seçin.

3. Uç noktalar listesinde, özel etki alanınızı içeren uç noktayı seçin.

4. HTTPS 'yi devre dışı bırakmak istediğiniz özel etki alanını seçin.

    ![Özel etki alanları listesi](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. HTTPS 'yi devre dışı bırakmak için **kapalı** seçeneğini belirleyip **Uygula**' yı seçin.

    ![Özel HTTPS iletişim kutusu](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Yayılma için bekleme

Özel etki alanı HTTPS özelliği devre dışı bırakıldıktan sonra bu işlemin geçerli olması 6-8 saate kadar sürebilir. İşlem tamamlandığında, Azure portal özel HTTPS durumu **devre dışı** olarak değiştirilir. Özel etki alanı iletişim kutusundaki üç işlem adımı tamamlanmış olarak işaretlenir. Özel etki alanınız artık HTTPS’yi kullanamaz.

![HTTPS iletişim kutusunu devre dışı bırakma](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>İşlem ilerleme durumu

Aşağıdaki tabloda, HTTPS’yi devre dışı bıraktığınızda oluşan işlem ilerleme durumunu gösterir. HTTPS 'yi devre dışı bıraktıktan sonra, özel etki alanı iletişim kutusunda üç işlem adımı görünür. Bir adım etkin hale geldiğinde Ayrıntılar adım altında görüntülenir. Bir adım başarıyla tamamlandıktan sonra adımın yanında yeşil bir onay işareti görünür. 

| İşlem ilerleme durumu | İşlem ayrıntıları | 
| --- | --- |
| 1 İstek gönderiliyor | İsteğiniz gönderiliyor |
| 2 Sertifika sağlaması kaldırılıyor | Sertifika siliniyor |
| 3 Tamamlandı | Sertifika silindi |

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

1. *Sertifika sağlayıcısı kimdir ve ne tür bir sertifika kullanılır?*

    Özel etki alanınız için DigiCert tarafından sunulan ayrılmış bir sertifika şu şekilde kullanılır:
    
    * **Verizon 'dan Azure CDN**
    * **Microsoft 'tan Azure CDN**

2. *IP tabanlı veya SNI TLS/SSL kullanıyor musunuz?*

    Hem **Azure CDN from Verizon** hem de **Azure CDN Standard from Microsoft** için SNI TLS/SSL kullanın.

3. *DigiCert’ten etki alanı doğrulama e-postası almazsam ne olur?*

    *Cdnverify* alt etki alanını KULLANMıYORSANıZ ve CNAME girişiniz uç nokta ana bilgisayar adı için ise, bir etki alanı doğrulama e-postası almazsınız.
     
    Doğrulama otomatik olarak gerçekleşir. Ancak CNAME girişiniz yoksa ve e-postayı 24 saat içinde almazsanız Microsoft destek ekibine başvurun.

4. *Ayrılmış sertifika kullanmak, SAN sertifikasından daha mı güvenlidir?*
    
    SAN sertifikası, ayrılmış sertifika ile aynı şifreleme ve güvenlik standartlarını uygular. Tüm verilen TLS/SSL sertifikaları, gelişmiş sunucu güvenliği için SHA-256 kullanır.

5. *DNS sağlayıcım ile Sertifika Yetkilisi Yetkilendirme kaydı kullanmam gerekir mi?*

    Sertifika yetkilisi yetkilendirme kaydı Şu anda gerekli değil. Ancak, varsa, geçerli CA olarak DigiCert’i içermelidir.

6. *20 Haziran 2018 ' de, varsayılan olarak SNı TLS/SSL ile ayrılmış bir sertifika kullanarak Verizon 'tan Azure CDN başladı. Konu diğer adları (SAN) sertifikası ve IP tabanlı TLS/SSL kullanarak var olan özel etki alanlarıma ne olur?*

    Microsoft, uygulamanıza yalnızca SNI istemci isteklerinin gönderildiğini algılarda mevcut etki alanlarınız önümüzdeki aylarda kademeli olarak tek sertifikaya geçirilecektir. 
    
    SNı istemcileri algılanırsa, etki alanlarınız IP tabanlı TLS/SSL ile SAN sertifikasında kalır. Hizmetinize veya SNı olmayan istemcilerinize yönelik istekler etkilenmemiştir.

7. *Sertifika yenilemesi kendi sertifikanızı getir ile nasıl çalışır?*

    Yeni bir sertifikanın, PoP altyapısına dağıtılmasını sağlamak için yeni sertifikanızı Azure Anahtar Kasası 'na yükleyin. Azure CDN üzerindeki TLS ayarlarınızda en yeni sertifika sürümünü seçin ve Kaydet ' i seçin. Azure CDN daha sonra yeni güncelleştirilmiş sertifikayı yayacaktır. 

8. *Uç nokta yeniden başlatıldıktan sonra HTTPS 'yi yeniden etkinleştirmem gerekir mi?*

    Evet. **Akamai ' den Azure CDN** kullanıyorsanız, uç nokta durdurup yeniden başlatılırsa, ayarın etkin olması durumunda https ayarını yeniden etkinleştirmeniz gerekir.


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
