---
title: Docker Run komutunun kapsayıcı örneğini Çalıştır
titleSuffix: Azure Cognitive Services
description: Sistem durumu kapsayıcısı için Docker Run komutu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: 3a89ddbc17eab683c658430a64447328ee024a3c
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089719"
---
## <a name="install-the-container"></a>Kapsayıcıyı yükler

Sistem durumu kapsayıcısı için Metin Analizi yükleyip çalıştırabilmeniz için birden çok yol vardır. 

- Bir Metin Analizi kaynağı oluşturmak için [Azure Portal](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) kullanın ve kapsayıcınızı almak Için Docker 'ı kullanın.
- Kaynak dağıtımı ve kapsayıcı yapılandırmasını otomatikleştirmek için aşağıdaki PowerShell ve Azure CLı betikleri kullanın.

### <a name="run-the-container-locally"></a>Kapsayıcıyı yerel olarak çalıştırma

Kapsayıcıyı kapsayıcı görüntüsünü indirdikten sonra kendi ortamınızda çalıştırmak için görüntü KIMLIĞINI bulun:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Aşağıdaki komutu yürütün `docker run` . Aşağıdaki yer tutucuları kendi değerlerinizle değiştirin:

| Yer tutucu | Değer | Biçim veya örnek |
|-------------|-------|---|
| **{API_KEY}** | Metin Analizi kaynağınız için anahtar. Bunu, kaynağın **anahtar ve uç nokta** sayfasında, Azure Portal bulabilirsiniz. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Metin Analizi API'si erişmek için uç nokta. Bunu, kaynağın **anahtar ve uç nokta** sayfasında, Azure Portal bulabilirsiniz. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | Kapsayıcının görüntü KIMLIĞI. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | Kapsayıcının giriş dizini. | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
rai_terms=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Şu komut:

- Giriş dizininin konak makinede bulunduğunu varsayar
- Kapsayıcı görüntüsünden sistem durumu kapsayıcısı için bir Metin Analizi çalıştırır
- 6 CPU çekirdeği ve 12 gigabayt (GB) bellek ayırır
- TCP bağlantı noktası 5000 ' i kullanıma sunar ve kapsayıcı için bir sözde TTY ayırır
- Son Kullanıcı Lisans Sözleşmesi (EULA) ve sorumlu AI (çı) şartlarını kabul eder
- Kapsayıcıyı çıktıktan sonra otomatik olarak kaldırır. Kapsayıcı görüntüsü hala ana bilgisayarda kullanılabilir.

### <a name="demo-ui-to-visualize-output"></a>Çıktıyı görselleştirmek için tanıtım Kullanıcı arabirimi

> [!NOTE]
> Demo yalnızca sistem durumu kapsayıcısı için Metin Analizi kullanılabilir.

Kapsayıcı REST tabanlı sorgu tahmin uç noktası API’lerini sağlar.  Ayrıca, kapsayıcının uç noktasına eklenerek erişilebilen kapsayıcıda bir görselleştirme aracı sağladık `/demo` . Örnek:

```
http://<serverURL>:5000/demo
```

Değişkeni uygun değerle değiştirerek dağıttığınız kapsayıcıya bir sorgu göndermek için aşağıdaki örnek kıvrımlı isteği kullanın `serverURL` .

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1-preview.4/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Azure Kapsayıcılar için Web App kullanarak kapsayıcıyı yüklerken

Azure [kapsayıcılar için Web App](https://azure.microsoft.com/services/app-service/containers/) , bulutta kapsayıcılar çalıştırmak için adanmış bir Azure kaynağıdır. Otomatik ölçeklendirme, Docker kapsayıcıları ve Docker Compose desteği, HTTPS desteği ve çok daha fazlası gibi kullanıma hazır özellikleri getirir.

> [!NOTE]
> Azure Web App 'i kullanarak bir etki alanını otomatik olarak şu biçimde alacaksınız `<appservice_name>.azurewebsites.net`

Aboneliğinizi ve HTTPS üzerinden kapsayıcı görüntüsünü kullanarak bir Kapsayıcılar için Web App oluşturmak için Azure CLı kullanarak bu PowerShell betiğini çalıştırın. İlk isteği göndermeden önce betiğin (yaklaşık 25-30 dakika) tamamlanmasını bekleyin.

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME 
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Azure Container Instance kullanarak kapsayıcıyı yükler

Ayrıca, dağıtımı kolaylaştırmak için bir Azure Container Instance (acı) kullanabilirsiniz. ACI, yönetilen, sunucusuz bir Azure ortamında Docker kapsayıcılarını isteğe bağlı olarak çalıştırmanıza olanak tanıyan bir kaynaktır. 

Azure portal kullanarak ACI kaynağını dağıtma adımları için bkz. [Azure Container Instances nasıl kullanılır](../../containers/azure-container-instance-recipe.md) . Ayrıca, kapsayıcı görüntüsünü kullanarak aboneliğinizde bir ACI oluşturacak olan Azure CLı kullanarak aşağıdaki PowerShell betiğini de kullanabilirsiniz.  İlk isteği göndermeden önce betiğin (yaklaşık 25-30 dakika) tamamlanmasını bekleyin.  ACI kaynağı başına en fazla CPU sayısı sınırı nedeniyle, her istek için 5 ' ten fazla büyük belge (yaklaşık 5000 karakter) göndermeyi düşünüyorsanız bu seçeneği seçmeyin.
Kullanılabilirlik bilgileri için [acı bölgesel destek](../../../container-instances/container-instances-region-availability.md) makalesine bakın. 

> [!NOTE] 
> Azure Container Instances Builtin Domains için HTTPS desteği eklemeyin. HTTPS gerekiyorsa, sertifika oluşturma ve bir etki alanını kaydetme dahil olmak üzere el ile yapılandırmanız gerekir. Bunu, aşağıdaki NGıNX ile yapmak için yönergeler bulabilirsiniz.

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Güvenli acı bağlantısı

Varsayılan olarak, kapsayıcı API 'SI ile acı kullanılırken bir güvenlik sağlanmaz. Bunun nedeni genellikle kapsayıcıların bir ağ köprüsü tarafından dışından korunan Pod 'ın bir parçası olarak çalışacaktır. Ancak, kapsayıcı uç noktasını özel olarak tutarak bir kapsayıcıyı öne bakan bir bileşenle değiştirebilirsiniz. Aşağıdaki örneklerde, HTTPS/SSL ve istemci sertifikası kimlik doğrulamasını desteklemek için bir giriş ağ geçidi olarak [NGINX](https://www.nginx.com) kullanılmaktadır.

> [!NOTE]
> NGıNX açık kaynaklı, yüksek performanslı bir HTTP sunucusudur ve proxy 'dir. Bir NGıNX kapsayıcısı, tek bir kapsayıcı için bir TLS bağlantısını sonlandırmak üzere kullanılabilir. Daha karmaşık NGıNX girişi tabanlı TLS sonlandırma çözümleri de mümkündür.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Giriş ağ geçidi olarak NGıNX ayarlama

NGıNX, çalışma zamanında özellikleri etkinleştirmek için [yapılandırma dosyalarını](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) kullanır. Başka bir hizmet için TLS sonlandırmasını etkinleştirmek üzere TLS bağlantısını sonlandırmak ve  `proxy_pass` hizmet için bir adres belirtmek üzere BIR SSL sertifikası belirtmeniz gerekir. Aşağıda bir örnek verilmiştir.


> [!NOTE]
> `ssl_certificate` NGıNX kapsayıcısının yerel dosya sistemi içinde bir yol belirtilmesini bekler. İçin belirtilen adres, `proxy_pass` NGıNX kapsayıcısının ağı içinden kullanılabilir olmalıdır.

NGıNX kapsayıcısı, içindeki içindeki tüm dosyaları `_.conf_` `/etc/nginx/conf.d/` http yapılandırma yoluna yükleyecek.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Örnek Docker Compose dosyası

Aşağıdaki örnek, NGıNX ve sistem durumu kapsayıcıları için Metin Analizi dağıtmak üzere bir [Docker Compose](https://docs.docker.com/compose/reference/overview) dosyasının nasıl oluşturulacağını gösterir:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Bu Docker Compose dosyasını başlatmak için, dosyanın kök düzeyindeki bir konsoldan aşağıdaki komutu yürütün:

```bash
docker-compose up
```

Daha fazla bilgi için, [NGıNX SSL sonlandırmasına](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/)ilişkin NGINX belgeleri bölümüne bakın.
