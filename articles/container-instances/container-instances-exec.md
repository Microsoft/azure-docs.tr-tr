---
title: Azure Container Instances içinde çalışan kapsayıcılarda komutları yürütme
description: Azure Container Instances ' de çalışmakta olan bir kapsayıcıda bir komutu nasıl yürütebileceğinizi öğrenin
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: a8583cf605891631a2bce6914b24525aebd59ea0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325991"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Çalışan bir Azure Container Instance 'da bir komut yürütün

Azure Container Instances, çalışan bir kapsayıcıda bir komutun yürütülmesini destekler. Zaten başlattığınız kapsayıcıda bir komutun çalıştırılması, özellikle uygulama geliştirme ve sorun giderme işlemleri sırasında yararlıdır. Bu özelliğin en yaygın kullanımı, çalışan bir kapsayıcıdaki sorunları ayıklayabilmeniz için etkileşimli bir kabuk başlatma.

## <a name="run-a-command-with-azure-cli"></a>Azure CLı ile bir komut çalıştırma

[Azure CLI][azure-cli]'de [az Container exec][az-container-exec] ile çalışan bir kapsayıcıda bir komut yürütün:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Örneğin, bir NGINX kapsayıcısında bash kabuğu başlatmak için:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Aşağıdaki örnek çıktıda, bash kabuğu çalışan bir Linux kapsayıcısında başlatılır ve içinde `ls` yürütülen bir Terminal sağlanır:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Bu örnekte, komut Istemi çalışan bir Nanoserver kapsayıcısında başlatılır:

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
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

[Kapsayıcı grubunuz](container-instances-container-groups.md) , uygulama kapsayıcısı ve günlük arabası gibi birden çok kapsayıcıyla, komutun `--container-name`çalıştırılacağı kapsayıcının adını belirtin.

Örneğin, *myngınx* kapsayıcı grubunda iki kapsayıcı, *NGINX-App* ve *günlükçü*vardır. *NGINX-uygulama* kapsayıcısında bir kabuğu başlatmak için:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Kısıtlamalar

Azure Container Instances Şu anda [az Container exec][az-container-exec]ile tek bir işlem başlatmayı destekler ve komut bağımsız değişkenlerini geçiremezsiniz. Örneğin, `sh -c "echo FOO && echo BAR"` `echo FOO`veya gibi komutları zincirlenemez.

## <a name="next-steps"></a>Sonraki adımlar

[Azure Container Instances 'de kapsayıcı ve dağıtım sorunlarını gidermeye yönelik](container-instances-troubleshooting.md)diğer sorun giderme araçları ve yaygın dağıtım sorunları hakkında bilgi edinin.

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure