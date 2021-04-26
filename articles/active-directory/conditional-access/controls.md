---
title: Azure AD koşullu erişim 'de özel denetimler
description: Koşullu erişim Azure Active Directory içindeki özel denetimlerin nasıl çalıştığını öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7585c91e42b2d3591532756c1ead9ea60b7035e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94837592"
---
# <a name="custom-controls-preview"></a>Özel denetimler (Önizleme)

Özel denetimler Azure Active Directory önizleme özelliğidir. Özel denetimler kullanılırken, kullanıcılarınız Azure Active Directory dışında kimlik doğrulama gereksinimlerini karşılamak için uyumlu bir hizmete yönlendirilir. Bu denetimi karşılamak için, bir kullanıcının tarayıcısı dış hizmete yönlendirilir, gerekli kimlik doğrulamasını gerçekleştirir ve ardından Azure Active Directory yeniden yönlendirilir. Azure Active Directory yanıtı doğrular ve kullanıcının kimliği başarıyla doğrulanır veya doğrulandıysa, Kullanıcı koşullu erişim akışında devam eder.

> [!NOTE]
> Özel denetim özelliğiyle ilgili planlama yaptığımız değişiklikler hakkında daha fazla bilgi için bkz. Şubat 2020 [Arşivi](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls), yenilikler.

## <a name="creating-custom-controls"></a>Özel denetimler oluşturma

Özel denetimler sınırlı bir onaylanan kimlik doğrulama sağlayıcıları kümesiyle birlikte kullanılabilir. Özel bir denetim oluşturmak için önce kullanmak istediğiniz sağlayıcıya başvurmanız gerekir. Her Microsoft dışı sağlayıcının, hizmetin bir parçası haline gelmesi, abone olması veya başka bir şekilde hizmet vermek ve koşullu erişimle tümleştirileceğini belirtmek için kendi işlemi ve gereksinimleri vardır. Bu noktada, sağlayıcı size JSON biçiminde bir veri bloğu sağlar. Bu veriler, sağlayıcının ve Koşullu erişimin kiracınız için birlikte çalışmasına izin verir, yeni denetimi oluşturur ve kullanıcılarınızın sağlayıcı ile doğrulamayı başarıyla gerçekleştirdiyse Koşullu erişimin nasıl bildirebileceğini tanımlar.

JSON verilerini kopyalayın ve ilgili metin kutusuna yapıştırın. Yaptığınız değişikliği açıkça anlamadığınız müddetçe JSON üzerinde herhangi bir değişiklik yapmayın. Herhangi bir değişiklik yapmak, sağlayıcı ile Microsoft arasındaki bağlantıyı bozabilir ve sizin ve kullanıcılarınızı sizin hesaplarınızdan geçirebilir.

Özel denetim oluşturma seçeneği, **koşullu erişim** sayfasının **Yönet** bölümünde bulunur.

![Koşullu erişimde özel denetimler arabirimi](./media/controls/custom-controls-conditional-access.png)

**Yeni özel denetim**' i tıklattığınızda, denetiminizin JSON verileri için TextBox ile bir dikey pencere açılır.  

![Yeni özel denetim](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>Özel denetimleri silme

Özel bir denetimi silmek için, önce herhangi bir koşullu erişim ilkesinde kullanılmadığından emin olmalısınız. Tamamlandıktan sonra:

1. Özel denetimler listesine git
1. Tıklayın...  
1. **Sil**’i seçin.

## <a name="editing-custom-controls"></a>Özel denetimleri Düzenle

Özel bir denetimi düzenlemek için geçerli denetimi silmeniz ve güncelleştirilmiş bilgilerle yeni bir denetim oluşturmanız gerekir.

## <a name="known-limitations"></a>Bilinen sınırlamalar

Özel denetimler, Azure AD Multi-Factor Authentication, Azure AD self servis parola sıfırlama (SSPR), kimlik koruması 'nın, Intune cihaz kaydının bir parçası olarak veya cihazları Azure AD 'ye eklerken ayrıcalıklı kimlik doğrulama talebi gereksinimlerini karşıladığı kimlik koruması 'nın otomasyonu ile birlikte kullanılamaz.

## <a name="next-steps"></a>Sonraki adımlar

- [Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

- [Yalnızca rapor modu](concept-conditional-access-report-only.md)

- [Koşullu erişim What If aracını kullanarak oturum açma davranışının benzetimini yapma](troubleshoot-conditional-access-what-if.md)
