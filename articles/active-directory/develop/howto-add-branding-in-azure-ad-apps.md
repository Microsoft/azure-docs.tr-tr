---
title: Uygulama markalama yönergeleri | Mavisi
titleSuffix: Microsoft identity platform
description: Microsoft Identity platform için uygulama markalama yönergeleri hakkında bilgi edinin.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2020
ms.author: ryanwi
ms.reviewer: arielgo, jiml
ms.custom: aaddev, signin_art
ms.openlocfilehash: 236e82ab97244e1428441f83295f6a5d4ed56350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582001"
---
# <a name="branding-guidelines-for-applications"></a>Uygulamalar için markalama yönergeleri

Microsoft Identity platformu ile uygulama geliştirirken, müşterilerinizin iş veya okul hesabı (Azure AD 'de yönetiliyor) veya kişisel hesapları kullanarak, uygulamanıza kaydolmak ve oturum açmak istediğinizde müşterilerinizi yönlendirirsiniz.

Bu makalede şunları yapacaksınız:

- Microsoft tarafından yönetilen iki hesap türünü ve uygulamanızdan Azure AD hesaplarına nasıl başvuracağınızı öğreneceksiniz
- Uygulamanızda Microsoft logosu kullanma gereksinimlerini öğrenin
- Uygulamanızda kullanmak için resmi **Oturum açın** veya **Microsoft hesabıyla oturum açın** görsellerini indireceksiniz
- Marka ve gezinti konularında yapmanız ve yapmamanız gerekenleri öğreneceksiniz

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Microsoft kişisel hesapları ile iş veya okul hesapları karşılaştırması

Microsoft, iki tür kullanıcı hesabı yönetir:

- **Kişisel hesaplar** (eski adıyla Windows Live ID olarak da bilinir). Bu hesaplar, *bireysel* kullanıcılar ile Microsoft arasındaki ilişkiyi temsil eder ve tüketici cihazlarına ve Microsoft hizmetlerine erişmek üzere kullanılır. Bu hesaplar, kişisel kullanım için tasarlanmıştır.
- **İş veya okul hesapları.** Bu hesaplar, Azure Active Directory kullanan kuruluşlar adına Microsoft tarafından yönetilir. Bu hesaplar, Microsoft 'un Microsoft 365 ve diğer iş hizmetlerinde oturum açmak için kullanılır.

Microsoft iş veya okul hesapları genel olarak kuruluşlar (şirket, okul, devlet kurumu) tarafından son kullanıcılara (çalışanlar, öğrenciler, kamu çalışanları) atanır. Bu hesaplar doğrudan bulutta (Azure AD platformunda) veya Windows Server Active Directory gibi şirket içi bir dizinden Azure AD ile eşitlenir. Microsoft iş veya okul hesaplarının *koruyucusudur* ancak hesapların mülkiyeti ve denetimi kuruluşa aittir.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Uygulamanızda Azure AD hesaplarına başvurma

Microsoft, son kullanıcılara Azure veya Active Directory marka adlarını göstermez ve sizin de göstermemeniz gerekir.

- Kullanıcılar oturum açtıktan sonra mümkün olduğunca kuruluşun adını ve logosunu kullanın. Bu, “kuruluşunuz” gibi genel terimler kullanılmasından çok daha iyidir.
- Kullanıcılar oturum açmadıklarında hesaplarından “İş veya okul hesapları” olarak bahsedin ve bu hesapları Microsoft’un yönettiğini belirtmek üzere Microsoft logosunu kullanın. Kullanıcının kafasını karıştırabilecek şekilde “kuruluş hesabı”, “iş hesabı” veya “kurumsal hesap” gibi terimler kullanmayın.

## <a name="user-account-pictogram"></a>Kullanıcı hesabı piktogramı

Bu yönergelerin daha önceki bir sürümünde “mavi rozet” piktogramı kullanılmasını önermiştik. Kullanıcı ve geliştirici geri bildirimleri doğrultusunda şu anda bunun yerine Microsoft logosunun kullanılmasını öneriyoruz. Microsoft logosu, kullanıcıların uygulamanızda oturum açmak için Microsoft 365 veya diğer Microsoft iş hizmetleri ile kullandıkları hesabı yeniden kullanabileceğini anlamalarına yardımcı olur.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Azure AD ile kaydolma ve oturum açma

Uygulamanız kaydolma ve oturum açma için ayrı yollar sunabilir. Aşağıdaki bölümlerde her iki duruma uygun görsel kılavuz sağlanmaktadır.

**Uygulamanız son kullanıcının kaydolmasını destekliyorsa (örneğin, ücretsiz deneme sürümü veya freemium modeli)**: Kullanıcıların iş hesapları veya kişisel hesapları ile uygulamanıza erişmesine izin veren bir **oturum aç** düğmesi gösterebilirsiniz. Azure AD, uygulamanıza ilk eriştiklerinde bir izin alma istemi gösterecektir.

**Uygulamanız yalnızca yöneticilerin verebileceği izinleri gerektiriyorsa veya uygulamanız kuruluş lisansı gerektiriyorsa**: Yönetici alımını kullanıcı oturum açma işleminden ayırın. **“Bu uygulamayı al” düğmesi** yöneticileri oturum açma ekranına yeniden yönlendirecek ve ardından kuruluştaki kullanıcılar adına izin vermelerini isteyecektir; bu düğme, son kullanıcı izin alma istemlerini uygulamanıza gizleme açısından ek avantaj sunar.

## <a name="visual-guidance-for-app-acquisition"></a>Uygulama alımı için görsel kılavuz

“Uygulamayı al” bağlantınızın, Microsoft tarafından barındırılan kuruluş verilerine erişmek üzere bir kuruluş yöneticisinin uygulamanızı yetkilendirmesine izin vermek için kullanıcıyı Azure AD erişim verme (yetkilendirme) sayfasına yeniden yönlendirmesi gerekir. Nasıl erişim talep edileceğine ilişkin ayrıntılar [Integrating Applications with Azure Active Directory (Uygulamaları Azure Active Directory ile Tümleştirme)](./quickstart-register-app.md) makalesinde açıklanmaktadır.

Yönetici, uygulamanıza izin verdikten sonra, Kullanıcı Microsoft 365 uygulama başlatıcısı deneyimine (waffle ve kaynağından erişilebilir) eklemeyi seçebilirler [https://portal.office.com/myapps](https://portal.office.com/myapps) . Bu özelliği tanıtmak istiyorsanız “Bu uygulamayı kuruluşunuza ekleyin” gibi ifadeler kullanabilir ve aşağıdaki gibi bir düğme görüntüleyebilirsiniz:

![Microsoft logosu ve "kuruluşuma Ekle" metinlerinin gösterildiği düğme](./media/howto-add-branding-in-azure-ad-apps/add-to-my-org.png)

Ancak düğmelere güvenmek yerine açıklayıcı metinler yazmanızı öneririz. Örnek:

> *Zaten Microsoft 'tan Microsoft 365 veya başka bir iş hizmeti kullanıyorsanız, kuruluşunuzun verilerine <your_app_name> erişim izni verebilirsiniz. Bu, kullanıcılarınızın mevcut iş hesaplarıyla> <your_app_name erişmesine izin verir.*

Uygulamanızda kullanmak üzere resmi Microsoft logosunu indirmek için, kullanmak istediğiniz logoya sağ tıklayın ve ardından bilgisayarınıza kaydedin.

| Varlık                                | PNG biçimi | SVG biçimi |
| ------------------------------------ | ---------- | ---------- |
| Microsoft logosu  | ![PNG biçiminde indirilebilir Microsoft logosu](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.png) | ![SVG biçiminde indirilebilir Microsoft logosu](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Oturum açma için görsel kılavuz

Uygulamanızda kullanıcıları, Azure AD ile tümleştirme için kullandığınız protokole karşılık gelen oturum açma uç noktasına yönlendiren bir oturum açma düğmesinin görüntülenmesi gerekir. Aşağıdaki bölümde bu düğmenin nasıl görünmesi gerektiğine ilişkin ayrıntılı bilgi sağlanmaktadır.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogram ve “Microsoft ile Oturum Aç”

Microsoft logosu ve “Microsoft ile Oturum Aç” ifadesinin bir arada sunulması, Azure AD çözümünü uygulamanızın destekleyebileceği diğer kimlik sağlayıcılarından benzersiz şekilde ayıran özelliktir. “Microsoft ile Oturum Aç” ifadesinin tamamını içerecek kadar alanınız yoksa “Oturum Aç” olarak kısaltabilirsiniz. Düğmeler için açık veya koyu renk şeması kullanabilirsiniz.

Aşağıdaki diyagramda, uygulamanızla birlikte varlıkları kullanırken Microsoft tarafından önerilen kırmızı çizgiler gösterilmektedir. Kırmızı çizgiler, “Microsoft ile Oturum Aç” veya kısa hali “Oturum Aç” sürümü için geçerlidir.

!["Microsoft hesabıyla oturum açın" Redlines gösterir](./media/howto-add-branding-in-azure-ad-apps/sign-in-with-microsoft-redlines.png)

Uygulamanızda kullanmak üzere resmi görüntüleri indirmek için kullanmak istediğiniz görüntüye sağ tıklayın ve ardından görüntüyü bilgisayarınıza kaydedin.

| Varlık                                | PNG biçimi | SVG biçimi |
| ------------------------------------ | ---------- | ---------- |
| Microsoft ile Oturum Aç (koyu tema)  | ![İndirilebilir "Microsoft hesabıyla oturum açın" düğmesi Koyu tema PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.png) | ![İndirilebilir "Microsoft hesabıyla oturum açın" düğmesi Koyu tema SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.svg) |
| Microsoft ile Oturum Aç (açık tema) | ![İndirilebilir "Microsoft hesabıyla oturum açın" düğmesi açık Tema PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.png) | ![İndirilebilir "Microsoft hesabıyla oturum açın" düğmesi açık Tema SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.svg) |
| Oturum Aç (koyu tema)                 | ![İndirilebilir "oturum aç" kısa düğmesi Koyu tema PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.png) | ![İndirilebilir "oturum aç" kısa düğmesi Koyu tema SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.svg) |
| Oturum Aç (açık tema)                | ![İndirilebilir "oturum açma" kısa düğme açık teması PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.png) | ![İndirilebilir "oturum aç" kısa düğme açık teması SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.svg) |

## <a name="branding-dos-and-donts"></a>Markalama için Yapılması ve Yapılmaması Gerekenler

**YAPIN**: Son kullanıcıların kullanıp kullanamayacaklarını anlamalarına yardımcı olmak üzere ek açıklama sağlamak için “Microsoft ile Oturum Aç” düğmesi ile birlikte “iş veya okul hesabı” ifadesini kullanın. **YAPMAYIN**: “Kuruluş hesabı”, “iş hesabı” veya “kurumsal hesap” gibi başka ifadeler kullanmayın.”

"Microsoft 365 ID" veya "Azure ID **" kullanmayın.** Microsoft 365 Ayrıca, kimlik doğrulaması için Azure AD kullanmayan Microsoft tarafından sunulan bir tüketici sunumunun adıdır.

**YAPMAYIN**: Microsoft logosunu değiştirmeyin.

**YAPMAYIN**: Son kullanıcılara Azure veya Active Directory markalarını sunmayın. Ancak geliştiriciler, BT uzmanları ve yöneticilere sunabilirsiniz.

## <a name="navigation-dos-and-donts"></a>Gezinti için Yapılması ve Yapılmaması Gerekenler

**YAPIN**: Kullanıcılara, oturumu kapatma ve başka bir kullanıcı hesabına geçme olanağı sağlayın. Çoğu kişi tek bir Microsoft/Facebook/Google/Twitter hesabına sahip olsa da genellikle birden fazla kuruluş ile ilişkilidir. Birden fazla hesapla oturum açan kullanıcılar için destek yakında sunulacaktır.
