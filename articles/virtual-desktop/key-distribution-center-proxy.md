---
title: Kerberos Anahtar Dağıtım Merkezi proxy Windows sanal masaüstü 'Nü ayarlama-Azure
description: Bir Windows sanal masaüstü konak havuzunu Kerberos Anahtar Dağıtım Merkezi proxy kullanacak şekilde ayarlama.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 9dce264b7f2c88aed11f5b82a61f83cbac6c9697
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785118"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Kerberos Anahtar Dağıtım Merkezi ara sunucusunu yapılandırma (Önizleme)

> [!IMPORTANT]
> Bu özellik şu anda genel önizleme aşamasındadır.
> Bu önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve bunu üretim iş yükleri için kullanmanızı önermiyoruz. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Finansal veya kamu kurumları gibi güvenliğe göre bilinçli müşteriler genellikle smartcards kullanarak oturum açın. Smartcards, çok faktörlü kimlik doğrulaması (MFA) gerektirerek dağıtımları daha güvenli hale getirir. Ancak, bir Windows sanal masaüstü oturumunun RDP bölümünde, smartcards, Kerberos kimlik doğrulaması için bir Active Directory (AD) etki alanı denetleyicisiyle doğrudan bağlantı veya "görüş satırı" gerektirir. Bu doğrudan bağlantı olmadan, kullanıcılar kuruluşun ağında uzak bağlantılardan otomatik olarak oturum açamaz. Bir Windows sanal masaüstü dağıtımında bulunan kullanıcılar bu kimlik doğrulama trafiğini ara ve uzaktan oturum açmak için KDC ara hizmetini kullanabilir. KDC proxy, bir Windows sanal masaüstü oturumunun Uzak Masaüstü Protokolü kimlik doğrulamasına izin verir ve kullanıcının güvenli bir şekilde oturum açmasına olanak tanır. Bu, evden çalışmayı çok daha kolay hale getirir ve belirli olağanüstü durum kurtarma senaryolarının daha sorunsuz çalışmasını sağlar.

Ancak, KDC proxy 'nin kurulması genellikle Windows Server 2016 veya sonraki sürümlerde Windows Server Ağ Geçidi rolünün atanmasını içerir. Windows sanal masaüstü 'nde oturum açmak için Uzak Masaüstü Hizmetleri rolünü nasıl kullanacaksınız? Bunu yanıtlamak için bileşenlere hızlıca göz atalım.

Windows sanal masaüstü hizmeti 'nin kimlik doğrulamasının doğrulanması gereken iki bileşeni vardır:

- Windows sanal masaüstü istemcisindeki, kullanıcılara erişimleri olan kullanılabilir masaüstleri veya uygulamalar listesi veren akış. Bu kimlik doğrulama işlemi Azure Active Directory olur. Bu, bu bileşenin bu makalenin konusu olmadığı anlamına gelir.
- Bir kullanıcının mevcut kaynaklarından birini seçmesini sağlayan RDP oturumu. Bu bileşen Kerberos kimlik doğrulamasını kullanır ve uzak kullanıcılar için bir KDC ara sunucusu gerektirir.

Bu makalede, Azure portal Windows sanal masaüstü istemcisinde akışın nasıl yapılandırılacağı gösterilir. RD Ağ Geçidi rolünü yapılandırmayı öğrenmek istiyorsanız, bkz. [RD Ağ geçidi rolünü dağıtma](/azure/virtual-desktop/rd-gateway-role).

## <a name="requirements"></a>Gereksinimler

Bir Windows sanal masaüstü oturumu konağını bir KDC proxy ile yapılandırmak için aşağıdaki işlemleri yapmanız gerekir:

- Azure portal ve bir Azure yönetici hesabına erişin.
- Uzak istemci makineler Windows 10 veya Windows 7 çalıştırıyor olmalı ve [Windows Masaüstü istemcisinin](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) yüklü olması gerekir. Şu anda Web istemcisi desteklenmiyor.
- Makinenizde zaten yüklü bir KDC ara sunucusu olmalıdır. Bunun nasıl yapılacağını öğrenmek için bkz. [Windows sanal masaüstü için RD Ağ geçidi rolünü ayarlama](rd-gateway-role.md).
- Makinenin işletim sistemi Windows Server 2016 veya sonraki bir sürümü olmalıdır.

Bu gereksinimleri karşıladığınızdan emin olduktan sonra başlamaya hazırsınız demektir.

## <a name="how-to-configure-the-kdc-proxy"></a>KDC proxy 'yi yapılandırma

KDC proxy 'yi yapılandırmak için:

1. Azure Portal’da yönetici olarak oturum açın.

2. Windows sanal masaüstü sayfasına gidin.

3. KDC ara sunucusunu etkinleştirmek istediğiniz konak havuzunu seçin ve ardından **RDP özellikleri**' ni seçin.

    > [!div class="mx-imgBorder"]
    > ![Bir kullanıcının konak havuzlarını seçip örnek ana bilgisayar havuzunun adı ve ardından RDP özellikleri gösteren Azure portal sayfasının ekran görüntüsü.](media/rdp-properties.png)

4. **Gelişmiş** sekmesini seçin ve ardından boşluk olmadan aşağıdaki biçimde bir değer girin:

    
    > kdcproxyname: s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Adım 4 ' te açıklanan değer ile, seçilen Gelişmiş sekmesini gösteren ekran görüntüsü.](media/advanced-tab-selected.png)

5. **Kaydet**’i seçin.

6. Seçili konak havuzu şimdi 4. adımda girdiğiniz kdcproxyname değerini içeren RDP bağlantı dosyalarını vermeye başlamalıdır.

## <a name="next-steps"></a>Sonraki adımlar

KDC proxy 'sinin Uzak Masaüstü Hizmetleri tarafını yönetme ve RD Ağ Geçidi rolünü atama hakkında bilgi edinmek için bkz. [RD Ağ geçidi rolünü dağıtma](rd-gateway-role.md).

KDC ara sunucularınızı ölçeklendirmeyle ilgileniyorsanız, [RD Web ve ağ geçidi Web ön için yüksek kullanılabilirlik Ekle](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)bölümünde KDC proxy için yüksek kullanılabilirlik ayarlamayı öğrenin.
