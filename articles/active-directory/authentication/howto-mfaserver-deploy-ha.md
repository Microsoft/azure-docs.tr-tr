---
title: Azure MFA sunucusu için yüksek kullanılabilirlik-Azure Active Directory
description: Yüksek kullanılabilirlik sağlayan yapılandırmalarda birden çok Azure Multi-Factor Authentication Sunucusu örneğini dağıtın.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1853a4784e3098ecbcefa94d5512b4877ac4dc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97584485"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Azure Multi-Factor Authentication Sunucusu yüksek kullanılabilirlik için yapılandırma

Azure Server MFA dağıtımınız ile yüksek kullanılabilirlik elde etmek için birden çok MFA sunucusu dağıtmanız gerekir. Bu bölümde, Azure MFS sunucu dağıtımında yüksek kullanılabilirlik hedeflerine ulaşmak için yük dengeli bir tasarımla ilgili bilgiler sağlanmaktadır.

> [!IMPORTANT]
> 1 Temmuz 2019 itibariyle, Microsoft artık Yeni dağıtımlar için MFA sunucusu sağlamamaktadır. Oturum açma olayları sırasında çok faktörlü kimlik doğrulaması (MFA) gerektirmek isteyen yeni müşteriler, bulut tabanlı Azure AD Multi-Factor Authentication kullanmalıdır.
>
> Bulut tabanlı MFA 'yı kullanmaya başlamak için bkz. [öğretici: Azure AD Multi-Factor Authentication Ile güvenli Kullanıcı oturum açma olayları](tutorial-enable-azure-mfa.md).
>
> MFA sunucusunu 1 Temmuz 2019 tarihinden önce etkinleştiren mevcut müşteriler, en son sürümü, gelecekteki güncelleştirmeleri indirebilir ve her zamanki gibi etkinleştirme kimlik bilgilerini oluşturabilir.

## <a name="mfa-server-overview"></a>MFA sunucusuna genel bakış

Azure MFA sunucu hizmeti mimarisi, aşağıdaki diyagramda gösterildiği gibi birçok bileşeni kapsar:

 ![MFA sunucu mimarisi bileşenleri](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

MFA sunucusu, Azure Multi-Factor Authentication yazılımının yüklü olduğu bir Windows sunucusudur. MFA sunucusu örneğinin Azure 'da MFA hizmeti tarafından çalışması için etkinleştirilmesi gerekir. Şirket içi bir MFA sunucusu yüklenebilir.

Yüklü olan ilk MFA sunucusu, varsayılan olarak Azure MFA hizmeti tarafından etkinleştirilmesinin ardından birincil MFA sunucusudur. Birincil MFA sunucusunda PhoneFactor. pfdata veritabanının yazılabilir bir kopyası vardır. MFA sunucusu örneklerinin sonraki yüklemeleri, astlar olarak bilinir. MFA astlarındaki PhoneFactor. pfdata veritabanının çoğaltılan bir salt okunurdur kopyası vardır. MFA sunucuları, uzak yordam çağrısı (RPC) kullanarak bilgileri çoğaltır. Tüm MFA 'lar, bilgileri çoğaltmak için her zaman etki alanına katılmış veya tek başına olmalıdır.

Hem MFA birincil hem de alt MFA sunucuları, iki öğeli kimlik doğrulaması gerektiğinde MFA hizmeti ile iletişim kurar. Örneğin, bir Kullanıcı iki öğeli kimlik doğrulaması gerektiren bir uygulamaya erişim kazanmak istediğinde, önce Kullanıcı Active Directory (AD) gibi bir kimlik sağlayıcısı tarafından doğrulanır.

AD ile başarılı kimlik doğrulamasından sonra MFA sunucusu MFA hizmetiyle iletişim kurar. MFA sunucusu, MFA hizmetinden uygulamaya Kullanıcı erişimine izin vermek veya erişimi reddetmek için bildirim bekler.

MFA birincil sunucusu çevrimdışı kalırsa, kimlik doğrulamaları yine de işlenebilir, ancak MFA veritabanında değişiklik gerektiren işlemler işlenemiyor. (Örnekler şunlardır: kullanıcıların eklenmesi, self servis PIN değişikliği, Kullanıcı bilgilerini değiştirme veya Kullanıcı portalına erişim)

## <a name="deployment"></a>Dağıtım

Azure MFA sunucusu 'nun ve ilgili bileşenlerinin yük dengelenmesi için aşağıdaki önemli noktaları göz önünde bulundurun.

* **Yüksek kullanılabilirlik elde etmek IÇIN RADIUS standardını kullanma**. Azure MFA sunucularını RADIUS sunucuları olarak kullanıyorsanız, büyük olasılıkla bir MFA sunucusunu birincil RADIUS kimlik doğrulama hedefi ve diğer Azure MFA sunucuları olarak ikincil kimlik doğrulama hedefleri olarak yapılandırabilirsiniz. Ancak, ikincil kimlik doğrulama hedefinde kimlik doğrulamasından geçmeden önce birincil kimlik doğrulama hedefinde kimlik doğrulaması başarısız olduğunda bir zaman aşımı süresinin oluşmasını beklemeniz gerektiğinden, yüksek kullanılabilirlik elde etmek için bu yöntem pratik olmayabilir. RADIUS istemcilerini, işaret ettikleri tek bir URL ile yapılandırabilmeniz için, RADIUS istemcisi ile RADIUS sunucuları arasındaki RADIUS trafiğinin yük dengelenmesi daha etkilidir (Bu durumda, RADIUS sunucuları olarak davranan Azure MFA sunucuları).
* **MFA astlarını el ile yükseltmeniz gerekir**. Birincil Azure MFA sunucusu çevrimdışı kalırsa, ikincil Azure MFA sunucuları MFA isteklerini işlemeye devam eder. Ancak, birincil MFA sunucusu kullanılabilir olana kadar Yöneticiler kullanıcı ekleyemez veya MFA ayarlarını değiştiremezler ve kullanıcılar Kullanıcı portalını kullanarak değişiklik yapamaz. MFA alt öğesini birincil role yükseltmek her zaman el ile gerçekleştirilen bir işlemdir.
* **Bileşenlerin Separability**. Azure MFA sunucusu, aynı Windows Server örneğine veya farklı örneklere yüklenebilen birkaç bileşenden oluşur. Bu bileşenler Kullanıcı Portalı, mobil uygulama Web hizmeti ve ADFS bağdaştırıcısı (aracı) içerir. Bu ayrılabilir, Kullanıcı Portalı ve mobil uygulama Web sunucusunu çevre ağından yayımlamak için Web uygulaması ara sunucusu 'nu kullanmayı mümkün kılar. Bu tür bir yapılandırma, aşağıdaki diyagramda gösterildiği gibi tasarımınızın genel güvenliğine ekler. MFA Kullanıcı Portalı ve mobil uygulama Web sunucusu da HA yük dengeli yapılandırmalara dağıtılabilir.

   ![Çevre ağı olan MFA sunucusu](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **SMS üzerinden (tek yönlü SMS olarak da bilinir) bir kerelik parola (OTP), trafik yük dengeli ise yapışkan oturumların kullanılmasını gerektirir**. Tek yönlü SMS, MFA sunucusunun kullanıcılara OTP içeren bir kısa mesaj göndermesini sağlayan bir kimlik doğrulama seçeneğidir. Kullanıcı MFA sınamasını tamamlamaya yönelik bir istem penceresinde OTP 'yi girer. Azure MFA sunucularının yükünü dengeleyebilirsiniz, ilk kimlik doğrulama isteğine hizmet eden aynı sunucu, kullanıcıdan OTP iletisini alan sunucu olmalıdır; başka bir MFA sunucusu OTP yanıtı alırsa, kimlik doğrulama sınaması başarısız olur. Daha fazla bilgi için bkz. [Azure MFA sunucusuna SMS üzerinden bir kez parola eklendi](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Kullanıcı Portalı ve mobil uygulama Web hizmeti 'Nin yük dengeli dağıtımları, yapışkan oturumlar gerektirir**. MFA Kullanıcı Portalı ve mobil uygulama Web hizmeti 'ni yük dengelebiliyorsanız, her oturumun aynı sunucuda kalması gerekir.

## <a name="high-availability-deployment"></a>Yüksek kullanılabilirlik dağıtımı

Aşağıdaki diyagramda, Azure MFA ve bileşenlerinin, başvuru için ADFS ile birlikte tam bir HA yükü dengelenmiş uygulamasını gösterilmektedir.

 ![Azure MFA sunucusu HA uygulama](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Yukarıdaki diyagramın ilgili numaralandırılmış alanı için aşağıdaki öğeleri göz önünde bulundurun.

1. İki Azure MFA sunucusu (MFA1 ve MFA2) yük dengeli (mfaapp.contoso.com) ve PhoneFactor. pfdata veritabanını çoğaltmak için statik bağlantı noktası (4443) kullanmak üzere yapılandırılmıştır. Web hizmeti SDK 'Sı, ADFS sunucularıyla TCP bağlantı noktası 443 üzerinden iletişim sağlamak için MFA sunucusuna yüklenir. MFA sunucuları durum bilgisiz yük dengeli bir yapılandırmada dağıtılır. Ancak, SMS üzerinden OTP kullanmak isterseniz, durum bilgisi olan yük dengelemeyi kullanmanız gerekir.
   ![Azure MFA sunucusu-App Server HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > RPC dinamik bağlantı noktaları kullandığından, güvenlik duvarlarını RPC 'nin potansiyel olarak kullanabileceği dinamik bağlantı noktası aralığına kadar açmanız önerilmez. MFA uygulama sunucularınız **arasında** bir güvenlik duvarınız varsa, MFA sunucusunu, alt ve birincil sunucular arasındaki çoğaltma trafiği için bir statik bağlantı noktası üzerinde iletişim kuracak şekilde yapılandırmanız ve bu bağlantı noktasını güvenlik duvarınızdan açmanız gerekir. Çağrılan bir DWORD kayıt defteri değeri oluşturarak ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` ```Pfsvc_ncan_ip_tcp_port``` ve değeri kullanılabilir bir statik bağlantı noktasına ayarlayarak statik bağlantı noktasını zorlayabilirsiniz. Bağlantılar her zaman alt MFA sunucuları tarafından birincil olarak başlatılır, statik bağlantı noktası yalnızca birincil üzerinde gereklidir, ancak bir alt öğesini istediğiniz zaman birincil olacak şekilde yükseltebileceğinizden, tüm MFA sunucularında statik bağlantı noktasını ayarlamanız gerekir.

2. İki Kullanıcı Portalı/MFA mobil uygulama sunucusu (MFA-UP-MAS1 ve MFA-MAS2), **durum bilgisi olan** bir yapılandırmada (MFA.contoso.com) yük dengelemesi yapılır. Yapışkan oturumların MFA Kullanıcı Portalı ve mobil App Service yük dengelemesi için bir gereksinim olduğunu hatırlayın.
   ![Azure MFA sunucusu-Kullanıcı Portalı ve mobil App Service HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. ADFS sunucu grubu, çevre ağındaki yük dengeli ADFS proxy 'leri aracılığıyla yük dengelenebilir ve Internet 'e yayımlanır. Her ADFS sunucusu, TCP bağlantı noktası 443 üzerinden tek bir yük dengeli URL (mfaapp.contoso.com) kullanarak Azure MFA sunucularıyla iletişim kurmak için ADFS aracısını kullanır.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure MFA sunucusunu yükleyip yapılandırma](howto-mfaserver-deploy.md)
