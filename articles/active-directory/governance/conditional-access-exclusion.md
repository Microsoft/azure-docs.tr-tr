---
title: Koşullu erişim ilkelerinden dışlanan kullanıcıları yönetme-Azure AD
description: Koşullu erişim ilkelerinden dışlanan kullanıcıları yönetmek için Azure Active Directory (Azure AD) erişim incelemelerini nasıl kullanacağınızı öğrenin
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b2ac36ad1140968fd17db0bed0b60a8aca6a02
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532680"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Koşullu erişim ilkelerinden dışlanan kullanıcıları yönetmek için Azure AD erişim incelemelerini kullanın

İdeal bir dünyada, tüm kullanıcılar kuruluşunuzun kaynaklarına erişimi güvenli hale getirmek için erişim ilkelerini izler. Ancak bazen özel durumlar oluşturmanız gereken iş durumları vardır. Bu makale, dışlamaların gerekli olabileceği bazı durumlara örnek olarak geçer. BT Yöneticisi olarak bu görevi yönetebilir, ilke özel durumlarının fazla ilerlemesinin önüne kaçabilir ve bu özel durumların Azure Active Directory (Azure AD) erişim gözden geçirmeleri kullanılarak düzenli olarak inceleneceğini prova ile Denetçiler sağlayabilirsiniz.

>[!NOTE]
> Azure AD erişim gözden geçirmeleri kullanmak için geçerli bir Azure AD Premium P2, Enterprise Mobility + Security E5 ücretli veya deneme lisansı gerekir. Daha fazla bilgi için bkz. [Azure Active Directory sürümleri](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Kullanıcıları ilkelerden nasıl dışlıyorsunuz?

Yönetici olarak, çok faktörlü kimlik doğrulaması (MFA) istemek ve kimlik doğrulama isteklerini belirli ağlara veya cihazlara sınırlamak için [Azure AD koşullu erişimi](../conditional-access/concept-conditional-access-policy-common.md) kullanmaya karar verdiğinizi varsayalım. Dağıtım planlaması sırasında, tüm kullanıcıların bu gereksinimleri karşılamayabileceğini fark etmiş olursunuz. Örneğin, iç ağınızın bir parçası değil, uzak ofislerden çalışan kullanıcılarınız olabilir. Ayrıca, bu cihazların değiştirilmesini beklerken desteklenmeyen cihazları kullanarak bağlanan kullanıcılara da sahip olmanız gerekebilir. Kısacası, bu kullanıcıların oturum açmasını ve işlerini, koşullu erişim ilkelerinden dışlanmasını sağlamak için yapması gerekir.

Başka bir örnek olarak, kullanıcıların kiracılarına erişmesine izin vermek istemediğiniz bir ülke ve bölge kümesi belirtmek üzere koşullu erişim içinde [adlandırılmış konumlar](../conditional-access/location-condition.md) kullanıyor olabilirsiniz.

![Koşullu erişimde adlandırılmış konumlar](./media/conditional-access-exclusion/named-locations.png)

Ne yazık ki, bazı kullanıcılar hala bu engellenen ülkelerde/bölgelerde oturum açmak için geçerli bir neden olabilir. Örneğin, kullanıcılar iş için seyahat ediyor ve şirket kaynaklarına erişmesi gerekiyor. Bu durumda, bu ülkeleri/bölgeleri engellemeye yönelik koşullu erişim ilkesi, ilkeden çıkarılan kullanıcılar için bir bulut güvenlik grubu kullanabilir. Seyahat sırasında erişmesi gereken kullanıcılar, [Azure AD Self Servis Grup Yönetimi](../enterprise-users/groups-self-service-management.md)'ni kullanarak kendilerini gruba ekleyebilir.

Diğer bir örnek [, kullanıcılarınızın büyük çoğunluğu için eski kimlik doğrulamasını engelleyen](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/)koşullu erişim ilkeniz olabilir. Ancak, Office 2010 veya IMAP/SMTP/POP tabanlı istemciler aracılığıyla kaynaklarınıza erişmek için eski kimlik doğrulama yöntemlerini kullanması gereken bazı kullanıcılarınız varsa, bu kullanıcıları eski kimlik doğrulama yöntemlerini engelleyen ilkeden dışlayabilirsiniz.

>[!NOTE]
>Microsoft, güvenlik duruşunuzu geliştirmek için kiracınızdaki eski protokollerin kullanımını engellemeniz önerilir.

## <a name="why-are-exclusions-challenging"></a>Dışlamaları neden zor?

Azure AD 'de bir kullanıcı kümesine koşullu erişim ilkesi kapsamı ekleyebilirsiniz. Ayrıca, Azure AD rolleri, bireysel kullanıcılar veya konuklar ' i seçerek dışlamaları yapılandırabilirsiniz. Dışlamalar yapılandırıldığında, ilke hedefinin dışlanan kullanıcılara zorlanmayacağını aklınızda bulundurmanız gerekir. Dışlamalar, bir kullanıcı listesi veya eski şirket içi güvenlik grupları kullanılarak yapılandırılmışsa, dışlamalarla sınırlı görünürlüğe sahip olursunuz. Sonuç olarak:

- Kullanıcılar Dışlandıklarından emin olmayabilir.

- Kullanıcılar, ilkeyi atlamak için güvenlik grubuna katılabilir.

- Dışlanan kullanıcılar dışlama için uygun olmayabilir ancak artık buna uygun olmayabilir.

Genellikle, bir dışlamayı ilk yapılandırdığınızda, ilkeyi atlayan kullanıcıların shortlistesi vardır. Zaman içinde, daha fazla Kullanıcı dışlamaya eklenir ve liste artar. Bir noktada listeyi gözden geçirmeniz ve bu kullanıcıların her birinin dışlamaya uygun olduğunu onaylamanız gerekir. Dışlama listesini bir teknik bakış noktasından yönetmek nispeten kolay olabilir, ancak iş kararlarını kim yapabilir ve bunların tamamen denetlenebilir olmasını nasıl sağlar? Ancak, bir Azure AD grubu kullanarak dışlamasını yapılandırırsanız, erişim incelemelerini bir telafi denetimi olarak kullanabilir, görünürlüğü ve dışlanan kullanıcı sayısını azaltabilirsiniz.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Koşullu erişim ilkesinde dışlama grubu oluşturma

Yeni bir Azure AD grubu ve bu gruba uygulanmayan bir koşullu erişim ilkesi oluşturmak için bu adımları izleyin.

### <a name="create-an-exclusion-group"></a>Dışlama grubu oluşturma

1. Azure portalında oturum açın.

2. Sol gezinti bölmesinde **Azure Active Directory** ' a ve ardından **gruplar**' a tıklayın.

3. En üstteki menüde **Yeni Grup** ' a tıklayarak Grup bölmesini açın.

4. **Grup türü** listesinde **güvenlik**' i seçin. Bir ad ve açıklama belirtin.

5. **Üyelik** türünü **atanmış** olarak ayarladığınızdan emin olun.

6. Bu dışlama grubunun parçası olması gereken kullanıcıları seçin ve ardından **Oluştur**' a tıklayın.

![Azure Active Directory yeni grup bölmesi](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Grubu dışlayan bir koşullu erişim ilkesi oluşturma

Artık, bu dışlama grubunu kullanan bir koşullu erişim ilkesi oluşturabilirsiniz.

1. Sol gezinti bölmesinde **Azure Active Directory** ' a ve ardından **koşullu erişim** ' e tıklayarak **ilkeler** dikey penceresini açın.

2. Yeni **ilke** ' ye tıklayarak **Yeni** bölmeyi açın.

3. Bir ad belirtin.

4. Atamalar ' ın altında **Kullanıcılar ve gruplar**' a tıklayın.

5. **Dahil et** sekmesinde, **tüm kullanıcılar**' ı seçin.

6. **Dışla** sekmesinde, **Kullanıcılar ve gruplar** 'a onay işareti ekleyin ve **Dışlanan kullanıcıları seç**' e tıklayın.

7. Oluşturduğunuz dışlama grubunu seçin.

   > [!NOTE] 
   > En iyi uygulama olarak, kiracınızdan kilitlenmediğinden emin olmak için, test edilirken en az bir yönetici hesabının dışlanmasını öneririz.

8. Kurumsal gereksinimlerinize göre koşullu erişim ilkesini ayarlamaya devam edin.

![Koşullu erişimde dışlanan kullanıcılar bölmesini seçin](./media/conditional-access-exclusion/select-excluded-users.png)
  
Koşullu erişim ilkelerinde dışlamaları yönetmek için erişim incelemelerini kullanabileceğiniz iki örneği ele alalım.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Örnek 1: Engellenen ülkelerden/bölgelerden erişen kullanıcılar için erişim gözden geçirmesi

Belirli ülkelerden/bölgelerden erişimi engelleyen bir koşullu erişim ilkeniz olduğunu varsayalım. Bu, ilkeden dışlanan bir grubu içerir. Grubun üyelerinin gözden geçirilmesi durumunda önerilen bir erişim incelemesi aşağıda verilmiştir.

> [!NOTE] 
> Erişim İncelemeleri oluşturmak için bir genel yönetici veya Kullanıcı Yöneticisi rolü gereklidir.

1. Gözden geçirme her hafta gerçekleştirilir.

2. Bu dışlama grubunu en güncel durumda tutduğunuzdan emin olmak için hiçbir zaman bitmeyecektir.

3. Bu grubun tüm üyeleri gözden geçirme kapsamında olacaktır.

4. Her kullanıcının bu engellenen ülkeler/bölgelerden hala erişmesi gereken kendi kendine test olması gerekir, bu nedenle yine de grubun üyesi olmaları gerekir.

5. Kullanıcı, gözden geçirme isteğine yanıt vermezse, bunlar gruptan otomatik olarak kaldırılır ve bu ülkelere/bölgelere yolculuk sırasında artık kiracıya erişemez.

6. Kullanıcıların erişim incelemesinin başlangıcını ve tamamlanmasını bilmesini sağlamak için e-posta bildirimlerini etkinleştirin.

    ![Bir erişim gözden geçirme bölmesi oluşturun örneğin 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Örnek 2: eski kimlik doğrulamasıyla erişen kullanıcılar için erişim gözden geçirmesi

Eski kimlik doğrulaması ve daha eski istemci sürümlerini kullanan kullanıcılar için erişimi engelleyen bir koşullu erişim ilkeniz olduğunu ve bu, ilkeden dışlanan bir grubu içermenizi sağlar. Grubun üyelerinin gözden geçirilmesi durumunda önerilen bir erişim incelemesi aşağıda verilmiştir.

1. Bu incelemenin yinelenen bir gözden geçirme olması gerekir.

2. Gruptaki herkesin gözden geçirilmesi gerekir.

3. Bu, iş birimi sahiplerini seçili gözden geçirenler olarak listelemek üzere yapılandırılabilir.

4. Sonuçları otomatik olarak uygulayın ve eski kimlik doğrulama yöntemlerini kullanarak devam etmek üzere onaylanmamış kullanıcıları kaldırın.

5. Büyük grupların gözden geçirenlerin kararlarına kolayca karar verebilmeleri için önerilerin etkinleştirilmesi yararlı olabilir.

6. Kullanıcılara erişim incelemesinin başlatılması ve tamamlanması hakkında bildirim almak için posta bildirimlerini etkinleştirin.

    ![Bir erişim gözden geçirme bölmesi oluşturun örneğin 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Çok sayıda dışlama grubunuz varsa ve bu nedenle birden çok erişim incelemesi oluşturmanız gerekiyorsa, Microsoft Graph Beta uç noktasında onları programlı bir şekilde oluşturmanıza ve yönetmenize olanak tanıyan bir API sunuyoruz. Başlamak için bkz. [Azure AD erişim gözden GEÇIRMELERI API başvurusu](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) ve [MICROSOFT Graph aracılığıyla Azure AD erişim gözden geçirmeleri alma örneği](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Erişim gözden geçirmesi sonuçları ve denetim günlükleri

Artık her şeye, gruba, koşullu erişim ilkesine ve erişim incelemelerine sahip olduğunuza göre, bu incelemelerdeki sonuçların izlenmesi ve izlenmesi zaman alabilir.

1. Azure portal, **erişim İncelemeleri** dikey penceresini açın.

2. Dışlama grubunu yönetmek için oluşturduğunuz denetim ve programı açın.

3. Listede kalmak ve kimin kaldırılmadığını görmek için **sonuçlar** ' a tıklayın.

    ![Erişim gözden geçirmeleri sonuçları kimin onaylandığını gösterir](./media/conditional-access-exclusion/access-reviews-results.png)

4. Ardından, bu gözden geçirme sırasında gerçekleştirilen eylemleri görmek için **Denetim günlükleri** ' ne tıklayın.

    ![Erişim gözden geçirmeleri denetim günlüklerini listeleme eylemleri](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

BT Yöneticisi olarak, ilke dışlama gruplarını ilkelerinize göre yönetme bazen de kaçınılmaz. Bununla birlikte, bu grupları korumak, iş sahibi veya kullanıcıların kendileri tarafından düzenli olarak incelenmesi ve Azure AD erişim gözden geçirmeleri ile bu değişiklikleri denetlemek daha kolay hale getirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md)
- [Azure Active Directory Koşullu erişim nedir?](../conditional-access/overview.md)