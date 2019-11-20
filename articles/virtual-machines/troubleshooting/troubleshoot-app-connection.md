---
title: Azure 'da VM uygulama erişimi sorunlarını giderme | Microsoft Docs
description: Azure 'daki sanal makinelerde çalışan uygulamalara bağlanma sorunlarını yalıtmak için bu ayrıntılı sorun giderme adımlarını kullanın.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Uygulama başlatılamıyor, program açılmaz, dinleme bağlantı noktası engellendi, program başlatılamıyor, dinleme bağlantı noktası engellendi
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: caf73ffbc18a603ace22acfbd0da490048da698a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058128"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Azure 'daki sanal makinelerde uygulama bağlantı sorunlarını giderme

Azure sanal makinesi (VM) üzerinde çalışan bir uygulamaya başlayaveya bağlanamadığınızda çeşitli nedenler vardır. Nedenler, uygulamanın, beklenen bağlantı noktalarını, dinleme bağlantı noktasını engellediği veya ağ kuralları uygulamaya doğru şekilde geçirmeyen ağ kuralları üzerinde çalışmadığını veya dinlemediğini içerir. Bu makalede, sorunu bulmak ve düzeltmek için bir methodical yaklaşımı açıklanır.

RDP veya SSH kullanarak sanal makinenize bağlanma sorunları yaşıyorsanız, önce aşağıdaki makalelerden birine göz atın:

* [Windows tabanlı bir Azure sanal makinesine yönelik uzak masaüstü bağlantılarında sorun giderme](troubleshoot-rdp-connection.md)
* [Linux tabanlı bir Azure sanal makinesine yönelik Secure Shell (SSH) bağlantılarında sorun giderin](troubleshoot-ssh-connection.md).

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve **Destek Al**' ı seçin.

## <a name="quick-start-troubleshooting-steps"></a>Hızlı başlangıç sorunlarını giderme adımları
Bir uygulamaya bağlanma sorunları yaşıyorsanız, aşağıdaki genel sorun giderme adımlarını deneyin. Her adımdan sonra uygulamanıza yeniden bağlanmayı deneyin:

* Sanal makineyi yeniden başlatma
* Uç nokta/güvenlik duvarı kuralları/ağ güvenlik grubu (NSG) kurallarını yeniden oluşturma
  * [Kaynak Yöneticisi modeli-ağ güvenlik gruplarını yönetme](../../virtual-network/manage-network-security-group.md)
  * [Klasik model-Cloud Services uç noktalarını yönetme](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Farklı bir Azure sanal ağı gibi farklı bir konumdan Bağlan
* Sanal makineyi yeniden dağıtma
  * [Windows VM 'yi yeniden dağıtma](redeploy-to-new-node-windows.md)
  * [Linux VM 'yi yeniden dağıtma](redeploy-to-new-node-linux.md)
* Sanal makineyi yeniden oluşturma

Daha fazla bilgi için bkz. [Endpoint connectivity sorunlarını giderme (RDP/SSH/http, vb. hata)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Ayrıntılı sorun giderme genel bakış
Azure sanal makinesinde çalışan bir uygulamaya erişim sorunlarını gidermek için dört ana alan vardır.

![sorun giderme uygulama başlatılamıyor](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Azure sanal makinesinde çalışan uygulama.
   * Uygulamanın kendisi doğru çalışıyor mu?
2. Azure sanal makinesi.
   * VM 'nin kendisi doğru çalışıyor ve isteklere yanıt veriyor mu?
3. Azure ağ uç noktaları.
   * Klasik dağıtım modelindeki sanal makineler için bulut hizmeti uç noktaları.
   * Kaynak Yöneticisi dağıtım modelindeki sanal makineler için ağ güvenlik grupları ve gelen NAT kuralları.
   * Trafik, kullanıcılardan beklenen bağlantı noktalarında VM/uygulamaya trafik akışı verebilir mi?
4. Internet Edge cihazınız.
   * Güvenlik duvarı kuralları, trafiğin doğru şekilde akmasını önler mi?

Uygulamaya siteden siteye VPN veya ExpressRoute bağlantısı üzerinden erişen istemci bilgisayarlar için, soruna neden olabilecek ana alanlarda uygulama ve Azure sanal makinesi yer alabilir.

Sorunun kaynağını ve düzeltmesini öğrenmek için aşağıdaki adımları izleyin.

## <a name="step-1-access-application-from-target-vm"></a>1\. adım: Hedef VM 'den uygulamaya erişme
Uygulamayı, üzerinde çalıştığı VM 'den uygun istemci programıyla erişmeyi deneyin. Yerel ana bilgisayar adını, yerel IP adresini veya geri döngü adresini (127.0.0.1) kullanın.

![uygulamayı doğrudan VM 'den Başlat](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Örneğin, uygulama bir Web sunucusu ise, VM 'de bir tarayıcı açın ve VM 'de barındırılan bir Web sayfasına erişmeyi deneyin.

Uygulamaya erişebilmenize [2. adım](#step2)' a gidin.

Uygulamaya erişemiyorsanız, aşağıdaki ayarları doğrulayın:

* Uygulama hedef sanal makinede çalışıyor.
* Uygulama, beklenen TCP ve UDP bağlantı noktalarını dinler.

Hem Windows hem de Linux tabanlı sanal makinelerde, etkin dinleme bağlantı noktalarını göstermek için **netstat-a** komutunu kullanın. Uygulamanızın dinlemesi gereken beklenen bağlantı noktaları için çıktıyı inceleyin. Uygulamayı yeniden başlatın veya gerektiğinde beklenen bağlantı noktalarını kullanacak şekilde yapılandırın ve uygulamaya yeniden yerel olarak erişmeyi deneyin.

## <a id="step2"></a>2. Adım: Aynı sanal ağdaki başka bir VM 'den uygulamaya erişme
Farklı bir VM 'den, ancak VM 'nin ana bilgisayar adını veya Azure tarafından atanan ortak, özel veya sağlayıcı IP adresini kullanarak aynı sanal ağdaki uygulamaya erişmeyi deneyin. Klasik dağıtım modeli kullanılarak oluşturulan sanal makineler için, bulut hizmetinin genel IP adresini kullanmayın.

![uygulamayı farklı bir VM 'den Başlat](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Örneğin, uygulama bir Web sunucusu ise, aynı sanal ağdaki farklı bir VM 'deki bir tarayıcıdan bir Web sayfasına erişmeyi deneyin.

Uygulamaya erişebilmenize [3. adım](#step3)' a gidin.

Uygulamaya erişemiyorsanız, aşağıdaki ayarları doğrulayın:

* Hedef VM üzerindeki ana bilgisayar güvenlik duvarı, gelen istek ve giden yanıt trafiğine izin verir.
* Hedef VM üzerinde çalışan yetkisiz giriş algılama veya ağ izleme yazılımı trafiğe izin verir.
* Cloud Services uç noktaları veya ağ güvenlik grupları trafiğe izin verir:
  * [Klasik model-Cloud Services uç noktalarını yönetme](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Kaynak Yöneticisi modeli-ağ güvenlik gruplarını yönetme](../../virtual-network/manage-network-security-group.md)
* VM 'niz ile yük dengeleyici veya güvenlik duvarı gibi sanal ağınız arasındaki yolda çalışan ayrı bir bileşen trafiğe izin verir.

Windows tabanlı bir sanal makinede, Güvenlik Duvarı kurallarının uygulamanızın gelen ve giden trafiğini dışlanmasını isteyip istemediğinizi öğrenmek için Gelişmiş Güvenlik Özellikli Windows Güvenlik Duvarı 'nı kullanın.

## <a id="step3"></a>Adım 3: Uygulamaya sanal ağ dışından erişin
Uygulamaya, uygulamanın çalıştığı VM olarak sanal ağ dışındaki bir bilgisayardan erişmeyi deneyin. Özgün istemci bilgisayarınız olarak farklı bir ağ kullanın.

![uygulamayı sanal ağ dışındaki bir bilgisayardan Başlat](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Örneğin, uygulama bir Web sunucusu ise, sanal ağda olmayan bir bilgisayarda çalışan bir tarayıcıdan Web sayfasına erişmeyi deneyin.

Uygulamaya erişemiyorsanız, aşağıdaki ayarları doğrulayın:

* Klasik dağıtım modeli kullanılarak oluşturulan VM 'Ler için:
  
  * VM için uç nokta yapılandırmasının gelen trafiğe, özellikle protokol (TCP veya UDP) ve genel ve özel bağlantı noktası numaralarına izin vermesini doğrulayın.
  * Uç noktasındaki erişim denetim listelerinin (ACL 'Ler) Internet 'ten gelen trafiği engellemediğini doğrulayın.
  * Daha fazla bilgi için bkz. [sanal makineye uç noktaları ayarlama](../windows/classic/setup-endpoints.md).
* Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulan VM 'Ler için:
  
  * VM için gelen NAT kuralı yapılandırmasının, özellikle protokol (TCP veya UDP) ve genel ve özel bağlantı noktası numaralarını gelen trafiğe izin vermesini doğrulayın.
  * Ağ güvenlik gruplarının gelen istek ve giden yanıt trafiğine izin vermesini doğrulayın.
  * Daha fazla bilgi için bkz. [ağ güvenlik grubu nedir?](../../virtual-network/security-overview.md)

Sanal makine veya uç nokta, yük dengeli bir küme üyesiyse:

* Araştırma protokolünün (TCP veya UDP) ve bağlantı noktası numarasının doğru olduğundan emin olun.
* Araştırma Protokolü ve bağlantı noktası yük dengeli küme protokolünden ve bağlantı noktasından farklıysa:
  * Uygulamanın araştırma protokolünü (TCP veya UDP) ve bağlantı noktası numarasını (hedef VM 'de **netstat – a** kullanın) dinlediğini doğrulayın.
  * Hedef VM 'deki ana bilgisayar güvenlik duvarının gelen araştırma isteğine ve giden araştırma yanıtı trafiğine izin vermesini doğrulayın.

Uygulamaya erişebiliyorsanız, Internet Edge cihazınızın izin vermesini sağlamak için şunları yapın:

* Giden uygulama, istemci bilgisayarınızdan Azure sanal makinesine trafik ister.
* Azure sanal makinesinden gelen uygulama yanıtı trafiği.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>4\. adım uygulamaya erişemiyorsanız, ayarları denetlemek için IP doğrulamasını kullanın. 

Daha fazla bilgi için bkz. [Azure ağ izlemeye genel bakış](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Ek kaynaklar
[Windows tabanlı bir Azure sanal makinesine yönelik uzak masaüstü bağlantılarında sorun giderme](troubleshoot-rdp-connection.md)

[Linux tabanlı bir Azure sanal makinesine yönelik Secure Shell (SSH) bağlantılarında sorun giderme](troubleshoot-ssh-connection.md)


