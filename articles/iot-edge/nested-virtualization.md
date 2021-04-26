---
title: Windows 'da Linux için Azure IoT Edge iç içe sanallaştırma | Microsoft Docs
description: Windows üzerinde Linux için Azure IoT Edge iç içe sanallaştırmaya nasıl gidebileceğiniz hakkında bilgi edinin.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 4f01362fd9342c1f508f165b34e121a11e8d07e2
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496597"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Windows üzerinde Linux için Azure IoT Edge iç içe sanallaştırma
Windows üzerinde Linux için Azure IoT Edge birlikte iç içe sanallaştırma ile uyumlu iki biçim vardır. Kullanıcılar, yerel bir VM veya Azure VM aracılığıyla dağıtmayı seçebilirler. Bu makale, kullanıcılara, senaryoları için en uygun seçeneği açıklığa kavuşturmak ve yapılandırma gereksinimleriyle ilgili öngörüler sağlar.

> [!NOTE]
>
> İç içe sanallaştırma için tek bir [ağ seçeneğinin](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) etkinleştirildiğinden emin olun. Bunun başarısız olması, yükleme hatalarına neden olur. 

## <a name="deployment-on-local-vm"></a>Yerel VM 'de dağıtım
Bu, Windows üzerinde Linux için Azure IoT Edge barındıran herhangi bir Windows sanal makinesi için temel yaklaşımdır. Bu durumda, dağıtım başlatılmadan önce iç içe sanallaştırmanın etkinleştirilmesi gerekir. Bu senaryoyu yapılandırma hakkında daha fazla bilgi için [, Iç Içe sanallaştırmaya sahip bir sanal makinede Hyper-V '](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) y i çalıştırın.

Windows Server kullanıyorsanız, [Hyper-V rolünü](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)yüklediğinizden emin olun.

## <a name="deployment-on-azure-vms"></a>Azure VM 'lerde dağıtım
Azure VM 'lerinde dağıtmayı seçerseniz, tasarıma göre dış anahtar olmadığını unutmayın. Windows üzerinde Linux için Azure IoT Edge, bir varsayılan anahtar getiren belirli bir betik yürütülene kadar sunucu SKU 'SU çalıştıran bir Azure VM üzerinde de uyumlu değildir. Varsayılan bir anahtarı nasıl getirecek hakkında daha fazla bilgi için, aşağıdaki [Windows Server bölümüne](#windows-server) bakın. 

> [!NOTE]
>
> EFLOW barındırması beklenen tüm Azure VM 'Leri, [iç içe sanallaştırmayı destekleyen](../virtual-machines/acu.md) bir VM olmalıdır


## <a name="limited-use-of-external-switch"></a>Dış anahtar kullanımı sınırlı
VM 'nin dış anahtar aracılığıyla bir IP adresi edinebildiği herhangi bir senaryoda, dağıtım işlevi otomatik olarak dağıtım için iç varsayılan anahtarı kullanır. Bu, Linux VM için Azure IoT Edge yönetiminin yalnızca hedef cihazın kendisinde yürütülebileceğini (yani, WAC PowerShell SSH Uzantısı ile Linux VM için Azure IoT Edge bağlanmak yalnızca localhost üzerinde mümkündür) anlamına gelir.

## <a name="windows-server"></a>Windows Server
Windows Server kullanıcıları için Windows üzerinde Linux için Azure IoT Edge varsayılan anahtarı otomatik olarak desteklemediğine unutmayın.

* Yerel VM sonuçları: EFLOW VM 'nin dış anahtar aracılığıyla bir IP adresi alabildiğini doğrulayın.

* Azure VM için sonuçlar: Azure VM 'lerde dış anahtar bulunmadığından, sunucuda bir iç anahtar ayarlamadan önce EFLOW dağıtmak mümkün değildir.

Sunucu SKU 'Larında varsayılan olarak (sunucu SKU 'sunun bir Azure VM 'de çalışmasına bakılmaksızın) varsayılan anahtar yoktur. Dış anahtarın kullanılabileceği bir Azure sanal makinesine dağıtım yaparken, Windows dağıtımında Linux için Azure IoT Edge önce varsayılan anahtarın el ile ayarlanması ve yapılandırılması gerekir. Dağıtım işlevselemiz, iç anahtar, bir NAT yapılandırması ve bir DHCP sunucusunu yükleme ve yapılandırma için IP yapılandırması gerektirdiğinden bunu ele almaktadır. Genel önizlemede bulunan dağıtım işlevselemiz, bu ayarlarla fiddle, üretken dağıtımlardaki ağ yapılandırmalarına zarar vermek için bu ayarlarla ilgili değildir.

* Varsayılan anahtarı el ile ayarlama hakkında ilgili bilgiler burada bulunabilir: [Azure sanal makinelerinde iç içe sanallaştırmayı etkinleştirme](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* Bu senaryo için bir DHCP sunucusu ayarlamaya ilişkin belgeler şurada bulunabilir: [Windows PowerShell kullanarak DHCP dağıtma](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
