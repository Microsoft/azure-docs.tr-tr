---
title: Frontline Worker yönetimi-Azure Active Directory
description: Personel Portalı ile sunulan saflarda Worker yönetim özellikleri hakkında bilgi edinin.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 32cee4ca0558166c44ff83c5ce9d61360e79e535
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969370"
---
# <a name="frontline-worker-management"></a>Frontline Worker yönetimi

Küresel iş gücünün yüzde 80 ' sinden fazla olması için Frontline Worker hesabı. Henüz yüksek ölçekli, hızlı ciro ve parçalanmış süreçler nedeniyle, en önde bulunan çalışanlar genellikle yoğun işleri biraz daha kolay hale getirmeye yönelik araçların olmamasıdır. Saflarda Worker Management, tüm saflarda iş gücünün tamamına dijital dönüşüm getirir. İş gücü Yöneticiler, saflarda çalışanları, işlemler ve BT içerebilir.

Önde bir çalışan yönetimi, aşağıdaki etkinliklerin daha kolay kullanılmasını sağlayarak saflarda iş gücünü güçler:
- Personmy ile yaygın BT görevlerini hızlandırma
- Basit kimlik doğrulaması aracılığıyla saflarda çalışanları kolay bir şekilde ekleme
- Paylaşılan cihazların sorunsuz bir şekilde sağlanması ve saflarda çalışanlarının güvenli bir şekilde kaydolması

## <a name="delegated-user-management-through-my-staff"></a>Personeliniz aracılığıyla Kullanıcı yönetimi temsilcisi

Azure Active Directory (Azure AD), personel portalı aracılığıyla Kullanıcı yönetiminin [sunum](../roles/my-staff-configure.md)yöneticileriyle, değerli zamandan tasarruf etme ve riskleri azaltmaya yardımcı olma yeteneği sağlar. Yöneticiler doğrudan mağaza 'dan veya fabrika katlarından Basitleştirilmiş parola sıfırlamaları ve telefon yönetimini etkinleştirerek, isteği yardım masası, BT veya işlemler aracılığıyla yönlendirmeksizin çalışanlara erişim izni verebilir.

![Personel Portalı 'nda temsilci Kullanıcı Yönetimi](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>Basitleştirilmiş kimlik doğrulaması ile hızlandırılmış ekleme

Personeliniz Ayrıca, saflarda yöneticilerinin [SMS oturum açma](../authentication/howto-authentication-sms-signin.md)için kendi takım üyelerinin telefon numaralarını kaydettirmelerini sağlar. Çoğu durumda, önde gelen çalışanlar yerel bir Kullanıcı adı ve parola birleşimini, genellikle çok sayıda, pahalı ve hataya açık bir çözümdür. SMS oturum açma kullanılarak kimlik doğrulaması yapıldığında, saflarda çalışanları yalnızca telefon numaralarını ve SMS aracılığıyla gönderilen tek seferlik geçiş kodunu (OTP) kullanan Microsoft ekipleri ve diğer uygulamalar için [Çoklu oturum açma (SSO)](../manage-apps/what-is-single-sign-on.md) ile oturum açabilir. Bu, en çok ihtiyaç duydukları uygulamalara hızlı erişim sağlayan, saflarda çalışanları için oturum açmayı basit ve güvenli hale getirir.

![SMS oturum açma](media/concept-fundamentals-frontline-worker/sms-signin.png)

Frontline yöneticileri, çalışanların Intune 'a kayıtlı Android ayrılmış cihazlarında belirli bir uygulama kümesine erişmesine izin vermek için yönetilen giriş ekranı (MHS) uygulamasını da kullanabilir. Adanmış cihazlar [Azure AD paylaşılan cihaz moduna](../develop/msal-shared-devices.md)kaydedilir. Microsoft Uç Nokta Yöneticisi (MEM) konsolunda birden çok uygulama bilgi noktası modunda yapılandırıldığında, MHS otomatik olarak cihazda varsayılan giriş ekranı olarak başlatılır ve son kullanıcıya *tek* giriş ekranı olarak görünür. Daha fazla bilgi edinmek için bkz. [Android Enterprise Için Microsoft tarafından yönetilen giriş ekranı uygulamasını yapılandırma](/mem/intune/apps/app-configuration-managed-home-screen-app).

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>Paylaşılan cihazlardan gelen saflarda çalışanları için güvenli kaydolma

Çok sayıda şirket paylaşılan cihazları kullanır. bu nedenle, saflarda çalışanları tek tek cihazları sağlama ve izleme yükü olmadan envanter yönetimi ve satış noktası işlemleri gerçekleştirebilir. Paylaşılan cihaz oturumu kapatma sayesinde, bir ön uç çalışanının, bir hub 'a geri teslim etmeden veya bir sonraki vardiyada bir ekipte bir ekip üzerine geçirmeden önce herhangi bir paylaşılan cihazdaki tüm uygulamalarda güvenli bir şekilde oturumunu kapatması çok kolay. Microsoft ekipleri, Paylaşılan cihazlarda Şu anda desteklenen uygulamalardan biridir ve saflarda çalışanlarının bunlara atanan görevleri görüntülemesine olanak tanır. Bir çalışan paylaşılan bir cihazda oturumu kapattıktan sonra, Intune ve Azure AD tüm şirket verilerini temizleyerek cihazın bir sonraki ilişkilendirmeye güvenle devredilmesi sağlanır. Bu özelliği, [Microsoft kimlik doğrulama kitaplığı](../develop/msal-overview.md)'nı kullanarak tüm Iş kolu [iOS](../develop/msal-ios-shared-devices.md) ve [Android](../develop/msal-android-shared-devices.md) uygulamalarınız ile tümleştirmeyi tercih edebilirsiniz.

![Paylaşılan cihaz oturumu kapatma](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>Sonraki adımlar

- Temsilci Kullanıcı yönetimi hakkında daha fazla bilgi için bkz. [personel Kullanıcı belgeleri](../user-help/my-staff-team-manager.md).
- SAP başarılı faktörlerinin gelen Kullanıcı sağlaması için, [Kullanıcı sağlamasını Active Directory IÇIN SAP başarılı faktörleri yapılandırma](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)öğreticisine bakın.
- Workday 'den gelen Kullanıcı sağlama için, [Otomatik Kullanıcı sağlama Için Workday yapılandırma](../saas-apps/workday-inbound-tutorial.md)hakkında öğreticiye bakın.
