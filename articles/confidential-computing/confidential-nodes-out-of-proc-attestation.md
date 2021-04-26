---
title: Azure 'da Intel SGX quote Yardımcısı DaemonSet ile kanıtlama desteği (Önizleme)
description: Bir DaemonSet, Intel SGX uygulama işleminin dışında bir teklif oluşturmak için. Bu makalede, bir kapsayıcı içinde çalışan gizli iş yükleri için işlem dışı kanıtlama özelliğinin nasıl sağlandığı açıklanmaktadır.
ms.service: container-service
ms.subservice: confidential-computing
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 849fd7afa3f9365f31ee8e03d9f9cc2174d64304
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484413"
---
# <a name="platform-software-management-with-intel-sgx-quote-helper-daemonset-preview"></a>Intel SGX quote Yardımcısı DaemonSet (Önizleme) ile platform yazılım yönetimi

Uzaktan kanıtlama gerçekleştiren [uygulamalar](confidential-computing-enclaves.md) , oluşturulan bir teklif gerektirir. Bu teklif, kimliğin ve uygulamanın durumunun ve kuşın çalıştığı ortamın şifreleme sağlamasını sağlar. Teklifin oluşturulması, Intel Platform yazılım bileşenlerinin (PSW) parçası olan güvenilir yazılım bileşenleri gerektirir.

## <a name="overview"></a>Genel Bakış
 
Intel, teklif üretimini çalıştırmak için iki kanıtlama modunu destekler:

- *Işlem içi* , güvenilir yazılım bileşenlerini, şifreleme uygulama işleminin içinde barındırır.

- *Işlem dışı* , güvenilir yazılım bileşenlerini, şifreleme uygulamasının dışında barındırır.
 
Open Enclave SDK kullanılarak oluşturulan Intel Software Guard uzantısı (Intel SGX) uygulamaları, varsayılan olarak işlem içi kanıtlama modunu kullanır. Intel SGX tabanlı uygulamalar işlem dışı kanıtlama moduna izin verir. Bu modu kullanmak istiyorsanız, ek barındırma yapmanız gerekir ve mimari Enclave Service Manager (AESD) gibi gerekli bileşenleri uygulamanın dışında kullanıma sunmalıdır.

Bu özellik, Intel Platform güncelleştirmeleri veya DCAP sürücü güncelleştirmeleri sırasında, kuşve uygulamalarınızın çalışma süresini geliştirir. Bu nedenle, bunu kullanmanızı öneririz.

Bu özelliği bir Azure Kubernetes hizmeti (AKS) kümesinde etkinleştirmek için, `--enable-sgxquotehelper` gizli bilgi işlem eklentisini etkinleştirirken komutunu Azure CLI 'ya ekleyin. 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

Daha fazla bilgi için bkz. [hızlı başlangıç: Azure CLI kullanarak BIR AKS kümesini gizli bilgi işlem düğümleri Ile dağıtma](confidential-nodes-aks-get-started.md).

## <a name="benefits-of-the-out-of-process-mode"></a>İşlem dışı modunun avantajları

Aşağıdaki listede, bu kanıtlama modunun bazı önemli avantajları açıklanmaktadır:

-   Her Kapsayıcılı uygulama için PSW teklif üretimi bileşenleri için güncelleştirme gerekmez. Kapsayıcı sahiplerinin, kapsayıcısı içerisindeki güncelleştirmeleri yönetmesi gerekmez. Kapsayıcı sahipleri, kapsayıcı dışında Merkezi hizmeti çağıran sağlayıcı arabirimine bağımlıdır. Sağlayıcı kapsayıcıyı güncelleştirir ve yönetir.

-   Güncel olmayan PSW bileşenleri nedeniyle kanıtlama hatalarıyla uğraşmanız gerekmez. Sağlayıcı, bu bileşenlere yönelik güncelleştirmeleri yönetir.

-   İşlem dışı modu, EPC belleğinin işlem içi moddan daha iyi şekilde kullanımını sağlar. İşlem içi modda, her bir kuşatma uygulamasının, uzak kanıtlama için QE ve PCE 'nin kopyasını oluşturması gerekir. İşlem dışı modda, kapsayıcının bu kuştları barındırması gerekmez ve bu nedenle kapsayıcı kotasından şifreleme belleğini tüketmez.

-   Intel SGX sürücüsünün bir Linux çekirdeğine yukarı akış yaparken, bir kuşın daha yüksek ayrıcalığa sahip olmaya yönelik bir uygulama vardır. Bu ayrıcalık, kuşatma 'nın, işlem içi modda çalışan şifreleme uygulamasını kesen PCE 'yi çağırmasına izin verir. Varsayılan olarak, şifreleme bu izni almaz. Bu ayrıcalığı bir şifreleme uygulamasına vermek, uygulama yükleme işleminde değişiklik yapılmasını gerektirir. Bunun tersine, işlem dışı modunda, işlem dışı istekleri işleyen hizmetin sağlayıcısı, hizmetin bu ayrıcalıkla yüklenmesini sağlar.

-   PSW ve DCAP ile geriye dönük uyumluluğu denetlemeniz gerekmez. PSW 'nin teklif üretimi bileşenleri güncelleştirmeleri, güncelleştirmeden önce sağlayıcı tarafından geriye dönük uyumluluk için onaylanır. Bu, gizli iş yükleri için güncelleştirmeleri dağıtmadan önce uyumluluk sorunlarını ele almanıza yardımcı olur.

## <a name="confidential-workloads"></a>Gizli iş yükleri

Teklifin istek sahibi ve teklif üretimi ayrı olarak çalışır, ancak aynı fiziksel makinedir. Teklif üretimi merkezi ve tüm varlıklardan teklif taleplerini sunar. Teklif istemek için herhangi bir varlık için, arabirimin düzgün şekilde tanımlanması ve keşfedilebilir olması gerekir.

![Teklif istek sahibi, teklif üretimi ve arabirim arasındaki ilişkileri gösteren diyagram.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Bu soyut model, zaten kullanılabilir olan AESE hizmetinden yararlanarak gizli iş yükü senaryosu için geçerlidir. AESK Kapsayıcılı ve Kubernetes kümesinde bir DaemonSet olarak dağıtılır. Kubernetes, her bir aracı düğümüne dağıtılacak bir pod 'a kaydırılmış bir AESE hizmet kapsayıcısının tek bir örneğini garanti eder. AESM hizmet kapsayıcısı, QE ve PCE şifrelemesi başlatmak üzere SGX-Device-Plugin ' den EPC belleği istetiğinden, yeni Intel SGX quote DaemonSet, SGX-Device-Plugin DaemonSet 'e bağımlılığı vardır.

Oluşturma sırasında ortam değişkenini ayarlayarak, her kapsayıcının işlem dışı teklif üretimini kullanması gerekir `SGX_AESM_ADDR=1` . Kapsayıcı Ayrıca, isteği varsayılan UNIX etki alanı yuvasına yönlendirmekten sorumlu olan libsgx-quote-EX paketini de içermelidir.

Uygulama, daha önce olduğu gibi işlem içi kanıtlama kullanmaya devam edebilir, ancak işlem içi ve işlem dışı işlemler bir uygulama içinde eşzamanlı olarak kullanılamaz. İşlem dışı altyapı varsayılan olarak kullanılabilir ve kaynakları tüketir.

## <a name="sample-implementation"></a>Örnek uygulama

Aşağıdaki Docker dosyası, açık şifreleme temelli bir uygulama için bir örnektir. `SGX_AESM_ADDR=1`Docker dosyasında ortam değişkenini ayarlayın veya dağıtım dosyasında ayarı yapın. Aşağıdaki örnek, Docker dosyası ve dağıtımı için ayrıntılar sağlar. 

  > [!Note] 
  > İşlem dışı kanıtlama düzgün şekilde çalışması için, Intel 'ten alınan libsgx-quote-EX uygulamasının uygulama kapsayıcısında paketlenmesi gerekir.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for details
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# This sets the flag for out-of-process attestation mode. Alternatively you can set this flag on the deployment files.
ENV SGX_AESM_ADDR=1 

CMD make run
```
Alternatif olarak, Deployment. YAML dosyasında işlem dışı kanıtlama modunu ayarlayabilirsiniz. Aşağıdaki adımları uygulayın:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>Sonraki adımlar

[Hızlı başlangıç: Azure CLı kullanarak bir AKS kümesini gizli bilgi işlem düğümleri ile dağıtma](./confidential-nodes-aks-get-started.md)

[Hızlı başlangıç örnekleri gizli kapsayıcıları](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU 'Ları](../virtual-machines/dcv2-series.md)
