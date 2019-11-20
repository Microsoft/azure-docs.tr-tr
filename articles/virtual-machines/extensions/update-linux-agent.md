---
title: GitHub 'dan Azure Linux aracısını güncelleştirme
description: Azure 'da Linux VM 'niz için Azure Linux aracısını güncelleştirme hakkında bilgi edinin
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: akjosh
ms.openlocfilehash: 2215136c02d9cf967e0184af7588ce8d48362009
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072947"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Bir VM 'de Azure Linux aracısını güncelleştirme

Azure 'da bir Linux sanal makinesinde [Azure Linux aracınızı](https://github.com/Azure/WALinuxAgent) güncelleştirmek için şu durumda olmalıdır:

- Azure 'da çalışan bir Linux VM 'si.
- SSH kullanarak bu Linux VM 'sine bağlantı.

Önce Linux 'taki depodaki bir paketi denetlemeniz gerekir. Mevcut paketin en son sürümü olmayabilir, ancak otomatik güncelleştirme özelliğinin etkinleştirilmesi, Linux aracısının her zaman en son güncelleştirmeyi almasını sağlamaktır. Paket yöneticilerinden yükleme sorunlarıyla karşılaşmanız durumunda, çıkış satıcısından destek almanız gerekir.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Azure 'da en düşük sanal makine Aracısı desteği
Devam etmeden önce [Azure 'da sanal makine aracıları Için en düşük sürüm desteğini](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) doğrulayın.

## <a name="updating-the-azure-linux-agent"></a>Azure Linux aracısını güncelleştirme

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Geçerli paket sürümünüzü denetleyin

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Paket önbelleğini Güncelleştir

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>En son paket sürümünü yükler

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Otomatik güncelleştirme 'nin etkinleştirildiğinden emin olun

İlk olarak, etkin olup olmadığını kontrol edin:

```bash
cat /etc/waagent.conf
```

' Otomatik güncelleştir. etkinleştirildi ' öğesini bulun. Bu çıktıyı görürseniz, etkin olur:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Çalıştırmayı etkinleştirmek için:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Waagent hizmetini yeniden başlatın

#### <a name="restart-agent-for-1404"></a>14,04 için aracıyı yeniden başlatın

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>16,04/17,04 için aracıyı yeniden Başlat

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Deki 7 "Wheezy"

#### <a name="check-your-current-package-version"></a>Geçerli paket sürümünüzü denetleyin

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Paket önbelleğini Güncelleştir

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>En son paket sürümünü yükler

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Aracı otomatik güncelleştirmesini etkinleştir
De2.0.16 'in bu sürümünde bir sürüm > = yok, bu nedenle otomatik güncelleştirme kullanılamaz. Yukarıdaki komutun çıktısı, paketin güncel olup olmadığını gösterir.

### <a name="debian-8-jessie--debian-9-stretch"></a>8 "Jese"/deni 9 "uzat"

#### <a name="check-your-current-package-version"></a>Geçerli paket sürümünüzü denetleyin

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Paket önbelleğini Güncelleştir

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>En son paket sürümünü yükler

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>Otomatik güncelleştirme 'nin etkinleştirildiğinden emin olun 

İlk olarak, etkin olup olmadığını kontrol edin:

```bash
cat /etc/waagent.conf
```

' Otomatik güncelleştir. etkinleştirildi ' öğesini bulun. Bu çıktıyı görürseniz, etkin olur:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Çalıştırmayı etkinleştirmek için:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Waagent hizmetini yeniden başlatın

```
sudo systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat/CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Geçerli paket sürümünüzü denetleyin

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Kullanılabilir güncelleştirmeleri denetle

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>En son paket sürümünü yükler

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Otomatik güncelleştirme 'nin etkinleştirildiğinden emin olun 

İlk olarak, etkin olup olmadığını kontrol edin:

```bash
cat /etc/waagent.conf
```

' Otomatik güncelleştir. etkinleştirildi ' öğesini bulun. Bu çıktıyı görürseniz, etkin olur:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Çalıştırmayı etkinleştirmek için:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Waagent hizmetini yeniden başlatın

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Geçerli paket sürümünüzü denetleyin

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Kullanılabilir güncelleştirmeleri denetle

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>En son paket sürümünü yükler

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Otomatik güncelleştirme 'nin etkinleştirildiğinden emin olun 

İlk olarak, etkin olup olmadığını kontrol edin:

```bash
cat /etc/waagent.conf
```

' Otomatik güncelleştir. etkinleştirildi ' öğesini bulun. Bu çıktıyı görürseniz, etkin olur:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Çalıştırmayı etkinleştirmek için:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Waagent hizmetini yeniden başlatın

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Geçerli paket sürümünüzü denetleyin

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Kullanılabilir güncelleştirmeleri denetle

Yukarıdaki çıktı, paketin güncel olup olmadığını gösterir.

#### <a name="install-the-latest-package-version"></a>En son paket sürümünü yükler

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Otomatik güncelleştirme 'nin etkinleştirildiğinden emin olun 

İlk olarak, etkin olup olmadığını kontrol edin:

```bash
cat /etc/waagent.conf
```

' Otomatik güncelleştir. etkinleştirildi ' öğesini bulun. Bu çıktıyı görürseniz, etkin olur:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Çalıştırmayı etkinleştirmek için:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Waagent hizmetini yeniden başlatın

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Geçerli paket sürümünüzü denetleyin

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Kullanılabilir güncelleştirmeleri denetle

Yukarıdaki çıktıda, bu, paketin güncel olup olmadığını gösterir.

#### <a name="install-the-latest-package-version"></a>En son paket sürümünü yükler

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Otomatik güncelleştirme 'nin etkinleştirildiğinden emin olun 

İlk olarak, etkin olup olmadığını kontrol edin:

```bash
cat /etc/waagent.conf
```

' Otomatik güncelleştir. etkinleştirildi ' öğesini bulun. Bu çıktıyı görürseniz, etkin olur:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Çalıştırmayı etkinleştirmek için:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>Waagent hizmetini yeniden başlatın

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 ve 7

Oracle Linux için `Addons` deposunun etkinleştirildiğinden emin olun. `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) veya `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux) dosyasını düzenlemeyi seçin ve satır `enabled=0` bu dosyadaki **[`enabled=1`]** veya **[ol6_addons]** altındaki ol7_addons olarak değiştirin.

Ardından, Azure Linux aracısının en son sürümünü yüklemek için şunu yazın:

```bash
sudo yum install WALinuxAgent
```

Eklenti deposunu bulamazsanız, bu satırları Oracle Linux sürümüne göre. repo dosyanızın sonuna eklemeniz yeterlidir:

Oracle Linux 6 sanal makineler için:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Oracle Linux 7 sanal makinesi için:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Ardından şunu yazın:

```bash
sudo yum update WALinuxAgent
```

Genellikle bu tek ihtiyacınız vardır ancak bazı nedenlerle https://github.com doğrudan yüklemeniz gerekiyorsa, aşağıdaki adımları kullanın.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Dağıtım için bir aracı paketi yoksa Linux aracısını Güncelleştir

Wget 'i (Red hat, CentOS ve Oracle Linux sürüm 6,4 ve 6,5), komut satırına `sudo yum install wget` yazarak, bu uygulamayı varsayılan olarak yüklemek.

### <a name="1-download-the-latest-version"></a>1. en son sürümü indirin
[GitHub 'Da Azure Linux aracısının](https://github.com/Azure/WALinuxAgent/releases) sürümünü bir Web sayfasında açın ve en son sürüm numarasını bulun. (`waagent --version`yazarak geçerli sürümünüzü bulabilirsiniz.)

#### <a name="for-version-22x-or-later-type"></a>2\.2. x veya üzeri sürümler için şunu yazın:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

Aşağıdaki satır örnek olarak 2.2.0 sürümünü kullanır:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Azure Linux aracısını yükler

#### <a name="for-version-22x-use"></a>2\.2. x sürümü için şunu kullanın:
Önce paketi `setuptools` yüklemeniz gerekebilir. [buraya](https://pypi.python.org/pypi/setuptools)bakın. Ardından şunu çalıştırın:

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Otomatik güncelleştirme 'nin etkinleştirildiğinden emin olun

İlk olarak, etkin olup olmadığını kontrol edin:

```bash
cat /etc/waagent.conf
```

' Otomatik güncelleştir. etkinleştirildi ' öğesini bulun. Bu çıktıyı görürseniz, etkin olur:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Çalıştırmayı etkinleştirmek için:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. waagent hizmetini yeniden başlatın
Çoğu Linux distros için:

```bash
sudo service waagent restart
```

Ubuntu için şunu kullanın:

```bash
sudo service walinuxagent restart
```

CoreOS için şunu kullanın:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Azure Linux Aracısı sürümünü onaylayın
    
```bash
waagent -version
```

CoreOS için yukarıdaki komut çalışmayabilir.

Azure Linux Aracısı sürümünün yeni sürüme güncelleştirildiğini görürsünüz.

Azure Linux Aracısı hakkında daha fazla bilgi için bkz. [Azure Linux Aracısı Benioku dosyası](https://github.com/Azure/WALinuxAgent).
