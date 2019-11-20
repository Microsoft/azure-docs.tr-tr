---
title: Azure 'da bir OpenSUSE VM 'ye MySQL 'i yükler
description: Azure 'da bir OpenSUSE Linux sanal makinesine MySQL yüklemeyi öğrenin.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: d5f2ef2d82cbcced6202ad2c09f23dd734d373b3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035592"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Azure'da OpenSUSE Linux çalıştıran bir sanal makineye MySQL yükleme

[MySQL](https://www.mysql.com) popüler, açık KAYNAKLı bir SQL veritabanıdır. Bu öğreticide, OpenSUSE Linux çalıştıran bir sanal makinenin nasıl oluşturulacağı ve MySQL 'nin nasıl yükleneceği gösterilmektedir.


CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz Azure CLI 2.0 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux çalıştıran bir sanal makine oluşturma

İlk olarak bir kaynak grubu oluşturun. Bu örnekte, kaynak grubu *Mysqsuseresourcegroup* olarak adlandırılır ve *Doğu ABD* bölgesinde oluşturulur.

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

VM 'yi oluşturun. Bu örnekte, sanal makinenin adı *Myvm* , VM boyutu ise *Standard_D2s_v3*, ancak iş yükünüz için en uygun olduğunu düşündüğünüz [VM boyutunu](sizes.md) seçmeniz gerekir.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

Ayrıca, MySQL için 3306 numaralı bağlantı noktası üzerinden trafiğe izin vermek üzere ağ güvenlik grubuna bir kural eklemeniz gerekir.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>VM’ye bağlanma

SANAL makineye bağlanmak için SSH kullanacaksınız. Bu örnekte, VM 'nin genel IP adresi *10.111.112.113*' dir. Sanal makineyi oluştururken çıktıda IP adresini görebilirsiniz.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>VM 'yi güncelleştirme
 
SANAL makineye bağlandıktan sonra, sistem güncelleştirmelerini ve düzeltme eklerini yükledikten sonra. 
   
```bash
sudo zypper update
```

VM 'nizi güncelleştirmek için istemleri izleyin.

## <a name="install-mysql"></a>MySQL'i yükleme 


MySQL 'i SSH üzerinden VM 'ye yükler. Uygun şekilde komut istemlerini yanıtlayın.

```bash
sudo zypper install mysql
```
 
MySQL, sistem önyüklendiğinde başlatılacak şekilde ayarlanır. 

```bash
sudo systemctl enable mysql
```
MySQL 'in etkinleştirildiğini doğrulayın.

```bash
systemctl is-enabled mysql
```

Bu, döndürmelidir: etkin.

Sunucuyu yeniden başlatın.

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL parolası

Yüklemeden sonra MySQL kök parolası varsayılan olarak boştur. MySQL 'i güvenli hale getirmek için **mysql\_güvenli\_yükleme** betiği çalıştırın. Betik, MySQL kök parolasını değiştirmenizi, anonim kullanıcı hesaplarını kaldırmanızı, uzak kök oturum açmayı devre dışı bırakmayı, test veritabanlarını kaldırmanızı ve ayrıcalıklar tablosunu yeniden yüklemenizi ister. 

Sunucu yeniden başlatıldıktan sonra, sanal makineye SSH tekrar.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>MySQL 'de oturum açma

Artık oturum açabilir ve MySQL istemi ' ni girebilirsiniz.

```bash  
mysql -u root -p
```
Bu, veritabanı ile etkileşimde bulunmak için SQL deyimlerini oluşturabileceğiniz MySQL istemine geçer.

Şimdi yeni bir MySQL kullanıcısı oluşturun.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Noktalı virgül (;) satırın sonunda, komutu sonlandırmak için çok önemlidir.


## <a name="create-a-database"></a>Veritabanı oluşturma


Bir veritabanı oluşturun ve `mysqluser` Kullanıcı izinlerini verin.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Veritabanı kullanıcı adları ve parolalar yalnızca veritabanına bağlanan betikler tarafından kullanılır.  Veritabanı kullanıcı hesabı adları, sistemdeki gerçek Kullanıcı hesaplarını temsil etmez.

Başka bir bilgisayardan oturum açmayı etkinleştirin. Bu örnekte, oturum açmaya izin verilecek bilgisayarın IP adresi *10.112.113.114*' dir.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
MySQL veritabanı yönetim yardımcı programından çıkmak için şunu yazın:

```    
quit
```


## <a name="next-steps"></a>Sonraki adımlar
MySQL hakkındaki ayrıntılar için [MySQL belgelerine](https://dev.mysql.com/doc)bakın.




