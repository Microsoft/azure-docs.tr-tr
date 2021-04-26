---
title: Azure Stack Edge Pro GPU cihazında bir Kubernetes kümesi oluşturma ve yönetme | Microsoft Docs
description: Windows PowerShell arabirimi aracılığıyla Azure Stack Edge Pro GPU cihazında bir Kubernetes kümesinin nasıl oluşturulacağını ve yönetileceğini açıklar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: ddbf3c99845f4c38f511260f4dec274903a9d586
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631970"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU cihazınızdan kubectl aracılığıyla bir Kubernetes kümesine bağlanma ve bunları yönetme

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro cihazınızda, işlem rolünü yapılandırırken bir Kubernetes kümesi oluşturulur. Kubernetes kümesi oluşturulduktan sonra, *kubectl* gibi yerel bir araç aracılığıyla bir istemci makinesinden kümeye bağlanabilir ve yerel olarak yönetim sağlayabilirsiniz.

Bu makalede, Azure Stack Edge Pro cihazınızda bir Kubernetes kümesine bağlanmayı ve sonra da *kubectl* kullanarak yönetmeyi açıklanmaktadır. 


## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce aşağıdakilerden emin olun:

1. Azure Stack Edge Pro cihazına erişirsiniz.

2. Azure Stack Edge Pro cihazınızı [etkinleştirme Azure Stack Edge Pro 'Yu etkinleştir](azure-stack-edge-gpu-deploy-activate.md)bölümünde açıklandığı gibi etkinleştirdiniz.

3. Cihazda işlem rolünü etkinleştirdiniz. Cihazda, [Azure Stack Edge Pro cihazınızda Işlem yapılandırma](azure-stack-edge-gpu-deploy-configure-compute.md)bölümündeki yönergelere göre işlem yapılandırdığınızda bir Kubernetes kümesi de oluşturulmuştur.

4. Cihaza erişmek için PowerShell 5,0 veya sonraki bir sürümü çalıştıran bir Windows istemci sistemine erişirsiniz. [Desteklenen bir işletim sistemine](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) sahip başka bir istemciniz de olabilir. 

5. Yerel Web Kullanıcı arabiriminizdeki **cihaz** sayfasında Kubernetes API uç noktası vardır. Daha fazla bilgi için bkz. [Kubernetes API uç noktasını edinme](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) yönergeleri


## <a name="connect-to-powershell-interface"></a>PowerShell arabirimine Bağlan

Kubernetes kümesi oluşturulduktan sonra, ad alanları ve kullanıcılar oluşturmak ve kullanıcıları ad alanlarına atamak için bu kümeye erişebilirsiniz. Bu, cihazın PowerShell arabirimine bağlanmanızı gerektirir. PowerShell çalıştıran Windows istemcisinde bu adımları izleyin.

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-kubernetes-rbac"></a>Kubernetes RBAC aracılığıyla küme erişimini yapılandırma

Kubernetes kümesi oluşturulduktan sonra, kümeye erişmek için komut satırı aracılığıyla *kubectl* 'yi kullanabilirsiniz. 

Bu yaklaşımda bir ad alanı ve Kullanıcı oluşturursunuz. Daha sonra kullanıcıyı ad alanıyla ilişkilendirirsiniz. Ayrıca, bir Kubernetes istemcisini, Azure Stack Edge Pro cihazınızın PowerShell arabirimine bağlanabilmek gerekmeden doğrudan oluşturduğunuz Kubernetes kümesi ile konuşmanıza olanak tanıyan bir *yapılandırma* dosyası almanız gerekir.

1. Ad alanı oluşturun. Şunu yazın:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > Hem ad alanı hem de Kullanıcı adları için [DNS alt etki alanı adlandırma kuralları](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) geçerlidir.

    Örnek çıktı aşağıdaki gibidir:

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. Bir kullanıcı oluşturun ve bir yapılandırma dosyası alın. Şunu yazın:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > Azure Stack Edge Pro için IoT ad alanıyla ilişkili bir varsayılan kullanıcı için ayrılmış olduğundan, *aseuser* Kullanıcı adı olarak kullanılamaz.

    Yapılandırma dosyasının örnek bir çıkışı aşağıda verilmiştir:
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. Bir yapılandırma dosyası düz metin olarak görüntülenir. Bu dosyayı kopyalayın ve bir *yapılandırma* dosyası olarak kaydedin. 

    > [!IMPORTANT]
    > Yapılandırma dosyasını *. txt* dosyası olarak kaydetmeyin, dosyayı herhangi bir dosya uzantısı olmadan kaydedin.

4. Yapılandırma dosyası `.kube` yerel makinedeki Kullanıcı profilinizin klasöründe bulunmalıdır. Dosyayı Kullanıcı profilinizde bu klasöre kopyalayın.

    ![İstemcideki yapılandırma dosyası konumu](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)

5. Ad alanını oluşturduğunuz kullanıcıyla ilişkilendirin. Şunu yazın:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    Örnek çıktı aşağıdaki gibidir:

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    Yapılandırma dosyasına sahip olduktan sonra kümeye fiziksel erişime ihtiyacınız yoktur. İstemciniz Azure Stack Edge Pro cihaz IP 'sini ping yapabiliyor ise, *kubectl* komutlarını kullanarak kümeyi yönlendirebilirsiniz.

6. İstemcinizdeki yeni bir PowerShell oturumu başlatın. Cihaz arabirimine bağlı olmanız gerekmez. Artık `kubectl` aşağıdaki komutu kullanarak istemcinizi yükleyebilirsiniz:

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    Örneğin, Kubernetes ana düğümü v 1.15.2 çalıştırıyorsa, istemciye v 1.15.2 ' yi yükler.

    > [!IMPORTANT]
    > Ana sürümden en fazla bir alt sürüm uzakta olan bir istemci indirin. İstemci sürümü, ancak ana öğeyi bir alt sürüme kadar gösterebilir. Örneğin, bir v 1.3 yöneticisinin v 1.1, v 1.2 ve v 1.3 düğümleri ile çalışması ve v 1.2, v 1.3 ve v 1.4 istemcileri ile çalışması gerekir. Kubernetes istemci sürümü hakkında daha fazla bilgi için bkz. [Kubernetes sürümü ve sürüm eğriltme destek ilkesi](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew). Azure Stack Edge Pro 'da Kubernetes sunucu sürümü hakkında daha fazla bilgi için, Kubernetes sunucu sürümünü al bölümüne gidin.<!-- insert link-->
    > Bazen `kubectl` Docker for Windows veya başka araçlar çalıştırıyorsanız sisteminize önceden yüklenir. Bu `kubectl` bölümde belirtilen bu Kubernetes kümesiyle çalışmak için belirli sürümünü indirmek önemlidir. 

    Yükleme birkaç dakika sürer.

7. İndirdiğiniz sürümün yüklü olduğunu doğrulayın. Sisteminize yüklendiği yerin mutlak yolunu belirtmeniz gerekir `kubectl.exe` .
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    `kubectl`Kubernetes kümesini yönetmek için kullanılan komutlar hakkında daha fazla bilgi için, [kubectl 'ye genel bakış](https://kubernetes.io/docs/reference/kubectl/overview/)bölümüne gidin.

8. Sisteminizdeki Hosts dosyasına bir DNS girişi ekleyin. 

    1. Not defteri 'Ni yönetici olarak çalıştırın ve `hosts` konumunda bulunan dosyayı açın `C:\windows\system32\drivers\etc\hosts` . 
    2. Ana bilgisayarlar dosyasında girişi oluşturmak için önceki adımda yer alan yerel kullanıcı arabirimindeki **cihaz** sayfasından kaydettiğiniz bilgileri kullanın. 

        Örneğin, bu uç noktayı, `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` CIHAZ IP adresi ve DNS etki alanı ile aşağıdaki girdiyi oluşturmak için kopyalayın: 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Kubernetes pods 'ye bağlanabildiğinizi doğrulamak için şunu yazın:
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
Artık uygulamalarınızı ad alanında dağıtabilir, ardından bu uygulamaları ve bunların günlüklerini görüntüleyebilirsiniz.

> [!IMPORTANT]   
> Çalıştıramayamayabilmeniz gereken birçok komut vardır, örneğin, yönetici erişiminizin olmasını gerektiren komutlar. Yalnızca ad alanında izin verilen işlemleri gerçekleştirebilirsiniz.


## <a name="remove-kubernetes-cluster"></a>Kubernetes kümesini kaldır

Kubernetes kümesini kaldırmak için IoT Edge yapılandırmasını kaldırmanız gerekecektir.

Ayrıntılı yönergeler için [IoT Edge yapılandırmayı Yönet](azure-stack-edge-gpu-manage-compute.md#manage-iot-edge-configuration)' e gidin.
   

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stack Edge Pro 'unuzda durum bilgisiz bir uygulama dağıtın](azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).
