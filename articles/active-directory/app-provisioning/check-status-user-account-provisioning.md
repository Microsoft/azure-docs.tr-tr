---
title: SaaS uygulamalarına otomatik Kullanıcı hesabı sağlamayı bildir
description: Otomatik Kullanıcı hesabı sağlama işlerinin durumunu denetlemeyi ve bireysel kullanıcıların sağlanması hakkında nasıl sorun gidermeyi öğrenin.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/09/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5d93cec201feeb43700fe849f2fddc8eaf22488a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579493"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Öğretici: otomatik Kullanıcı hesabı sağlama üzerinde raporlama

Azure Active Directory (Azure AD), uçtan uca kimlik yaşam döngüsü yönetimi amacıyla SaaS uygulamalarındaki ve diğer sistemlerdeki Kullanıcı hesaplarının sağlamasını otomatik hale getirmeye yardımcı olan bir [Kullanıcı hesabı sağlama hizmeti](user-provisioning.md) içerir. Azure AD, [burada](../saas-apps/tutorial-list.md)Kullanıcı sağlama öğreticilerine sahip tüm uygulamalar ve sistemler için önceden tümleştirilmiş Kullanıcı sağlama bağlayıcılarını destekler.

Bu makalede, kurulumu yapıldıktan sonra sağlama işlerinin durumunun nasıl denetleneceği ve bireysel kullanıcıların ve grupların sağlanmasından nasıl giderileceği açıklanmaktadır.

## <a name="overview"></a>Genel Bakış

Sağlama bağlayıcıları, desteklenen uygulama için [belirtilen belgeleri](../saas-apps/tutorial-list.md) izleyerek [Azure Portal](https://portal.azure.com)kullanılarak ayarlanır ve yapılandırılır. Yapılandırılıp çalıştırıldığında, sağlama işleri iki yöntemden birini kullanarak bildirilebilir:

* **Azure Portal** -Bu makalede öncelikle, bir sağlama Özeti raporu ve belirli bir uygulama için ayrıntılı sağlama denetim günlükleri sağlayan [Azure Portal](https://portal.azure.com)rapor bilgilerinin alınması açıklanmaktadır.
* **Denetim API 'si** -Azure Active Directory ayrıntılı sağlama denetim günlüklerinin programlı bir şekilde alınmasına izin veren bir denetim API 'si de sağlar. Bu API 'yi kullanmaya özgü belgeler için bkz. [Azure Active Directory Denetim API 'si başvurusu](/graph/api/resources/directoryaudit) . Bu makale, API 'yi nasıl kullanacağınızı özellikle kapsamadığından, denetim günlüğüne kaydedilen sağlama olaylarının türlerini ayrıntılandırır.

### <a name="definitions"></a>Tanımlar

Bu makale aşağıda tanımlanan aşağıdaki terimleri kullanır:

* **Kaynak sistem** -Azure AD sağlama hizmeti 'nin eşitlendiği kullanıcı deposu. Azure Active Directory, önceden tümleştirilmiş sağlama bağlayıcılarının çoğunluğunun kaynak sistemidir, ancak bazı özel durumlar vardır (örnek: Workday gelen eşitlemesi).
* **Hedef sistem** -Azure AD sağlama hizmeti 'nin eşitlendiği kullanıcı deposu. Bu genellikle bir SaaS uygulamasıdır (örnekler: Salesforce, ServiceNow, G Suite, Iş için Dropbox), ancak bazı durumlarda Active Directory gibi bir şirket içi sistem olabilir (örnek: Active Directory için Workday gelen eşitlemesi).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Azure portal sağlama raporlarının alınması

Belirli bir uygulama için sağlama raporu bilgilerini almak için, etkinlik bölümündeki [Azure Portal](https://portal.azure.com) ve  &gt; **Kurumsal uygulamalar** &gt; **sağlama günlükleri (Önizleme)**  Azure Active Directory başlatarak başlayın. Ayrıca, sağlama yapılandırılan kurumsal uygulamaya de gidebilirsiniz. Örneğin, kullanıcıları LinkedIn 'e sağlıyorsanız, uygulama ayrıntılarının gezinti yolu şu şekilde olur:

**Azure Active Directory tüm uygulamalar > > kurumsal uygulamalar > LinkedIn yükselt**

Buradan, sağlama ilerleme çubuğuna ve sağlama günlüklerine, aşağıda açıklanan şekilde erişebilirsiniz.

## <a name="provisioning-progress-bar"></a>Sağlama ilerleme çubuğu

[Sağlama ilerleme çubuğu](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) , belirli bir uygulamanın **sağlama** sekmesinde görünür. **Geçerli durum** bölümünde bulunur ve geçerli ilk veya artımlı döngüsünün durumunu gösterir. Bu bölümde ayrıca şunları da gösterilmektedir:

* Eşitlenmiş ve kaynak sistem ile hedef sistem arasında sağlama için kapsamda bulunan ve şu anda kapsamdaki Toplam Kullanıcı ve/grup sayısı.
* Eşitlemenin son çalıştırıldığı zaman. Eşitlemeler genellikle [ilk bir döngüden](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) sonra her 20-40 dakikada bir gerçekleşir.
* Bir [Başlangıç döngüsünün](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) tamamlanıp tamamlanmadığını belirtir.
* Sağlama işleminin karantinaya alınmış olup olmadığı ve karantina durumunun nedenleri (örneğin, geçersiz yönetici kimlik bilgileri nedeniyle hedef sistemle iletişim kurma hatası).

**Geçerli durum** , yöneticinin sağlama işinin işletimsel sistem durumunu kontrol etmek için arayacağı ilk alan olmalıdır.

 ![Özet raporu](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Sağlama günlükleri (Önizleme)

Sağlama hizmeti tarafından gerçekleştirilen tüm etkinlikler Azure AD [sağlama günlüklerine](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)kaydedilir.  &gt; Etkinlik bölümünde **Kurumsal uygulamalar** &gt; **sağlama günlükleri (Önizleme)**  Azure Active Directory ' ni seçerek Azure Portal sağlama günlüklerine erişebilirsiniz. Sağlama verilerini kullanıcı adına veya kaynak sistemde ya da hedef sistemde tanımlayıcı temelinde arayabilirsiniz. Ayrıntılar için bkz. [sağlama günlükleri (Önizleme)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Günlüğe kaydedilen etkinlik olay türleri şunları içerir:

## <a name="troubleshooting"></a>Sorun giderme

Sağlama Özeti raporu ve sağlama günlükleri, yöneticilerin çeşitli kullanıcı hesabı sağlama sorunlarını gidermesine yardımcı olan bir anahtar rol oynar.

Otomatik Kullanıcı sağlama sorunlarını gidermeye yönelik senaryo tabanlı yönergeler için bkz. [kullanıcıları yapılandırma ve uygulama sağlama sorunları](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Ek Kaynaklar

* [Kurumsal Uygulamalar için kullanıcı hesabı hazırlamayı yönetme](configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory ile uygulama erişimi ve çoklu oturum açma özellikleri nelerdir?](../manage-apps/what-is-single-sign-on.md)