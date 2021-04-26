---
title: Kubernetes araçlarını kullanarak bir PostgreSQL hiper ölçek sunucu grubu oluşturma
description: Kubernetes araçlarını kullanarak bir PostgreSQL hiper ölçek sunucu grubu oluşturma
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: fd1b74d33793c06e586a92cc8b2e8d2d36f4827a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519971"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>Kubernetes araçlarını kullanarak bir PostgreSQL hiper ölçek sunucu grubu oluşturma

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Önkoşullar

Zaten bir [Azure Arc veri denetleyicisi](./create-data-controller.md)oluşturmuş olmanız gerekir.

Kubernetes araçlarını kullanarak bir PostgreSQL hiper ölçek sunucu grubu oluşturmak için Kubernetes araçlarının yüklü olması gerekir.  Bu makaledeki örneklerde kullanılacak `kubectl` , ancak benzer yaklaşımlar, Kubernetes panosu gibi diğer Kubernetes araçlarıyla `oc` veya `helm` Bu araçlar ve Kubernetes YAML/JSON hakkında bilginiz varsa kullanılabilir.

[Kubectl aracını kurma](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Genel Bakış

PostgreSQL hiper ölçek sunucu grubu oluşturmak için, Postgres yönetici oturum açma ve parolanızı güvenli bir şekilde depolamak için bir Kubernetes gizli anahtarı ve PostgreSQL-12 veya PostgreSQL-11 özel kaynak tanımlarına dayalı bir PostgreSQL hiper ölçek sunucu grubu özel kaynağı oluşturmanız gerekir.

## <a name="create-a-yaml-file"></a>YAML dosyası oluşturma

Kendi özel PostgreSQL hiper ölçek sunucu grubu YAML Dosyanızı oluşturmak için bir başlangıç noktası olarak, [YAML dosyasını şablon](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/postgresql.yaml) olarak kullanabilirsiniz.  Bu dosyayı yerel bilgisayarınıza indirip bir metin düzenleyicisinde açın.  YAML dosyaları için söz dizimi vurgulamasını destekleyen [vs Code](https://code.visualstudio.com/download) gibi bir metin Düzenleyicisi kullanmak faydalıdır.

Bu örnek bir YAML dosyasıdır:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: pg1-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1alpha1
kind: postgresql-12
metadata:
  generation: 1
  name: pg1
spec:
  engine:
    extensions:
    - name: citus
  scale:
    shards: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Oturum açma ve parolayı özelleştirme.
Bir Kubernetes parolası, Kullanıcı adı ve diğeri parola için Base64 kodlamalı dize olarak depolanır.  Bir yönetici oturum açma ve parola kodlayıp ve bunları ve yer tutucu konumuna yerleştirmeniz gerekir `data.password` `data.username` .  `<` `>` Şablonda sunulan ve sembollerini eklemeyin.

İstediğiniz kullanıcı adınızı ve parolanızı Base64 olarak kodlamak için çevrimiçi bir araç kullanabilir veya platformunuza bağlı olarak yerleşik CLı araçlarını kullanabilirsiniz.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>Adı özelleştirme

Şablon, Name özniteliği için ' PG1 'in ' değerine sahip.  Bunu değiştirebilirsiniz, ancak DNS adlandırma standartlarını izleyen karakterler olmalıdır.  Ayrıca, eşleştirilecek gizli anahtar adını da değiştirmelisiniz.  Örneğin, PostgreSQL hiper ölçek sunucu grubunun adını ' PG2 ' olarak değiştirirseniz, gizli anahtar adını ' PG1 'in-Login-Secret ' iken ' PG2-Login-Secret ' olarak değiştirmeniz gerekir

### <a name="customizing-the-engine-version"></a>Altyapı sürümünü özelleştirme

Özelliği düzenleyerek motor sürümünü PostgreSQL-11 ya da PostgreSQL-12 olacak şekilde değiştirebilirsiniz `kind` .

### <a name="customizing-the-resource-requirements"></a>Kaynak gereksinimlerini özelleştirme

Kaynak gereksinimlerini (RAM ve çekirdek sınırları ve istekleri) gerektiği gibi değiştirebilirsiniz.  

> [!NOTE]
> [Kubernetes Resource idare](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)hakkında daha fazla bilgi edinebilirsiniz.

Kaynak sınırları ve istekleri için gereksinimler:
- Fatura amaçları için çekirdek sınırı değeri **gereklidir** .
- Kaynak isteklerinin ve limitlerin geri kalanı isteğe bağlıdır.
- Çekirdek sınırı ve isteği belirtilmişse pozitif bir tamsayı değeri olmalıdır.
- Belirtilmişse, çekirdek isteği için en az 1 çekirdek gereklidir.
- Bellek değeri biçimi Kubernetes gösterimini izler.  

### <a name="customizing-service-type"></a>Hizmet türünü özelleştirme

İstenirse, hizmet türü NodePort olarak değiştirilebilir.  Rastgele bir bağlantı noktası numarası atanacaktır.

### <a name="customizing-storage"></a>Depolamayı özelleştirme

Depolama sınıflarını, ortamınızda eşleşecek şekilde özelleştirebilirsiniz.  Hangi depolama sınıflarının kullanılabilir olduğundan emin değilseniz, `kubectl get storageclass` bunları görüntülemek için komutunu çalıştırabilirsiniz.  Şablonun ' default ' varsayılan değeri vardır.  Bu, varsayılan _olarak_ ' default ' _adlı_ bir depolama sınıfı olduğu anlamına gelir.  Ayrıca, isteğe bağlı olarak depolamanın boyutunu değiştirebilirsiniz.  [Depolama yapılandırması](./storage-configuration.md)hakkında daha fazla bilgi edinebilirsiniz.

## <a name="creating-the-postgresql-hyperscale-server-group"></a>PostgreSQL hiper ölçek sunucu grubu oluşturuluyor

PostgreSQL hiper ölçek sunucu grubu YAML dosyasını özelleştirdiğinize göre, şu komutu çalıştırarak PostgreSQL hiper ölçek sunucu grubunu oluşturabilirsiniz:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>Oluşturma durumunu izleme

PostgreSQL hiper ölçek sunucu grubunu oluşturma işleminin tamamlanması birkaç dakika sürer. İlerleme durumunu başka bir Terminal penceresinde aşağıdaki komutlarla izleyebilirsiniz:

> [!NOTE]
>  Aşağıdaki örnek komutlarda, ' Arc ' adına sahip ' PG1 'in ' ve Kubernetes ad alanı adlı bir PostgreSQL hiper ölçek sunucu grubu oluşturduğunuzu varsayalım.  Farklı bir ad alanı/PostgreSQL hiper ölçek sunucu grubu adı kullandıysanız, ' yay ' ve ' PG1 'in ' değerini adlarınızla değiştirebilirsiniz.

```console
kubectl get postgresql-12/pg1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Ayrıca, aşağıdaki gibi bir komut çalıştırarak belirli bir pod 'un oluşturma durumunu da denetleyebilirsiniz.  Bu, özellikle herhangi bir sorunu gidermek için kullanışlıdır.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/pg1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Oluşturma sorunlarını giderme

Oluşturma ile herhangi bir sorun yaşarsanız, lütfen [sorun giderme kılavuzuna](troubleshoot-guide.md)bakın.