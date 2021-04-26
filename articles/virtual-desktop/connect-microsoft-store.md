---
title: Microsoft Store Client-Azure 'a bağlanma
description: Microsoft Store istemcisini kullanarak Windows sanal masaüstüne bağlanma.
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5662b32d8e2c9af457ded62b0302c2c64b31edea
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448024"
---
# <a name="connect-with-the-microsoft-store-client"></a>Microsoft Store istemcisiyle bağlanma

>Uygulama hedefi: Windows 10.

Windows 10 ile cihazlarda Windows sanal masaüstü kaynaklarına erişebilirsiniz.

## <a name="install-the-microsoft-store-client"></a>Microsoft Store istemcisini yükler

Geçerli Kullanıcı için, yönetici hakları gerektirmeyen istemcisini yükleyebilirsiniz. Alternatif olarak, yöneticiniz, cihazdaki tüm kullanıcıların erişebilmesi için istemcisini yükleyebilir ve yapılandırabilir.

Yüklendikten sonra istemci, uzak masaüstü araması yaparak başlangıç menüsünden başlatılabilir.

Başlamak için [Microsoft Store istemcisini indirip yükleyin](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS).

## <a name="subscribe-to-a-workspace"></a>Çalışma alanına abone olma

Bilgisayarınızda erişebileceğiniz yönetilen kaynakların listesini almak için yöneticiniz tarafından sunulan çalışma alanına abone olun.

Bir çalışma alanına abone olmak için:

1. Bağlantı merkezi ekranında **+ Ekle**' ye ve ardından **çalışma alanları**' na dokunun.
2. Yönetici tarafından sunulan çalışma alanı URL 'si alanına çalışma alanı URL 'sini girin. Çalışma alanı URL 'SI ya bir URL ya da bir e-posta adresi olabilir.
   
   - Bir çalışma alanı URL 'SI kullanıyorsanız, yöneticinizin size verdiği URL 'YI kullanın.
   - Windows sanal masaüstünden bağlanıyorsanız, kullanmakta olduğunuz hizmetin hangi sürümüne bağlı olarak aşağıdaki URL 'Lerden birini kullanın:
       - Windows sanal masaüstü (klasik): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` .
       - Windows sanal masaüstü: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` .
  
3. **Abone ol**' a dokunun.
4. İstendiğinde kimlik bilgilerinizi sağlayın.
5. Abone olduktan sonra, çalışma alanlarının bağlantı merkezinde gösterilmesi gerekir.

Çalışma alanları, yöneticiniz tarafından yapılan değişikliklere göre eklenebilir, değiştirilebilir veya kaldırılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Microsoft Store istemcisinin nasıl kullanılacağı hakkında daha fazla bilgi edinmek için [Microsoft Store istemcisini kullanmaya başlama](/windows-server/remote/remote-desktop-services/clients/windows/)konusunu inceleyin.