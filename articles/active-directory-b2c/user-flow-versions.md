---
title: Azure Active Directory B2C 'daki Kullanıcı akışı sürümleri | Microsoft Docs
description: Azure Active Directory B2C ' de kullanılabilen Kullanıcı akışlarının sürümleri hakkında bilgi edinin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1c05ff1bf1956943230bf523584025787495d57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517863"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 'daki Kullanıcı akışı sürümleri

Azure Active Directory B2C (Azure AD B2C) Kullanıcı akışları, müşteri kimlik deneyimlerini tam olarak tanımlayan ortak [ilkeleri](user-flow-overview.md) ayarlamanıza yardımcı olur. Bu deneyimlere kaydolma, oturum açma, parola sıfırlama veya profil düzenlemesi dahildir. Aşağıdaki tablolarda Azure AD B2C bulunan Kullanıcı akışları açıklanır.

> [!IMPORTANT]
> Kullanıcı akışı sürümlerine başvuru şeklimizi değiştirdik. Daha önce V1 (üretime hazır) sürümleriyle V1.1 ve V2 (önizleme) sürümlerini sunduk. Artık Kullanıcı akışlarını iki sürüme birleştiriyoruz:
>
>- **Önerilen** Kullanıcı akışları, Kullanıcı akışlarının yeni önizleme sürümlerindedir. Kapsamlı olarak test edilmiştir ve eski **v2** ve **v 1.1** sürümlerinin tüm özelliklerini birleştirirler. İleri giderek, önerilen yeni Kullanıcı akışları korunur ve güncelleştirilir. Bu yeni önerilen Kullanıcı akışlarına taşıdıktan sonra, serbest bırakılanlar gibi yeni özelliklere erişebilirsiniz.
>- Daha önce **v1** olarak bilinen **Standart** Kullanıcı akışları, genel olarak kullanılabilir, üretime hazır Kullanıcı akışıdır. Kullanıcı akışlarınız görev açısından kritiktir ve yüksek oranda kararlı sürümlere bağlıysa, standart Kullanıcı akışlarını kullanmaya devam edebilirsiniz ve bu sürümlerin korunmayacağını ve güncelleştirilmediğini da sürdürmez.
>
>Eski tüm önizleme Kullanıcı akışları (V 1.1 ve v2) **1 ağustos 2021 '** e kadar kullanımdan kalkmaya yönelik bir yoldur. Mümkün olan yerlerde, en son özelliklerden ve güncelleştirmelerden her zaman yararlanabilmeniz [için, **Önerilen** yeni sürümlere](#how-to-switch-to-a-new-recommended-user-flow) en kısa sürede geçmeniz önerilir. *Bu değişiklikler yalnızca Azure genel bulutu için geçerlidir. Diğer ortamlar [eski Kullanıcı akış sürümü oluşturmayı](user-flow-versions-legacy.md)kullanmaya devam edecektir.*

## <a name="recommended-user-flows"></a>Önerilen Kullanıcı akışları

Önerilen Kullanıcı akışları, yeni özellikleri eski v2 ve V 1.1 özellikleriyle birleştiren önizleme sürümleridir. İleri giderek, önerilen Kullanıcı akışları korunur ve güncelleştirilir.

| Kullanıcı akışı | Description |
| --------- | ----------- |
| Parola sıfırlama (Önizleme) | Kullanıcının e-postalarını doğruladıktan sonra yeni bir parola seçmesini sağlar. Bu kullanıcı akışını kullanarak şunları yapılandırabilirsiniz: <ul><li>[Çok faktörlü kimlik doğrulaması](multi-factor-authentication.md)</li><li>Belirteç uyumluluk ayarları</li><li>[Yaş sınırı](age-gating.md)</li><li>[parola karmaşıklığı gereksinimleri](password-complexity.md)</li></ul> |
| Profil düzenlemesi (Önizleme) | Kullanıcının kullanıcı özniteliklerini yapılandırmasına olanak sağlar. Bu kullanıcı akışını kullanarak şunları yapılandırabilirsiniz: <ul><li>[Belirteç ömrü](tokens-overview.md)</li><li>Belirteç uyumluluk ayarları</li><li>Oturum davranışı</li></ul> |
| Oturum aç (Önizleme) | Kullanıcının hesabında oturum açmasını sağlar. Bu kullanıcı akışını kullanarak şunları yapılandırabilirsiniz: <ul><li>[Çok faktörlü kimlik doğrulaması](multi-factor-authentication.md)</li><li>[Belirteç ömrü](tokens-overview.md)</li><li>Belirteç uyumluluk ayarları</li><li>Oturum davranışı</li><li>[Yaş sınırı](age-gating.md)</li><li>Oturum açma sayfası özelleştirmesi</li></ul> |
| Kaydolma (Önizleme) | Kullanıcının hesap oluşturmasını sağlar. Bu kullanıcı akışını kullanarak şunları yapılandırabilirsiniz: <ul><li>[Çok faktörlü kimlik doğrulaması](multi-factor-authentication.md)</li><li>[Belirteç ömrü](tokens-overview.md)</li><li>Belirteç uyumluluk ayarları</li><li>Oturum davranışı</li><li>[Yaş sınırı](age-gating.md)</li><li>[Parola karmaşıklığı gereksinimleri](password-complexity.md)</li></ul> |
| Kaydolma ve oturum açma (Önizleme) | Kullanıcının hesabında hesap oluşturmasını veya oturum açmasını sağlar. Bu kullanıcı akışını kullanarak şunları yapılandırabilirsiniz: <ul><li>[Çok faktörlü kimlik doğrulaması](multi-factor-authentication.md)</li><li>[Yaş sınırı](age-gating.md)</li><li>[Parola karmaşıklığı gereksinimleri](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>Standart Kullanıcı akışları

Standart Kullanıcı akışları (daha önce v1 olarak adlandırılır) genel olarak kullanılabilir, üretime hazır Kullanıcı akışıdır. Standart Kullanıcı akışları bundan sonra güncelleştirilmeyecek.

| Kullanıcı akışı | Description |
| --------- | ----------- | ----------- |
| Parola sıfırlama | Kullanıcının e-postalarını doğruladıktan sonra yeni bir parola seçmesini sağlar. Bu kullanıcı akışını kullanarak şunları yapılandırabilirsiniz: <ul><li>[Çok faktörlü kimlik doğrulaması](multi-factor-authentication.md)</li><li>Belirteç uyumluluk ayarları</li><li>[Parola karmaşıklığı gereksinimleri](password-complexity.md)</li></ul> |
| Profil düzenlemesi | Kullanıcının kullanıcı özniteliklerini yapılandırmasına olanak sağlar. Bu kullanıcı akışını kullanarak şunları yapılandırabilirsiniz: <ul><li>[Belirteç ömrü](tokens-overview.md)</li><li>Belirteç uyumluluk ayarları</li><li>Oturum davranışı</li></ul> |
| Oturum açın | Kullanıcının hesabında oturum açmasını sağlar. Bu kullanıcı akışını kullanarak şunları yapılandırabilirsiniz: <ul><li>[Çok faktörlü kimlik doğrulaması](multi-factor-authentication.md)</li><li>[Belirteç ömrü](tokens-overview.md)</li><li>Belirteç uyumluluk ayarları</li><li>Oturum davranışı</li><li>Oturum açmayı engelle</li><li>Parola sıfırlamayı zorla</li><li>Oturumumu Açık tut (KMSı)</ul><br>Kullanıcı arabirimini bu kullanıcı akışıyla özelleştiremezsiniz. |
| Kaydolma | Kullanıcının hesap oluşturmasını sağlar. Bu kullanıcı akışını kullanarak şunları yapılandırabilirsiniz: <ul><li>[Çok faktörlü kimlik doğrulaması](multi-factor-authentication.md)</li><li>[Belirteç ömrü](tokens-overview.md)</li><li>Belirteç uyumluluk ayarları</li><li>Oturum davranışı</li><li>[Parola karmaşıklığı gereksinimleri](password-complexity.md)</li></ul> |
| Kaydolma ve oturum açma | Kullanıcının hesabı oluşturmasını veya hesabında oturum açmasını sağlar. Bu kullanıcı akışını kullanarak şunları yapılandırabilirsiniz: <ul><li>[Çok faktörlü kimlik doğrulaması](multi-factor-authentication.md)</li><li>[Belirteç ömrü](tokens-overview.md)</li><li>Belirteç uyumluluk ayarları</li><li>Oturum davranışı</li><li>[Parola karmaşıklığı gereksinimleri](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>Yeni bir önerilen Kullanıcı akışına geçme

Bir Kullanıcı akışının eski bir sürümünden yeni **Önerilen** önizleme sürümüne geçiş yapmak için şu adımları izleyin:

1. [Öğretici: Azure Active Directory Kullanıcı akışları oluşturma](tutorial-create-user-flows.md)bölümündeki adımları izleyerek yeni bir Kullanıcı akış ilkesi oluşturun. Kullanıcı akışını oluştururken **Önerilen** sürümü seçin.

3. Yeni Kullanıcı akışınızı, eski ilkede yapılandırılan ayarlarla yapılandırın.

4. Uygulama oturum açma URL 'nizi yeni oluşturulan ilkeye güncelleştirin.

5. Kullanıcı akışını test ettikten ve çalıştığını onayladıktan sonra, aşağıdaki adımları izleyerek eski kullanıcı akışını silin:
   1. Azure AD B2C kiracı genel bakış menüsünde **Kullanıcı akışları**' nı seçin.
   2. Silmek istediğiniz kullanıcı akışını bulun.
   3. Son sütunda bağlam menüsünü (**...**) seçip **Sil**' i seçin.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>Hala eski v2 ve V 1.1 Kullanıcı akışları oluşturabilir miyim?

Eski v2 ve V 1.1 sürümlerine göre Yeni Kullanıcı akışları oluşturamazsınız, ancak şu anda kullanmakta olduğunuz eski v2 ve V 1.1 Kullanıcı akışını okumaya, güncelleştirmeye ve silmeye devam edebilirsiniz.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>Eski v2 ve V 1.1 Kullanıcı akışlarını kullanmaya devam etmek için herhangi bir neden var mı?

Yanlış. Yeni **Önerilen** önizleme sürümleri, eski v2 ve v 1.1 sürümleriyle aynı işlevleri içerir. Hiçbir şey kaldırılmadı ve aslında artık ek özellikler içeriyor.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>Eski v2 ve V 1.1 ilkelerinden geçiş yapmam durumunda uygulamamı nasıl etkiler?

Eski bir v2 veya V 1.1 Kullanıcı akışı kullanıyorsanız, uygulamanız bu sürüm oluşturma değişikliğinden etkilenmez. Ancak yeni özelliklere veya ilke değişikliklerine erişim sağlamak için, **Önerilen** yeni sürümlere geçmeniz gerekir.

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>Microsoft, eski v2 veya V 1.1 Kullanıcı akışı ilkenizi desteklemeye devam ediyor mu?

Kullanıcı akışlarının eski v2 ve V 1.1 sürümleri tam olarak desteklenmeye devam edecektir.
