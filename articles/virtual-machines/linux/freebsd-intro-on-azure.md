---
title: Azure 'da FreeBSD 'ye giriş
description: Azure 'da FreeBSD sanal makinelerini kullanma hakkında bilgi edinin
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: d243806d619aa6d7328220eae53804d734737489
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036117"
---
# <a name="introduction-to-freebsd-on-azure"></a>Azure 'da FreeBSD 'ye giriş
Bu makalede, Azure 'da FreeBSD sanal makinesini çalıştırmaya ilişkin bir genel bakış sunulmaktadır.

## <a name="overview"></a>Genel Bakış
Microsoft Azure için FreeBSD, modern sunucular, masaüstü bilgisayarlar ve ekli platformları desteklemek için kullanılan gelişmiş bir bilgisayar işletim sistemidir.

Microsoft Corporation, ücretsiz BSD görüntülerini Azure 'da önceden yapılandırılmış [Azure VM Konuk Aracısı](https://github.com/Azure/WALinuxAgent/) ile kullanıma sunulmuştur. Şu anda, aşağıdaki FreeBSD sürümleri Microsoft tarafından resim olarak sunulmaktadır:

- [Azure Marketi üzerinde FreeBSD 10,4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [Azure Marketi üzerinde FreeBSD 11,2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)
- [Azure Marketi üzerinde FreeBSD 12,0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD120)

Aracı, FreeBSD VM 'si ile Azure yapısı arasındaki iletişimden sorumludur (Kullanıcı adı, parola veya SSH anahtarı, ana bilgisayar adı vb.) ve seçmeli VM uzantıları için işlevselliği etkinleştirir.

FreeBSD 'nin gelecekteki sürümlerinde olduğu gibi, strateji güncel kalmakta ve en son sürümlerin FreeBSD sürüm Mühendisliği ekibi tarafından yayımlandıklarında kısa bir süre içinde kullanılabilmesini sağlar.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>FreeBSD üzerinde Azure CLı aracılığıyla FreeBSD VM 'si oluşturma
İlk olarak, bir FreeBSD makinesinde aşağıdaki komutu izleyerek [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 'yı yüklemeniz gerekir.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Bash, FreeBSD makinenizde yüklü değilse, yüklemeden önce aşağıdaki komutu çalıştırın. 

```bash
sudo pkg install bash
```

Bir Python, FreeBSD makinenizde yüklü değilse, yüklemeden önce aşağıdaki komutları çalıştırın. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Yükleme sırasında sizden `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`istenir. `y` yanıtlayın ve `/etc/rc.conf` `a path to an rc file to update`olarak girerseniz, sorun `ERROR: [Errno 13] Permission denied`karşılayabilirsiniz. Bu sorunu çözmek için, dosya `etc/rc.conf`için geçerli kullanıcıya yazma hakkı vermelisiniz.

Artık Azure 'da oturum açabilir ve FreeBSD VM 'nizi oluşturabilirsiniz. FreeBSD 11,0 VM oluşturmak için aşağıda bir örnek verilmiştir. Ayrıca, yeni oluşturulan genel IP için genel olarak benzersiz bir DNS adıyla `--public-ip-address-dns-name` parametresini ekleyebilirsiniz. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Daha sonra, boş BSD VM 'niz üzerinde, yukarıdaki dağıtımın çıktısında yazdırılmış IP adresini kullanarak oturum açabilirsiniz. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>FreeBSD için VM uzantıları
Aşağıda, FreeBSD 'de desteklenen VM uzantıları verilmiştir.

### <a name="vmaccess"></a>VMAccess
[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) uzantısı şunları yapabilir:

* Özgün sudo kullanıcısının parolasını sıfırlayın.
* Belirtilen parolayla yeni bir sudo kullanıcısı oluşturun.
* Ortak ana bilgisayar anahtarını verilen anahtarla ayarlayın.
* Konak anahtarı sağlanmazsa VM sağlama sırasında belirtilen genel ana bilgisayar anahtarını sıfırlayın.
* Reset_ssh true olarak ayarlandıysa SSH bağlantı noktasını (22) açın ve sshd_config geri yükleyin.
* Mevcut kullanıcıyı kaldırın.
* Diskleri denetleyin.
* Eklenen bir diski onarın.

### <a name="customscript"></a>CustomScript
[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) uzantısı şunları yapabilir:

* Sağlanmışsa, özelleştirilmiş betikleri Azure Storage 'dan veya dış genel depolamadan (örneğin, GitHub) indirin.
* Giriş noktası betiğini çalıştırın.
* Satır içi komutları destekler.
* Shell ve Python betiklerdeki Windows stili yeni satır otomatik olarak Dönüştür.
* Shell ve Python betiklerdeki BOM 'ı otomatik olarak kaldırın.
* CommandToExecute içindeki hassas verileri koruyun.

> [!NOTE]
> FreeBSD VM 'si yalnızca CustomScript sürüm 1. x 'i şimdi destekler.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Kimlik doğrulaması: Kullanıcı adları, parolalar ve SSH anahtarları
Azure portal kullanarak FreeBSD sanal makinesi oluştururken, bir Kullanıcı adı, parola veya SSH ortak anahtarı sağlamanız gerekir.
Azure üzerinde FreeBSD sanal makinesini dağıtmaya yönelik kullanıcı adları, sanal makinede (örneğin, "root") zaten mevcut olan sistem hesaplarının (UID < 100) adlarıyla aynı olmamalıdır.
Şu anda yalnızca RSA SSH anahtarı desteklenir. Çok satırlı SSH anahtarı `---- BEGIN SSH2 PUBLIC KEY ----` ile başlamalı ve `---- END SSH2 PUBLIC KEY ----`bitmelidir.

## <a name="obtaining-superuser-privileges"></a>Süper kullanıcı ayrıcalıkları alma
Azure üzerinde sanal makine örneği dağıtımı sırasında belirtilen kullanıcı hesabı ayrıcalıklı bir hesaptır. Sudo paketi yayınlanan FreeBSD görüntüsüne yüklendi.
Bu kullanıcı hesabından oturum açtıktan sonra komut sözdizimini kullanarak komutları kök olarak çalıştırabilirsiniz.

```
$ sudo <COMMAND>
```

İsteğe bağlı olarak `sudo -s`kullanarak bir kök kabuğu elde edebilirsiniz.

## <a name="known-issues"></a>Bilinen sorunlar
[Azure VM Konuk Aracısı](https://github.com/Azure/WALinuxAgent/) sürümü 2.2.2, Azure üzerinde FreeBSD VM 'si için sağlama hatasına neden olan [bilinen bir sorunla](https://github.com/Azure/WALinuxAgent/pull/517) karşılaştı. Bu çözüm, [Azure VM Konuk Aracısı](https://github.com/Azure/WALinuxAgent/) sürüm 2.2.3 ve sonraki sürümleri tarafından yakalanmıştı. 

## <a name="next-steps"></a>Sonraki adımlar
* FreeBSD VM oluşturmak için [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) ' ne gidin.
