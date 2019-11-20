---
title: Linux Vm'leri için DHCPv6'ı yapılandırma
titlesuffix: Azure Load Balancer
description: Bu makalede, Linux VM 'Leri için DHCPv6 yapılandırma hakkında bilgi edinin.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, azure yük dengeleyici, ikili yığın, genel IP, yerel IPv6, mobil veya IOT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 1eea6d71b06bac47dcc4fdca9302ee937e0fd54d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077042"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Linux Vm'leri için DHCPv6'ı yapılandırma


Linux sanal makine görüntüleri Azure Market'te bazıları, dinamik konak Yapılandırma Protokolü sürüm 6 (DHCPv6) varsayılan olarak yapılandırılmış yoktur. IPv6 desteği için kullandığınız Linux işletim sistemi dağıtımlarında DHCPv6 yapılandırılması gerekir. Farklı paketler kullandıkları için çeşitli Linux dağıtımlarını çeşitli şekillerde DHCPv6 yapılandırın.

> [!NOTE]
> Son SUSE Linux ve CoreOS görüntüleri Azure Market'te DHCPv6 ile önceden yapılandırılmış. Bu görüntüleri kullandığınızda, ek değişiklik gerekmez.

Bu belge, Linux sanal makinenizi bir IPv6 adresi alır, böylece DHCPv6 etkinleştirmek açıklar.

> [!WARNING]
> Hatalı ağ yapılandırma dosyalarını düzenleyerek, sanal makinenizde ağ erişimi kaybedebilir. Üretim dışı sistemlere yapılandırma değişiklikleri test etmenizi öneririz. Bu makaledeki yönergeleri Azure Market'teki Linux görüntüleri en son sürümlerinde test edilmiştir. Daha ayrıntılı yönergeler için kendi Linux sürümü için belgelerine bakın.

## <a name="ubuntu"></a>Ubuntu

1. Düzen */etc/dhcp/dhclient6.conf* dosyasını bulun ve aşağıdaki satırı ekleyin:

        timeout 10;

2. Aşağıdaki yapılandırma ile eth0 arabirimi için ağ yapılandırmasını düzenleyin:

   * Üzerinde **Ubuntu 12.04 ve 14.04**, Düzen */etc/network/interfaces.d/eth0.cfg* dosya. 
   * Üzerinde **Ubuntu 16.04**, Düzen */etc/network/interfaces.d/50-cloud-init.cfg* dosya.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. IPv6 adresini Yenile:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Ubuntu 17,10 ile başlayarak, varsayılan ağ yapılandırma mekanizması [Netplan]( https://netplan.io)' dır.  Install/örneklemesi oluşturma sırasında NETPLAN şu konumdaki YAML yapılandırma dosyalarından ağ yapılandırmasını okur:/{lib, vs, Run}/Netplan/*. YAML.

Lütfen yapılandırmanızda her Ethernet arabirimi için bir *dhcp6: true* ifadesini ekleyin.  Örneğin:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Erken önyükleme sırasında, Netplan "ağ Oluşturucu", bir yandan, NETPLAN ile ilgili başvuru bilgileri Için belirtilen ağ Daemon 'a cihaz denetimini el ile almak için yapılandırmayı bir şekilde yazar. https://netplan.io/reference.
 
## <a name="debian"></a>Debian

1. Düzen */etc/dhcp/dhclient6.conf* dosyasını bulun ve aşağıdaki satırı ekleyin:

        timeout 10;

2. Düzen */etc/network/interfaces* dosyasını bulun ve aşağıdaki yapılandırma ekleyin:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. IPv6 adresini Yenile:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS ve Oracle Linux

1. Düzen */etc/sysconfig/network* dosyasını bulun ve aşağıdaki parametreyi ekleyin:

        NETWORKING_IPV6=yes

2. Düzen */etc/sysconfig/network-scripts/ifcfg-eth0* dosyasını bulun ve aşağıdaki iki parametreyi ekleyin:

        IPV6INIT=yes
        DHCPV6C=yes

3. IPv6 adresini Yenile:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 ve openSUSE 13

Azure'da openSUSE görüntülerine ve son SUSE Linux Enterprise Server (SLES) DHCPv6 ile önceden yapılandırılmış olmuştur. Bu görüntüleri kullandığınızda, ek değişiklik gerekmez. Bir daha eski veya özel bir SUSE görüntüyü temel alarak bir VM varsa, aşağıdakileri yapın:

1. Yükleme `dhcp-client` gerekiyorsa, paket:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Düzen */etc/sysconfig/network/ifcfg-eth0* dosyasını bulun ve aşağıdaki parametreyi ekleyin:

        DHCLIENT6_MODE='managed'

3. IPv6 adresini Yenile:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 ve openSUSE artık

Azure'nün son SLES ve openSUSE görüntülerde DHCPv6 ile önceden yapılandırılmış. Bu görüntüleri kullandığınızda, ek değişiklik gerekmez. Bir daha eski veya özel bir SUSE görüntüyü temel alarak bir VM varsa, aşağıdakileri yapın:

1. Düzen */etc/sysconfig/network/ifcfg-eth0* dosyasını bulun ve değiştirin `#BOOTPROTO='dhcp4'` parametre şu değeri taşıyan:

        BOOTPROTO='dhcp'

2. İçin */etc/sysconfig/network/ifcfg-eth0* şu parametreyi ekleyin:

        DHCLIENT6_MODE='managed'

3. IPv6 adresini Yenile:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Azure'da yeni bir CoreOS görüntüleri DHCPv6 ile önceden yapılandırılmış. Bu görüntüleri kullandığınızda, ek değişiklik gerekmez. Bir daha eski veya özel bir CoreOS görüntüyü temel alarak bir VM varsa, aşağıdakileri yapın:

1. Düzen */etc/systemd/network/10_dhcp.network* dosyası:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. IPv6 adresini Yenile:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
