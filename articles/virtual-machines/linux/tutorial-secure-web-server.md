---
title: 'Öğretici: TLS/SSL sertifikalarıyla bir Web sunucusunun güvenliğini sağlama'
description: Bu öğreticide, Azure Key Vault’ta depolanan SSL sertifikaları ile NGINX web sunucusunda çalışan bir Linux sanal makinesinin güvenliğini sağlamak için Azure CLI kullanmayı öğreneceksiniz.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 4b2290d29d1f6fbf8acd2e2652cd47a321fe674f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813173"
---
# <a name="tutorial-use-tlsssl-certificates-to-secure-a-web-server"></a>Öğretici: bir Web sunucusunun güvenliğini sağlamak için TLS/SSL sertifikaları kullanma
Web sunucularının güvenliğini sağlamak için, daha önce Güvenli Yuva Katmanı (SSL) olarak bilinen bir Aktarım Katmanı Güvenliği (TLS), sertifika Web trafiğini şifrelemek için kullanılabilir. Bu TLS/SSL sertifikaları Azure Key Vault depolanabilir ve Azure 'da Linux sanal makinelerine (VM 'Ler) sertifikaların güvenli bir şekilde dağıtılmasına izin verebilir. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Azure Key Vault oluşturma
> * Sertifikaları oluşturma veya Key Vault’a yükleme
> * VM oluşturma ve NGINX web sunucusunu yükleme
> * Sertifikayı VM 'ye ekleme ve NGıNX 'i TLS bağlaması ile yapılandırma

Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](../../cloud-shell/overview.md)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme]( /cli/azure/install-azure-cli).


## <a name="overview"></a>Genel Bakış
Azure Key Vault, sertifikalar veya parolalar gibi şifreleme anahtarları ile gizli dizilerin güvenliğini sağlar. Key Vault, sertifika yönetimi işlemini kolaylaştırır ve bu sertifikalara erişen anahtarları denetiminizde tutmanıza olanak sağlar. Key Vault içinde otomatik olarak imzalanan bir sertifika oluşturabilir veya sahip olduğunuz güvenli bir sertifikayı karşıya yükleyebilirsiniz.

Oluşturulan sertifikaları içeren özel bir VM görüntüsü kullanmak yerine, sertifikaları çalışan bir VM’ye eklersiniz. Bu işlem, dağıtım sırasında web sunucusuna en güncel sertifikaların yüklenip yüklenmediğini kontrol eder. Ayrıca, bir sertifikayı yeniler veya değiştirirseniz yeni ve özel bir VM görüntüsü oluşturmanız da gerekmez. En güncel sertifikalar, siz ek VM oluşturdukça otomatik olarak eklenir. Bu işlem sırasında, sertifikalar Azure platformundan ayrılmaz veya bir betikte, komut satırı geçmişinde veya şablonda kullanıma sunulmaz.


## <a name="create-an-azure-key-vault"></a>Azure Key Vault oluşturma
Key Vault ve sertifikalarını oluşturabilmek için [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroupSecureWeb* adlı bir kaynak grubu oluşturur:

```azurecli-interactive 
az group create --name myResourceGroupSecureWeb --location eastus
```

Ardından, [az keyvault create](/cli/azure/keyvault) ile bir Key Vault oluşturun ve bu anahtarın VM dağıtırken kullanılmasını etkinleştirin. Her Key Vault için benzersiz bir ad gerekir ve tüm harfler küçük olmalıdır. *\<mykeyvault>* Aşağıdaki örnekte, kendi benzersiz Key Vault adınızla değiştirin:

```azurecli-interactive 
keyvault_name=<mykeyvault>
az keyvault create \
    --resource-group myResourceGroupSecureWeb \
    --name $keyvault_name \
    --enabled-for-deployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Sertifika oluşturma ve sertifikayı Key Vault’ta depolama
Üretim sırasında kullanım için, [az keyvault certificate import](/cli/azure/keyvault/certificate) komutunu kullanarak güvenilen bir sağlayıcı tarafından imzalanan geçerli bir sertifikayı içeri aktarmalısınız. Bu öğreticide, aşağıdaki örnekte varsayılan sertifika ilkesini kullanan [az keyvault certificate create](/cli/azure/keyvault/certificate) ile nasıl otomatik olarak imzalanan sertifika oluşturabileceğiniz gösterilmektedir:

```azurecli-interactive 
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy)"
```

### <a name="prepare-a-certificate-for-use-with-a-vm"></a>VM ile kullanım için sertifika hazırlama
VM oluşturma sürecinde sertifikayı kullanmak için, [az keyvault secret list-versions](/cli/azure/keyvault/secret) komutuyla sertifikanızın kimliğini alın. [az vm secret format](/cli/azure/vm/secret#az_vm_secret_format) komutuyla sertifikayı dönüştürün. Aşağıdaki örnekte, sonraki adımlarda kullanım kolaylığı sağlamak için bu komutların çıkışı değişkenlere atanmaktadır:

```azurecli-interactive 
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secrets "$secret" -g myResourceGroupSecureWeb --keyvault $keyvault_name)
```

### <a name="create-a-cloud-init-config-to-secure-nginx"></a>NGINX’in güvenliğini sağlamak için cloud-init yapılandırması oluşturma
[Cloud-init](https://cloudinit.readthedocs.io), Linux VM’sini ilk kez önyüklendiğinde özelleştirmeyi sağlayan, sık kullanılan bir yaklaşımdır. cloud-init’i paket yükleme, dosyalara yazma ve kullanıcılar ile güvenliği yapılandırma işlemleri için kullanabilirsiniz. cloud-init önyükleme işlemi sırasında çalışırken, yapılandırmanıza uygulayabileceğiniz ek adım veya gerekli aracı yoktur.

VM oluşturduğunuzda, sertifika ve anahtarlar korunan */var/lib/waagent/* dizininde depolanır. VM’ye sertifika ekleme ve web sunucusunu yapılandırma işlemlerini otomatikleştirmek için cloud-init’i kullanın. Bu örnekte, NGINX web sunucusunu yükleme ve yapılandırma işlemleri anlatılmaktadır. Aynı işlemleri Apache’yi yüklemek ve yapılandırmak için de kullanabilirsiniz. 

*cloud-init-web-server.txt* adlı bir dosya oluşturup aşağıdaki yapılandırmayı yapıştırın:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
      }
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
```

### <a name="create-a-secure-vm"></a>Güvenli VM oluşturma
Şimdi [az vm create](/cli/azure/vm) ile bir VM oluşturun. Bu sertifika verileri, `--secrets` parametresiyle Key Vault’tan eklenir. cloud-init yapılandırmasını `--custom-data` parametresiyle geçirirsiniz:

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-web-server.txt \
    --secrets "$vm_secret"
```

VM’nin oluşturulması, paketlerin yüklenmesi ve uygulamanın başlatılması birkaç dakika sürebilir. VM oluşturulduktan sonra, Azure CLI tarafından görüntülenen `publicIpAddress` değerini not edin. Bu adres, web tarayıcısında sitenize erişmek için kullanılır.

Güvenli web trafiğinin VM’nize erişmesine izin vermek için, [az vm open-port](/cli/azure/vm) komutuyla internette 443 numaralı bağlantı noktasını açın:

```azurecli-interactive 
az vm open-port \
    --resource-group myResourceGroupSecureWeb \
    --name myVM \
    --port 443
```


### <a name="test-the-secure-web-app"></a>Güvenli web uygulamasını sınama
Artık bir Web tarayıcısı açıp adres çubuğuna *https: \/ \/ \<publicIpAddress>* yazabilirsiniz. VM oluşturma işleminden kendi herkese açık IP adresinizi sağlayın. Otomatik olarak imzalanan sertifika kullanıyorsanız güvenlik uyarısını kabul edin:

![Web tarayıcısı güvenlik uyarısını kabul edin](./media/tutorial-secure-web-server/browser-warning.png)

Güvenli NGINX siteniz, sonra aşağıdaki örnekte olduğu gibi görüntülenir:

![Çalışan güvenli NGINX sitesini görüntüleme](./media/tutorial-secure-web-server/secured-nginx.png)


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure Key Vault depolanan bir TLS/SSL sertifikası ile bir NGıNX Web sunucusunun güvenliğini sağlayabilirsiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Azure Key Vault oluşturma
> * Sertifikaları oluşturma veya Key Vault’a yükleme
> * VM oluşturma ve NGINX web sunucusunu yükleme
> * Sertifikayı VM 'ye ekleme ve NGıNX 'i TLS bağlaması ile yapılandırma

Hazır sanal makine betik örneklerini görmek için bu bağlantıyı izleyin.

> [!div class="nextstepaction"]
> [Linux sanal makinesi betik örnekleri](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)