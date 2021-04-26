---
title: Azure AD Connect bulut eşitlemede öznitelik eşleme
description: Bu makalede, öznitelikleri eşlemek için Azure AD Connect bulut eşitleme özelliğinin nasıl kullanılacağı açıklanır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555219"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect bulut eşitlemede öznitelik eşleme

Şirket içi kullanıcı veya grup nesneleriniz ile Azure AD içindeki nesneler arasında öznitelikleri eşlemek için Azure Active Directory (Azure AD) ' ın bulut eşitleme özelliğini kullanabilirsiniz. Bu özellik, bulut eşitleme yapılandırmasına eklenmiştir.

Varsayılan öznitelik eşlemelerini iş gereksinimlerinize göre özelleştirebilir (değiştirebilir, silebilir veya oluşturabilirsiniz). Eşitlenen özniteliklerin bir listesi için bkz. [Azure Active Directory ile eşitlenen öznitelikler](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understand-types-of-attribute-mapping"></a>Öznitelik eşleme türlerini anlayın
Öznitelik eşleme ile özniteliklerin Azure AD 'de nasıl doldurulduğunu kontrol edersiniz. Azure AD dört eşleme türünü destekler:

- **Doğrudan**: Target özniteliği, Active Directory bağlantılı nesnenin bir özniteliğinin değeri ile doldurulur.
- **Sabit**: Target özniteliği belirttiğiniz belirli bir dizeyle doldurulur.
- **İfade**: Target özniteliği, komut dosyası benzeri bir ifadenin sonucuna göre doldurulur. Daha fazla bilgi için bkz. [Azure Active Directory öznitelik eşlemeleri için Ifadeler yazma](reference-expressions.md).
- **Hiçbiri**: hedef özniteliği değiştirilmemiş olarak bırakıldı. Ancak, hedef özniteliği boşsa, belirttiğiniz varsayılan değerle doldurulur.

Bu temel türlerle birlikte, özel öznitelik eşlemeleri isteğe bağlı bir *varsayılan* değer atama kavramını destekler. Varsayılan değer atama, Azure AD veya hedef nesne bir değere sahip değilse bir hedef özniteliğin bir değerle doldurulmasını sağlar. En yaygın yapılandırma bu boş bırakılmamalıdır.

## <a name="understand-properties-of-attribute-mapping"></a>Öznitelik eşlemesinin özelliklerini anlayın

Tür özelliği ile birlikte, öznitelik eşlemeleri aşağıdaki öznitelikleri destekler:

- **Kaynak özniteliği**: kaynak sistemden Kullanıcı özniteliği (örnek: Active Directory).
- **Target özniteliği**: Hedef sistemdeki kullanıcı özniteliği (örnek: Azure Active Directory).
- **Null Ise varsayılan değer (isteğe bağlı)**: kaynak özniteliği null ise hedef sisteme geçirilecek değer. Bu değer, yalnızca bir kullanıcı oluşturulduğunda sağlanacak. Mevcut bir kullanıcıyı güncelleştirirken bu, sağlanmayacaktır.  
- **Bu eşlemeyi Uygula**:
  - **Her zaman**: Bu eşlemeyi hem Kullanıcı oluşturma hem de güncelleştirme eylemlerine uygulayın.
  - **Yalnızca oluşturma sırasında**: Bu eşlemeyi yalnızca Kullanıcı oluşturma eylemlerinde uygulayın.

> [!NOTE]
> Bu makalede, Azure portal öznitelikleri eşlemek için nasıl kullanılacağı açıklanır.  Microsoft Graph kullanımı hakkında bilgi için bkz. [dönüşümler](how-to-transformation.md).

## <a name="add-an-attribute-mapping"></a>Öznitelik eşlemesi Ekle

Yeni özelliği kullanmak için şu adımları izleyin:

1.  Azure portalında **Azure Active Directory** seçeneğini belirleyin.
2.  **Azure AD Connect** seçin.
3.  **Bulut eşitlemesini Yönet**' i seçin.

    ![Bulut eşitlemesini yönetme bağlantısını gösteren ekran görüntüsü.](media/how-to-install/install-6.png)

4. **Yapılandırma** altında yapılandırmanızı seçin.
5. **Eşlemeleri düzenlemek Için tıklayın ' ı** seçin.  Bu bağlantı, **öznitelik eşlemeleri** ekranını açar.

    ![Öznitelik ekleme bağlantısını gösteren ekran görüntüsü.](media/how-to-attribute-mapping/mapping-6.png)

6.  **Öznitelik Ekle**' yi seçin.

    ![Öznitelik ve eşleme türleri listeleriyle birlikte öznitelik ekleme düğmesini gösteren ekran görüntüsü.](media/how-to-attribute-mapping/mapping-1.png)

7. Eşleme türünü seçin. Bu örnekte, **ifadesini** kullandık.
8. Kutuya ifadeyi girin. Bu örnekte, kullanıyoruz `Replace([mail], "@contoso.com", , ,"", ,)` .
9. Target özniteliğini girin. Bu örnekte, **ExtensionAttribute15** kullanıyoruz.
10. Bu eşlemenin ne zaman uygulanacağını seçin ve ardından **Uygula**' yı seçin.

    ![Öznitelik eşlemesi oluşturmak için doldurulmuş kutuları gösteren ekran görüntüsü.](media/how-to-attribute-mapping/mapping-2a.png)

11. **Öznitelik eşlemeleri** ekranına geri döndüğünüzde yeni öznitelik eşlemenizi görmeniz gerekir.  
12. **Şemayı kaydet**' i seçin.

    ![Şemayı Kaydet düğmesini gösteren ekran görüntüsü.](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Öznitelik eşlemenizi test etme

Öznitelik eşlemenizi test etmek için [isteğe bağlı sağlama](how-to-on-demand-provision.md)kullanabilirsiniz: 

1. Azure portalında **Azure Active Directory** seçeneğini belirleyin.
2. **Azure AD Connect** seçin.
3. **Sağlamayı Yönet**' i seçin.
4. **Yapılandırma** altında yapılandırmanızı seçin.
5. **Doğrula** altında **Kullanıcı sağla** düğmesini seçin. 
6. **İsteğe bağlı sağlama** ekranında, bir kullanıcının veya grubun ayırt edici adını girin ve **sağlama** düğmesini seçin. 

   Ekranda sağlamanın devam ettiğini gösterir.

   ![Sürmekte olan sağlamayı gösteren ekran görüntüsü.](media/how-to-attribute-mapping/mapping-4.png)

8. Sağlama bittikten sonra, dört yeşil onay işareti içeren bir başarı ekranı görünür. 

   **Eylem gerçekleştir** altında **Ayrıntıları görüntüle**' yi seçin. Sağ tarafta, yeni özniteliği eşitlenmiş ve ifade uygulanmış olarak görmeniz gerekir.

   ![Başarı ve dışarı aktarma ayrıntılarını gösteren ekran görüntüsü.](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)
- [Öznitelik eşlemeleri için ifadeler yazma](reference-expressions.md)
- [Azure Active Directory eşitlenen öznitelikler](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
