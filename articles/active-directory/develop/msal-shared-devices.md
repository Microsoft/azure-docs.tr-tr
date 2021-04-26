---
title: Paylaşılan cihaz moduna genel bakış
titleSuffix: Microsoft identity platform | Azure
description: Saflarda çalışanlarınız için cihaz paylaşımını etkinleştirmek üzere paylaşılan cihaz modu hakkında bilgi edinin.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: cf8869002fb3e0170331709af3da5b971a098740
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612407"
---
# <a name="overview-of-shared-device-mode"></a>Paylaşılan cihaz moduna genel bakış

Paylaşılan cihaz modu, saflarda çalışanları destekleyen ve bunlara dağıtılan cihazlarda paylaşılan cihaz modunu etkinleştiren uygulamalar oluşturmanıza olanak sağlayan bir Azure Active Directory özelliğidir.

>[!IMPORTANT]
> İOS için paylaşılan cihaz modu [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="what-are-frontline-workers"></a>Saflarda çalışanları nelerdir?

Önde gelen çalışanlar, perakende çalışanları, bakım ve alan aracıları, tıp personeli ve bir bilgisayarın başında oturmama veya işbirliği için kurumsal e-posta kullanan diğer kullanıcılardır. Aşağıdaki bölümler, önde gelen çalışanları desteklemeye yönelik yönleri ve zorlukları ve ardından, uygulamanızın bir kuruluşun saflarda çalışanları tarafından kullanılmasını sağlayan Microsoft tarafından sunulan özelliklere giriş yapmanızı sağlar.

### <a name="challenges-of-supporting-frontline-workers"></a>Saflarda çalışanları destekleme sorunları

Saflarda Worker iş akışlarının etkinleştirilmesi, genellikle tipik bilgi çalışanları tarafından sunulan güçlükleri içerir. Bu tür sorunlar, bir kuruluşun temel üretkenlik araçlarıyla yüksek ciro ücreti ve daha az benzerlik içerebilir. Saflarda çalışanlarının güçlenmesini sağlamak için kuruluşlar farklı stratejiler benimseme. Bazıları, çalışanlarının kendi kişisel telefonlarındaki iş uygulamalarını kullandığı bir kendi cihazını getir (BYOD) stratejisini benimseirken, diğer bir deyişle, çalışanları iPads veya Android tabletler gibi paylaşılan cihazları sağlıyor.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Tek bir kullanıcı için tasarlanan cihazlarda birden çok kullanıcıyı destekleme

İOS veya Android çalıştıran mobil cihazlar tek kullanıcılar için tasarlandığından, çoğu uygulama deneyimlerini tek bir kullanıcı tarafından kullanım açısından en iyi hale getirir. Bu en iyileştirilmiş deneyim kapsamında, uygulamalarda çoklu oturum açma özelliğinin etkinleştirilmesi ve kullanıcıların cihazında oturum açmış olması anlamına gelir. Bir Kullanıcı bir uygulamadan hesabını kaldırdığında, uygulama genellikle güvenlikle ilgili bir olay olarak kabul etmez. Birçok uygulama bile hızlı oturum açma için kullanıcının kimlik bilgilerini saklar. Mobil cihazınızdan bir uygulamayı silmiş ve sonra da hala oturum açmış olduğunuzu keşfettiğiniz zaman bu sorunla karşılaşmış olabilirsiniz.

### <a name="global-sign-in-and-sign-out-sso"></a>Genel oturum açma ve oturum kapatma (SSO)

Kuruluşun çalışanlarının uygulamalarını bu çalışanlar tarafından paylaşılan bir cihaz havuzunda kullanmasına izin vermek için, geliştiricilerin karşı deneyimi etkinleştirmeleri gerekir. Çalışanlar havuzdan bir cihaz seçebilmelidir ve vardiyalarına yönelik olarak "BT 'yi yap" için tek bir hareket gerçekleştirebilir. Bu kişilerin, kendi kişisel ve şirket bilgilerinin cihaz havuzuna geri dönebilmeleri için tüm kişisel ve şirket bilgilerini kaldırdığından başka bir hareket gerçekleştirebilmeleri gerekir. Ayrıca, bir çalışan oturumu kapatmak unutursa, cihazın otomatik olarak, vardiyası sonunda ve/veya bir süre etkinlik dışı kaldıktan sonra imzalanması gerekir.

Azure Active Directory, **paylaşılan cihaz modu** adlı bir özellik ile bu senaryolara izin vermez.

## <a name="introducing-shared-device-mode"></a>Paylaşılan cihaz moduna giriş

Belirtildiği gibi, paylaşılan cihaz modu, aşağıdakileri yapmanızı sağlayan Azure Active Directory bir özelliğidir:

* Saflarda çalışanlarını destekleyen uygulamalar oluşturun
* Cihazları saflarda çalışanlara dağıtın ve paylaşılan cihaz modunu açın

### <a name="build-applications-that-support-frontline-workers"></a>Saflarda çalışanlarını destekleyen uygulamalar oluşturun

*Paylaşılan cihaz modu* adlı bir cihaz durumunun etkinleştirilmesi için Microsoft kimlik doğrulama kitaplığı (msal) ve [Microsoft Authenticator uygulamasını](../user-help/user-help-auth-app-overview.md) kullanarak uygulamalarınızda bulunan saflarda çalışanlarını destekleyebilirsiniz. Bir cihaz paylaşılan cihaz modundayken, Microsoft, uygulamanın cihazdaki Kullanıcı durumuna bağlı olarak davranışını değiştirmesine izin vermek için bilgileri sağlar ve Kullanıcı verilerini koruyor.

Desteklenen özellikler şunlardır:

* Desteklenen herhangi bir uygulama aracılığıyla **bir kullanıcı cihazını genelinde oturum açın** .
* Desteklenen herhangi bir uygulama aracılığıyla **bir kullanıcı cihazını genelinde oturumu kapatın** .
* Uygulamanızın paylaşılan cihaz modundaki bir cihazda olup olmadığını öğrenmek için **cihazın durumunu sorgulayın** .
* Uygulamanızın en son kullanıldığı zamandan bu yana herhangi bir şeyin değişip değişmediğini öğrenmek için cihazdaki **kullanıcının cihaz durumunu sorgulayın** .

Paylaşılan cihaz modunun desteklenmesi, uygulamanız için hem bir güvenlik geliştirmesi hem de özellik yükseltmesi olarak düşünülmelidir ve sağlık ve finans gibi yüksek düzeyde düzenlenen ortamlarda benimsemesinin artırılmasına yardımcı olabilir.

Kullanıcılarınız, verilerinin başka bir kullanıcıya sızmasını sağlamak için size bağlıdır. Cihaz yönetme modu, uygulamanıza, yönetmeniz gereken bir değişikliğin gerçekleştiğini belirten yararlı sinyaller sağlar. Uygulamanızın, uygulamanın her kullanıldığı her seferinde kullanıcının durumunu denetlemesi, önceki kullanıcının verilerini temizleyerek sorumludur. Bu, çoklu görev sırasında arka planda yeniden yüklenmiş olup olmadığını içerir. Kullanıcı değişikliği üzerinde, hem önceki Kullanıcı verilerinin temizlendiğinden hem de uygulamanızda görüntülenen önbelleğe alınmış verilerin kaldırıldığından emin olmalısınız. Uygulamanıza paylaşılan cihaz modu özelliği ekledikten sonra her zaman kapsamlı bir güvenlik incelemesi işlemi gerçekleştirmenizi öneririz.

Uygulamalarınızı paylaşılan cihaz modunu destekleyecek şekilde değiştirme hakkında daha fazla bilgi için, bu makalenin sonundaki [sonraki adımlar](#next-steps) bölümüne bakın.

### <a name="deploy-devices-to-frontline-workers-and-turn-on-shared-device-mode"></a>Cihazları saflarda çalışanlara dağıtın ve paylaşılan cihaz modunu açın

Uygulamalarınız paylaşılan cihaz modunu destekledikten ve gerekli verileri ve güvenlik değişikliklerini dahil ederek, bunları saflarda çalışanları tarafından kullanılabilir olarak tanıtabilirsiniz.

Bir kuruluşun cihaz yöneticileri, cihazlarını ve uygulamalarınızı, Microsoft Intune gibi bir mobil cihaz yönetimi (MDM) çözümü aracılığıyla mağazalarına ve iş konumlarına dağıtabiyor. Sağlama sürecinin bir parçası, cihazı *paylaşılan bir cihaz* olarak işaretliyor. Yöneticiler, [Microsoft Authenticator uygulamasını](../user-help/user-help-auth-app-overview.md) dağıtarak ve paylaşılan cihaz modunu yapılandırma parametreleri aracılığıyla ayarlayarak paylaşılan cihaz modunu yapılandırır. Bu adımları gerçekleştirdikten sonra, paylaşılan cihaz modunu destekleyen tüm uygulamalar, Kullanıcı durumunu yönetmek ve cihaz ve kuruluşa yönelik güvenlik özellikleri sağlamak için Microsoft Authenticator uygulamasını kullanır.

## <a name="next-steps"></a>Sonraki adımlar

Paylaşılan cihaz modu için iOS ve Android platformlarını destekliyoruz. Uygulamalarınızda bulunan saflarda çalışanları desteklemeye başlamak için, platformunuzun aşağıdaki belgelerini inceleyin.

* [İOS için paylaşılan cihaz modunu destekleme](msal-ios-shared-devices.md)
* [Android için paylaşılan cihaz modunu destekleme](msal-android-shared-devices.md)
