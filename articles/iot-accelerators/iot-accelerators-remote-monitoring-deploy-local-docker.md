---
title: Uzaktan Izleme çözümünü yerel olarak dağıtma-Docker-Azure | Microsoft Docs
description: Bu nasıl yapılır kılavuzunda, test ve geliştirme için Docker kullanarak uzaktan izleme çözümü hızlandırıcısının yerel makinenize nasıl dağıtılacağı gösterilmektedir.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888829"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Uzaktan Izleme çözüm Hızlandırıcısını yerel olarak dağıtma-Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Bu makalede, test ve geliştirme için uzaktan Izleme çözümü hızlandırıcısının yerel makinenize nasıl dağıtılacağı gösterilmektedir. Mikro hizmetleri yerel Docker kapsayıcılarına dağıtmayı öğrenirsiniz. Yerel bir mikro hizmet dağıtımı, bulutta IoT Hub, Cosmos DB, Azure Akış Analizi ve Azure Time Series Insights Hizmetleri kullanır.

Uzaktan Izleme çözüm hızlandırıcıyı yerel makinenizde bir IDE 'de çalıştırmak istiyorsanız, bkz. [Uzaktan izleme çözüm Hızlandırıcısını yerel olarak dağıtma-Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Ön koşullar

Uzaktan Izleme çözümü Hızlandırıcısı tarafından kullanılan Azure hizmetlerini dağıtmak için etkin bir Azure aboneliğine ihtiyacınız vardır.

Hesabınız yoksa yalnızca birkaç dakika içinde ücretsiz bir deneme sürümü hesabı oluşturabilirsiniz. Ayrıntılar için bkz. [Azure Ücretsiz Deneme](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Makine Kurulumu

Yerel dağıtımı tamamlamaya yönelik olarak, yerel geliştirme makinenizde aşağıdaki araçların yüklü olması gerekir:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) -mikro hizmetlerde değişiklik yapmayı planlıyorsanız.
* [Node. js V8](https://nodejs.org/) -bu yazılım, betiklerin Azure kaynakları oluşturmak IÇIN KULLANDıĞı bilgisayar CLI için bir önkoşuldur. Node. js ile v10 arasındaki kullanmayın.

> [!NOTE]
> Bu araçlar, Windows, Linux ve iOS gibi birçok platformda mevcuttur.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Mikro hizmetleri Docker 'da çalıştırma

**Start. cmd** betiği tarafından ayarlanan ortam değişkenlerine erişiminizin olduğundan emin olmak için yeni bir komut istemi açın. Windows 'da, aşağıdaki komutu çalıştırarak ortam değişkenlerinin ayarlandığını doğrulayabilirsiniz:

```cmd
set PCS
```

Komut **Start. cmd** betiği tarafından ayarlanan tüm ortam değişkenlerini gösterir.

Yerel makinenizde Docker 'ın çalıştığından emin olun.
> [!NOTE]
> Docker, Windows üzerinde çalışıyorsa [Linux kapsayıcıları](https://docs.docker.com/docker-for-windows/) çalıştırıyor olmalıdır.

Yerel Docker kapsayıcılarında çalışan mikro hizmetlerin Azure bulut hizmetlerine erişmesi gerekir. Bir kapsayıcı içinden Internet adresine ping göndermek için aşağıdaki komutu kullanarak Docker ortamınızın Internet bağlantısını test edebilirsiniz:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Çözüm hızlandırıcıyı çalıştırmak için, komut satırı ortamınızda **yerel klasör\\hizmetler\\betikleri** ' ne gidin ve şu komutu çalıştırın:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> `docker-compose up`çalıştırmadan önce Docker ile [yerel bir sürücü paylaştığınızdan](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) emin olun.

Bu komutu ilk kez çalıştırdığınızda Docker, kapsayıcıları yerel olarak oluşturmak için Docker Hub 'ından mikro hizmet görüntülerini indirir. Aşağıdaki çalışmalarda Docker Kapsayıcıları hemen çalıştırır.

> [!TIP]
> Microsoft sıklıkla yeni işlevlerle yeni Docker görüntüleri yayımlar. En son olanları çekmeden önce yerel Docker Kapsayıcılarınızı ve ilgili görüntüleri temizlemek için aşağıdaki komut kümesini kullanabilirsiniz:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Kapsayıcılardan günlükleri görüntülemek için ayrı bir Shell kullanabilirsiniz. Önce `docker ps` komutunu kullanarak kapsayıcı KIMLIĞINI bulun. Ardından, belirtilen kapsayıcının son 1000 girişlerini görüntülemek için `docker logs {container-id} --tail 1000` kullanın.

### <a name="start-the-stream-analytics-job"></a>Stream Analytics işini Başlat

Stream Analytics işini başlatmak için aşağıdaki adımları izleyin:

1. [Azure portalına](https://portal.azure.com) gidin.
1. Çözümünüz için oluşturulan **kaynak grubuna** gidin. Kaynak grubunun adı, **Start. cmd** betiğini çalıştırdığınızda çözümünüz için seçtiğiniz addır.
1. Kaynak listesinde **Stream Analytics işe** tıklayın.
1. Stream Analytics iş **genel bakış** sayfasında **Başlat** düğmesine tıklayın. Şimdi işi başlatmak için **Başlat** ' a tıklayın.

### <a name="connect-to-the-dashboard"></a>Panoya Bağlan

Uzaktan Izleme çözümü panosuna erişmek için tarayıcınızda `http://localhost:8080` gidin. Artık Web Kullanıcı arabirimini ve yerel mikro hizmetleri kullanabilirsiniz.

## <a name="clean-up"></a>Temizleme

Gereksiz ücretlerden kaçınmak için, testlerinizi bitirdiğinizde Azure aboneliğinizden bulut hizmetlerini kaldırın. Hizmetleri kaldırmak için [Azure Portal](https://ms.portal.azure.com) gidin ve **Start. cmd** betiğinin oluşturulduğu kaynak grubunu silin.

Docker görüntülerini kaldırmak ve yerel makinenizde boş alan boşaltmak için `docker-compose down --rmi all` komutunu kullanın. Ayrıca, kaynak kodu GitHub 'dan Klonladığınız zaman oluşturulan uzaktan Izleme deposunun yerel kopyasını da silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Uzaktan Izleme çözümünü dağıttığınıza göre, bir sonraki adım [çözüm panosunun yeteneklerini araştırmakta](quickstart-remote-monitoring-deploy.md).
