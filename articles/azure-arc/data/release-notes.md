---
title: Azure Arc etkin veri Hizmetleri-sürüm notları
description: En son sürüm notları
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/09/2021
ms.topic: conceptual
ms.openlocfilehash: 1fe5974bafddcb4e474ef59a062836e071ab9461
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304928"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Sürüm notları-Azure Arc etkin veri Hizmetleri (Önizleme)

Bu makalede, Azure Arc etkin veri Hizmetleri için kısa süre önce yayınlanan veya geliştirilmiş özellikleri, özellikleri ve geliştirmeleri vurgular. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>Mart 2021

Mart 2021 sürümü başlangıçta 2021 5 Nisan 'da sunulmuştur ve sürümün son aşamaları 2021 9 Nisan 'da tamamlandı.

Bilinen sorunlarda bu sürümün sınırlamalarını gözden geçirin [-Azure Arc etkin veri Hizmetleri (Önizleme)](known-issues.md).

Azure Data CLı ( `azdata` ) sürüm numarası: 20.3.2. `azdata` [Azure Data CLI ( `azdata` ) uygulamasını yükleyebilirsiniz](/sql/azdata/install/deploy-install-azdata).

### <a name="data-controller"></a>Veri denetleyicisi

- Azure Arc etkin veri Hizmetleri veri denetleyicisini portaldan doğrudan bağlanma modunda dağıtın. [Veri denetleyicisini dağıtmaya başlama-doğrudan bağlantı modu-Önkoşullar](deploy-data-controller-direct-mode-prerequisites.md).

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc etkin PostgreSQL hiper ölçeği

PostgreSQL için özel kaynak tanımları (CRD) tek bir CRD ile birleştirildi. Aşağıdaki tabloya bakın.

|Yayınla |CRD |
|-----|-----|
|Şubat 2021 ve öncesi| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|Mart 2021 ' den itibaren | postgresqls.arcdata.microsoft.com

Geçmiş yüklemeleri Temizleme sırasında önceki CRDs 'yi silecaksınız. Bkz. [Geçmiş yüklemelerinden Temizleme](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations).

### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc özellikli SQL Yönetilen Örneği

- Artık doğrudan bağlı moddaki Azure portal bir SQL yönetilen örneği oluşturabilirsiniz.

- Artık bir veritabanını 3 çoğaltmalarıyla SQL yönetilen örneği 'ne geri yükleyebilirsiniz ve kullanılabilirlik grubuna otomatik olarak eklenir. 

- Artık 3 çoğaltmalarıyla dağıtılan SQL yönetilen örneklerinde ikincil bir salt okunurdur. `azdata arc sql endpoint list`İkincil salt okunurdur bağlantı uç noktasını görmek için kullanın.

### <a name="known-issues"></a>Bilinen sorunlar

- Doğrudan bağlı modda, kullanarak kullanım, ölçüm ve günlüklerin karşıya yüklenmesi `azdata arc dc upload` Şu anda engelleniyor. Kullanım otomatik olarak yüklenir. Dolaylı bağlı modda oluşturulan veri denetleyicisi için karşıya yükleme çalışmaya devam etmelidir.
- Veri denetleyicisinin doğrudan modda dağıtılması yalnızca Azure portal yapılabilir ve azdata, Azure Data Studio veya kubectl gibi istemci araçlarından kullanılamaz.
- Doğrudan modda Azure Arc etkin SQL yönetilen örneğinin dağıtılması yalnızca Azure portal yapılabilir ve azdata, Azure Data Studio veya kubectl gibi araçlardan kullanılamaz.
- Doğrudan modda Azure Arc etkin PostgeSQL hiper ölçek dağıtımı şu anda kullanılamıyor.
- Üzerinden proxy kullanılıyorsa, doğrudan bağlantı modundaki kullanım verilerinin otomatik olarak yüklenmesi başarılı olmaz `–proxy-cert <path-t-cert-file>` .

## <a name="february-2021"></a>Şubat 2021

### <a name="new-capabilities-and-features"></a>Yeni özellikler ve Özellikler

Azure Data CLı ( `azdata` ) sürüm numarası: 20.3.1. `azdata` [Azure Data CLI ( `azdata` ) uygulamasını yükleyebilirsiniz](/sql/azdata/install/deploy-install-azdata).

Ek güncelleştirmeler şunlardır:

- Azure Arc özellikli SQL Yönetilen Örneği
   - Always on kullanılabilirlik grupları ile yüksek kullanılabilirlik

- Azure yay etkin PostgreSQL hiper ölçek Azure Data Studio: 
   - Genel Bakış sayfası artık düğüm başına listelenen sunucu grubunun durumunu gösterir
   - Yeni bir Özellikler sayfası artık sunucu grubuyla ilgili daha fazla ayrıntı göstermek için kullanılabilir
   - **Düğüm parametreleri** sayfasından Postgres motoru parametrelerini yapılandırma

Bu sürümle ilişkili sorunlar için bkz. [bilinen sorunlar-Azure Arc etkin veri Hizmetleri (Önizleme)](known-issues.md)

## <a name="january-2021"></a>Ocak 2021

### <a name="new-capabilities-and-features"></a>Yeni özellikler ve Özellikler

Azure Data CLı ( `azdata` ) sürüm numarası: 20.3.0. `azdata` [Azure Data CLI ( `azdata` ) uygulamasını yükleyebilirsiniz](/sql/azdata/install/deploy-install-azdata).

Ek güncelleştirmeler şunlardır:
- Yerelleştirilmiş Portal 17 yeni dil için kullanılabilir
- Kuin-Native. YAML dosyalarında küçük değişiklikler
- Yeni Grafana ve kibana sürümleri
- Azure Data Studio çözümlenen not defterlerinde azdata kullanırken Python ortamları ile ilgili sorunlar
- Pg_audit uzantısı artık PostgreSQL hiper ölçek için kullanılabilir
- Bir PostgreSQL hiper ölçek veritabanının tam geri yüklenmesi sırasında bir yedekleme KIMLIĞI artık gerekli değildir
- Durum (sistem durumu), bir sunucu grubu oluşturan tüm PostgreSQL örnekleri için raporlanır

   Önceki sürümlerde, durum sunucu grubu düzeyinde toplanmış ve PostgreSQL düğüm düzeyinde toplanmıyor.

- PostgreSQL dağıtımları artık Create komutlarında belirtilen birim boyutu parametrelerini karşılayamıyor
- Bir sunucu grubu düzenlenirken altyapı sürümü parametreleri artık kabul edilir
- PostgreSQL hiper ölçek etkinleştirilmiş Azure Arc 'ın adlandırma kuralı değiştirildi

    Şu biçimdedir: `ServergroupName{c, w}-n` . Örneğin, üç düğüm içeren bir sunucu grubu, bir düzenleyici düğümü ve iki çalışan düğümü şöyle temsil edilir:
   - `Postgres01c-0` (düzenleyici düğümü)
   - `Postgres01w-0` (çalışan düğümü)
   - `Postgres01w-1` (çalışan düğümü)

## <a name="december-2020"></a>Aralık 2020

### <a name="new-capabilities--features"></a>Yeni yetenekler & özellikleri

Azure Data CLı ( `azdata` ) sürüm numarası: 20.2.5. `azdata` [Azure Data CLI ( `azdata` ) uygulamasını yükleyebilirsiniz](/sql/azdata/install/deploy-install-azdata).

SQL yönetilen örneği ve PostgreSQL hiper ölçek için uç noktalarını, Azure Data CLı ( `azdata` ) ile ve komutlarını kullanarak görüntüleyin `azdata arc sql endpoint list` `azdata arc postgres endpoint list` .

Azure Data Studio kullanarak SQL yönetilen örnek kaynağını (CPU çekirdeği ve bellek) isteklerini ve sınırlarını düzenleyin.

Azure Arc etkin PostgreSQL Hyperscale artık, her iki sürüm 11 ve 12 ' den PostgreSQL için tam yedekleme geri yüklemeye ek olarak zaman içinde nokta geri yüklemeye olanak sağlar. Zaman içindeki nokta geri yükleme özelliği, geri yüklenecek belirli bir tarih ve saati belirtmenize olanak tanır.

Azure Arc 'ın hiper ölçek etkin PostgreSQL için adlandırma kuralı değişti. Şu biçimdedir: ServergroupName {r, s}-_n_. Örneğin, üç düğüm içeren bir sunucu grubu, bir düzenleyici düğümü ve iki çalışan düğümü şöyle temsil edilir:
- `postgres02r-0` (düzenleyici düğümü)
- `postgres02s-0` (çalışan düğümü)
- `postgres02s-1` (çalışan düğümü)

### <a name="breaking-change"></a>Son değişiklik

#### <a name="new-resource-provider"></a>Yeni kaynak sağlayıcısı

Bu sürümde, adlı güncelleştirilmiş bir [kaynak sağlayıcısı](../../azure-resource-manager/management/azure-services-resource-providers.md) tanıtılmıştır `Microsoft.AzureArcData` . Bu özelliği kullanabilmeniz için önce bu kaynak sağlayıcısını kaydetmeniz gerekir. 

Bu kaynak sağlayıcısını kaydetmek için: 

1. Azure portal **abonelikler** ' i seçin 
2. Aboneliğinizi seçin
3. **Ayarlar** altında **kaynak sağlayıcıları** ' nı seçin. 
4. Kayıt Ara `Microsoft.AzureArcData` ve Seç  

[Azure kaynak sağlayıcıları ve türlerindeki](../../azure-resource-manager/management/resource-providers-and-types.md)ayrıntılı adımları gözden geçirebilirsiniz. Bu değişiklik Ayrıca, Azure portal yüklediğiniz tüm mevcut Azure kaynaklarını da kaldırır. Kaynak sağlayıcısını kullanabilmeniz için, veri denetleyicisini güncelleştirmeniz ve en son CLI 'yi kullanmanız gerekir `azdata` .  

### <a name="platform-release-notes"></a>Platform sürüm notları

#### <a name="direct-connectivity-mode"></a>Doğrudan bağlantı modu

Bu sürüm doğrudan bağlantı modunu tanıtır. Doğrudan bağlantı modu, veri denetleyicisinin kullanım bilgilerini Azure 'a otomatik olarak yüklemesine olanak sağlar. Kullanım yükleme işleminin bir parçası olarak, Arc veri denetleyicisi kaynağı zaten karşıya yükleme yoluyla oluşturulmadıysa portalda otomatik olarak oluşturulur `azdata` .  

Veri denetleyicisini oluştururken doğrudan bağlantı belirtebilirsiniz. Aşağıdaki örnek, `azdata arc dc create` `arc` doğrudan bağlantı modu () kullanılarak adlandırılmış bir veri denetleyicisi oluşturur `connectivity-mode direct` . Örneği çalıştırmadan önce `<subscription id>` ABONELIK Kimliğinizle değiştirin.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>Bilinen sorunlar

- Azure Kubernetes Service (AKS) üzerinde Kubernetes sürüm 1.19. x desteklenmez.
- Kubernetes 1,19 üzerinde `containerd` desteklenmez.
- Azure 'daki veri denetleyicisi kaynağı şu anda bir Azure kaynağıdır. Silme gibi tüm güncelleştirmeler Kubernetes kümesine geri yayılmaz.
- Örnek adları 13 karakterden fazla olamaz
- Azure Arc veri denetleyicisi veya veritabanı örnekleri için yerinde yükseltme yok.
- Arc özellikli veri hizmetleri kapsayıcı görüntüleri imzalanmadı.  Kubernetes düğümlerinizi, imzasız kapsayıcı görüntülerine çekilmesine izin verecek şekilde yapılandırmanız gerekebilir.  Örneğin, kapsayıcı çalışma zamanı olarak Docker kullanıyorsanız, DOCKER_CONTENT_TRUST = 0 ortam değişkenini ayarlayabilir ve yeniden başlatabilirsiniz.  Diğer kapsayıcı çalışma zamanları [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration)'te olduğu gibi benzer seçeneklere sahiptir.
- Azure portal Azure Arc etkin SQL yönetilen örnekleri veya PostgreSQL hiper ölçek sunucu grupları oluşturulamıyor.
- Şimdilik, NFS kullanıyorsanız, `allowRunAsRoot` `true` Azure Arc veri denetleyicisini oluşturmadan önce dağıtım profili dosyanızda öğesini olarak ayarlamanız gerekir.
- Yalnızca SQL ve PostgreSQL oturum açma kimlik doğrulaması.  Azure Active Directory veya Active Directory desteği yok.
- Openshıft üzerinde bir veri denetleyicisi oluşturmak için gevşek güvenlik kısıtlamaları gerekir.  Ayrıntılar için belgelere bakın.
- Azure Arc veri denetleyicisi ve veritabanı örnekleri ile Azure Stack Hub üzerinde Azure Kubernetes hizmeti (AKS) altyapısı kullanıyorsanız, daha yeni bir Kubernetes sürümüne yükseltme desteklenmez. Kubernetes kümesini yükseltmeden önce Azure Arc veri denetleyicisi 'ni ve tüm veritabanı örneklerini kaldırın.
- [Birden çok kullanılabilirlik bölgesini](../../aks/availability-zones.md) kapsayan aks kümeleri, Azure Arc etkin veri Hizmetleri için şu anda desteklenmiyor. Bu sorundan kaçınmak için, Azure portal ' de AKS kümesi oluşturduğunuzda, bölgelerin kullanılabildiği bir bölgeyi seçerseniz seçim denetiminden tüm bölgeleri temizleyin. Aşağıdaki resme bakın:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Hiçbirini belirtmek için her bir bölgenin onay kutularını temizleyin.":::

## <a name="october-2020"></a>Ekim 2020 

Azure Data CLı ( `azdata` ) sürüm numarası: 20.2.3. `azdata` [Azure Data CLI ( `azdata` ) uygulamasını yükleyebilirsiniz](/sql/azdata/install/deploy-install-azdata).

### <a name="breaking-changes"></a>Yeni değişiklikler

Bu sürümde aşağıdaki son değişiklikler tanıtılmaktadır: 

* PostgreSQL özel kaynak tanımında (CRD), terim `shards` olarak yeniden adlandırılır `workers` . Bu terim ( `workers` ) komut satırı parametre adıyla eşleşir.

* `azdata arc postgres server delete` bir Postgres örneğini silmeden önce onay ister.  `--force`İstemi atlamak için kullanın.

### <a name="additional-changes"></a>Ek değişiklikler

* Çağrılan yeni bir isteğe bağlı parametre eklendi `azdata arc postgres server create` `--volume-claim mounts` . Değer, birim talebi TAK'nin bir virgülle ayrılmış listesidir. Birim talebi bağlama, birim türü ve PVC adı çiftidir. Şu anda desteklenen tek birim türü `backup` .  PostgreSQL içinde, birim türü olduğunda, `backup` PVC öğesine bağlanır `/mnt/db-backups` .  Bu, bir PostgresSQL örneğinin yedeğinin başka bir örneğe geri yüklenebilmesi için PostgresSQL örnekleri arasında yedeklemelerin paylaşılmasını mümkün bir şekilde sunar.

* PostgresSQL özel kaynak tanımları için yeni kısa adlar: 
  * `pg11` 
  * `pg12`
* Telemetri karşıya yüklemesi, Kullanıcı şunları içerir:
   * Azure 'a yüklenen noktaların sayısı veya 
   * Azure 'a hiçbir veri yüklenmediyse, yeniden denemek için bir istem yazın.
* `azdata arc dc debug copy-logs` Şimdi de klasörden okur `/var/opt/controller/log` ve Linux üzerinde PostgreSQL altyapı günlüklerini toplar.
*   PostgreSQL hiper Ölçeklendirmesiyle yedek oluşturma ve geri yükleme sırasında çalışma göstergesi görüntüleyin.
* `azdata arc postrgres backup list` Şimdi yedek boyut bilgilerini içerir.
* SQL yönetilen örnek yönetici adı özelliği, Azure portal genel bakış dikey penceresinin sağ sütununa eklendi.
* Azure Data Studio, PostgreSQL hiper ölçek için çalışan düğüm sayısı, sanal çekirdek ve bellek ayarlarının yapılandırılmasını destekler. 
* Önizleme, Postgres sürüm 11 ve 12 için yedekleme/geri yüklemeyi destekler.

## <a name="september-2020"></a>Eylül 2020

Azure Arc etkin veri Hizmetleri genel önizleme için kullanıma sunuldu. Yay etkin veri Hizmetleri, veri hizmetlerini her yerde yönetmenizi sağlar.

- SQL Yönetilen Örnek
- PostgreSQL hiper ölçek

Yönergeler için bkz. [Azure Arc etkin veri Hizmetleri nedir?](overview.md)

## <a name="next-steps"></a>Sonraki adımlar

> **Yalnızca bir şeyi denemek mi istiyorsunuz?**  
> AKS, AWS elastik Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) veya bir Azure VM 'de [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) ile hızlı bir şekilde çalışmaya başlayın.

- [İstemci araçları 'nı yükler](install-client-tools.md)
- [Azure Arc veri denetleyicisi oluşturma](create-data-controller.md) (önce istemci araçlarının yüklenmesini gerektirir)
- Azure [Arc üzerinde Azure SQL yönetilen örneği oluşturma](create-sql-managed-instance.md) (önce Azure Arc veri denetleyicisinin oluşturulmasını gerektirir)
- [Azure Arc 'Da PostgreSQL Için Azure veritabanı hiper ölçek sunucu grubu oluşturma](create-postgresql-hyperscale-server-group.md) (önce Azure Arc veri denetleyicisi oluşturulmasını gerektirir)
- [Azure hizmetleri için kaynak sağlayıcıları](../../azure-resource-manager/management/azure-services-resource-providers.md)
