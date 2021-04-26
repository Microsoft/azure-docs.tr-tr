---
title: Avere vFXT Denetim Masası 'na erişin-Azure
description: Avere vFXT 'yi yapılandırmak için vFXT kümesine ve tarayıcı tabanlı avere denetim masasına bağlanma
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 79e7c5db2a2c445ae740a21744a0bdfe0736c01a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342442"
---
# <a name="access-the-vfxt-cluster"></a>vFXT kümesine erişme

Küme ayarlarını ayarlamak ve kümeyi izlemek için avere denetim masasını kullanın. Avere denetim masası, kümeye yönelik tarayıcı tabanlı bir grafik arabirimidir.

VFXT kümesi özel bir sanal ağın içinde bulunduğundan, kümenin yönetim IP adresine ulaşmak için bir SSH tüneli oluşturmanız veya başka bir yöntem kullanmanız gerekir.

İki temel adım vardır:

1. İş istasyonunuz ve özel sanal ağ arasında bağlantı oluşturma
1. Kümenin denetim masasını bir Web tarayıcısına yükleme

> [!NOTE]
> Bu makalede, küme denetleyicisinde veya kümenizin sanal ağı içindeki başka bir VM 'de genel bir IP adresi ayarlamış olduğunuz varsayılmaktadır. Bu makalede, kümeye erişmek için bu VM 'nin bir konak olarak nasıl kullanılacağı açıklanır. Sanal ağ erişimi için bir VPN veya ExpressRoute kullanıyorsanız, [avere Denetim Masası 'Na bağlanma](#connect-to-the-avere-control-panel-in-a-browser)bölümüne atlayın.

Bağlanmadan önce, küme denetleyicisini oluştururken kullandığınız SSH ortak/özel anahtar çiftinin yerel makinenizde yüklü olduğundan emin olun. Yardıma ihtiyacınız varsa [Windows](../virtual-machines/linux/ssh-from-windows.md) veya [Linux](../virtual-machines/linux/mac-create-ssh-keys.md) için SSH anahtarları belgelerini okuyun. Ortak anahtar yerine bir parola kullandıysanız, bu parolayı bağlandığınızda girmeniz istenir.

## <a name="create-an-ssh-tunnel"></a>SSH tüneli oluşturma

Linux tabanlı veya Windows 10 istemci sisteminin komut satırından bir SSH tüneli oluşturabilirsiniz.

Bu formla bir SSH tünel oluşturma komutu kullanın:

SSH-L *local_port*:*cluster_mgmt_ip*: 443 *controller_username* \@ *controller_public_IP*

Bu komut, küme denetleyicisinin IP adresi aracılığıyla kümenin yönetim IP adresine bağlanır.

Örnek:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

Küme oluşturmak için SSH ortak anahtarınızı kullandıysanız ve eşleşen anahtar istemci sisteminde yüklüyse kimlik doğrulaması otomatik olarak yapılır. Bir parola kullandıysanız, sistem bunu girmenizi ister.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Tarayıcıda avere Denetim Masası 'na bağlanma

Bu adım, vFXT kümesindeki yapılandırma yardımcı programına bağlanmak için bir Web tarayıcısı kullanır.

* SSH tüneli bağlantısı için Web tarayıcınızı açın ve adresine gidin `https://127.0.0.1:8443` .

  Tüneli oluştururken küme IP adresine bağlanırsınız, bu nedenle yalnızca, tarayıcıda localhost IP adresini kullanmanız gerekir. 8443 dışında bir yerel bağlantı noktası kullandıysanız, bunun yerine bağlantı noktası numaranızı kullanın.

* Kümeye ulaşmak için bir VPN veya ExpressRoute kullanıyorsanız, tarayıcınızda küme yönetimi IP adresine gidin. Örnek: ``https://203.0.113.51``

Tarayıcınıza bağlı olarak **Gelişmiş** ' e tıklamanız ve sayfaya devam etmek için güvenli olduğunu doğrulamanız gerekebilir.

`admin`Kümeyi oluştururken belirttiğiniz kullanıcı adını ve yönetici parolasını girin.

![Avere oturum açma sayfasının Kullanıcı adı ' admin' ve parola ile doldurulmuş ekran görüntüsü](media/avere-vfxt-gui-login.png)

**Oturum aç** ' a tıklayın veya klavyenizde Enter tuşuna basın.

## <a name="next-steps"></a>Sonraki adımlar

Kümenin denetim masasında oturum açtıktan sonra [desteğini](avere-vfxt-enable-support.md)etkinleştirin.