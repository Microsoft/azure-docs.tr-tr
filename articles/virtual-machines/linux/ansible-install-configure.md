---
title: Hızlı başlangıç-Azure 'da Linux sanal makinelerine erişilebilir bir şekilde yükler
description: Bu hızlı başlangıçta, Ubuntu, CentOS ve SLES üzerinde Azure kaynaklarını yönetmek için nasıl yüklenip yapılandırılacağı hakkında bilgi edinin
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: a1ea5814ffd201456a2751b37b77f3062cac789a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037064"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Hızlı başlangıç: Azure 'da Linux sanal makinelerine erişilebilir bir şekilde yüklemeyin

Ansible, ortamınızdaki kaynakların dağıtımını ve yapılandırılmasını otomatikleştirmenizi sağlar. Bu makalede, en yaygın Linux kaldırmalar için nasıl yapılandırma yapılacağı gösterilmektedir. Diğer distrolar üzerinde anormal yükleme yapmak için, belirli platformunuz için yüklü paketleri ayarlayın. 

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Linux veya Linux sanal makinesine erişim** - Linux makineniz yoksa [Linux sanal makinesi](/azure/virtual-network/quick-create-cli) oluşturun.

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Bir Azure Linux sanal makinesine Ansible yükleme

Linux makinenizde oturum açın ve Ansible yükleme adımları için aşağıdaki dağıtımlardan birini seçin:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Bu bölümde, CentOS 'ı anda kullanacak şekilde yapılandırırsınız.

1. Bir terminal penceresi açın.

1. Azure Python SDK modülleri için gereken paketleri yüklemek üzere aşağıdaki komutu girin:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Gerekli paketleri yüklemek için aşağıdaki komutu girin:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Azure kimlik bilgilerini oluşturun](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Bu bölümde, Ubuntu 'ı kullanmak üzere yapılandırırsınız.

1. Bir terminal penceresi açın.

1. Azure Python SDK modülleri için gereken paketleri yüklemek üzere aşağıdaki komutu girin:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Gerekli paketleri yüklemek için aşağıdaki komutu girin:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Azure kimlik bilgilerini oluşturun](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Bu bölümde, SLES 'yi kullanmak için yapılandıracaksınız.

1. Bir terminal penceresi açın.

1. Azure Python SDK modülleri için gereken paketleri yüklemek üzere aşağıdaki komutu girin:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Gerekli paketleri yüklemek için aşağıdaki komutu girin:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Çakışan Python şifreleme paketini kaldırmak için aşağıdaki komutu girin:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Azure kimlik bilgilerini oluşturun](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Azure kimlik bilgilerini oluşturma

Anormal kimlik bilgilerini yapılandırmak için aşağıdaki bilgilere ihtiyacınız vardır:

* Azure abonelik KIMLIĞINIZ 
* Hizmet sorumlusu değerleri

Anormal Tower veya Jenkins kullanıyorsanız, hizmet sorumlusu değerlerini ortam değişkenleri olarak bildirin.

Aşağıdaki tekniklerden birini kullanarak anormal kimlik bilgilerini yapılandırın:

- [Ansible kimlik bilgileri dosyası oluşturma](#file-credentials)
- [Ansible ortam değişkenlerini kullanma](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Ansible kimlik bilgileri dosyası oluşturma

Bu bölümde, kimlik bilgilerini sağlamak için bir yerel kimlik bilgileri dosyası oluşturacaksınız. 

Anormal kimlik bilgilerini tanımlama hakkında daha fazla bilgi için bkz. [Azure modüllerine kimlik bilgileri sağlama](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Bir geliştirme ortamı için konak sanal makinesinde `credentials` adlı bir dosya oluşturun:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Dosyasına aşağıdaki satırları ekleyin. Yer tutucuları hizmet sorumlusu değerleriyle değiştirin.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Dosyayı kaydedin ve kapatın.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Ansible ortam değişkenlerini kullanma

Bu bölümde, anormal kimlik bilgilerinizi yapılandırmak için hizmet sorumlusu değerlerini dışarı aktarcaksınız.

1. Bir terminal penceresi açın.

1. Hizmet sorumlusu değerlerini dışarı aktar:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Yapılandırmayı doğrulama

Başarılı yapılandırmayı doğrulamak için, bir Azure Kaynak grubu oluşturmak için anda kullanın.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"] 
> [Hızlı başlangıç: Azure 'da bir Linux sanal makinesini kullanarak yapılandırma](./ansible-create-vm.md)