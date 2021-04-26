---
title: Azure AD Connect bulut eşitleme 'de isteğe bağlı sağlama
description: Bu makalede yapılandırma değişikliklerini test etmek için Azure AD Connect bulut eşitleme özelliğinin nasıl kullanılacağı açıklanır.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554284"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect bulut eşitleme 'de isteğe bağlı sağlama

Bu değişiklikleri tek bir kullanıcıya uygulayarak, yapılandırma değişikliklerini test etmek için Azure Active Directory (Azure AD) ile bağlantı için bulut eşitleme özelliğini kullanabilirsiniz. Bu isteğe bağlı sağlama, yapılandırma üzerinde yapılan değişikliklerin düzgün şekilde uygulandığını ve Azure AD ile doğru eşitlendiğini doğrulamanıza yardımcı olur.  

> [!IMPORTANT] 
> İsteğe bağlı sağlama kullandığınızda, kapsam filtreleri seçtiğiniz kullanıcıya uygulanmaz. Belirttiğiniz kuruluş birimleri dışındaki kullanıcılar üzerinde isteğe bağlı sağlama kullanabilirsiniz.

## <a name="validate-a-user"></a>Kullanıcı doğrulama
İsteğe bağlı sağlama kullanmak için şu adımları izleyin:

1.  Azure portalında **Azure Active Directory** seçeneğini belirleyin.
2.  **Azure AD Connect** seçin.
3.  **Bulut eşitlemesini Yönet**' i seçin.

    ![Bulut eşitlemesini yönetme bağlantısını gösteren ekran görüntüsü.](media/how-to-install/install-6.png)
4. **Yapılandırma** altında yapılandırmanızı seçin.
5. **Doğrula** altında **Kullanıcı sağla** düğmesini seçin. 

   ![Kullanıcı sağlama düğmesini gösteren ekran görüntüsü.](media/how-to-on-demand-provision/on-demand-2.png)

6. **İsteğe bağlı sağlama** ekranında, bir kullanıcının ayırt edici adını girin ve **sağlama** düğmesini seçin.  
 
   ![Bir Kullanıcı adı ve sağlama düğmesi gösteren ekran görüntüsü.](media/how-to-on-demand-provision/on-demand-3.png)
7. Sağlama bittikten sonra, dört yeşil onay işareti içeren bir başarı ekranı görünür. Sol tarafta herhangi bir hata görünür.

   ![Başarılı sağlamayı gösteren ekran görüntüsü.](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>Sağlama ile ilgili ayrıntıları alın
Artık Kullanıcı bilgilerine bakabilir ve yapılandırmada yaptığınız değişikliklerin uygulanmış olup olmadığını belirleyebilirsiniz. Bu makalenin geri kalanında, başarıyla eşitlenen bir kullanıcının ayrıntılarında görüntülenen tek tek bölümler açıklanmaktadır.

### <a name="import-user"></a>Kullanıcı al
**Kullanıcı Içeri aktarma** bölümü, Active Directory içeri aktarılan Kullanıcı hakkında bilgi sağlar. Bu, kullanıcının Azure AD 'ye sağlamadan önce nasıl görüneceğine ilişkin bir şeydir. Bu bilgileri görüntülemek için **Ayrıntıları görüntüle** bağlantısını seçin.

![İçeri aktarılan Kullanıcı hakkındaki ayrıntıları görüntülemek için düğmenin ekran görüntüsü.](media/how-to-on-demand-provision/on-demand-5.png)

Bu bilgileri kullanarak, içeri aktarılan çeşitli öznitelikleri (ve bunların değerlerini) görebilirsiniz. Özel bir öznitelik eşlemesi oluşturduysanız, burada değeri görebilirsiniz.

![Kullanıcı ayrıntılarını gösteren ekran görüntüsü.](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Kullanıcının kapsamda olup olmadığını belirleme
**Kullanıcının kapsam içinde** olup olmadığını belirleme bölümünde, Azure AD 'ye aktarılmış kullanıcının kapsamda olup olmadığı hakkında bilgi sağlanır. Bu bilgileri görüntülemek için **Ayrıntıları görüntüle** bağlantısını seçin.

![Kullanıcı kapsamıyla ilgili ayrıntıları görüntülemek için düğmenin ekran görüntüsü.](media/how-to-on-demand-provision/on-demand-7.png)

Bu bilgileri kullanarak, kullanıcının kapsamda olup olmadığını görebilirsiniz.

![Kullanıcı kapsamı ayrıntılarını gösteren ekran görüntüsü.](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Kaynak ve hedef sistemle Kullanıcı Eşleştir
**Kaynak ve hedef sistem arasındaki kullanıcıyı Eşleştir** bölümü, kullanıcının Azure AD 'de zaten mevcut olup olmadığı ve yeni bir kullanıcı sağlamak yerine bir birleştirmenin oluşması hakkında bilgiler sağlar. Bu bilgileri görüntülemek için **Ayrıntıları görüntüle** bağlantısını seçin.

![Eşleşen bir kullanıcı hakkındaki ayrıntıları görüntülemek için düğmenin ekran görüntüsü.](media/how-to-on-demand-provision/on-demand-8.png)

Bu bilgileri kullanarak, bir eşleşmenin bulunup bulunamamadığını veya yeni bir Kullanıcı oluşturulup oluşturulmayacağını görebilirsiniz.

![Kullanıcı bilgilerini gösteren ekran görüntüsü.](media/how-to-on-demand-provision/on-demand-11.png)

Eşleşen Ayrıntılar aşağıdaki üç işlemden birine sahip bir ileti gösterir:
- **Oluştur**: Azure AD 'de bir Kullanıcı oluşturulur.
- **Güncelleştir**: bir Kullanıcı, yapılandırmada yapılan bir değişikliğe göre güncelleştirilir.
- **Sil**: bir Kullanıcı Azure AD 'den kaldırılır.

Gerçekleştirdiğiniz işlem türüne bağlı olarak, ileti farklılık gösterecektir.

### <a name="perform-action"></a>Eylem gerçekleştir
**Eylem gerçekleştirme** bölümü, yapılandırma uygulandıktan sonra Azure AD 'ye sağlanan veya bu dosyaya aktarılmış Kullanıcı hakkında bilgiler sağlar. Bu, kullanıcının Azure AD 'ye sağlamaktan sonra nasıl görüneceğine ilişkin bir şeydir. Bu bilgileri görüntülemek için **Ayrıntıları görüntüle** bağlantısını seçin.

![Gerçekleştirilen bir eylemle ilgili ayrıntıları görüntülemek için düğmenin ekran görüntüsü.](media/how-to-on-demand-provision/on-demand-9.png)

Bu bilgileri kullanarak, yapılandırma uygulandıktan sonra özniteliklerin değerlerini görebilirsiniz. Ne içeri aktarıldığına benzer mı, yoksa farklı mı? Yapılandırma başarıyla uygulandı mı?  

Bu işlem, bulutta ve Azure AD kiracınıza geçiş yaparken öznitelik dönüşümünü izlemenizi sağlar.

![İzlenen öznitelik ayrıntılarını gösteren ekran görüntüsü.](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Sonraki adımlar 

- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)
- [Azure AD Connect bulut eşitlemesini yükler](how-to-install.md)
 