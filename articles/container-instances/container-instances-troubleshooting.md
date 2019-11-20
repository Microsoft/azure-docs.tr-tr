---
title: Sorun giderme Azure Container Instances
description: Dağıtım, çalıştırma veya yönetme Azure Container Instances sık karşılaşılan sorunları giderme hakkında bilgi edinin
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 8b102f31fe25b9c45d85a2dae269fce64be10a5d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147847"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Azure Container Instances genel sorunları giderme

Bu makalede, Azure Container Instances için kapsayıcıları yönetmek veya dağıtmak için sık karşılaşılan sorunların nasıl giderileceği gösterilmektedir. Ayrıca bkz. [sık sorulan sorular](container-instances-faq.md).

Ek desteğe ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) **Yardım + Destek** seçeneklerine bakın.

## <a name="issues-during-container-group-deployment"></a>Kapsayıcı grubu dağıtımı sırasında sorunlar
### <a name="naming-conventions"></a>Adlandırma kuralları

Kapsayıcı belirtimini tanımlarken, belirli parametreler adlandırma kısıtlamalarına uygun olması gerekir. Kapsayıcı grubu özellikleri için belirli gereksinimlere sahip bir tablo aşağıda verilmiştir. Azure adlandırma kuralları hakkında daha fazla bilgi için Azure Mimari Merkezi [adlandırma kuralları][azure-name-restrictions] bölümüne bakın.

| Kapsam | Uzunluk | Büyük/Küçük Harf Kullanımı | Geçerli karakterler | Önerilen düzen | Örnek |
| --- | --- | --- | --- | --- | --- |
| Kapsayıcı grubu adı | 1-64 |Büyük/Küçük harfe duyarsız |İlk veya son karakter dışında bir yerde alfasayısal ve kısa çizgi |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Kapsayıcı adı | 1-64 |Büyük/Küçük harfe duyarsız |İlk veya son karakter dışında bir yerde alfasayısal ve kısa çizgi |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Kapsayıcı bağlantı noktaları | 1 ile 65535 arasında |Tamsayı |1 ile 65535 arasında tamsayı |`<port-number>` |`443` |
| DNS ad etiketi | 5-63 |Büyük/Küçük harfe duyarsız |İlk veya son karakter dışında bir yerde alfasayısal ve kısa çizgi |`<name>` |`frontend-site1` |
| Ortam değişkeni | 1-63 |Büyük/Küçük harfe duyarsız |Alfasayısal ve alt çizgi (_) ilk veya son karakter dışında bir yerde |`<name>` |`MY_VARIABLE` |
| Birim adı | 5-63 |Büyük/Küçük harfe duyarsız |Küçük harfler ve rakamlar ve ilk veya son karakteri hariç her yerde tire. Art arda iki kısa çizgi içeremez. |`<name>` |`batch-output-volume` |

### <a name="os-version-of-image-not-supported"></a>Görüntünün işletim sistemi sürümü desteklenmiyor

Azure Container Instances desteklemediği bir görüntü belirtirseniz, bir `OsVersionNotSupported` hatası döndürülür. Hata, `{0}` dağıtmaya çalıştığınız görüntünün adı olan aşağıdakine benzerdir:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Bu hata en sık, yarı yıllık kanal sürümü 1709 veya 1803 tabanlı Windows görüntülerini dağıtmada desteklenmez ve bu durum desteklenmez. Azure Container Instances sürümünde desteklenen Windows görüntüleri için bkz. [sık sorulan sorular](container-instances-faq.md#what-windows-base-os-images-are-supported).

### <a name="unable-to-pull-image"></a>Görüntü çekilemiyor

Azure Container Instances ilk olarak görüntünüzü çekilemiyor, bir süre yeniden dener. Görüntü çekme işlemi başarısız olmaya devam ederse, ACI sonunda dağıtım başarısız olur ve bir `Failed to pull image` hatası görebilirsiniz.

Bu sorunu çözmek için kapsayıcı örneğini silin ve dağıtımınızı yeniden deneyin. Görüntünün kayıt defterinde mevcut olduğundan ve görüntü adını doğru yazdığınızdan emin olun.

Görüntü çekemiyorum, aşağıdaki gibi olaylar [az Container Show][az-container-show]çıktısında gösterilmektedir:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Kaynak kullanılamıyor hatası

Azure 'da değişen bölgesel kaynak yükü nedeniyle, bir kapsayıcı örneğini dağıtmaya çalışırken şu hatayı alabilirsiniz:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Bu hata, dağıtmayı denediğiniz bölgedeki ağır yük nedeniyle, Kapsayıcınız için belirtilen kaynakların bu anda ayrılamaz olduğunu gösterir. Sorununuzu gidermeye yardımcı olması için aşağıdaki risk azaltma adımlarından birini veya birkaçını kullanın.

* Kapsayıcı dağıtım ayarlarınızın, [Azure Container Instances Için bölge kullanılabilirliği](container-instances-region-availability.md) bölümünde tanımlanan parametreler dahilinde olduğunu doğrulayın
* Kapsayıcının daha düşük CPU ve bellek ayarlarını belirtin
* Farklı bir Azure bölgesine dağıtın
* Daha sonraki bir zamanda dağıtın

## <a name="issues-during-container-group-runtime"></a>Kapsayıcı grubu çalışma zamanı sırasında sorunlar
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Kapsayıcı sürekli olarak çıkar ve yeniden başlatılır (uzun süre çalışan işlem yok)

Kapsayıcı grupları **her zaman** [yeniden başlatma ilkesine](container-instances-restart-policy.md) varsayılan olarak, bu nedenle kapsayıcı grubundaki kapsayıcılar, tamamlandıktan sonra her zaman yeniden başlatılır. Görev tabanlı kapsayıcılar çalıştırmak istiyorsanız bunu **OnFailure** veya **hiçbir** şekilde değiştirmeniz gerekebilir. **OnFailure** ' i belirtirseniz ve yine de sürekli yeniden başlatmalar görüyorsanız, kapsayıcıda veya betikte yürütülen uygulamada bir sorun olabilir.

Uzun süre çalışan süreçler olmadan kapsayıcı grupları çalıştırırken, Ubuntu veya alp gibi görüntülerle yinelenen çıkış ve yeniden başlatmalar görebilirsiniz. Kapsayıcı, etkin durumda olmayan bir işlem içermediğinden, [Exec](container-instances-exec.md) aracılığıyla bağlanma çalışmayacaktır. Bu sorunu çözmek için kapsayıcının çalışır durumda tutulması için kapsayıcı grubu dağıtımınıza aşağıdaki gibi bir start komutu ekleyin.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

Container Instances API ve Azure portal bir `restartCount` özelliği içerir. Bir kapsayıcının yeniden başlatma sayısını denetlemek için, Azure CLı 'de [az Container Show][az-container-show] komutunu kullanabilirsiniz. Aşağıdaki örnek çıktıda (breçekimi için kesilmiş), çıktının sonundaki `restartCount` özelliğini görebilirsiniz.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> Linux dağıtımları için çoğu kapsayıcı görüntüsü, varsayılan komut olarak Bash gibi bir kabuk ayarlar. Kendi üzerindeki bir kabuk uzun süredir çalışan bir hizmet olmadığından, Bu kapsayıcılar hemen çıkış yapar ve varsayılan olarak **her zaman** yeniden başlatma ilkesiyle yapılandırıldığında bir yeniden başlatma döngüsüne girer.

### <a name="container-takes-a-long-time-to-start"></a>Kapsayıcının başlaması uzun sürüyor

Azure Container Instances içinde kapsayıcı başlatma zamanına katkıda bulunan üç ana etken şunlardır:

* [Görüntü boyutu](#image-size)
* [Görüntü konumu](#image-location)
* [Önbelleğe alınmış görüntüler](#cached-images)

Windows görüntülerinin [ek konuları](#cached-images)vardır.

#### <a name="image-size"></a>Görüntü boyutu

Kapsayıcının başlaması uzun zaman alıyorsa, ancak sonuç olarak başarılı olduktan sonra kapsayıcı resminizin boyutuna bakarak başlayın. Azure Container Instances kapsayıcı görüntünüzü isteğe bağlı olarak çeker, gördüğünüz başlangıç zamanı doğrudan boyutuyla ilgilidir.

Docker CLı 'daki `docker images` komutunu kullanarak kapsayıcı Resminizin boyutunu görüntüleyebilirsiniz:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Görüntü boyutlarının küçük tutulması için gereken anahtar, son görüntünüzün çalışma zamanında gerekli olmayan herhangi bir şey içermediğinden emin olmanızı sağlamaktır. Bunu yapmanın bir yolu [çok aşamalı Derlemelerle][docker-multi-stage-builds]aynıdır. Çok aşamalı derlemeler, son görüntünün yalnızca uygulamanız için gereken yapıtları içerdiğinden ve derleme zamanında gerekli olan ek içeriklerin hiçbirini içermediğinden emin olmanızı kolaylaştırır.

#### <a name="image-location"></a>Görüntü konumu

Kapsayıcının başlangıç saatine görüntü çekmenin etkilerini azaltmanın bir başka yolu da kapsayıcı görüntüsünü [Azure Container Registry](/azure/container-registry/) kapsayıcı örneklerini dağıtmak istediğiniz bölgede barındırmaktır. Bu, kapsayıcı görüntüsünün yolculuğu gereken ağ yolunu kısaltır ve indirme süresini önemli ölçüde kısaltır.

#### <a name="cached-images"></a>Önbelleğe alınmış görüntüler

Azure Container Instances, `nanoserver:1809`, `servercore:ltsc2019`ve `servercore:1809`dahil olmak üzere ortak [Windows temel görüntülerinde](container-instances-faq.md#what-windows-base-os-images-are-supported)oluşturulan görüntülerin kapsayıcı başlangıç süresini hızlandırmaya yardımcı olmak için bir önbelleğe alma mekanizması kullanır. `ubuntu:1604` ve `alpine:3.6` gibi yaygın olarak kullanılan Linux görüntüleri de önbelleğe alınır. Önbelleğe alınmış görüntülerin ve etiketlerin güncel listesi için [önbelleğe alınmış görüntüleri Listele][list-cached-images] API 'sini kullanın.

> [!NOTE]
> Azure Container Instances 'da Windows Server 2019 tabanlı görüntülerin kullanımı önizlemededir.

#### <a name="windows-containers-slow-network-readiness"></a>Windows kapsayıcıları yavaş ağ hazırlığı

İlk oluşturma sırasında, Windows kapsayıcılarının 30 saniyeye kadar (veya daha uzun durumlarda, nadiren) gelen veya giden bağlantısı olmayabilir. Kapsayıcı uygulamanızın bir Internet bağlantısı olması gerekiyorsa, Internet bağlantısı kurmak için 30 saniye izin vermek üzere gecikme ve yeniden deneme mantığı ekleyin. İlk kurulumdan sonra kapsayıcı ağı uygun şekilde sürdürülür.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Temel alınan Docker API 'sine bağlanılamıyor veya ayrıcalıklı kapsayıcılar Çalıştır

Azure Container Instances, kapsayıcı gruplarını barındıran temeldeki altyapıya doğrudan erişim sunmaz. Bu, kapsayıcının ana bilgisayarında çalışan ve ayrıcalıklı kapsayıcıları çalıştıran Docker API 'sine erişimi içerir. Docker etkileşimine ihtiyacınız varsa, ACI API 'sinin nasıl desteklediğini görmek için [rest başvurusu belgelerine](https://aka.ms/aci/rest) bakın. Eksik bir sorun varsa, [acı geri bildirim forumlarında](https://aka.ms/aci/feedback)bir istek gönderin.

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Eşleşmeyen bağlantı noktaları nedeniyle kapsayıcı grubu IP adresi erişilebilir olmayabilir

Azure Container Instances, normal Docker yapılandırmasıyla benzer bağlantı noktası eşlemeyi henüz desteklememektedir. Bir kapsayıcı grubunun IP adresini, olması gerektiğine inanıyorsanız erişilebilir değilse, kapsayıcı grubunuza `ports` özelliği ile birlikte kullanıma sunabileceğiniz aynı bağlantı noktalarını dinlemek için kapsayıcı görüntünüzü yapılandırdığınızdan emin olun.

Azure Container Instances Container Image 'da yapılandırdığınız bağlantı noktasında dinleyebildiğini doğrulamak istiyorsanız, bağlantı noktasını kullanıma sunan `aci-helloworld` görüntüsünün bir dağıtımını test edin. Ayrıca `aci-helloworld` uygulamasını, bağlantı noktasında dinleyeceği şekilde çalıştırın. `aci-helloworld`, `PORT` dinlediği 80 varsayılan bağlantı noktasını geçersiz kılmak için isteğe bağlı bir ortam değişkenini kabul eder. Örneğin, 9000 bağlantı noktasını test etmek için, kapsayıcı grubunu oluştururken [ortam değişkenini](container-instances-environment-variables.md) ayarlayın:

1. 9000 numaralı bağlantı noktasını kullanıma sunmak için kapsayıcı grubunu ayarlayın ve bağlantı noktası numarasını ortam değişkeninin değeri olarak geçirin. Örnek bash kabuğu için biçimlendirilir. PowerShell veya komut Istemi gibi başka bir kabuğu tercih ediyorsanız, değişken atamasını uygun şekilde ayarlamanız gerekir.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. `az container create`komut çıkışında kapsayıcı grubunun IP adresini bulun. **IP**değerini arayın. 
1. Kapsayıcı başarıyla sağlandıktan sonra, tarayıcınızda kapsayıcı uygulamasının IP adresine ve bağlantı noktasına gidin, örneğin: `192.0.2.0:9000`. 

    "Hoş geldiniz Azure Container Instances!" görmeniz gerekir Web uygulaması tarafından görünen ileti.
1. Kapsayıcı ile işiniz bittiğinde `az container delete` komutunu kullanarak kaldırın.

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Sonraki adımlar

Kapsayıcılarınızın hatalarını ayıklamanıza yardımcı olmak için [kapsayıcı günlüklerini ve olaylarını nasıl alacağınızı](container-instances-get-logs.md) öğrenin.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
