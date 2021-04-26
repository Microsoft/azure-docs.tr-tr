---
title: Windows sanal masaüstü Windows 10 veya 7-Azure 'a bağlanma
description: Windows masaüstü istemcisini kullanarak Windows sanal masaüstüne bağlanma.
author: Heidilohr
ms.topic: how-to
ms.date: 09/22/2020
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 625662a6b67e7d30e6320fe7831e4fa7793b9c30
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447897"
---
# <a name="connect-with-the-windows-desktop-client"></a>Windows Masaüstü istemcisine bağlanma

Windows Masaüstü istemcisi 'ni kullanarak Windows 10, Windows 10 IoT Enterprise ve Windows 7 ile cihazlarda Windows sanal masaüstü kaynaklarına erişebilirsiniz. 

> [!IMPORTANT]
> Bu, Windows 8 veya Windows 8.1 desteklemez.
> 
> Bu yalnızca Azure Resource Manager nesneleri destekler, Azure Resource Manager olmayan nesneleri desteklemek için bkz. [Windows Masaüstü (klasik) istemcisi Ile bağlanma](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).
> 
> Bu, RemoteApp ve Masaüstü bağlantıları (ÇDC) istemcisini veya Uzak Masaüstü Bağlantısı (MSTSC) istemcisini desteklemez.

## <a name="install-the-windows-desktop-client"></a>Windows masaüstü istemcisini yükler

Windows sürümünüzü temel alarak istemciyi indirin:

- [Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Erişimi belirlemek için yükleme sırasında şunlardan birini seçin:

- **Yalnızca sizin için yüklensin**
- **Bu makinenin tüm kullanıcıları Için yüklensin** (yönetici hakları gerektirir)

Yüklemeyi tamamladıktan sonra istemcisini başlatmak için **Başlat** menüsünü kullanın ve **Uzak Masaüstü** araması yapın.

## <a name="subscribe-to-a-workspace"></a>Çalışma alanına abone olma

Bir çalışma alanına abone olmak için aşağıdakilerden birini seçin:

- Bir iş veya okul hesabı kullanın ve istemcinin sizin için kullanılabilir kaynakları bulmasını sağlayabilirsiniz
- Kaynağın belirli URL 'sini kullanın

Kaynağı abone olduktan sonra başlatmak için **bağlantı merkezine** gidin ve kaynağa çift tıklayın.

> [!TIP]
> **Başlangıç** menüsünden bir kaynak başlatmak Için, çalışma alanı adına sahip klasörü bulabilir veya arama çubuğuna kaynak adını girebilirsiniz.

### <a name="use-a-user-account"></a>Kullanıcı hesabı kullan

1. Ana sayfadan **abone ol** ' u seçin.
1. İstendiğinde kullanıcı hesabınızla oturum açın.

Çalışma alanına göre gruplandırılan kaynaklar **bağlantı merkezinde** görüntülenir.

   > [!NOTE]
   > Windows istemcisi otomatik olarak Windows sanal masaüstü 'Nü (klasik) varsayılan olarak belirler. 
   > 
   > Ancak, istemci ek Azure Resource Manager kaynakları algılarsa, bunları otomatik olarak ekler veya kullanıcıya kullanılabilir olduğunu bildirir.

### <a name="use-a-specific-url"></a>Belirli bir URL kullan

1. Ana sayfadan **URL Ile abone ol** ' u seçin.
1. *Çalışma alanı URL 'sini* veya bir *e-posta adresini* girin:
   - **Çalışma alanı URL 'si** için yöneticinizin sunduğu URL 'yi kullanın.

   |Kullanılabilir kaynaklar|URL|
   |-|-|
   |Windows Sanal Masaüstü (klasik)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Windows Sanal Masaüstü|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Windows sanal masaüstü (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   
   - **E-posta** adresi için e-posta adresinizi kullanın. 
      
   Yönetici e-posta [bulmayı](/windows-server/remote/remote-desktop-services/rds-email-discovery)etkinleştirmişse, istemci, e-postalarınız ile ilişkili URL 'yi bulur.

1. **İleri**’yi seçin.
1. İstendiğinde kullanıcı hesabınızla oturum açın.

Çalışma alanına göre gruplandırılan kaynaklar **bağlantı merkezinde** görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

İstemcisini kullanma hakkında daha fazla bilgi edinmek için, [Windows Masaüstü istemcisi ile çalışmaya başlama](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)konusunu inceleyin.

İstemci özellikleri hakkında daha fazla bilgi edinmek isteyen bir yöneticileriniz varsa, [Yöneticiler Için Windows masaüstü istemcisini](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin)inceleyin.
