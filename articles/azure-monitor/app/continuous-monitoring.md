---
title: Azure Pipelines ve Azure Application Insights ile DevOps yayın işlem hattınızı sürekli izleme | Microsoft Docs
description: Application Insights ile sürekli izlemeyi hızlı bir şekilde ayarlamaya yönelik yönergeler sağlar
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/16/2019
ms.openlocfilehash: c891cc7564a60dfd665d84d569e266332d255fa9
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677526"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Yayın ardışık düzenine sürekli izleme ekleme

Azure Pipelines, yazılım geliştirme yaşam döngüsü boyunca DevOps yayın işlem hattınızı sürekli olarak izlemeye olanak tanımak için Azure Application Insights ile tümleşir. 

Sürekli izleme sayesinde, yayın işlem hatları Application Insights ve diğer Azure kaynaklarından izleme verileri içerebilir. Yayın ardışık düzeni bir Application Insights uyarısı algıladığında, işlem hattı, uyarı çözümlenene kadar dağıtımı geçit veya geri alabilir. Tüm denetimler başarılı olursa dağıtımlar, el ile müdahale gerekmeden üretime kadar her türlü teste otomatik olarak devam edebilir. 

## <a name="configure-continuous-monitoring"></a>Sürekli izlemeyi yapılandırma

1. [Azure DevOps](https://dev.azure.com)'da bir kuruluş ve proje seçin.
   
1. Proje sayfasının sol menüsünde, işlem **hatları**  > **sürümler**' i seçin. 
   
1. **Yeni** ' nin yanındaki oku aşağı kaydırın ve **Yeni sürüm işlem hattı**' nı seçin. Ya da henüz bir işlem hattınız yoksa görüntülenen sayfada yeni işlem **hattı** ' nı seçin.
   
1. **Şablon seçin** bölmesinde, **sürekli izlemeye sahip Azure App Service dağıtımı**arayıp seçin ve ardından **Uygula**' yı seçin. 

   ![Yeni Azure Pipelines yayın işlem hattı](media/continuous-monitoring/001.png)

1. **Aşama 1** kutusunda, **aşama görevlerini görüntülemek** için köprüyü seçin.

   ![Aşama görevlerini görüntüle](media/continuous-monitoring/002.png)

1. 1\. **aşama** Yapılandırma bölmesinde aşağıdaki alanları doldurun: 

    | Parametre        | Değer |
   | ------------- |:-----|
   | **Aşama adı**      | Bir aşama adı sağlayın veya **1. aşamada**bırakın. |
   | **Azure aboneliği** | Açılır ve kullanmak istediğiniz bağlı Azure aboneliğini seçin.|
   | **Uygulama türü** | Açılır ve uygulama türü ' nü seçin. |
   | **App Service adı** | Azure App Service adını girin. |
   | **Application Insights için kaynak grubu adı**    | Açılır ve kullanmak istediğiniz kaynak grubunu seçin. |
   | **Application Insights kaynak adı** | Açılır ve seçtiğiniz kaynak grubu için Application Insights kaynağını seçin.

1. İşlem hattını varsayılan uyarı kuralı ayarlarıyla kaydetmek için, Azure DevOps penceresinde sağ üst üste **Kaydet** ' i seçin. Açıklayıcı bir açıklama girin ve ardından **Tamam**' ı seçin.

## <a name="modify-alert-rules"></a>Uyarı kurallarını değiştirme

Kullanıma hazır, **sürekli izleme şablonuyla Azure App Service dağıtımı** dört uyarı kuralına sahiptir: **kullanılabilirlik**, **başarısız istekler**, **sunucu yanıt süresi**ve **sunucu özel durumları**. Daha fazla kural ekleyebilir veya kural ayarlarını hizmet seviyesi gereksinimlerinize uyacak şekilde değiştirebilirsiniz. 

Uyarı kuralı ayarlarını değiştirmek için:

1. Yayın ardışık düzeni sayfasının sol bölmesinde **Application Insights uyarılarını Yapılandır**' ı seçin.

1. **Azure Izleyici uyarıları** bölmesinde **Uyarı kuralları**' nın yanındaki üç nokta **...** seçeneğini belirleyin.
   
1. **Uyarı kuralları** iletişim kutusunda, **kullanılabilirlik**gibi bir uyarı kuralının yanındaki açılan simgeyi seçin. 
   
1. **Eşiği** ve diğer ayarları gereksinimlerinize uyacak şekilde değiştirin.
   
   ![Uyarıyı Değiştir](media/continuous-monitoring/003.png)
   
1. **Tamam**' ı seçin ve ardından Azure DevOps penceresinde sağ üst üste **Kaydet** ' i seçin. Açıklayıcı bir açıklama girin ve ardından **Tamam**' ı seçin.

## <a name="add-deployment-conditions"></a>Dağıtım koşulları ekleme

Yayın işlem hattınızı dağıtım kapıları eklediğinizde, sizin ayarladığınız eşikleri aşan bir uyarı istenmeyen yayın yükseltmesini önler. Uyarıyı çözdükten sonra dağıtım otomatik olarak devam edebilir.

Dağıtım kapıları eklemek için:

1. Ana ardışık düzen sayfasında, **aşamalar**' ın altında, hangi aşamanın sürekli bir izleme kapısı ihtiyacı olduğuna bağlı olarak dağıtım **öncesi koşulları** veya **dağıtım sonrası koşullar** simgesini seçin.
   
   ![Dağıtım öncesi koşulları](media/continuous-monitoring/004.png)
   
1. **Dağıtım öncesi koşullar** Yapılandırma bölmesinde, **kapıları** öğesini **etkin**olarak ayarlayın.
   
1. **Dağıtım kapıları**' ın yanındaki **Ekle**' yi seçin.
   
1. Açılan menüden **Azure izleyici uyarılarını sorgula** ' yı seçin. Bu seçenek hem Azure Izleyici hem de Application Insights uyarılarınıza erişmenizi sağlar.
   
   ![Azure Izleyici uyarılarını sorgulama](media/continuous-monitoring/005.png)
   
1. **Değerlendirme seçenekleri**altında, **ağ geçitleri için yeniden değerlendirme ve kapıların** **başarısız olduğu zaman aşımı**arasındaki süre gibi ayarlar için istediğiniz değerleri girin. 

## <a name="view-release-logs"></a>Yayın günlüklerini görüntüle

Dağıtım kapısı davranışını ve diğer sürüm adımlarını yayın günlüklerinde görebilirsiniz. Günlükleri açmak için:

1. Ardışık düzen sayfasının sol menüsünde **yayınlar** ' ı seçin. 
   
1. Herhangi bir sürüm seçin. 
   
1. **Aşamalar**' ın altında, bir sürüm özetini görüntülemek için herhangi bir aşamayı seçin. 
   
1. Günlükleri görüntülemek için, yayın özetinde **günlükleri görüntüle** ' yi seçin, herhangi bir aşamada **başarılı** veya **başarısız** köprüyü seçin ya da herhangi bir aşamanın üzerine gelin ve **Günlükler**' i seçin. 
   
   ![Yayın günlüklerini görüntüle](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure Pipelines hakkında daha fazla bilgi için [Azure Pipelines belgelerine](https://docs.microsoft.com/azure/devops/pipelines)bakın.
