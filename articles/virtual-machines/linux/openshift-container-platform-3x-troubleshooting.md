---
title: Azure 'da OpenShift kapsayıcı platformu 3,11 dağıtımında sorun giderme
description: Azure 'da Openshıft kapsayıcı platformu 3,11 dağıtımında sorun giderin.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 1915cce1878b9b7ec058c13167e03c3c318f3668
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035484"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Azure 'da OpenShift kapsayıcı platformu 3,11 dağıtımında sorun giderme

OpenShift kümesi başarılı bir şekilde dağıtımazsa, Azure portal hata çıktısı sağlayacaktır. Çıktının okunması zor olabilir, bu da sorunu belirlemeyi zorlaştırır. Bu çıktıyı, 3, 4 veya 5 çıkış kodu için hızlıca tarayın. Aşağıda bu üç çıkış kodu hakkında bilgi verilmektedir:

- Çıkış kodu 3: Red Hat aboneliğinizin Kullanıcı adı/parolası veya kuruluş KIMLIĞI/etkinleştirme anahtarınız hatalı
- Çıkış kodu 4: Red Hat havuzu KIMLIĞINIZ yanlış veya kullanılabilir yetkilendirmeler yok
- Çıkış kodu 5: Docker Ince havuz birimi sağlanamıyor

Diğer tüm çıkış kodları için, günlük dosyalarını görüntülemek için SSH aracılığıyla ana bilgisayara bağlanın.

**OpenShift kapsayıcı platformu 3,11**

Anerişilebilir PlayBook konağına SSH. Şablon veya Market teklifi için savunma konağını kullanın. Savunma sırasında kümedeki diğer tüm düğümlere SSH ekleyebilirsiniz (Master, infra, CNS, COMPUTE). Günlük dosyalarını görüntülemek için kök olmanız gerekir. Kök, varsayılan olarak SSH erişimi için devre dışıdır, bu nedenle diğer düğümlere SSH için kök kullanmayın.

**OKD**

Anerişilebilir PlayBook konağına SSH. OKD şablonu (sürüm 3,9 ve önceki sürümler) için ana-0 konağını kullanın. OKD şablonu için (sürüm 3,10 ve üzeri), savunma konağını kullanın. Anormal PlayBook ana bilgisayarında, kümedeki diğer tüm düğümlere SSH ekleyebilirsiniz (Master, infra, CNS, COMPUTE). Günlük dosyalarını görüntülemek için kök (sudo su-) olmanız gerekir. Kök, varsayılan olarak SSH erişimi için devre dışıdır, bu nedenle diğer düğümlere SSH için kök kullanmayın.

## <a name="log-files"></a>Günlük dosyaları

Konak hazırlama betikleri için günlük dosyaları (stderr ve STDOUT) tüm konaklarda `/var/lib/waagent/custom-script/download/0` bulunur. Konağın hazırlanması sırasında bir hata oluştuysa, hatayı öğrenmek için bu günlük dosyalarını görüntüleyin.

Hazırlama betikleri başarıyla çalıştırılmışsa, anlabilen PlayBook konağının `/var/lib/waagent/custom-script/download/1` dizinindeki günlük dosyalarının incelenmesi gerekir. OpenShift 'in gerçek yüklemesi sırasında hata oluştuysa, stdout dosyasında hata görüntülenir. Daha fazla yardım için desteğe başvurmak üzere bu bilgileri kullanın.

Örnek çıkış

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

Yükleme sırasında en yaygın hatalar şunlardır:

1. Özel anahtarda parola vardır
2. Özel anahtarla Anahtar Kasası gizli anahtarı doğru bir şekilde oluşturulmadı
3. Hizmet sorumlusu kimlik bilgileri yanlış girildi
4. Hizmet sorumlusu, kaynak grubuna katkıda bulunan erişimi yok

### <a name="private-key-has-a-passphrase"></a>Özel anahtarda bir parola vardır

SSH için iznin reddedildiğini belirten bir hata görürsünüz. Özel anahtardaki bir parolayı denetlemek için anerişilebilir PlayBook konağına SSH.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Özel anahtarla Anahtar Kasası gizli anahtarı doğru bir şekilde oluşturulmadı

Özel anahtar, anerişilebilir PlayBook konağına kopyalanır-~/. ssh/id_rsa. Bu dosyanın doğru olduğundan emin olun. Anerişilebilir PlayBook ana bilgisayarındaki küme düğümlerinden birine bir SSH oturumu açarak test edin.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Hizmet sorumlusu kimlik bilgileri yanlış girildi

Şablona veya Market teklifine giriş sağlarken yanlış bilgiler sağlandı. Hizmet sorumlusu için doğru AppID (ClientID) ve parolayı (clientSecret) kullandığınızdan emin olun. Aşağıdaki Azure CLI komutunu vererek doğrulayın.

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Hizmet sorumlusu, kaynak grubuna katkıda bulunan erişimi yok

Azure bulut sağlayıcısı etkinse kullanılan hizmet sorumlusu, kaynak grubuna katkıda bulunan erişimine sahip olmalıdır. Aşağıdaki Azure CLI komutunu vererek doğrulayın.

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Ek araçlar

Bazı hatalarda, daha fazla bilgi edinmek için aşağıdaki komutları da kullanabilirsiniz:

1. systemctl durum \<Hizmeti >
2. journalctl-XE
