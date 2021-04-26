---
title: VM ağ aktarım hızını iyileştirme | Microsoft Docs
description: Ubuntu, CentOS ve Red hat gibi başlıca dağıtımlar dahil olmak üzere Microsoft Azure Windows ve Linux VM 'Leri için ağ aktarım hızını iyileştirin.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: bb9235f4d1190bf7f71ddc007f09c9666c353234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98216810"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Azure sanal makineleri için ağ aktarım hızını iyileştirme

Azure sanal makineleri (VM), ağ işleme için daha iyi iyileştirilen varsayılan ağ ayarlarına sahiptir. Bu makalede Ubuntu, CentOS ve Red hat gibi başlıca dağıtımlar dahil olmak üzere Microsoft Azure Windows ve Linux VM 'Leri için ağ aktarım hızını en iyi duruma getirmeyi açıklamaktadır.

## <a name="windows-vm"></a>Windows VM

Windows VM 'niz [hızlandırılmış ağı](create-vm-accelerated-networking-powershell.md)destekliyorsa, bu özelliği etkinleştirmek üretilen iş için en uygun yapılandırma olacaktır. Diğer tüm Windows VM 'Leri için, Alma Tarafı Ölçeklendirmesi (RSS) kullanımı, RSS olmayan bir VM 'den daha yüksek olan en fazla verimlilik elde edebilir. RSS, bir Windows VM 'de varsayılan olarak devre dışı bırakılabilir. RSS 'nin etkinleştirilip etkinleştirilmediğini ve şu anda devre dışı bırakılmışsa bu seçeneği etkinleştirip etkinleştirmediğini anlamak için aşağıdaki adımları izleyin:

1. PowerShell komutuyla bir ağ bağdaştırıcısı için RSS 'nin etkinleştirilip etkinleştirilmediğini inceleyin `Get-NetAdapterRss` . ' Dan döndürülen aşağıdaki örnek çıktıda `Get-NetAdapterRss` RSS etkin değildir.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. RSS 'yi etkinleştirmek için aşağıdaki komutu girin:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Önceki komutun çıktısı yok. Komut, NIC ayarlarını değiştirdi ve yaklaşık bir dakika boyunca geçici bağlantı kaybına neden olur. Bağlantı kaybı sırasında yeniden bağlama iletişim kutusu görüntülenir. Bağlantı genellikle üçüncü girişimden sonra geri yüklenir.
3. Komutu tekrar girerek, VM 'de RSS 'nin etkinleştirildiğini doğrulayın `Get-NetAdapterRss` . Başarılı olursa aşağıdaki örnek çıktı döndürülür:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Linux VM

RSS her zaman bir Azure Linux VM 'de varsayılan olarak etkindir. 2017 Ekim ' den itibaren Yayınlanan Linux çekirdekler, Linux VM 'nin daha yüksek ağ aktarım hızını elde etmelerini sağlayan yeni ağ iyileştirmeleri seçenekleri içerir.

### <a name="ubuntu-for-new-deployments"></a>Yeni dağıtımlar için Ubuntu

Ubuntu Azure çekirdeği, Azure 'da ağ performansı için en iyi duruma getirilmiştir. En son iyileştirmeleri almak için, önce desteklenen en son 18,04-LTS sürümünü aşağıdaki gibi yüklemeniz gerekir:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "18.04-LTS",
"Version": "latest"
```

Oluşturma işlemi tamamlandıktan sonra, en son güncelleştirmeleri almak için aşağıdaki komutları girin. Bu adımlar, şu anda Ubuntu Azure çekirdeğini çalıştıran VM 'Ler için de çalışır.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Aşağıdaki isteğe bağlı komut kümesi, zaten Azure çekirdeğine sahip olan ancak hatalarla daha fazla güncelleştirme başarısız olan Ubuntu dağıtımları için yararlı olabilir.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Mevcut VM 'Ler için Ubuntu Azure Kernel yükseltmesi

Azure Linux çekirdeğine yükselterek önemli performans elde edilebilir. Bu çekirdeğe sahip olup olmadığınızı doğrulamak için çekirdek sürümünüzü kontrol edin. Bu, örnekle aynı veya ondan sonra olmalıdır.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

SANAL makinenizin Azure çekirdeği yoksa, sürüm numarası genellikle "4,4" ile başlar. VM 'nin Azure çekirdeği yoksa kök olarak aşağıdaki komutları çalıştırın:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

En son iyileştirmeleri almak için, aşağıdaki parametreleri belirterek en son desteklenen sürüme sahip bir VM oluşturmak en iyisidir:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.7",
"Version": "latest"
```

Yeni ve mevcut VM 'Ler, en son Linux Tümleştirme Hizmetleri 'ni (LIS) yüklemeye yarar olabilir. Üretilen iş iyileştirmesi 4.2.2-2 ' den başlayarak, sonraki sürümlerde daha fazla geliştirmeler de içerse de bulunur. En son LIS 'yi yüklemek için aşağıdaki komutları girin:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

En iyi duruma getirmek için, aşağıdaki parametreleri belirterek en son desteklenen sürüme sahip bir VM oluşturmak en iyisidir:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Yeni ve mevcut VM 'Ler, en son Linux Tümleştirme Hizmetleri 'ni (LIS) yüklemeye yarar olabilir. Üretilen iş iyileştirmesi, 4,2 ' den başlayarak LIS 'de bulunur. LIS indirmek ve yüklemek için aşağıdaki komutları girin:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

[İndirme sayfasını](https://www.microsoft.com/download/details.aspx?id=55106)görüntüleyerek, Hyper-V Için Linux Tümleştirme hizmetleri sürüm 4,2 hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar
* [Yakınlık yerleştirme grubuyla](../virtual-machines/co-location.md) düşük gecikme süresi boyunca VM 'leri birbirlerine yakın bir şekilde dağıtın
* Senaryolarınız için [Azure VM 'yi kullanarak bant genişliği/aktarım hızı](virtual-network-bandwidth-testing.md) ile iyileştirilmiş sonuca bakın.
* [Bant genişliğinin sanal makinelere nasıl ayrıldığı](virtual-machine-network-throughput.md) hakkında bilgi edinin
* [Azure sanal ağ hakkında sık sorulan sorular (SSS)](virtual-networks-faq.md) hakkında daha fazla bilgi edinin