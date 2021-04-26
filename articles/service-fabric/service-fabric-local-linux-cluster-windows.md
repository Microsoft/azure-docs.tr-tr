---
title: Windows 'da Azure Service Fabric Linux kümesi ayarlama
description: Bu makalede, Windows geliştirme makinelerinde çalışan Linux kümelerinin Service Fabric nasıl ayarlanacağı ele alınmaktadır. Bu yaklaşım, platformlar arası geliştirme için yararlıdır.
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087086"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Windows Geliştirici makinenizde Linux Service Fabric kümesi ayarlama

Bu belgede, bir Windows geliştirme makinesinde yerel bir Linux Service Fabric kümesinin nasıl ayarlanacağı ele alınmaktadır. Yerel bir Linux kümesi ayarlamak, Linux kümelerine hedeflenmiş ancak bir Windows makinesinde geliştirilen uygulamaları hızlı bir şekilde test etmek için kullanışlıdır.

## <a name="prerequisites"></a>Önkoşullar
Linux tabanlı Service Fabric kümeleri Windows üzerinde çalışmaz, ancak platformlar arası prototipleme özelliğini etkinleştirmek için, Docker for Windows aracılığıyla dağıtılabilecek bir Linux Service Fabric tek Box kümesi Docker kapsayıcısı sağladık.

Başlamadan önce şunlar gereklidir:

* En az 4 GB RAM
* [Docker for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows) en son sürümü
* Docker, Linux kapsayıcıları modunda çalışıyor olmalıdır

>[!TIP]
> Windows makinenize Docker yüklemek için [Docker belgelerindeki](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)adımları izleyin. Yükledikten sonra [yüklemenizi doğrulayın](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>Yerel bir kapsayıcı oluşturma ve Service Fabric’i ayarlama
Yerel bir Docker kapsayıcısı ayarlamak ve üzerinde Service Fabric bir kümesi çalıştırmak için aşağıdaki adımları çalıştırın:


1. Ana bilgisayarınızda Docker daemon yapılandırmasını aşağıdakiyle güncelleştirin ve Docker daemon programını yeniden başlatın: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    ' Nin güncelleştirilmesi için tavsiye edilen yol şu şekilde yapılır: 

    * Docker simgesi > Docker motoru >
    * Yukarıda listelenen yeni alanları ekleyin
    * Uygulama & yeniden başlatma-Docker Daemon 'ı yeniden başlatarak değişikliklerin etkili olması için.

2. Kümeyi PowerShell aracılığıyla başlatın.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Varsayılan olarak bu, görüntüyü Service Fabric’in en son sürümüyle çeker. Belirli düzeltmeler için Docker Hub 'daki [Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) sayfasına bakın.



3. İsteğe bağlı: genişletilmiş Service Fabric görüntünüzü oluşturun.

    Yeni bir dizinde `Dockerfile` özelleştirilmiş görüntünüzü derlemek için adlı bir dosya oluşturun:

    >[!NOTE]
    >Kapsayıcıya ek programlar veya bağımlılıklar eklemek için, yukarıdaki görüntüyü bir Dockerfile ile uyarlayabilirsiniz.
    >Örneğin, `RUN apt-get install nodejs -y` komutu eklendiğinde, konuk yürütülebilir dosyaları olarak `nodejs` uygulamaları için destek sağlanır.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > Varsayılan olarak bu, görüntüyü Service Fabric’in en son sürümüyle çeker. Belirli düzeltmeler için lütfen [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) sayfasını ziyaret edin.

    Yeniden kullanılabilir görüntünüzü içinden derlemek için, `Dockerfile` bir Terminal açın ve `cd` ardından çalıştırmak için bir Terminal açın `Dockerfile` :

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Bu işlem biraz zaman alır, ancak yalnızca bir kez gereklidir.

    Artık, her ihtiyaç duyduğunuzda Service Fabric yerel bir kopyasını hemen başlatabilirsiniz:

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Kapsayıcı örneğiniz için bir ad belirterek, örneğinizin daha okunaklı bir biçimde işlenebilmesini sağlayın. 
    >
    >Uygulamanız belirli bağlantı noktalarını dinliyorsa, bağlantı noktaları ek `-p` etiketleri kullanılarak belirtilmelidir. Örneğin, uygulamanız 8080 bağlantı noktasını dinliyorsa, şuradaki `-p` etiketini ekleyin:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. Kümenin başlatılması kısa bir süre sürer, aşağıdaki komutu kullanarak günlükleri görüntüleyebilir veya `http://localhost:19080` küme durumunu görüntülemek için panoya atlayabilirsiniz:

    ```powershell 
    docker logs sftestcluster
    ```

5. 4. adımda gözlemlendiği gibi, küme başarıyla dağıtıldıktan sonra, ``http://localhost:19080`` Service Fabric Explorer panosunu bulmak Için Windows makinenizden bölümüne gidebilirsiniz. Bu noktada, Windows Geliştirici makinenizden araçları kullanarak bu kümeye bağlanabilir ve Linux Service Fabric kümelerine yönelik uygulamalar dağıtabilirsiniz. 

    > [!NOTE]
    > Eclipse eklentisi Windows üzerinde şu anda desteklenmemektedir. 

6. İşiniz bittiğinde kapsayıcıyı bu komutla durdurup temizleyin:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Bilinen Sınırlamalar 
 
 Mac’e yönelik kapsayıcıdaki yerel küme çalıştırmaya ilişkin bilinen sınırlandırmalar aşağıda verilmiştir: 
 
 * DNS hizmeti çalışmıyor ve şu anda kapsayıcı içinde desteklenmiyor. [Sorun #132](https://github.com/Microsoft/service-fabric/issues/132)
 * Kapsayıcı tabanlı uygulamaların çalıştırılması, bir Linux ana bilgisayarında SF çalıştırmayı gerektirir. İç içe kapsayıcı uygulamalar şu anda desteklenmiyor.

## <a name="next-steps"></a>Sonraki adımlar
* [Linux üzerinde Yeoman kullanarak ilk Service Fabric Java uygulamanızı oluşturma ve dağıtma](service-fabric-create-your-first-linux-application-with-java.md)
* [Tutulma](./service-fabric-get-started-eclipse.md) ile çalışmaya başlama
* Diğer [Java örneklerine](https://github.com/Azure-Samples/service-fabric-java-getting-started) göz atın
* [Service Fabric destek seçenekleri](service-fabric-support.md) hakkında bilgi edinin


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
