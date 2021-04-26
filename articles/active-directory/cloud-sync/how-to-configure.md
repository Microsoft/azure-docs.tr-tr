---
title: Azure AD Connect bulut eşitleme yeni aracı yapılandırması
description: Bu makalede, bulut eşitleme 'nin nasıl yükleneceği açıklanır.
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
ms.openlocfilehash: ffdd27b67a122f82cc5fdb5568f11c85387955e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660805"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Azure AD Connect bulut eşitlemesi için yeni bir yapılandırma oluşturma

Azure AD Connect sağlama aracısını yükledikten sonra, Azure portal oturum açmanız ve yapılandırmanız gerekir. Aracıyı etkinleştirmek için bu adımları izleyin.

## <a name="configure-provisioning"></a>Sağlamayı Yapılandır
Sağlamayı yapılandırmak için aşağıdaki adımları izleyin.

 1. Azure portal **Azure Active Directory** seçin
 2. **Azure AD Connect** seçin.
 3. **Bulut eşitlemesini Yönet**' i seçin.

 ![Sağlamayı Yönet](media/how-to-install/install-6.png)
 
 4. **Yeni yapılandırma**' yı seçin.
 5. Yapılandırma ekranında, etki alanınızı ve parola karması eşitlemesini etkinleştirip etkinleştirmeyeceğinizi seçin.  **Oluştur**' a tıklayın.  
 
 ![Yeni yapılandırma oluştur](media/how-to-configure/configure-1.png)


 6.  Sağlama yapılandırma düzenleme ekranı açılır.

   ![Yapılandırmayı Düzenle](media/how-to-configure/con-1.png)

 7. Bir **bildirim e-postası** girin. Bu e-postaya sağlama sağlıklı olmadığında bildirim alınacaktır.  **Yanlışlıkla silmenin** etkin tutulmasını önler ve **yanlışlıkla silme eşiğini** , hakkında bildirim almak istediğiniz bir sayıya ayarlamanız önerilir.  Daha fazla bilgi için aşağıdaki [yanlışlıkla silme](#accidental-deletions) bölümüne bakın.
 8. Seçimi etkinleştirmek için seçiciyi taşıyın ve Kaydet ' i seçin.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Belirli kullanıcılara ve gruplara kapsam sağlama
Şirket içi Active Directory grupları veya kuruluş birimleri kullanarak belirli kullanıcıları ve grupları eşitlenmek için aracının kapsamını belirleyebilirsiniz. Bir yapılandırma içinde grupları ve kuruluş birimlerini yapılandıramazsınız. 

 1.  Azure portalında **Azure Active Directory** seçeneğini belirleyin.
 2. **Azure AD Connect** seçin.
 3. **Bulut eşitlemesini Yönet**' i seçin.
 4. **Yapılandırma** altında yapılandırmanızı seçin.

 ![Yapılandırma bölümü](media/how-to-configure/scope-1.png)
 
 5. **Yapılandır**' ın altında, yapılandırma kuralının kapsamını değiştirmek için **kapsam Filtrelerini Düzenle** ' yi seçin.
 ![Kapsamı Düzenle](media/how-to-configure/scope-3.png)
 7. Sağ tarafta, kapsamı değiştirebilirsiniz.  Bitti **' ye tıklayın**  ve Işiniz bittiğinde **kaydedin** .
 8. Kapsamı değiştirdikten sonra, değişikliklerin anında eşitlenmesini başlatmak için [sağlamayı yeniden başlatmanız](#restart-provisioning) gerekir.

## <a name="attribute-mapping"></a>Öznitelik eşlemesi
Azure AD Connect bulut eşitlemesi, şirket içi Kullanıcı/Grup nesneleriniz ile Azure AD içindeki nesneler arasındaki öznitelikleri kolayca eşlemenizi sağlar.  Varsayılan öznitelik eşlemelerini iş gereksinimlerinize göre özelleştirebilirsiniz. Bu nedenle, var olan öznitelik eşlemelerini değiştirebilir veya silebilir veya yeni öznitelik eşlemeleri oluşturabilirsiniz.  Daha fazla bilgi için bkz. [öznitelik eşleme](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>İsteğe bağlı sağlama
Azure AD Connect bulut eşitleme, bu değişiklikleri tek bir kullanıcıya veya gruba uygulayarak yapılandırma değişikliklerini test etmenize olanak tanır.  Yapılandırma üzerinde yapılan değişikliklerin düzgün şekilde uygulandığını ve Azure AD ile doğru eşitlendiğini doğrulamak için bunu kullanabilirsiniz.  Daha fazla bilgi için bkz. [isteğe bağlı sağlama](how-to-on-demand-provision.md).

## <a name="accidental-deletions"></a>Yanlışlıkla silme işlemleri
Yanlışlıkla silme özelliği, çok sayıda kullanıcı ve grubu etkileyecek şirket içi dizininizde yanlışlıkla yapılan yapılandırma değişikliklerinden ve değişikliklerden korunmak için tasarlanmıştır.  Bu özellik şunları yapmanıza olanak sağlar:

- yanlışlıkla silmeleri otomatik olarak engelleme özelliğini yapılandırın. 
- Yapılandırmanın yürürlüğe alınacağı nesne (eşik) sayısını ayarlayın 
- söz konusu senaryo için söz konusu eşitleme işi karantinaya alındıktan sonra bir e-posta bildirimi almak için bir bildirim e-posta adresi ayarlayın 

Daha fazla bilgi için bkz. [yanlışlıkla silme](how-to-accidental-deletes.md)

## <a name="quarantines"></a>Yazılımı karantinaya aldığında
Bulut eşitleme, yapılandırmanızın sistem durumunu izler ve sağlıksız nesneleri karantina durumuna koyar. Hedef sisteme karşı yapılan çağrıların çoğu veya hepsi sürekli olarak hata nedeniyle başarısız olursa, örneğin, geçersiz yönetici kimlik bilgileri, eşitleme işi karantinaya alındı olarak işaretlenir.  Daha fazla bilgi için bkz. [quarantınes](how-to-troubleshoot.md#provisioning-quarantined-problems)hakkında sorun giderme bölümü.

## <a name="restart-provisioning"></a>Sağlamayı yeniden Başlat 
Zamanlanan bir sonraki çalıştırmaya beklemek istemiyorsanız, sağlama çalıştırmasını **yeniden başlatma sağlama** düğmesini kullanarak tetikleyin. 
 1.  Azure portalında **Azure Active Directory** seçeneğini belirleyin.
 2. **Azure AD Connect** seçin.
 3.  **Bulut eşitlemesini Yönet**' i seçin.
 4. **Yapılandırma** altında yapılandırmanızı seçin.

   ![Sağlamayı yeniden başlatmak için yapılandırma seçimi](media/how-to-configure/scope-1.png)

 5. En üstte **sağlamayı yeniden Başlat**' ı seçin.

## <a name="remove-a-configuration"></a>Yapılandırmayı kaldırma
Bir yapılandırmayı silmek için aşağıdaki adımları izleyin.

 1.  Azure portalında **Azure Active Directory** seçeneğini belirleyin.
 2. **Azure AD Connect** seçin.
 3. **Bulut eşitlemesini Yönet**' i seçin.
 4. **Yapılandırma** altında yapılandırmanızı seçin.
   
   ![Yapılandırmayı kaldırmak için yapılandırma seçimi](media/how-to-configure/scope-1.png)

 5. Yapılandırma ekranının üst kısmında **Sil**' i seçin.

>[!IMPORTANT]
>Bir yapılandırma silinmeden önce hiçbir onay yok. **Sil**' i seçmeden önce bunun gerçekleştirmek istediğiniz eylem olduğundan emin olun.


## <a name="next-steps"></a>Sonraki adımlar 

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)
