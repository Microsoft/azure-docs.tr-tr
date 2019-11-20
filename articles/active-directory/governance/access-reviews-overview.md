---
title: Erişim gözden geçirmeleri nedir? -Azure Active Directory | Microsoft Docs
description: Azure Active Directory erişim gözden geçirmeleri kullanarak, kuruluşunuzdaki idare, risk yönetimi ve uyumluluk girişimlerini karşılamak için Grup üyeliği ve uygulama erişimini denetleyebilirsiniz.
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
ms.date: 08/05/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65def17622f026aa4869a4c60e7cb5146d56c5b0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389536"
---
# <a name="what-are-azure-ad-access-reviews"></a>Azure AD erişim gözden geçirmesi nedir?

Azure Active Directory (Azure AD) erişim gözden geçirmeleri, kuruluşların grup üyeliklerini verimli bir şekilde yönetmesine, kurumsal uygulamalara erişime ve rol atamalarına olanak tanır. Kullanıcının erişimi, yalnızca doğru kişilerin erişmeye devam ettiğinden emin olmak için düzenli olarak incelenebilir.

Erişim incelemelerine hızlı bir genel bakış sağlayan bir video aşağıda verilmiştir:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Erişim gözden geçirmeleri neden önemlidir?

Azure AD, kuruluşunuzun içinde ve iş ortakları gibi harici kuruluşlardaki kullanıcılarla işbirliği yapmanızı sağlar. Kullanıcılar gruplara katılabilir, konukları davet edebilir, bulut uygulamalarına bağlanabilir ve kendi iş veya kişisel cihazlarından uzaktan çalışabilir. Self Servis 'nin gücünden yararlanma kolaylığı, daha iyi erişim yönetimi özelliklerine ihtiyaç duymasına yol açmıştır.

- Yeni çalışanlar katılırsanız, doğru erişimi sağlamak için bunların üretken olmasını nasıl sağlayabilirsiniz?
- İnsanlar takımları taşıdıkça veya şirketten ayrıldığında, özellikle de konukları içeriyorsa eski erişiminin kaldırılmasına nasıl emin olabilirsiniz?
- Aşırı erişim hakları, erişim üzerinde denetim olmaması durumunda bulmalar ve bulguları denetlemeye yol açabilir.
- Kaynaklarına kimlerin erişimi olduğunu düzenli olarak gözden geçirdiklerinden emin olmak için kaynak sahipleriyle önceden önlem almanız gerekir.

## <a name="when-to-use-access-reviews"></a>Erişim gözden geçirmeleri ne zaman kullanılır?

- **Ayrıcalıklı rollerde çok fazla sayıda Kullanıcı:** Kaç kullanıcının yönetim erişimi olduğunu, bunların kaç tane genel yönetici olduğunu ve bir yönetim görevi için atandıktan sonra kaldırılmayan davet konukları veya iş ortakları olduğunu kontrol etmek iyi bir fikirdir. Genel Yöneticiler gibi [Azure AD rollerinde](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) rol atama kullanıcılarını veya [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) deneyiminde Kullanıcı erişimi Yöneticisi gibi [Azure kaynakları rollerini](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) yeniden ayarlayabilirsiniz.
- **Otomasyon uygulanabilir olmadığında:** Güvenlik gruplarında veya Office 365 gruplarında dinamik üyelik için kurallar oluşturabilirsiniz, ancak HR verileri Azure AD 'de değilse veya kullanıcıların değiştirme işlerini eğitmek üzere gruptan ayrıldıktan sonra hala erişime ihtiyacı varsa ne olur? Daha sonra, hala erişime ihtiyacı olan kişilerin erişmeye devam ettiğinden emin olmak için bu grupta bir gözden geçirme oluşturabilirsiniz.
- **Yeni bir amaç için bir grup kullanıldığında:** Azure AD ile eşitlenecek bir grubunuz varsa veya satış ekibi grubundaki herkes için uygulama Salesforce 'ı etkinleştirmeyi planlıyorsanız, Grup sahibinden farklı bir risk Co 'da kullanılan gruptan önce grup üyeliğini gözden geçirmesini istemek yararlı olacaktır. Erik.
- **İş açısından kritik veri erişimi:** belirli kaynaklar IÇIN, BT dışındaki kişilerin düzenli olarak oturumu açmasını ve denetim amacıyla erişmesi gereken nedenleri hakkında bir gerekçe vermesini istemek gerekebilir.
- **Bir ilkenin özel durum listesini korumak için:** İdeal bir dünyada, tüm kullanıcılar kuruluşunuzun kaynaklarına erişimi güvenli hale getirmek için erişim ilkelerini takip edecektir. Ancak bazen özel durumlar oluşturmanız gereken iş durumları vardır. BT Yöneticisi olarak, bu görevi yönetebilir, ilke özel durumlarının fazla ilerlemesinin önüne kaçabilir ve bu özel durumların düzenli olarak inceleneceğini prova ile Denetçiler sağlayabilirsiniz.
- **Grup sahiplerini, gruplarında hala Konuk olmaları gerektiğini onaylamasını isteyin:** Çalışan erişimi, bazı şirket içi ıAM ile otomatikleştirilebilir, ancak davet konukları konuklarıyla otomatik olabilir. Bir grup, iş açısından gizli içeriğe Konuklar erişimi veriyorsa, konukların hala erişim için meşru bir iş ihtiyacı olduğunu onaylamak için Grup sahibinin sorumluluğundadır.
- **Gözden geçirmeler düzenli aralıklarla Yinele:** Kullanıcılara haftalık, aylık, üç aylık veya yıllık olarak ayarlanan sıklıklara yönelik yinelenen erişim İncelemeleri ayarlayabilirsiniz ve her İnceleme başlangıcında gözden geçirenler bilgilendirilir. Gözden geçirenler kolay bir arabirimle ve akıllı önerilerin yardımıyla erişimi onaylayabilir veya reddedebilir.

## <a name="where-do-you-create-reviews"></a>İncelemeleri nerede oluşturursunuz?

Gözden geçirmek istediğinize bağlı olarak, Azure AD erişim gözden geçirmeleriyle, Azure AD kurumsal uygulamalarında (önizlemede) veya Azure AD PıM 'de erişim incelemenizi oluşturacaksınız.

| Kullanıcıların erişim hakları | Gözden geçirenler şu şekilde olabilir | İnceleme oluşturma tarihi | Gözden geçiren deneyim |
| --- | --- | --- | --- |
| Güvenlik grubu üyeleri</br>Office grubu üyeleri | Belirtilen gözden geçirenler</br>Grup sahipleri</br>Kendi kendini gözden geçirme | Azure AD erişim gözden geçirmeleri</br>Azure AD grupları | Erişim paneli |
| Bağlı bir uygulamaya atandı | Belirtilen gözden geçirenler</br>Kendi kendini gözden geçirme | Azure AD erişim gözden geçirmeleri</br>Azure AD kurumsal uygulamaları (önizlemede) | Erişim paneli |
| Azure AD rolü | Belirtilen gözden geçirenler</br>Kendi kendini gözden geçirme | [Azure AD PıM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure portalı |
| Azure Kaynak rolü | Belirtilen gözden geçirenler</br>Kendi kendini gözden geçirme | [Azure AD PıM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure portalı |


## <a name="onboard-access-reviews"></a>Erişim gözden geçirmeleri ekleme

Erişim gözden geçirmeleri eklemek için aşağıdaki adımları izleyin.

1. Genel yönetici veya Kullanıcı Yöneticisi olarak, erişim gözden geçirmeleri kullanmak istediğiniz [Azure Portal](https://portal.azure.com) oturum açın.

1. Sol gezinti bölmesinde **Azure Active Directory**' ye tıklayın.

1. Sol taraftaki menüden **kimlik**Yönetimi ' ne tıklayın.

1. **Erişim İncelemeleri**' ne tıklayın.
 
    ![Erişim İncelemeleri başlangıç sayfası](./media/access-reviews-overview/access-reviews-overview-onboard.png)

1. Sayfasında, **Şimdi** Ekle düğmesine tıklayın.
    
      ![Bulunan erişim İncelemeleri](./media/access-reviews-overview/access-reviews-overview-select-onboard.png)


## <a name="learn-about-access-reviews"></a>Erişim incelemeleri hakkında bilgi edinin

Erişim gözden geçirmeleri oluşturma ve gerçekleştirme hakkında daha fazla bilgi edinmek için şu kısa tanıtımı izleyin:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Kuruluşunuzda erişim gözden geçirmeleri dağıtmaya hazırsanız, bu adımları izleyerek, yöneticilerinizi eğitin, eğitmeniz ve ilk erişim incelemenizi oluşturmanız gerekir!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Lisans gereksinimleri

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="which-users-must-have-licenses"></a>Hangi kullanıcıların Lisansı olmalıdır?

Erişim incelemeleriyle etkileşim kuran her kullanıcının ücretli Azure AD Premium P2 lisansı olması gerekir. Örneğin:

- Erişim gözden geçirmesi oluşturan Yöneticiler
- Erişim gözden geçirmesi gerçekleştiren Grup sahipleri
- Gözden geçirenler olarak atanan kullanıcılar
- Kendi kendine İnceleme gerçekleştiren kullanıcılar

Ayrıca, Konuk kullanıcılardan kendi erişimini gözden geçirmesini isteyebilirsiniz. Kendi kuruluşunuzun kullanıcılarından birine atadığınız her ücretli Azure AD Premium P2 lisansı için, dış Kullanıcı tahsisatından beş adede kadar Konuk kullanıcıya davet etmek için Azure AD işletmeden işletmeye (B2B) kullanabilirsiniz. Bu Konuk kullanıcılar Azure AD Premium P2 özelliklerini de kullanabilir. Daha fazla bilgi için bkz. [Azure AD B2B işbirliği Lisanslama Kılavuzu](../b2b/licensing-guidance.md).

Sahip olmanız gereken lisansların sayısını belirlemenize yardımcı olacak bazı örnek senaryolar aşağıda verilmiştir.

| Senaryo | Hesaplama | Gerekli sayıda lisans |
| --- | --- | --- |
| Yönetici, 500 Kullanıcıyla Grup A 'nın erişim incelemesini oluşturur. 3 Grup sahibini gözden geçirenler olarak atar. | Her grup sahibine ait yönetici + 3 Lisansı için gözden geçirenler olarak 1 lisans. | 4 |
| Yönetici, 500 Kullanıcıyla Grup A 'nın erişim incelemesini oluşturur. Kendi kendine İnceleme yapar. | 1 her kullanıcı için yönetici + 500 lisansı için kendi kendini gözden geçirenler olarak lisanslayın. | 501 |
| Yönetici, 5 Kullanıcı ve 25 Konuk kullanıcıyla Grup B 'ye yönelik bir erişim incelemesi oluşturur. Kendi kendine İnceleme yapar. | 1 her kullanıcı için yönetici + 5 Lisansı için kendi kendini gözden geçirenler olarak lisanslayın.<br/>(Konuk kullanıcılar gereken 1:5 oranına göre ele alınmıştır) | 6 |
| Yönetici, 5 Kullanıcı ve 108 Konuk kullanıcıyla Grup C 'nin erişim incelemesini oluşturur. Kendi kendine İnceleme yapar. | 1 her kullanıcıya yönelik yönetici + 5 Lisansı, gerekli 1:5 oranında tüm 108 Konuk kullanıcıları kapsayacak şekilde, her bir kullanıcı için kendi kendine gözden geçirenler ve 16 ek lisans olarak lisanslayın.<br/>1 + 5 = 6 lisans, 5 @ no__t-06 = 30 Konuk kullanıcıyı kapsar. Kalan (108-5 @ no__t-06) = 78 Konuk kullanıcılar için 78/5 = 16 ek lisans gerekir. Bu nedenle Total, 6 + 16 = 22 lisans gerekir. | 22 |

Kullanım için lisansları atama hakkında daha fazla bilgi için, bkz. [Azure Active Directory portalını kullanarak lisans atama veya kaldırma](../fundamentals/license-users-groups.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Gruplar veya uygulamalar için erişim gözden geçirmesi oluşturma](create-access-review.md)
- [Bir Azure AD yönetici rolündeki kullanıcılar için erişim gözden geçirmesi oluşturma](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Gruplara veya uygulamalara erişimi gözden geçirme](perform-access-review.md)
- [Grupların veya uygulamaların erişim incelemesini tamamlar](complete-access-review.md)
