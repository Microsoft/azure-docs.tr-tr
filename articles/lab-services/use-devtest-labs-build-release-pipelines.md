---
title: Azure Pipelines oluşturma ve yayınlama işlem hatları 'nda DevTest Labs kullanma | Microsoft Docs
description: Azure Pipelines derleme ve serbest bırakma işlem hatlarında Azure DevTest Labs nasıl kullanacağınızı öğrenin.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2019
ms.author: spelluru
ms.openlocfilehash: 032f598fed765b281d4a6a124f8855abc201ee94
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774467"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Azure Pipelines derleme ve yayınlama işlem hatları 'nda DevTest Labs kullanın
Bu makalede, DevTest Labs Azure Pipelines oluşturma ve yayınlama işlem hatları 'nda nasıl kullanılabileceği hakkında bilgiler verilmektedir. 

## <a name="overall-flow"></a>Genel akış
Temel akış, aşağıdaki görevleri gerçekleştiren bir **Yapı ardışık düzenine** sahip olur:

1. Uygulama kodunu oluşturun.
1. DevTest Labs 'de temel ortamı oluşturun.
1. Ortamı özel bilgilerle güncelleştirin.
1. Uygulamayı DevTest Labs ortamına dağıtma
1. Kodu test edin. 

Yapı başarıyla tamamlandıktan sonra, **yayın işlem hattı** hazırlama veya üretim dağıtımı için derleme yapıtlarını kullanır. 

Gerekli Şirket dışında, test ekosisteminin yeniden oluşturulması için gereken tüm bilgilerin, Azure kaynaklarının yapılandırması da dahil olmak üzere derleme yapıtlarında kullanılabilir olması gerekir. Azure kaynakları kullanılırken bir ücret doğurduğuna göre, şirketler bu kaynakların kullanımını denetlemek veya izlemek ister. Bazı durumlarda, kaynakları oluşturmak ve yapılandırmak için kullanılan Azure Resource Manager şablonlar bunun gibi başka bir departman tarafından yönetilebilir. Bu şablonlar, farklı bir depoda depolanabilir. Bir yapının oluşturulduğu ve test edileceği ve hem kodun hem de yapılandırmanın derleme yapıtları içinde depolanması gerektiğinde sistemi üretimde doğru bir şekilde yeniden oluşturmak için gereken ilgi çekici bir duruma yol açar. 

Yapı/test aşamasında DevTest Labs 'i kullanarak, yayın aşamasında, test etmek için kullanılan tam yapılandırmanın üretime dağıtılması için yapı kaynaklarına Azure Resource Manager şablonları ve destekleyici dosyalar ekleyebilirsiniz. Doğru yapılandırmayla **Azure DevTest Labs ortamı oluştur** görevi, Kaynak Yöneticisi şablonlarını derleme yapıtlarına kaydeder. Bu örnek için [öğreticideki kodu kullanacaksınız: Azure 'da Web uygulamasını dağıtmak ve test etmek için Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)' de bir .NET Core ve SQL veritabanı Web uygulaması oluşturun.

![Genel akış](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Azure kaynakları ayarlama
Önceden oluşturulması gereken birkaç öğe vardır:

- İki depo. İlk biri öğreticiden kodu ve iki ek VM ile Kaynak Yöneticisi şablonu. İkinci bir, temel Azure Resource Manager şablonu (varolan yapılandırma) içerir.
- Üretim kodu ve yapılandırma dağıtımı için bir kaynak grubu.
- Bir laboratuvarın derleme işlem hattının [yapılandırma deposu](devtest-lab-create-environment-from-arm.md) ile ayarlanması gerekir. Kaynak Yöneticisi şablonu, DevTest Labs 'in şablonu tanımasını ve dağıtmasını sağlamak için Metadata. JSON ile azuredeploy. JSON olarak yapılandırma deposuna işaretlenmelidir.

Yapı işlem hattı bir DevTest Labs ortamı oluşturur ve test için kodu dağıtır.

## <a name="set-up-a-build-pipeline"></a>Derleme işlem hattı ayarlama
Azure Pipelines, [öğreticideki kodu kullanarak bir yapı işlem hattı oluşturun: Azure App Service](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)' de bir .NET Core ve SQL veritabanı Web uygulaması oluşturun. Kodu derlemek, test etmek ve yayımlamak için gerekli görevi doldurmayacak **ASP.NET Core** şablonunu kullanın.

![ASP.NET şablonunu seçin](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

DevTest Labs içinde ortam oluşturmak ve ortama dağıtmak için üç ek görev eklemeniz gerekir.

![Üç görevle işlem hattı](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Ortam görevi oluşturma
Ortam Oluştur görevinde (**ortam oluşturma görevi Azure DevTest Labs** ) aşağıdaki değerleri seçmek için açılan listeleri kullanın:

- Azure aboneliği
- Laboratuvarın adı
- Deponun adı
- şablonun adı (ortamın depolandığı klasörü gösterir). 

Bilgileri el ile girmek yerine sayfada açılan listeleri kullanmanızı öneririz. Bilgileri el ile girerseniz tam Azure Kaynak kimliklerini girin. Görev, kaynak kimlikleri yerine kolay adları görüntüler. 

Ortam adı, DevTest Labs içinde gösterilen görünen addır. Her derleme için benzersiz bir ad olmalıdır. Örneğin: **Testenv $ (Build. BuildId)** . 

Kaynak Yöneticisi şablonuna bilgi geçirmek için parametre dosyası ya da parametre belirtebilirsiniz. 

**Ortam şablonu çıktısını temel alarak çıkış değişkenlerini oluştur** seçeneğini belirleyin ve bir başvuru adı girin. Bu örnek için, başvuru adı için **Baseenv** girin. Sonraki görevi yapılandırırken bu BaseEnv kullanacaksınız. 

![Azure DevTest Labs ortam görevi oluşturma](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Ortam görevini doldur
İkinci görev (**Azure DevTest Labs ortamı doldur** görevi), mevcut DevTest Labs ortamını güncelleştirmedir. Ortam oluşturma görevi, bu görevin ortam adını yapılandırmak için kullanılan **Baseenv. Environmentresourceıd** çıktısını verir. Bu örneğin Kaynak Yöneticisi şablonunda, **AdminUserName** ve **adminPassword**olmak üzere iki parametre vardır. 

![Azure DevTest Labs ortamı görevini doldur](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>App Service Dağıtım görevi
Üçüncü görev **Azure App Service dağıtım** görevidir. Uygulama türü **Web uygulaması** olarak ayarlanır ve App Service adı **$ (website)** olarak ayarlanır.

![App Service Dağıtım görevi](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Yayın ardışık düzenini ayarlama
İki görevle bir yayın işlem hattı oluşturursunuz: **Azure dağıtımı: Kaynak grubu** oluşturun veya güncelleştirin ve **Azure App Service dağıtın**. 

İlk görev için kaynak grubunun adını ve konumunu belirtin. Şablon konumu bağlı bir yapıdır. Kaynak Yöneticisi şablonu bağlantılı şablonlar içeriyorsa, özel bir kaynak grubu dağıtımının uygulanması gerekir. Şablon, yayımlanan bırakma yapıtında. Kaynak Yöneticisi şablonu için şablon parametrelerini geçersiz kılın. Kalan ayarları varsayılan değerlerle bırakabilirsiniz. 

İkinci görev **dağıtımı Azure App Service**için Azure aboneliğini belirtin, **uygulama türü**için **Web uygulaması** ' nı ve **App Service adının** **$ (Web sitesi)** seçeneğini belirleyin. Kalan ayarları varsayılan değerlerle bırakabilirsiniz. 

## <a name="test-run"></a>Test çalıştırması
Her iki işlem hattı da ayarlandığına göre, bir derlemeyi el ile sıraya alın ve çalıştığını görün. Sonraki adım derleme için uygun tetikleyiciyi ayarlamaya ve derlemeyi yayın işlem hattına bağlamaya yöneliktir.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın:

- [Azure DevTest Labs Azure Pipelines sürekli tümleştirme ve teslim ardışık düzenine tümleştirin](devtest-lab-integrate-ci-cd-vsts.md)
- [Ortamları Azure Pipelines CI/CD işlem hatlarınızla tümleştirin](integrate-environments-devops-pipeline.md)
