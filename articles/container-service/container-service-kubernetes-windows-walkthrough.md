---
title: "Windows için Azure Kubernetes kümesi | Microsoft Docs"
description: "Azure Container Service’te Windows kapsayıcıları için Kubernetes kümesi dağıtma ve kullanmaya başlama"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: eb3af43b8a13eaaebfa9147848383ff889119d97
ms.lasthandoff: 04/03/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Kapsayıcı Hizmetinde Kubernetes ve Windows kapsayıcılarına başlangıç


Bu makalede, Azure Container Service’te Windows kapsayıcılarını çalıştırmaya yönelik Windows düğümleri içeren bir Kubernetes kümesinin nasıl oluşturulacağı açıklanmaktadır. 

> [!NOTE]
> Azure Container Service’te Kubernetes ile Windows kapsayıcıları desteği önizleme aşamasındadır. Windows düğümleri içeren bir Kubernetes kümesi oluşturmak için Azure portalını ya da bir Resource Manager şablonu kullanın. Bu özellik, şu anda Azure CLI 2.0 ile desteklenmemektedir.
>



Aşağıdaki görüntüde, Azure Container Service’te bir Linux ana düğümü ve iki Windows aracı düğümü içeren bir Kubernetes kümesinin mimarisi gösterilmektedir. 

* Linux ana düğümü, Kubernetes REST API’ye hizmet verir ve SSH tarafından bağlantı noktası 22’de veya `kubectl` tarafından bağlantı noktası 443'te erişilebilir durumdadır. 
* Windows aracı düğümleri, bir Azure kullanılabilirlik kümesinde gruplandırılıp kapsayıcılarınızı çalıştırır. Windows düğümlerine, bir RDP SSH tüneli üzerinden ana düğüm aracılığıyla erişilebilir. Azure Load Balancer kuralları, kullanıma sunulan hizmetlere göre kümeye dinamik olarak eklenir.


![Azure’da Kubernetes kümesinin görüntüsü](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

Tüm VM’ler aynı gizli sanal ağ üzerindedir ve birbirlerine tam olarak erişilebilir. Tüm VM’ler, bir kubelet, Docker ve bir ara sunucu çalıştırır.

## <a name="prerequisites"></a>Ön koşullar


* **SSH RSA ortak anahtarı**: Portal veya Azure hızlı başlangıç şablonlarından biri ile dağıtım yaparken, Azure Container Service sanal makinelerinde kimlik doğrulamak için bir SSH RSA ortak anahtarı belirtmeniz gerekir. Güvenli Kabuk (SSH) RSA anahtarları oluşturmak için [OS X ve Linux](../virtual-machines/linux/mac-create-ssh-keys.md) veya [Windows](../virtual-machines/linux/ssh-from-windows.md) kılavuzuna bakın. 

* **Hizmet sorumlusu istemci kimliği ve gizli dizisi**: Daha fazla bilgi ve rehberlik için bkz. [Kubernetes kümelerinde hizmet sorumlusu hakkında](container-service-kubernetes-service-principal.md).




## <a name="create-the-cluster"></a>Kümeyi oluşturma

Azure portalını kullanarak Windows aracı düğümleri içeren bir [Kubernetes kümesi oluşturabilirsiniz](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal). Kümeyi oluştururken aşağıdaki ayarlara dikkat edin:

* **Temel bilgiler** dikey penceresinin **Düzenleyici** bölümünde **Kubernetes**'i seçin. 

  ![Kubernetes orchestrator’ı seçin](media/container-service-kubernetes-windows-walkthrough/portal-select-kubernetes.png)

* **Ana yapılandırma** dikey penceresinde Linux ana düğümleri için kullanıcı kimlik bilgilerini ve hizmet sorumlusu kimlik bilgilerini girin. 1, 3 ya da 5 ana seçin.

* **Aracı yapılandırması** dikey penceresinin **İşletim sistemi** bölümünde **Windows (önizleme)** öğesini seçin. Windows aracısı düğümleri için yönetici kimlik bilgilerini girin.

  ![Windows aracılarını seçin](media/container-service-kubernetes-windows-walkthrough/portal-select-windows.png)

Ayrıntılar için, bkz. [Azure Container Service kümesi dağıtma](container-service-deployment.md).

## <a name="connect-to-the-cluster"></a>Kümeye bağlanma

Yerel bilgisayarınızdan Kubernetes kümesi ana düğümüne bağlanmak için `kubectl` komut satırı aracını kullanın. `kubectl` yüklemesi ve ayarlanması hakkında daha fazla bilgi için bkz. [Azure Container Service kümesine bağlanma](container-service-connect.md#connect-to-a-kubernetes-cluster). `kubectl` komutlarını kullanarak Kubernetes web kullanıcı arabirimine erişip Windows kapsayıcısı iş yükleri oluşturabilir ve yönetebilirsiniz.

## <a name="create-your-first-kubernetes-service"></a>İlk Kubernetes hizmetinizi oluşturma

Kümeyi oluşturup `kubectl` ile bağlandıktan sonra temel bir Windows web uygulamasını başlatmayı ve İnternet'te kullanıma sunmayı deneyebilirsiniz. Bu örnekte, bir YAML dosyası kullanılarak kapsayıcı kaynakları belirtilmekte ve `kubctl apply` ile kapsayıcı oluşturulmaktadır.

1. Düğümlerinizin listesini görmek için `kubectl get nodes` yazın. Düğümlerin tüm ayrıntılarını öğrenmek istiyorsanız şunu yazın:  

    ```
    kubectl get nodes -o yaml
    ```

2. `simpleweb.yaml` adlı bir dosya oluşturun ve aşağıdakini kopyalayın. Bu dosya, [Docker Hub](https://hub.docker.com/r/microsoft/windowsservercore/)’dan Windows Server 2016 Sunucu Çekirdeği temel işletim sistemi görüntüsünü kullanarak bir web uygulaması ayarlar.  

```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add('http://*:80/') ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at http://*:80/') ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

      
> [!NOTE] 
> Yapılandırmaya `type: LoadBalancer` dahildir. Bu ayar, hizmetin bir Azure Load Balancer üzerinden İnternet'te kullanıma sunulmasına neden olur. Daha fazla bilgi edinmek için bkz. [Azure Container Service’teki bir Kubernetes kümesinde yük dengeleme kapsayıcıları](container-service-kubernetes-load-balancing.md).
>

## <a name="start-the-application"></a>Uygulamayı başlatma

1. Uygulamayı başlatmak için aşağıdakini yazın:  

    ```
    kubectl apply -f simpleweb.yaml
    ```  
  
  
2. Hizmeti dağıtımını (yaklaşık 30 saniye sürer) doğrulamak için aşağıdakini yazın:  

    ```
    kubectl get pods
    ```

3. Hizmet çalışmaya başladıktan sonra hizmete ait iç ve dış IP adreslerini görmek için aşağıdakini yazın:

    ```
    kubectl get svc
    ``` 
  
    ![Windows hizmetinin IP adresleri](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

    Dış IP adresinin eklenmesi birkaç dakika sürer. Dış adres, yük dengeleyici tarafından yapılandırılmadan önce `<pending>` şeklinde görünür.

4. Dış IP adresi kullanılabilir olduktan sonra hizmete web tarayıcınızda ulaşabilirsiniz.

    ![Tarayıcıda Windows sunucu uygulaması](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>Windows düğümlerine erişim
Windows düğümlerine, Uzak Masaüstü Bağlantısı aracılığıyla yerel bir Windows bilgisayarından erişilebilir. Ana düğüm aracılığıyla bir RDP SSH tüneli kullanmanızı öneririz. 

Windows’da SSH tünelleri oluşturmak için birden çok seçenek vardır. Bu bölüm, tüneli oluşturmak için PuTTY’nin nasıl kullanılacağını açıklar.

1. [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)’yi Windows sisteminize indirin.

2. Uygulamayı çalıştırın.

3. Küme yöneticisi kullanıcı adı ve kümedeki ilk ana düğümün genel DNS adından oluşan bir ana bilgisayar adı girin. **Ana Bilgisayar Adı** `adminuser@PublicDNSName`’e benzer. **Bağlantı Noktası** için 22 girin.

  ![PuTTY yapılandırması 1](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. **SSH > Yetkilendirme** öğesini seçin. Özel anahtar dosyanıza (.ppk biçimi) kimlik doğrulaması için bir yol ekleyin. [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) gibi bir araç kullanarak bu dosyayı kümenin oluşturulması için kullanılan SSH anahtarından oluşturabilirsiniz.

  ![PuTTY yapılandırması 2](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. **SSH > Tüneller**’i seçin ve iletilen bağlantı noktalarını yapılandırın. Yerel Windows makineniz zaten bağlantı noktası 3389’u kullanmakta olduğundan Windows düğümü 0 ve Windows düğümü 1’e ulaşmak için aşağıdaki ayarları kullanmanız önerilir. (Ek Windows düğümleri için bu şekilde devam edin.)

    **Windows Düğümü 0**

    * **Kaynak Bağlantı Noktası:** 3390
    * **Hedef:** 10.240.245.5:3389

    **Windows Düğümü 1**

    * **Kaynak Bağlantı Noktası:** 3391
    * **Hedef:** 10.240.245.6:3389

    ![Windows RDP tünellerinin görüntüsü](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. İşiniz bittiğinde, bağlantı yapılandırmasını kaydetmek için **Oturum > Kaydet**’e tıklayın.

7. PuTTY oturumuna bağlanmak için **Aç**’a tıklayın. Ana düğüme bağlantıyı tamamlayın.

8. Uzak Masaüstü Bağlantısı'nı başlatın. İlk Windows düğümüne bağlanmak için, **Bilgisayar** kısmında `localhost:3390` belirtip **Bağlan**’a tıklayın. (İkinci düğüme bağlanmak için `localhost:3390` belirtin ve bu şekilde devam edin.) Bağlantınızı tamamlamak için, dağıtım sırasında yapılandırdığınız yerel Windows yönetici parolasını belirtin.


## <a name="next-steps"></a>Sonraki adımlar

Kubernetes hakkında daha fazla bilgi edinmek için aşağıdaki bağlantılardan yararlanabilirsiniz:

* [Kubernetes Bootcamp](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) - kapsayıcılı uygulamalar için dağıtma, ölçeklendirme, güncelleştirme ve hata ayıklama işlemlerini gösterir.
* [Kubernetes Kullanıcı Kılavuzu](http://kubernetes.io/docs/user-guide/) - var olan bir Kubernetes kümesindeki çalışan programlar hakkında bilgi sağlar.
* [Kubernetes Örnekleri](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes ile gerçek uygulama çalıştırmaya ilişkin örnekler sunar.
