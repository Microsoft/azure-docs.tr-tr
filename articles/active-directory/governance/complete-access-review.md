---
title: Gruplar veya uygulamalar için bir erişim incelemesi yapın-Azure Active Directory | Microsoft Docs
description: Azure Active Directory erişim gözden geçirmeleriyle grup üyelerinin veya uygulama erişiminin erişim incelemesini tamamlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c405032368ffd06f5808bc4518302d2f6d66b9
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489157"
---
# <a name="complete-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmeleriyle grupların veya uygulamaların erişim incelemesini tamamlar

Yönetici olarak, [gruplar veya uygulamalar için erişim gözden geçirmesi oluşturursunuz](create-access-review.md) ve gözden geçirenler [erişim incelemesini gerçekleştirir](perform-access-review.md). Bu makalede, erişim incelemesi sonuçlarının nasıl görüntüleneceği ve sonuçların nasıl uygulanacağı açıklanır.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Önkoşullar

- Azure AD Premium P2
- Genel yönetici, Kullanıcı Yöneticisi, güvenlik yöneticisi veya güvenlik okuyucusu

Daha fazla bilgi için [hangi kullanıcıların lisanslarına sahip olması gerektiğini](access-reviews-overview.md#which-users-must-have-licenses)görün.

## <a name="view-an-access-review"></a>Erişim gözden geçirmesini görüntüleme

Gözden geçirenler incelemelerini tamamlamalarını gözlemlediği için ilerlemeyi izleyebilirsiniz.

1. Azure portal oturum açın ve [Identity idare sayfasını](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)açın.

1. Sol menüde, **erişim gözden geçirmeleri**' na tıklayın.

1. Listede, bir erişim gözden geçirmesi ' na tıklayın.

    Bir dizi erişim incelemesi görüntülemek için, erişim gözden geçirmelerine gidin ve yaklaşan örnekleri zamanlanan incelemelerde bulabilirsiniz.

    **Genel bakış** sayfasında ilerlemeyi görebilirsiniz. İnceleme tamamlanana kadar dizinde erişim hakkı değiştirilmez.

    ![Erişim gözden geçirmeleri ilerleme durumu](./media/complete-access-review/overview-progress.png)

1. Zamanlanan bitiş tarihine ulaşılmadan önce bir erişim incelemesini durdurmak istiyorsanız **Durdur** düğmesine tıklayın.

    Bir gözden geçirmeyi durdurduğunuzda, gözden geçirenler artık yanıt veremeyecektir. Bir gözden geçirmeyi durdurduktan sonra yeniden çalıştıramazsınız.

1. Artık erişim incelemesinin ilgilenmiyorsanız **Sil** düğmesine tıklayarak bunu silebilirsiniz.

## <a name="apply-the-changes"></a>Değişiklikleri Uygula

**Sonuçları kaynağa otomatik olarak Uygula** özelliği etkinleştirildiyse ve **tamamlanma ayarlarına**bağlı olarak seçimlerinizi temel alıyorsa otomatik uygulama, gözden geçirme işleminin bitiş tarihinden sonra veya gözden geçirmeyi el ile durdurduğunuzda yürütülür.

Gözden geçirme için **sonuçların otomatik olarak uygulanması** etkinleştirilmemişse, değişiklikleri el ile uygulamak için **Uygula** ' ya tıklayın. Gözden geçirme sırasında bir kullanıcının erişimi reddedildiyse, **Uygula**' ya TıKLADıĞıNıZDA Azure AD, üyeliklerini veya uygulama atamasını kaldırır.

![Erişim gözden geçirme değişikliklerini Uygula](./media/complete-access-review/apply-changes.png)

İnceleme durumu, **uygulama** ve son olarak durum **sonucu uygulanan**gibi ara durumlar aracılığıyla **tamamlandı** olarak değişir. Grup üyeliğinden veya uygulama atamasından birkaç dakika içinde kaldırılan reddedilen kullanıcıları görmeyi beklemelisiniz.

Yapılandırılmış bir otomatik uygulama incelemesi veya **Uygula** seçeneğinin belirlenmesi, şirket içi dizinde veya dinamik grupta bulunan bir grup üzerinde bir etkiye sahip değildir. Şirket içinde kaynak olan bir grubu değiştirmek istiyorsanız, sonuçları indirin ve bu değişiklikleri bu dizindeki grubun gösterimine uygulayın.

## <a name="retrieve-the-results"></a>Sonuçları alma

Bir kerelik erişim incelemesinin sonuçlarını görüntülemek için **sonuçlar** sayfasına tıklayın. Yalnızca bir kullanıcının erişimini görüntülemek için, arama kutusuna, erişimi gözden geçirilmiş bir kullanıcının görünen adını veya Kullanıcı asıl adını yazın.

![Erişim gözden geçirmesi için sonuçları alma](./media/complete-access-review/retrieve-results.png)

Yinelenen etkin bir erişim incelemesinin ilerlemesini görüntülemek için **sonuçlar** sayfasına tıklayın.

Yinelenen bir erişim incelemesinin tamamlanan bir örneğinin sonuçlarını görüntülemek için, **Gözden geçirme geçmişi**' ne tıklayın ve ardından örnek başlangıç ve bitiş tarihine göre tamamlanmış erişim gözden geçirme örnekleri listesinden belirli örneği seçin. Bu örneğin sonuçları, **sonuçlar** sayfasından elde edilebilir.

Bir erişim incelemesinin tüm sonuçlarını almak için **İndir** düğmesine tıklayın. Elde edilen CSV dosyası Excel 'de veya UTF-8 ile kodlanmış CSV dosyalarını açan diğer programlarda görüntülenebilir.

## <a name="remove-users-from-an-access-review"></a>Erişim gözden geçirden Kullanıcı kaldırma

 Varsayılan olarak, silinmiş bir kullanıcı Azure AD’de 30 gün boyunca silinmiş olarak kalır ve bu süre boyunca gerekirse bir yönetici tarafından geri alınabilir.  30 gün sonra bu kullanıcı kalıcı olarak silinir.  Ayrıca, bir Genel Yönetici bu süreye ulaşılmadan önce Azure Active Directory portalını kullanarak [kısa süre önce silinmiş bir kullanıcıyı kalıcı olarak silebilir](../fundamentals/active-directory-users-restore.md).  Bir kullanıcı kalıcı olarak silindikten sonra, bu kullanıcıya ilişkin sonraki veriler etkin erişim gözden geçirmelerinden kaldırılır.  Silinmiş kullanıcılara ilişkin denetim bilgileri, denetim günlüğünde kalır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD erişim gözden geçirmeleriyle kullanıcı erişimini yönetme](manage-user-access-with-access-reviews.md)
- [Azure AD erişim gözden geçirmeleriyle konuk erişimini yönetme](manage-guest-access-with-access-reviews.md)
- [Gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md)
- [Bir Azure AD yönetici rolündeki kullanıcılar için erişim gözden geçirmesi oluşturma](../privileged-identity-management/pim-how-to-start-security-review.md)
