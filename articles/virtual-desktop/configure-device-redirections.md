---
title: Cihaz yeniden yönlendirmelerini Yapılandırma-Azure
description: Windows sanal masaüstü için cihaz yeniden yönlendirmeleri yapılandırma.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 37ecd06c4e3e71234e8fb1b6bad0cd05482dd31b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727858"
---
# <a name="configure-device-redirections"></a>Cihaz yeniden yönlendirmelerini yapılandırma

Windows sanal masaüstü ortamınız için cihaz yeniden yönlendirmelerini yapılandırmak, Uzak oturumdaki yazıcı, USB cihazları, mikrofonlar ve diğer çevresel cihazları kullanmanıza olanak sağlar. Bazı cihaz yeniden yönlendirmeleri Uzak Masaüstü Protokolü (RDP) özelliklerinde ve grup ilkesi ayarlarında değişiklik yapılmasını gerektirir.

## <a name="supported-device-redirections"></a>Desteklenen cihaz yeniden yönlendirmeleri

Her istemci farklı cihaz yeniden yönlendirmelerini destekler. [İstemcileri,](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) her istemci için desteklenen cihaz yeniden yönlendirmeleri tam listesine göre karşılaştırın.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Bir konak havuzu için RDP özelliklerini özelleştirme

PowerShell veya Azure portal kullanarak bir konak havuzu için RDP özelliklerini özelleştirme hakkında daha fazla bilgi edinmek için, [RDP özelliklerine](customize-rdp-properties.md)göz atın. Desteklenen RDP özelliklerinin tam listesi için bkz. [desteklenen RDP dosyası ayarları](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

## <a name="setup-device-redirections"></a>Cihaz yeniden yönlendirmeleri ayarlama

Cihaz yeniden yönlendirmelerini yapılandırmak için aşağıdaki RDP özelliklerini ve grup ilkesi ayarlarını kullanabilirsiniz.

### <a name="audio-input-microphone-redirection"></a>Ses girişi (mikrofon) yeniden yönlendirme

Ses girişi yeniden yönlendirmeyi yapılandırmak için aşağıdaki RDP özelliğini ayarlayın:

- `audiocapturemode:i:1` Ses girişi yeniden yönlendirmeyi etkinleştirilir.
- `audiocapturemode:i:0` Ses girişi yeniden yönlendirmeyi devre dışı bırakır.

### <a name="audio-output-speaker-redirection"></a>Ses çıkışı (konuşmacı) yeniden yönlendirme

Ses çıkışı yeniden yönlendirmeyi yapılandırmak için aşağıdaki RDP özelliğini ayarlayın:

- `audiomode:i:0` ses çıkışı yeniden yönlendirmeyi etkinleştirilir.
- `audiomode:i:1` veya `audiomode:i:2` ses çıkışı yeniden yönlendirmeyi devre dışı bırakın.

### <a name="camera-redirection"></a>Kamera yeniden yönlendirme

Kamera yeniden yönlendirmeyi yapılandırmak için aşağıdaki RDP özelliğini ayarlayın:

- `camerastoredirect:s:*` Tüm kameraları yeniden yönlendirir.
- `camerastoredirect:s:` Kamera yeniden yönlendirmeyi devre dışı bırakır.

>[!NOTE]
>`camerastoredirect:s:`Özelliği devre dışı olsa bile, yerel kameralar özelliği aracılığıyla yeniden yönlendirilebilir `devicestoredirect:s:` . Kamera yeniden yönlendirme kümesini tamamen devre dışı bırakmak `camerastoredirect:s:` ve `devicestoredirect:s:` herhangi bir kamera içermeyen Tak ve kullan cihazlarının bazı alt kümelerini ayarlamak ya da tanımlamak için.

Ayrıca, gibi KSCATEGORY_VIDEO_CAMERA arabirimlerin noktalı virgülle ayrılmış bir listesini kullanarak belirli kameraları yeniden yönlendirebilirsiniz `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` .

### <a name="clipboard-redirection"></a>Pano yeniden yönlendirme

Pano yeniden yönlendirmeyi yapılandırmak için aşağıdaki RDP özelliğini ayarlayın:

- `redirectclipboard:i:1` Pano yeniden yönlendirmeyi mümkün.
- `redirectclipboard:i:0` Pano yeniden yönlendirmeyi devre dışı bırakır.

### <a name="com-port-redirections"></a>COM bağlantı noktası yönlendirmeleri

COM bağlantı noktası yeniden yönlendirmeyi yapılandırmak için aşağıdaki RDP özelliğini ayarlayın:

- `redirectcomports:i:1` COM bağlantı noktası yeniden yönlendirmeyi etkinleştirilir.
- `redirectcomports:i:0` COM bağlantı noktası yeniden yönlendirmeyi devre dışı bırakır.

### <a name="usb-redirection"></a>USB yönlendirmesi

İlk olarak, USB cihaz yeniden yönlendirmeyi etkinleştirmek için aşağıdaki RDP özelliğini ayarlayın:

- `usbdevicestoredirect:s:*` USB cihaz yeniden yönlendirmeyi etkinleştirilir.
- `usbdevicestoredirect:s:` USB cihaz yeniden yönlendirmeyi devre dışı bırakır.

İkinci olarak, kullanıcının yerel cihazında aşağıdaki grup ilkesi ayarlayın:

-   >   >    >  **Windows bileşenleri**  >  **Uzak Masaüstü Hizmetleri**  >  **Uzak Masaüstü bağlantısı istemci**  >  **RemoteFX USB cihaz yeniden yönlendirmesi** Yönetim Şablonları bilgisayar yapılandırma ilkelerine gidin.
- **Bu bilgisayardan desteklenen diğer REMOTEFX USB CIHAZLARıNıN RDP yönlendirmesine Izin verir**' i seçin.
- **Etkin** seçeneğini belirleyin ve ardından **RemoteFX USB yeniden yönlendirme erişimi hakları kutusunda Yöneticiler ve kullanıcılar** ' ı seçin.
- **Tamam**’ı seçin.

### <a name="plug-and-play-device-redirection"></a>Tak ve Çalıştır cihaz yeniden yönlendirmesi

Tak ve kullan cihaz yeniden yönlendirmeyi yapılandırmak için aşağıdaki RDP özelliğini ayarlayın:

- `devicestoredirect:s:*` Tüm Tak ve kullan cihazlarının yeniden yönlendirilmesine izin vermez.
- `devicestoredirect:s:` Tak ve kullan cihazlarının yeniden yönlendirilmesini devre dışı bırakır.

Ayrıca, gibi noktalı virgülle ayrılmış bir liste kullanarak belirli Tak ve kullan cihazlarını da seçebilirsiniz `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Yerel sürücü yeniden yönlendirmesi

Yerel sürücü yeniden yönlendirmeyi yapılandırmak için aşağıdaki RDP özelliğini ayarlayın:

- `drivestoredirect:s:*` Tüm disk sürücülerinin yeniden yönlendirilmesine izin vermez.
- `drivestoredirect:s:` Yerel sürücü yeniden yönlendirmeyi devre dışı bırakır.

Ayrıca, gibi, noktalı virgülle ayrılmış bir liste kullanarak belirli sürücüleri de seçebilirsiniz `drivestoredirect:s:C:;E:;` .

Web istemcisi dosya aktarımını yapılandırmak için, ayarlayın `drivestoredirect:s:*` .

### <a name="printer-redirection"></a>Yazıcı yeniden yönlendirme

Yazıcı yeniden yönlendirmeyi yapılandırmak için aşağıdaki RDP özelliğini ayarlayın:

- `redirectprinters:i:1` yazıcı yeniden yönlendirmeyi mümkün.
- `redirectprinters:i:0` yazıcı yeniden yönlendirmeyi devre dışı bırakır.

### <a name="smart-card-redirection"></a>Akıllı kart yeniden yönlendirmesi

Akıllı kart yeniden yönlendirmeyi yapılandırmak için aşağıdaki RDP özelliğini ayarlayın:

- `redirectsmartcards:i:1` Akıllı kart yeniden yönlendirmeyi etkinleştirilir.
- `redirectsmartcards:i:0` Akıllı kart yeniden yönlendirmeyi devre dışı bırakır.
