---
title: Azure Izleyici 'de kapsayıcı Izleme çözümü | Microsoft Docs
description: Azure Izleyici 'de kapsayıcı Izleme çözümü, Docker ve Windows kapsayıcı konaklarınızı tek bir konumda görüntülemenize ve yönetmenize yardımcı olur.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: b71818d5d840a0466b5ff6f271df117043341f7b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899105"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Azure Izleyici 'de kapsayıcı Izleme çözümü

![Kapsayıcılar simgesi](./media/containers/containers-symbol.png)

Bu makalede, Azure Izleyici 'de kapsayıcı Izleme çözümünün nasıl ayarlanacağı ve kullanılacağı açıklanır. Bu, Docker ve Windows kapsayıcı konaklarınızı tek bir konumda görüntülemenize ve yönetmenize yardımcı olur. Docker, BT altyapısına yazılım dağıtımını otomatikleştiren kapsayıcılar oluşturmak için kullanılan bir yazılım sanallaştırma sistemidir.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Çözüm hangi kapsayıcıların çalıştığını, hangi kapsayıcı görüntüsünün çalıştığını ve kapsayıcıların nerede çalıştığını gösterir. Kapsayıcılarla kullanılan komutları gösteren ayrıntılı denetim bilgilerini görebilirsiniz. Ve, Docker veya Windows konaklarını uzaktan görüntülemek zorunda kalmadan merkezi günlükleri görüntüleyip arayarak kapsayıcılara sorun giderme işlemleri yapabilirsiniz. Bir konakta, gürültülü olabilecek ve fazla kaynak alabilen kapsayıcılar bulabilirsiniz. Ve, kapsayıcılar için merkezi CPU, bellek, depolama ve ağ kullanımını ve performans bilgilerini görüntüleyebilirsiniz. Windows çalıştıran bilgisayarlarda, günlükleri Windows Server, Hyper-V ve Docker kapsayıcılarından merkezileştirmek ve karşılaştırmak için kullanabilirsiniz. Çözüm aşağıdaki kapsayıcı düzenleyiciler destekler:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

[Azure Service Fabric](../../service-fabric/service-fabric-overview.md)'te dağıtılan kapsayıcılar varsa, küme olaylarının izlenmesini dahil etmek için hem [Service Fabric çözümü](../../service-fabric/service-fabric-diagnostics-oms-setup.md) hem de bu çözümü etkinleştirmenizi öneririz. Service Fabric çözümünü etkinleştirmeden önce, ne sağladığını ve nasıl kullanılacağını anlamak için [Service Fabric çözümünü kullanarak](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) gözden geçirin.

Azure Kubernetes Service (AKS) üzerinde barındırılan Kubernetes ortamlarına dağıtılan iş yüklerinizin performansını izlemeye ilgileniyorsanız, bkz. [Azure Kubernetes hizmetini izleme](../../azure-monitor/insights/container-insights-overview.md). Kapsayıcı Izleme çözümü, bu platformun izlenmesini desteklemiyor.  

Aşağıdaki diyagramda, Azure Izleyici ile çeşitli kapsayıcı konakları ve aracıları arasındaki ilişkiler gösterilmektedir.

![Kapsayıcılar diyagramı](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Sistem gereksinimleri ve desteklenen platformlar

Başlamadan önce, önkoşulları karşıladığınızdan emin olmak için aşağıdaki ayrıntıları gözden geçirin.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Docker Orchestrator ve OS platformu için kapsayıcı izleme çözümü desteği

Aşağıdaki tabloda, Azure Izleyici ile kapsayıcı envanteri, performansı ve günlükleri için Docker düzenleme ve işletim sistemi izleme desteği özetlenmektedir.   

| | ACS | Linux | Windows | Kapsayıcı<br>Envanter | Görüntü<br>Envanter | Düğüm<br>Envanter | Kapsayıcı<br>Performans | Kapsayıcı<br>Olay | Olay<br>Günlük | Kapsayıcı<br>Günlük |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Hizmet<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat açık<br>Karakter | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>etikete | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux Sunucu<br>etikete | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Linux 'ta desteklenen Docker sürümleri

- Docker 1,11-1,13
- Docker CE ve EE v 17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>kapsayıcı konakları olarak desteklenen x64 Linux dağıtımları

- Ubuntu 14,04 LTS ve 16,04 LTS
- CoreOS (kararlı)
- Amazon Linux 2016.09.0
- openSUSE 13,2
- openSUSE artık 42,2
- CentOS 7,2 ve 7,3
- SLES 12
- RHEL 7,2 ve 7,3
- Red Hat OpenShift kapsayıcı platformu (OCP) 3,4 ve 3,5
- ACS Mesosphere DC/OS 1.7.3 to 1.8.8
- ACS Kubernetes 1.4.5 to 1,6
    - Kubernetes olayları, Kubernetes envanteri ve kapsayıcı işlemlerine yalnızca Linux için Log Analytics aracısının 1.4.1-45 ve üzeri sürümleri ile desteklenir
- ACS Docker Sısınma

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Desteklenen Windows işletim sistemi

- Windows Server 2016
- Windows 10 yıldönümü sürümü (Professional veya Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Windows 'da desteklenen Docker sürümleri

- Docker 1,12 ve 1,13
- Docker 17.03.0 ve üzeri

## <a name="installing-and-configuring-the-solution"></a>Çözümü yükleme ve yapılandırma

Çözümü yüklemek ve yapılandırmak için aşağıdaki bilgileri kullanın.

1. Kapsayıcı Izleme çözümünü [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) 'ndeki Log Analytics çalışma alanınıza veya [Çözüm Galerisi izleme çözümlerini ekleme](../../azure-monitor/insights/solutions.md)bölümünde açıklanan işlemi kullanarak ekleyin.

2. Log Analytics aracı ile Docker 'ı yükleyip kullanın. İşletim sisteminize ve Docker Orchestrator 'a göre, aracınızı yapılandırmak için aşağıdaki yöntemleri kullanabilirsiniz.
   - Tek başına konaklar için:
     - Desteklenen Linux işletim sistemlerinde Docker 'ı yükleyip çalıştırın ve [Linux için Log Analytics aracısını](../../azure-monitor/learn/quick-collect-linux-computer.md)yükleyip yapılandırın.  
     - CoreOS 'ta Linux için Log Analytics aracısını çalıştıramazsınız. Bunun yerine, Linux için Log Analytics aracısının kapsayıcılı bir sürümünü çalıştırırsınız. Azure Kamu bulutundaki kapsayıcılarla çalışıyorsanız, CoreOS veya Azure Kamu Linux kapsayıcı Konakları dahil olmak üzere Linux kapsayıcı konaklarını inceleyin.
     - Windows Server 2016 ve Windows 10 ' da Docker altyapısını ve istemcisini yükledikten sonra, bilgi toplamak ve Azure Izleyici 'ye göndermek için bir aracı bağlayın. Windows ortamınız varsa [Windows kapsayıcı konaklarınızı yüklemeyi ve yapılandırmayı](#install-and-configure-windows-container-hosts) gözden geçirin.
   - Docker çoklu konak düzenlemesi için:
     - Red Hat OpenShift ortamınız varsa, Red Hat OpenShift için Log Analytics Aracısı yapılandırma ' yı gözden geçirin.
     - Azure Container Service kullanarak bir Kubernetes kümeniz varsa:
       - [Kubernetes için Log Analytics Linux Aracısı yapılandırma](#configure-a-log-analytics-linux-agent-for-kubernetes)konusunu gözden geçirin.
       - [Kubernetes için Log Analytics Windows Aracısı yapılandırma](#configure-a-log-analytics-windows-agent-for-kubernetes)konusunu gözden geçirin.
       - Linux Kubernetes 'te Log Analytics Aracısı dağıtmak için Held kullanın.
     - Bir Azure Container Service DC/OS kümeniz varsa, [Azure izleyici ile Azure CONTAINER SERVICE DC/OS kümesini izleme](../../container-service/dcos-swarm/container-service-monitoring-oms.md)hakkında daha fazla bilgi edinin.
     - Docker Sısınma modu ortamınız varsa, Docker Sısınma için Log Analytics Aracısı yapılandırma hakkında daha fazla bilgi edinin.
     - Bir Service Fabric kümeniz varsa, [Azure izleyici ile kapsayıcıları izlemek](../../service-fabric/service-fabric-diagnostics-oms-containers.md)için daha fazla bilgi edinin.

Windows çalıştıran bilgisayarlarda Docker altyapılarını yüklemek ve yapılandırmak hakkında daha fazla bilgi için [Windows makalesindeki Docker altyapısını](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) gözden geçirin.

> [!IMPORTANT]
> Kapsayıcı konaklarınıza [Linux için Log Analytics aracısını](../../azure-monitor/learn/quick-collect-linux-computer.md) yüklemeden **önce** Docker çalışıyor olmalıdır. Docker 'ı yüklemeden önce aracıyı zaten yüklediyseniz, Linux için Log Analytics aracısını yeniden yüklemeniz gerekir. Docker hakkında daha fazla bilgi için bkz. [Docker Web sitesi](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Linux kapsayıcı Konakları yükleyip yapılandırma

Docker 'ı yükledikten sonra, aracıyı Docker ile kullanılmak üzere yapılandırmak için kapsayıcı ana bilgisayarınız için aşağıdaki ayarları kullanın. İlk olarak, Azure portal bulabileceğiniz Log Analytics çalışma alanı KIMLIĞINIZ ve anahtarınız olmalıdır. Çalışma alanınızda, **çalışma alanı kimliğinizi** ve **birincil anahtarınızı**görüntülemek için **hızlı başlangıç** > **bilgisayarlar** ' a tıklayın.  Her ikisini de kopyalayıp sık kullandığınız bir düzenleyiciye yapıştırın.

**CoreOS hariç tüm Linux kapsayıcı konakları için:**

- Linux için Log Analytics aracısının nasıl yükleneceğine ilişkin daha fazla bilgi ve adım için, bkz. [Log Analytics aracısına genel bakış](../../azure-monitor/platform/log-analytics-agent.md).

**CoreOS dahil tüm Linux kapsayıcı konakları için:**

İzlemek istediğiniz kapsayıcıyı başlatın. Aşağıdaki örneği değiştirin ve kullanın:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**CoreOS dahil tüm Azure Kamu Linux kapsayıcı konakları için:**

İzlemek istediğiniz kapsayıcıyı başlatın. Aşağıdaki örneği değiştirin ve kullanın:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Bir kapsayıcıda yüklü bir Linux aracısını kullanarak geçiş yapma**

Daha önce doğrudan yüklenmiş aracıyı kullandıysanız ve bunun yerine bir kapsayıcıda çalışan bir aracı kullanmak istiyorsanız, önce Linux için Log Analytics aracısını kaldırmanız gerekir. Aracının başarıyla nasıl kaldırılacağını anlamak için bkz. [Linux için Log Analytics Aracısı 'Nı kaldırma](../../azure-monitor/learn/quick-collect-linux-computer.md) .  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Docker Sısınma için Log Analytics Aracısı yapılandırma

Log Analytics aracısını Docker Sısınma üzerinde küresel bir hizmet olarak çalıştırabilirsiniz. Log Analytics aracı hizmeti oluşturmak için aşağıdaki bilgileri kullanın. Log Analytics çalışma alanı KIMLIĞINIZI ve birincil anahtarınızı sağlamanız gerekir.

- Ana düğümde aşağıdakileri çalıştırın.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Docker Sısınma için güvenli gizlilikler

Docker Sısınma için, çalışma alanı KIMLIĞI ve birincil anahtar için gizli dizi oluşturulduktan sonra, gizli bilgilerinizi oluşturmak için aşağıdaki bilgileri kullanın.

1. Ana düğümde aşağıdakileri çalıştırın.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Gizli dizileri doğru bir şekilde oluşturulduğunu doğrulayın.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Gizli dizileri Kapsayıcılı Log Analytics aracısına bağlamak için aşağıdaki komutu çalıştırın.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Red Hat OpenShift için Log Analytics Aracısı yapılandırma

Kapsayıcı izleme verilerini toplamaya başlamak için Log Analytics aracısını Red Hat OpenShift 'e eklemenin üç yolu vardır.

* Her OpenShift düğümüne [Linux için Log Analytics aracısını doğrudan yüklemeyi](../../azure-monitor/learn/quick-collect-linux-computer.md)  
* Azure 'da bulunan her OpenShift düğümünde [log ANALYTICS VM uzantısını etkinleştirin](../../azure-monitor/learn/quick-collect-azurevm.md)  
* Log Analytics aracısını OpenShift daemon olarak kurma  

Bu bölümde Log Analytics aracısını OpenShift Daemon-set olarak yüklemek için gereken adımları ele alınmaktadır.  

1. OpenShift ana düğümünde oturum açın ve YAML dosyası [OCP-omsagent. YAML](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) öğesini GitHub 'dan ana düğümünüz ile kopyalayın ve değeri Log Analytics çalışma alanı kimliğinizle ve birincil anahtarınızla değiştirin.
2. Azure Izleyici için bir proje oluşturmak ve Kullanıcı hesabını ayarlamak için aşağıdaki komutları çalıştırın.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Daemon-set ' i dağıtmak için aşağıdakileri çalıştırın:

    `oc create -f ocp-omsagent.yaml`

4. Yapılandırıldığını ve doğru şekilde çalıştığını doğrulamak için aşağıdakini yazın:

    `oc describe daemonset omsagent`  

    ve çıktının şöyle olması gerekir:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Log Analytics aracı Daemon-set YAML dosyasını kullanırken Log Analytics çalışma alanı KIMLIĞINIZI ve birincil anahtarınızı güvenli hale getirmek için gizli dizileri kullanmak istiyorsanız, aşağıdaki adımları uygulayın.

1. OpenShift ana düğümünde oturum açın ve YAML dosyası [OCP-DS-omsagent. YAML](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) ve gizli betik oluşturma [OCP-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) öğesini GitHub 'dan kopyalayın.  Bu betik, Log Analytics çalışma alanı KIMLIĞI ve birincil anahtar için, gizlice bilgilerinizin güvenliğini sağlamak üzere gizlilikler YAML dosyasını oluşturur.  
2. Azure Izleyici için bir proje oluşturmak ve Kullanıcı hesabını ayarlamak için aşağıdaki komutları çalıştırın. Gizli betik oluşturma, Log Analytics çalışma alanı KIMLIĞI `<WSID>` ve birincil anahtar `<KEY>` ister ve tamamlandığında, OCP-Secret. YAML dosyasını oluşturur.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Aşağıdaki çalıştırarak gizli dosyayı dağıtın:

    `oc create -f ocp-secret.yaml`

4. Aşağıdakileri çalıştırarak dağıtımı doğrulayın:

    `oc describe secret omsagent-secret`  

    ve çıktının şöyle olması gerekir:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

5. Log Analytics Aracısı Daemon 'ı dağıtma-aşağıdaki çalıştırarak YAML dosyasını ayarlayın:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Aşağıdakileri çalıştırarak dağıtımı doğrulayın:

    `oc describe ds oms`

    ve çıktının şöyle olması gerekir:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Kubernetes için Log Analytics Linux Aracısı yapılandırma

Kubernetes için, Linux için Log Analytics aracısını yüklemek üzere çalışma alanı KIMLIĞINIZ ve birincil anahtarınız için gizli dizileri YAML dosyası oluşturmak üzere bir komut dosyası kullanın. [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) sayfasında, gizli bilginiz olmadan veya bunlarla birlikte kullanabileceğiniz dosyalar vardır.

- Linux DaemonSet için varsayılan Log Analytics Aracısı gizli bilgiler içermez (omsagent. YAML)
- Linux DaemonSet YAML dosyası için Log Analytics Aracısı gizli dizi oluşturma betiklerine sahip gizli bilgileri (omsagent-DS-gizlilikler. YAML) kullanarak gizli dizileri (omsagentsecret. YAML) dosyası oluşturur.

Gizli dizileri olan veya olmayan omsagent DaemonSets oluşturmayı seçebilirsiniz.

**Gizli olmayan varsayılan OMSagent DaemonSet YAML dosyası**

- Varsayılan Log Analytics Agent DaemonSet YAML dosyası için `<WSID>` ve `<KEY>` yerine WSıD ve ANAHTARıNıZLA değiştirin. Dosyayı ana düğüme kopyalayın ve aşağıdakileri çalıştırın:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Gizli olmayan varsayılan OMSagent DaemonSet YAML dosyası**

1. Gizli bilgileri kullanarak Log Analytics Agent DaemonSet kullanmak için öncelikle gizli dizileri oluşturun.
    1. Betiği ve gizli şablon dosyasını kopyalayın ve aynı dizinde olduklarından emin olun.
        - Gizli betik oluşturma-secret-gen.sh
        - gizli şablon-gizli-şablon. YAML
    2. Aşağıdaki örnekte olduğu gibi betiği çalıştırın. Betik, Log Analytics çalışma alanı KIMLIĞI ve birincil anahtar ve bunları girdikten sonra komut dosyası, çalıştırmak için bir gizli YAML dosyası oluşturur.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Aşağıdakileri çalıştırarak gizli dizileri oluşturun:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Doğrulamak için aşağıdakileri çalıştırın:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Çıkış şuna benzemelidir:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Çıkış şuna benzemelidir:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Omsagent Daemon 'nizi oluşturma-```sudo kubectl create -f omsagent-ds-secrets.yaml``` çalıştırarak ayarlama

2. Log Analytics Agent DaemonSet 'ın çalıştığını ve aşağıdakine benzer şekilde çalıştığını doğrulayın:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Kubernetes için, çalışma alanı KIMLIĞI ve Linux için Log Analytics aracısına ait birincil anahtar için gizli anahtar dosyası oluşturmak üzere bir komut dosyası kullanın. Gizli bilgilerinizin güvenliğini sağlamak için [omsagent YAML dosyası](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) ile aşağıdaki örnek bilgileri kullanın.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Kubernetes için Log Analytics Windows Aracısı yapılandırma

Windows Kubernetes için, Log Analytics aracısını yüklemek üzere çalışma alanı KIMLIĞINIZ ve birincil anahtarınız için gizli anahtar dosyası oluşturmak üzere bir komut dosyası kullanın. [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) sayfasında, gizli bilgileriniz ile kullanabileceğiniz dosyalar vardır.  Ana ve aracı düğümleri için Log Analytics aracısını ayrı olarak yüklemeniz gerekir.  

1. Ana düğümdeki gizli bilgileri kullanarak Log Analytics Agent DaemonSet kullanmak için, oturum açın ve öncelikle gizli dizileri oluşturun.
    1. Betiği ve gizli şablon dosyasını kopyalayın ve aynı dizinde olduklarından emin olun.
        - Gizli betik oluşturma-secret-gen.sh
        - gizli şablon-gizli-şablon. YAML

    2. Aşağıdaki örnekte olduğu gibi betiği çalıştırın. Betik, Log Analytics çalışma alanı KIMLIĞI ve birincil anahtar ve bunları girdikten sonra komut dosyası, çalıştırmak için bir gizli YAML dosyası oluşturur.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Omsagent Daemon 'nizi oluşturma-```kubectl create -f omsagentsecret.yaml``` çalıştırarak ayarlama
    4. Denetlemek için aşağıdakileri çalıştırın:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Çıkış şuna benzemelidir:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Omsagent Daemon 'nizi oluşturma-```kubectl create -f ws-omsagent-de-secrets.yaml``` çalıştırarak ayarlama

2. Log Analytics Agent DaemonSet 'ın çalıştığını ve aşağıdakine benzer şekilde çalıştığını doğrulayın:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Aracıyı Windows çalıştıran çalışan düğümüne yüklemek için, [Windows kapsayıcı konaklarını yükleyip yapılandırma](#install-and-configure-windows-container-hosts)bölümündeki adımları izleyin.

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Linux Kubernetes 'te Log Analytics Aracısı dağıtmak için Held kullanma

Linux Kubernetes ortamınızda Log Analytics Aracısı dağıtmak üzere Held 'yi kullanmak için aşağıdaki adımları gerçekleştirin.

1. Omsagent Daemon 'nizi oluşturma-```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms``` çalıştırarak ayarlama
2. Sonuçlar şuna benzer olacaktır:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. Şu komutu çalıştırarak omsagent 'ın durumunu denetleyebilirsiniz: ```helm status "omsagent"``` ve çıkış aşağıdakine benzer şekilde görünür:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    Daha fazla bilgi için lütfen [kapsayıcı çözümü HELI grafiğini](https://aka.ms/omscontainerhelm)ziyaret edin.

### <a name="install-and-configure-windows-container-hosts"></a>Windows kapsayıcı Konakları yükleyip yapılandırma

Windows kapsayıcı Konakları yüklemek ve yapılandırmak için bölümündeki bilgileri kullanın.

#### <a name="preparation-before-installing-windows-agents"></a>Windows aracılarını yüklemeden önce hazırlık

Windows çalıştıran bilgisayarlara aracıları yüklemeden önce, Docker hizmetini yapılandırmanız gerekir. Yapılandırma, Windows aracısının veya Azure Izleyici sanal makine uzantısının Docker TCP yuvasını kullanmasına izin verir, böylece aracıların Docker Daemon 'ı uzaktan erişip izleme için veri yakalamalarını sağlayabilirsiniz.

##### <a name="to-configure-the-docker-service"></a>Docker hizmetini yapılandırmak için  

Windows Server için TCP kanalını ve adlandırılmış kanalı etkinleştirmek üzere aşağıdaki PowerShell komutlarını gerçekleştirin:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Windows kapsayıcılarıyla kullanılan Docker Daemon yapılandırması hakkında daha fazla bilgi için bkz. [Windows 'Da Docker motoru](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Windows aracılarını yükler

Windows ve Hyper-V kapsayıcı izlemesini etkinleştirmek için, kapsayıcı Konakları olan Windows bilgisayarlarına Microsoft Monitoring Agent (MMA) ' yı (MMA) yüklersiniz. Şirket içi ortamınızda Windows çalıştıran bilgisayarlar için bkz. [Windows bilgisayarlarını Azure izleyici 'ye bağlama](../../azure-monitor/platform/agent-windows.md). Azure 'da çalışan sanal makineler için, [sanal makine uzantısını](../../azure-monitor/learn/quick-collect-azurevm.md)kullanarak bunları Azure izleyici 'ye bağlayın.

Service Fabric üzerinde çalışan Windows kapsayıcılarını izleyebilirsiniz. Ancak, yalnızca [Azure 'da çalışan sanal makineler](../../azure-monitor/learn/quick-collect-azurevm.md) ve [Şirket içi ortamınızda Windows çalıştıran bilgisayarlar](../../azure-monitor/platform/agent-windows.md) Service Fabric için desteklenmektedir.

Kapsayıcı Izleme çözümünün Windows için doğru şekilde ayarlandığını doğrulayabilirsiniz. Yönetim paketinin doğru şekilde indirilip indirilmediğini denetlemek için *ContainerManagement.xxx*bakın. Dosyalar, C:\Program Files\Microsoft Monitoring Sk\k\sistem sağlığı hizmeti State\Management Packs klasöründe olmalıdır.

## <a name="solution-components"></a>Çözüm bileşenleri

Azure portal, *Çözüm Galerisi* gidin ve **kapsayıcı izleme çözümünü**ekleyin. Windows aracılarını kullanıyorsanız, bu çözümü eklediğinizde aşağıdaki yönetim paketi bir aracı olan her bilgisayara yüklenir. Yönetim Paketi için yapılandırma veya bakım gerekli değildir.

- *ContainerManagement.xxx* , C:\Program Files\Microsoft Monitoring Tors T\k\sistem sağlığı hizmeti State\Management paketlerine yüklendi

## <a name="container-data-collection-details"></a>Kapsayıcı verileri toplama ayrıntıları

Kapsayıcı Izleme çözümü, etkinleştirdiğiniz aracıları kullanarak kapsayıcı konaklarından ve kapsayıcılardan çeşitli performans ölçümlerini ve günlük verilerini toplar.

Veriler aşağıdaki aracı türleri tarafından üç dakikada bir toplanır.

- [Linux için Log Analytics Aracısı](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Windows Aracısı](../../azure-monitor/platform/agent-windows.md)
- [Log Analytics VM Uzantısı](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Kapsayıcı kayıtları

Aşağıdaki tabloda, kapsayıcı Izleme çözümü tarafından toplanan kayıt örnekleri ve günlük araması sonuçlarında görünen veri türleri gösterilmektedir.

| Veri türü | Günlük aramasında veri türü | Alanlar |
| --- | --- | --- |
| Konaklar ve kapsayıcılar için performans | `Perf` | Bilgisayar, ObjectName, CounterName &#40;% işlemci zamanı, DISK okuma MB, DISK yazma MB, bellek kullanımı MB, ağ alma baytları, ağ gönderme baytları, işlemci kullanım sn, ağ&#41;, CounterValue, TimeGenerated, CounterPath, dir |
| Kapsayıcı envanteri | `ContainerInventory` | TimeGenerated, bilgisayar, kapsayıcı adı, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, komut, CreatedTime, StartedTime, Sonlandırhedtime, dir, Containerıd, ImageID |
| Kapsayıcı görüntüsü envanteri | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, çalışıyor, duraklatıldı, durduruldu, Failed, dir, ImageID, TotalContainer |
| Kapsayıcı günlüğü | `ContainerLog` | TimeGenerated, bilgisayar, görüntü KIMLIĞI, kapsayıcı adı, LogEntrySource, LogEntry, dir, Containerıd |
| Kapsayıcı hizmeti günlüğü | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, dir, Containerıd |
| Kapsayıcı düğümü envanteri | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, dir|
| Kubernetes stoku | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, dir |
| Kapsayıcı işlemi | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, dir |
| Kubernetes olayları | `KubeEvents_CL` | TimeGenerated, Computer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, dir, Ileti |

*Pod etiketi* veri türlerine eklenen Etiketler kendi özel etiketleridir. Tabloda gösterilen eklenmiş Pod etiketi etiketleri örnek olarak verilebilir. `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s``PodLabel_docker_registry_s` ortamınızın veri kümesinde ve genel olarak benzerine benzer `PodLabel_yourlabel_s`farklıdır.

## <a name="monitor-containers"></a>Kapsayıcıları izleme
Azure portal çözümü etkinleştirildikten sonra **kapsayıcılar** kutucuğu, kapsayıcı konaklarınız ve konaklarda çalışan kapsayıcılar hakkındaki özet bilgileri gösterir.

![Kapsayıcılar kutucuğu](./media/containers/containers-title.png)

Kutucukta, ortamda kaç kapsayıcının olduğunu ve başarısız, çalışıyor veya durdurulmuş olduğunu gösteren bir genel bakış gösterilmektedir.

### <a name="using-the-containers-dashboard"></a>Kapsayıcılar panosunu kullanma

**Kapsayıcılar** kutucuğuna tıklayın. Buradan görünümleri görürsünüz:

- **Kapsayıcı olayları** -kapsayıcı durumunu ve başarısız kapsayıcıları olan bilgisayarları gösterir.
- **Kapsayıcı günlükleri** -zaman içinde oluşturulan bir kapsayıcı günlük dosyası grafiği ve en çok sayıda günlük dosyası olan bilgisayarların listesini gösterir.
- **Kubernetes olayları** -zaman içinde oluşturulan bir Kubernetes olayı grafiği ve olayların neden üretilme nedenlerinin bir listesini gösterir. *Bu veri kümesi yalnızca Linux ortamlarında kullanılır.*
- **Kubernetes ad alanı envanteri** -ad alanları ve düğüm sayısını gösterir ve bunların hiyerarşisini gösterir. *Bu veri kümesi yalnızca Linux ortamlarında kullanılır.*
- **Kapsayıcı düğümü envanteri** -kapsayıcı düğümlerinde/konaklarda kullanılan düzenleme türlerinin sayısını gösterir. Bilgisayar düğümleri/konaklar da kapsayıcı sayısına göre listelenir. *Bu veri kümesi yalnızca Linux ortamlarında kullanılır.*
- **Kapsayıcı görüntüleri envanteri** -kullanılan kapsayıcı görüntülerinin toplam sayısını ve görüntü türü sayısını gösterir. Görüntü sayısı da resim etiketi tarafından listelenir.
- **Kapsayıcılar durumu** -çalışan kapsayıcıları olan kapsayıcı düğümlerinin/konak bilgisayarlarının toplam sayısını gösterir. Bilgisayarlar, çalışan ana bilgisayar sayısına göre de listelenir.
- **Kapsayıcı işlemi** -zaman içinde çalışan bir kapsayıcı işlemleri çizgi grafiğini gösterir. Kapsayıcılar, kapsayıcılar içinde komut/işlem çalıştırılarak da listelenir. *Bu veri kümesi yalnızca Linux ortamlarında kullanılır.*
- **KAPSAYıCı CPU performansı** -bilgisayar düğümleri/konaklar için zaman IÇINDE ortalama CPU kullanımının bir çizgi grafiğini gösterir. Ayrıca, ortalama CPU kullanımına göre bilgisayar düğümlerini/konaklarınızı listeler.
- **Kapsayıcı bellek performansı** -zaman içinde bellek kullanımının bir çizgi grafiğini gösterir. Ayrıca, örnek adına göre bilgisayar belleği kullanımını listeler.
- **Bilgisayar performansı** -zaman içinde CPU performansının yüzdesi, zaman içinde bellek kullanımının yüzdesi ve zaman içinde megabayt boş disk alanı hakkında çizgi grafikleri gösterir. Daha fazla ayrıntı görüntülemek için grafikteki herhangi bir satırın üzerine gelebilmeniz gerekir.

Panonun her alanı, toplanan verilerde çalıştırılan bir aramanın görsel gösterimidir.

![Kapsayıcılar panosu](./media/containers/containers-dash01.png)

![Kapsayıcılar panosu](./media/containers/containers-dash02.png)

**Kapsayıcı durumu** alanında, en üstteki alana aşağıda gösterildiği gibi tıklayın.

![Kapsayıcılar durumu](./media/containers/containers-status.png)

Log Analytics, kapsayıcılarınızın durumu hakkında bilgi görüntüleyerek açılır.

![Kapsayıcılar için Log Analytics](./media/containers/containers-log-search.png)

Buradan, ilgilendiğiniz belirli bilgileri bulmak için arama sorgusunu düzenleyebilirsiniz. Günlük sorguları hakkında daha fazla bilgi için bkz. [Azure izleyici 'de günlük sorguları](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Başarısız bir kapsayıcı bulma ile ilgili sorunları giderme

Log Analytics sıfır olmayan bir çıkış kodu ile çıkış yaptıysanız kapsayıcıyı **başarısız** olarak işaretler. Hatalı **kapsayıcılar** alanındaki ortamdaki hatalara ve hatalara genel bir bakış görebilirsiniz.

### <a name="to-find-failed-containers"></a>Başarısız kapsayıcıları bulmak için

1. **Kapsayıcı durumu** alanına tıklayın.  
   ![kapsayıcıları durumu](./media/containers/containers-status.png)
2. Log Analytics açılır ve kapsayıcılarınızın durumunu aşağıdakine benzer şekilde görüntüler.  
   ![kapsayıcılar durumu](./media/containers/containers-log-search.png)
3. Başarısız satırı genişletin ve ölçütünü sorguya eklemek için + simgesini tıklatın. Sonra sorgudaki özetleme satırını açıklama satırı yapın.
   başarısız kapsayıcı ![](./media/containers/containers-state-failed-select.png)  
1. Sorguyu çalıştırın ve ardından sonuçlarda bir satırı genişleterek görüntü KIMLIĞINI görüntüleyin.  
   ![başarısız kapsayıcılar](./media/containers/containers-state-failed.png)  
1. Günlük sorgusuna aşağıdakini yazın. görüntü boyutu ve durdurulma ve başarısız görüntü sayısı gibi görüntüyle ilgili ayrıntıları görmek için `ContainerImageInventory | where ImageID == <ImageID>`.  
   başarısız kapsayıcı ![](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Kapsayıcı verileri için sorgu günlükleri

Belirli bir hatayla ilgili sorunları giderirken, ortamınızda nerede oluştuğunu görmek yardımcı olabilir. Aşağıdaki günlük türleri, istediğiniz bilgileri döndürmek için sorgular oluşturmanıza yardımcı olur.

- **Containerımageınventory** : görüntüyle düzenlenmiş bilgileri bulmaya ve görüntü kimlikleri veya boyutları gibi görüntü bilgilerini görüntülemeye çalışırken bu türü kullanın.
- **Containerınventory** : kapsayıcı konumu, adlarının ne olduğu ve hangi görüntüleri çalıştırdığı hakkında bilgi edinmek istediğinizde bu türü kullanın.
- **ContainerLog** : belirli hata günlüğü bilgilerini ve girdilerini bulmak istediğinizde bu türü kullanın.
- **ContainerNodeInventory_CL**  Kapsayıcıları bulunan konak/düğüm hakkında bilgi istediğinizde bu türü kullanın. Docker sürümü, düzenleme türü, depolama ve ağ bilgilerini sağlar.
- **ContainerProcess_CL** Kapsayıcıda çalışan işlemi hızlı bir şekilde görmek için bu türü kullanın.
- **ContainerServiceLog** – başlatma, durdurma, silme veya çekme komutları gibi Docker Daemon için denetim izi bilgilerini bulmaya çalışırken bu türü kullanın.
- **KubeEvents_CL**  Kubernetes olaylarını görmek için bu türü kullanın.
- **KubePodInventory_CL**  Küme hiyerarşisi bilgilerini anlamak istediğinizde bu türü kullanın.


### <a name="to-query-logs-for-container-data"></a>Kapsayıcı verileri için günlükleri sorgulamak için

* Kısa süre önce başarısız olduğunu bildiğiniz bir görüntü seçin ve bunun için hata günlüklerini bulun. Bir **Containerınventory** aramayla bu görüntüyü çalıştıran bir kapsayıcı adı bularak başlayın. Örneğin, `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"` arayın  
    Ubuntu kapsayıcıları için arama ![](./media/containers/search-ubuntu.png)

  Sonuçlardan herhangi bir satırı genişleterek ilgili kapsayıcının ayrıntılarını görüntüleyin.

## <a name="example-log-queries"></a>Örnek günlük sorguları

Genellikle bir örnek veya iki ile başlayan sorgular oluşturmak ve ardından bunları ortamınıza uyacak şekilde değiştirmek yararlıdır. Başlangıç noktası olarak, daha gelişmiş sorgular oluşturmanıza yardımcı olması için **örnek sorgular** alanıyla denemeler yapabilirsiniz.

![Kapsayıcılar sorguları](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Günlük sorguları kaydediliyor

Sorguları kaydetme, Azure Izleyici 'de standart bir özelliktir. Bu kayıtları kaydederek, daha sonra kullanmak üzere yararlı bir özellik bulabilirsiniz.

Faydalı bulduğunuz bir sorgu oluşturduktan sonra, günlük araması sayfasının en üstündeki **Sık Kullanılanlar** ' a tıklayarak kaydedin. Daha sonra **Pano sayfam** sayfasından daha sonra kolayca erişebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Ayrıntılı kapsayıcı veri kayıtlarını görüntülemek için [sorgu günlükleri](../log-query/log-query-overview.md) .
