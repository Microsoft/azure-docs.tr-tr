---
title: Visual Studio Code ile etkileşimli hata ayıklama
titleSuffix: Azure Machine Learning
description: Visual Studio Code kullanarak etkileşimli olarak hata ayıklama Azure Machine Learning kod, ardışık düzen ve dağıtım
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 09/30/2020
ms.openlocfilehash: 69a69afedbd86871987a8e62b55dfc070121cc78
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813875"
---
# <a name="interactive-debugging-with-visual-studio-code"></a>Visual Studio Code ile etkileşimli hata ayıklama



Visual Studio Code (VS Code) ve hata ayıklayıcı [GPY](https://github.com/microsoft/debugpy/)kullanarak Azure Machine Learning denemeleri, işlem hatları ve dağıtımlarda etkileşimli olarak hata ayıklamayı öğrenin.

## <a name="run-and-debug-experiments-locally"></a>Denemeleri yerel olarak çalıştır ve hata ayıkla

Machine Learning denemeleri 'i buluta göndermeden önce doğrulamak, çalıştırmak ve hatalarını ayıklamak için Azure Machine Learning uzantısını kullanın.

### <a name="prerequisites"></a>Önkoşullar

* Azure Machine Learning VS Code uzantısı (Önizleme). Daha fazla bilgi için bkz. [set up Azure Machine Learning vs Code uzantısı](tutorial-setup-vscode-extension.md).
* [Docker](https://www.docker.com/get-started)
  * Mac ve Windows için Docker Masaüstü
  * Linux için Docker motoru.
* [Python 3 karşılaştırması](https://www.python.org/downloads/)

> [!NOTE]
> Windows 'da [Docker 'ı Linux kapsayıcıları kullanacak şekilde yapılandırmayı](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)unutmayın.

> [!TIP]
> Windows için gerekli olmasa da, [Linux Için Windows alt sistemi (WSL) 2 Ile Docker kullanılması](/windows/wsl/tutorials/wsl-containers#install-docker-desktop)önemle önerilir.

> [!IMPORTANT]
> Denemenizi yerel olarak çalıştırmadan önce Docker 'ın çalıştığından emin olun.

### <a name="debug-experiment-locally"></a>Denemede yerel olarak hata ayıkla

1. VS Code, Azure Machine Learning uzantısı görünümünü açın.
1. Çalışma alanınızı içeren abonelik düğümünü genişletin. Henüz bir tane yoksa, uzantıyı kullanarak [bir Azure Machine Learning çalışma alanı oluşturabilirsiniz](how-to-manage-resources-vscode.md#create-a-workspace) .
1. Çalışma alanı düğümünü genişletin.
1. **Denemeleri** düğümüne sağ tıklayın ve **deneme oluştur**' u seçin. İstem belirdiğinde, denemeniz için bir ad girin.
1. **Denemeleri** düğümünü genişletin, çalıştırmak istediğiniz denemeyi sağ tıklatın ve **deneme Çalıştır**' ı seçin.
1. Denemenizin çalıştırılacağı seçenekler listesinden **Yerel**' i seçin.
1. **Yalnızca Windows 'Da ilk kez kullanılır**. Dosya paylaşımının izin versorulduğunda **Evet**' i seçin. Dosya paylaşımından izin uyguladığınızda, Docker 'ın komut dosyanızı kapsayıcı dizinine takmasına olanak tanır. Ayrıca, Docker 'ın, çalıştırınızdan günlükleri ve çıkışları sisteminizdeki geçici bir dizinde depolamasına de olanak tanır.
1. Denemenizin hatalarını ayıklamak için **Evet** ' i seçin. Gerekmiyorsa **Hayır**'ı seçin. Hayır seçeneğinin belirlenmesi, denemenizin hata ayıklayıcıya eklemeden yerel olarak çalıştırılmasını sağlar.
1. Çalıştırma yapılandırmanızı oluşturmak için **Yeni çalıştırma yapılandırması oluştur** ' u seçin. Çalıştırma yapılandırması, çalıştırmak istediğiniz betiği, bağımlılıkları ve veri kümelerini tanımlar. Alternatif olarak, zaten bir tane varsa, açılan listeden seçin.
    1. Ortamınızı seçin. [Azure Machine Learning](resource-curated-environments.md) birini seçerek veya kendinizinkini oluşturabilirsiniz.
    1. Çalıştırmak istediğiniz betiğin adını belirtin. Yol, VS Code açılan dizine göredir.
    1. Azure Machine Learning veri kümesi kullanmak isteyip istemediğinizi seçin. Uzantıyı kullanarak [Azure Machine Learning veri kümeleri](how-to-manage-resources-vscode.md#create-dataset) oluşturabilirsiniz.
    1. Hata ayıklayıcıyı denemenizin çalıştığı kapsayıcıya iliştirmek için hata ayıklayıcı GPY gereklidir. Hata ayıklama GPY 'yi bağımlılık olarak eklemek için, hata ayıklama **GPY Ekle**' yi seçin. Aksi takdirde **Atla**' yı seçin. Bir bağımlılık olarak hata ayıklayıcının GPY eklenmesi, denemenizin hata ayıklayıcıya iliştirilmesi gerekmeden çalışmasını sağlar.
    1. Çalışma yapılandırma ayarlarınızı içeren bir yapılandırma dosyası düzenleyicide açılır. Ayarları karşılıyoruz, **deneme gönder**' i seçin. Alternatif olarak, menü çubuğundan komut paletini (**görüntüleyin > komut paleti**) açın ve `Azure ML: Submit experiment` metin kutusuna komutu girin.
1. Denemeler gönderildikten sonra, komut dosyanızı ve çalıştırma yapılandırmanızda belirtilen yapılandırmaları içeren bir Docker görüntüsü oluşturulur.

    Docker görüntü oluşturma işlemi başladığında, `60_control_log.txt` dosya akışının içerikleri vs Code çıktı konsoluna.

    > [!NOTE]
    > Docker görüntünüzün ilk kez oluşturulması birkaç dakika sürebilir.

1. Görüntünüz oluşturulduktan sonra, hata ayıklayıcıyı başlatmak için bir istem belirir. Betiğinizdeki kesme noktalarınızı ayarlayın ve hata ayıklamayı başlatmaya hazırsanız **hata ayıklayıcıyı Başlat** ' ı seçin. Bunun yapılması, VS Code hata ayıklayıcıyı denemenizin çalıştırıldığı kapsayıcıya iliştirir. Alternatif olarak, Azure Machine Learning uzantısında, geçerli çalıştırtınız için düğümün üzerine gelin ve hata ayıklayıcıyı başlatmak için Oynat simgesini seçin.

    > [!IMPORTANT]
    > Tek bir deneme için birden çok hata ayıklama oturumuna sahip olabilirsiniz. Ancak birden çok VS Code örneği kullanarak iki veya daha fazla denemeleri hata ayıklaması yapabilirsiniz.

Bu noktada, VS Code kullanarak kodunuzun hatalarını ayıklamanıza ve Hata ayıklayabilmeniz gerekir.

Çalıştırmayı iptal etmek istediğiniz herhangi bir noktada çalışma düğümünüz öğesine sağ tıklayın ve **çalıştırmayı Iptal et**' i seçin.

Uzaktan denemenin çalışmasına benzer şekilde, günlükleri ve çıkışları denetlemek için çalıştırma düğümünüz genişletebilirsiniz.

> [!TIP]
> Ortamınızda tanımlanan aynı bağımlılıkları kullanan Docker görüntüleri, çalıştırmalar arasında yeniden kullanılır. Ancak, yeni veya farklı bir ortam kullanarak bir deneme çalıştırırsanız yeni bir görüntü oluşturulur. Bu görüntüler yerel depolama alanına kaydedildiğinden, eski veya kullanılmayan Docker görüntülerini kaldırmanız önerilir. Sisteminizdeki görüntüleri kaldırmak için [DOCKER CLI](https://docs.docker.com/engine/reference/commandline/rmi/) veya [vs Code Docker uzantısını](https://code.visualstudio.com/docs/containers/overview)kullanın.

## <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Makine öğrenmesi işlem hatlarında hata ayıklama ve sorun giderme

Bazı durumlarda, ML ardışık düzeninde kullanılan Python kodunda etkileşimli olarak hata ayıklaması yapmanız gerekebilir. VS Code ve hata ayıklama GPY kullanarak, eğitim ortamında çalışırken koda ekleyebilirsiniz.

### <a name="prerequisites"></a>Önkoşullar

* __Azure sanal ağını__ kullanmak üzere yapılandırılmış bir __Azure Machine Learning çalışma alanı__ .
* İşlem hattı adımlarının bir parçası olarak Python betikleri kullanan bir __Azure Machine Learning işlem hattı__ . Örneğin, bir PythonScriptStep.
* __Sanal ağdaki__ bir Azure Machine Learning işlem kümesi ve __eğitim için işlem hattı tarafından kullanılır__.
* __Sanal ağdaki__ bir __geliştirme ortamı__ . Geliştirme ortamı aşağıdakilerden biri olabilir:

  * Sanal ağdaki bir Azure sanal makinesi
  * Sanal ağdaki bir not defteri VM 'sinin Işlem örneği
  * VPN ya da ExpressRoute aracılığıyla sanal ağa özel ağ bağlantısı olan bir istemci makinesi.

Azure Machine Learning ile bir Azure sanal ağı kullanma hakkında daha fazla bilgi için bkz. [sanal ağ yalıtımı ve gizliliğe genel bakış](how-to-network-security-overview.md).

> [!TIP]
> Bir sanal ağın arkasında olmayan Azure Machine Learning kaynaklarıyla çalışabilseniz de sanal ağ kullanılması önerilir.

### <a name="how-it-works"></a>Nasıl çalışır?

ML işlem hattı adımlarınız Python betikleri çalıştırır. Bu betikler aşağıdaki eylemleri gerçekleştirecek şekilde değiştirilir:

1. Çalıştıkları konağın IP adresini günlüğe kaydedin. Hata ayıklayıcıyı betiğe bağlamak için IP adresini kullanın.

2. Hata ayıklayıcı GPY hata ayıklama bileşenini başlatın ve bir hata ayıklayıcının bağlanmasını bekleyin.

3. Geliştirme ortamınızdan, komut dosyasının çalıştığı IP adresini bulmak için eğitim süreci tarafından oluşturulan günlükleri izleyebilirsiniz.

4. Bir dosya kullanarak hata ayıklayıcıyı bağlamak için IP adresine VS Code söylemiş olursunuz `launch.json` .

5. Hata ayıklayıcıyı ekler ve komut dosyasında etkileşimli olarak ilereolursunuz.

### <a name="configure-python-scripts"></a>Python betikleri yapılandırma

Hata ayıklamayı etkinleştirmek için, ML işlem hattınızdaki adımlar tarafından kullanılan Python betiklerinde aşağıdaki değişiklikleri yapın:

1. Aşağıdaki içeri aktarma deyimlerini ekleyin:

    ```python
    import argparse
    import os
    import debugpy
    import socket
    from azureml.core import Run
    ```

1. Aşağıdaki bağımsız değişkenleri ekleyin. Bu bağımsız değişkenler, gereken şekilde hata ayıklayıcıyı etkinleştirmenizi ve hata ayıklayıcıyı iliştirmek için zaman aşımını ayarlamanıza olanak tanır:

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                        default=300,
                        help=f'Defines how much time the AML compute target '
                        f'will await a connection from a debugger client (VSCODE).')
    parser.add_argument('--remote_debug_client_ip', type=str,
                        help=f'Defines IP Address of VS Code client')
    parser.add_argument('--remote_debug_port', type=int,
                        default=5678,
                        help=f'Defines Port of VS Code client')
    ```

1. Aşağıdaki deyimleri ekleyin. Bu deyimler, kodun çalıştığı düğümün IP adresini günlüğe kaydetmek için geçerli çalışma bağlamını yükler:

    ```python
    global run
    run = Run.get_context()
    ```

1. `if`Hata ayıklayıcı GPY 'yi Başlatan bir ifade ekleyin ve bir hata ayıklayıcının iliştirmesini bekler. Zaman aşımından önce bir hata ayıklayıcı yoksa, komut dosyası normal olarak devam eder. `HOST`Ve `PORT` değerlerini `listen` kendi değerlerinizle değiştirdiğinizden emin olun.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        try:
            ip = args.remote_debug_client_ip
        except:
            print("Need to supply IP address for VS Code client")
        print(f'ip_address: {ip}')
        debugpy.listen(address=(ip, args.remote_debug_port))
        # Wait for the timeout for debugger to attach
        debugpy.wait_for_client()
        print(f'Debugger attached = {debugpy.is_client_connected()}')
    ```

Aşağıdaki Python örneği, `train.py` hata ayıklamayı sağlayan basit bir dosya göstermektedir:

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import debugpy
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
parser.add_argument('--remote_debug_client_ip', type=str,
                    help=f'Defines IP Address of VS Code client')
parser.add_argument('--remote_debug_port', type=int,
                    default=5678,
                    help=f'Defines Port of VS Code client')

# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    # ip = socket.gethostbyname(socket.gethostname())
    try:
        ip = args.remote_debug_client_ip
    except:
        print("Need to supply IP address for VS Code client")
    print(f'ip_address: {ip}')
    debugpy.listen(address=(ip, args.remote_debug_port))
    # Wait for the timeout for debugger to attach
    debugpy.wait_for_client()
    print(f'Debugger attached = {debugpy.is_client_connected()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>ML ardışık düzenini yapılandırma

Hata ayıklama GPY 'yi başlatmak için gereken Python paketlerini sağlamak ve çalıştırma bağlamını almak için bir ortam oluşturun ve ayarlayın `pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>']` . SDK sürümünü, kullanmakta olduğunuz bir sürümle eşleşecek şekilde değiştirin. Aşağıdaki kod parçacığı bir ortamın nasıl oluşturulacağını gösterir:

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['debugpy', 'azureml-sdk==<SDK-VERSION>'])
```

[Python betikleri Yapılandır](#configure-python-scripts) bölümünde, ml ardışık düzen adımlarınız tarafından kullanılan betiklerin yeni bağımsız değişkenleri eklenmiştir. Aşağıdaki kod parçacığı, bileşen için hata ayıklamayı etkinleştirmek ve zaman aşımı ayarlamak için bu bağımsız değişkenlerin nasıl kullanılacağını gösterir. Ayrıca, daha önce oluşturulan ortamın şu şekilde nasıl kullanılacağını gösterir `runconfig=run_config` :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300,'--remote_debug_client_ip','<VS-CODE-CLIENT-IP>','--remote_debug_port',5678],
                         compute_target=aml_compute,
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

İşlem hattı çalıştırıldığında, her adım bir alt çalıştırma oluşturur. Hata ayıklama etkinleştirilirse, değiştirilen betik, alt çalışma için içindeki aşağıdaki metne benzer bilgileri günlüğe kaydeder `70_driver_log.txt` :

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Değeri kaydedin `ip_address` . Sonraki bölümde kullanılır.

> [!TIP]
> Bu işlem hattı adımı için alt çalıştırma için çalıştırma günlüklerinden IP adresini de bulabilirsiniz. Bu bilgileri görüntüleme hakkında daha fazla bilgi için bkz. [Azure ML deneme çalıştırmaları ve ölçümlerini izleme](how-to-log-view-metrics.md).

### <a name="configure-development-environment"></a>Geliştirme ortamını yapılandırma

1. VS Code geliştirme ortamınıza hata ayıklama GPY yüklemek için aşağıdaki komutu kullanın:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    VS Code ile hata ayıklama GPY kullanma hakkında daha fazla bilgi için bkz. [Uzaktan hata ayıklama](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Hata ayıklayıcıyı çalıştıran Azure Machine Learning işlem ile iletişim kurmak üzere VS Code yapılandırmak için yeni bir hata ayıklama yapılandırması oluşturun:

    1. VS Code, __Hata Ayıkla__ menüsünü ve ardından __yapılandırma aç__' ı seçin. __launch.js__ adlı bir dosya açılır.

    1. Dosyadaki __launch.js__ , içeren satırı bulun `"configurations": [` ve sonra aşağıdaki metni ekleyin. Girişi, `"host": "<IP-ADDRESS>"` önceki bölümden günlüklerinizin DÖNDÜRDÜĞÜ IP adresiyle değiştirin. Girişi, `"localRoot": "${workspaceFolder}/code/step"` hata ayıklanan betiğin bir kopyasını içeren bir yerel dizin olarak değiştirin:

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "<IP-ADDRESS>",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Konfigürasyonlar bölümünde zaten başka girdiler varsa, eklediğiniz koddan sonra bir virgül (,) ekleyin.

        > [!TIP]
        > Özellikle işlem hatları için en iyi yöntem, kodun yalnızca adımların her biri için uygun olması için, kaynakların ayrı dizinlerde betikleri tutmaktır. Bu örnekte `localRoot` örnek değer başvuruları vardır `/code/step1` .
        >
        > Birden çok betikte hata ayıklaması yapıyorsanız, farklı dizinlerde, her komut dosyası için ayrı bir yapılandırma bölümü oluşturun.

    1. __launch.js__ dosyaya kaydedin.

### <a name="connect-the-debugger"></a>Hata ayıklayıcıyı bağlama

1. VS Code açın ve betiğin yerel bir kopyasını açın.
2. Komut dosyasının iliştirdikten sonra durdurulmasını istediğiniz kesme noktalarını ayarlayın.
3. Alt işlem betiği çalıştırırken ve `Timeout for debug connection` günlüklerde görüntülenirken, F5 tuşunu veya __Hata Ayıkla__' yı seçin. İstendiğinde, __Azure Machine Learning işlem: uzaktan hata ayıklama__ yapılandırması ' nı seçin. Ayrıca, yan çubukta hata ayıklama simgesini, Azure Machine Learning: uzaktan hata ayıklama açılan menüsünden __Uzaktan hata ayıklama__ girişini seçebilir ve ardından hata ayıklayıcıyı eklemek için yeşil oku kullanabilirsiniz.

    Bu noktada, VS Code işlem düğümündeki hata ayıklama GPY 'ye bağlanır ve daha önce ayarladığınız kesme noktasında durmaktadır. Artık kodu çalışırken, değişkenleri görüntülerken vb. adımları izleyebilirsiniz.

    > [!NOTE]
    > Günlükte belirten bir giriş görüntüleniyorsa `Debugger attached = False` , zaman aşımı süresi doldu ve betik hata ayıklayıcı olmadan devam eder. İşlem hattını yeniden gönder ve `Timeout for debug connection` iletiden sonra, zaman aşımı süresi dolmadan önce hata ayıklayıcıyı bağla.

## <a name="debug-and-troubleshoot-deployments"></a>Hata ayıklama ve Dağıtımda sorun giderme

Bazı durumlarda, model dağıtımınızda bulunan Python kodunda etkileşimli olarak hata ayıklaması yapmanız gerekebilir. Örneğin, giriş betiği başarısız olursa ve neden ek günlüğe kaydetme ile saptanamaz. VS Code ve hata ayıklama GPY kullanarak, Docker kapsayıcısının içinde çalışan koda iliştirebilirsiniz.

> [!IMPORTANT]
> Bu hata ayıklama yöntemi, `Model.deploy()` `LocalWebservice.deploy_configuration` bir modeli yerel olarak dağıtırken ve kullanılırken çalışmaz. Bunun yerine, [model. Package ()](/python/api/azureml-core/azureml.core.model.model#package-workspace--models--inference-config-none--generate-dockerfile-false-) yöntemini kullanarak bir görüntü oluşturmanız gerekir.

Yerel Web hizmeti dağıtımları, yerel sisteminizde çalışan bir Docker yüklemesi gerektirir. Docker kullanma hakkında daha fazla bilgi için [Docker belgelerine](https://docs.docker.com/)bakın. İşlem örnekleriyle çalışırken Docker 'ın zaten yüklü olduğunu unutmayın.

### <a name="configure-development-environment"></a>Geliştirme ortamını yapılandırma

1. Yerel VS Code geliştirme ortamınıza hata ayıklama GPY yüklemek için şu komutu kullanın:

    ```bash
    python -m pip install --upgrade debugpy
    ```

    VS Code ile hata ayıklama GPY kullanma hakkında daha fazla bilgi için bkz. [Uzaktan hata ayıklama](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection).

1. Docker görüntüsüyle iletişim kurmak üzere VS Code yapılandırmak için yeni bir hata ayıklama yapılandırması oluşturun:

    1. VS Code, __çalışma__ uzantısı 'Ndaki __hata ayıklama__ menüsünü ve ardından __açık yapılandırma__' yı seçin. __launch.js__ adlı bir dosya açılır.

    1. __launch.js__ dosyadaki __"konfigürasyonlar"__ öğesini (içeren satır `"configurations": [` ) bulun ve bundan sonra aşağıdaki metni ekleyin. 

        ```json
        {
            "name": "Azure Machine Learning Deployment: Docker Debug",
            "type": "python",
            "request": "attach",
            "connect": {
                "port": 5678,
                "host": "0.0.0.0",
            },
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```
        Ekleme işleminden sonra, dosyadaki __launch.js__ aşağıdakine benzer olmalıdır:
        ```json
        {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/linkid=830387
        "version": "0.2.0",
        "configurations": [
            {
                "name": "Python: Current File",
                "type": "python",
                "request": "launch",
                "program": "${file}",
                "console": "integratedTerminal"
            },
            {
                "name": "Azure Machine Learning Deployment: Docker Debug",
                "type": "python",
                "request": "attach",
                "connect": {
                    "port": 5678,
                    "host": "0.0.0.0"
                    },
                "pathMappings": [
                    {
                        "localRoot": "${workspaceFolder}",
                        "remoteRoot": "/var/azureml-app"
                    }
                ]
            }
            ]
        }
        ```

        > [!IMPORTANT]
        > Konfigürasyonlar bölümünde zaten başka girdiler varsa, eklediğiniz koddan sonra bir virgül ( __,__ ) ekleyin.

        Bu bölüm, __5678__ numaralı bağlantı noktasını kullanarak Docker kapsayıcısına ekler.

    1. __launch.js__ dosyaya kaydedin.

### <a name="create-an-image-that-includes-debugpy"></a>Hata ayıklama GPY içeren bir görüntü oluşturma

1. Dağıtımınız için Conda ortamını değiştirerek, hata ayıklama GPY 'yi içerir. Aşağıdaki örnek, parametresini kullanarak eklemeyi gösterir `pip_packages` :

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.83', 'debugpy'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Hata ayıklama GPY 'yi başlatmak ve hizmet başladığında bir bağlantı beklemek için, dosyanızın en üstüne aşağıdakileri ekleyin `score.py` :

    ```python
    import debugpy
    # Allows other computers to attach to debugpy on this IP address and port.
    debugpy.listen(('0.0.0.0', 5678))
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    debugpy.wait_for_client()
    print("Debugger attached...")
    ```

1. Ortam tanımına dayalı bir görüntü oluşturun ve görüntüyü yerel kayıt defterine çekin. 

    > [!NOTE]
    > Bu örnek `ws` , Azure Machine Learning çalışma alanınıza işaret ettiğini ve bu `model` modelin dağıtılmakta olduğunu varsayar. `myenv.yml`Dosya, 1. adımda oluşturulan Conda bağımlılıklarını içerir.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Görüntü oluşturulup indirildikten sonra (Bu işlem 10 dakikadan uzun sürebilir, bu nedenle lütfen haleye bekleyin), görüntü yolu (Bu örnekte de kendi Özeti olan depo, ad ve etiket dahil) son olarak aşağıdakine benzer bir iletide görüntülenir:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Görüntüyle yerel olarak çalışmayı kolaylaştırmak için, bu görüntü için bir etiket eklemek üzere aşağıdaki komutu kullanabilirsiniz. `myimagepath`Aşağıdaki komutta, önceki adımdaki konum değeriyle değiştirin.

    ```bash
    docker tag myimagepath debug:1
    ```

    Adımlar geri kalanında, `debug:1` tam görüntü yolu değeri yerine yerel görüntüye başvurabilirsiniz.

### <a name="debug-the-service"></a>Hizmette hata ayıkla

> [!TIP]
> Dosyadaki hata ayıklayıcı GPY bağlantısı için bir zaman aşımı ayarlarsanız `score.py` , zaman aşımı süresi dolmadan önce vs Code hata ayıklama oturumuna bağlamanız gerekir. VS Code başlatın, yerel kopyasını açın `score.py` , bir kesme noktası ayarlayın ve bu bölümdeki adımları kullanmadan önce başlamaya hazırlanın.
>
> Hata ayıklama ve kesme noktaları ayarlama hakkında daha fazla bilgi için bkz. [hata ayıklama](https://code.visualstudio.com/Docs/editor/debugging).

1. Görüntüyü kullanarak bir Docker kapsayıcısı başlatmak için aşağıdaki komutu kullanın:

    ```bash
    docker run -it --name debug -p 8000:5001 -p 5678:5678 -v <my_local_path_to_score.py>:/var/azureml-app/score.py debug:1 /bin/bash
    ```

    Bu `score.py` , yerel olarak kapsayıcıdaki birine ekler. Bu nedenle, düzenleyicide yapılan tüm değişiklikler otomatik olarak kapsayıcıya yansıtılır

2. Daha iyi bir deneyim için, kapsayıcıya yeni bir VS Code arabirimiyle gidebilirsiniz. `Docker`Vs Code yan çubukta uzantı seçin, bu belgede oluşturulan yerel kapsayıcınızı bulun `debug:1` . Bu kapsayıcıya sağ tıklayın ve öğesini seçin `"Attach Visual Studio Code"` , ardından yeni bir vs Code arabirimi otomatik olarak açılır ve bu arabirim oluşturduğunuz kapsayıcının içini gösterir.

    ![Kapsayıcı VS Code arabirimi](./media/how-to-troubleshoot-deployment/container-interface.png)

3. Kapsayıcının içinde, kabukta aşağıdaki komutu çalıştırın

    ```bash
    runsvdir /var/runit
    ```
    Sonra kapsayıcının içindeki kabukta aşağıdaki çıktıyı görebilirsiniz:

    ![Kapsayıcı konsol çıkışını çalıştırma](./media/how-to-troubleshoot-deployment/container-run.png)

4. Kapsayıcı içinde hata ayıklayıcı GPY 'ye VS Code iliştirmek için, VS Code açın ve F5 tuşunu kullanın veya __Hata Ayıkla__' yı seçin. İstendiğinde, __Azure Machine Learning dağıtım: Docker hata ayıklama__ yapılandırması ' nı seçin. Ayrıca, yan çubukta uzantı __Çalıştır__ simgesini, hata ayıklama açılan menüsünden __Azure Machine Learning Deployment: Docker hata ayıklama__ girişini seçip, ardından hata ayıklayıcıyı eklemek için yeşil oku kullanabilirsiniz.

    ![Hata ayıklama simgesi, hata ayıklamayı Başlat düğmesi ve yapılandırma Seçicisi](./media/how-to-troubleshoot-deployment/start-debugging.png)
    
    Yeşil oka tıkladıktan ve hata ayıklayıcıyı iliştirdikten sonra kapsayıcı VS Code arabiriminde bazı yeni bilgileri görebilirsiniz:
    
    ![Kapsayıcı hata ayıklayıcı ekli bilgileri](./media/how-to-troubleshoot-deployment/debugger-attached.png)
    
    Ayrıca, ana VS Code arayüzde görebileceğiniz özellikler şunlardır:

    ![Score.py içinde VS Code kesme noktası](./media/how-to-troubleshoot-deployment/local-debugger.png)

Artık, `score.py` kapsayıcıya bağlı olan yerel, ayarladığınız kesme noktalarında zaten durmuş. Bu noktada, VS Code Docker kapsayıcısının içinde hata ayıklama GPY 'ye bağlanır ve daha önce ayarladığınız kesme noktasında Docker kapsayıcısını sonlandırır. Artık kodu çalışırken, değişkenleri görüntülerken vb. adımları izleyebilirsiniz.

Python 'da hata ayıklamak için VS Code kullanma hakkında daha fazla bilgi için bkz. [Python kodunuzda hata ayıklama](https://code.visualstudio.com/docs/python/debugging).

### <a name="stop-the-container"></a>Kapsayıcıyı durdurma

Kapsayıcıyı durdurmak için aşağıdaki komutu kullanın:

```bash
docker stop debug
```

## <a name="next-steps"></a>Sonraki adımlar

VS Code uzak olarak ayarladığınıza göre, kodunuzda etkileşimli olarak hata ayıklamak için VS Code uzaktan işlem olarak bir işlem örneği kullanabilirsiniz. 

Sorun giderme hakkında daha fazla bilgi edinin:

* [Yerel model dağıtımı](how-to-troubleshoot-deployment-local.md)
* [Uzak model dağıtımı](how-to-troubleshoot-deployment.md)
* [Makine öğrenmesi işlem hatları](how-to-debug-pipelines.md)
* [ParallelRunStep](how-to-debug-parallel-run-step.md)

