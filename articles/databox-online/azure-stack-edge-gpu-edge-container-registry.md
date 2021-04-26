---
title: Azure Stack Edge Pro GPU cihazında kenar kapsayıcısı kayıt defteri 'ni etkinleştirme
description: Azure Stack Edge Pro GPU cihazında yerel bir kenar kapsayıcısı kayıt defterinin nasıl etkinleştirileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 56b691b2755b5e248b16e338f8fd82864f5bf218
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560344"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazınızda kenar kapsayıcısı kayıt defterini etkinleştirme

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Bu makalede, Edge kapsayıcı kayıt defterinin nasıl etkinleştirileceği ve Azure Stack Edge Pro cihazınızda Kubernetes kümesi içinden nasıl kullanılacağı açıklanır. Makalede kullanılan örnekte, bir kaynak kayıt defterinden bir görüntüyü gönderme ayrıntıları, bu durumda Microsoft kapsayıcı kayıt defteri, Azure Stack Edge cihazında, kenar kapsayıcısı kayıt defteri.

### <a name="about-edge-container-registry"></a>Kenar kapsayıcısı kayıt defteri hakkında

Kapsayıcılı işlem uygulamaları kapsayıcı görüntülerinde çalışır ve bu görüntüler kayıt defterlerine kaydedilir. Kayıt defterleri, Docker Hub, özel veya Azure Container Registry gibi yönetilen bulut sağlayıcısı gibi genel olabilir. Daha fazla bilgi için bkz. [kayıt defterleri, depolar ve görüntüler hakkında](../container-registry/container-registry-concepts.md).

Edge kapsayıcısı kayıt defteri, Azure Stack Edge Pro cihazınızda uç sırada bir depo sağlar. Özel kapsayıcı görüntülerinizi depolamak ve yönetmek için bu kayıt defterini kullanabilirsiniz.

Çok düğümlü bir ortamda, kapsayıcı görüntüleri indirilebilir ve kenar kapsayıcısı kayıt defterine bir kez gönderilir. Tüm uç uygulamalar, sonraki dağıtımlar için kenar kapsayıcısı kayıt defterini kullanabilir.


## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. Azure Stack Edge Pro cihazına erişirsiniz.

1. Azure Stack Edge Pro cihazınızı [etkinleştirme Azure Stack Edge Pro 'Yu etkinleştir](azure-stack-edge-gpu-deploy-activate.md)bölümünde açıklandığı gibi etkinleştirdiniz.

1. Cihazda işlem rolünü etkinleştirdiniz. Cihazda, [Azure Stack Edge Pro cihazınızda Işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md)bölümündeki yönergelere göre işlem yapılandırdığınızda bir Kubernetes kümesi de oluşturulmuştur.

1. Yerel Web Kullanıcı arabiriminizdeki **cihaz** sayfasında Kubernetes API uç noktası vardır. Daha fazla bilgi için bkz. [Kubernetes API uç noktası](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)ile ilgili yönergeler.

1. [Desteklenen bir işletim sistemi](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)ile bir istemci sistemine erişirsiniz. Bir Windows istemcisi kullanıyorsanız, sistem cihaza erişmek için PowerShell 5,0 veya üstünü çalıştırmalıdır.

    1. Kendi kapsayıcı görüntülerinizi çekmek ve göndermek istiyorsanız, sistemde Docker istemcisinin yüklü olduğundan emin olun. Bir Windows istemcisi kullanıyorsanız, [Windows 'A Docker Desktop 'ı yüklersiniz](https://docs.docker.com/docker-for-windows/install/).  


## <a name="enable-container-registry-as-add-on"></a>Kapsayıcı kayıt defterini eklenti olarak etkinleştir

İlk adım, kenar kapsayıcısı kayıt defterinin eklenti olarak etkinleştirilme adımıdır.

1. [Cihazın PowerShell arabirimine bağlanın](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

1. Kapsayıcı kayıt defterini eklenti olarak etkinleştirmek için şunu yazın: 

    `Set-HcsKubernetesContainerRegistry`
    
    Bu işlemin tamamlanması birkaç dakika sürebilir.

    Bu komutun örnek çıktısı aşağıda verilmiştir:  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. Kapsayıcı kayıt defteri ayrıntılarını almak için şunu yazın:

    `Get-HcsKubernetesContainerRegistryInfo`

    Bu komutun dışında bir örnek aşağıda verilmiştir:  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. Çıktısından Kullanıcı adını ve parolayı bir yere göz önünde yapın `Get-HcsKubernetesContainerRegistryInfo` . Bu kimlik bilgileri, görüntü gönderilirken kenar kapsayıcısı kayıt defterinde oturum açmak için kullanılır.         


## <a name="manage-container-registry-images"></a>Kapsayıcı kayıt defteri görüntülerini yönetme

Kapsayıcı kayıt defterine Azure Stack Edge cihazınızın dışından erişmek isteyebilirsiniz. Ayrıca, kayıt defterine görüntü göndermek veya çekmek isteyebilirsiniz.

Kenar kapsayıcısı kayıt defterine erişmek için şu adımları izleyin:

1. Kenar kapsayıcısı kayıt defteri için uç nokta ayrıntılarını alın.
    1. Cihazın yerel kullanıcı arabiriminde **cihaz**' a gidin.
    1. **Kenar kapsayıcısı kayıt defteri uç noktasını** bulun.
        ![Cihaz sayfasındaki kenar kapsayıcısı kayıt defteri uç noktası](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Bu uç noktayı kopyalayın ve `C:\Windows\System32\Drivers\etc\hosts` uç kapsayıcı kayıt defteri uç noktasına bağlanmak için istemcinizin dosyasına karşılık gelen BIR DNS girişi oluşturun. 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![Kenar kapsayıcısı kayıt defteri uç noktası için DNS girişi ekleme](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Yerel kullanıcı arabiriminden kenar kapsayıcısı kayıt defteri sertifikasını indirin. 
    1. Cihazın yerel kullanıcı arabiriminde, **Sertifikalar**' a gidin.
    1. **Edge kapsayıcısı kayıt defteri sertifikası** için girişi bulun. Bu girişin sağında, istemci sisteminizdeki uç kapsayıcı kayıt defteri sertifikasını cihazınıza erişmek için kullanacağınız şekilde indirmek için **İndir** ' i seçin. 

        ![Edge kapsayıcısı kayıt defteri sertifikasını indirin](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. İndirilen sertifikayı istemciye yükler. Bir Windows istemcisi kullanıyorsanız, şu adımları izleyin: 
    1. Sertifikayı seçin ve **sertifika Içeri aktarma sihirbazında**, konumu **yerel makine** olarak depola ' yı seçin. 

        ![Sertifika yüklemeyi 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Sertifikayı, güvenilen kök depoda yerel makinenize yükler. 

        ![Sertifika yüklemeyi 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. Sertifika yüklendikten sonra, sisteminizde Docker istemcisini yeniden başlatın.

1. Kenar kapsayıcısı kayıt defterinde oturum açın. Şunu yazın:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    **Cihazlar** sayfasından kenar kapsayıcısı kayıt defteri uç noktasını ve çıktısından aldığınız kullanıcı adını ve parolayı belirtin `Get-HcsKubernetesContainerRegistryInfo` . 

1. Kapsayıcı kayıt defterinden kapsayıcı görüntülerini göndermek veya çekmek için Docker Push veya PULL komutlarını kullanın.
 
    1. Microsoft Container Registry görüntüsünden bir resim çekin. Şunu yazın:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. Kayıt defterinizin tam yolunu içeren görüntünün diğer adını oluşturun.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. Görüntüyü Kayıt defterinize gönderin.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. Kayıt defterinize gönderdiğiniz görüntüyü çalıştırın.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        Çekme ve itme komutlarının örnek bir çıktısı aşağıda verilmiştir:

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. `http://localhost:8080`Çalışan kapsayıcıyı görüntülemek için öğesine gidin. Bu durumda, NGINX web sunucusu çalıştıran bir durum görürsünüz.

    ![Çalışan kapsayıcıyı görüntüleme](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    Kapsayıcıyı durdurmak ve kaldırmak için tuşuna basın `Control+C` .

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Kubernetes Pod aracılığıyla kenar kapsayıcısı kayıt defteri görüntülerini kullanma

Artık kenar kapsayıcısı Kayıt defterinize gönderdiğiniz görüntüyü Kubernetes pods içinden dağıtabilirsiniz.

1. Görüntüyü dağıtmak için, *kubectl* aracılığıyla küme erişimini yapılandırmanız gerekir. Bir ad alanı, kullanıcı oluşturun, ad alanına kullanıcı erişimi verin ve bir *yapılandırma* dosyası alın. Kubernetes pods 'ye bağlanabildiğinizden emin olun. 
    
    [Azure Stack Edge Pro GPU cihazınızdan kubectl aracılığıyla bir Kubernetes kümesini bağlama ve yönetme](azure-stack-edge-gpu-create-kubernetes-cluster.md)bölümündeki tüm adımları izleyin. 

    Burada, kullanıcının Kubernetes kümesine erişebileceği, cihazınızda bulunan bir ad alanı için örnek bir çıktı verilmiştir.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. Görüntü çekme gizli dizileri, cihazınızdaki tüm Kubernetes ad alanlarında zaten ayarlanmış. Komutunu kullanarak gizli dizileri alabilirsiniz `get secrets` . Örnek çıktı aşağıdaki gibidir:

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Kubectl kullanarak ad alanınızı Pod 'a dağıtın. Aşağıdakileri kullanın `yaml` . 

    Görüntüsünü değiştirin: `<image-name>` görüntü, kapsayıcı kayıt defterine gönderilir. Bir ada sahip ımagepullgizlilikler kullanarak ad boşluklarınızın gizli dizilerini inceleyin: `ase-ecr-credentials` .
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Uygula komutunu kullanarak, oluşturduğunuz ad alanına dağıtımı uygulayın. Kapsayıcının çalıştığını doğrulayın. Örnek çıktı aşağıdaki gibidir:
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>Kapsayıcı kayıt defteri görüntülerini silme

Edge Container Registry depolama, Azure Stack Edge Pro cihazınızda bulunan ve cihazdaki kullanılabilir depolama alanı tarafından sınırlanan yerel bir paylaşımda barındırılır. Docker HTTP v2 API 'sini kullanarak, kullanılmayan Docker görüntülerini kapsayıcı kayıt defterinden silme sorumluluğu sizin sorumluluğunuzdadır https://docs.docker.com/registry/spec/api/) .  

Bir veya daha fazla kapsayıcı görüntüsünü kaldırmak için şu adımları izleyin:

1. Görüntü adını silmek istediğiniz görüntüye ayarlayın.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. Kapsayıcı kayıt defterinin Kullanıcı adı ve parolasını PS kimlik bilgisi olarak ayarla

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. Görüntüyle ilişkili etiketleri listeleyin

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. Silmek istediğiniz etiketle ilişkili Özeti listeleyin. Bu, yukarıdaki komutun çıktısından $tags kullanır. Birden çok etiket varsa, bunlardan birini seçin ve sonraki komutta öğesini kullanın.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Görüntünün özetini kullanarak görüntüyü silin: etiket

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

Kullanılmayan görüntüleri sildikten sonra, başvurulmayan görüntülerle ilişkili alan, gecelik çalıştıran bir işlem tarafından otomatik olarak geri kazanılır. 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro 'unuzda durum bilgisiz bir uygulama dağıtın](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).