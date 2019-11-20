---
title: Azure IoT Edge geliştirme ortamı | Microsoft Docs
description: IoT Edge modülleri oluşturmanıza yardımcı olacak desteklenen sistemler ve birinci taraf geliştirme araçları hakkında bilgi edinin
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ddfa93328fb3533a937cc7f0d81482b66275faf3
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67848958"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Geliştirme ve test ortamınızı IoT Edge için hazırlama

Azure IoT Edge, mevcut iş mantığınızı kenarda çalışan cihazlara kaydırır. Uygulamalarınızı ve iş yüklerinizi [IoT Edge modüller](iot-edge-modules.md)olarak çalışacak şekilde hazırlamak için, bunları kapsayıcı olarak oluşturmanız gerekir. Bu makalede, bir IoT Edge çözümünü başarıyla oluşturabilmeniz için geliştirme ortamınızı yapılandırma konusunda rehberlik sunulmaktadır. Geliştirme ortamınızı ayarladıktan sonra, [kendi IoT Edge modüllerinizi geliştirmeyi](module-development.md)öğrenebilirsiniz.

Herhangi bir IoT Edge çözümünde göz önünde bulundurmanız gereken en az iki makine vardır. Bunlardan biri, IoT Edge modülünü çalıştıran IoT Edge cihazdır (veya cihazlardır). Diğeri, modülleri derlemek, test etmek ve dağıtmak için kullandığınız geliştirme makinedir. Bu makale öncelikle geliştirme makinesine odaklanır. Test amacıyla, iki makine aynı olabilir. Geliştirme makinenizde IoT Edge çalıştırabilir ve buna modüller dağıtabilirsiniz.

## <a name="operating-system"></a>İşletim sistemi

Azure IoT Edge, belirli bir [desteklenen işletim sistemleri](support.md)kümesi üzerinde çalışır. IoT Edge için geliştirme için, bir kapsayıcı altyapısını çalıştıratabilen çoğu işletim sistemini kullanabilirsiniz. Kapsayıcı altyapısı, modüllerinizi kapsayıcılar olarak derlemek ve bir kapsayıcı kayıt defterine göndermek için geliştirme makinesinde bir gereksinimdir. 

Geliştirme makineniz Azure IoT Edge çalıştıramadığından, yerel olarak test etmenize ve hata ayıklamanıza yardımcı olan test [araçları](#testing-tools) hakkında bilgi edinmek için bu makaleye devam edin. 

Geliştirme makinenizin işletim sisteminin IoT Edge cihazınızın işletim sistemiyle eşleşmesi gerekmez. Ancak, kapsayıcı işletim sisteminin, geliştirme makinesi ile IoT Edge cihaz arasında tutarlı olması gerekir. Örneğin, bir Windows makinesinde modüller geliştirebilir ve bunları bir Linux cihazına dağıtabilirsiniz. Linux cihazının modüllerini derlemek için Windows makinenin Linux kapsayıcıları çalıştırması gerekir. 

## <a name="container-engine"></a>Kapsayıcı altyapısı

IoT Edge merkezi kavramı, iş ve bulut mantığınızı kapsayıcılara paketleyerek cihazlara uzaktan dağıtmanıza olanak sağlar. Kapsayıcılar oluşturmak için geliştirme makinenizde bir kapsayıcı altyapısına ihtiyacınız vardır. 

Üretimde IoT Edge cihazları için desteklenen tek kapsayıcı altyapısı, Moby ' dir. Ancak, Docker gibi açık kapsayıcı girişimi ile uyumlu olan herhangi bir kapsayıcı altyapısı, IoT Edge modül görüntüleri oluşturma özelliğine sahiptir. 

## <a name="development-tools"></a>Geliştirme araçları

Hem Visual Studio hem de Visual Studio Code, IoT Edge çözümleri geliştirmeye yardımcı olmak için eklenti uzantılarına sahiptir. Bu uzantılar yeni IoT Edge senaryoları oluşturmaya ve dağıtmaya yardımcı olacak dile özgü şablonlar sağlar. Visual Studio için Azure IoT Edge uzantıları ve Visual Studio Code IoT Edge çözümlerinizi kodlemenize, oluşturmanıza, dağıtmanıza ve hata ayıklamanıza yardımcı olur. Birden çok modül içeren bir IoT Edge çözümünün tamamını oluşturabilirsiniz ve uzantılar her yeni modül eklemesi ile bir dağıtım bildirimi şablonunu otomatik olarak güncelleştirir. Uzantılar ile, IoT cihazlarınızı Visual Studio veya Visual Studio Code içinden de yönetebilirsiniz. Bir cihaza modül dağıtın, durumu izleyin ve iletileri IoT Hub geldikçe görüntüleyin. Her iki uzantı de, geliştirme makinenizde yerel olarak çalışan ve modülleri hata ayıklamanın yanı sıra [IoT EdgeHub geliştirme aracı](#iot-edgehub-dev-tool) 'nı kullanır. 

Başka düzenleyicilerle veya CLı ile geliştirmeyi tercih ediyorsanız, Azure IoT Edge dev aracı komut satırından geliştirme ve test yapabilmeniz için komutlar sağlar. Yeni IoT Edge senaryolar oluşturabilir, modül görüntüleri oluşturabilir, modülleri benzeticide çalıştırabilir ve IoT Hub gönderilen iletileri izleyebilirsiniz. 

### <a name="visual-studio-code-extension"></a>Visual Studio Code uzantısı

Visual Studio Code için Azure IoT Edge uzantısı C, C#, Java, Node. js ve Python gibi programlama dillerinde oluşturulan IoT Edge modül şablonlarını ve içindeki C#Azure işlevlerini sağlar. 

Daha fazla bilgi ve indirme için bkz. [Visual Studio Code Için Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

IoT Edge uzantılarına ek olarak, geliştirme için ek uzantılar yüklemeyi yararlı bulabilirsiniz. Örneğin, görüntülerinizi, Kapsayıcılarınızı ve kayıt defterlerini yönetmek için [Visual Studio Code Docker desteğini](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) kullanabilirsiniz. Ayrıca, tüm başlıca desteklenen dillerin, modüller geliştirirken yardımcı olabilecek Visual Studio Code uzantıları vardır. 

#### <a name="prerequisites"></a>Önkoşullar

Bazı diller ve hizmetlerde modül şablonlarının, Visual Studio Code ile geliştirme makinenizde proje klasörlerini oluşturmak için gereken önkoşulları vardır.

| Modül şablonu | Önkoşul |
| --------------- | ------------ |
| Azure İşlevleri | [.NET Core 2,1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET Core 2,1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [JAVA_HOME ortam değişkenini ayarlama](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Node. js modül oluşturucuyu Azure IoT Edge](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019 uzantısı

Visual Studio için Azure IoT Edge araçları, ve C üzerinde C# oluşturulmuş bir IoT Edge modül şablonu sağlar. 

Daha fazla bilgi ve indirme için bkz. Visual [studio 2017 için Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) veya [Visual studio 2019 için Azure IoT Edge araçları](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>IoT Edge geliştirme aracı

Azure IoT Edge dev aracı, komut satırı becerileri ile IoT Edge geliştirmeyi basitleştirir. Bu araç, modülleri geliştirmek, hatalarını ayıklamak ve test etmek için CLı komutları sağlar. IoT Edge dev aracı, bağımlılıkları makinenize el ile yüklemişseniz veya IoT Edge dev kapsayıcısını kullanarak geliştirme sisteminizle birlikte çalışmaktadır. 

Daha fazla bilgi ve kullanmaya başlamak için bkz. [IoT Edge geliştirme aracı wiki](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Test araçları

IoT Edge cihazlarının veya hata ayıklama modüllerinin daha verimli benzetimini yapmanıza yardımcı olmak için çeşitli test araçları mevcuttur. Aşağıdaki tabloda, araçlar arasındaki üst düzey bir karşılaştırma ve sonra ayrı bölümler her bir aracı daha özel olarak açıklanmaktadır. 

Üretim dağıtımları için yalnızca IoT Edge çalışma zamanı desteklenir, ancak aşağıdaki araçlar geliştirme ve test amaçları için IoT Edge cihazları benzetim yapmanıza veya kolayca oluşturmanıza olanak tanır. Bu araçlar birbirini dışlanmamış, ancak tamamen bir geliştirme deneyimi için birlikte çalışabilir. 

| Araç | Olarak da bilinen | Desteklenen platformlar | En iyi kullanım alanı: |
| ---- | ------------- | ------------------- | --------- |
| IoT EdgeHub geliştirme aracı  | iotedgehubdev | Windows, Linux, MacOS | Modül hatalarını ayıklamak için bir cihazın benzetimini yapın. |
| IoT Edge dev kapsayıcısı | Microsoft/ıotedgedev | Windows, Linux, MacOS | Bağımlılıkları yüklemeden geliştirme. |
| Bir kapsayıcıda IoT Edge çalışma zamanı | iotedgec | Windows, Linux, MacOS, ARM | Çalışma zamanını destekleyemeyebilir bir cihazda test etme. |
| IoT Edge cihaz kapsayıcısı | toolboc/Azure-IoT-Edge-cihaz-kapsayıcı | Windows, Linux, MacOS, ARM | Çok sayıda IoT Edge cihazı olan bir senaryoyu ölçeklendirerek test etme. |

### <a name="iot-edgehub-dev-tool"></a>IoT EdgeHub geliştirme aracı

Azure IoT EdgeHub geliştirme aracı, yerel bir geliştirme ve hata ayıklama deneyimi sağlar. Araç, IoT Edge modülleri ve çözümleri yerel olarak oluşturabilmek, geliştirmek, test etmek, çalıştırmak ve hatalarını ayıklamak için IoT Edge çalışma zamanı olmadan IoT Edge modüllerle çalışmaya yardımcı olur. Bir kapsayıcı kayıt defterine görüntü göndermeniz ve bunları test için bir cihaza dağıtmanız gerekmez.

IoT EdgeHub geliştirme aracı, Visual Studio ve Visual Studio Code uzantıları ile birlikte çalışmak üzere tasarlanmıştır ve IoT Edge geliştirme aracıdır. İç döngü geliştirmeyi ve dış döngü sınamasını destekler, bu nedenle DevOps araçlarıyla de tümleştirilir. 

Daha fazla bilgi ve yüklemek için bkz. [Azure IoT EdgeHub geliştirme aracı](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>IoT Edge dev kapsayıcısı

Azure IoT Edge dev kapsayıcısı, IoT Edge geliştirme için ihtiyaç duyduğunuz tüm bağımlılıklara sahip bir Docker kapsayıcısıdır. Bu kapsayıcı, Python, Node. js ve Java dahil olmak C#üzere hangi dilde geliştirme yapmak istediğinizi kullanmaya başlamanızı kolaylaştırır. Yüklemeniz gereken tek şey, kapsayıcıyı geliştirme makinenize çekmek için Docker veya Moby gibi bir kapsayıcı altyapısıdır. 

Daha fazla bilgi için bkz. [Azure IoT Edge geliştirme kapsayıcısı](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>Bir kapsayıcıda IoT Edge çalışma zamanı

Bir kapsayıcıdaki IoT Edge çalışma zamanı, cihaz Bağlantı dizenizi ortam değişkeni olarak alan bir çalışma zamanı sağlar. Bu kapsayıcı, MacOS gibi yerel çalışma zamanını desteklemeyen bir sistemde IoT Edge modülleri ve senaryoları test etmenizi sağlar. Dağıttığınız tüm modüller çalışma zamanı kapsayıcısının dışında başlatılır. Çalışma zamanının ve dağıtılan modüllerin aynı kapsayıcı içinde mevcut olmasını istiyorsanız bunun yerine IoT Edge cihaz kapsayıcısını göz önünde bulundurun.

Daha fazla bilgi için bkz. [Azure IoT Edge bir kapsayıcıda çalıştırma](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>IoT Edge cihaz kapsayıcısı

IoT Edge cihaz kapsayıcısı, kapsayıcı altyapısı olan herhangi bir makinede başlatılmaya hazırlı bir IoT Edge cihazdır. Cihaz kapsayıcısı, IoT Edge çalışma zamanı ve kapsayıcı altyapısının kendisini içerir. Kapsayıcının her örneği, tamamen işlevsel bir kendi kendine sağlama IoT Edge cihazındır. Modül için bir ağ yolu olduğu sürece, cihaz kapsayıcısı modüllerin uzaktan hata ayıklamasını destekler. Cihaz kapsayıcısı, ölçeklendirmekte olan senaryoları veya Azure Pipelines sınamak için çok sayıda IoT Edge cihazı hızlı bir şekilde oluşturmak için uygundur. Ayrıca, Held aracılığıyla Kubernetes 'e dağıtımı da destekler. 

Daha fazla bilgi için bkz. [cihaz kapsayıcısı Azure IoT Edge](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>DevOps araçları

Kapsamlı üretim senaryolarında ölçekli çözümler geliştirmeye hazırsanız, otomasyon, izleme ve kolaylaştırılmış Yazılım Mühendisliği işlemleri dahil modern DevOps ilkelerine yararlanın. IoT Edge, Azure DevOps, Azure DevOps Projeleri ve Jenkins gibi DevOps araçlarını destekleyecek uzantılara sahiptir. Mevcut bir işlem hattını özelleştirmek veya Circlecı ya da Traviscı gibi farklı bir DevOps aracı kullanmak istiyorsanız, IoT Edge dev aracında bulunan CLı özellikleriyle bunu yapabilirsiniz.

Daha fazla bilgi, kılavuz ve örnekler için aşağıdaki sayfalara bakın:
* [Azure IoT Edge için sürekli tümleştirme ve sürekli dağıtım](how-to-ci-cd.md)
* [Azure DevOps Projeleri ile IoT Edge için bir CI/CD işlem hattı oluşturma](how-to-devops-project.md)
* [Jenkins eklentisi Azure IoT Edge](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps GitHub deposu](https://github.com/toolboc/IoTEdge-DevOps)
