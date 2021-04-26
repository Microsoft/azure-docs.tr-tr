---
title: Koşullu erişim What If aracı-Azure Active Directory
description: Ortamınızdaki koşullu erişim ilkelerinizin etkisini nasıl anlayabileceğinizi öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 119540738ac2703afd79891d9bf8a4a9ff0c7472
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952771"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Koşullu Erişimde What If aracını kullanarak sorunları giderme

[Koşullu erişim](./overview.md) , yetkili kullanıcıların bulut uygulamalarınıza nasıl erişebileceğini denetlemenize olanak tanıyan Azure Active Directory (Azure AD) özelliğidir. Ortamınızdaki koşullu erişim ilkelerinden ne bekleneceğinizi nasıl anlarsınız? Bu soruyu yanıtlamak için **koşullu erişim What If aracı**' nı kullanabilirsiniz.

Bu makalede, koşullu erişim ilkelerinizi test etmek için bu aracı nasıl kullanabileceğiniz açıklanır.

> [!VIDEO https://www.youtube.com/embed/M_iQVM-3C3E]

## <a name="what-it-is"></a>Nedir?

**Koşullu erişim What If ilkesi aracı** , ortamınızda koşullu erişim ilkelerinizin etkisini anlamanıza olanak tanır. Birden çok oturum açma işlemini el ile gerçekleştirerek, ilkelerinizi test etmek yerine, bu araç kullanıcının benzetimli bir oturum açmasını değerlendirmenizi sağlar. Benzetim, bu oturum açma işleminin ilkeleriniz üzerindeki etkisini tahmin eder ve bir benzetim raporu oluşturur. Rapor yalnızca uygulanan koşullu erişim ilkelerini listelemez, ancak varsa [Klasik ilkeleri](policy-migration.md#classic-policies) de listeler.    

**What If** Aracı, belirli bir kullanıcı için uygulanan ilkeleri hızlıca belirleyebilmek için bir yol sağlar. Bu bilgileri, örneğin bir sorunu gidermeniz gerekiyorsa kullanabilirsiniz.    

## <a name="how-it-works"></a>Nasıl çalışır?

**Koşullu erişim What If aracında**, önce benzetimini yapmak istediğiniz oturum açma senaryosunun ayarlarını yapılandırmanız gerekir. Bu ayarlar şunlardır:

- Test etmek istediğiniz Kullanıcı 
- Kullanıcının erişmeyi deneyeceği bulut uygulamaları
- Yapılandırılan bulut uygulamalarına erişimin gerçekleştirildiği koşullar
     
Bir sonraki adım olarak, ayarlarınızı değerlendiren bir simülasyon çalıştırması başlatabilirsiniz. Yalnızca etkinleştirilen ilkeler bir değerlendirme çalıştırmasının parçasıdır.

Değerlendirme tamamlandığında, araç etkilenen ilkelerin bir raporunu oluşturur. Koşullu erişim bilgileri hakkında daha fazla bilgi toplamak için, [koşullu erişim öngörüleri ve raporlama çalışma kitabı](howto-conditional-access-insights-reporting.md) , yalnızca rapor modundaki ilkeler ve şu anda etkin olan ilkeler hakkında ek ayrıntılar verebilir.

## <a name="running-the-tool"></a>Aracı çalıştırma

**What If** aracını, Azure Portal **[koşullu erişim ilkeleri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** sayfasında bulabilirsiniz.

Aracı başlatmak için, ilke listesinin üstündeki araç çubuğunda **What If**' ye tıklayın.

:::image type="content" source="./media/what-if-tool/01.png" alt-text="Azure portal koşullu erişim Ilkeleri sayfasının ekran görüntüsü. Araç çubuğunda, ne tür öğesi vurgulandığında." border="false":::

Bir değerlendirmeyi çalıştırmadan önce, ayarları yapılandırmanız gerekir.

## <a name="settings"></a>Ayarlar

Bu bölüm, benzetim çalıştırmasının ayarları hakkında bilgi sağlar.

:::image type="content" source="./media/what-if-tool/02.png" alt-text="Azure portal What If sayfasının, bir Kullanıcı, bulut uygulamaları, ı P adresi, bir cihaz platformu, istemci uygulaması ve oturum açma riski içeren ekran görüntüsü." border="false":::

### <a name="user"></a>Kullanıcı

Yalnızca bir kullanıcı seçebilirsiniz. Bu, tek gerekli alandır.

### <a name="cloud-apps"></a>Bulut uygulamaları

Bu ayar için varsayılan değer **tüm bulut** uygulamalardır. Varsayılan ayar, ortamınızdaki kullanılabilir tüm ilkelerin değerlendirmesini gerçekleştirir. Belirli bulut uygulamalarını etkileyen ilkeler için kapsamı daraltabilirsiniz.

### <a name="ip-address"></a>IP Adresi

IP adresi, [konum koşulunu](location-condition.md)taklit eden tek bir IPv4 adresidir. Adres, Kullanıcı tarafından oturum açmak için kullanılan cihazın Internet 'e yönelik adresini temsil eder. Bir cihazın IP adresini, örneğin, [IP adresim](https://whatismyipaddress.com)'e giderek doğrulayabilirsiniz.    

### <a name="device-platforms"></a>Cihaz platformları

Bu ayar [cihaz platformları koşulunu](concept-conditional-access-conditions.md#device-platforms) taklit eder ve **Tüm platformların (desteklenmeyen dahil)** eşdeğerini temsil eder. 

### <a name="client-apps"></a>İstemci uygulamaları

Bu ayar, [istemci uygulamaları koşulunu](concept-conditional-access-conditions.md#client-apps)taklit eder.
Varsayılan olarak, bu ayar **tarayıcı** veya **mobil uygulamalar ve Masaüstü istemcileri** tek tek veya her ikisi de seçili olan tüm ilkelerin değerlendirilmesine neden olur. Ayrıca, **Exchange ActiveSync (EAS)** uygulayan ilkeleri de algılar. Şunları seçerek bu ayarı daraltabilirsiniz:

- En az **tarayıcı** seçili olan tüm ilkeleri değerlendirmek için **tarayıcı** . 
- **Mobil uygulamalar ve Masaüstü istemcileri** , en az **mobil uygulamalara ve Masaüstü istemcilerine** sahip tüm ilkeleri değerlendirmelidir. 

### <a name="sign-in-risk"></a>Oturum açma riski

Bu ayar, [oturum açma risk koşulunu](concept-conditional-access-conditions.md#sign-in-risk)taklit eder.   

## <a name="evaluation"></a>Değerlendirme 

**What If**' ye tıklayarak bir değerlendirme başlatabilirsiniz. Değerlendirme sonucu size şunları içeren bir rapor sağlar: 

:::image type="content" source="./media/what-if-tool/03.png" alt-text="Değerlendirme raporunun ekran görüntüsü. Metin, klasik en az bir ilkenin yapılandırıldığını gösterir. İlkeleri görüntülemek için sekmeler kullanılabilir." border="false":::

- Ortamınızda klasik ilkelerin var olup olmadığı göstergesi
- Kullanıcılarınız için uygulanan ilkeler
- Kullanıcılarınız için uygulanan ilkeler

Seçili bulut uygulamaları için [Klasik ilkeler](policy-migration.md#classic-policies) varsa, size bir gösterge sunulur. Göstergeye tıklayarak klasik ilkeler sayfasına yönlendirilirsiniz. Klasik ilkeler sayfasında, klasik bir ilkeyi geçirebilir veya devre dışı bırakabilirsiniz. Bu sayfayı kapatarak değerlendirme sonucuna dönebilirsiniz.

Seçtiğiniz kullanıcı için uygulanan ilke listesinde, kullanıcılarınızın karşılaması gereken [Denetim](concept-conditional-access-grant.md) ve [oturum denetimleri](concept-conditional-access-session.md) listesini de bulabilirsiniz.

Kullanıcılarınız için uygulanamadıkları ilkeler listesinde, bu ilkelerin neden uygulanamadığına ilişkin nedenleri de bulabilirsiniz. Listelenen her ilke için, nedeni karşılanmadı ilk koşulu temsil eder. Uygulanamayan bir ilke için olası bir neden, daha fazla değerlendirilmediğinden devre dışı bırakılmış bir ilkedir.   

## <a name="next-steps"></a>Sonraki adımlar

- Koşullu erişim ilkesi uygulaması hakkında daha fazla bilgi, [koşullu erişim öngörülerini ve raporlamayı](howto-conditional-access-insights-reporting.md)kullanan ilkeler yalnızca rapor modu kullanılarak bulunabilir.
- Ortamınız için koşullu erişim ilkelerini yapılandırmaya hazırsanız, [koşullu erişim ortak ilkelerine](concept-conditional-access-policy-common.md)bakın.
