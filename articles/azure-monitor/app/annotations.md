---
title: Application Insights için sürüm ek açıklamaları | Microsoft Docs
description: Application Insights içinde Ölçüm Gezgini grafiklerinize dağıtım veya yapı işaretçileri ekleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/01/2019
ms.openlocfilehash: f9ab1f1ef2b64c07a3b0c8ddf93d6ef4293517db
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668108"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights 'de ölçüm grafiklerindeki ek açıklamalar

[Ölçüm Gezgini](../../azure-monitor/app/metrics-explorer.md) grafiklerde ek açıklamalar, yeni bir derlemeyi veya diğer önemli olayları dağıttığınız yeri gösterir. Ek açıklamalar, değişikliklerinizin uygulamanızın performansı üzerinde herhangi bir etkiye sahip olup olmadığını görmenizi kolaylaştırır. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/tasks/) yapı sistemi tarafından otomatik olarak oluşturulabilir. Ayrıca, PowerShell 'den oluşturarak istediğiniz herhangi bir olayı işaretlemek için ek açıklamalar da oluşturabilirsiniz.

> [!NOTE]
> Bu makalede, kullanımdan kaldırılan **Klasik ölçüm deneyimi**yansıtılmıştır. Ek açıklamalar yalnızca klasik deneyimde ve **[çalışma kitaplarında](../../azure-monitor/app/usage-workbooks.md)** kullanılabilir. Geçerli ölçüm deneyimi hakkında daha fazla bilgi edinmek için bkz. [Azure Ölçüm Gezgini gelişmiş özellikleri](../../azure-monitor/platform/metrics-charts.md).

![Ek açıklama örneği](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines derlemesi olan sürüm ek açıklamaları

Sürüm ek açıklamaları, Azure DevOps 'un bulut tabanlı Azure Pipelines hizmetinin bir özelliğidir.

### <a name="install-the-annotations-extension-one-time"></a>Ek açıklama uzantısını (bir kez) yükler
Sürüm ek açıklamaları oluşturabilmek için Visual Studio Market sağlanan birçok Azure DevOps uzantısını yüklemeniz gerekir.

1. [Azure DevOps](https://azure.microsoft.com/services/devops/) projenizde oturum açın.
   
1. Visual Studio Market [Sürüm ek açıklaması uzantısı](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) sayfasında, Azure DevOps kuruluşunuzu seçin ve ardından uzantıyı Azure DevOps kuruluşunuza **eklemek için Ekle** ' yi seçin.
   
   ![Bir Azure DevOps organizasyonu seçin ve ardından Install ' ı seçin.](./media/annotations/1-install.png)
   
Yalnızca Azure DevOps kuruluşunuz için uzantıyı bir kez yüklemeniz gerekir. Artık kuruluşunuzdaki tüm projeler için sürüm ek açıklamalarını yapılandırabilirsiniz.

### <a name="configure-release-annotations"></a>Sürüm ek açıklamalarını Yapılandır

Azure Pipelines sürüm şablonlarınızın her biri için ayrı bir API anahtarı oluşturun.

1. [Azure Portal](https://portal.azure.com) oturum açın ve uygulamanızı izleyen Application Insights kaynağını açın. Ya da bir tane yoksa, [Yeni bir Application Insights kaynağı oluşturun](../../azure-monitor/app/app-insights-overview.md).
   
1. **API erişimi** sekmesini açın ve **Application Insights kimliği**' ni kopyalayın.
   
   ![API erişimi altında uygulama KIMLIĞINI kopyalayın.](./media/annotations/2-app-id.png)

1. Ayrı bir tarayıcı penceresinde, Azure Pipelines dağıtımlarınızı yöneten yayın şablonunu açın veya oluşturun.
   
1. **Görev Ekle**' yi seçin ve sonra menüden **Application Insights sürüm ek açıklaması** görevi ' ni seçin.
   
   ![Görev Ekle ' yi seçin ve sürüm ek açıklaması Application Insights seçin.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Sürüm ek açıklaması görevi şu anda yalnızca Windows tabanlı aracıları desteklemektedir; Linux, macOS veya diğer aracı türleri üzerinde çalışmaz.
   
1. **Uygulama kimliği**' nin altında, **API erişimi** sekmesinden kopyaladığınız Application Insights kimliğini yapıştırın.
   
   ![Application Insights KIMLIĞINI yapıştırın](./media/annotations/4-paste-app-id.png)
   
1. Application Insights **API erişimi** penceresine geri döndüğünüzde, **API anahtarı oluştur**' u seçin. 
   
   ![API erişimi sekmesinde, API anahtarı oluştur ' u seçin.](./media/annotations/5-create-api-key.png)
   
1. **API anahtarı oluştur** penceresinde bir açıklama yazın, **ek açıklamaları yaz**' ı seçin ve ardından **anahtar oluştur**' u seçin. Yeni anahtarı kopyalayın.
   
   ![API anahtarı Oluştur penceresinde bir açıklama yazın, ek açıklamaları yaz ' ı seçin ve ardından anahtar oluştur ' u seçin.](./media/annotations/6-create-api-key.png)
   
1. Yayın Şablonu penceresinde, **değişkenler** sekmesinde, yeni API anahtarı için bir değişken tanımı oluşturmak üzere **Ekle** ' yi seçin.

1. **Ad**' ın altına `ApiKey`yazın ve **değer**' ın altına, **API erişimi** sekmesinden kopyaladığınız API anahtarını yapıştırın.
   
   ![Azure DevOps değişkenleri sekmesinde Ekle ' yi seçin, ApiKey değişkenini adlandırın ve değer altına API anahtarını yapıştırın.](./media/annotations/7-paste-api-key.png)
   
1. Şablonu kaydetmek için ana yayın şablonu penceresinde **Kaydet** ' i seçin.

## <a name="view-annotations"></a>Ek açıklamaları görüntüle
Şimdi yeni bir sürüm dağıtmak için yayın şablonunu kullandığınızda Application Insights için bir ek açıklama gönderilir. Ek açıklamalar **Ölçüm Gezgini**grafiklerde grafikler üzerinde görüntülenir.

İstek sahibi, kaynak denetimi dalı, yayın işlem hattı ve ortam dahil olmak üzere sürüm hakkındaki ayrıntıları açmak için herhangi bir ek açıklama işaretini (açık gri ok) seçin.

![Bir sürüm ek açıklaması işaretleyicisi seçin.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>PowerShell 'den özel ek açıklamalar oluşturma
Azure DevOps kullanmadan istediğiniz herhangi bir işlemden ek açıklama oluşturmak için GitHub ' dan [Createreleaseannotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell betiğini kullanabilirsiniz. 

1. [Createreleaseannotation. ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)yerel bir kopyasını oluşturun.
   
1. Application Insights KIMLIĞINIZI almak ve Application Insights **API erişimi** sekmesinizden bir API anahtarı oluşturmak için yukarıdaki yordamdaki adımları kullanın.
   
1. Aşağıdaki kodla PowerShell betiğini çağırın ve açılı ayraçlı yer tutucuları değerlerinizle değiştirin. `-releaseProperties` isteğe bağlıdır. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Örneğin, geçmiş için ek açıklamalar oluşturmak üzere betiği değiştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [İş öğeleri Oluştur](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [PowerShell ile otomasyon](../../azure-monitor/app/powershell.md)
