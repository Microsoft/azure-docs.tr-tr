---
title: GitHub 'dan Azure Linux aracısını güncelleştirme
description: Azure 'da Linux VM 'niz için Azure Linux aracısını güncelleştirme hakkında bilgi edinin
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 08/02/2017
ms.openlocfilehash: a274435d6e0fc32bdf5b2ab04702ed971d7c5175
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558993"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>Bir VM 'de Azure Linux aracısını güncelleştirme

Azure 'da bir Linux sanal makinesinde [Azure Linux aracınızı](https://github.com/Azure/WALinuxAgent) güncelleştirmek için şu durumda olmalıdır:

- Azure 'da çalışan bir Linux VM 'si.
- SSH kullanarak bu Linux VM 'sine bağlantı.

Önce Linux 'taki depodaki bir paketi denetlemeniz gerekir. Mevcut paketin en son sürümü olmayabilir, ancak otomatik güncelleştirme özelliğinin etkinleştirilmesi, Linux aracısının her zaman en son güncelleştirmeyi almasını sağlamaktır. Paket yöneticilerinden yükleme sorunlarıyla karşılaşmanız durumunda, çıkış satıcısından destek almanız gerekir.

> [!NOTE]
> Daha fazla bilgi için bkz. [Azure 'Da onaylama Linux dağıtımları](../linux/endorsed-distros.md)

Devam etmeden önce [Azure 'da sanal makine aracıları Için en düşük sürüm desteğini](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) doğrulayın.


## <a name="ubuntu"></a>Ubuntu

Geçerli paket sürümünüzü denetleyin

```bash
apt list --installed | grep walinuxagent
```

Paket önbelleğini Güncelleştir

```bash
sudo apt-get -qq update
```

En son paket sürümünü yükler

```bash
sudo apt-get install walinuxagent
```

Otomatik Güncelleştirme özelliğinin etkinleştirildiğinden emin olun. İlk olarak, etkin olup olmadığını kontrol edin:

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

14,04 için Wate gt hizmetini yeniden Başlat

```bash
initctl restart walinuxagent
```

16,04/17,04 için waagent hizmetini yeniden Başlat

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat/CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

Geçerli paket sürümünüzü denetleyin

```bash
sudo yum list WALinuxAgent
```

Kullanılabilir güncelleştirmeleri denetle

```bash
sudo yum check-update WALinuxAgent
```

En son paket sürümünü yükler

```bash
sudo yum install WALinuxAgent
```

Otomatik güncelleştirme 'nin etkinleştirildiğinden emin olun 

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

Waagent hizmetini yeniden başlatın

```
sudo service waagent restart
```

## <a name="rhelcentos-7"></a>RHEL/CentOS 7

Geçerli paket sürümünüzü denetleyin

```bash
sudo yum list WALinuxAgent
```

Kullanılabilir güncelleştirmeleri denetle

```bash
sudo yum check-update WALinuxAgent
```

En son paket sürümünü yükler

```bash
sudo yum install WALinuxAgent  
```

Otomatik Güncelleştirme özelliğinin etkinleştirildiğinden emin olun. İlk olarak, etkin olup olmadığını kontrol edin:

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

Waagent hizmetini yeniden başlatın

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

Geçerli paket sürümünüzü denetleyin

```bash
zypper info python-azure-agent
```

Kullanılabilir güncelleştirmeleri denetleyin. Yukarıdaki çıktı, paketin güncel olup olmadığını gösterir.

En son paket sürümünü yükler

```bash
sudo zypper install python-azure-agent
```

Otomatik güncelleştirme 'nin etkinleştirildiğinden emin olun 

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

Waagent hizmetini yeniden başlatın

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

Geçerli paket sürümünüzü denetleyin

```bash
zypper info python-azure-agent
```

Kullanılabilir güncelleştirmeleri denetle

Yukarıdaki çıktıda, bu, paketin güncel olup olmadığını gösterir.

En son paket sürümünü yükler

```bash
sudo zypper install python-azure-agent
```

Otomatik güncelleştirme 'nin etkinleştirildiğinden emin olun 

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

Waagent hizmetini yeniden başlatın

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>7 "Jesi"/de, 7 "uzat"

Geçerli paket sürümünüzü denetleyin

```bash
dpkg -l | grep waagent
```

Paket önbelleğini Güncelleştir

```bash
sudo apt-get -qq update
```

En son paket sürümünü yükler

```bash
sudo apt-get install waagent
```

Aracı otomatik güncelleştirmesini etkinleştir Bu dev 'nin bu sürümünde bir sürüm >= 2.0.16 yok, bu nedenle otomatik güncelleştirme kullanılamaz. Yukarıdaki komutun çıktısı, paketin güncel olup olmadığını gösterir.

### <a name="debian-8-jessie--debian-9-stretch"></a>8 "Jese"/deni 9 "uzat"

Geçerli paket sürümünüzü denetleyin

```bash
apt list --installed | grep waagent
```

Paket önbelleğini Güncelleştir

```bash
sudo apt-get -qq update
```

En son paket sürümünü yükler

```bash
sudo apt-get install waagent
```

Otomatik güncelleştirme 'nin etkinleştirildiğinden emin olun, etkin olup olmadığını denetleyin:

```bash
cat /etc/waagent.conf
```

' Otomatik güncelleştir. etkinleştirildi ' öğesini bulun. Bu çıktıyı görürseniz, etkin olur:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Çalıştırmayı etkinleştirmek için:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 ve Oracle Linux 7

Oracle Linux için `Addons` deponun etkinleştirildiğinden emin olun. Dosyayı `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) veya `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux) düzenleyip satırı `enabled=0` `enabled=1` Bu dosyada **[ol6_addons]** veya **[ol7_addons]** altında olacak şekilde değiştirin.

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

Genellikle bu tek ihtiyacınız vardır ancak bazı nedenlerle doğrudan yüklemeniz gerekiyorsa https://github.com , aşağıdaki adımları kullanın.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Dağıtım için bir aracı paketi yoksa Linux aracısını Güncelleştir

Wget 'i (Red hat, CentOS ve Oracle Linux sürüm 6,4 ve 6,5) komut satırına yazarak, bu uygulamayı varsayılan olarak yüklemek `sudo yum install wget` .

### <a name="1-download-the-latest-version"></a>1. en son sürümü indirin
[GitHub 'Da Azure Linux aracısının](https://github.com/Azure/WALinuxAgent/releases) sürümünü bir Web sayfasında açın ve en son sürüm numarasını bulun. (Yazarak geçerli sürümünüzü bulabilirsiniz `waagent --version` .)

2.2. x veya üzeri sürümler için şunu yazın:
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

2.2. x sürümü için şunu kullanın: önce paketi yüklemeniz gerekebilir `setuptools` . [buraya](https://pypi.python.org/pypi/setuptools)bakın. Ardından şunu çalıştırın:

```bash
sudo python setup.py install
```

Otomatik Güncelleştirme özelliğinin etkinleştirildiğinden emin olun. İlk olarak, etkin olup olmadığını kontrol edin:

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
