---
title: Cloud Services kullanın (genişletilmiş destek) (Önizleme)
description: Visual Studio ile Azure Resource Manager kullanarak bir Azure bulut hizmeti oluşturup dağıtmayı öğrenin
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 80aa160c53b278137467dba2afa41384c7c4f378
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722679"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Visual Studio kullanarak bir Azure bulut hizmeti (genişletilmiş destek) oluşturma ve dağıtma

[Visual Studio 2019 sürüm 16,9](https://visualstudio.microsoft.com/vs/preview/) (Şu anda önizleme aşamasında) ile başlayarak, Azure kaynaklarının bakımını ve yönetimini büyük ölçüde kolaylaştıran ve moderntiren Azure Resource Manager (ARM) kullanarak bulut hizmetleriyle çalışabilirsiniz. Bu, Cloud Services (genişletilmiş destek) olarak adlandırılan yeni bir Azure hizmeti tarafından etkinleştirilir. Mevcut bir bulut hizmetini Cloud Services (genişletilmiş destek) olarak yayımlayabilirsiniz. Bu Azure hizmeti hakkında daha fazla bilgi için bkz. [Cloud Services (genişletilmiş destek) belgeleri](overview.md).

> [!IMPORTANT]
> Cloud Services (genişletilmiş destek) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Aboneliğiniz için özelliği kaydedin
Cloud Services (genişletilmiş destek) Şu anda önizleme aşamasındadır. Aboneliğiniz için özelliği aşağıdaki şekilde kaydedin:

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
Daha fazla bilgi için bkz. [Cloud Services dağıtmaya yönelik önkoşullar (genişletilmiş destek)](deploy-prerequisite.md)

## <a name="create-a-project"></a>Proje oluşturma

Visual Studio, **Azure bulut hizmeti (genişletilmiş destek)** adlı genişletilmiş destek Ile Azure bulut hizmeti oluşturmanıza olanak sağlayan bir proje şablonu sağlar. Bulut hizmeti, genel amaçlı basit bir Azure hizmetidir. Proje oluşturulduktan sonra, Visual Studio bulut hizmetini yapılandırmanıza, hata ayıklamanıza ve Azure 'da dağıtmanıza olanak sağlar.

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Visual Studio 'da bir Azure bulut hizmeti (genişletilmiş destek) projesi oluşturmak için

Bu bölümde, bir veya daha fazla web rolü ile Visual Studio 'da bir Azure bulut hizmeti projesi oluşturma işlemi adım adım açıklanmaktadır.

1. Başlangıç penceresinden **Yeni proje oluştur**' u seçin.

1. Arama kutusuna *Cloud* yazın ve ardından **Azure bulut hizmeti (genişletilmiş destek)** öğesini seçin.

   ![Genişletilmiş Destek ile yeni Azure bulut hizmeti](./media/choose-project-template.png)

1. Projeye bir ad verin ve **Oluştur**' a tıklayın.

   ![Projeye bir ad verin](./media/configure-new-project.png)

1. **Yeni Microsoft Azure bulut hizmeti** iletişim kutusunda eklemek istediğiniz rolleri seçin ve sağ ok düğmesini seçerek bunları çözümünüze ekleyin.

    ![Yeni Azure bulut hizmeti rolleri seçin](./media/choose-roles.png)

1. Eklediğiniz bir rolü yeniden adlandırmak için, **yeni Microsoft Azure bulut hizmeti** iletişim kutusundaki rol üzerine gelin ve bağlam menüsünden **Yeniden Adlandır**' ı seçin. Ayrıca, çözümünüzde bir rolü ( **Çözüm Gezgini**) eklendikten sonra yeniden adlandırabilirsiniz.

    ![Azure bulut hizmeti rolünü yeniden adlandır](./media/new-cloud-service-rename.png)

Visual Studio Azure projesinde, çözümdeki rol projeleriyle ilişkiler vardır. Proje ayrıca *hizmet tanım dosyasını* ve *hizmet yapılandırma dosyasını* da içerir:

- **Hizmet tanımı dosyası** -uygulamanız için gerekli olan, uç noktaların ve sanal makine boyutu dahil olmak üzere çalışma zamanı ayarlarını tanımlar.
- **Hizmet yapılandırma dosyası** -bir rolün kaç örneğinin çalıştırılacağını ve bir rol için tanımlanan ayarların değerlerini yapılandırır.

Bu dosyalar hakkında daha fazla bilgi için bkz. [Visual Studio Ile Azure bulut hizmeti Için rolleri yapılandırma](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service).

## <a name="publish-a-cloud-service"></a>Bulut hizmeti yayımlama

1. Visual Studio 'da bir Azure bulut hizmeti projesi oluşturun veya açın.

1. **Çözüm Gezgini**, projeye sağ tıklayın ve bağlam menüsünden **Yayımla**' yı seçin.

   ![Oturum açma sayfası](./media/publish-step-1.png)

1. **Hesap** -hesap açılır listesinden bir hesap seçin veya hesap **Ekle** ' yi seçin.

1. **Aboneliğinizi seçin** -dağıtımınız için kullanılacak aboneliği seçin. Cloud Services dağıtmak için kullandığınız aboneliğin (genişletilmiş destek) rol tabanlı erişim denetimi (RBAC) ile atanmış sahip veya katkıda bulunan rolleri olması gerekir. Aboneliğiniz bu rollerden herhangi birine sahip değilse, daha fazla devam etmeden önce bu rolü eklemek için [rol ataması ekleme adımlarına](../role-based-access-control/role-assignments-steps.md) bakın.

1. **Ayarlar** sayfasına gitmek için **İleri ' yi** seçin.

   ![Ortak ayarlar](./media/publish-settings.png)

1. **Bulut hizmeti** -açılan menüyü kullanarak var olan bir bulut hizmetini seçin ya da **Yeni oluştur**' u seçin ve bir bulut hizmeti oluşturun. Veri merkezi her bir bulut hizmeti için parantez içinde görüntülenir. Bulut hizmeti için veri merkezi konumunun, depolama hesabı için veri merkezi konumuyla aynı olması önerilir.

   Yeni bir bulut hizmeti oluşturmayı seçerseniz, **bulut hizmeti oluştur (genişletilmiş destek)** iletişim kutusunu görürsünüz. Bulut hizmeti için kullanmak istediğiniz konumu ve kaynak grubunu belirtin.

   ![Genişletilmiş Destek ile bir bulut hizmeti oluşturma](./media/extended-support-dialog.png)

1. **Derleme yapılandırması** - **Hata Ayıkla** veya **Yayınla**' yı seçin.

1. **Hizmet yapılandırması** - **bulut** ya da **Yerel**' i seçin.

1. **Depolama hesabı** -bu dağıtım için kullanılacak depolama hesabını seçin veya depolama hesabı oluşturmak Için **Yeni oluştur** ' u seçin. Bölge, her depolama hesabı için parantez içinde görüntülenir. Depolama hesabı için veri merkezi konumunun, bulut hizmeti için veri merkezi konumuyla aynı olması önerilir (ortak ayarlar).

   Azure depolama hesabı, uygulama dağıtımı için paketi depolar. Uygulama dağıtıldıktan sonra, paket depolama hesabından kaldırılır.

1. **Key Vault** -bu bulut hizmeti için gizli dizileri içeren Key Vault belirleyin. Bu, uzak masaüstü etkinleştirilmişse veya yapılandırmaya sertifikalar eklendiyse etkinleştirilir.

1. **Tüm roller Için uzak masaüstünü etkinleştir** -hizmete uzaktan bağlanabiliyor olmanız için bu seçeneği belirleyin. Kimlik bilgilerini belirtmeniz istenecektir.

   ![Uzak Masaüstü ayarları](./media/remote-desktop-configuration.png)

1. **Tanılama ayarları** sayfasına gitmek için **İleri ' yi** seçin.

   ![Tanılama ayarları](./media/diagnostics-settings.png)

   Tanılama, bir Azure bulut hizmetinde (veya Azure sanal makinesinde) sorun gidermenize olanak sağlar. Tanılama hakkında daha fazla bilgi için bkz. [Azure Cloud Services ve sanal makineler Için tanılamayı yapılandırma](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines). Application Insights hakkında daha fazla bilgi için bkz. [Application Insights nedir?](../azure-monitor/app/app-insights-overview.md).

1. **Özet** sayfasına geçmek için **İleri ' yi** seçin.

   ![Özet](./media/publish-summary.png)

1. **Hedef profil** -seçtiğiniz ayarlardan bir yayımlama profili oluşturmayı tercih edebilirsiniz. Örneğin, bir test ortamı için bir profil ve bir üretim için başka bir profil oluşturabilirsiniz. Bu profili kaydetmek için **Kaydet** simgesini seçin. Sihirbaz profili oluşturur ve Visual Studio projesine kaydeder. Profil adını değiştirmek için, **hedef profil** listesini açın ve ardından **Yönet...** öğesini seçin.

   > [!Note]
   > Yayımlama profili, Visual Studio 'da Çözüm Gezgini görüntülenir ve profil ayarları. azurePubxml uzantılı bir dosyaya yazılır. Ayarlar, XML etiketlerinin öznitelikleri olarak kaydedilir.

1. Projenizin dağıtımı için tüm ayarları yapılandırdıktan sonra, iletişim kutusunun alt kısmında **Yayımla** ' yı seçin. İşlem durumunu Visual Studio 'daki **Azure etkinlik günlüğü** çıkışı penceresinde izleyebilirsiniz.

Tebrikler! Genişletilmiş destek bulut hizmeti projenizi Azure 'da yayımladınız. Aynı ayarlarla yeniden yayımlamak için, yayımlama profilini yeniden kullanabilir veya yeni bir tane oluşturmak için bu adımları yineleyebilirsiniz.

## <a name="clean-up-azure-resources"></a>Azure kaynaklarını Temizleme

Bu öğreticiyi izleyerek oluşturduğunuz Azure kaynaklarını temizlemek için [Azure Portal](https://portal.azure.com)gidin, **kaynak grupları**' nı seçin, hizmeti oluşturmak için kullandığınız kaynak grubunu bulup açın ve **kaynak grubunu sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

**Yayımla** ekranında **Yapılandır** düğmesini kullanarak sürekli tümleştirme (CI) ayarlayın. Daha fazla bilgi için bkz. [Azure Pipelines belgeleri](/azure/devops/pipelines).