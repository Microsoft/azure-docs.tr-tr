---
title: Azure VM için ayrıntılı SSH sorunlarını giderme | Microsoft Docs
description: Azure sanal makinesine bağlanma sorunları için daha ayrıntılı SSH sorun giderme adımları
keywords: SSH bağlantısı reddedildi, SSH hatası, Azure SSH, SSH bağlantısı başarısız oldu
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 3807f713065d16d4c6743c65f6a770d158ac7191
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058495"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Azure 'da bir Linux VM 'ye bağlanma sorunları için ayrıntılı SSH sorun giderme adımları
SSH istemcisinin VM 'deki SSH hizmetine erişememesinin pek çok nedeni olabilir. Daha [Genel SSH sorun giderme adımlarına](troubleshoot-ssh-connection.md)uyuldıysanız, bağlantı sorununu gidermeye devam etmeniz gerekir. Bu makale, SSH bağlantısının başarısız olduğunu ve nasıl çözümleneceğini belirlemede ayrıntılı sorun giderme adımları boyunca size rehberlik eder.

## <a name="take-preliminary-steps"></a>Ön adımlar al
Aşağıdaki diyagramda ilgili bileşenler gösterilmektedir.

![SSH hizmetinin bileşenlerini gösteren diyagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Aşağıdaki adımlar, hatanın kaynağını yalıtmanıza ve çözümlerin veya geçici çözümlerin sağlanmasına yardımcı olur.

1. Portalda VM 'nin durumunu denetleyin.
   [Azure Portal](https://portal.azure.com) **sanal makineler** > *VM adı*' nı seçin.

   VM 'nin durum bölmesinde **çalışıyor**gösterilmesi gerekir. İşlem, depolama ve ağ kaynaklarına yönelik son etkinlikleri göstermek için aşağı kaydırın.

2. Uç noktaları, IP adresleri, ağ güvenlik grupları ve diğer ayarları incelemek için **ayarları** seçin.

   VM, **uç noktalar** veya **[ağ güvenlik grubu](../../virtual-network/security-overview.md)** içinde görüntüleyebileceğiniz SSH trafiği için tanımlanmış bir uç noktaya sahip olmalıdır. Kaynak Yöneticisi kullanılarak oluşturulan VM 'lerdeki uç noktalar bir ağ güvenlik grubunda depolanır. Kuralların ağ güvenlik grubuna uygulandığını ve alt ağda başvurulduğunu doğrulayın.

Ağ bağlantısını doğrulamak için, yapılandırılmış uç noktaları denetleyin ve VM 'ye HTTP veya başka bir hizmet gibi başka bir protokol aracılığıyla bağlanıp bağlanamadığınıza bakın.

Bu adımları tamamladıktan sonra SSH bağlantısını yeniden deneyin.

## <a name="find-the-source-of-the-issue"></a>Sorunun kaynağını bulun
Bilgisayarınızdaki SSH istemcisi, aşağıdaki alanlardaki sorunlar veya yapılandırma sorunları nedeniyle Azure VM 'deki SSH hizmetine bağlanamamasına neden olabilir:

* [SSH istemci bilgisayarı](#source-1-ssh-client-computer)
* [Kuruluş Edge cihazı](#source-2-organization-edge-device)
* [Bulut hizmeti uç noktası ve erişim denetimi listesi (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Ağ güvenlik grupları](#source-4-network-security-groups)
* [Linux tabanlı Azure VM](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Kaynak 1: SSH istemci bilgisayarı
Bilgisayarınızı hatanın kaynağı olarak ortadan kaldırmak için, diğer şirket içi, Linux tabanlı bir bilgisayara SSH bağlantısı yapabildiğini doğrulayın.

![SSH istemci bilgisayar bileşenlerini vurgulayan diyagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Bağlantı başarısız olursa, bilgisayarınızda aşağıdaki sorunları kontrol edin:

* Gelen veya giden SSH trafiğini engelleyen yerel bir güvenlik duvarı ayarı (TCP 22)
* SSH bağlantılarını engelleyen yerel olarak yüklenen istemci proxy yazılımı
* SSH bağlantılarını engelleyen yerel olarak yüklenen ağ izleme yazılımı
* Trafiği izleyen veya belirli trafik türlerine izin veren/izin vermeyen diğer güvenlik yazılımı türleri

Bu koşullardan biri geçerliyse, yazılımı geçici olarak devre dışı bırakın ve şirket içi bir bilgisayara bir SSH bağlantısı deneyerek bağlantının bilgisayarınızda engellenip engellenmediğini öğrenin. Daha sonra, SSH bağlantılarına izin vermek için yazılım ayarlarını düzeltmek üzere ağ yöneticinizle birlikte çalışın.

Sertifika kimlik doğrulaması kullanıyorsanız, giriş dizininizde. SSH klasörü için bu izinlere sahip olduğunuzu doğrulayın:

* Chmod 700 ~/.SSH
* Chmod 644 ~/.SSH/\*. pub
* Chmod 600 ~/.ssh/id_rsa (veya özel anahtarlarınız içinde depolanan diğer dosyalar)
* Chmod 644 ~/.SSH/known_hosts (SSH aracılığıyla bağladığınız Konakları içerir)

## <a name="source-2-organization-edge-device"></a>Kaynak 2: Kuruluş Edge cihazı
Kuruluşunuzun kenar cihazını hatanın kaynağı olarak ortadan kaldırmak için, doğrudan Internet 'e bağlı bir bilgisayarın Azure sanal makinenize SSH bağlantısı yapıp yapabildiğini doğrulayın. VM 'ye siteden siteye VPN veya Azure ExpressRoute bağlantısı üzerinden erişiyorsanız, kaynak 4 ' e [atlayın: Ağ güvenlik grupları](#nsg).

![Kuruluş Edge cihazını vurgulayan diyagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Doğrudan Internet 'e bağlı bir bilgisayarınız yoksa, kendi kaynak grubunda veya bulut hizmetinde yeni bir Azure VM oluşturun ve bu yeni VM 'yi kullanın. Daha fazla bilgi için bkz. [Azure 'Da Linux çalıştıran bir sanal makine oluşturma](../linux/quick-create-cli.md). Testinizdeki işiniz bittiğinde kaynak grubunu veya VM 'yi ve bulut hizmetini silin.

Doğrudan Internet 'e bağlı bir bilgisayarla SSH bağlantısı oluşturuyorsanız, kuruluşunuzun uç cihazınızı şu şekilde denetleyin:

* Internet ile SSH trafiğini engelleyen dahili bir güvenlik duvarı
* SSH bağlantılarını engelleyen bir ara sunucu
* Uç ağınızdaki cihazlarda çalışan, SSH bağlantılarını önleyen yetkisiz giriş algılama veya ağ izleme yazılımı

Internet ile SSH trafiğine izin vermek için kuruluşunuzun uç cihazlarınızın ayarlarını düzeltmek üzere ağ yöneticinizle birlikte çalışın.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Kaynak 3: Bulut hizmeti uç noktası ve ACL
> [!NOTE]
> Bu kaynak yalnızca klasik dağıtım modeli kullanılarak oluşturulan VM 'Ler için geçerlidir. Kaynak Yöneticisi kullanılarak oluşturulan VM 'ler için, kaynak 4 ' e [atlayın: Ağ güvenlik grupları](#nsg).

Bulut hizmeti uç noktasını ve ACL 'yi hatanın kaynağı olarak ortadan kaldırmak için, aynı sanal ağdaki başka bir Azure sanal makinesinin SSH kullanarak bağlanabildiğini doğrulayın.

![Bulut hizmeti uç noktası ve ACL 'yi vurgulayan diyagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Aynı sanal ağda başka bir VM yoksa kolayca bir tane oluşturabilirsiniz. Daha fazla bilgi için bkz. [CLI kullanarak Azure 'Da LINUX VM oluşturma](../linux/quick-create-cli.md). Testinizdeki işiniz bittiğinde ek VM 'yi silin.

Aynı sanal ağdaki bir VM ile bir SSH bağlantısı oluşturbiliyorsanız, aşağıdaki alanlara bakın:

* **Hedef VM 'deki SSH trafiğine yönelik uç nokta yapılandırması.** Uç noktanın özel TCP bağlantı noktası, sanal makine üzerindeki SSH hizmetinin dinlediği TCP bağlantı noktasıyla eşleşmelidir. (Varsayılan bağlantı noktası 22 ' dir). **Sanal makineler** > *VM adı* > ayarlarıuç > **noktaları**' nı seçerek Azure Portal SSH TCP bağlantı noktası numarasını doğrulayın.
* **Hedef sanal makinedeki SSH trafiği uç noktası için ACL.** ACL, kaynak IP adresine bağlı olarak Internet 'ten izin verilen veya reddedilen gelen trafiği belirtmenize olanak sağlar. Yanlış yapılandırılmış ACL 'Ler, gelen SSH trafiğini uç noktaya engelleyebilir. Proxy 'nizin veya diğer uç sunucunuzun genel IP adreslerinden gelen trafiğe izin verildiğinden emin olmak için ACL 'larınızı denetleyin. Daha fazla bilgi için bkz. [ağ erişim denetim listeleri (ACL 'ler) hakkında](../../virtual-network/virtual-networks-acl.md).

Uç noktayı sorunun bir kaynağı olarak ortadan kaldırmak için, geçerli uç noktayı kaldırın, başka bir uç nokta oluşturun ve SSH adını (genel ve özel bağlantı noktası numarası için TCP bağlantı noktası 22) belirtin. Daha fazla bilgi için bkz. [Azure 'da bir sanal makinede uç noktaları ayarlama](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Kaynak 4: Ağ güvenlik grupları
Ağ güvenlik grupları izin verilen gelen ve giden trafik üzerinde daha ayrıntılı denetim sahibi olmasını sağlar. Bir Azure sanal ağında alt ağları ve bulut hizmetlerini kapsayan kurallar oluşturabilirsiniz. Internet 'ten gelen ve giden SSH trafiğine izin verildiğinden emin olmak için ağ güvenlik grubu kurallarınızı denetleyin.
Daha fazla bilgi için bkz. [ağ güvenlik grupları hakkında](../../virtual-network/security-overview.md).

NSG yapılandırmasını doğrulamak için IP doğrulamasını da kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure ağ izlemeye genel bakış](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Kaynak 5: Linux tabanlı Azure sanal makinesi
Olası sorunların son kaynağı Azure sanal makinesinin kendisidir.

![Linux tabanlı Azure sanal makinesini vurgulayan diyagram](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Daha önce yapmadıysanız, [bir parola Linux tabanlı sanal makine sıfırlama](../linux/reset-password.md)yönergelerini izleyin.

Bilgisayarınızdan yeniden bağlanmayı deneyin. Yine de başarısız olursa, olası bazı sorunlar aşağıda verilmiştir:

* SSH hizmeti hedef sanal makinede çalışmıyor.
* SSH hizmeti TCP bağlantı noktası 22 ' de dinleme yapmıyor. Test etmek için yerel bilgisayarınıza bir Telnet istemcisi yükleyip "Telnet *Cloudservicename*. cloudapp.net 22" öğesini çalıştırın. Bu adım, sanal makinenin SSH uç noktasına gelen ve giden iletişime izin verdiğini belirler.
* Hedef sanal makinedeki yerel güvenlik duvarının gelen veya giden SSH trafiğini engelleyen kuralları vardır.
* Azure sanal makinesinde çalışan yetkisiz giriş algılama veya ağ izleme yazılımı SSH bağlantılarını engellemektedir.

## <a name="additional-resources"></a>Ek kaynaklar
Uygulama erişiminin sorunlarını giderme hakkında daha fazla bilgi için bkz. [Azure sanal makinesinde çalışan bir uygulamaya erişim sorunlarını giderme](../linux/troubleshoot-app-connection.md)
