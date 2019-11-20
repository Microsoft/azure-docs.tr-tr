---
title: Koşullu erişim Azure Active Directory konum koşulu nedir? | Microsoft Belgeleri
description: Bir kullanıcının ağ konumuna bağlı olarak, bulut uygulamalarınıza erişimi denetlemek için konum koşulunu nasıl kullanacağınızı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 799a994e1351f62fac9f5a07060658cea60c9274
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74065743"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Koşullu erişim Azure Active Directory konum koşulu nedir? 

[Azure Active Directory (Azure AD) koşullu erişimi](../active-directory-conditional-access-azure-portal.md)sayesinde, yetkili kullanıcıların bulut uygulamalarınıza nasıl erişebileceğini denetleyebilirsiniz. Koşullu erişim ilkesinin konum koşulu, erişim denetimleri ayarlarını kullanıcılarınızın ağ konumlarına bağlamamanızı sağlar.

Bu makale, konum koşulunu yapılandırmak için ihtiyacınız olan bilgileri sağlar.

## <a name="locations"></a>Konumlar

Azure AD, cihazlar, uygulamalar ve hizmetler için genel İnternet 'te herhangi bir yerden çoklu oturum açma imkanı sağlar. Konum koşulu ile, bir kullanıcının ağ konumuna bağlı olarak bulut uygulamalarınıza erişimi kontrol edebilirsiniz. Konum koşulunun yaygın kullanım örnekleri şunlardır:

- Şirket ağı kapalıyken bir hizmete erişen kullanıcılar için Multi-Factor Authentication gerektirme.
- Belirli ülkelerden veya bölgelerden bir hizmete erişen kullanıcılar için erişimi engelleme.

Konum, adlandırılmış bir konumu veya Multi-Factor Authentication güvenilen IP 'Leri temsil eden bir ağ konumunun etikettir.

## <a name="named-locations"></a>Adlandırılmış konumlar

Adlandırılmış konumlarla, IP adresi aralıkları veya ülkeler ve bölgeler için mantıksal gruplandırmaları oluşturabilirsiniz.

Koşullu erişim sayfasının **Yönet** bölümünde, adlandırılmış konumlarınıza erişebilirsiniz.

![Koşullu erişimde adlandırılmış konumlar](./media/location-condition/02.png)

Adlandırılmış bir konum aşağıdaki bileşenlere sahiptir:

![Yeni bir adlandırılmış konum oluştur](./media/location-condition/42.png)

- **Ad** -adlandırılmış konumun görünen adı.
- **IP aralıkları** -CIDR biçimindeki bir veya daha fazla IPv4 adresi aralığı. IPv6 adres aralığı belirtilmesi desteklenmez.

   > [!NOTE]
   > IPv6 adres aralıkları Şu anda adlandırılmış bir konuma dahil edilemez. Bu, IPv6 aralıklarının koşullu erişim ilkesinden dışlanmadığı anlamına gelir.

- **Güvenilen konum olarak işaretle** -güvenilen bir konum göstermek için adlandırılmış bir konum için ayarlayabileceğiniz bayrak. Genellikle, güvenilen konumlar BT departmanınız tarafından denetlenen ağ alanlarıdır. Koşullu erişime ek olarak, güvenilir adlandırılmış konumlar da Azure kimlik koruması ve Azure AD güvenlik raporları tarafından [Hatalı pozitif](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1)sonuçları azaltmak için kullanılır.
- **Ülkeler/bölgeler** -Bu seçenek, adlandırılmış bir konum tanımlamak için bir veya daha fazla ülke veya bölge seçmenizi sağlar.
- **Bilinmeyen bölgeleri dahil et** -bazı IP adresleri belirli bir ülkeye veya bölgeye eşlenmedi. Bu seçenek, bu IP adreslerinin adlandırılmış konuma dahil edilip edilmeyeceğini seçmenizi sağlar. Adlandırılmış konumu kullanan ilke bilinmeyen konumlara uygulanacaksa bu ayarı kullanın.

Yapılandırabileceğiniz adlandırılmış konumların sayısı, Azure AD 'de ilgili nesnenin boyutuyla sınırlıdır. Aşağıdaki sınırlamalara göre konumları yapılandırabilirsiniz:

- En fazla 1200 IP aralığına sahip bir adlandırılmış konum.
- Her birine bir IP aralığı atanmış en fazla 90 adlandırılmış konum.

Koşullu erişim ilkesi, IPv4 ve IPv6 trafiği için geçerlidir. Şu anda adlandırılmış konumlar IPv6 aralıklarının yapılandırılmasına izin vermez. Bu sınırlama aşağıdaki durumlara neden olur:

- Koşullu erişim ilkesi belirli IPv6 aralıklarına hedeflenemez
- Koşullu erişim ilkesi belirli ıPV6 aralıklarını dışlayamazsınız

Bir ilke "herhangi bir konum" için uygulanmak üzere yapılandırılmışsa, bu, IPv4 ve IPv6 trafiği için de geçerlidir. Belirtilen ülkeler ve bölgeler için yapılandırılmış adlandırılmış konumlar yalnızca IPv4 adreslerini destekler. IPv6 trafiği yalnızca "bilinmeyen alan Ekle" seçeneği belirlenmişse dahil edilir.

## <a name="trusted-ips"></a>Güvenilen IP'ler

Ayrıca, [Multi-Factor Authentication hizmeti ayarlarındaki](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)kuruluşunuzun yerel intranetinizi temsıl eden IP adresi aralıklarını yapılandırabilirsiniz. Bu özellik, en fazla 50 IP adresi aralığı yapılandırmanızı sağlar. IP adresi aralıkları CıDR biçimindedir. Daha fazla bilgi için bkz. [Güvenilen IP 'ler](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Güvenilen IP 'Ler yapılandırdıysanız, konum koşulunun Konum listesinde **MFA güvenilir IP 'leri** olarak gösterilir.

### <a name="skipping-multi-factor-authentication"></a>Multi-Factor Authentication atlanıyor

Multi-Factor Authentication hizmet ayarları sayfasında, intranetteki şirket içi **kullanıcılardan gelen istekler için Multi-Factor Authentication 'ı atla**' yı seçerek kurumsal intranet kullanıcılarını belirleyebilirsiniz. Bu ayar, AD FS tarafından verilen şirket içi ağ talebinin güvenilen ve Kullanıcı tarafından şirket ağı üzerinde olduğunu belirlemek için kullanılması gerektiğini belirtir. Daha fazla bilgi için bkz. [koşullu erişim kullanarak güvenilir IP 'ler özelliğini etkinleştirme](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Bu seçeneği denetledikten sonra, **MFA güvenilir IP 'leri** de dahil olmak üzere bu seçenek belirlenmiş olan her ilke için geçerlidir.

Uzun süreli oturum yaşam süreleri olan mobil ve masaüstü uygulamalarında, koşullu erişim düzenli aralıklarla yeniden değerlendirilecektir. Varsayılan değer bir saattir. Şirket içi ağ talebi yalnızca ilk kimlik doğrulama sırasında verildiğinde, Azure AD 'nin güvenilir IP aralıkları listesi olmayabilir. Bu durumda, kullanıcının hala şirket ağında olup olmadığını belirlemek daha zordur:

1. Kullanıcının IP adresinin güvenilir IP aralıklarından birinde olup olmadığını denetleyin.
2. Kullanıcının IP adresinin ilk üç sekizlinin ilk kimlik doğrulamanın IP adresinin ilk üç sekizlisi ile eşleşip eşleşmediğini denetleyin. Şirket içi ağ talebi ilk yayımlandığında ve Kullanıcı konumu doğrulandığında, IP adresi ilk kimlik doğrulamasıyla karşılaştırılır.

Her iki adım da başarısız olursa, kullanıcının artık güvenilen bir IP 'de olmaması kabul edilir.

## <a name="location-condition-configuration"></a>Konum koşulu yapılandırması

Konum koşulunu yapılandırırken şunları ayırt etme seçeneğiniz vardır:

- Herhangi bir konum
- Tüm güvenilen konumlar
- Seçili konumlar

![Konum koşulu yapılandırması](./media/location-condition/01.png)

### <a name="any-location"></a>Herhangi bir konum

Varsayılan olarak, **herhangi bir konumun** seçilmesi bir ILKENIN tüm IP adreslerine uygulanmasına neden olur, bu da Internet 'teki herhangi bir adres anlamına gelir. Bu ayar, adlandırılmış konum olarak yapılandırdığınız IP adresleriyle sınırlı değildir. **Herhangi bir konum**seçtiğinizde, hala belirli konumları bir ilkeden dışlayabilirsiniz. Örneğin, şirket ağı dışında, kapsamı tüm konumlara ayarlamak için güvenilen konumlar hariç tüm konumlara bir ilke uygulayabilirsiniz.

### <a name="all-trusted-locations"></a>Tüm güvenilen konumlar

Bu seçenek şu şekilde geçerlidir:

- Güvenilen konum olarak işaretlenen tüm konumlar
- **MFA güvenilir IP 'leri** (yapılandırıldıysa)

### <a name="selected-locations"></a>Seçili konumlar

Bu seçenekle, bir veya daha fazla adlandırılmış konum seçebilirsiniz. Bu ayarın uygulanacağı bir ilke için, bir kullanıcının seçili konumlardan herhangi birinden bağlanması gerekir. ' A tıkladığınızda, adlandırılmış ağların listesini gösteren adlandırılmış ağ seçimi denetimini **Seç** ' e tıklayın. Liste ayrıca ağ konumunun güvenilir olarak işaretlenip işaretlenmediğini gösterir. **MFA güvenilir IP 'ler** adlı adlandırılmış konum, Multi-Factor Authentication hizmet ayarı sayfasında yapılandırılabilecek IP ayarlarını eklemek için kullanılır.

## <a name="what-you-should-know"></a>Bilmeniz gerekenler

### <a name="when-is-a-location-evaluated"></a>Bir konum ne zaman değerlendirilir?

Koşullu erişim ilkeleri şu durumlarda değerlendirilir:

- Kullanıcı başlangıçta bir Web uygulaması, mobil veya masaüstü uygulamasında oturum açar.
- Modern kimlik doğrulaması kullanan bir mobil veya masaüstü uygulaması, yeni bir erişim belirteci almak için yenileme belirteci kullanır. Varsayılan olarak, bu denetim bir saattir.

Bu denetim, modern kimlik doğrulaması kullanan mobil ve Masaüstü uygulamaları için, ağ konumunun değiştirilmesi için bir saat içinde konum değişikliği tespit edilebilir. Modern kimlik doğrulaması kullanmayan mobil ve Masaüstü uygulamaları için her bir belirteç isteğine ilke uygulanır. İsteğin sıklığı uygulamaya göre farklılık gösterebilir. Benzer şekilde, Web uygulamaları için ilke ilk oturum açma sırasında uygulanır ve Web uygulamasındaki oturumun kullanım ömrü boyunca iyidir. Uygulamalar arasındaki oturum yaşam sürelerinin farklılığı nedeniyle, ilke değerlendirmesi arasındaki süre de farklılık gösterecektir. Uygulama yeni bir oturum açma belirteci istediğinde, ilke uygulanır.

Varsayılan olarak, Azure AD, saatlik olarak bir belirteç yayınlar. Şirket ağını kapattıktan sonra, bir saat içinde ilke modern kimlik doğrulaması kullanan uygulamalar için zorlanır.

### <a name="user-ip-address"></a>Kullanıcı IP adresi

İlke değerlendirmesinde kullanılan IP adresi, kullanıcının genel IP adresidir. Özel bir ağdaki cihazlar için, bu IP adresi intranetteki Kullanıcı cihazının istemci IP 'si değildir, ağ tarafından genel İnternet 'e bağlanmak için kullanılan adrestir.

> [!WARNING]
> Cihazınızın yalnızca bir IPv6 adresi varsa, konum koşulunu yapılandırmak desteklenmez.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Adlandırılmış konumların toplu karşıya yüklenmesi ve indirilmesi

Adlandırılmış konumları oluştururken veya güncelleştirdiğinizde, toplu güncelleştirmeler için IP aralıklarıyla bir CSV dosyasını karşıya yükleyebilir veya indirebilirsiniz. Karşıya yükleme, listedeki IP aralıklarını, dosyadaki dosyalarla değiştirir. Dosyanın her satırı CıDR biçiminde bir IP adresi aralığı içerir.

### <a name="cloud-proxies-and-vpns"></a>Bulut proxy 'leri ve VPN 'Ler

Bir bulut barındırılan proxy veya VPN çözümü kullandığınızda, bir ilkeyi değerlendirirken Azure AD 'nin kullandığı IP adresi, proxy 'nin IP adresidir. Kullanıcının genel IP adresini içeren X-Iletilmiş-for (XFF) üst bilgisi kullanılmıyor çünkü güvenilen bir kaynaktan geldiğini belirten bir doğrulama olmadığından, bir IP adresi için bir yöntem sunar.

Bir bulut proxy 'si olduğunda, etki alanına katılmış bir cihaz istemek için kullanılan bir ilke veya AD FS ' den gelen Corpnet talebi kullanılabilir.

### <a name="api-support-and-powershell"></a>API desteği ve PowerShell

API ve PowerShell, adlandırılmış konumlar veya koşullu erişim ilkeleri için henüz desteklenmiyor.

## <a name="next-steps"></a>Sonraki adımlar

- Koşullu erişim ilkesini nasıl yapılandıracağınızı öğrenmek isterseniz bkz. [koşullu erişim Azure Active Directory belirli uygulamalar IÇIN MFA gerektirme](app-based-mfa.md).
- Ortamınız için koşullu erişim ilkelerini yapılandırmaya hazırsanız, [Azure Active Directory Koşullu erişim için en iyi yöntemlere](best-practices.md)bakın.
