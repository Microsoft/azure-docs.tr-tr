---
title: Windows sanal masaüstü (klasik) Windows 10 veya 7-Azure 'a bağlanma
description: Windows masaüstü istemcisini kullanarak Windows sanal masaüstüne (klasik) bağlanma.
author: Heidilohr
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 62686589b03f7187ec473dab8fba602eaf7176f2
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445253"
---
# <a name="connect-with-the-windows-desktop-classic-client"></a>Windows Masaüstü (klasik) istemcisiyle bağlantı

> Uygulama hedefi: Windows 7, Windows 10 ve Windows 10 IoT Enterprise

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../connect-windows-7-10.md)bakın.

Windows Masaüstü istemcisi 'ni kullanarak Windows 7, Windows 10 ve Windows 10 IoT Enterprise ile cihazlarda Windows sanal masaüstü kaynaklarına erişebilirsiniz. İstemci Windows 8 veya Windows 8.1 desteklemez.

>[!NOTE]
>Windows istemcisi otomatik olarak Windows sanal masaüstü 'Nü (klasik) varsayılan olarak belirler. Ancak, istemci kullanıcının Azure Resource Manager kaynaklara sahip olduğunu algılarsa, kaynakları otomatik olarak ekler veya kullanıcılara kullanılabilir olduğunu bildirir.

> [!IMPORTANT]
> Windows sanal masaüstü, RemoteApp ve Masaüstü bağlantıları (ÇDC) istemcisini veya Uzak Masaüstü Bağlantısı (MSTSC) istemcisini desteklemez.

> [!IMPORTANT]
> Windows sanal masaüstü Şu anda Windows Mağazası 'ndan uzak masaüstü istemcisini desteklememektedir.

## <a name="install-the-windows-desktop-client"></a>Windows masaüstü istemcisini yükler

Windows sürümünüzle eşleşen istemciyi seçin:

- [Windows 64-bit](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bit](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Yönetici hakları gerektirmeyen geçerli kullanıcı için istemcisini yükleyebilir veya yöneticiniz, cihazdaki tüm kullanıcıların erişebilmesi için istemciyi yükleyip yapılandırabilir.

Yüklendikten sonra istemci, **Uzak Masaüstü** araması yaparak başlangıç menüsünden başlatılabilir.

## <a name="subscribe-to-a-workspace"></a>Çalışma alanına abone olma

Bir çalışma alanına abone olabileceğiniz iki yol vardır. İstemci, iş veya okul hesabınızdan size sunulan kaynakları bulmayı deneyebilir ya da kaynaklarınızın istemci tarafından bulamadığı durumlar için olduğu durumlarda doğrudan URL 'YI belirtebilirsiniz. Bir çalışma alanına abone olduktan sonra, kaynakları aşağıdaki yöntemlerden birini kullanarak başlatabilirsiniz:

- Bağlantı merkezine gidin ve başlatmak için bir kaynağa çift tıklayın.
- Ayrıca Başlat menüsüne gidebilir ve çalışma alanı adına sahip bir klasörü arayabilir ya da arama çubuğuna kaynak adını girebilirsiniz.

### <a name="subscribe-with-a-user-account"></a>Kullanıcı hesabıyla abone olma

1. İstemcinin ana sayfasından **abone ol**' u seçin.
2. İstendiğinde kullanıcı hesabınızla oturum açın.
3. Kaynaklar bağlantı merkezinde görünür ve çalışma alanına göre gruplandırılır.

### <a name="subscribe-with-a-url"></a>URL ile abone olma

1. İstemcinin ana sayfasında **URL Ile abone ol**' u seçin.
2. Çalışma alanı URL 'sini veya e-posta adresinizi girin:
   - **Çalışma alanı URL 'sini** kullanıyorsanız, yöneticinizin size verdiği bir tane kullanın. Windows sanal masaüstünden kaynaklara erişim için aşağıdaki URL 'Lerden birini kullanabilirsiniz:
     - Windows sanal masaüstü (klasik): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Windows sanal masaüstü: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - Bunun yerine **e-posta** alanını kullanıyorsanız, e-posta adresinizi girin. Bu, yönetici [e-posta bulmayı](/windows-server/remote/remote-desktop-services/rds-email-discovery)ayarlamışsa, istemciye e-posta adresinizle ILIŞKILI bir URL aramasını söyler.
3. **İleri**’yi seçin.
4. İstendiğinde kullanıcı hesabınızla oturum açın.
5. Kaynaklar, bağlantı merkezinde, çalışma alanına göre gruplanmış olarak görünmelidir.

## <a name="next-steps"></a>Sonraki adımlar

Windows Masaüstü istemcisinin nasıl kullanılacağı hakkında daha fazla bilgi edinmek için, [Windows Masaüstü istemcisi ile çalışmaya başlama](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)konusunu inceleyin.
