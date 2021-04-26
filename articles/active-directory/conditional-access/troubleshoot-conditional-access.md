---
title: Koşullu erişim ile oturum açma sorunlarını giderme-Azure Active Directory
description: Bu makalede, koşullu erişim ilkeleriniz beklenmedik sonuçlar elde edildiğinde ne yapmanız gerekenler açıklanmaktadır
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f722977329bd5d79d4d0e410a29c730faf00c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92145092"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Koşullu Erişimle ilgili oturum açma sorunlarını giderme

Bu makaledeki bilgiler, hata iletileri ve Azure AD oturum açma günlüğü kullanılarak koşullu erişimle ilgili beklenmedik oturum açma sonuçlarının giderilmesi için kullanılabilir.

## <a name="select-all-consequences"></a>"Tüm" sonuçları ' nı seçin

Koşullu erişim çerçevesi size harika bir yapılandırma esnekliği sağlar. Ancak harika esneklik, istenmeyen sonuçlara engel olmak için, her yapılandırma ilkesini serbest bırakmadan önce dikkatli bir şekilde incelemeniz gerektiği anlamına da gelir. Bu bağlamda, **tüm kullanıcılar/gruplar/bulut uygulamaları gibi tüm grupları** etkileyen atamalara özel dikkat etmeniz gerekir.

Kuruluşlar aşağıdaki yapılandırmalardan kaçınmalıdır:

**Tüm kullanıcılar için tüm bulut uygulamaları:**

- **Erişimi engelle** -bu yapılandırma, tüm kuruluşunuzu engeller.
- **Cihazın uyumlu olarak Işaretlenmesini gerektir** -cihazlarını henüz kaydetmemiş olan kullanıcılar için, bu ilke Intune portalına erişim dahil tüm erişimi engeller. Kayıtlı bir cihaz olmadan yöneticisiyseniz, bu ilke ilkeyi değiştirmek için Azure portal geri almanızı engeller.
- **Karma Azure AD etki alanına katılmış cihaz gerektir** -Bu ilke engelleme erişimi, karma Azure AD 'ye katılmış bir cihaz yoksa kuruluşunuzdaki tüm kullanıcılar için erişimi engellemeyi de mümkün kılıyor.
- **Uygulama koruma Ilkesi gerektir** -Intune ilkeniz yoksa, bu ilke engelleme erişimi, kuruluşunuzdaki tüm kullanıcılar için erişimi engellemeyi de mümkün değildir. Bir Intune uygulama koruma ilkesine sahip bir istemci uygulaması olmadan yöneticisiyseniz, bu ilke Intune ve Azure gibi portallara geri alma işlemi uygulamanızı engeller.

**Tüm kullanıcılar, tüm bulut uygulamaları ve tüm cihaz platformları için:**

- **Erişimi engelle** -bu yapılandırma, tüm kuruluşunuzu engeller.

## <a name="conditional-access-sign-in-interrupt"></a>Koşullu erişim oturum açma kesmesi

İlk yöntem görüntülenen hata iletisini gözden geçirmesidir. Bir Web tarayıcısı kullanırken oturum açan sorunlar için, hata sayfasında ayrıntılı bilgiler bulunur. Bu bilgiler, yalnızca sorunun ne olduğunu ve bir çözüm önerebileceğini betimedebilir.

![Oturum açma hatasıyla uyumlu cihaz gerekiyor](./media/troubleshoot-conditional-access/image1.png)

Yukarıdaki hatada, ileti uygulamanın yalnızca şirketin mobil cihaz yönetimi ilkesini karşılayan cihazlardan veya istemci uygulamalarından erişilebilir olduğunu belirtir. Bu durumda, uygulama ve cihaz bu ilkeyi karşılamıyor.

## <a name="azure-ad-sign-in-events"></a>Azure AD oturum açma olayları

Oturum açma kesintisini hakkında ayrıntılı bilgi almak için ikinci yöntem, hangi koşullu erişim ilkesi veya ilkelerinin uygulanacağını ve bunun neden olduğunu görmek için Azure AD oturum açma olaylarını gözden geçirmelidir.

İlk hata sayfasında **daha fazla ayrıntı** öğesine tıklanarak sorun hakkında daha fazla bilgi bulabilirsiniz. **Daha fazla ayrıntı** için, kullanıcının Microsoft ile bir destek olayı görmekte veya hata OLUŞTUĞUNDA Azure AD oturum açma olaylarını ararken yararlı olan sorun giderme bilgileri ortaya çıkar.

![Koşullu erişim kesintiye uğramış Web tarayıcı oturum açma bilgileri hakkında daha fazla bilgi.](./media/troubleshoot-conditional-access/image2.png)

Hangi koşullu erişim ilkesinin veya ilkelerinin uygulanacağını ve bunun nedenini nasıl yapabileceğinizi öğrenmek için.

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya küresel okuyucu olarak oturum açın.
1. **Azure Active Directory**  >  **oturum açma** işlemlerini inceleyin.
1. Gözden geçirilecek oturum açma için olayı bulun. Gereksiz bilgileri filtrelemek için filtre ve sütun ekleyin veya kaldırın.
   1. Kapsamı daraltmak için filtre ekleyin:
      1. Araştırmanız için özel bir olaylarınızın olduğu **BAĞıNTı kimliği** .
      1. İlke hatasını ve başarısını görmek için **koşullu erişim** . Filtrenizi yalnızca sonuçları sınırlamaya yönelik hataların kapsamını gösterecek şekilde kapsama.
      1. Belirli kullanıcılarla ilgili bilgileri görmek için **Kullanıcı adı** .
      1. Söz konusu zaman dilimine göre kapsam **tarihi** .

   ![Oturum açma günlüğünde koşullu erişim filtresini seçme](./media/troubleshoot-conditional-access/image3.png)

1. Kullanıcının oturum açma hatasına karşılık gelen oturum açma olayı bulunursa **koşullu erişim** sekmesini seçin. Koşullu erişim sekmesi, oturum açma kesintiye neden olan belirli ilke veya ilkeleri gösterir.
   1. **Sorun giderme ve destek** sekmesindeki bilgiler, bir oturum açma nedeninin, uyumluluk gereksinimlerini karşılamayan bir cihaz gibi neden başarısız olduğu konusunda açık bir neden sağlayabilir.
   1. Daha fazla araştırmak için, **Ilke adına** tıklayarak ilkelerin yapılandırmasında ayrıntıya gidin. **Ilke adına** tıkladığınızda, seçilen ilke için ilke yapılandırma kullanıcı arabirimi gözden geçirme ve düzenlemeyle gösterilir.
   1. Koşullu erişim ilkesi değerlendirmesi için kullanılan **istemci kullanıcı** ve **cihaz ayrıntıları** **temel bilgiler**, **konum**, **cihaz bilgileri**, **kimlik doğrulama ayrıntıları** ve oturum açma olayının **ek ayrıntılar** sekmelerinde da kullanılabilir.

### <a name="policy-details"></a>İlke ayrıntıları

Bir oturum açma olayında ilkenin sağ tarafındaki üç noktayı seçmek ilke ayrıntılarını getirir. Bu, yöneticilere bir ilkenin neden başarıyla uygulandığı hakkında ek bilgiler verir.

   ![Oturum açma olayı koşullu erişim sekmesi](./media/troubleshoot-conditional-access/image5.png)

   ![İlke ayrıntıları (Önizleme)](./media/troubleshoot-conditional-access/policy-details.png)

Sol tarafta, oturum açma sırasında toplanan ayrıntılar sağlanır ve sağ tarafta, bu ayrıntıların uygulanan koşullu erişim ilkelerinin gereksinimlerini karşılayıp karşılamadığını karşılayıp karşılamadığını ayrıntılı olarak bulabilirsiniz. Koşullu erişim ilkeleri yalnızca tüm koşulların karşılanması veya yapılandırılmadığı durumlar için geçerlidir.

Olaydaki bilgiler, oturum açma sonuçlarını anlamak veya istenen sonuçları almak için ilkeyi ayarlamak üzere yeterli değilse, bir destek olayı açılabilir. Bu oturum açma olayının **sorun giderme ve destek** sekmesine gidin ve **Yeni bir destek isteği oluştur**' u seçin.

![Oturum açma olayının sorun giderme ve destek sekmesi](./media/troubleshoot-conditional-access/image6.png)

Olayı gönderirken, olay gönderim ayrıntılarında oturum açma olayından istek KIMLIĞI ve saat ve tarih bilgilerini sağlayın. Bu bilgiler, Microsoft destek 'in ilgilendiğiniz olayı bulmasını sağlar.

### <a name="conditional-access-error-codes"></a>Koşullu erişim hata kodları

| Oturum açma hata kodu | Hata dizesi |
| --- | --- |
| 53000 | Devicenotuyumlu |
| 53001 | Devicenotdomainkatılmış |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | Redakupblockedduetorisk |

## <a name="what-to-do-if-you-are-locked-out-of-the-azure-portal"></a>Azure portal kilitlediyseniz ne yapmanız gerekir?

Bir koşullu erişim ilkesinde yanlış bir ayar nedeniyle Azure portal kilitlendiyse:

- Denetim, kuruluşunuzda henüz engellenmeyen başka yöneticiler var. Azure portal erişimi olan bir yönetici, oturum açmanızı etkileyen ilkeyi devre dışı bırakabilir. 
- Kuruluşunuzdaki yöneticilerin hiçbiri ilkeyi güncelleştirebildiğinden, bir destek isteği gönderebilirsiniz. Microsoft desteği, erişimi engelleyen koşullu erişim ilkelerini gözden geçirebilir ve onayını güncelleştirebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Active Directory portaldaki oturum açma etkinlik raporları](../reports-monitoring/concept-sign-ins.md)
- [What If aracını kullanarak koşullu erişim sorunlarını giderme](troubleshoot-conditional-access-what-if.md)
