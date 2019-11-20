---
title: Koşullu erişim ilkelerinden dışlanan kullanıcıları yönetmek için erişim incelemelerini kullanın-Azure Active Directory | Microsoft Docs
description: Koşullu erişim ilkelerinden dışlanan kullanıcıları yönetmek için Azure Active Directory (Azure AD) erişim incelemelerini nasıl kullanacağınızı öğrenin
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
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 246503fec6436bf49dcd5fb89c2dc0ed345a43ca
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499907"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Koşullu erişim ilkelerinden dışlanan kullanıcıları yönetmek için Azure AD erişim incelemelerini kullanın

İdeal bir dünyada, tüm kullanıcılar kuruluşunuzun kaynaklarına erişimi güvenli hale getirmek için erişim ilkelerini takip edecektir. Ancak bazen özel durumlar oluşturmanız gereken iş durumları vardır. Bu makalede, dışlamaları gerekebilecek bazı örnekler ve BT Yöneticisi olarak BT Yöneticisi olarak bu görevi yönetebilir, ilke özel durumlarından daha fazla ilerleyebiliriz ve bu özel durumların Azure kullanarak düzenli olarak incelendiğini kanıtlamalarla denetçiler sağlayabilirsiniz Active Directory (Azure AD) erişim gözden geçirmeleri.

> [!NOTE]
> Azure AD erişim gözden geçirmeleri kullanmak için geçerli bir Azure AD Premium P2, Enterprise Mobility + Security E5 ücretli veya deneme lisansı gerekir. Daha fazla bilgi için bkz. [Azure Active Directory sürümleri](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Kullanıcıları ilkelerden nasıl dışlıyorsunuz?

BT Yöneticisi olarak, kullanıcıların Multi-Factor Authentication (MFA) kullanarak kimlik doğrulaması yapmasını veya güvenilir bir ağ ya da cihazdan oturum açmasını gerektirmek için [Azure AD koşullu erişimi](../conditional-access/overview.md) kullanabilirsiniz. Dağıtım planlaması sırasında, bu gereksinimlerin bazılarının tüm kullanıcılar tarafından karşılanmadığını görürsünüz. Örneğin, iç ağınızın parçası olmayan bir uzak Office 'ten çalışan kullanıcılar vardır veya desteklenmeyen eski bir telefonu kullanan bir yönetici vardır. İş, bu kullanıcıların oturum açmasına ve işlerini yapmasına izin verilmesini gerektirir, bu nedenle koşullu erişim ilkelerinden çıkarılır.

Başka bir örnek olarak, kullanıcıların kiracılarına erişmesine izin vermek istemediğiniz bir ilçe ve bölge kümesi yapılandırmak için koşullu erişimde [adlandırılmış konumları](../conditional-access/location-condition.md) kullanabilirsiniz.

![Koşullu erişimde adlandırılmış konumlar](./media/conditional-access-exclusion/named-locations.png)

Ancak, bazı durumlarda, kullanıcıların bu engellenen ülkelerde/bölgelerde oturum açması için yasal bir nedeni olabilir. Örneğin, kullanıcılar iş veya kişisel nedenlerle seyahat edebilir. Bu örnekte, bu ülkeleri/bölgeleri engellemeye yönelik koşullu erişim ilkesinin, ilkeden dışlanan kullanıcılar için ayrılmış bir bulut güvenlik grubu olabilir. Seyahat sırasında erişmesi gereken kullanıcılar, [Azure AD Self Servis Grup Yönetimi](../users-groups-roles/groups-self-service-management.md)'ni kullanarak kendilerini gruba ekleyebilir.

Diğer bir örnek [, kullanıcılarınızın büyük çoğunluğu için eski kimlik doğrulamasını engelleyen](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/)bir koşullu erişim ilkeniz olabilir. Microsoft, güvenlik duruşunuzu geliştirmek için kiracınızdaki eski protokollerin kullanımını engellemeniz önerilir. Ancak, Office 2010 veya IMAP/SMTP/POP tabanlı istemciler aracılığıyla kaynaklarınıza erişmek için kesinlikle eski kimlik doğrulama yöntemlerini kullanması gereken bazı kullanıcılarınız varsa, bu kullanıcıları eski kimlik doğrulama yöntemlerini engelleyen ilkeden hariç bırakabilirsiniz.

## <a name="why-are-exclusions-challenging"></a>Dışlamaları neden zor?

Azure AD 'de bir kullanıcı kümesine koşullu erişim ilkesi kapsamı ekleyebilirsiniz. Ayrıca, Azure AD rolleri, bireysel kullanıcılar veya kullanıcıların konukları ' nı seçerek bu kullanıcıların bazılarını da hariç bırakabilirsiniz. Bu Dışlamalar yapılandırıldığında, ilke hedefinin bu kullanıcılar için zorlanmayacağını unutmamak önemlidir. Bu Dışlamalar tek tek kullanıcıların bir listesi veya eski bir şirket içi güvenlik grubu olarak yapılandırılmışsa, bu dışlama listesinin görünürlüğünü (kullanıcılar varlığını bilmiyor olabilir) ve BT yöneticisinin denetimini ele alabilir (kullanıcılar ilkeye göre güvenlik grubu). Ayrıca, bir seferde dışlama için uygun olan kullanıcılar artık buna ihtiyaç duymayabilir.

Bir dışlamanın başlangıcında, ilkeyi atlayan kullanıcıların kısa bir listesi bulunur. Zamanla, daha fazla Kullanıcı dışarıda bırakılır ve liste büyür. Bir noktada, listeyi gözden geçirmeniz ve bu kullanıcıların her birinin hala dışlanacağını onaylamanız gerekir. Listenin teknik bir bakış noktasından yönetilmesi nispeten daha kolay olabilir, ancak iş kararlarını kim yapabilir ve bunların nasıl denetlenebilir olduğuna nasıl emin olabilirsiniz?

Ancak, bir Azure AD grubu kullanarak koşullu erişim ilkesine dışlamasını yapılandırırsanız, erişim incelemelerini bir telafi denetimi olarak kullanabilir, görünürlük ve özel bir duruma sahip olan kullanıcı sayısını azaltabilirsiniz.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Koşullu erişim ilkesinde dışlama grubu oluşturma

Yeni bir Azure AD grubu ve bu gruba uygulanmayan bir koşullu erişim ilkesi oluşturmak için bu adımları izleyin.

### <a name="create-an-exclusion-group"></a>Dışlama grubu oluşturma

1. Azure Portal’da oturum açın.

1. Sol gezinti bölmesinde **Azure Active Directory** ' a ve ardından **gruplar**' a tıklayın.

1. En üstteki menüde **Yeni Grup** ' a tıklayarak Grup bölmesini açın.

1. **Grup türü** listesinde **güvenlik**' i seçin. Bir ad ve açıklama belirtin.

1. **Üyelik** türünü **atanmış**olarak ayarladığınızdan emin olun.

1. Bu dışlama grubunun parçası olması gereken kullanıcıları seçin ve ardından **Oluştur**' a tıklayın.

    ![Azure Active Directory yeni grup bölmesi](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Grubu dışlayan bir koşullu erişim ilkesi oluşturma

Artık, bu dışlama grubunu kullanan bir koşullu erişim ilkesi oluşturabilirsiniz.

1. Sol gezinti bölmesinde **Azure Active Directory** ' a ve ardından **koşullu erişim** ' e tıklayarak **ilkeler** dikey penceresini açın.

1. Yeni **ilke** ' ye tıklayarak **Yeni** bölmeyi açın.

1. Bir ad belirtin.

1. Atamalar ' ın altında **Kullanıcılar ve gruplar**' a tıklayın.

1. **Dahil et** sekmesinde, **tüm kullanıcılar**' ı seçin.

1. **Dışla** sekmesinde, **Kullanıcılar ve gruplar** 'a onay işareti ekleyin ve **Dışlanan kullanıcıları seç**' e tıklayın.

1. Oluşturduğunuz dışlama grubunu seçin.

    > [!NOTE]
    > En iyi uygulama olarak, kiracınızdan kilitlenmediğinden emin olmak için, test edilirken en az bir yönetici hesabının dışlanmasını öneririz.

1. Kurumsal gereksinimlerinize göre koşullu erişim ilkesini ayarlamaya devam edin.

    ![Koşullu erişimde dışlanan kullanıcılar bölmesini seçin](./media/conditional-access-exclusion/select-excluded-users.png)

Koşullu erişim ilkelerinde dışlamaları yönetmek için erişim incelemelerini kullanabileceğiniz iki örneği ele alalım.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Örnek 1: Engellenen ülkelerden/bölgelerden erişen kullanıcılar için erişim gözden geçirmesi

Belirli ülkelerden/bölgelerden erişimi engelleyen bir koşullu erişim ilkeniz olduğunu varsayalım. Bu, ilkeden dışlanan bir grubu içerir. Grubun üyelerinin gözden geçirilmesi durumunda önerilen bir erişim incelemesi aşağıda verilmiştir.

> [!NOTE]
> Erişim İncelemeleri oluşturmak için bir genel yönetici veya Kullanıcı Yöneticisi rolü gereklidir.

1. Gözden geçirme her hafta yeniden gerçekleşmeyecektir.

2. Bu dışlama grubunu en güncel durumda tutduğunuzdan emin olmak için hiçbir zaman bitmeyecektir.

3. Bu grubun tüm üyeleri gözden geçirme kapsamında olacaktır.

4. Her kullanıcının kendi kendine bu engellenen ülkelerden/bölgelerden erişimleri olması gerektiğini belirten kendi kendine test yapması gerekecektir. bu nedenle yine de grubun üyesi olmaları gerekir.

5. Kullanıcı, gözden geçirme isteğine yanıt vermezse, bunlar gruptan otomatik olarak kaldırılır ve bu nedenle bu ülkelere/bölgelere yolculuk sırasında kiracıya artık erişemez.

6. Kullanıcılara erişim incelemesinin başlatılması ve tamamlanması hakkında bildirim almak için posta bildirimlerini etkinleştirin.

    ![Bir erişim gözden geçirme bölmesi oluşturun örneğin 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Örnek 2: Eski kimlik doğrulamasıyla erişen kullanıcılar için erişim gözden geçirmesi

Eski kimlik doğrulaması ve eski istemci sürümlerini kullanan kullanıcılar için erişimi engelleyen bir koşullu erişim ilkenizin olduğunu varsayalım. Bu, ilkeden dışlanan bir grubu içerir. Grubun üyelerinin gözden geçirilmesi durumunda önerilen bir erişim incelemesi aşağıda verilmiştir.

1. Bu incelemenin yinelenen bir gözden geçirme olması gerekir.

2. Gruptaki herkesin gözden geçirilmesi gerekir.

3. Bu, iş birimi sahiplerini seçili gözden geçirenler olarak listelemek üzere yapılandırılabilir.

4. Sonuçları otomatik olarak uygulayın ve eski kimlik doğrulama yöntemlerini kullanarak devam etmek üzere onaylanmamış kullanıcıları kaldırın.

5. Büyük grupların gözden geçirenlerin kararlarına kolayca karar verebilmeleri için önerilerin etkinleştirilmesi yararlı olabilir.

6. Kullanıcılara erişim incelemesinin başlatılması ve tamamlanması hakkında bildirim almak için posta bildirimlerini etkinleştirin.

    ![Bir erişim gözden geçirme bölmesi oluşturun örneğin 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Pro ipucu**: Çok sayıda dışlama grubunuz varsa ve bu nedenle birden çok erişim incelemesi oluşturmanız gerekiyorsa, Microsoft Graph Beta uç noktasında onları programlı bir şekilde oluşturmanıza ve yönetmenize olanak tanıyan bir API sunuyoruz. Başlamak için bkz. [Azure AD erişim gözden GEÇIRMELERI API başvurusu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) ve [MICROSOFT Graph aracılığıyla Azure AD erişim gözden geçirmeleri alma örneği](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Erişim gözden geçirmesi sonuçları ve denetim günlükleri

Artık her şeye, gruba, koşullu erişim ilkesine ve erişim incelemelerine sahip olduğunuza göre, bu incelemelerdeki sonuçların izlenmesi ve izlenmesi zaman alabilir.

1. Azure portal, **erişim İncelemeleri** dikey penceresini açın.

1. Dışlama grubunu yönetmek için oluşturduğunuz denetim ve programı açın.

1. Listede kalmak ve kimin kaldırılmadığını görmek için **sonuçlar** ' a tıklayın.

    ![Erişim gözden geçirmeleri sonuçları kimin onaylandığını gösterir](./media/conditional-access-exclusion/access-reviews-results.png)

1. Ardından, bu gözden geçirme sırasında gerçekleştirilen eylemleri görmek için **Denetim günlükleri** ' ne tıklayın.

    ![Erişim gözden geçirmeleri denetim günlüklerini listeleme eylemleri](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

BT Yöneticisi olarak, ilke dışlama gruplarını ilkelerinize göre yönetme bazen de kaçınılmaz. Bununla birlikte, bu grupları korumak, iş sahibi veya kullanıcıların kendileri tarafından düzenli olarak incelenmesi ve Azure AD erişim gözden geçirmeleri ile bu değişiklikleri denetlemek daha kolay hale getirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md)
- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)
