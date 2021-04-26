---
title: Ortam görüntülerinin sorunlarını giderme
titleSuffix: Azure Machine Learning
description: Ortam görüntüsü derlemeleri ve paket yüklemeleri ile ilgili sorunları nasıl giderebileceğinizi öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: ec0c7d64f2145cdaf594cb903c072984f4d376a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519138"
---
# <a name="troubleshoot-environment-image-builds"></a>Ortam görüntüsü Derlemeleriyle ilgili sorunları giderme

Docker ortam görüntü yapıları ve paket yüklemeleri ile ilgili sorunları nasıl giderebileceğinizi öğrenin.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. [Azure Machine Learning ücretsiz veya ücretli sürümünü](https://aka.ms/AMLFree)deneyin.
* [Azure MACHINE LEARNING SDK](/python/api/overview/azure/ml/install).
* [Azure CLI](/cli/azure/install-azure-cli).
* [Azure Machine Learning Için CLI uzantısı](reference-azure-machine-learning-cli.md).
* Yerel olarak hata ayıklamak için yerel sisteminizde çalışan bir Docker yüklemeniz olmalıdır.

## <a name="docker-image-build-failures"></a>Docker görüntü oluşturma sorunları
 
Çoğu görüntü derleme başarısızlıkları için, görüntü derleme günlüğünde kök nedeni bulacaksınız.
Azure Machine Learning portalından (20 \_ görüntü \_ Derleme \_log.txt) veya Azure Container Registry görev çalıştırma günlüklerinizin görüntü oluşturma günlüğünü bulun.
 
Genellikle hataları yerel olarak yeniden oluşturmak daha kolaydır. Hata türünü denetleyip aşağıdakilerden birini deneyin `setuptools` :

- Yerel olarak bir Conda bağımlılığı yükler: `conda install suspicious-dependency==X.Y.Z` .
- Yerel olarak bir PIP bağımlılığı yükler: `pip install suspicious-dependency==X.Y.Z` .
- Tüm ortamı çalıştırmayı deneyin: `conda create -f conda-specification.yml` .

> [!IMPORTANT]
> Yerel işlem kümenizdeki platform ve yorumlayıcı 'nın, uzak işlem kümesindeki olanlarla eşleştiğinden emin olun. 

### <a name="timeout"></a>Zaman aşımı 
 
Aşağıdaki ağ sorunları, zaman aşımı hatalarına neden olabilir:

- Düşük internet bant genişliği
- Sunucu sorunları
- Verilen Conda veya PIP zaman aşımı ayarlarıyla indirilebilen büyük bağımlılıklar
 
Aşağıdaki örneklere benzer iletiler sorunu gösterecektir:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Bir hata iletisi alırsanız, aşağıdaki olası çözümlerden birini deneyin:
 
- Bağımlılık için yansıtmalar, Azure Blob Storage veya diğer Python akışları gibi farklı bir kaynak deneyin.
- Conda veya PIP 'yi güncelleştirin. Özel bir Docker dosyası kullanıyorsanız, zaman aşımı ayarlarını güncelleştirin.
- Bazı PIP sürümlerinde bilinen sorunlar var. Ortam bağımlılıklarına belirli bir PIN sürümü eklemeyi göz önünde bulundurun.

### <a name="package-not-found"></a>Paket bulunamadı

Aşağıdaki hatalar görüntü derleme hataları için en yaygın olarak verilmiştir:

- Conda paketi bulunamadı:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- Belirtilen PIP paketi veya sürümü bulunamadı:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- Geçersiz iç içe nokta bağımlılığı:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Paketin belirtilen kaynaklarda mevcut olup olmadığını denetleyin. PIP bağımlılıklarını doğrulamak için [PIP aramasını](https://pip.pypa.io/en/stable/reference/pip_search/) kullanın:

- `pip search azureml-core`

Conda bağımlılıkları için [Conda aramasını](https://docs.conda.io/projects/conda/en/latest/commands/search.html)kullanın:

- `conda search conda-forge::numpy`

Daha fazla seçenek için şunu deneyin:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Yükleyici notları

Gerekli dağıtımın belirtilen platform ve Python yorumlayıcı sürümü için mevcut olduğundan emin olun.

PIP bağımlılıkları için, `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` gerekli sürümün kullanılabilir olup olmadığını görmek için bölümüne gidin. https://pypi.org/project/azureml-core/1.11.0/#filesBir örnek görmek için adresine gidin.

Conda bağımlılıkları için kanal deposundaki paketi kontrol edin.
Anaconda, Inc. tarafından tutulan kanallar için, [Anaconda paketleri sayfasını](https://repo.anaconda.com/pkgs/)kontrol edin.

### <a name="pip-package-update"></a>PIP paketi güncelleştirmesi

Bir yükleme sırasında veya bir PIP paketinin güncelleştirilmesi sırasında, çözümleyici 'nin, yeni gereksinimleri karşılamak için önceden yüklenmiş bir paketi güncelleştirmesi gerekebilir.
Kaldırma işlemi, PIP sürümü veya bağımlılığın yüklenme yöntemiyle ilgili çeşitli nedenlerden dolayı başarısız olabilir.
En yaygın senaryo, Conda tarafından yüklenen bir bağımlılığın PIP tarafından kaldırılmasına yönelik bir yöntemdir.
Bu senaryo için, kullanarak bağımlılığı kaldırmayı düşünün `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Yükleyici sorunları

Bazı yükleyici sürümlerinde, paket çözümleyicilerine bir yapı hatasına yol açabilecek sorunlar vardır.

Özel bir temel görüntü veya Dockerfile kullanıyorsanız, Conda Version 4.5.4 veya üstünü kullanmanızı öneririz.

PIP bağımlılıklarını yüklemek için bir PIP paketi gerekir. Ortamda bir sürüm belirtilmemişse, en son sürüm kullanılacaktır.
Geçici sorunları veya aracın en son sürümünün neden olabileceği değişiklikleri önlemek için, PIP 'nin bilinen bir sürümünü kullanmanızı öneririz.

Aşağıdaki iletiyi görürseniz, PIP sürümünü ortamınıza sabitlemeyi göz önünde bulundurun:

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

PIP alt işlemi hatası:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

Bağımlılıklarda çözümlenebilen çakışmalar varsa, PIP yüklemesi sonsuz bir döngüye takılmış olabilir. Yerel olarak çalışıyorsanız, PIP sürümünün < 20,3 ' e düşürüyoruz. YAML dosyasından oluşturulmuş bir Conda ortamında, bu sorunu yalnızca Conda-Forge en yüksek öncelikli kanal ise görürsünüz. Sorunu azaltmak için, Conda belirtim dosyasında bir Conda bağımlılığı olarak < 20,3 (! = 20,3 veya = 20.2.4 PIN 'i diğer sürüme sabitle) açıkça belirtin.

## <a name="service-side-failures"></a>Hizmet tarafı sorunları

Olası hizmet tarafı hatalarıyla ilgili sorunları gidermek için aşağıdaki senaryolara bakın.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>Bir kapsayıcı kayıt defterinden görüntü çekilemiyor veya bir kapsayıcı kayıt defteri için adres çözümlenemedi

Olası sorunlar:
- Kapsayıcı kayıt defterinin yol adı doğru çözümlenmeyebilir. Görüntü adlarının çift eğik çizgi ve Linux üzerinde eğik çizgilerin yönünü ve Windows konaklarına karşı doğru olup olmadığını denetleyin.
- Bir sanal ağın arkasındaki bir kapsayıcı kayıt defteri [Desteklenmeyen bir bölgede](../private-link/private-link-overview.md#availability)özel bir uç nokta kullanıyorsa, portaldan hizmet uç noktasını (genel erişim) kullanarak kapsayıcı kayıt defterini yapılandırın ve yeniden deneyin.
- Kapsayıcı kayıt defterini bir sanal ağın arkasına koyduktan sonra, çalışma alanının kapsayıcı kayıt defteri örneğiyle iletişim kurabilmesi için [Azure Resource Manager şablonunu](./how-to-network-security-overview.md) çalıştırın.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Çalışma alanı kapsayıcısı kayıt defterinden 401 hatası alırsınız

[Ws.sync_keys ()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--)kullanarak depolama anahtarlarını yeniden eşitleyin.

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>Ortam, "diğer Conda işlemlerinin bitmesi bekleniyor..." hatayla

Bir görüntü oluşturma devam ederken, Conda SDK istemcisi tarafından kilitlenir. İşlem kilitlendi veya Kullanıcı tarafından yanlış bir şekilde iptal edilirse, Conda kilitli durumda kalır. Bu sorunu çözmek için kilit dosyasını el ile silin. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>Özel Docker görüntünüz kayıt defterinde değil

[Doğru etiketin](./how-to-use-environments.md#create-an-environment) kullanıldığını ve olduğunu denetleyin `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` Conda 'yi devre dışı bırakır ve kullanıcının yüklü paketlerini kullanır.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>Aşağıdaki ortak sanal ağ sorunlarından birini alırsınız

- Depolama hesabı, işlem kümesi ve kapsayıcı kayıt defterinin tümünün sanal ağın aynı alt ağında olup olmadığını denetleyin.
- Kapsayıcı kayıt defteriniz bir sanal ağın arkasında olduğunda, doğrudan görüntü oluşturmak için kullanılamaz. Görüntü oluşturmak için işlem kümesini kullanmanız gerekir.
- Şu durumlarda depolamanın bir sanal ağın arkasına yerleştirilmesi gerekebilir:
    - Inor sınırlama veya özel tekerlek kullanın.
    - Bkz. 403 (yetkilendirilmemiş) hizmet hataları.
    - Azure Container Registry görüntü ayrıntıları alınamıyor.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>Ağ korumalı depolamaya erişmeye çalışırken görüntü oluşturma işlemi başarısız oluyor

- Azure Container Registry görevler bir sanal ağın arkasında çalışmaz. Kullanıcının kapsayıcı kayıt defteri bir sanal ağın arkasında varsa, bir görüntü oluşturmak için işlem kümesini kullanmaları gerekir.
- Bağımlılıkları bundan çekmek için depolama alanı bir sanal ağın arkasında olmalıdır.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>Depolamada ağ güvenliği etkinleştirildiğinde denemeleri çalıştıramazsınız

Varsayılan Docker görüntülerini kullanıyorsanız ve Kullanıcı tarafından yönetilen bağımlılıkları etkinleştirirseniz, microsoftcontainerregistry ve azurefrontkapısı. firstparti [hizmeti etiketlerini](./how-to-network-security-overview.md) izin Azure Container Registry ve bağımlılıklarını kullanın.

 Daha fazla bilgi için bkz. [sanal ağları etkinleştirme](./how-to-network-security-overview.md).

### <a name="you-need-to-create-an-icm"></a>Bir ICM oluşturmanız gerekir

Bir ICM 'yi oluşturma/atama yaparken, sorunu daha iyi anlayabilmemiz için CSS destek bileti ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Bir makine öğrenme modelini, çiçekleri kategorilere ayırmak için eğitme](how-to-train-scikit-learn.md)
- [Özel bir Docker görüntüsü kullanarak makine öğrenimi modelini eğitme](how-to-train-with-custom-image.md)
