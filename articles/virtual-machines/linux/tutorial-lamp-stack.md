---
title: Öğretici-bir VM 'de lamba ve WordPress dağıtma
description: Bu öğreticide, Azure 'daki bir Linux sanal makinesine lamba yığınını ve WordPress 'yi nasıl yükleyeceğinizi öğreneceksiniz.
author: cynthn
ms.collection: linux
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: 5365bad5fdea2a8213defc103f0cdd966ebe50a5
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816359"
---
# <a name="tutorial-install-a-lamp-stack-on-an-azure-linux-vm"></a>Öğretici: Azure Linux sanal makinesine lamba yığını yüklemesi

Bu makalede, Azure’daki bir Ubuntu sanal makinesine Apache web sunucusunun, MySQL ve PHP’nin (LAMP yığını) nasıl dağıtılacağı gösterilmektedir. LAMP sunucusunu çalışır halde görmek için, isteğe bağlı olarak bir WordPress sitesi yükleyip yapılandırabilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Ubuntu sanal makinesi oluşturma 
> * Web trafiği için 80 numaralı bağlantı noktasını açın
> * Apache, MySQL ve PHP yükleme
> * Yükleme ve yapılandırmayı doğrulama
> * WordPress yükleme 

Bu kurulum, hızlı testler veya kavram kanıtı içindir. Üretim ortamına yönelik öneriler de dahil olmak üzere, LAMP yığını hakkında daha fazla bilgi için [Ubuntu belgelerine](https://help.ubuntu.com/community/ApacheMySQLPHP) bakın.

Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](../../cloud-shell/overview.md)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

[az vm create](/cli/azure/vm) komutuyla bir sanal makine oluşturun. 

Aşağıdaki örnekte *myVM* adlı bir VM oluşturulur ve varsayılan anahtar konumunda henüz yoksa SSH anahtarları oluşturulur. Belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın. Komut ayrıca *azureuser* yönetici kullanıcı adını belirler. Bu adı daha sonra VM'ye bağlanmak için kullanacaksınız. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir. `publicIpAddress` değerini not edin. Sonraki adımlarda bu adres, VM’ye erişmek için kullanılır.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```



## <a name="open-port-80-for-web-traffic"></a>Web trafiği için 80 numaralı bağlantı noktasını açın 

Varsayılan olarak, Azure’a dağıtılmış Linux VM'lerde yalnızca SSH bağlantılarına izin verilir. Bu VM bir web sunucusu olacağı için, İnternet’ten 80 numaralı bağlantı noktasını açmanız gerekir. İstediğiniz bağlantı noktasını açmak için [az vm open-port](/cli/azure/vm) komutunu kullanın.  
 
```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

Sanal makinenize bağlantı noktalarını açma hakkında daha fazla bilgi için bkz. [açık bağlantı noktaları](nsg-quickstart.md).

## <a name="ssh-into-your-vm"></a>VM’ye SSH uygulama

VM 'nizin genel IP adresini henüz bilmiyorsanız [az Network public-IP List](/cli/azure/network/public-ip) komutunu çalıştırın. Bu IP adresine sonraki adımlarda ihtiyacınız olacak.

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Sanal makine ile bir SSH oturumu oluşturmak için aşağıdaki komutu kullanın. Sanal makinenizin doğru genel IP adresi ile değiştirdiğinizden emin olun. Bu örnekte IP adresi *40.68.254.142*’dir. *azureuser*, VM'yi oluşturduğunuzda belirlenen yönetici kullanıcı adıdır.

```bash
ssh azureuser@40.68.254.142
```


## <a name="install-apache-mysql-and-php"></a>Apache, MySQL ve PHP yükleme

Ubuntu paket kaynaklarını güncelleştirmek ve Apache, MySQL ve PHP yüklemek için aşağıdaki komutu çalıştırın. `lamp-server^` paket adının parçası olan, komutun sonundaki şapka karakterine (^) dikkat edin. 


```bash
sudo apt update && sudo apt install lamp-server^
```

Paketleri ve diğer bağımlılıkları yüklemeniz istenir. Bu işlem, MySQL ile PHP kullanmak için gereken en düşük PHP uzantılarını yükler.  

## <a name="verify-apache"></a>Apache 'yi doğrula

Aşağıdaki komutla Apache sürümünü denetleyin:
```bash
apache2 -v
```

Apache yüklüyken ve sanal makinenizde 80 numaralı bağlantı noktası açıkken, web sunucusuna İnternet üzerinden erişilebilir. Apache2 Ubuntu Varsayılan Sayfasını görüntülemek için bir web tarayıcısı açın ve sanal makinenin genel IP adresini girin. Sanal makineye SSH oturumu açmak için kullandığınız genel IP adresini kullanın:

![Apache varsayılan sayfası][3]


## <a name="verify-and-secure-mysql"></a>MySQL 'i doğrulama ve güvenli hale getirme

Aşağıdaki komutla MySQL sürümünü denetleyin (ana `V` parametresini not edin):

```bash
mysql -V
```

Kök parola ayarlama da dahil olmak üzere MySQL yüklemesinin güvenliğinin sağlanmasına yardımcı olmak için betiği çalıştırın `mysql_secure_installation` . 

```bash
sudo mysql_secure_installation
```

İsteğe bağlı olarak parolayı Doğrula eklentisini ayarlayabilirsiniz (önerilir). Ardından, MySQL kök kullanıcısı için bir parola ayarlayın ve ortamınız için kalan güvenlik ayarlarını yapılandırın. Tüm sorulara "Y" (Evet) yanıtını etmenizi öneririz.

MySQL özelliklerini (MySQL veritabanı oluşturma, kullanıcı ekleme veya yapılandırma ayarlarını değiştirme) denemek istiyorsanız MySQL’de oturum açın. Bu öğreticiyi tamamlamak için bu adım gerekli değildir.

```bash
sudo mysql -u root -p
```

İşiniz bittiğinde, `\q` yazarak mysql isteminden çıkın.

## <a name="verify-php"></a>PHP 'yi doğrula

Aşağıdaki komutla PHP sürümünü denetleyin:

```bash
php -v
```

Daha fazla test etmek istiyorsanız, tarayıcıda görüntülenecek bir hızlı PHP bilgi sayfası oluşturun. Aşağıdaki komut, PHP bilgi sayfasını oluşturur:

```bash
sudo sh -c 'echo "<?php phpinfo(); ?>" > /var/www/html/info.php'
```

Artık oluşturduğunuz PHP bilgi sayfasını denetleyebilirsiniz. Bir tarayıcıyı açın ve `http://yourPublicIPAddress/info.php` adresine gidin. Sanal makinenizin genel IP adresini değiştirin. Şu görüntüye benzemelidir.

![PHP bilgi sayfası][2]

[!INCLUDE [virtual-machines-linux-tutorial-wordpress.md](../../../includes/virtual-machines-linux-tutorial-wordpress.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure’da bir LAMP sunucusu dağıttınız. Şunları öğrendiniz:

> [!div class="checklist"]
> * Ubuntu sanal makinesi oluşturma
> * Web trafiği için 80 numaralı bağlantı noktasını açın
> * Apache, MySQL ve PHP yükleme
> * Yükleme ve yapılandırmayı doğrulama
> * LAMP sunucusuna WordPress yükleme

TLS/SSL sertifikalarıyla Web sunucularının güvenliğini sağlama hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [TLS ile güvenli Web sunucusu](tutorial-secure-web-server.md)

[2]: ./media/tutorial-lamp-stack/phpsuccesspage.png
[3]: ./media/tutorial-lamp-stack/apachesuccesspage.png
