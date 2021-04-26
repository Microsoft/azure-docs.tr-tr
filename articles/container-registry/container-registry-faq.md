---
title: Sık sorulan sorular
description: Azure Container Registry hizmetiyle ilgili sık sorulan soruların yanıtları
author: sajayantony
ms.topic: article
ms.date: 03/15/2021
ms.author: sajaya
ms.openlocfilehash: e5c855675990d6fd3ec97b839539acd843016a7d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864712"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Azure Container Registry hakkında sık sorulan sorular

Bu makalede, Azure Container Registry hakkında sık sorulan sorular ve bilinen sorunlar ele alınmaktadır.

Kayıt defteri sorunlarını giderme kılavuzu için bkz.:
* [Kayıt defteri oturum açma sorunlarını giderme](container-registry-troubleshoot-login.md)
* [Kayıt defteri ile ağ sorunlarını giderme](container-registry-troubleshoot-access.md)
* [Kayıt defteri performansının sorunlarını giderme](container-registry-troubleshoot-performance.md)

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

Evet. [Azure Güvenlik Merkezi](../security-center/defender-for-container-registries-introduction.md), [twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) ve [deniz mavisi](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry)belgelerine bakın.

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

Azure PowerShell kullanma:

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
- [TLS 1,2 nasıl etkinleştirilir?](#how-to-enable-tls-12)
- [Azure Container Registry Içerik güvenini destekliyor mu?](#does-azure-container-registry-support-content-trust)
- [Kayıt defteri kaynağını yönetme izni olmadan çekme veya gönderme görüntülerine erişim izni Nasıl yaparım? mı?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Kayıt defteri için otomatik görüntü karantinasını etkinleştirmek Nasıl yaparım? mı?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Anonim çekme erişimini nasıl etkinleştirebilirim?](#how-do-i-enable-anonymous-pull-access)
- [Dağıtılabilir olmayan katmanları bir kayıt defterine itmi Nasıl yaparım??](#how-do-i-push-non-distributable-layers-to-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Docker kayıt defteri HTTP API v2 'ye Nasıl yaparım? erişin?

ACR, Docker kayıt defteri HTTP API v2 'YI destekler. API 'Lere ' de erişilebilir `https://<your registry login server>/v2/` . Örnek: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Bir depodaki herhangi bir etiket tarafından başvurulmayan tüm bildirimler silinsin mi Nasıl yaparım??

Bash kullanıyorsanız:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

PowerShell için:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Note: `-y` onayı atlamak için Sil komutuna ekleyebilirsiniz.

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

```azurecli
az acr show-usage -n myregistry
```

Azure CLı veya portalını kullanarak görüntüyü silin ve güncelleştirilmiş kullanımı birkaç dakika içinde denetleyin.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Nasıl yaparım? bir kapsayıcıda CLı çalıştırırken kayıt defterimde kimlik doğrulaması yapılsın mı?

Docker yuvasını bağlayarak Azure CLı kapsayıcısını çalıştırmanız gerekir:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

Kapsayıcıda şunu yüklemesi `docker` :

```bash
apk --update add docker
```

Ardından kayıt defterinizde kimlik doğrulaması yapın:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>TLS 1,2 nasıl etkinleştirilir?

Herhangi bir yeni Docker istemcisini (sürüm 18.03.0 ve üzeri) kullanarak TLS 1,2 'yi etkinleştirin. 

> [!IMPORTANT]
> Azure Container Registry 13 Ocak 2020 tarihinden itibaren, sunuculardan ve uygulamalardan gelen tüm güvenli bağlantıların TLS 1,2 kullanması gerekir. TLS 1,0 ve 1,1 desteği kullanımdan kaldırılacak.

### <a name="does-azure-container-registry-support-content-trust"></a>Azure Container Registry Içerik güvenini destekliyor mu?

Evet, [Docker noçi](https://docs.docker.com/notary/getting_started/) tümleşik olduğundan ve etkinleştirilemediğinden Azure Container Registry içindeki güvenilir görüntüleri kullanabilirsiniz. Ayrıntılar için bkz. [Azure Container Registry Içerik güveni](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Parmak izi dosyası nerede bulunur?

Altında `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata` :

* Tüm rollerin ortak anahtarları ve sertifikaları (atama rolleri hariç) içinde depolanır `root.json` .
* Ortak anahtarlar ve temsili rolünün sertifikaları, üst rolünün JSON dosyasında (örneğin `targets.json` , `targets/releases` rol için) depolanır.

Bu ortak anahtarların ve sertifikaların, Docker ve Nodown istemcisi tarafından gerçekleştirilen genel olarak doğrulanması sonrasında doğrulanması önerilir.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Kayıt defteri kaynağını yönetme izni olmadan çekme veya gönderme görüntülerine erişim izni Nasıl yaparım? mı?

ACR, farklı izin düzeyleri sağlayan [özel rolleri](container-registry-roles.md) destekler. Özellikle `AcrPull` ve `AcrPush` rolleri, kullanıcıların Azure 'daki kayıt defteri kaynağını yönetme izni olmadan resimleri çekmesini ve/veya gönderemelerine olanak tanır.

* Azure portal: kayıt defteriniz > Access Control (ıAM)-> Add (rol seçin `AcrPull` `AcrPush` ).
* Azure CLı: aşağıdaki komutu çalıştırarak kayıt defterinin kaynak KIMLIĞINI bulun:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Daha sonra `AcrPull` `AcrPush` bir kullanıcıya veya rolünü atayabilirsiniz (aşağıdaki örnekte kullanır `AcrPull` ):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Ya da rolü, uygulama KIMLIĞI tarafından tanımlanan bir hizmet sorumlusuna atayın:

  ```azurecli
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

* Bir görüntü çekmek için:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Yalnızca `AcrPull` veya `AcrPush` rolü kullanımıyla, atanan oturum, Azure 'da kayıt defteri kaynağını yönetme iznine sahip değildir. Örneğin, `az acr list` veya `az acr show -n myRegistry` kayıt defterini göstermez.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Kayıt defteri için otomatik görüntü karantinasını etkinleştirmek Nasıl yaparım? mı?

Görüntü karantina Şu anda ACR 'nin önizleme özelliğidir. Yalnızca güvenlik taramasını başarıyla geçen görüntülerin normal kullanıcılara görünür olması için bir kayıt defterinin karantina modunu etkinleştirebilirsiniz. Ayrıntılar için bkz. [ACR GitHub deposu](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Anonim çekme erişimini nasıl etkinleştirebilirim?

Anonim (kimliği doğrulanmamış) çekme erişimi için bir Azure Kapsayıcı kayıt defteri ayarlamak, standart ve Premium [Hizmet katmanlarında](container-registry-skus.md)kullanılabilen bir önizleme özelliğidir. 

Anonim çekme erişimini etkinleştirmek için Azure CLı (sürüm 2.21.0 veya üzeri) kullanarak bir kayıt defteri güncelleştirin ve `--anonymous-pull-enabled` parametreyi [az ACR Update](/cli/azure/acr#az_acr_update) komutuna geçirin:

```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

' İ ayarlayarak dilediğiniz zaman anonim çekme erişimini devre dışı bırakabilirsiniz `--anonymous-pull-enabled` `false` .

> [!NOTE]
> * Anonim çekme işlemini denemeden önce, `docker logout` tüm mevcut Docker kimlik bilgilerini temizlemenizi sağlamak için öğesini çalıştırın.
> * Yalnızca veri düzlemi işlemleri, kimliği doğrulanmamış istemciler tarafından kullanılabilir.
> * Kayıt defteri, yüksek oranda kimliği doğrulanmamış isteklerin azaledebilir.
> * Şu anda, [coğrafi olarak çoğaltılan](container-registry-geo-replication.md) kayıt defteri bölgelerinde anonim çekme erişimi desteklenmez.

> [!WARNING]
> Anonim çekme erişimi şu anda kayıt defterindeki tüm depolar için geçerlidir. Depoda [kapsamlı belirteçleri](container-registry-repository-scoped-permissions.md)kullanarak depo erişimini yönetiyorsanız, tüm kullanıcıların anonim çekme için etkinleştirilen bir kayıt defterindeki depolardan çekeceğini unutmayın. Anonim çekme erişimi etkinleştirildiğinde belirteçleri silmenizi öneririz.

### <a name="how-do-i-push-non-distributable-layers-to-a-registry"></a>Dağıtılabilir olmayan katmanları bir kayıt defterine itmi Nasıl yaparım??

Bir bildirimde dağıtılabilir olmayan bir katman, içeriğin getirileceği bir URL parametresi içerir. Dağıtılabilir katman gönderimleri etkinleştirmek için bazı olası kullanım durumları, ağ kısıtlı kayıt defterleri, kısıtlı erişime sahip AIR-gapped kayıt defterleri veya internet bağlantısı olmayan kayıt defterleri için kullanılır.

Örneğin, bir VM 'nin yalnızca Azure Container Kayıt defterinizden görüntü çekebilmesi için NSG kurallarınızı ayarladıysanız, Docker yabancı/dağıtılabilir olmayan katmanlar için hatalara sahip olur. Örneğin, bir Windows Server çekirdek görüntüsü, bildiriminde Azure Container Registry 'ye yabancı Katman başvuruları içerir ve bu senaryoya çekme işlemi başarısız olur.

Dağıtılabilir katmanların göndermeyi etkinleştirmek için:

1. `daemon.json` `/etc/docker/` Linux konaklarında ve Windows Server 'da bulunan dosyasını düzenleyin `C:\ProgramData\docker\config\daemon.json` . Dosyanın daha önce boş olduğunu varsayarsak, aşağıdaki içerikleri ekleyin:

   ```json
   {
     "allow-nondistributable-artifacts": ["myregistry.azurecr.io"]
   }
   ```
   > [!NOTE]
   > Değer, virgülle ayrılmış bir kayıt defteri adresleri dizisidir.

2. Dosyayı kaydedin ve kapatın.

3. Docker 'ı yeniden başlatın.

Görüntüleri listedeki kayıt defterlerine gönderdiğinizde, dağıtılabilir olmayan katmanlar kayıt defterine gönderilir.

> [!WARNING]
> Dağıtılabilir yapıtlar genellikle ve nerede dağıtılabilecekleri ve paylaşılabileceği konusunda kısıtlamalar vardır. Yapıtları yalnızca özel kayıt defterlerine göndermek için bu özelliği kullanın. Dağıtılabilir olmayan yapıtları yeniden dağıtımı kapsayan koşullarla uyumlu olduğunuzdan emin olun.

## <a name="diagnostics-and-health-checks"></a>Tanılama ve durum denetimleri

- [Sistem durumunu denetle `az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull hata vererek başarısız oldu: net/http: bağlantı beklenirken istek iptal edildi (üst bilgiler beklenirken Istemci. zaman aşımı aşıldı)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [Docker Push başarılı oldu, ancak docker pull hata vererek başarısız oluyor: kimlik doğrulaması gerekli](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` başarılı oldu, ancak Docker komutları hata vererek başarısız oluyor: kimlik doğrulaması gerekli](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Docker Daemon 'un hata ayıklama günlüklerini etkinleştirin ve alın](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [Yeni Kullanıcı izinleri güncelleştirmeden hemen sonra etkili olmayabilir](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [Kimlik doğrulama bilgileri doğrudan REST API çağrılarında doğru biçimde verilmez](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Azure portal neden tüm depolarımı veya etiketlerimi listelemez?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Azure portal depo veya etiket getirme neden başarısız oluyor?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Neden çekme veya gönderim isteği izin verilmeyen işlemle başarısız oluyor?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Depo biçimi geçersiz veya desteklenmiyor](#repository-format-is-invalid-or-unsupported)
- [Windows 'da http izlemeleri Nasıl yaparım? mi toplıyorsunuz?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Sistem durumunu denetle `az acr check-health`

Ortak ortam ve kayıt defteri sorunlarını gidermek için bkz. [Azure Container Registry 'nin sistem durumunu denetleme](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull hata vererek başarısız oldu: net/http: bağlantı beklenirken istek iptal edildi (üst bilgiler beklenirken Istemci. zaman aşımı aşıldı)

 - Bu hata geçici bir sorun ise, yeniden deneme başarılı olur.
 - `docker pull`Sürekli başarısız olursa Docker daemon ile ilgili bir sorun olabilir. Bu sorun genellikle Docker Daemon yeniden başlatılarak azaltılabilir. 
 - Docker Daemon 'ı yeniden başlattıktan sonra bu sorunu görmeye devam ederseniz, sorun makinede bazı ağ bağlantısı sorunları olabilir. Makinedeki genel ağın sağlıklı olup olmadığını denetlemek için uç nokta bağlantısını test etmek üzere aşağıdaki komutu çalıştırın. `az acr`Bu bağlantı denetimi komutunu içeren en düşük sürüm 2.2.9 ' dir. Daha eski bir sürüm kullanıyorsanız Azure CLı 'nizi yükseltin.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Tüm Docker istemci işlemlerinde her zaman bir yeniden deneme mekanizması olmalıdır.

### <a name="docker-pull-is-slow"></a>Docker çekme yavaş
Makine ağ indirme hızınızı sınamak için [Bu](http://www.azurespeed.com/Azure/Download) aracı kullanın. Makine ağı yavaşsa, kayıt defterinizdeki aynı bölgedeki Azure VM 'yi kullanmayı göz önünde bulundurun. Bu genellikle daha hızlı ağ hızına sahip olmanızı sağlar.

### <a name="docker-push-is-slow"></a>Docker Push yavaş
Makine ağ yükleme hızınızı sınamak için [Bu](http://www.azurespeed.com/Azure/Upload) aracı kullanın. Makine ağı yavaşsa, kayıt defterinizdeki aynı bölgedeki Azure VM 'yi kullanmayı göz önünde bulundurun. Bu genellikle daha hızlı ağ hızına sahip olmanızı sağlar.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker Push başarılı oldu, ancak docker pull hata vererek başarısız oluyor: kimlik doğrulaması gerekli

Bu hata, varsayılan olarak etkin olan Docker Daemon 'ın Red Hat sürümünde ortaya çıkabilir `--signature-verification` . Aşağıdaki komutu çalıştırarak Red Hat Enterprise Linux (RHEL) veya Fedora için Docker Daemon seçeneklerini kontrol edebilirsiniz:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Örneğin, Fedora 28 sunucusu aşağıdaki Docker Daemon seçeneklerine sahiptir:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

`--signature-verification=false`Eksik ile `docker pull` Şuna benzer bir hata ile başarısız olur:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Hatayı gidermek için:
1. `--signature-verification=false`Docker Daemon yapılandırma dosyasına seçeneğini ekleyin `/etc/sysconfig/docker` . Örnek:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Aşağıdaki komutu çalıştırarak Docker Daemon hizmetini yeniden başlatın:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Ayrıntıları `--signature-verification` çalıştırılarak bulunabilir `man dockerd` .

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az ACR oturum açma işlemi başarılı ancak Docker şu hatayla başarısız oldu: kimlik doğrulaması gerekli

Örneğin, `docker push myregistry.azurecr.io/myimage:latest` kayıt defteri kaynak adı büyük harfle veya karışık durumda olsa bile, tüm küçük harfli sunucu URL 'sini kullandığınızdan emin olun `myRegistry` .

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Docker Daemon 'un hata ayıklama günlüklerini etkinleştirin ve alın    

`dockerd` `debug` Seçeneğiyle başlayın. İlk olarak, mevcut değilse Docker Daemon yapılandırma dosyasını ( `/etc/docker/daemon.json` ) oluşturun ve şu `debug` seçeneği ekleyin:

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

 * Günlükler, sisteminize bağlı olarak farklı konumlarda oluşturulabilir. Örneğin, Ubuntu 14,04 için bu `/var/log/upstart/docker.log` .    
Ayrıntılar için bkz. [Docker belgeleri](https://docs.docker.com/engine/admin/#read-the-logs) .    

 * Docker for Windows için Günlükler% LOCALAPPDATA%/Docker/altında oluşturulur. Ancak, henüz tüm hata ayıklama bilgilerini içermeyebilir.    

   Tüm Daemon günlüğüne erişmek için bazı ek adımlara ihtiyacınız olabilir:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Şimdi, çalıştıran VM 'nin tüm dosyalarına erişiminiz var `dockerd` . Günlük `/var/log/docker.log` .

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>Yeni Kullanıcı izinleri güncelleştirmeden hemen sonra etkili olmayabilir

Bir hizmet sorumlusuna yeni izinler (yeni roller) verdiğinizde, değişiklik hemen etkili olmayabilir. Olası iki neden vardır:

* Rol atama gecikmesi Azure Active Directory. Normalde hızlıdır, ancak yayma gecikmesi nedeniyle birkaç dakika sürebilir.
* ACR belirteç sunucusu 'nda izin gecikmesi. Bu işlem 10 dakikaya kadar sürebilir. Azaltmak için, `docker logout` 1 dakika sonra aynı kullanıcıyla yeniden kimlik doğrulaması yapabilir ve sonra tekrar kimlik doğrulaması yapabilirsiniz:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Şu anda ACR, kullanıcılar tarafından giriş çoğaltmasını silmeyi desteklemez. Geçici çözüm, ana çoğaltma oluşturma işlemini şablona eklemek, ancak `"condition": false` aşağıda gösterildiği gibi ekleyerek oluşturmayı atlar:

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

`InvalidAuthenticationInfo`Özellikle, `curl` `-L` `--location` (yeniden yönlendirmeleri izlemek için) aracıyla birlikte kullanarak bir hatayla karşılaşabilirsiniz.
Örneğin, `curl` WITH `-L` seçeneği ve temel kimlik doğrulaması kullanarak blobu getirme:

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

Kök nedeni, bazı `curl` uygulamaların özgün istekten gelen üstbilgilere yeniden yönlendirmeleri takip eden bir nedendir.

Sorunu çözmek için, başlıkları olmadan yeniden yönlendirmeleri el ile izlemeniz gerekir. Yanıt üst bilgilerini `-D -` `curl` ' i seçeneğiyle yazdırın ve sonra ayıklayın: `Location`

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Azure portal neden tüm depolarımı veya etiketlerimi listelemez? 

Microsoft Edge/IE tarayıcısı kullanıyorsanız, en fazla 100 depo veya etiket görebilirsiniz. Kayıt defterinizde 100 ' den fazla depo veya etiket varsa, bunları listelemek için Firefox veya Chrome tarayıcısı kullanmanızı öneririz.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Azure portal depo veya etiket getirme neden başarısız oluyor?

Tarayıcı, sunucuya depo veya etiket getirme isteğini gönderemeyebilir. Şöyle çeşitli nedenlerle şunlar olabilir:

* Ağ bağlantısı olmaması
* Güvenlik Duvarı
* Ad engelleyiciler
* DNS hataları

Lütfen ağ yöneticinize başvurun veya ağ yapılandırmanızı ve bağlantınızı kontrol edin. `az acr check-health -n yourRegistry`Ortamınızın Container Registry bağlanıp bağlanamadıysanız emin olmak Için Azure CLI 'nizi kullanarak çalıştırmayı deneyin. Ayrıca, eski tarayıcı önbelleğinizi veya tanımlama bilgilerini önlemek için tarayıcınızda bir veya özel oturum da deneyebilirsiniz.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Neden çekme veya gönderim isteği izin verilmeyen işlemle başarısız oluyor?

İşlemlere izin verilmeyen bazı senaryolar aşağıda verilmiştir:
* Klasik kayıt defterleri artık desteklenmiyor. Lütfen [az ACR Update](/cli/azure/acr#az_acr_update) veya Azure Portal kullanarak desteklenen bir [hizmet katmanına](./container-registry-skus.md) yükseltin.
* Resim veya depo silinemeyebilir veya güncelleştirilemeyebilir. Geçerli öznitelikleri görüntülemek için [az ACR Show Repository](./container-registry-image-lock.md) komutunu kullanabilirsiniz.
* Görüntü karantinaya alındı ise bazı işlemlere izin verilmez. [Karantina](https://github.com/Azure/acr/tree/master/docs/preview/quarantine)hakkında daha fazla bilgi edinin.
* Kayıt defteriniz [depolama sınırına](container-registry-skus.md#service-tier-features-and-limits)ulaşmış olabilir.

### <a name="repository-format-is-invalid-or-unsupported"></a>Depo biçimi geçersiz veya desteklenmiyor

"Desteklenmeyen depo biçimi", "geçersiz biçim" veya "istenen veriler yok" gibi bir hata görürseniz, depo işlemlerinde bir depo adı belirtirken, adın yazımını ve durumunu denetleyin. Geçerli depo adları yalnızca küçük harfli alfasayısal karakterler, noktalar, tireler, alt çizgiler ve eğik çizgi içerebilir. 

Tüm depo adlandırma kuralları için bkz. [kapsayıcı girişim dağıtım belirtimi](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Windows 'da http izlemeleri Nasıl yaparım? mi toplıyorsunuz?

#### <a name="prerequisites"></a>Önkoşullar

- Fiddler 'da https şifresini çözmeyi etkinleştir:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
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

Komutuna bir yerel kaynak klasörü geçirirseniz `az acr build` , `.git` klasör varsayılan olarak karşıya yüklenen paketten çıkarılır. `.dockerignore`Aşağıdaki ayarla bir dosya oluşturabilirsiniz. Komuta karşıya yüklenen paketteki tüm dosyaları geri yüklemesini söyler `.git` . 

`!.git/**`

Bu ayar komut için de geçerlidir `az acr run` .

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>Görevler kaynak Tetikleyicileri için GitLab destekliyor mu?

Şu anda kaynak Tetikleyicileri için GitLab 'yi desteklemiyoruz.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Görevler hangi git depo yönetimi hizmetini destekler?

| Git hizmeti | Kaynak bağlamı | El ile derleme | Tamamlama tetikleyicisi aracılığıyla otomatik derleme |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Evet | Yes |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Yes | Yes |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Yes | Hayır |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Yes | Hayır |

## <a name="run-error-message-troubleshooting"></a>Çalıştırma hata Iletisi sorun giderme

| Hata iletisi | Sorun giderme kılavuzu |
|---|---|
|VM için erişim yapılandırılmadı, bu nedenle abonelik bulunamadı|ACR görevinde kullanıyorsanız bu durum oluşabilir `az login --identity` . Bu geçici bir hatadır ve yönetilen kimliğinizin rol ataması yayılmamışsa oluşur. Yeniden denemeden önce birkaç saniye bekleniyor.|

## <a name="cicd-integration"></a>CI/CD tümleştirmesi

- [Circlecı](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Sonraki adımlar

* Azure Container Registry hakkında [daha fazla bilgi edinin](container-registry-intro.md) .
