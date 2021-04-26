---
title: Azure Arc özellikli PostgreSQL Hiper Ölçek sunucu grubu oluşturma
description: Azure Arc özellikli PostgreSQL Hiper Ölçek sunucu grubu oluşturma
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687949"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc özellikli PostgreSQL Hiper Ölçek sunucu grubu oluşturma

Bu belgede, Azure Arc üzerinde bir PostgreSQL hiper ölçek sunucu grubu oluşturma adımları açıklanmaktadır.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Kullanmaya başlama
Aşağıdaki konularda zaten bilgi sahibiyseniz bu paragrafı atlayabilirsiniz.
Oluşturmaya devam etmeden önce, okumak isteyebileceğiniz önemli konular vardır:
- [Azure Arc etkin veri Hizmetleri 'ne genel bakış](overview.md)
- [Bağlantı modları ve gereksinimler](connectivity.md)
- [Depolama yapılandırması ve Kubernetes depolama kavramları](storage-configuration.md)
- [Kubernetes kaynak modeli](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Tam bir ortamı sağlamadan şeyleri denemeyi tercih ediyorsanız Azure Kubernetes Service (AKS), AWS elastik Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) veya bir Azure VM 'de [Azure Arc atlamalı](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) ile hızlı bir şekilde çalışmaya başlayabilirsiniz.


## <a name="login-to-the-azure-arc-data-controller"></a>Azure Arc veri denetleyicisinde oturum açma

Bir örnek oluşturabilmeniz için önce Azure Arc veri denetleyicisinde oturum açmalısınız. Veri denetleyicisinde zaten oturum açtıysanız, bu adımı atlayabilirsiniz.

```console
azdata login
```

Ardından Kullanıcı adı, parola ve sistem ad alanı sorulur.  

> Veri denetleyicisi oluşturmak için betiği kullandıysanız, ad alanınız **yay** olmalıdır

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Yalnızca OpenShift kullanıcıları için ön ve geçici adım
Sonraki adıma geçmeden önce bu adımı uygulayın. Varsayılan dışında bir projede PostgreSQL hiper ölçek sunucu grubunu Red Hat OpenShift 'e dağıtmak için, güvenlik kısıtlamalarını güncelleştirmek üzere kümenizde aşağıdaki komutları yürütmeniz gerekir. Bu komut, PostgreSQL hiper ölçek sunucu grubunuzu çalıştıracak hizmet hesaplarına gerekli ayrıcalıkları verir. Güvenlik bağlamı kısıtlaması (SCC) Arc-Data-SCC, Azure Arc veri denetleyicisi 'ni dağıtırken eklediğiniz bir.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Sunucu-grup adı, bir sonraki adımda oluşturacağınız sunucu grubunun adıdır.**

OpenShift 'te SCCs hakkında daha fazla ayrıntı için lütfen [OpenShift belgelerine](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)bakın. Şimdi bir sonraki adımı uygulayabilirsiniz.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc özellikli PostgreSQL Hiper Ölçek sunucu grubu oluşturma

Arc veri denetleyicinizde Azure Arc etkin bir PostgreSQL hiper ölçek sunucu grubu oluşturmak için, `azdata arc postgres server create` çeşitli parametreleri geçireceği komutu kullanacaksınız.

Oluşturma zamanında ayarlayabileceğiniz tüm parametrelere ilişkin ayrıntılar için, komutun çıkışını gözden geçirin:
```console
azdata arc postgres server create --help
```

Ana parametreler şunları göz önünde bulundurmalıdır:
- dağıtmak istediğiniz **sunucu grubunun adı** . `--name` `-n` Uzunluğunun 11 karakteri aşmaması gereken bir ad veya arkasından bir ad belirtin.

- dağıtmak istediğiniz **PostgreSQL altyapısının sürümü** : varsayılan olarak 12. sürümüdür. Sürüm 12 ' yi dağıtmak için bu parametreyi atlayabilirsiniz veya şu parametrelerden birini geçirebilirsiniz: `--engine-version 12` ya da `-ev 12` . Sürüm 11 ' i dağıtmak için `--engine-version 11` veya belirtin `-ev 11` .

- genişletmek için dağıtmak istediğiniz **çalışan düğümlerinin sayısı** ve daha iyi performansa ulaşın. Buraya geçmeden önce [Postgres Hyperscale kavramlarını](concepts-distributed-postgres-hyperscale.md)okuyun. Dağıtılacak çalışan düğümlerinin sayısını göstermek için, parametresini kullanın `--workers` veya `-w` 2 ' ye eşit veya daha büyük bir tamsayı ile bunu yapın. Örneğin, 2 çalışan düğümü olan bir sunucu grubu dağıtmak istiyorsanız, `--workers 2` veya belirtin `-w 2` . Bu, biri Koordinatör düğümü/örneği ve iki çalışan düğümleri/örnekleri (çalışanların her biri için) için olmak üzere üç pods oluşturur.

- sunucu grubunuzun kullanmasını istediğiniz **Depolama sınıfları** . Dağıtım yapıldıktan sonra değiştirilenemez, depolama sınıfını bir sunucu grubunu dağıtırken doğrudan ayarlamanız önemlidir. Dağıtımdan sonra depolama sınıfını değiştirmiş olmanız durumunda, verileri ayıklamanız, sunucu grubunuzu silmeniz, yeni bir sunucu grubu oluşturmanız ve verileri içeri aktarmanız gerekir. Veriler, Günlükler ve yedeklemeler için kullanılacak depolama sınıflarını belirtebilirsiniz. Varsayılan olarak, depolama sınıflarını belirtmezseniz, veri denetleyicisinin Depolama sınıfları kullanılacaktır.
    - verilerin depolama sınıfını ayarlamak için, parametresini `--storage-class-data` veya `-scd` ardından depolama sınıfının adını belirtin.
    - Günlükler için depolama sınıfını ayarlamak üzere, parametresini `--storage-class-logs` veya `-scl` ardından depolama sınıfının adını belirtin.
    - yedeklemelerin depolama sınıfını ayarlamak için: Azure Arc etkin PostgreSQL hiper ölçeğinde Bu önizlemede, yapmak istediğiniz yedekleme/geri yükleme işlemlerinin türlerine bağlı olarak depolama sınıflarını ayarlamak için iki yol vardır. Bu deneyimi basitleştirmek için çalışıyoruz. Bir depolama sınıfı ya da bir birim talebi bağlama belirtecaksınız. Birim talebi bağlama, var olan kalıcı birim talebinin bir çiftidir (aynı ad alanında) ve birim türü (ve birim türüne göre isteğe bağlı meta veriler) iki nokta ile ayrılır. Kalıcı birim, PostgreSQL sunucu grubu için her Pod 'a bağlanır.
        - Planın yalnızca tam veritabanı geri yüklemelerini yapmak istiyorsanız, parametresini `--storage-class-backups` veya `-scb` ardından depolama sınıfının adını belirleyin.
        - hem tam veritabanı geri yükleme hem de zaman noktası geri yükleme yapmak istiyorsanız, parametresini ayarlayın `--volume-claim-mounts` veya `-vcm` ardından bir birim talebi ve bir birim türü yazın.

Oluştur komutunu yürüttüğünüzde, varsayılan yönetici kullanıcının parolasını girmeniz istenir `postgres` . Bu önizlemede bu kullanıcının adı değiştirilemez. `AZDATA_PASSWORD`Create komutunu çalıştırmadan önce oturum ortam değişkenini ayarlayarak etkileşimli istemi atlayabilirsiniz.

### <a name="examples"></a>Örnekler

**Veri denetleyicisiyle aynı depolama sınıflarını kullanan 2 çalışan düğümü ile postgres01 adlı bir Postgres sürüm 12 adlı sunucu grubunu dağıtmak için aşağıdaki komutu çalıştırın:**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**Veri ve Günlükler için veri denetleyicisi olarak aynı depolama sınıflarını kullanan 2 çalışan düğümü olan bir Postgres sürüm 12 adlı postgres01 adlı bir sunucu grubunu dağıtmak için, ancak belirli depolama sınıfı, tam geri yükleme ve zaman içinde geri yükleme işlemleri yapmak için aşağıdaki adımları kullanın:**

 Bu örnekte, sunucu grubunuzun bir Azure Kubernetes hizmeti (AKS) kümesinde barındırıldığı varsayılmaktadır. Bu örnekte azurefile-Premium, depolama sınıfı adı olarak kullanılır. Aşağıdaki örneği ortamınızla eşleşecek şekilde ayarlayabilirsiniz. Bu yapılandırma için **AccessMode ReadWriteMany ' ın gerekli** olduğunu unutmayın.  

İlk olarak, yedek PVC 'nin aşağıdaki açıklamasını içeren bir YAML dosyası oluşturun ve bunu CreateBackupPVC. yml olarak adlandırın:
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

Ardından, YAML dosyasında depolanan tanımı kullanarak bir PVC oluşturun:

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

Sonra, sunucu grubunu oluşturun:

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - [Yedekleme/geri yükleme ile ilgili geçerli sınırlamaları](limitations-postgresql-hyperscale.md#backup-and-restore)okuyun.


> [!NOTE]  
> - Veri denetleyicisini `AZDATA_USERNAME` ve `AZDATA_PASSWORD` oturum ortam değişkenlerini aynı Terminal oturumunda dağıttıysanız, `AZDATA_PASSWORD` Bu değerler PostgreSQL hiper ölçek sunucu grubunu da dağıtmak için kullanılacaktır. Başka bir parola kullanmayı tercih ediyorsanız, (1) veya değerini güncelleştirin ya da ( `AZDATA_PASSWORD` 2) `AZDATA_PASSWORD` ortam değişkenini silin veya (3) bir sunucu grubu oluştururken etkileşimli olarak parola girmesi istenir değerini silin.
> - PostgreSQL hiper ölçek sunucu grubu oluşturma, kaynakları hemen Azure 'da kaydetmez. [Kaynak envanteri](upload-metrics-and-logs-to-azure-monitor.md) veya [kullanım verilerini](view-billing-data-in-azure.md) Azure 'a yükleme işleminin bir parçası olarak, kaynaklar azure 'da oluşturulur ve Azure Portal kaynaklarınızı görebileceksiniz.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Arc veri denetleyicinizde dağıtılan PostgreSQL hiper ölçek sunucu gruplarını listeleyin

Arc veri denetleyicinizde dağıtılan PostgreSQL hiper ölçek sunucu gruplarını listelemek için aşağıdaki komutu çalıştırın:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc etkin PostgreSQL hiper ölçek sunucu gruplarına bağlanmak için uç noktaları alın

Bir PostgreSQL sunucu grubunun uç noktalarını görüntülemek için aşağıdaki komutu çalıştırın:

```console
azdata arc postgres endpoint list -n <server group name>
```
Örnek:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

PostgreSQL örnek uç noktasını, en sevdiğiniz araçtan PostgreSQL hiper ölçek sunucu grubuna bağlanmak için kullanabilirsiniz:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [pgclı](https://www.pgcli.com/) psql, pgAdmin, vb. Bunu yaptığınızda, dağıtılmış tablolar oluşturduysanız, sorguyu uygun çalışan düğümlerine/örneklerine yönlendirmeyi ele alan düzenleyici düğümüne/örneğine bağlanırsınız. Daha fazla ayrıntı için, [Azure Arc etkin PostgreSQL hiper ölçek kavramlarını](concepts-distributed-postgres-hyperscale.md)okuyun.

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Azure sanal makine dağıtımları hakkında özel bilgi

Bir Azure sanal makinesi kullanırken, uç nokta IP adresi _genel_ IP adresini göstermez. Genel IP adresini bulmak için aşağıdaki komutu kullanın:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Daha sonra bağlantı kurmak için genel IP adresini bağlantı noktasıyla birleştirebilirsiniz.

Ayrıca, PostgreSQL hiper ölçek sunucu grubunun bağlantı noktasını ağ güvenlik ağ geçidi (NSG) üzerinden kullanıma sunabilirsiniz. (NSG) üzerinden trafiğe izin vermek için aşağıdaki komutu kullanarak kullanabileceğiniz bir kural eklemeniz gerekir:

Bir kural ayarlamak için NSG 'nizin adını bilmeniz gerekir. Aşağıdaki komutu kullanarak NSG 'yi belirlersiniz:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

NSG 'nin adını aldıktan sonra, aşağıdaki komutu kullanarak bir güvenlik duvarı kuralı ekleyebilirsiniz. Burada örnek değerler, 30655 numaralı bağlantı noktası için bir NSG kuralı oluşturur ve **herhangi** BIR kaynak IP adresinden bağlantıya izin verir.  Bu en iyi güvenlik uygulaması değildir!  İstemci IP adresine özgü bir-kaynak-adres ön eki değeri ya da takımınızın veya kuruluşun IP adreslerini içeren bir IP adresi aralığı belirterek, şeyleri daha iyi bir şekilde kilitleyebilir.

Aşağıdaki--Destination-Port-Ranges parametresinin değerini yukarıdaki ' azdata Arc Postgres Server List ' komutundan aldığınız bağlantı noktası numarasıyla değiştirin.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Azure Data Studio ile bağlanma

Azure Data Studio açın ve yukarıdaki dış uç nokta IP adresini ve bağlantı noktası numarasını ve örneği oluşturduğunuz sırada belirttiğiniz parolayı kullanarak örneğinizle bağlantı yapın.  *Bağlantı türü* açılan listesinde PostgreSQL yoksa, Uzantılar sekmesinde PostgreSQL ' ı arayarak PostgreSQL uzantısını yükleyebilirsiniz.

> [!NOTE]
> Bağlantı masasındaki [Gelişmiş] düğmesine tıklayarak bağlantı noktası numarasını girmeniz gerekir.

Azure VM kullanıyorsanız, şu komutla erişilebilen _genel_ IP adresine ihtiyacınız olacağını unutmayın:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Psql ile bağlanma

PostgreSQL hiper ölçek sunucu grubunuza erişmek için, yukarıda aldığınız PostgreSQL hiper ölçek sunucu grubunun dış uç noktasını geçirin:

Artık psql 'e bağlanabilirsiniz:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Sonraki adımlar

- PostgreSQL için Azure veritabanı 'nın, verileri birden çok PostgreSQL hiper ölçek düğümüne dağıtmak ve daha iyi performansaldan yararlanmak için nasıl yapılır kılavuzlarını okuyun:
    * [Düğümler ve tablolar](../../postgresql/concepts-hyperscale-nodes.md)
    * [Uygulama türünü belirleme](../../postgresql/concepts-hyperscale-app-type.md)
    * [Dağıtım sütunu seçme](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tablo birlikte bulundurma](../../postgresql/concepts-hyperscale-colocation.md)
    * [Tabloları dağıtma ve değiştirme](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Çok kiracılı bir veritabanı tasarlama](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Gerçek zamanlı analiz panosu tasarlama](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Yukarıdaki belgelerde, **Azure Portal oturum açma** bölümünü atlayın, & **PostgreSQL Için Azure veritabanı oluşturun-hiper ölçek (Citus)**. Azure Arc dağıtımınızda kalan adımları uygulayın. Bu bölümler, Azure bulutunda PaaS hizmeti olarak sunulan PostgreSQL için Azure veritabanı 'na (Citus) özgüdür, ancak belgelerin diğer kısımları doğrudan Azure Arc etkin PostgreSQL hiper ölçeklendirmenize uygulanabilir.

- [PostgreSQL hiper ölçek sunucu grubu için etkin Azure yayı ölçeklendirme](scale-out-postgresql-hyperscale-server-group.md)
- [Depolama yapılandırması ve Kubernetes depolama kavramları](storage-configuration.md)
- [Kalıcı birim taleplerini genişletme](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes kaynak modeli](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
