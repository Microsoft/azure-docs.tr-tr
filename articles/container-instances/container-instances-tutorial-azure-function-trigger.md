---
title: Öğretici-Azure işlevi tarafından Azure Container Instances tetikleme
description: Azure Container Instances oluşturmayı otomatikleştirmek için HTTP ile tetiklenen, sunucusuz bir PowerShell işlevi oluşturma
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 00bd017b0bcff6386e678802c301087819792744
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179984"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Öğretici: Bir kapsayıcı grubu oluşturmak için HTTP ile tetiklenen bir Azure işlevi kullanın

[Azure işlevleri](../azure-functions/functions-overview.md) , bir http isteği, süreölçer veya bir Azure depolama kuyruğundaki ileti gibi çeşitli olaylara yanıt olarak komut dosyalarını veya kodu çalıştıran sunucusuz bir bilgi işlem hizmetidir.

Bu öğreticide, bir HTTP isteği alan ve bir [kapsayıcı grubunun](container-instances-container-groups.md)dağıtımını tetikleyen bir Azure işlevi oluşturacaksınız. Bu örnekte, Azure Container Instances ' de otomatik olarak kaynak oluşturmak için Azure Işlevleri kullanmanın temelleri gösterilmektedir. Daha karmaşık senaryolar veya diğer olay tetikleyicileri için örneği değiştirin veya genişletin. 

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Temel HTTP ile tetiklenen bir PowerShell işlevi oluşturmak için [Azure işlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) ile Visual Studio Code kullanın.
> * İşlev uygulamasında bir kimliği etkinleştirin ve Azure kaynakları oluşturmak için izin verin.
> * Tek kapsayıcılı bir kapsayıcı grubunun dağıtımını otomatik hale getirmek için PowerShell işlevini değiştirin ve yeniden yayımlayın.
> * Kapsayıcının HTTP tarafından tetiklenen dağıtımını doğrulayın.

> [!IMPORTANT]
> Azure Işlevleri için PowerShell Şu anda önizlemededir. Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="prerequisites"></a>Önkoşullar

İşletim sisteminde Azure Işlevleri ile Visual Studio Code yüklemek ve kullanmak için bkz. Önkoşullar için [Azure 'da Ilk PowerShell işlevinizi oluşturma](../azure-functions/functions-create-first-function-powershell.md#prerequisites) .

Bu makaledeki bazı adımlarda Azure CLı kullanılır. Bu adımları tamamlayabilmeniz için Azure Cloud Shell veya yerel bir Azure CLı yüklemesi kullanabilirsiniz. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Temel PowerShell işlevi oluşturma

HTTP tetikleyici şablonunu kullanarak bir PowerShell işlevi oluşturmak için [Azure 'da Ilk PowerShell işlevinizi oluşturma](../azure-functions/functions-create-first-function-powershell.md) bölümündeki adımları izleyin. Varsayılan Azure işlevi adı **Httptrigger**' i kullanın. Hızlı başlangıçta gösterildiği gibi, işlevi yerel olarak test edin ve projeyi Azure 'da bir işlev uygulamasında yayımlayın. Bu örnek, bir metin dizesi döndüren temel bir HTTP ile tetiklenen işlevdir. Bu makaledeki sonraki adımlarda, işlevi bir kapsayıcı grubu oluşturacak şekilde değiştirirsiniz.

Bu makalede, projeyi, işlev uygulaması adına (Ayrıca *myfunctionapp*) göre otomatik olarak adlandırılan bir Azure Kaynak grubundaki *myfunctionapp*adını kullanarak yayımladığınızı varsaymaktadır. Daha sonraki adımlarda benzersiz işlev uygulamanızın adını ve kaynak grubu adını değiştirin.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>İşlev uygulamasında Azure tarafından yönetilen bir kimliği etkinleştirme

Artık işlev uygulamanızda sistem tarafından atanan bir [yönetilen kimliği](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#adding-a-system-assigned-identity) etkinleştirin. Uygulamayı çalıştıran PowerShell ana bilgisayarı, bu kimlik kullanılarak otomatik olarak kimlik doğrulaması yapabilir, böylece işlevleri, kimliğin erişim izni verilen Azure hizmetleri üzerinde işlem yapmasını sağlar. Bu öğreticide, işlev uygulamasının kaynak grubunda kaynak oluşturmak için yönetilen kimlik izinlerini verirsiniz. 

Önce, işlev uygulamasının kaynak grubunun KIMLIĞINI almak ve bir ortam değişkeninde depolamak için [az Group Show][az-group-show] komutunu kullanın. Bu örnek, bir bash kabuğu 'nda komutunu çalıştırdığınız varsayılır.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

İşlev uygulamasına yerel bir kimlik atamak ve kaynak grubuna katkıda bulunan bir rol atamak için [az functionapp Identity App Assign][az-functionapp-identity-app-assign] ' i çalıştırın. Bu rol, kimliğin kaynak grubunda kapsayıcı grupları gibi ek kaynaklar oluşturmasına izin verir.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>HttpTrigger işlevini Değiştir

Bir kapsayıcı grubu oluşturmak için **Httptrigger** işlevinin PowerShell kodunu değiştirin. İşlevin dosyasında `run.ps1` aşağıdaki kod bloğunu bulun. Bu kod, işlev URL 'sinde sorgu dizesi olarak geçirilmemişse bir ad değeri görüntüler:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Bu kodu aşağıdaki örnek blokla değiştirin. Burada, sorgu dizesinde bir ad değeri geçirilirse, [New-AzContainerGroup][new-azcontainergroup] cmdlet 'ini kullanarak bir kapsayıcı grubunu adlandırmak ve oluşturmak için kullanılır. *Myfunctionapp* kaynak grubu adını, işlev uygulamanızın kaynak grubu adıyla değiştirdiğinizden emin olun:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

Bu örnek, `alpine` görüntüyü çalıştıran tek bir kapsayıcı örneğinden oluşan bir kapsayıcı grubu oluşturur. Kapsayıcı tek `echo` bir komut çalıştırır ve sonra sonlanır. Gerçek dünyada bir örnekte, toplu iş çalıştırmak için bir veya daha fazla kapsayıcı grubu oluşturulmasını tetikleyebilirsiniz.
 
## <a name="test-function-app-locally"></a>İşlev uygulamasını yerel olarak test etme

İşlev uygulaması projesini Azure 'a yeniden yayımlayabilmeniz için işlevin düzgün şekilde yerel olarak çalıştığından emin olun. [PowerShell hızlı başlangıç](../azure-functions/functions-create-first-function-powershell.md)bölümünde gösterildiği gibi, PowerShell betiğine bir yerel kesme noktası ve bunun üzerine `Wait-Debugger` bir çağrı ekleyin. Hata ayıklama Kılavuzu için bkz. [PowerShell Azure işlevleri 'nde yerel olarak hata ayıklama](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Azure işlev uygulamasını yeniden yayımlama

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi Azure 'da var olan işlev uygulamasına yeniden yayımlamanız zaman alır.

> [!NOTE]
> İşlevlerinizi Azure 'da yayımlamadan `Wait-Debugger` önce tüm çağrıları kaldırmayı unutmayın.

1. Visual Studio Code ' de, komut paleti ' ni açın. Araması yapın ve seçin `Azure Functions: Deploy to function app...`.
1. Zip ve dağıtım için geçerli çalışma klasörünü seçin.
1. Aboneliği ve ardından mevcut işlev uygulamasının adını (*myfunctionapp*) seçin. Önceki dağıtımın üzerine yazmak istediğinizi onaylayın.

İşlev uygulamanız oluşturulduktan sonra bir bildirim görüntülenir ve dağıtım paketi uygulanır. Güncelleştirdiğiniz Azure kaynakları dahil olmak üzere oluşturma ve dağıtım sonuçlarını görüntülemek için bu bildirimde **çıktıyı görüntüle** ' yi seçin.

## <a name="run-the-function-in-azure"></a>İşlevi Azure 'da çalıştırma

Dağıtım başarıyla tamamlandıktan sonra işlev URL 'sini alın. Örneğin, **Azure 'u kullanın: Visual** Studio Code 'da, **httptrigger** işlev URL 'sini kopyalamak veya [Azure Portal](../azure-functions/functions-create-first-azure-function.md#test-the-function)işlev URL 'sini almak için işlevler alanı.

İşlev URL 'SI benzersiz bir kod içerir ve şu biçimdedir:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>İşlevi bir ad geçirmeden Çalıştır

İlk test olarak, `curl` komutunu çalıştırın ve bir `name` sorgu dizesi eklemeden işlev URL 'sini geçirin. İşlevinizin benzersiz kodunu eklediğinizden emin olun.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

İşlev 400 durum kodunu ve metnini `Please pass a name on the query string or in the request body`döndürür:

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>İşlevi Çalıştır ve bir kapsayıcı grubunun adını geçir

Şimdi bir kapsayıcı `curl` grubunun adını (*mycontainergroup*) bir sorgu dizesi `&name=mycontainergroup`olarak ekleyerek komutu çalıştırın:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

İşlev 200 durum kodunu döndürür ve kapsayıcı grubunun oluşturulmasını tetikler:

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Kapsayıcının [az Container logs][az-container-logs] komutuyla çalıştığını doğrulayın:

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Örnek çıktı:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide oluşturduğunuz kaynakları artık gerekmiyorsa, kaynak grubunu ve içerdiği tüm kaynakları kaldırmak için [az Group Delete][az-group-delete] komutunu çalıştırabilirsiniz. Bu komut, oluşturduğunuz kapsayıcı kayıt defterinin yanı sıra çalışan kapsayıcıyı ve tüm ilişkili kaynakları siler.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir HTTP isteği alan ve bir kapsayıcı grubunun dağıtımını tetikleyen bir Azure işlevi oluşturdunuz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Temel HTTP ile tetiklenen bir PowerShell işlevi oluşturmak için Azure Işlevleri Uzantısı ile Visual Studio Code kullanın.
> * İşlev uygulamasında bir kimliği etkinleştirin ve Azure kaynakları oluşturmak için izin verin.
> * Tek kapsayıcılı bir kapsayıcı grubunun dağıtımını otomatik hale getirmek için PowerShell işlev kodunu değiştirin.
> * Kapsayıcının HTTP tarafından tetiklenen dağıtımını doğrulayın.

Kapsayıcılı bir işi başlatma ve izlemeye yönelik ayrıntılı bir örnek için bkz. PowerShell Azure Işlevleri ve Azure Container Instances ve birlikte gelen [kod örneği](https://github.com/anthonychu/functions-powershell-run-aci) [Ile olay temelli sunucusuz kapsayıcılar](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) .

Azure işlevleri oluşturma ve bir işlevler projesi yayımlama hakkında ayrıntılı yönergeler için bkz. [Azure işlevleri belgeleri](/azure/azure-functions/) . 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
