---
title: Azure Red Hat OpenShift 4 kümesine Red hat çekme gizli anahtarını ekleme veya güncelleştirme
description: Var olan 4. x ARO kümelerinde Red hat çekme gizli anahtarını ekleme veya güncelleştirme
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/21/2020
keywords: Çek gizli, Aro, OpenShift, Red Hat
ms.openlocfilehash: 58c0eb2be3423783a69d005277ffe75aaf59415f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633742"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 kümesine Red hat çekme gizli anahtarını ekleme veya güncelleştirme

Bu kılavuzda, var olan bir Azure Red Hat OpenShift (ARO) 4. x kümesi için Red hat çekme gizli anahtarını ekleme veya güncelleştirme ele alınmaktadır.

İlk kez bir küme oluşturuyorsanız, kümenizi oluştururken çekme gizli dizisini ekleyebilirsiniz. Red hat çekme gizli anahtarı ile bir ARO kümesi oluşturma hakkında daha fazla bilgi için bkz. [Azure Red Hat OpenShift 4 kümesi oluşturma](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional).

## <a name="before-you-begin"></a>Başlamadan önce

Bu kılavuzda, var olan bir Azure Red Hat OpenShift 4 kümeniz olduğunu varsaymaktadır. Kümenize yönetici erişiminiz olduğundan emin olun.

## <a name="prepare-your-pull-secret"></a>Çekme sırlarınızı hazırlayın
Red hat çekme parolası eklemeden bir ARO kümesi oluşturduğunuzda, kümenizde otomatik olarak bir çekme parolası oluşturulur. Ancak, bu çekme gizli anahtarı tamamen doldurulmuyor.

Bu bölümde, bu çekme gizliliğini Red hat çekme gizli dizinizdeki ek değerlerle güncelleştirme adımları gösterilmektedir.

1. Ad alanında adlı gizli anahtarı getirin `pull-secret` `openshift-config` ve aşağıdaki komutu çalıştırarak ayrı bir dosyaya kaydedin: 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    Çıktın aşağıdakine benzer olması gerekir. (Gerçek gizli anahtar değerinin kaldırıldığını unutmayın.)

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. [Red Hat Openshıft Küme Yöneticisi portalınıza](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) gidin ve **çekme gizli anahtarını indir**' i seçin. Red hat çekme gizli dizisi aşağıdaki gibi görünür. (Gerçek gizli değerlerin kaldırıldığını unutmayın.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Kırmızı hat çekme gizli dosyanızda bulunan girişlere ekleyerek, kümeinizden aldığınız çekme gizli dosyasını düzenleyin. 

    > [!IMPORTANT]
    > `cloud.openshift.com`Kırmızı hat çekme gizli dizinizdeki girişin dahil edilmesi, kümenizin Red Hat 'e telemetri verileri göndermeye başlamasına neden olur. Bu bölümü yalnızca telemetri verilerini göndermek istiyorsanız ekleyin. Aksi takdirde, aşağıdaki bölümden ayrılın.    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > `arosvc.azurecr.io`Çekme gizli dizinizdeki girişi kaldırmayın veya değiştirmeyin. Bu bölüm, kümenizin düzgün çalışması için gereklidir.

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    Son dosyanız aşağıdaki gibi görünmelidir. (Gerçek gizli değerlerin kaldırıldığını unutmayın.)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. Dosyanın geçerli bir JSON olduğundan emin olun. JSON 'nizi doğrulamak için kullanabileceğiniz birçok yol vardır. Aşağıdaki örnek JQ kullanır:

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > Dosyada bir hata varsa, olarak görünür `parse error` .

## <a name="add-your-pull-secret-to-your-cluster"></a>Çekme sırlarınızı kümenize ekleyin

Çekme sırlarınızı güncelleştirmek için aşağıdaki komutu çalıştırın.

> [!NOTE]
> Bu komutun çalıştırılması, Küme düğümlerinizin güncelleştirildiğinden tek tek yeniden başlatılmasına neden olur. 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

Gizli dizi ayarlandıktan sonra Red Hat sertifikalı Işleçleri etkinleştirmeye hazırsınız demektir.

### <a name="modify-the-configuration-files"></a>Yapılandırma dosyalarını değiştirme

Red Hat Işleçlerini etkinleştirmek için aşağıdaki nesneleri değiştirin.

İlk olarak, Samples Işleci yapılandırma dosyasını değiştirin. Ardından, yapılandırma dosyasını düzenlemek için aşağıdaki komutu çalıştırabilirsiniz:

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

`spec.architectures.managementState`Ve `status.architecture.managementState` değerlerini `Removed` olarak değiştirin `Managed` . 

Aşağıdaki YAML kod parçacığı yalnızca düzenlenmiş YAML dosyasının ilgili bölümlerini gösterir:

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

İkinci olarak, operatör hub 'ı yapılandırma dosyasını düzenlemek için aşağıdaki komutu çalıştırın:  

```console
oc edit operatorhub cluster -o yaml
```

`Spec.Sources.Disabled`Ve değerlerini, `Status.Sources.Disabled` `true` `false` etkin olmasını istediğiniz tüm kaynaklar için ile olarak değiştirin.

Aşağıdaki YAML kod parçacığı yalnızca düzenlenmiş YAML dosyasının ilgili bölümlerini gösterir:

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

Düzenlemelerinizi uygulamak için dosyayı kaydedin.

## <a name="validate-that-your-secret-is-working"></a>Gizli dizinizin çalıştığını doğrulama

Çekme sıranız ekledikten ve doğru yapılandırma dosyalarını değiştirdikten sonra, kümenizin güncellenmesi birkaç dakika sürebilir. Kümenizin güncelleştirildiğini denetlemek için şu komutu çalıştırarak sertifikalı Işleçler ve Red Hat Işletmenleri kaynakları ' nı görüntüleyin:

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

Sertifikalı Işleçleri ve Red Hat Işleçlerini görmüyorsanız, birkaç dakika bekleyip yeniden deneyin.

Çekme gizliliğin güncelleştirildiğinden ve düzgün çalıştığından emin olmak için OperatorHub açın ve Red Hat onaylanmış bir operatör olup olmadığını denetleyin. Örneğin, OpenShift kapsayıcı depolama Işlecinin kullanılabilir olup olmadığını denetleyin ve yüklemek için izinleriniz olup olmadığını denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Red hat çekme gizli dizileri hakkında daha fazla bilgi edinmek için bkz. [görüntü çekme gizli dizileri kullanma](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html).

Red Hat OpenShift 4 hakkında daha fazla bilgi edinmek için bkz. [Red Hat OpenShift kapsayıcı platformu belgeleri](https://docs.openshift.com/container-platform/4.6/welcome/index.html).
