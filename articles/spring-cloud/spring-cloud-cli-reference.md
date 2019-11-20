---
title: az yay bulutu
description: Azure CLı kullanarak Azure yay bulutu 'nı yönetme
author: jpconnock
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: jeconnoc
ms.openlocfilehash: a1f82d4efa7756b44ca5ed9859aa872c1f55b565
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607831"
---
# <a name="az-spring-cloud"></a>az yay-bulut

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Azure CLı kullanarak Azure yay bulutu 'nı yönetme

>[!Note]
> Azure yay bulutu Şu anda önizleme aşamasındadır.  Bu komutlar sonraki bir sürümde değiştirilebilir veya kaldırılabilir.

| az yay-bulut |  |
|------|------:|
| [az yay-bulut oluşturma](#az-spring-cloud-create) | Azure yay bulutu örneği oluşturun. |
| [az yay-Cloud Delete](#az-spring-cloud-delete) | Azure yay bulutu örneğini silin. |
| [az yay-bulut listesi](#az-spring-cloud-list) | Belirtilen kaynak grubundaki tüm Azure yay bulutu örneklerini listeleyin, aksi takdirde abonelik kimliklerini listeleyin. |
| [az yay-bulut göster](#az-spring-cloud-show) | Azure Spring Cloud 'ın ayrıntılarını görüntüleyin. |

| az Spring-Cloud App | Azure Spring Cloud 'da uygulamaları yönetmeye yönelik komutlar.  |
| ---- | ----: |
| [az yay-bulut uygulaması oluşturma](#az-spring-cloud-app-create) | Azure yay bulutu 'nda varsayılan bir dağıtıma sahip yeni bir uygulama oluşturun. |
| [az yay-Cloud App Delete](#az-spring-cloud-app-delete) | Azure Spring Cloud 'da bir uygulamayı silin. |
| [az yay-Cloud App Deploy](#az-spring-cloud-app-deploy) | Kaynak koddan veya önceden oluşturulmuş ikiliden bir uygulamaya dağıtın ve ilgili konfigürasyonları güncelleştirin. |
| [az Spring-Cloud App List](#az-spring-cloud-app-list) | Azure Spring Cloud 'daki tüm uygulamaları listeleyin. |
| [az Spring-Cloud App restart](#az-spring-cloud-app-restart) | Üretim dağıtım varsayılanlarını kullanarak uygulamanın örneklerini yeniden başlatın. |
| [az yay-bulut uygulaması ölçeği](#az-spring-cloud-app-scale) | Bir uygulamayı veya dağıtımlarını el ile ölçeklendirin. |
| [az yay-Cloud App Set-Deployment](#az-spring-cloud-app-set-deployment) | Uygulamanın üretim dağıtımını ayarlayın. |
| [az yay-Cloud App Show](#az-spring-cloud-app-show) | Azure Spring Cloud 'da bir uygulamanın ayrıntılarını görüntüleyin. |
| [az yay-Cloud App Show-Deploy-log](#az-spring-cloud-app-show-deploy-log) | Kaynaktan en son dağıtım için derleme günlüklerini göster. Varsayılan olarak üretim dağıtımı. |
| [az Spring-Cloud app start](#az-spring-cloud-app-start) | Üretim dağıtım varsayılanlarını kullanarak uygulamanın örneklerini başlatın. |
| [az yay-Cloud App durdur](#az-spring-cloud-app-stop) | Üretim dağıtım varsayılanlarını kullanarak uygulamanın örneklerini durdurun. |
| [az Spring-Cloud App Update](#az-spring-cloud-app-update) | Belirtilen uygulamanın yapılandırmasını güncelleştirin. |

| az yay-bulut uygulaması bağlama | Azure veri Hizmetleri ile bağlamaları yönetme komutları.  Bu ayarların etkili olabilmesi için önce uygulamanın yeniden başlatılması gerekiyor. |
| --- | ---: |
| [az yay-bulut uygulaması bağlama listesi](#az-spring-cloud-app-binding-list) | Bir uygulamadaki tüm hizmet bağlamalarını listeleyin. |
| [az yay-bulut uygulaması bağlama kaldırma](#az-spring-cloud-app-binding-remove) | Uygulamadan bir hizmet bağlamasını kaldırın. |
| [az yay-bulut uygulaması bağlama göster](#az-spring-cloud-app-binding-show) | Bir hizmet bağlamasının ayrıntılarını gösterin. |
| [az yay-bulut uygulaması bağlama Cosmos Add](#az-spring-cloud-app-binding-cosmos-add) | Bir Azure CosmosDB 'yi uygulamayla bağlayın. |
| [az yay-bulut uygulaması bağlama Cosmos güncelleştirmesi](#az-spring-cloud-app-binding-cosmos-update) | Azure CosmosDB hizmeti bağlamasını güncelleştirin. |
| [az yay-bulut uygulaması bağlama MySQL Add](#az-spring-cloud-app-binding-mysql-add) | Uygulamayla bir MySQL için Azure veritabanı bağlayın. |
| [az yay-bulut uygulaması bağlama MySQL güncelleştirmesi](#az-spring-cloud-app-binding-mysql-update) | MySQL için Azure veritabanı hizmeti bağlamayı güncelleştirin. |
| [az yay-bulut uygulaması bağlama Red, Add](#az-spring-cloud-app-binding-redis-add) | Reda için Azure önbelleğini uygulamayla bağlayın. |
| [az Spring-Cloud App Binding redsıs Update](#az-spring-cloud-app-binding-redis-update) | Redsıs hizmeti bağlaması için bir Azure önbelleğini güncelleştirin. |

| az yay-bulut uygulaması dağıtımı | Azure Spring Cloud 'da bir uygulamanın dağıtım yaşam döngüsünü yönetmeye yönelik komutlar. |
| --- | ---: |
| [az yay-Cloud App Deployment oluşturma](#az-spring-cloud-app-deployment-create) | Uygulama için bir hazırlama dağıtımı oluşturun. |
| [az yay-bulut uygulaması dağıtımı silme](#az-spring-cloud-app-deployment-delete) | Uygulamanın dağıtımını silin. |
| [az yay-bulut uygulaması dağıtım listesi](#az-spring-cloud-app-deployment-list) | Bir uygulamadaki tüm dağıtımları listeleyin. |
| [az yay-bulut uygulaması dağıtım göster](#az-spring-cloud-app-deployment-show) | Dağıtımın ayrıntılarını gösterin. |

| az yay-bulut yapılandırması-sunucu | Azure Spring Cloud config Server 'ı yönetmeye yönelik komutlar. |
| --- | ---: |
| [az yay-bulut yapılandırması-sunucu temizle](#az-spring-cloud-config-server-clear) | Yapılandırma sunucusundaki tüm ayarları silin. |
| [az yay-bulut yapılandırması-sunucu kümesi](#az-spring-cloud-config-server-set) | Bir YAML dosyasından yapılandırma sunucusu tanımlayın. |
| [az yay-bulut yapılandırması-sunucu göster](#az-spring-cloud-config-server-show) | Yapılandırma sunucusu yapılandırmasını göster. |
| [az Spring-Cloud config Server git kümesi](#az-spring-cloud-config-server-git-set) | Yapılandırma sunucusu için git özelliklerini tanımlayın.  Önceki değerlerin üzerine yazılacak. |
| [az Spring-Cloud config Server git deposu ekleme](#az-spring-cloud-config-server-git-repo-add) | Yapılandırma sunucusuna yeni bir git deposu yapılandırması ekleyin. |
| [az Spring-Cloud config Server git deposu listesi](#az-spring-cloud-config-server-git-repo-list) | Yapılandırma sunucusu için tüm git deposu yapılandırmaları listesini listeleyin. |
| [az Spring-Cloud config Server git deposu kaldır](#az-spring-cloud-config-server-git-repo-remove) | Belirtilen Git deposunu yapılandırma sunucusundan kaldırın. |

| az Spring-Cloud test-Endpoint | Azure Spring Cloud 'da Endpoint Testing 'i yönetmeye yönelik komutlar |
| --- | ---: |
| [az yay-bulut testi-uç nokta devre dışı](#az-spring-cloud-test-endpoint-disable) | Test uç noktasını devre dışı bırakın. |
| [az yay-bulut testi-uç nokta etkinleştir](#az-spring-cloud-test-endpoint-enable) | Test uç noktasını etkinleştirin. |
| [az yay-bulut testi-uç nokta listesi](#az-spring-cloud-test-endpoint-list) | Test uç noktası anahtarlarını listeleyin. |
| [az yay-bulut testi-uç nokta yenileme-anahtar](#az-spring-cloud-test-endpoint-renew-key) | Test-Endpoint anahtarını yeniden oluşturun. |

## <a name="az-spring-cloud-create"></a>az yay-bulut oluşturma

Azure yay bulutu 'nda varsayılan bir dağıtıma sahip yeni bir uygulama oluşturun.

```cli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Bu Azure yay bulutu örneğinin adı. |
| --Resource-Group-g | Bu uygulama için kaynak grubunu belirtir.  `az configure --defaults group=<name>` kullanarak varsayılan grubu yapılandırma |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --Location-l | Bu uygulama için sunucu konumunu belirtir.  `az account list-locations` kullanarak geçerli konumlar bulma |
| --No-wait | Uzun süre çalışan işlemlerin tamamlanmasını kullanmayın.

### <a name="examples"></a>Örnekler

WestUS 'te yeni bir Azure yay bulutu oluşturun

```cli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az yay-Cloud Delete

Azure yay bulutu örneğini silin.

```cli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Silinecek Azure Spring Cloud örneğinin adı. |
| --Resource-Group-g | Azure Spring Cloud 'ın ait olduğu kaynak grubunun adı. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| -No-wait | Uzun süre çalışan işlemlerin bitmesini beklememe. |

### <a name="example"></a>Örnek

' MyResourceGroup ' öğesinden ' hizmetim ' adlı bir Azure yay bulutu örneğini silin.

```cli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az yay-bulut listesi

Belirtilen kaynak grubuyla ilişkili tüm Azure yay bulutu örneklerini listeleyin. Kaynak grubu belirtilmemişse, abonelik kimliklerini listeleyin.

```cli
az spring-cloud list --resource-group -g
```

| Gerekli parametreler | |
| --- | ---: |
| --Resource-Group-g | Kaynak grubunun adı. |

## <a name="az-spring-cloud-show"></a>az yay-bulut göster

Belirtilen Azure yay bulutu örneğinin ayrıntılarını göster.

```cli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Azure Spring Cloud örneğinin adı. |
| --Resource-Group-g | Azure Spring Cloud örneğinin ait olduğu kaynak grubunun adı.

## <a name="az-spring-cloud-app-create"></a>az yay-bulut uygulaması oluşturma

Azure yay bulutu 'nda yeni bir uygulama oluşturun.

```cli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --CPU | Örnek başına sanal çekirdek sayısı.  Varsayılan: 1. |
| --Enable-kalıcı-depolama | Boolean değeri.  Doğru ise, varsayılan yol ile bir 50 disk bağlar. |
| --örnek-sayısı | Örnek sayısı.  Varsayılan: 1. |
| --,-public | Boolean değeri.  Doğru ise, ortak bir etki alanı atar. |
| --bellek | Örnek başına GB bellek sayısı.  Varsayılan: 1. |

### <a name="examples"></a>Örnekler

Varsayılan yapılandırmayla bir uygulama oluşturun.

```cli
az spring-cloud app create -n MyApp -s MyService
```

3 örneğe sahip genel olarak erişilebilir bir uygulama oluşturun.  Her örnekte 3 GB bellek ve 2 CPU çekirdeği bulunur.

```cli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az yay-Cloud App Delete

Azure Spring Cloud 'da bir uygulamayı siler.

```cli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

## <a name="az-spring-cloud-app-deploy"></a>az yay-Cloud App Deploy

Kaynak koddan veya önceden oluşturulmuş bir ikiliden Azure Spring buluta bir uygulama dağıtın ve ilgili konfigürasyonları güncelleştirin.

```cli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --CPU | Örnek başına sanal CPı çekirdeği sayısı. |
| --Deployment-d | Var olan bir uygulama dağıtımının adı.  Belirtilmemişse, varsayılan olarak üretim dağıtımı yapılır. |
| --env | ' Key [= Value] ' biçiminde boşlukla ayrılmış ortam değişkenleri. |
| --örnek-sayısı | Örnek sayısı. |
| --jar-yol | Sağlanmışsa, belirtilen yoldan jar dağıtın. Aksi takdirde, geçerli klasörü bir tar olarak dağıtın. |
| --JVM-seçenekler | JVM seçeneklerini içeren bir dize.  Kabuk ayrıştırma hatalarını önlemek için ' ' yerine ' = ' kullanın. Ör. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --bellek | Örnek başına GB bellek sayısı. |
| --No-wait | Uzun süre çalışan işlemlerin bitmesini beklememe. |
| --Runtime-sürümü | Uygulamada kullanılan dilin çalışma zamanı sürümü.  İzin verilen değerler: `Java_11`, `Java_8`. |
| --Target-Module | Dağıtılacak alt modül.  Kaynak koddan birden çok jar paketi oluşturulduğunda gereklidir. |
| --sürüm | Dağıtım sürümü.  Ayarlanmamışsa değiştirilmez. |

### <a name="examples"></a>Örnekler

Kaynak kodunu bir uygulamaya dağıtın. Bu, geçerli dizini paketleyebilir, özetleme derleme hizmetini kullanarak bir ikili oluşturur ve ardından uygulamaya dağıtır.

```cli
az spring-cloud app deploy -n MyApp -s MyService
```

JVM seçeneklerini ve ortam değişkenlerini kullanarak bir uygulamaya önceden oluşturulmuş bir jar dağıtın.

```cli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Kaynak kodunu bir uygulamanın belirli bir dağıtımına dağıtın.

```cli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az Spring-Cloud App List

Azure yay bulutu örneğindeki tüm uygulamaları listeleyin.

```cli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Gerekli parametreler | |
| --- | ---: |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

## <a name="az-spring-cloud-app-restart"></a>az Spring-Cloud App restart

Uygulamanın örneklerini yeniden başlatın.  Üretim dağıtımı varsayılan olarak belirlenmiştir.

```cli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --Deployment-d | Uygulamanın var olan dağıtımının adı.  Belirtilmemişse, varsayılan olarak üretim dağıtımı yapılır. |
| --No-wait | Uzun süre çalışan işlemlerin bitmesini beklememe. |

## <a name="az-spring-cloud-app-scale"></a>az yay-bulut uygulaması ölçeği

Bir uygulamayı veya dağıtımlarını el ile ölçeklendirin.

```cli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --CPU | Uygulama örneği başına sanal CPU çekirdeği sayısı. |
| --Deployment-d | Uygulamanın var olan dağıtımının adı.  Belirtilmemişse, varsayılan olarak üretim dağıtımı yapılır. |
| --örnek-sayısı | Bu uygulamanın örneklerinin sayısı. |
| --bellek | Uygulama örneği başına GB bellek miktarı. |
| --No-wait | Uzun süre çalışan işlemlerin bitmesini beklememe. |

### <a name="examples"></a>Örnekler

Bir uygulamayı, örnek başına 4 CPU çekirdeği ve 8 GB bellek ile ölçeklendirin.

```cli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Uygulamanın bir dağıtımını 5 örneğe ölçeklendirin.

```cli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az yay-Cloud App Set-Deployment

Uygulamanın üretim dağıtımı için yapılandırma seçeneklerini ayarlayın.

```cli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Gerekli parametreler | |
| --- | ---: |
| --Deployment-d | Uygulamanın var olan dağıtımının adı. |
| --ad-n | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --No-wait | Uzun süre çalışan işlemlerin bitmesini beklememe. |

### <a name="examples"></a>Örnekler

Uygulamanın hazırlama dağıtımını üretime değiştirme.

```cli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az yay-Cloud App Show

Azure Spring Cloud 'da bir uygulamanın ayrıntılarını görüntüleyin.

```cli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az yay-Cloud App Show-Deploy-log

Kaynak koddan son dağıtımın Derleme günlüğünü göster.  Varsayılan değer üretim ortamıdır.

```cli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --Deployment-d | Uygulamanın var olan dağıtımının adı.  Varsayılan değer üretim ortamıdır. |

## <a name="az-spring-cloud-app-start"></a>az Spring-Cloud app start

Uygulamanın örneklerini başlatır.  Varsayılan değer üretim ortamıdır.

```cli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --Deployment-d | Uygulamanın var olan dağıtımının adı.  Varsayılan değer üretim ortamıdır. |
| --No-wait | Uzun süre çalışan işlemlerin bitmesini beklememe. |

## <a name="az-spring-cloud-app-stop"></a>az yay-Cloud App durdur

Uygulamanın örneklerini durdurun.  Varsayılan değer üretim ortamıdır.

```cli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --Deployment-d | Uygulamanın var olan dağıtımının adı.  Varsayılan değer üretim ortamıdır. |
| --No-wait | Uzun süre çalışan işlemlerin bitmesini beklememe. |

## <a name="az-spring-cloud-app-update"></a>az Spring-Cloud App Update

Uygulamanın depolanan yapılandırmasını güncelleştirin.

```cli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Gerekli parametreler | |
| --- | ---: |
| --ad-n | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --Deployment-d | Uygulamanın var olan dağıtımının adı.  Varsayılan değer üretim ortamıdır. |
| --Enable-kalıcı-depolama | Boolean.  Doğru ise, varsayılan yolla bir 50 disk bağlayın. |
| --env | ' Key [= Value] ' biçiminde boşlukla ayrılmış ortam değişkenleri. |
| --,-public | Boolean.  Doğru ise, uygulamaya ortak bir etki alanı atayın. |
| --JVM-seçenekler | JVM seçeneklerini içeren bir dize.  Kabuk ayrıştırma hatalarını önlemek için ' ' yerine ' = ' kullanın. Ör. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --No-wait | Uzun süre çalışan işlemlerin bitmesini beklememe. |
| --Runtime-sürümü | Uygulamada kullanılan dilin çalışma zamanı sürümü.  İzin verilen değerler: `Java_11`, `Java_8`. |

### <a name="example"></a>Örnek

Uygulama için bir ortam değişkeni ekleyin.

```cli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az yay-bulut uygulaması bağlama listesi

Bir uygulamadaki tüm hizmet bağlamalarını listeleyin.

```cli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

## <a name="az-spring-cloud-app-binding-remove"></a>az yay-bulut uygulaması bağlama kaldırma

Uygulamadan bir hizmet bağlamasını kaldırın.

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --ad | Kaldırılacak hizmet bağlamasının adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

## <a name="az-spring-cloud-app-binding-show"></a>az yay-bulut uygulaması bağlama göster

Bir hizmet bağlamasının ayrıntılarını gösterin.

```cli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --ad | Hizmet bağlamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az yay-bulut uygulaması bağlama Cosmos Add

Azure Cosmos DB uygulamayla bağlayın.

```cli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```cli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Gerekli parametreler | |
| --- | ---: |
| --api-Type | Şu değerlerden birini kullanarak API türünü belirtin: Cassandra, Gremlin, Mongo, SQL, Table. |
| --uygulama | Uygulamanın adı. |
| --ad | Hizmet bağlamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

|İsteğe bağlı parametreler | |
| --- | ---: |
| --koleksiyon-adı | Koleksiyonun adı.  Gremlin kullanılırken gereklidir. |
| --veritabanı-adı | Veritabanının adı.  Mongo, SQL ve Gremlin kullanılırken gereklidir. |
| --anahtar-boşluk | Cassandra anahtarı-boşluk.  Cassandra kullanılırken gereklidir. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az yay-bulut uygulaması bağlama Cosmos güncelleştirmesi

```cli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --ad | Hizmet bağlamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

|İsteğe bağlı parametreler | |
| --- | ---: |
| --koleksiyon-adı | Koleksiyonun adı.  Gremlin kullanılırken gereklidir. |
| --veritabanı-adı | Veritabanının adı.  Mongo, SQL ve Gremlin kullanılırken gereklidir. |
| --anahtar-boşluk | Cassandra anahtarı-boşluk.  Cassandra kullanılırken gereklidir. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az yay-bulut uygulaması bağlama MySQL Add

```cli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --veritabanı-adı | Veritabanının adı. |
| --anahtar | Hizmetin API anahtarı. |
| --ad | Hizmet bağlamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --kaynak kimliği | Bağlanacak hizmetin Azure Kaynak KIMLIĞI. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |
| --username | Veritabanı erişimi için Kullanıcı adı. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az yay-bulut uygulaması bağlama MySQL güncelleştirmesi

Uygulamayı bir MySQL için Azure veritabanı ile bağlantılı hizmet bağlamayı güncelleştirin.

```cli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --ad | Hizmet bağlamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --veritabanı-adı | Veritabanının adı. |
| --anahtar | Hizmetin API anahtarı. |
| --username | Veritabanı erişimi için Kullanıcı adı. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az yay-bulut uygulaması bağlama Red, Add

Reda için Azure önbelleğini uygulamayla bağlayın.

```cli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --ad | Hizmet bağlamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --kaynak kimliği | Bağlamak istediğiniz hizmetin Azure Kaynak KIMLIĞI. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --Disable-SSL | SSL 'yi devre dışı bırakın. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az Spring-Cloud App Binding redsıs Update

Redsıs için Azure önbelleği için bir hizmet bağlamayı güncelleştirin.

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --ad | Hizmet bağlamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --Disable-SSL | SSL 'yi devre dışı bırakın. |

## <a name="az-spring-cloud-app-deployment-create"></a>az yay-Cloud App Deployment oluşturma

Uygulama için bir hazırlama dağıtımı oluşturun.

Kodu dağıtmak veya mevcut bir dağıtıma ayarları güncelleştirmek için `az spring-cloud app deploy --deployment <staging-deployment>` veya ' az Spring-Cloud App Update--Deployment <staging deployment>kullanın.

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --ad | Hizmet bağlamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --CPU | Örnek başına sanal CPU çekirdeği sayısı.  Varsayılan: 1 |
| --env | ' Key [= Value] ' biçiminde boşlukla ayrılmış ortam değişkenleri. |
| --örnek-sayısı | Örnek sayısı. Varsayılan: 1. |
| --jar-yol | Sağlanmışsa, jar dağıtın.  Aksi takdirde, geçerli klasörü bir tar olarak dağıtın. |
| --JVM-seçenekler | JVM seçeneklerini içeren bir dize.  Kabuk ayrıştırma hatalarını önlemek için ' ' yerine ' = ' kullanın. Ör. `--jvm-options='-Xms1024m -Xmx2048m`. |
| --bellek | Örnek başına GB bellek sayısı. |
| --No-wait | Uzun süre çalışan işlemlerin bitmesini beklememe. |
| --Runtime-sürümü | Uygulamada kullanılan dilin çalışma zamanı sürümü.  İzin verilen değerler: `Java_11`, `Java_8`. |
| --Skip-Clone-Settings | Geçerli üretim dağıtım ayarlarını kopyalayarak bir hazırlama dağıtımı oluşturun. |
| --Target-Module | Dağıtılacak alt modül.  Kaynak koddan birden çok jar paketi oluşturulduğunda gereklidir. |
| --sürüm | Dağıtım sürümü.  Ayarlanmamışsa değiştirilmez. |

### <a name="examples"></a>Örnekler

Kaynak kodunu uygulamanın yeni bir dağıtımına dağıtın.  Bu, geçerli dizini paketleyebilir, özetleme yapı sistemini kullanarak bir ikili oluşturur ve ardından dağıtır.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

JVM seçenekleri ve ortam değişkenleri içeren bir uygulamaya önceden oluşturulmuş bir jar dağıtımı yapın.

```cli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az yay-bulut uygulaması dağıtımı silme

Uygulamanın dağıtımını silin.

```cli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --ad | Dağıtımın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

## <a name="az-spring-cloud-app-deployment-list"></a>az yay-bulut uygulaması dağıtım listesi

Bir uygulamadaki tüm dağıtımları listeleyin.

```cli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

## <a name="az-spring-cloud-app-deployment-show"></a>az yay-bulut uygulaması dağıtım göster

Bir dağıtımın ayrıntılarını göster.

```cli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Gerekli parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --ad | Dağıtımın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --hizmet-s | Azure Spring Cloud 'ın adı.  Varsayılan hizmeti `az configure --defaults spring-cloud=<name>`kullanarak yapılandırabilirsiniz. |

## <a name="az-spring-cloud-config-server-clear"></a>az yay-bulut yapılandırması-sunucu temizle

Yapılandırma sunucusundaki tüm yapılandırma ayarlarını silin.

```cli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Gerekli parametreler | |
| --- | ---: |
| --ad | Azure Spring Cloud 'ın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |

## <a name="az-spring-cloud-config-server-set"></a>az yay-bulut yapılandırması-sunucu kümesi

Bir YAML dosyası kullanarak yapılandırma sunucusu üzerindeki yapılandırma ayarlarını belirleyin.

```cli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Gerekli parametreler | |
| --- | ---: |
| --config-File | Yapılandırma sunucusunun yapılandırması için bir YAML bildiriminin dosya yolu. |
| --ad | Azure Spring Cloud 'ın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --No-wait | Uzun süre çalışan işlemlerin tamamlanmasını kullanmayın.

## <a name="az-spring-cloud-config-server-show"></a>az yay-bulut yapılandırması-sunucu göster

Yapılandırma sunucusu ayarlarını görüntüleyin.

```cli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Gerekli parametreler | |
| --- | ---: |
| --ad | Azure Spring Cloud 'ın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |

## <a name="az-spring-cloud-config-server-git-set"></a>az yay-bulut yapılandırması-sunucu git kümesi

Yapılandırma sunucusu için git özelliklerini ayarlayın.  Bu, var olan tüm git özelliklerinin üzerine yazar.

```cli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Gerekli parametreler | |
| --- | ---: |
| --ad | Azure Spring Cloud 'ın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --URI | Eklenen yapılandırmaya ait URI. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --ertele | Azure 'a göndermek yerine, nesneyi yerel önbellekte geçici olarak depolayın.  Görüntülemek/temizlemek için `az cache` kullanın. |
| --Ana bilgisayar-anahtar | Eklenen yapılandırma için ana bilgisayar anahtarı. |
| --Host-anahtar algoritması | Eklenen yapılandırma için konak anahtar algoritması. |
| --Etiket | Eklenen yapılandırma etiketi. |
| --parola | Eklenen yapılandırmaya ait parola. |
| --özel-anahtar | Eklenen yapılandırmaya ait özel anahtar. |
| --Arama-yollar | Eklenen yapılandırmaya ait arama yolları.  Birden çok yol için virgül sınırlayıcılarını kullanın. |
| --katı-konak-anahtar denetimi | Eklenen yapılandırmaya katı ana bilgisayar anahtarı denetimini sunar. |
| --username | Eklenen yapılandırmaya ait Kullanıcı adı. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az yay-bulut yapılandırması-sunucu git deposu ekleme

```cli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Gerekli parametreler | |
| --- | ---: |
| --ad | Azure Spring Cloud 'ın adı. |
| --Repo adı | Deponun URI 'SI. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --URI | Eklenen yapılandırmaya ait URI. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --ertele | Azure 'a göndermek yerine, nesneyi yerel önbellekte geçici olarak depolayın.  Görüntülemek/temizlemek için `az cache` kullanın. |
| --Ana bilgisayar-anahtar | Eklenen yapılandırma için ana bilgisayar anahtarı. |
| --Host-anahtar algoritması | Eklenen yapılandırma için konak anahtar algoritması. |
| --Etiket | Eklenen yapılandırma etiketi. |
| --parola | Eklenen yapılandırmaya ait parola. |
| --Model | Depo için model.  Birden çok yol için virgül sınırlayıcılarını kullanın.|
| --özel-anahtar | Eklenen yapılandırmaya ait özel anahtar. |
| --Arama-yollar | Eklenen yapılandırmaya ait arama yolları.  Birden çok yol için virgül sınırlayıcılarını kullanın. |
| --katı-konak-anahtar denetimi | Eklenen yapılandırmaya katı ana bilgisayar anahtarı denetimini sunar. |
| --username | Eklenen yapılandırmaya ait Kullanıcı adı. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az yay-bulut yapılandırması-sunucu git deposu listesi

Yapılandırma sunucusunda tanımlanan tüm Git depolarını listeleme

```cli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Gerekli parametreler | |
| --- | ---: |
| --ad | Azure Spring Cloud 'ın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --ertele | Azure 'a göndermek yerine, nesneyi yerel önbellekte geçici olarak depolayın.  Görüntülemek/temizlemek için `az cache` kullanın. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az yay-bulut yapılandırması-sunucu git deposu kaldır

Yapılandırma sunucusundan var olan bir git deposu yapılandırmasını kaldırın.

```cli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Gerekli parametreler | |
| --- | ---: |
| --ad | Azure Spring Cloud 'ın adı. |
| --Repo adı | Deponun URI 'SI. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --ertele | Azure 'a göndermek yerine, nesneyi yerel önbellekte geçici olarak depolayın.  Görüntülemek/temizlemek için `az cache` kullanın. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az yay-bulut testi-uç nokta devre dışı

Azure Spring Cloud 'ın test uç noktasını devre dışı bırak

```cli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Gerekli parametreler | |
| --- | ---: |
| --ad | Azure Spring Cloud 'ın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az yay-bulut testi-uç nokta etkinleştir

Azure yay bulutu için test uç noktasını etkinleştirin. 

```cli 
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Gerekli parametreler | |
| --- | ---: |
| --ad | Azure Spring Cloud 'ın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |

## <a name="az-spring-cloud-test-endpoint-list"></a>az yay-bulut testi-uç nokta listesi 

Azure yay bulutu için kullanılabilir test uç noktası anahtarlarını listeleyin.

```cli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Gerekli parametreler | |
| --- | ---: |
| --ad | Azure Spring Cloud 'ın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |

| İsteğe bağlı parametreler | |
| --- | ---: |
| --uygulama | Uygulamanın adı. |
| --Deployment-d | Uygulamanın var olan dağıtımının adı.  Belirtilmemişse, varsayılan olarak üretime yapılır. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az yay-bulut testi-uç nokta yenileme-anahtar

Azure yay bulutu için bir test uç noktası anahtarı oluşturun.

```cli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Gerekli parametreler | |
| --- | ---: |
| --ad | Azure Spring Cloud 'ın adı. |
| --Resource-Group-g | Kaynak grubunun adı.  `az configure --defaults group=<name>`kullanarak varsayılan grubu yapılandırabilirsiniz. |
| --tür | Test uç noktası anahtarının türü.  İzin verilen değerler: birincil, Ikincil. |
