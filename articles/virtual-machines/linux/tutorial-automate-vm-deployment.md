---
title: Öğretici-Azure 'da Cloud-init ile Linux VM Özelleştirme
description: Bu öğreticide, Cloud-init ve Key Vault kullanarak Azure 'da ilk kez önyükleme yaparken Linux VM 'lerini nasıl özelleştireceğinizi öğreneceksiniz.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 27c7e32f081003ac236c6d1405eb3512f6c4433c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034632"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>Öğretici - Azure’da ilk önyüklemede bir Linux sanal makinesini özelleştirmek için cloud-init kullanma

Bir önceki öğreticide, sanal makineye nasıl SSH uygulanacağını ve NGINX öğesinin el ile nasıl yükleneceğini öğrendiniz. Hızlı ve tutarlı şekilde sanal makineler oluşturmak için genellikle bir otomasyon biçimi istenir. İlk önyüklemede bir sanal makineyi özelleştirmek için genellikle [cloud-init](https://cloudinit.readthedocs.io) kullanılır. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * cloud-init yapılandırma dosyası oluşturma
> * cloud-init dosyası kullanan bir sanal makine oluşturma
> * Sanal makine oluşturulduktan sonra çalıştırılan bir Node.js uygulamasını görüntüleme
> * Sertifikaları güvenli şekilde depolamak için Key Vault’u kullanma
> * cloud-init ile güvenli NGINX dağıtımlarını otomatikleştirme

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="cloud-init-overview"></a>Cloud-init genel bakış
[Cloud-init](https://cloudinit.readthedocs.io), Linux VM’sini ilk kez önyüklendiğinde özelleştirmeyi sağlayan, sık kullanılan bir yaklaşımdır. cloud-init’i paket yükleme, dosyalara yazma ve kullanıcılar ile güvenliği yapılandırma işlemleri için kullanabilirsiniz. cloud-init önyükleme işlemi sırasında çalışırken, yapılandırmanıza uygulayabileceğiniz ek adım veya gerekli aracı yoktur.

Cloud-init, dağıtımlar arasında da çalışır. Örneğin, bir paket yüklemek için **apt-get install** veya **yum install** kullanmazsınız. Bunun yerine, yüklenecek paketlerin listesini tanımlayabilirsiniz. Cloud-init, seçtiğiniz dağıtım için yerel paket yönetim aracını otomatik olarak kullanır.

Azure’a sağladıkları görüntülere cloud-init’in dahil edilmesini ve bu görüntülerde çalışmasını sağlamak için iş ortaklarımızla çalışıyoruz. Aşağıdaki tabloda, Azure platform görüntülerindeki geçerli cloud-init kullanılabilirliği açıklanmaktadır:

| Yayımcı | Sunduğu | SKU | Sürüm | cloud-init hazır |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |latest |evet | 
|Canonical |UbuntuServer |16.04-LTS |latest |evet | 
|Canonical |UbuntuServer |14.04.5-LTS |latest |evet |
|CoreOS |CoreOS |Dengeli |latest |evet |
|OpenLogic 7,6 |CentOS |7-CI |latest |önizleme |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |evet |
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 |önizleme |


## <a name="create-cloud-init-config-file"></a>cloud-init yapılandırma dosyası oluşturma
cloud-init’i uygulamalı olarak görmek için, NGINX’i yükleyen ve basit bir 'Merhaba Dünya' Node.js uygulaması çalıştıran bir sanal makine oluşturun. Aşağıdaki cloud-init yapılandırması, gerekli paketleri yükler, bir Node.js uygulaması oluşturur, ardından uygulamayı kullanıma hazırlar ve başlatır.

Bash isteminizdeki veya Cloud Shell, *kabuğunuzda Cloud-init. txt* adlı bir dosya oluşturun ve aşağıdaki yapılandırmayı yapıştırın. Örneğin, dosyayı oluşturmak ve kullanılabilir düzenleyicilerin listesini görmek için `sensible-editor cloud-init.txt` yazın. Başta birinci satır olmak üzere cloud-init dosyasının tamamının doğru bir şekilde kopyalandığından emin olun:

```azurecli-interactive
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

cloud-init yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. [cloud-init config örnekleri](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).

## <a name="create-virtual-machine"></a>Sanal makine oluşturma
VM oluşturabilmek için önce [az group create](/cli/azure/group#az-group-create) ile bir kaynak grubu oluşturun. Aşağıdaki örnek, *eastus* konumunda *myResourceGroupAutomate* adlı bir kaynak grubu oluşturur:

```azurecli-interactive
az group create --name myResourceGroupAutomate --location eastus
```

Şimdi [az vm create](/cli/azure/vm#az-vm-create) ile bir VM oluşturun. `--custom-data` parametresini kullanarak cloud-init yapılandırma dosyanızı geçirin. Dosyayı mevcut çalışma dizininizin dışına kaydettiyseniz *cloud-init.txt* yapılandırmasının tam yolunu belirtin. Aşağıdaki örnek *myVM* adlı bir VM oluşturur:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myAutomatedVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

VM’nin oluşturulması, paketlerin yüklenmesi ve uygulamanın başlatılması birkaç dakika sürebilir. Azure CLI sizi isteme geri döndürdükten sonra çalışmaya devam eden arka plan görevleri vardır. Uygulamaya erişmeniz birkaç dakika sürebilir. VM oluşturulduktan sonra, Azure CLI tarafından görüntülenen `publicIpAddress` değerini not edin. Bu adres, web tarayıcısı aracılığıyla Node.js uygulamasına erişmek için kullanılır.

Web trafiğinin VM’nize erişmesine izin vermek için, [az vm open-port](/cli/azure/vm#az-vm-open-port) komutuyla İnternet’te 80 numaralı bağlantı noktasını açın:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myAutomatedVM
```

## <a name="test-web-app"></a>Web uygulamasını test etme
Artık bir Web tarayıcısı açıp adres çubuğuna *http:\/\/\<publicıpaddress >* girebilirsiniz. VM oluşturma işleminden kendi herkese açık IP adresinizi sağlayın. Node.js uygulamanız, aşağıdaki örnekte olduğu gibi görüntülenir:

![Çalışan NGINX sitesini görüntüleme](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Key Vault’tan sertifikalar ekleme
Bu isteğe bağlı bölümde, Azure Key Vault’ta sertifikaları nasıl güvenli şekilde depolayabileceğiniz ve sanal makine dağıtımı sırasında bunları nasıl ekleyebileceğiniz gösterilir. Bu süreç, sertifikaları içeren özel bir görüntü kullanmak yerine ilk önyüklemede bir sanal makineye en güncel sertifikaların eklenmesini sağlar. İşlem sırasında sertifika hiçbir zaman Azure platformundan ayrılmaz veya bir betikte, komut satırı geçmişinde ya da şablonda gösterilmez.

Azure Key Vault, sertifikalar veya parolalar gibi şifreleme anahtarları ile gizli dizilerin güvenliğini sağlar. Key Vault, anahtar yönetimi işleminin kolaylaştırılmasına yardımcı olur ve verilerinize erişen ve bunları şifreleyen anahtarları denetiminizde tutmanıza olanak sağlar. Bu senaryo, Key Vault’un nasıl kullanılacağına dair kapsamlı bir genel bakış sunmasa da, sertifika oluşturma ve kullanmaya yönelik bazı Key Vault kavramlarını sunar.

Aşağıdaki adımlar şunları nasıl yapabileceğinizi gösterir:

- Azure Key Vault oluşturma
- Sertifikaları oluşturma veya Key Vault’a yükleme
- Sanal makineye eklenecek sertifikadan gizli dizi oluşturma
- VM oluşturma ve sertifika ekleme

### <a name="create-an-azure-key-vault"></a>Azure Key Vault oluşturma
İlk olarak [az keyvault create](/cli/azure/keyvault#az-keyvault-create) ile bir Key Vault oluşturun ve bu anahtarın VM dağıtırken kullanılmasını etkinleştirin. Her Key Vault benzersiz bir ad gerektirir ve küçük harflerle yazılmalıdır. Aşağıdaki örnekte yer alan *mykeyvault* değerini, kendi benzersiz Key Vault adınızla değiştirin:

```azurecli-interactive
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Sertifika oluşturma ve sertifikayı Key Vault’ta depolama
Üretim sırasında kullanım için, [az keyvault certificate import](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) komutunu kullanarak güvenilen bir sağlayıcı tarafından imzalanan geçerli bir sertifikayı içeri aktarmalısınız. Bu öğreticide, aşağıdaki örnekte varsayılan sertifika ilkesini kullanan [az keyvault certificate create](/cli/azure/keyvault/certificate#az-keyvault-certificate-create) ile nasıl otomatik olarak imzalanan sertifika oluşturabileceğiniz gösterilmektedir:

```azurecli-interactive
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy --output json)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Sertifikayı VM ile kullanım için hazırlama
VM oluşturma sürecinde sertifikayı kullanmak için, [az keyvault secret list-versions](/cli/azure/keyvault/secret#az-keyvault-secret-list-versions) komutuyla sertifikanızın kimliğini alın. VM, sertifikanın önyüklemede eklenmesi için belirli bir biçimde olmasını gerektirir, bu nedenle [az vm secret format](/cli/azure/vm) komutu ile sertifikayı dönüştürün. Aşağıdaki örnekte, sonraki adımlarda kullanım kolaylığı sağlamak için bu komutların çıkışı değişkenlere atanmaktadır:

```azurecli-interactive
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret" --output json)
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>NGINX’in güvenliğini sağlamak için cloud-init yapılandırması oluşturma
VM oluşturduğunuzda, sertifika ve anahtarlar korunan */var/lib/waagent/* dizininde depolanır. VM’ye sertifika eklenmesini ve NGINX’in yapılandırılmasını otomatikleştirmek için önceki örnekte yer alan güncelleştirilmiş bir cloud-init yapılandırmasını kullanabilirsiniz.

*cloud-init-secured.txt* adlı bir dosya oluşturup aşağıdaki yapılandırmayı yapıştırın. Cloud Shell kullanıyorsanız, yerel makinenizde değil, bulut-init yapılandırma dosyasını oluşturun. Örneğin, dosyayı oluşturmak ve kullanılabilir düzenleyicilerin listesini görmek için `sensible-editor cloud-init-secured.txt` yazın. Başta birinci satır olmak üzere cloud-init dosyasının tamamının doğru bir şekilde kopyalandığından emin olun:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Güvenli VM oluşturma
Şimdi [az vm create](/cli/azure/vm#az-vm-create) ile bir VM oluşturun. Bu sertifika verileri, `--secrets` parametresiyle Key Vault’tan eklenir. Önceki örnekte olduğu gibi `--custom-data` parametresi ile de cloud-init yapılandırmasını geçirirsiniz:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

VM’nin oluşturulması, paketlerin yüklenmesi ve uygulamanın başlatılması birkaç dakika sürebilir. Azure CLI sizi isteme geri döndürdükten sonra çalışmaya devam eden arka plan görevleri vardır. Uygulamaya erişmeniz birkaç dakika sürebilir. VM oluşturulduktan sonra, Azure CLI tarafından görüntülenen `publicIpAddress` değerini not edin. Bu adres, web tarayıcısı aracılığıyla Node.js uygulamasına erişmek için kullanılır.

Güvenli web trafiğinin VM’nize erişmesine izin vermek için, [az vm open-port](/cli/azure/vm#az-vm-open-port) komutuyla internette 443 numaralı bağlantı noktasını açın:

```azurecli-interactive
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --port 443
```

### <a name="test-secure-web-app"></a>Güvenli web uygulamasını test etme
Artık bir Web tarayıcısı açıp adres çubuğuna *https:\/\/\<publicıpaddress >* girebilirsiniz. Önceki VM oluşturma işleminin çıkışında gösterildiği gibi kendi genel IP adresinizi girin. Otomatik olarak imzalanan sertifika kullanıyorsanız güvenlik uyarısını kabul edin:

![Web tarayıcısı güvenlik uyarısını kabul edin](./media/tutorial-automate-vm-deployment/browser-warning.png)

Güvenli NGINX siteniz ve Node.js uygulamanız aşağıdaki örnekte olduğu gibi görüntülenir:

![Çalışan güvenli NGINX sitesini görüntüleme](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, cloud-init ile ilk önyüklemede VM’leri yapılandırdınız. Şunları öğrendiniz:

> [!div class="checklist"]
> * cloud-init yapılandırma dosyası oluşturma
> * cloud-init dosyası kullanan bir sanal makine oluşturma
> * Sanal makine oluşturulduktan sonra çalıştırılan bir Node.js uygulamasını görüntüleme
> * Sertifikaları güvenli şekilde depolamak için Key Vault’u kullanma
> * cloud-init ile güvenli NGINX dağıtımlarını otomatikleştirme

Özel VM görüntülerinin nasıl oluşturulacağını öğrenmek için sonraki öğreticiye geçin.

> [!div class="nextstepaction"]
> [Özel VM görüntüleri oluşturma](./tutorial-custom-images.md)
