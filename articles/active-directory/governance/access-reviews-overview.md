---
title: Erişim gözden geçirmeleri nedir? -Azure Active Directory | Microsoft Docs
description: Azure Active Directory erişim gözden geçirmeleri kullanarak, kuruluşunuzdaki idare, risk yönetimi ve uyumluluk girişimlerini karşılamak için Grup üyeliği ve uygulama erişimini denetleyebilirsiniz.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 10/29/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.custom: contperf-fy21q1
ms.openlocfilehash: 22bfef17f68b2e83e4f7462d8e9af6fb8aacc284
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246749"
---
# <a name="what-are-azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmesi nedir?

Azure Active Directory (Azure AD) erişim gözden geçirmeleri, kuruluşların grup üyeliklerini verimli bir şekilde yönetmesine, kurumsal uygulamalara erişime ve rol atamalarına olanak tanır. Kullanıcının erişimi, yalnızca doğru kişilerin erişmeye devam ettiğinden emin olmak için düzenli olarak incelenebilir.

Erişim incelemelerine hızlı bir genel bakış sağlayan bir video aşağıda verilmiştir:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Erişim gözden geçirmeleri neden önemlidir?

Azure AD, kuruluşunuzun içinden ve dış kullanıcılarla işbirliği yapmanıza olanak sağlar. Kullanıcılar gruplara katılabilir, konukları davet edebilir, bulut uygulamalarına bağlanabilir ve kendi iş veya kişisel cihazlarından uzaktan çalışabilir. Self Servis kullanmanın rahatlığı, daha iyi erişim yönetimi özelliklerine ihtiyaç duyar.

- Yeni çalışanlar katılırsanız, üretken olmaları gereken erişimleri nasıl sağlar?
- İnsanlar takımları taşıdıkça veya şirketten ayrıldığında, eski erişiminin kaldırılmasına nasıl emin olabilirsiniz?
- Aşırı erişim hakları, bu sonuçlara yol açabilir.
- Aşırı erişim hakkı, erişim üzerinde denetim olmaması durumunda da denetim bulguları oluşmasına neden olabilir.
- Kaynaklarına kimlerin erişimi olduğunu düzenli olarak gözden geçirdiklerinden emin olmak için kaynak sahipleriyle önceden önlem almanız gerekir.

## <a name="when-should-you-use-access-reviews"></a>Erişim gözden geçirmeleri ne zaman kullanılmalıdır?

- **Ayrıcalıklı rollerde çok fazla sayıda Kullanıcı:** Kaç kullanıcının yönetim erişimi olduğunu, bunların kaç tane genel yönetici olduğunu ve bir yönetim görevi için atandıktan sonra kaldırılmayan davet konukları veya iş ortakları olduğunu kontrol etmek iyi bir fikirdir. Genel Yöneticiler gibi [Azure AD rollerinde](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) rol atama kullanıcılarını veya [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) deneyiminde Kullanıcı erişimi Yöneticisi gibi [Azure kaynakları rollerini](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) yeniden ayarlayabilirsiniz.
- **Otomasyon mümkün olmadığında:** Güvenlik gruplarında veya Microsoft 365 gruplarında dinamik üyelik için kurallar oluşturabilirsiniz, ancak HR verileri Azure AD 'de değilse veya kullanıcıların değiştirme işlerini eğitmek üzere gruptan ayrıldıktan sonra hala erişime ihtiyacı varsa ne olur? Daha sonra, hala erişime ihtiyacı olan kişilerin erişmeye devam ettiğinden emin olmak için bu grupta bir gözden geçirme oluşturabilirsiniz.
- **Yeni bir amaç için bir grup kullanıldığında:** Azure AD ile eşitlenecek bir grubunuz varsa veya satış ekibi grubundaki herkes için uygulama Salesforce 'ı etkinleştirmeyi planlıyorsanız, Grup sahibinden farklı bir risk içeriğinde kullanılan gruptan önce grup üyeliğini gözden geçirmesini istemek yararlı olacaktır.
- **İş açısından kritik veri erişimi:** belirli kaynaklar IÇIN, BT dışındaki kişilerin düzenli olarak oturumu açmasını ve denetim amacıyla erişmesi gereken nedenleri hakkında bir gerekçe vermesini istemek gerekebilir.
- **Bir ilkenin özel durum listesini korumak için:** İdeal bir dünyada, tüm kullanıcılar kuruluşunuzun kaynaklarına erişimi güvenli hale getirmek için erişim ilkelerini takip edecektir. Ancak bazen özel durumlar oluşturmanız gereken iş durumları vardır. BT Yöneticisi olarak, bu görevi yönetebilir, ilke özel durumlarının fazla ilerlemesinin önüne kaçabilir ve bu özel durumların düzenli olarak inceleneceğini prova ile Denetçiler sağlayabilirsiniz.
- **Grup sahiplerini, gruplarında hala Konuk olmaları gerektiğini onaylamasını isteyin:** Çalışan erişimi, bazı şirket içi kimlik ve erişim yönetimi (ıAM) ile otomatikleştirilebilir, ancak davet edilmemelidir. Bir grup, iş açısından gizli içeriğe Konuklar erişimi veriyorsa, konukların hala erişim için meşru bir iş ihtiyacı olduğunu onaylamak için Grup sahibinin sorumluluğundadır.
- **Gözden geçirmeler düzenli aralıklarla Yinele:** Kullanıcılara haftalık, aylık, üç aylık veya yıllık olarak ayarlanan sıklıklara yönelik yinelenen erişim İncelemeleri ayarlayabilirsiniz ve her İnceleme başlangıcında gözden geçirenler bilgilendirilir. Gözden geçirenler kolay bir arabirimle ve akıllı önerilerin yardımıyla erişimi onaylayabilir veya reddedebilir.

>[!NOTE]
>Erişim gözden geçirmeleri denemeye hazırsanız, [gruplar veya uygulamalar için erişim gözden geçirmesi oluşturmaya](create-access-review.md) göz atın

## <a name="where-do-you-create-reviews"></a>İncelemeleri nerede oluşturursunuz?

Gözden geçirmek istediğinize bağlı olarak, Azure AD erişim gözden geçirmeleriyle, Azure AD kurumsal uygulamalarında (önizlemede) veya Azure AD PıM 'de erişim incelemenizi oluşturacaksınız.

| Kullanıcıların erişim hakları | Gözden geçirenler şu şekilde olabilir | İnceleme oluşturma tarihi | Gözden geçiren deneyim |
| --- | --- | --- | --- |
| Güvenlik grubu üyeleri</br>Office grubu üyeleri | Belirtilen gözden geçirenler</br>Grup sahipleri</br>Kendi kendini gözden geçirme | Azure AD erişim gözden geçirmeleri</br>Azure AD grupları | Erişim paneli |
| Bağlı bir uygulamaya atandı | Belirtilen gözden geçirenler</br>Kendi kendini gözden geçirme | Azure AD erişim gözden geçirmeleri</br>Azure AD kurumsal uygulamaları (önizlemede) | Erişim paneli |
| Azure AD rolü | Belirtilen gözden geçirenler</br>Kendi kendini gözden geçirme | [Azure AD PıM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure portalı |
| Azure Kaynak rolü | Belirtilen gözden geçirenler</br>Kendi kendini gözden geçirme | [Azure AD PıM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure portalı |

## <a name="license-requirements"></a>Lisans gereksinimleri

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Kaç lisansa sahip olmanız gerekir?

Dizininiz, aşağıdaki görevleri gerçekleştiren çalışanların sayısı kadar en az sayıda Azure AD Premium P2 lisansı gerektirir:

-   Gözden geçirenler olarak atanan üye kullanıcıları
-   Kendi kendine İnceleme gerçekleştiren üye kullanıcıları
-   Erişim gözden geçirmesi gerçekleştiren Grup sahipleri olarak üye kullanıcılar
-   Erişim gözden geçirmesi gerçekleştiren uygulama sahipleri olarak üye kullanıcılar

Konuk kullanıcılar için lisanslama gereksinimlerine bağlı olarak, kullanmakta olduğunuz lisanslama modeline bağlıdır. Ancak, aşağıdaki konuk kullanıcıların etkinlikleri Azure AD Premium P2 kullanımı olarak kabul edilir:

-   Gözden geçirenler olarak atanan Konuk kullanıcılar
-   Kendi kendine İnceleme gerçekleştiren Konuk kullanıcılar
-   Erişim gözden geçirmesi gerçekleştiren Grup sahipleri olarak Konuk kullanıcılar
-   Erişim gözden geçirmesi gerçekleştiren uygulama sahipleri olarak Konuk kullanıcılar


Azure AD Premium P2 lisansları, erişim gözden geçirmeleri kuran, ayarları yapılandıran veya kararların kararlarını uygulayan genel yönetici veya Kullanıcı Yöneticisi rollerine sahip kullanıcılar için gerekli **değildir** .

Azure AD Konuk Kullanıcı erişimi, 1:5 Ratio faturalandırma modelinin yerini alan aylık etkin kullanıcılar (MAU) Faturalandırma modelini temel alır. Daha fazla bilgi için bkz. [Azure AD dış kimlikler fiyatlandırması](../external-identities/external-identities-pricing.md).

Lisanslar hakkında daha fazla bilgi için bkz. [Azure Active Directory portalını kullanarak lisans atama veya kaldırma](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Örnek lisans senaryoları

Sahip olmanız gereken lisans sayısını belirlemenize yardımcı olacak bazı örnek lisans senaryoları aşağıda verilmiştir.

| Senaryo | Hesaplama | Lisans sayısı |
| --- | --- | --- |
| Yönetici, 75 Kullanıcı ve 1 Grup sahibi ile A grubu için bir erişim incelemesi oluşturur ve Grup sahibini gözden geçiren olarak atar. | 1 Grup sahibi için gözden geçiren olarak lisans | 1 |
| Yönetici, 500 Kullanıcı ve 3 Grup sahibi olan B grubunun erişim incelemesini oluşturur ve 3 Grup sahibini gözden geçirenler olarak atar. | Her grup sahibi için gözden geçirenler olarak 3 lisans | 3 |
| Yönetici, 500 Kullanıcıyla Grup B 'ye erişim incelemesi oluşturur. Kendi kendine İnceleme yapar. | Her Kullanıcı için kendi kendini gözden geçirenler olarak 500 lisans | 500 |
| Yönetici, 50 üye kullanıcıları ve 25 Konuk kullanıcıyla Grup C 'nin erişim incelemesini oluşturur. Kendi kendine İnceleme yapar. | Her Kullanıcı için kendi kendine gözden geçirenler olarak 50 lisans. * | 50 |
| Yönetici, 6 üye kullanıcısı ve 108 Konuk kullanıcıyla Grup D 'ye yönelik bir erişim incelemesi oluşturur. Kendi kendine İnceleme yapar. | Her Kullanıcı için kendi kendini gözden geçirenler olarak 6 lisans. Konuk kullanıcılar aylık bir etkin kullanıcı (MAU) temelinde faturalandırılır. Ek lisans gerekmez. *  | 6 |

\* Azure AD dış kimlikleri (Konuk Kullanıcı) fiyatlandırması, aylık etkin kullanıcıları (MAU) temel alır. Bu, bir takvim ayı içinde kimlik doğrulama etkinliği olan benzersiz kullanıcıların sayısıdır. Bu model, kiracınızdaki Azure AD Premium her bir lisans için beş adede kadar Konuk kullanıcıya izin veren 1:5 orandaki faturalandırma modelinin yerini alır. Kiracınız bir aboneliğe bağlandığında ve konuk kullanıcılarla işbirliği yapmak için dış kimlikler özelliklerini kullandığınızda, MAU tabanlı faturalandırma modeli kullanılarak otomatik olarak faturalandırılırsınız. Daha fazla bilgi için bkz. Azure AD dış kimlikleri için faturalandırma modeli.

## <a name="next-steps"></a>Sonraki adımlar

- [Gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md)
- [Bir Azure AD yönetici rolündeki kullanıcılar için erişim gözden geçirmesi oluşturma](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Gruplara veya uygulamalara erişimi gözden geçirme](perform-access-review.md)
- [Grupların veya uygulamaların erişim incelemesini tamamlar](complete-access-review.md)
