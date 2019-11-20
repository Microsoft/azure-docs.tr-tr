---
title: Ortamları Azure DevTest Labs Azure Pipelines ile tümleştirin | Microsoft Docs
description: Azure DevTest Labs ortamlarını Azure DevOps sürekli tümleştirme (CI) ve sürekli teslim (CD) işlem hatları ile tümleştirmeyi öğrenin.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: c665c65dcda2266a7acd7bc78726179d559f4d64
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163921"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Ortamları Azure DevOps CI/CD işlem hatlarınızla tümleştirin
Sürekli tümleştirme (CI)/sürekli teslim (CD) derleme ve yayın işlem hattınızı Azure ile kolayca tümleştirmek için Azure DevOps Services yüklenen Azure DevTest Labs görevleri uzantısını (eski adıyla Visual Studio Team Services) kullanabilirsiniz DevTest Labs. Bu uzantılar, belirli bir test görevi için bir [ortamı](devtest-lab-test-env.md) hızlı bir şekilde dağıtmayı kolaylaştırır ve sonra test tamamlandığında onu siler. 

Bu makalede bir ortamın nasıl oluşturulduğu ve dağıtılacağı ve sonra ortamın tümünü bir bütün işlem hattında nasıl silineceği gösterilir. Bu görevlerin her birini kendi özel yapı-test-dağıtım işlem hattınızda tek tek gerçekleştirirsiniz. Bu makalede kullanılan uzantılar, bu [oluşturma/silme DTL VM görevlerine](devtest-lab-integrate-ci-cd-vsts.md)ek niteliğindedir:

- Ortam oluşturma
- Bir ortamı silme

## <a name="before-you-begin"></a>Başlamadan önce
CI/CD işlem hattınızı Azure DevTest Labs tümleştirmenize başlamadan önce, Visual Studio Market [Azure DevTest Labs görevleri](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) uzantısını yükleyebilirsiniz. 

## <a name="create-and-configure-the-lab-for-environments"></a>Ortamlar için Laboratuvar oluşturma ve yapılandırma
Bu bölümde, Azure ortamının dağıtılacağı bir laboratuvarı oluşturma ve yapılandırma açıklanmaktadır.

1. Henüz bir tane yoksa [bir laboratuvar oluşturun](devtest-lab-create-lab.md) . 
2. Bu makaledeki yönergeleri izleyerek Laboratuvarı yapılandırın ve bir ortam şablonu oluşturun: [Azure Resource Manager şablonlarıyla çoklu VM ortamları ve PaaS kaynakları oluşturma](devtest-lab-create-environment-from-arm.md).
3. Bu örnek için [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/)var olan bir Azure hızlı başlangıç şablonunu kullanın.
4. **201-Web-App-redin-Cache-SQL-Database** klasörünü adım 2 ' de yapılandırılan depodaki **armtemplate** klasörüne kopyalayın.

## <a name="create-a-release-definition"></a>Yayın tanımı oluşturma
Yayın tanımı oluşturmak için aşağıdakileri yapın:

1.  **Yapı & sürüm hub 'ının** **yayınlar** sekmesinde **artı işareti (+)** düğmesini seçin.
2.  **Yayın tanımı oluştur** penceresinde **boş** şablonu seçin ve ardından **İleri**' yi seçin.
3.  **Daha sonra Seç**' i seçin ve ardından **Oluştur** ' u seçerek bir varsayılan ortamla ve bağlı yapıtlara sahip yeni bir yayın tanımı oluşturun.
4.  Kısayol menüsünü açmak için, yeni yayın tanımında, ortam adının yanındaki **üç nokta (...)** simgesini seçin ve ardından **değişkenleri Yapılandır**' ı seçin.
5.  **Yapılandırma-ortamı** penceresinde, yayın tanımı görevlerinde kullandığınız değişkenler için aşağıdaki değerleri girin:
1.  **Tınlogin**Için SQL yönetici oturum açma adını girin.
2.  **Tınloginpassword**IÇIN, SQL Yöneticisi oturum açma tarafından kullanılacak parolayı girin. Parolayı gizlemek ve güvenli hale getirmek için "asma Lock" simgesini kullanın.
3.  **DatabaseName**Için SQL veritabanı adını girin.
4.  Bu değişkenler örnek ortamlara özgüdür, farklı ortamların farklı değişkenleri olabilir.

## <a name="create-an-environment"></a>Ortam oluşturma
Dağıtımın sonraki aşaması, geliştirme veya test amacıyla kullanılacak ortamı oluşturmaktır.

1. Yayın tanımında, **Görev Ekle**' yi seçin.
2. **Görevler** sekmesinde bir ortam oluşturma görevi Azure DevTest Labs ekleyin. Görevi şu şekilde yapılandırın:
    1. **Azure RM aboneliği**Için, **kullanılabilir Azure hizmet bağlantıları** listesinden bir bağlantı seçin veya Azure aboneliğinize daha kısıtlı bir izinler bağlantısı oluşturun. Daha fazla bilgi için bkz. [Azure Resource Manager hizmeti uç noktası](/azure/devops/pipelines/library/service-endpoints).
2. **Laboratuvar adı**için, daha önce oluşturduğunuz örneğin adını seçin.
3. **Depo adı**için Kaynak Yöneticisi şablonunun (201) gönderildiği depoyu seçin.
4. **Şablon adı**için, kaynak kodu deponuza kaydettiğiniz ortamın adını seçin *. 
5. **Laboratuvar adı**, **Depo adı**ve **şablon adı** Azure Kaynak kimliklerinin kolay temsilleridir. Kolay adın el ile girilmesi hatalara neden olur, bilgileri seçmek için açılan listeleri kullanın.
6. **Ortam adı**için, laboratuvar dahilinde ortam örneğini benzersiz bir şekilde tanımlamak için bir ad girin.  Laboratuvar dahilinde benzersiz olmalıdır.
7. **Parametre dosyası** ve **Parametreler**, ortama özel parametrelerin geçirilmesine izin verir. Ya da her ikisi de parametre değerlerini ayarlamak için kullanılabilir. Bu örnek için parametreler bölümü kullanılacaktır. Ortamda tanımladığınız değişkenlerin adlarını kullanın, örneğin: `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. Ortam şablonundaki bilgiler, şablonun çıkış bölümünde geçirilebilirler. Diğer görevlerin verileri kullanabilmesi için **ortam şablonu çıktısına göre çıkış değişkenleri oluştur ' u** işaretleyin. `$(Reference name.Output Name)` izlenecek düzendeki bir modeldir. Örneğin, başvuru adı DTL ise ve şablondaki çıkış adı konumdaysa, değişken `$(DTL.location)`olur.

## <a name="delete-the-environment"></a>Ortamı silme
Son aşama Azure DevTest Labs örneğiniz için dağıttığınız ortamı silmektir. Geliştirme görevlerini yürütmeden veya dağıtılan kaynaklarda ihtiyacınız olan testleri çalıştırdıktan sonra normalde ortamı silebilirsiniz.

Yayın tanımında, **Görev Ekle**' yi seçin ve ardından **Dağıt** sekmesine bir **ortamı sil Azure DevTest Labs** ekleyin. Bunu şu şekilde yapılandırın:

1. VM 'yi silmek için bkz. [Azure DevTest Labs görevler](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. **Azure RM aboneliği**Için, **kullanılabilir Azure hizmet bağlantıları** listesinden bir bağlantı seçin veya Azure aboneliğinize daha kısıtlı bir izinler bağlantısı oluşturun. Daha fazla bilgi için bkz. [Azure Resource Manager hizmeti uç noktası](/azure/devops/pipelines/library/service-endpoints).
    2. **Laboratuvar adı**için ortamın bulunduğu Laboratuvarı seçin.
    3. **Ortam adı**için, kaldırılacak ortamın adını girin.
2. Yayın tanımı için bir ad girin ve sonra kaydedin.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makalelere bakın: 
- [Kaynak Yöneticisi şablonlarıyla çoklu VM ortamları oluşturun](devtest-lab-create-environment-from-arm.md).
- DevTest Labs [GitHub deposundan](https://github.com/Azure/azure-quickstart-templates)DevTest Labs otomasyonu için hızlı başlangıç Kaynak Yöneticisi şablonları.
- [VSTS sorun giderme sayfası](/azure/devops/pipelines/troubleshooting)

