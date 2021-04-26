---
title: Belirli bir kullanıcının bir uygulamaya ne zaman erişebileceğinizi öğrenin
description: Kritik öneme sahip bir kullanıcının Azure AD ile Kullanıcı sağlaması için yapılandırdığınız bir uygulamaya ne zaman erişebileceğini bulma
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9835ba2b6db2d71d0ff5825f2eb1996133e75537
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530825"
---
# <a name="check-the-status-of-user-provisioning"></a>Kullanıcı hazırlama durumunu denetleme

Azure AD sağlama hizmeti, kaynak sistem ve hedef sisteme karşı bir ilk sağlama döngüsü çalıştırır ve ardından düzenli artımlı Döngülerde. Bir uygulama için sağlama yapılandırdığınızda, sağlama hizmetinin geçerli durumunu denetleyebilir ve bir kullanıcının uygulamaya ne zaman erişebileceğini görebilirsiniz.

## <a name="view-the-provisioning-progress-bar"></a>Sağlama ilerleme çubuğunu görüntüleme

 Bir uygulamanın **sağlama** sayfasında, Azure AD sağlama hizmeti 'nin durumunu görüntüleyebilirsiniz. Sayfanın alt kısmındaki **geçerli durum** bölümü, sağlama döngüsünün Kullanıcı hesapları sağlamaya başlayıp başlamadığına ilişkin gösterir. Döngüsünün ilerlemesini izleyebilir, kaç Kullanıcı ve grup sağlandığını görebilir ve kaç rolün oluşturulduğunu görebilirsiniz.

Otomatik sağlamayı ilk yapılandırdığınızda, sayfanın en altındaki **geçerli durum** bölümü ilk sağlama döngüsünün durumunu gösterir. Bu bölüm, her bir artımlı döngüsünün çalıştırıldığı her seferinde güncellenir. Aşağıdaki ayrıntılar gösterilir:
- Şu anda çalışmakta olan veya en son tamamlanan sağlama döngüsünün (ilk veya artımlı) türü.
- Tamamlanan sağlama döngüsünün yüzdesini gösteren bir **ilerleme çubuğu** . Yüzde değeri, sağlanan sayfa sayısını yansıtır. Her sayfanın birden çok kullanıcı veya grup içerebileceğini unutmayın. bu nedenle yüzde, izin sağlanan Kullanıcı, Grup veya rol sayısıyla doğrudan bağıntılı değildir.
- Görünümü güncelleştirilmesini sağlamak için kullanabileceğiniz bir **yenileme** düğmesi.
- Bağlayıcı veri deposundaki **Kullanıcı** ve **grupların** sayısı. Sayma, sağlama kapsamına her bir nesne eklendiğinde artar. Kullanıcı geçici olarak silinmiş veya kalıcı olarak siliniyorsa, bu, nesneyi bağlayıcı veri deposundan kaldırmadığından, sayı aşağı gitmeyecektir. CD [sıfırlandıktan](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) sonra sayı ilk eşitleme yeniden hesaplanacak 
- Bireysel kullanıcıların sağlama durumu da dahil olmak üzere, Kullanıcı sağlama hizmeti tarafından çalıştırılan tüm işlemler hakkındaki ayrıntılar için Azure AD sağlama günlüklerini **görüntüleme** (aşağıdaki [sağlama günlüklerini kullanma](#use-provisioning-logs-to-check-a-users-provisioning-status) bölümüne bakın).

Bir sağlama süresi tamamlandıktan sonra, tarihe göre **İstatistikler** bölümü, son döngüsünün tamamlanma tarihi ve süresiyle birlikte tarih olarak sağlanan Toplam Kullanıcı ve grup sayısını gösterir. **Etkınlık kimliği** en son sağlama döngüsünü benzersiz şekilde tanımlar. **Iş kimliği** , sağlama işi için benzersiz bir tanımlayıcıdır ve kiracınızdaki uygulamaya özeldir.

Sağlama ilerlemesi, Azure portal **Azure Active Directory &gt; Enterprise Apps &gt; \[ uygulama adı \] &gt; sağlama** sekmesinde görüntülenebilir.

![Sağlama sayfası ilerleme çubuğu](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Bir kullanıcının sağlama durumunu denetlemek için sağlama günlüklerini kullanma

Seçilen bir kullanıcının sağlama durumunu görmek için Azure AD 'de [sağlama günlüklerine (Önizleme)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) bakın. Kullanıcı sağlama hizmeti tarafından çalıştırılan tüm işlemler, Azure AD sağlama günlüklerine kaydedilir. Bu, kaynak ve hedef sistemlere yapılan tüm okuma ve yazma işlemlerini ve her işlem sırasında okunan veya yazılan kullanıcı verilerini içerir.

 &gt; Etkinlik bölümünde **Kurumsal uygulamalar** &gt; **sağlama günlükleri (Önizleme)**  Azure Active Directory ' ni seçerek Azure Portal sağlama günlüklerine erişebilirsiniz. Sağlama verilerini kullanıcı adına veya kaynak sistemde ya da hedef sistemde tanımlayıcı temelinde arayabilirsiniz. Ayrıntılar için bkz. [sağlama günlükleri (Önizleme)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

Sağlama günlükleri aşağıdakiler de dahil olmak üzere, sağlama hizmeti tarafından gerçekleştirilen tüm işlemleri kaydeder:

* Sağlama kapsamında olan atanmış kullanıcılar için Azure AD sorgulama
* Hedef uygulama, bu kullanıcıların varlığı için sorgulanıyor
* Sistem arasındaki kullanıcı nesnelerini karşılaştırma
* Karşılaştırma temelinde hedef sistemde Kullanıcı hesabını ekleme, güncelleştirme veya devre dışı bırakma

Azure portal sağlama günlüklerinin nasıl okunmasıyla ilgili daha fazla bilgi için bkz. [sağlama Raporlama Kılavuzu](check-status-user-account-provisioning.md).

## <a name="how-long-will-it-take-to-provision-users"></a>Kullanıcıları sağlamak için ne kadar sürer?
Azure AD, bir uygulamayla otomatik Kullanıcı sağlamayı kullanırken, düzenli olarak zamanlanan bir zaman aralığında, genellikle her 40 dakikada bir [Kullanıcı ve grup ataması](../manage-apps/assign-user-or-group-access-portal.md) gibi öğelere dayalı olarak bir uygulamadaki Kullanıcı hesaplarını otomatik olarak sağlar ve güncelleştirir.

Belirli bir kullanıcının sağlanması için gereken süre, genellikle sağlama işinizin bir başlangıç döngüsünü mi yoksa artımlı bir döngüyü mi çalıştırdığına bağlıdır.

- **İlk döngüde** iş süresi, sağlama için kapsamdaki Kullanıcı ve grup sayısı ve kaynak sistemdeki Toplam Kullanıcı ve grup sayısı dahil olmak üzere birçok faktöre bağlıdır. Azure AD ve uygulama arasındaki ilk eşitleme, Azure AD dizininin boyutuna ve sağlama kapsamındaki kullanıcı sayısına bağlı olarak 20 dakikadan birkaç saate kadar sürebilir. İlk geçiş performansını etkileyen faktörlerin kapsamlı bir listesi bu bölümün ilerleyen kısımlarında özetlenir.

- İlk döngüden sonra **artımlı döngüler** için, iş süreleri daha hızlı (ör. 10 dakika içinde) olur, çünkü sağlama hizmeti ilk döngüden sonra her iki sistemin durumunu temsil eden filigranları depolar, sonraki eşitlemeler performansını geliştirir. İş saati, bu sağlama döngüsündeki algılanan değişiklik sayısına bağlıdır. 5.000 'den az Kullanıcı veya grup üyeliği değişikliği varsa, iş tek bir artımlı sağlama döngüsünün içinde bitebilirler. 

Aşağıdaki tabloda, yaygın sağlama senaryolarının eşitleme süreleri özetlenmektedir. Bu senaryolarda, kaynak sistem Azure AD ve hedef sistem bir SaaS uygulamasıdır. Eşitleme süreleri, SaaS uygulamaları ServiceNow, Workplace, Salesforce ve G Suite için eşitleme işlerinin istatistiksel analizinden türetilir.


| Kapsam yapılandırması | Kapsam içindeki kullanıcılar, gruplar ve Üyeler | İlk zaman çevrimi süresi | Artımlı zaman çevrimi süresi |
| -------- | -------- | -------- | -------- |
| Yalnızca atanan kullanıcıları ve grupları Eşitle |  < 1.000 |  < 30 dakika | < 30 dakika |
| Yalnızca atanan kullanıcıları ve grupları Eşitle |  1.000-10.000 | 142-708 dakika | < 30 dakika |
| Yalnızca atanan kullanıcıları ve grupları Eşitle |   10.000-100.000 | 1.170-2.340 dakika | < 30 dakika |
| Azure AD 'de tüm kullanıcıları ve grupları eşitleme |  < 1.000 | < 30 dakika  | < 30 dakika |
| Azure AD 'de tüm kullanıcıları ve grupları eşitleme |  1.000-10.000 | < 30-120 dakika | < 30 dakika |
| Azure AD 'de tüm kullanıcıları ve grupları eşitleme |  10.000-100.000  | 713-1.425 dakika | < 30 dakika |
| Azure AD 'de tüm kullanıcıları eşitleme|  < 1.000  | < 30 dakika | < 30 dakika |
| Azure AD 'de tüm kullanıcıları eşitleme | 1.000-10.000  | 43-86 dakika | < 30 dakika |

**Yalnızca yapılandırma eşitlemesi atanan kullanıcı ve gruplar** için aşağıdaki formülleri kullanarak yaklaşık en düşük ve en fazla beklenen **ilk zaman döngüsünü** belirleyebilirsiniz:

- En az dakika = 0,01 x [atanan kullanıcı, Grup ve grup üyesi sayısı]
- En fazla dakika = 0,08 x [atanan kullanıcı, Grup ve grup üyesi sayısı]

Bir **Başlangıç döngüsünün** tamamlanışında geçen süreyi etkileyen faktörlerin Özeti:

- Sağlama için kapsamdaki Toplam Kullanıcı ve grup sayısı.

- Kaynak sistemde (Azure AD) bulunan kullanıcıların, grupların ve grup üyelerinin toplam sayısı.

- Sağlama kapsamındaki kullanıcıların hedef uygulamadaki mevcut kullanıcılarla eşleştirilip eşleştirilmediğini veya ilk kez oluşturulması gerektiğini belirtir. Tüm kullanıcılar için ilk kez oluşturulan eşitleme işleri, tüm kullanıcıların mevcut kullanıcılarla eşleştiği eşitleme işleri kadar *iki kez* sürer.

- [Sağlama günlüklerindeki](check-status-user-account-provisioning.md)hataların sayısı. Çok sayıda hata varsa ve sağlama hizmeti bir karantina durumuna gittiğinden performans daha yavaştır. 

- Hedef sistem tarafından uygulanan istek hızı sınırları ve azaltma. Bazı hedef sistemler, büyük eşitleme işlemleri sırasında performansı etkileyebilecek istek hızı sınırlarını ve azaltmasını uygular. Bu koşullarda çok fazla sayıda istek alan bir uygulama, yanıt oranını yavaşlatabilir veya bağlantıyı kapatabilir. Performansı artırmak için bağlayıcının uygulama isteklerini, uygulamanın işleyebileceğinden daha hızlı göndermediğinden ayarlanması gerekir. Microsoft tarafından oluşturulan sağlama bağlayıcıları bu ayarlamayı yapar. 

- Atanan grupların sayısı ve boyutları. Atanan grupların eşitlenmesi, kullanıcıları eşitlemeye göre daha uzun sürer. Atanan grupların sayısı ve boyutları, performansı etkiler. Bir uygulamada, [Grup nesnesi eşitlemesi için etkin eşlemeler](customize-application-attributes.md#editing-group-attribute-mappings)varsa, grup adları ve üyelikleri gibi Grup Özellikleri, kullanıcılara ek olarak eşitlenir. Bu ek eşitlemeler, yalnızca kullanıcı nesnelerinden eşitlemeden daha uzun sürer.

- Performans bir sorun haline gelirse ve kiracınızdaki Kullanıcı ve grupların çoğunu sağlamaya çalışıyorsanız, kapsam filtrelerini kullanın. Kapsam filtreleri, kullanıcıları belirli öznitelik değerlerine göre filtreleyerek, sağlama hizmetinin Azure AD 'den ayıklayan verileri ayarlamanıza olanak sağlar. Kapsam filtreleri hakkında daha fazla bilgi için bkz. [kapsam filtreleriyle öznitelik tabanlı uygulama sağlama](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Sonraki adımlar
[Azure Active Directory ile SaaS uygulamalarına kullanıcı hazırlama ve sağlamayı kaldırma işlemlerini otomatik hale getirme](user-provisioning.md)