---
title: Erişim gözden geçirmeleriyle konuk erişimini yönetme-Azure Active Directory | Microsoft Docs
description: Konuk kullanıcıları bir grubun üyesi olarak yönetme veya Azure Active Directory erişim İncelemeleri olan bir uygulamaya atama
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
ms.date: 12/13/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a982b97bcef6a24f026bf6ad2943661db18d6af0
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499455"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmeleri ile konuk erişimini yönetme


Azure Active Directory (Azure AD) ile [Azure AD B2B özelliğini](../b2b/what-is-b2b.md)kullanarak kurumsal sınırlar genelinde işbirliğini kolayca etkinleştirebilirsiniz. Diğer kiracılardan Konuk kullanıcılar Yöneticiler veya [diğer kullanıcılar](../b2b/what-is-b2b.md)tarafından [davet](../b2b/add-users-administrator.md) edilebilir. Bu özellik, Microsoft hesapları gibi sosyal kimlikler için de geçerlidir.

Ayrıca, konuk kullanıcıların uygun erişime sahip olmasını da kolayca sağlayabilirsiniz. Konukların kendilerine veya bir karar veren bir erişim gözden geçirimine katılmasını ve konukilerin erişimini çözmesini (ya da attest) isteyebilirsiniz. Gözden geçirenler, Azure AD önerilerine dayanarak her kullanıcının erişiminin devam edip etmemesine yönelik girişler ekleyebilir. Erişim gözden geçirmesi tamamlandığında, değişiklikler yapabilir ve artık ihtiyaç duyulmayacak konuklara erişimi kaldırabilirsiniz.

> [!NOTE]
> Bu belge, konuk kullanıcıların erişimini gözden geçirmeye odaklanır. Yalnızca konukları değil tüm kullanıcıların erişimini gözden geçirmek istiyorsanız bkz. [erişim gözden geçirmeleri ile Kullanıcı erişimini yönetme](manage-user-access-with-access-reviews.md). Genel yönetici gibi yönetici rollerindeki kullanıcıların üyeliğini gözden geçirmek istiyorsanız, bkz. [Azure AD Privileged Identity Management bir erişim Incelemesi başlatma](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Önkoşullar

- Azure AD Premium P2

Daha fazla bilgi için [hangi kullanıcıların lisanslarına sahip olması gerektiğini](access-reviews-overview.md#which-users-must-have-licenses)görün.

## <a name="create-and-perform-an-access-review-for-guests"></a>Konuklar için erişim gözden geçirmesi oluşturma ve gerçekleştirme

İlk olarak, genel yönetici veya Kullanıcı Yöneticisi olarak, erişim gözden geçirmeleri kuruluşunuza hazırlanmaya devam etmek için [Identity idare sayfasına](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) gidin.

Azure AD, Konuk kullanıcıları gözden geçirmeye yönelik çeşitli senaryolar sunar.

Şunları gözden geçirebilirsiniz:

 - Azure AD 'de üye olarak bir veya daha fazla konuklara sahip bir grup.
 - Azure AD 'ye bağlanmış bir veya daha fazla Konuk kullanıcının atandığı bir uygulama. 

Ardından, her konuğa kendi erişimini gözden geçirmesini mi yoksa bir veya daha fazla kullanıcıdan her konuğun erişimini gözden geçirmesini mi isteyeceğinize karar verebilirsiniz.

 Bu senaryolar aşağıdaki bölümlerde ele alınmıştır.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Konuklarını bir grupta kendi üyeliklerini gözden geçirmesine sorma

Davet edilen ve bir gruba eklenen kullanıcıların erişime ihtiyacı olmaya devam etmesini sağlamak için erişim incelemelerini kullanabilirsiniz. Konuklara, bu gruptaki kendi üyeliklerini gözden geçirmesini kolayca sorabilirsiniz.

1. Gruba yönelik bir erişim incelemesi oluşturmak için, yalnızca Konuk Kullanıcı üyelerini dahil etmek üzere gözden geçirmeyi seçin ve üyeleri kendilerini gözden geçirin. Daha fazla bilgi için bkz. [gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md).

2. Her konuğa kendi üyeliklerini incelemesini isteyin. Varsayılan olarak, bir daveti kabul eden her konuk, Azure AD 'den erişim gözden geçirmesi bağlantısı ile bir e-posta alır. Azure AD, [gruplara veya uygulamalara erişimin nasıl incelemesinin](perform-access-review.md)yapılacağı konuklara ilişkin yönergeler içerir.

3. Gözden geçirenler bilgileri girdikten sonra erişim gözden geçirmesini durdurun ve değişiklikleri uygulayın. Daha fazla bilgi için bkz. [grupların veya uygulamaların erişim Incelemesini tamamlamaya](complete-access-review.md)yönelik.

4. Sürekli erişim için kendi ihtiyacını reddeden kullanıcıların yanı sıra, yanıt vermeyen kullanıcıları da kaldırabilirsiniz. Yanıt vermeyen kullanıcılar artık e-posta alamıyor.

5. Grup, erişim yönetimi için kullanılmıyorsa, gözden geçirmek üzere seçilmemiş kullanıcıları, davetini kabul etmediğiniz için de kaldırabilirsiniz. Kabul etmemelidir, davet edilen kullanıcının e-posta adresinin bir typo olduğunu gösterebilir. Bir grup dağıtım listesi olarak kullanılıyorsa, bazı Konuk kullanıcılar ilgili kişi nesneleri olduklarından katılmak üzere seçilmemiş olabilir.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Bir grup içindeki bir konuğun üyeliğini gözden geçirmek için sponsor sorun

Bir gruptaki devam eden üyelik gereksinimini gözden geçirmek için bir grubun sahibi gibi bir destekleyicisi isteyebilirsiniz.

1. Gruba yönelik bir erişim incelemesi oluşturmak için, yalnızca Konuk Kullanıcı üyelerini dahil etmek üzere gözden geçirmeyi seçin. Sonra bir veya daha fazla gözden geçiren belirtin. Daha fazla bilgi için bkz. [gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md).

2. Gözden geçirenlerden bilgileri girmelerini isteyin. Varsayılan olarak, her biri Azure AD 'den, [gruplara veya uygulamalara erişimi gözden geçirdikleri](perform-access-review.md)erişim paneli bağlantısı ile bir e-posta alır.

3. Gözden geçirenler bilgileri girdikten sonra erişim gözden geçirmesini durdurun ve değişiklikleri uygulayın. Daha fazla bilgi için bkz. [grupların veya uygulamaların erişim Incelemesini tamamlamaya](complete-access-review.md)yönelik.

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Konukların bir uygulamaya kendi erişimini incelemesini isteyin

Belirli bir uygulama için davet edilen kullanıcıların erişime ihtiyacı olmaya devam etmesini sağlamak için erişim incelemelerini kullanabilirsiniz. Konukların erişim için kendi ihtiyacını gözden geçirmesini kolayca isteyebilirsiniz.

1. Uygulamaya yönelik bir erişim incelemesi oluşturmak için, yalnızca konukları dahil etmek için gözden geçirmeyi seçin ve kullanıcılar kendi erişimini gözden geçirir. Daha fazla bilgi için bkz. [gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md).

2. Her konuğa uygulamaya kendi erişimini incelemesini isteyin. Varsayılan olarak, daveti kabul eden her konuk Azure AD 'den bir e-posta alır. Bu e-postada, kuruluşunuzun erişim panelinde erişim gözden geçirmesi bağlantısı bulunur. Azure AD, [gruplara veya uygulamalara erişimin nasıl incelemesinin](perform-access-review.md)yapılacağı konuklara ilişkin yönergeler içerir.

3. Gözden geçirenler bilgileri girdikten sonra erişim gözden geçirmesini durdurun ve değişiklikleri uygulayın. Daha fazla bilgi için bkz. [grupların veya uygulamaların erişim Incelemesini tamamlamaya](complete-access-review.md)yönelik.

4. Sürekli erişim için kendi ihtiyacını reddeden kullanıcılara ek olarak, yanıt vermeyen Konuk kullanıcıları da kaldırabilirsiniz. Yanıt vermeyen kullanıcılar artık e-posta alamıyor. Ayrıca, özellikle de yakın zamanda davet edilmeyen Konuk kullanıcıları da kaldırabilirsiniz. Bu kullanıcılar davetini kabul etmedi ve uygulama erişimi yoktu. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Bir kullanıcının bir uygulamaya erişimini gözden geçirmek için sponsor sorun

Uygulamaya devam etmek için konuk gereksinimini gözden geçirmek üzere bir uygulamanın sahibi gibi bir destekleyicisi isteyebilirsiniz.

1. Uygulamaya yönelik bir erişim incelemesi oluşturmak için, yalnızca konukları dahil etmek üzere gözden geçirmeyi seçin. Sonra bir veya daha fazla gözden geçiren olarak Kullanıcı belirtin. Daha fazla bilgi için bkz. [gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md).

2. Gözden geçirenlerden bilgileri girmelerini isteyin. Varsayılan olarak, her biri Azure AD 'den, [gruplara veya uygulamalara erişimi gözden geçirdikleri](perform-access-review.md)erişim paneli bağlantısı ile bir e-posta alır.

3. Gözden geçirenler bilgileri girdikten sonra erişim gözden geçirmesini durdurun ve değişiklikleri uygulayın. Daha fazla bilgi için bkz. [grupların veya uygulamaların erişim Incelemesini tamamlamaya](complete-access-review.md)yönelik.

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Konuklarına, genel olarak erişim ihtiyacını incelemeye sorma

Bazı kuruluşlarda, konuklar grup üyeliklerinin farkında olmayabilir.

> [!NOTE]
> Azure portal önceki sürümleri, UserType 'a sahip kullanıcılar tarafından yönetici erişimine izin vermedi. Bazı durumlarda, dizininizdeki bir yönetici, PowerShell kullanarak bir konuğun UserType değerini üye olarak değiştirmiş olabilir. Bu değişiklik dizininizde daha önce oluştuysa, önceki sorgu, yönetici erişim haklarına sahip olan tüm konuk kullanıcılarını içermeyebilir. Bu durumda, Konuk kullanıcı türünü değiştirmeniz veya Konuk grubunu grup üyeliğine el ile eklemeniz gerekir.

1. Uygun bir grup yoksa, konuklara Konuk olarak Azure AD 'de bir güvenlik grubu oluşturun. Örneğin, konukların el ile korunan bir üyeliğine sahip bir grup oluşturabilirsiniz. Ya da, contoso kiracısındaki kullanıcılar için UserType öznitelik değeri olan kullanıcılar için "contoso konukları" gibi bir ada sahip dinamik bir grup oluşturabilirsiniz.  Verimlilik için, grubun Predominately konukları olduğundan emin olun; üye kullanıcıların gözden geçirilmesi gerekmemesini sağlamak için üye kullanıcıları olan bir grup seçmeyin.  Ayrıca, grubun üyesi olan bir Konuk kullanıcının grubun diğer üyelerini görebileceğini aklınızda bulundurun.

2. Bu grup için bir erişim incelemesi oluşturmak için, üyelerin kendileri olacak gözden geçirenler ' i seçin. Daha fazla bilgi için bkz. [gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md).

3. Her konuğa kendi üyeliklerini incelemesini isteyin. Varsayılan olarak, bir daveti kabul eden her konuk, Azure AD 'den kuruluşunuzun erişim panelinde erişim incelemesi bağlantısını içeren bir e-posta alır. Azure AD, [gruplara veya uygulamalara erişimin nasıl incelemesinin](perform-access-review.md)yapılacağı konuklara ilişkin yönergeler içerir.  Davetini kabul etmediğiniz konukları, gözden geçirme sonuçlarında "bildirim yok" olarak görünür.

4. Gözden geçirenler giriş verdikten sonra erişim gözden geçirmesini durdurun. Daha fazla bilgi için bkz. [grupların veya uygulamaların erişim Incelemesini tamamlamaya](complete-access-review.md)yönelik.

5. Engellenen konuklara yönelik konuk erişimini kaldırma, gözden geçirmeyi tamamlamadım veya daha önce daveti kabul etmedi. Konulardan bazıları incelemeye katılmak üzere seçilmiş olan kişiler ise veya daha önce bir daveti kabul etmediyse, Azure portal veya PowerShell 'i kullanarak hesaplarını devre dışı bırakabilirsiniz. Konuğun artık erişim ihtiyacı yoksa ve bir kişi yoksa, Konuk Kullanıcı nesnesini silmek için Azure portal veya PowerShell 'i kullanarak kendi kullanıcı nesnelerini dizininizden kaldırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[Gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md)







