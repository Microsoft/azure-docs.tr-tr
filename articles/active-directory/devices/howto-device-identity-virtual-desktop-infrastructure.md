---
title: Cihaz kimliği ve masaüstü sanallaştırma-Azure Active Directory
description: VDı ve Azure AD cihaz kimliklerinin birlikte nasıl kullanılabileceğini öğrenin
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a1cba2c4572b2f898f631aefbbf316fae1195ac
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596350"
---
# <a name="device-identity-and-desktop-virtualization"></a>Cihaz kimliği ve Masaüstü Sanallaştırması

Yöneticiler, kuruluşlarında Windows işletim sistemlerini barındıran sanal masaüstü altyapısı (VDı) platformlarını yaygın olarak dağıtır. Yöneticiler VDı 'yi şu şekilde dağıtır:

- Yönetimi kolaylaştırma.
- Konsolidasyon ve merkezileşmeyi kaynakların maliyetlerini azaltın.
- Her yerden, herhangi bir cihazda, her yerden sanal masaüstlerine erişim sağlamak için son kullanıcılara yönelik taşınabilirlik ve özgürlük sunun.

İki ana sanal masaüstü türü vardır:

- Kalıcı
- Kalıcı olmayan

Kalıcı sürümler, her kullanıcı veya bir Kullanıcı havuzu için benzersiz bir masaüstü görüntüsü kullanır. Bu benzersiz masaüstleri, gelecekte kullanılmak üzere özelleştirilebilir ve kaydedilebilir. 

Kalıcı olmayan sürümler, kullanıcıların gerektiğinde erişebileceği bir masaüstü bilgisayar koleksiyonu kullanır. Bu kalıcı olmayan masaüstleri, Kullanıcı oturumu kapattıktan sonra özgün durumlarına döndürülür.

Bu makalede, Microsoft 'un cihaz kimliği ve VDı desteğiyle ilgili yöneticilere yönelik Kılavuzu ele alınacaktır. Cihaz kimliği hakkında daha fazla bilgi için, [cihaz kimliği nedir](overview.md)makalesine bakın.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

VDı ortamınız için Azure AD 'de cihaz kimliklerini yapılandırmadan önce, desteklenen senaryolar hakkında bilgi edinin. Aşağıdaki tabloda hangi sağlama senaryolarının desteklendiği gösterilmektedir. Bu bağlamda sağlama, bir yöneticinin herhangi bir son kullanıcı etkileşimi gerektirmeden cihaz kimliklerini ölçeklendirerek yapılandırabilmesini gerektirir.

| Cihaz kimliği türü | Kimlik altyapısı | Windows cihazları | VDı platformu sürümü | Desteklenen |
| --- | --- | --- | --- | --- |
| Karma Azure AD 'ye katılmış | Federasyon | Windows geçerli * * * ve Windows alt düzey * * * * | Kalıcı | Yes |
|   |   |   | Kalıcı olmayan | Yes |
|   | Yönetilen * * | Windows geçerli ve Windows alt düzeyi | Kalıcı | Yes |
|   |   | Windows alt düzey | Kalıcı olmayan | Yes |
|   |   | Windows geçerli | Kalıcı olmayan | Hayır |
| Azure AD 'ye katılmış | Federasyon | Windows geçerli | Kalıcı | Hayır |
|   |   |   | Kalıcı olmayan | Hayır |
|   | Yönetilen | Windows geçerli | Kalıcı | Hayır |
|   |   |   | Kalıcı olmayan | Hayır |
| Azure AD kayıtlı | Federasyon | Windows geçerli | Kalıcı | Hayır |
|   |   |   | Kalıcı olmayan | Hayır |
|   | Yönetilen | Windows geçerli | Kalıcı | Hayır |
|   |   |   | Kalıcı olmayan | Hayır |

\* bir **Federasyon** kimlik altyapısı ortamı, AD FS veya diğer üçüncü taraf IDP gibi bir kimlik sağlayıcısı ile bir ortamı temsil eder.

\* \* **yönetilen** bir kimlik altyapısı ortamı, ile [](../hybrid/whatis-phs.md) [](../hybrid/how-to-connect-pta.md) [Parola karması eşitleme (PHS) veya geçişli kimlik doğrulaması (PTA) ile dağıtılan kimlik sağlayıcısı olarak Azure AD ile bir ortamı temsil eder Kesintisiz çoklu oturum açma](../hybrid/how-to-connect-sso.md).

\* \* \* Windows **geçerli** cihazları Windows 10, windows Server 2016 ve windows Server 2019 ' i temsil eder.

\* \* \* \* Windows **alt düzey** cihazlar Windows 7, Windows 8.1, windows Server 2008 R2, windows Server 2012 ve windows Server 2012 R2 'yi temsil eder. Windows 7 ile ilgili destek bilgileri için bkz. [Windows 7 Için destek sonlandırılıyor](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Windows Server 2008 R2 ile ilgili destek bilgileri için bkz. [Windows server 2008 Için hazırlanma-destek sonu](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Microsoft 'un Kılavuzu

Yöneticiler, karma Azure AD JOIN 'in nasıl yapılandırılacağını öğrenmek için kimlik altyapısına bağlı olarak aşağıdaki makalelere başvurmalıdır.

- [Federasyon ortamına yönelik karma Azure Active Directory birleştirmesini yapılandırma](hybrid-azuread-join-federated-domains.md)
- [Yönetilen ortam için karma Azure Active Directory birleştirmesini yapılandırma](hybrid-azuread-join-managed-domains.md)

Sistem Hazırlama Aracı 'nı (Sysprep. exe) kullanıyorsanız ve yükleme için Windows 10 1809 öncesi bir görüntü kullanıyorsanız, görüntünün Azure AD 'ye karma Azure AD 'ye katılmış olarak zaten kayıtlı olan bir cihazdan olmadığından emin olun.

Ek VM 'Ler oluşturmak için bir sanal makine (VM) anlık görüntüsüne güvenmek istiyorsanız, anlık görüntünün Azure AD 'ye karma Azure AD katılımı olarak zaten kayıtlı olan bir VM 'den olmadığından emin olun.

Kalıcı olmayan VDı dağıtımı yaparken BT yöneticileri, Azure AD 'de eski cihazları yönetmek için yakın bir uyarı ödemelidir. Microsoft, BT yöneticilerinin aşağıdaki kılavuzu uygulamasını önerir. Bunun yapılmaması, dizininizde kalıcı olmayan VDı platformunuzun kayıtlı olan çok sayıda eski karma Azure AD 'ye katılmış cihaz olmasına neden olur.

- Masaüstünü VDı tabanlı olarak gösteren bilgisayarın görünen adı için bir ön ek oluşturun ve kullanın.
- Aşağıdaki komutları, oturum kapatma betiğinin bir parçası olarak uygulayın. Bu komutlar, cihazı silmek için Azure AD 'ye bir en iyi çaba çağrısı tetikleyecektir.
   - Windows geçerli cihazlar için – dsregcmd. exe/Leave
   - Windows alt düzey cihazlar için – autoworkplace. exe/Leave
- [Eski cihazları yönetmek](manage-stale-devices.md)için işlem tanımlayın ve uygulayın.
   - Kalıcı olmayan hibrit Azure AD 'ye katılmış cihazlarınızı belirleme stratejisinden sonra, dizininizin çok sayıda eski cihaz ile tüketilmemesini sağlamak için bu cihazların temizlenmesi üzerinde daha Agresif olabilirsiniz.
 
## <a name="next-steps"></a>Sonraki adımlar

[Federasyon ortamında karma Azure Active Directory katılmayı yapılandırma](hybrid-azuread-join-federated-domains.md)
