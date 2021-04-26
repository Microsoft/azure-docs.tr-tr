---
title: Çalışan kapsayıcı örneğinde komutları yürütme
description: Azure Container Instances ' de çalışmakta olan bir kapsayıcıda bir komutu nasıl yürütebileceğinizi öğrenin
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: 42832910efff67f111c669793798d9ff0e413536
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790788"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Çalışan bir Azure Container Instance 'da bir komut yürütün

Azure Container Instances, çalışmakta olan bir kapsayıcıda komut yürütülmesini destekler. Zaten başlattığınız bir kapsayıcıda komut yürütmek, özellikle de uygulama geliştirme ve sorun giderme sırasında kullanışlıdır. Bu özelliğin en yaygın kullanım örneği, çalışan bir kapsayıcıdaki sorunlarla ilgili hata ayıklama gerçekleştirmek amacıyla etkileşimli bir kabuk başlatmaktır.

## <a name="run-a-command-with-azure-cli"></a>Azure CLı ile bir komut çalıştırma

[Azure CLI][azure-cli]'de [az Container exec][az-container-exec] ile çalışan bir kapsayıcıda bir komut yürütün:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Örneğin, bir NGINX kapsayıcısında bash kabuğu başlatmak için:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Aşağıdaki örnek çıktıda, bash kabuğu çalışan bir Linux kapsayıcısında başlatılır ve içinde yürütülen bir Terminal sağlanır `ls` :

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Bu örnekte, komut Istemi çalışan bir Nanoserver kapsayıcısında başlatılır:

```azurecli
az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
```

```output
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Birden çok kapsayıcılı gruplar

[Kapsayıcı grubunuz](container-instances-container-groups.md) , uygulama kapsayıcısı ve günlük arabası gibi birden çok kapsayıcıyla, komutun çalıştırılacağı kapsayıcının adını belirtin `--container-name` .

Örneğin, *myngınx* kapsayıcı grubunda iki kapsayıcı, *NGINX-App* ve *günlükçü* vardır. *NGINX-uygulama* kapsayıcısında bir kabuğu başlatmak için:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Kısıtlamalar

Azure Container Instances Şu anda [az Container exec][az-container-exec]ile tek bir işlem başlatmayı destekler ve komut bağımsız değişkenlerini geçiremezsiniz. Örneğin, veya gibi komutları zincirlenemez `sh -c "echo FOO && echo BAR"` `echo FOO` .

## <a name="next-steps"></a>Sonraki adımlar

[Azure Container Instances 'de kapsayıcı ve dağıtım sorunlarını gidermeye yönelik](container-instances-troubleshooting.md)diğer sorun giderme araçları ve yaygın dağıtım sorunları hakkında bilgi edinin.

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[azure-cli]: /cli/azure