---
title: Azure Stack 'de OpenShift dağıtma
description: Azure Stack 'de OpenShift dağıtın.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: decc66d2d5abd16c084aa19443dbac54e87a9560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667226"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Azure Stack 'de OpenShift kapsayıcı platformunu veya OKD 'yi dağıtma

OpenShift, Azure Stack dağıtılabilir. Azure ile Azure Stack arasında bazı önemli farklılıklar vardır; böylece dağıtım biraz farklı olur ve özellikleri de biraz farklılık gösterir.

Azure bulut sağlayıcısı şu anda Azure Stack çalışmıyor. Bu nedenle, Azure Stack kalıcı depolama için disk iliştirme 'yi kullanamazsınız. Bunun yerine, NFS, Iscsı, GlusterFS vb. gibi diğer depolama seçeneklerini yapılandırabilirsiniz. Alternatif olarak, CNS ' ı etkinleştirebilir ve kalıcı depolama için GlusterFS kullanabilirsiniz. CNS etkinleştirilirse, GlusterFS kullanımı için ek depolama ile üç ek düğüm dağıtılır.

Azure Stack, OpenShift kapsayıcı platformunu veya OKD 'yi dağıtmak için birkaç yöntemden birini kullanabilirsiniz:

- Gerekli Azure altyapı bileşenlerini el ile dağıtabilir ve ardından [OpenShift kapsayıcı platformu belgelerini](https://docs.openshift.com/container-platform) veya [OKD belgelerini](https://docs.okd.io)takip edebilirsiniz.
- OpenShift kapsayıcı platformu kümesinin dağıtımını kolaylaştıran mevcut bir [Kaynak Yöneticisi şablonunu](https://github.com/Microsoft/openshift-container-platform/) da kullanabilirsiniz.
- Ayrıca, OKD kümesinin dağıtımını kolaylaştıran mevcut bir [Kaynak Yöneticisi şablonunu](https://github.com/Microsoft/openshift-origin) da kullanabilirsiniz.

Kaynak Yöneticisi şablonu kullanılıyorsa doğru dalı seçin (azurestack-Release-3. x). API sürümleri Azure ile Azure Stack arasında farklı olduğu için Azure şablonları çalışmaz. RHEL görüntü başvurusu Şu anda dosyada azuredeploy.jsbir değişken olarak sabit kodludur ve görüntkodda eşleşecek şekilde değiştirilmelidir.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Tüm seçenekler için, Red Hat aboneliği gerekir. Dağıtım sırasında Red Hat Enterprise Linux örneği Red Hat aboneliğine kaydedilir ve OpenShift kapsayıcı platformu için yetkilendirmeleri içeren havuz KIMLIĞINE eklenir.
Geçerli bir Red Hat abonelik Yöneticisi (RHSM) Kullanıcı adı, parola ve havuz KIMLIĞINIZ olduğundan emin olun. Alternatif olarak, bir etkinleştirme anahtarı, kuruluş KIMLIĞI ve havuz KIMLIĞI de kullanabilirsiniz.  Bu bilgileri, ' de oturum açarak doğrulayabilirsiniz https://access.redhat.com .

## <a name="azure-stack-prerequisites"></a>Azure Stack önkoşulları

Bir OpenShift kümesi dağıtmak için Azure Stack ortamınıza bir RHEL görüntüsü (OpenShift kapsayıcı platformu) veya CentOS görüntüsü (OKD) eklenmelidir. Bu görüntüleri eklemek için Azure Stack yöneticinize başvurun. Yönergeleri şurada bulabilirsiniz:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>OpenShift kapsayıcı platformunu veya OKD Kaynak Yöneticisi şablonunu kullanarak dağıtma

Kaynak Yöneticisi şablonunu kullanarak dağıtmak için, giriş parametrelerini sağlamak üzere bir parametreler dosyası kullanırsınız. Dağıtımı daha fazla özelleştirmek için GitHub deposunun çatalını yapın ve uygun öğeleri değiştirin.

Bazı yaygın özelleştirme seçenekleri şunlardır, ancak bunlarla sınırlı değildir:

- Savunma VM boyutu (azuredeploy.jsdeğişken)
- Adlandırma kuralları (azuredeploy.jsdeğişkenler)
- OpenShift küme özellikleri, Hosts dosyası aracılığıyla değiştirildi (deployOpenShift.sh)
- RHEL görüntü başvurusu (azuredeploy.jsdeğişken)

Azure CLı kullanarak dağıtma adımları için [OpenShift kapsayıcı platformu](./openshift-container-platform-3x.md) bölümünde veya [OKD](./openshift-okd.md) bölümünde uygun bölümü izleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Dağıtım sonrası görevler](./openshift-container-platform-3x-post-deployment.md)
- [Azure 'da OpenShift dağıtımında sorun giderme](./openshift-container-platform-3x-troubleshooting.md)