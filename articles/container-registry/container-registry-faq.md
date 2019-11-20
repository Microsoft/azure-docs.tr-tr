---
title: Azure Container Registry-sık sorulan sorular
description: Azure Container Registry hizmetiyle ilgili sık sorulan soruların yanıtları
services: container-registry
author: sajayantony
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: 88c4b2065576bd5bdcb29a266bd564c60b0e537c
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622699"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Azure Container Registry hakkında sık sorulan sorular

Bu makalede, Azure Container Registry hakkında sık sorulan sorular ve bilinen sorunlar ele alınmaktadır.

## <a name="resource-management"></a>Kaynak yönetimi

- [Kaynak Yöneticisi şablonu kullanarak Azure Container Registry oluşturabilir miyim?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [ACR 'deki görüntüleri taramak için güvenlik güvenlik açığı var mı?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Kubernetes Azure Container Registry yapılandırma Nasıl yaparım??](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Bir kapsayıcı kayıt defteri için yönetici kimlik bilgilerini almak Nasıl yaparım??](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Yönetici kimlik bilgilerini bir Kaynak Yöneticisi şablonunda Nasıl yaparım? mi?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [Çoğaltma, Azure CLı veya Azure PowerShell kullanılarak silinmese de, çoğaltmanın silinmesi yasak durumuyla başarısız oluyor](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [Güvenlik duvarı kuralları başarıyla güncelleştirildi, ancak bunlar etkili olmayacak](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak bir Azure Container Registry oluşturabilir miyim?

Evet. Kayıt defteri oluşturmak için kullanabileceğiniz [bir şablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) aşağıda verilmiştir.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>ACR 'deki görüntüleri taramak için güvenlik güvenlik açığı var mı?

Evet. [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) ve [deniz mavisi](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)belgelerine bakın.

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Kubernetes Azure Container Registry yapılandırma Nasıl yaparım??

[Azure Kubernetes hizmeti](../aks/cluster-container-registry-integration.md)Için [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) ve adımlar belgelerine bakın.

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Bir kapsayıcı kayıt defteri için yönetici kimlik bilgilerini almak Nasıl yaparım??

> [!IMPORTANT]
> Yönetici Kullanıcı hesabı, tek bir kullanıcı için genellikle test amacıyla kayıt defterine erişmek üzere tasarlanmıştır. Yönetici hesabı kimlik bilgilerini birden çok kullanıcıyla paylaşmayı önermiyoruz. Gözetimsiz senaryolara yönelik kullanıcılar ve hizmet sorumluları için bireysel kimlik önerilir. Bkz. [kimlik doğrulamasına genel bakış](container-registry-authentication.md).

Yönetici kimlik bilgilerini almadan önce, kayıt defterinin yönetici kullanıcısının etkinleştirildiğinden emin olun.

Azure CLı kullanarak kimlik bilgilerini almak için:

```azurecli
az acr credential show -n myRegistry
```

Azure PowerShell 'i kullanma:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Yönetici kimlik bilgilerini bir Kaynak Yöneticisi şablonunda Nasıl yaparım? mi?

> [!IMPORTANT]
> Yönetici Kullanıcı hesabı, tek bir kullanıcı için genellikle test amacıyla kayıt defterine erişmek üzere tasarlanmıştır. Yönetici hesabı kimlik bilgilerini birden çok kullanıcıyla paylaşmayı önermiyoruz. Gözetimsiz senaryolara yönelik kullanıcılar ve hizmet sorumluları için bireysel kimlik önerilir. Bkz. [kimlik doğrulamasına genel bakış](container-registry-authentication.md).

Yönetici kimlik bilgilerini almadan önce, kayıt defterinin yönetici kullanıcısının etkinleştirildiğinden emin olun.

İlk parolayı almak için:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

İkinci parolayı almak için:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>Çoğaltma, Azure CLı veya Azure PowerShell kullanılarak silinmese de, çoğaltmanın silinmesi yasak durumuyla başarısız oluyor

Kullanıcı bir kayıt defteri üzerinde izinlere sahip olduğunda ancak abonelik üzerinde okuyucu düzeyi izinlere sahip olmadığında hata görülür. Bu sorunu çözmek için, kullanıcıya abonelik üzerinde okuyucu izinleri atayın:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>Güvenlik duvarı kuralları başarıyla güncelleştirildi, ancak bunlar etkili olmayacak

Güvenlik duvarı kuralı değişikliklerinin yayılması biraz zaman alır. Güvenlik Duvarı ayarlarını değiştirdikten sonra bu değişikliği doğrulamadan önce lütfen birkaç dakika bekleyin.


## <a name="registry-operations"></a>Kayıt defteri işlemleri

- [Docker kayıt defteri HTTP API v2 'ye Nasıl yaparım? erişin?](#how-do-i-access-docker-registry-http-api-v2)
- [Bir depodaki herhangi bir etiket tarafından başvurulmayan tüm bildirimler silinsin mi Nasıl yaparım??](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Görüntüleri sildikten sonra kayıt defteri kotası kullanımı neden Azaltılmıyor?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Depolama kotası değişikliklerini doğrulamak Nasıl yaparım? mı?](#how-do-i-validate-storage-quota-changes)
- [Nasıl yaparım? bir kapsayıcıda CLı çalıştırırken kayıt defterimde kimlik doğrulaması yapılsın mı?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Azure Container Registry yalnızca TLS v 1.2 yapılandırmasını ve TLS v 1.2 'yi etkinleştirmeyi sağlar mi?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [Azure Container Registry Içerik güvenini destekliyor mu?](#does-azure-container-registry-support-content-trust)
- [Kayıt defteri kaynağını yönetme izni olmadan çekme veya gönderme görüntülerine erişim izni Nasıl yaparım? mı?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Kayıt defteri için otomatik görüntü karantinasını etkinleştirmek Nasıl yaparım?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Docker kayıt defteri HTTP API v2 'ye Nasıl yaparım? erişin?

ACR, Docker kayıt defteri HTTP API v2 'YI destekler. API 'Lerine `https://<your registry login server>/v2/`adresinden erişilebilir. Örnek: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Bir depodaki herhangi bir etiket tarafından başvurulmayan tüm bildirimler silinsin mi Nasıl yaparım??

Bash kullanıyorsanız:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

PowerShell için:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Note: onayı atlamak için Sil komutuna `-y` ekleyebilirsiniz.

Daha fazla bilgi için bkz. [Azure Container Registry kapsayıcı görüntülerini silme](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Görüntüleri sildikten sonra kayıt defteri kotası kullanımı neden Azaltılmıyor?

Bu durum, temeldeki katmanlara başka kapsayıcı görüntüleri tarafından hala başvuruluyorsa meydana gelebilir. Başvuruları olmayan bir görüntüyü silerseniz, kayıt defteri kullanımı birkaç dakika içinde güncelleştirilir.

### <a name="how-do-i-validate-storage-quota-changes"></a>Depolama kotası değişikliklerini doğrulamak Nasıl yaparım? mı?

Aşağıdaki Docker dosyasını kullanarak 1GB bir katman içeren bir görüntü oluşturun. Bu, görüntüde kayıt defterindeki başka bir görüntü tarafından paylaşılmayan bir katman olmasını sağlar.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Docker CLı kullanarak görüntüyü derleyin ve Kayıt defterinize gönderin.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Depolama kullanımının Azure portal arttığını veya CLı kullanarak kullanımı sorgulama yapabilmesini sağlayabilirsiniz.

```bash
az acr show-usage -n myregistry
```

Azure CLı veya portalını kullanarak görüntüyü silin ve güncelleştirilmiş kullanımı birkaç dakika içinde denetleyin.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Nasıl yaparım? bir kapsayıcıda CLı çalıştırırken kayıt defterimde kimlik doğrulaması yapılsın mı?

Docker yuvasını bağlayarak Azure CLı kapsayıcısını çalıştırmanız gerekir:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Kapsayıcıda `docker`' yi:

```bash
apk --update add docker
```

Ardından kayıt defterinizde kimlik doğrulaması yapın:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>Azure Container Registry yalnızca TLS v 1.2 yapılandırmasını ve TLS v 1.2 'yi etkinleştirmeyi sağlar mi?

Evet. Herhangi bir son Docker istemcisini (sürüm 18.03.0 ve üzeri) kullanarak TLS 'yi etkinleştirin. 

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry, İçerik Güveni'ni destekler mi?

Evet, [Docker noçi](https://docs.docker.com/notary/getting_started/) tümleşik olduğundan ve etkinleştirilemediğinden Azure Container Registry içindeki güvenilir görüntüleri kullanabilirsiniz. Ayrıntılar için bkz. [Azure Container Registry Içerik güveni](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Parmak izi dosyası nerede bulunur?

`~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`altında:

* Tüm rollerin ortak anahtarları ve sertifikaları (temsili roller hariç) `root.json`depolanır.
* Ortak anahtarlar ve temsili rolünün sertifikaları, üst rolünün JSON dosyasında depolanır (örneğin, `targets/releases` rolü için `targets.json`).

Bu ortak anahtarların ve sertifikaların, Docker ve Nodown istemcisi tarafından gerçekleştirilen genel olarak doğrulanması sonrasında doğrulanması önerilir.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Kayıt defteri kaynağını yönetme izni olmadan çekme veya gönderme görüntülerine erişim izni Nasıl yaparım? mı?

ACR, farklı izin düzeyleri sağlayan [özel rolleri](container-registry-roles.md) destekler. Özellikle, `AcrPull` ve `AcrPush` rolleri, kullanıcıların Azure 'daki kayıt defteri kaynağını yönetme izni olmadan resimleri çekmesini ve/veya gönderemelerine olanak tanır.

* Azure portal: kayıt defteriniz > Access Control (ıAM)-> Add (rol için `AcrPull` veya `AcrPush` seçin).
* Azure CLı: aşağıdaki komutu çalıştırarak kayıt defterinin kaynak KIMLIĞINI bulun:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Daha sonra, `AcrPull` veya `AcrPush` rolünü bir kullanıcıya atayabilirsiniz (Aşağıdaki örnek `AcrPull`kullanır):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Ya da rolü, uygulama KIMLIĞI tarafından tanımlanan bir hizmet ilkesine atayın:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

Atanan e-, daha sonra kayıt defterindeki görüntülere kimlik doğrulaması yapabilir ve bu görüntüleri erişebilir.

* Kayıt defterine kimlik doğrulaması yapmak için:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Depoları listelemek için:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Bir görüntü çekmek için:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

Yalnızca `AcrPull` veya `AcrPush` rolü kullanımıyla, atanan oturum, Azure 'da kayıt defteri kaynağını yönetme iznine sahip değildir. Örneğin, `az acr list` veya `az acr show -n myRegistry` kayıt defterini göstermez.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Kayıt defteri için otomatik görüntü karantinasını etkinleştirmek Nasıl yaparım? mı?

Görüntü karantina Şu anda ACR 'nin önizleme özelliğidir. Yalnızca güvenlik taramasını başarıyla geçen görüntülerin normal kullanıcılara görünür olması için bir kayıt defterinin karantina modunu etkinleştirebilirsiniz. Ayrıntılar için bkz. [ACR GitHub deposu](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics-and-health-checks"></a>Tanılama ve durum denetimleri

- [`az acr check-health` ile sistem durumunu denetleme](#check-health-with-az-acr-check-health)
- [docker pull hata vererek başarısız oldu: net/http: bağlantı beklenirken istek iptal edildi (üst bilgiler beklenirken Istemci. zaman aşımı aşıldı)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [Docker Push başarılı oldu, ancak docker pull hata vererek başarısız oluyor: kimlik doğrulaması gerekli](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Docker Daemon 'un hata ayıklama günlüklerini etkinleştirin ve alın](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [Yeni Kullanıcı izinleri güncelleştirmeden hemen sonra etkili olmayabilir](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Kimlik doğrulama bilgileri doğrudan REST API çağrılarında doğru biçimde verilmez](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Azure portal neden tüm depolarımı veya etiketlerimi listelemez?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Azure portal depo veya etiket getirme neden başarısız oluyor?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Windows 'da http izlemeleri Nasıl yaparım? mi toplıyorsunuz?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>`az acr check-health` ile sistem durumunu denetleme

Ortak ortam ve kayıt defteri sorunlarını gidermek için bkz. [Azure Container Registry 'nin sistem durumunu denetleme](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull hata vererek başarısız oldu: net/http: bağlantı beklenirken istek iptal edildi (üst bilgiler beklenirken Istemci. zaman aşımı aşıldı)

 - Bu hata geçici bir sorun ise, yeniden deneme başarılı olur.
 - `docker pull` sürekli olarak başarısız olursa Docker daemon ile ilgili bir sorun olabilir. Bu sorun genellikle Docker Daemon yeniden başlatılarak azaltılabilir. 
 - Docker Daemon 'ı yeniden başlattıktan sonra bu sorunu görmeye devam ederseniz, sorun makinede bazı ağ bağlantısı sorunları olabilir. Makinedeki genel ağın sağlıklı olup olmadığını denetlemek için uç nokta bağlantısını test etmek üzere aşağıdaki komutu çalıştırın. Bu bağlantı denetimi komutunu içeren en düşük `az acr` sürümü 2.2.9 ' dir. Daha eski bir sürüm kullanıyorsanız Azure CLı 'nizi yükseltin.
 
   ```azurecli
    az acr check-health -n myRegistry
    ```
 - Tüm Docker istemci işlemlerinde her zaman bir yeniden deneme mekanizması olmalıdır.

### <a name="docker-pull-is-slow"></a>Docker çekme yavaş
Makine ağ indirme hızınızı sınamak için [Bu](http://www.azurespeed.com/Azure/Download) aracı kullanın. Makine ağı yavaşsa, kayıt defterinizdeki aynı bölgedeki Azure VM 'yi kullanmayı göz önünde bulundurun. Bu genellikle daha hızlı ağ hızına sahip olmanızı sağlar.

### <a name="docker-push-is-slow"></a>Docker Push yavaş
Makine ağ yükleme hızınızı sınamak için [Bu](http://www.azurespeed.com/Azure/Upload) aracı kullanın. Makine ağı yavaşsa, kayıt defterinizdeki aynı bölgedeki Azure VM 'yi kullanmayı göz önünde bulundurun. Bu genellikle daha hızlı ağ hızına sahip olmanızı sağlar.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker Push başarılı oldu, ancak docker pull hata vererek başarısız oluyor: kimlik doğrulaması gerekli

Bu hata, `--signature-verification` varsayılan olarak etkin olan Docker Daemon 'ın Red Hat sürümünde ortaya çıkabilir. Aşağıdaki komutu çalıştırarak Red Hat Enterprise Linux (RHEL) veya Fedora için Docker Daemon seçeneklerini kontrol edebilirsiniz:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Örneğin, Fedora 28 sunucusu aşağıdaki Docker Daemon seçeneklerine sahiptir:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

`--signature-verification=false` eksik olan `docker pull` şuna benzer bir hata ile başarısız olur:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Hatayı gidermek için:
1. `--signature-verification=false` seçeneğini Docker Daemon yapılandırma dosyasına `/etc/sysconfig/docker`ekleyin. Örneğin:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Aşağıdaki komutu çalıştırarak Docker Daemon hizmetini yeniden başlatın:

  ```bash
  sudo systemctl restart docker.service
  ```

`--signature-verification` ayrıntıları, `man dockerd`çalıştırılarak bulunabilir.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Docker Daemon 'un hata ayıklama günlüklerini etkinleştirin ve alın  

`debug` seçeneğiyle `dockerd` başlatın. İlk olarak, mevcut değilse Docker Daemon yapılandırma dosyasını (`/etc/docker/daemon.json`) oluşturun ve `debug` seçeneğini ekleyin:

```json
{   
    "debug": true   
}
```

Sonra, arka plan programını yeniden başlatın. Örneğin, Ubuntu 14,04:

```bash
sudo service docker restart
```

Ayrıntılar [Docker belgelerinde](https://docs.docker.com/engine/admin/#enable-debugging)bulunabilir. 

 * Günlükler, sisteminize bağlı olarak farklı konumlarda oluşturulabilir. Örneğin, Ubuntu 14,04 için `/var/log/upstart/docker.log`.   
Ayrıntılar için bkz. [Docker belgeleri](https://docs.docker.com/engine/admin/#read-the-logs) .    

 * Docker for Windows için Günlükler% LOCALAPPDATA%/Docker/altında oluşturulur. Ancak, henüz tüm hata ayıklama bilgilerini içermeyebilir.   

   Tüm Daemon günlüğüne erişmek için bazı ek adımlara ihtiyacınız olabilir:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Artık `dockerd`çalıştıran VM 'nin tüm dosyalarına erişiminiz var. Günlük `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Yeni Kullanıcı izinleri güncelleştirmeden hemen sonra etkili olmayabilir

Bir hizmet sorumlusuna yeni izinler (yeni roller) verdiğinizde, değişiklik hemen etkili olmayabilir. Olası iki neden vardır:

* Rol atama gecikmesi Azure Active Directory. Normalde hızlıdır, ancak yayma gecikmesi nedeniyle birkaç dakika sürebilir.
* ACR belirteç sunucusu 'nda izin gecikmesi. Bu işlem 10 dakikaya kadar sürebilir. Azaltmak için, 1 dakika sonra aynı kullanıcıyla `docker logout` ve sonra yeniden kimlik doğrulaması yapabilirsiniz:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Şu anda ACR, kullanıcılar tarafından giriş çoğaltmasını silmeyi desteklemez. Geçici çözüm, ana çoğaltma oluşturma işlemini şablona eklemek, ancak aşağıda gösterildiği gibi `"condition": false` ekleyerek oluşturmayı atlar:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>Kimlik doğrulama bilgileri doğrudan REST API çağrılarında doğru biçimde verilmez

Özellikle `curl` aracı `-L`, `--location` (yeniden yönlendirmeleri izlemek için) ile birlikte `InvalidAuthenticationInfo` bir hatayla karşılaşabilirsiniz.
Örneğin, `-L` seçeneği ve temel kimlik doğrulaması ile `curl` kullanarak blobu getirme:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

Aşağıdaki yanıta neden olabilir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

Kök nedeni, bazı `curl` uygulamalarının özgün istekteki üst bilgilerle yeniden yönlendirmeleri takip eden bir nedendir.

Sorunu çözmek için, başlıkları olmadan yeniden yönlendirmeleri el ile izlemeniz gerekir. Yanıt üst bilgilerini `curl` `-D -` seçeneğiyle yazdırın ve sonra ayıklayın: `Location` üst bilgisi:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Azure portal neden tüm depolarımı veya etiketlerimi listelemez? 

Microsoft Edge/IE tarayıcısı kullanıyorsanız, en fazla 100 depo veya etiket görebilirsiniz. Kayıt defterinizde 100 ' den fazla depo veya etiket varsa, bunları listelemek için Firefox veya Chrome tarayıcısı kullanmanızı öneririz.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Azure portal depo veya etiket getirme neden başarısız oluyor?

Tarayıcı, sunucuya depo veya etiket getirme isteğini gönderemeyebilir. Şöyle çeşitli nedenlerle şunlar olabilir:

* Ağ bağlantısı olmaması
* Güvenlik duvarı
* Ad engelleyiciler
* DNS hataları

Lütfen ağ yöneticinize başvurun veya ağ yapılandırmanızı ve bağlantınızı kontrol edin. Ortamınızın Container Registry bağlanabiliyor olup olmadığını denetlemek için Azure CLı 'nizi kullanarak `az acr check-health -n yourRegistry` çalıştırmayı deneyin. Ayrıca, eski tarayıcı önbelleğinizi veya tanımlama bilgilerini önlemek için tarayıcınızda bir veya özel oturum da deneyebilirsiniz.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Windows 'da http izlemeleri Nasıl yaparım? mi toplıyorsunuz?

#### <a name="prerequisites"></a>Ön koşullar

- Fiddler 'da https şifresini çözmeyi etkinleştir: <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Docker Kullanıcı arabirimi aracılığıyla bir proxy kullanmak için Docker 'ı etkinleştirin: <https://docs.docker.com/docker-for-windows/#proxies>
- Tamamlandığında ' i döndürtığınızdan emin olun.  Docker bu etkin ve Fiddler çalışmadığı için çalışmaz.

#### <a name="windows-containers"></a>Windows kapsayıcıları

Docker proxy 'yi 127.0.0.1:8888 olarak yapılandırma

#### <a name="linux-containers"></a>Linux kapsayıcıları

Docker VM sanal anahtarının IP 'sini bul:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Docker proxy 'yi önceki komutun ve 8888 bağlantı noktasının (örneğin, 10.0.75.1:8888) çıktısını almak için yapılandırın

## <a name="tasks"></a>Görevler

- [Batch iptal Nasıl yaparım? çalıştırmaları mı?](#how-do-i-batch-cancel-runs)
- [Az ACR Build komutuna. git klasörünü dahil Nasıl yaparım?.](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [Görevler kaynak Tetikleyicileri için GitLab destekliyor mu?](#does-tasks-support-gitlab-for-source-triggers)
- [Görevler hangi git depo yönetimi hizmetini destekler?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Batch iptal Nasıl yaparım? çalıştırmaları mı?

Aşağıdaki komutlar, belirtilen kayıt defterindeki tüm çalışan görevleri iptal eder.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Az ACR Build komutuna. git klasörünü dahil Nasıl yaparım?.

`az acr build` komutuna yerel bir kaynak klasör geçirirseniz, `.git` klasörü varsayılan olarak karşıya yüklenen paketten çıkarılır. Aşağıdaki ayarla bir `.dockerignore` dosyası oluşturabilirsiniz. Komuta karşıya yüklenen pakette `.git` altındaki tüm dosyaları geri yüklemesini söyler. 

```sh
!.git/**
```

Bu ayar `az acr run` komutu için de geçerlidir.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Görevler kaynak Tetikleyicileri için GitLab destekliyor mu?

Şu anda kaynak Tetikleyicileri için GitLab 'yi desteklemiyoruz.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Görevler hangi git depo yönetimi hizmetini destekler?

| Git hizmeti | Kaynak bağlamı | El ile derleme | Tamamlama tetikleyicisi aracılığıyla otomatik derleme |
|---|---|---|---|
| GitHub | https://github.com/user/myapp-repo.git#mybranch:myfolder | Evet | Evet |
| Azure Repos | https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder | Evet | Evet |
| GitLab | https://gitlab.com/user/myapp-repo.git#mybranch:myfolder | Evet | Hayır |
| BitBucket | https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder | Evet | Hayır |

## <a name="run-error-message-troubleshooting"></a>Çalıştırma hata Iletisi sorun giderme

| Hata iletisi | Sorun giderme kılavuzu |
|---|---|
|VM için erişim yapılandırılmadı, bu nedenle abonelik bulunamadı|ACR görevinde `az login --identity` kullanıyorsanız bu durum oluşabilir. Bu geçici bir hatadır ve yönetilen kimliğinizin rol ataması yayılmamışsa oluşur. Yeniden denemeden önce birkaç saniye bekleniyor.|

## <a name="cicd-integration"></a>CI/CD tümleştirmesi

- [Circlecı](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub eylemleri](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Sonraki adımlar

* Azure Container Registry hakkında [daha fazla bilgi edinin](container-registry-intro.md) .
