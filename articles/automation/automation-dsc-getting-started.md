---
title: Azure Otomasyonu durum yapılandırması ile çalışmaya başlama
description: Azure Otomasyonu durum yapılandırması 'nda (DSC) en yaygın görevlere açıklama ve örnekler
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f943aac4a91217983963fac6f8d0b2b3ba6895a1
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243620"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Azure Otomasyonu durum yapılandırması ile çalışmaya başlama

Bu makalede, Azure Otomasyonu durum yapılandırması ile yapılandırma oluşturma, içeri aktarma ve derleme, yönetim için makine ekleme ve raporları görüntüleme gibi en yaygın görevlerin nasıl yapılacağı açıklanır. Azure Otomasyonu durum yapılandırmasının ne olduğuna ilişkin genel bakış için bkz. [Azure Otomasyonu durum yapılandırmasına genel bakış](automation-dsc-overview.md). Istenen durum yapılandırması (DSC) belgeleri için bkz. [Windows PowerShell Istenen durum yapılandırmasına genel bakış](/powershell/scripting/dsc/overview/overview).

Bu makalede, Azure Otomasyonu durum yapılandırması 'nı kullanmaya yönelik adım adım kılavuz sağlanmıştır. Bu makalede açıklanan adımları izleyerek zaten ayarlanmış bir örnek ortam istiyorsanız, şu Kaynak Yöneticisi şablonunu kullanabilirsiniz: [Azure Otomasyonu yönetilen düğüm şablonu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Bu şablon, Azure Otomasyonu durum yapılandırması tarafından yönetilen bir Azure VM de dahil olmak üzere, tamamlanmış bir Azure Otomasyonu durum yapılandırma ortamı oluşturur.

## <a name="prerequisites"></a>Prerequisites

Bu makaledeki örnekleri tamamlayabilmeniz için aşağıdakiler gereklidir:

- Bir Azure Otomasyonu hesabı. Azure Otomasyonu farklı çalıştır hesabı oluşturma yönergeleri için bkz. [Azure farklı çalıştır hesabı](automation-sec-configure-azure-runas-account.md).
- [Desteklenen bir işletim sistemi](automation-dsc-overview.md#operating-system-requirements)çalıştıran BIR Azure Resource Manager VM (klasik değil). VM oluşturma yönergeleri için, [Azure Portal Ilk Windows sanal makinenizi oluşturma](../virtual-machines/virtual-machines-windows-hero-tutorial.md) bölümüne bakın.

## <a name="creating-a-dsc-configuration"></a>DSC yapılandırması oluşturma

Düğüm atama yönteminize bağlı olarak, **Web-Server** Windows ÖZELLIĞININ (IIS) varlığını veya yokluğunu sağlayan basit bir [DSC yapılandırması](/powershell/scripting/dsc/configurations/configurations) oluşturursunuz.

1. [Vscode](https://code.visualstudio.com/docs) 'u (veya herhangi bir metin düzenleyicisini) başlatın.
1. Aşağıdaki metni yazın:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Dosyayı `TestConfig.ps1` olarak kaydedin.

Bu yapılandırma, **Web-Server** özelliğinin varlığını veya yokluğunu sağlayan her bir düğüm bloğunda, [WindowsFeature kaynağı](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource)olan bir kaynağı çağırır.

## <a name="importing-a-configuration-into-azure-automation"></a>Azure Otomasyonu 'na bir yapılandırma aktarma

Ardından, yapılandırmayı Otomasyon hesabına içeri aktarırsınız.

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. **Otomasyon hesabı** sayfasında, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** öğesini seçin.
1. **Durum Yapılandırması (DSC)** sayfasında, **yapılandırmalar** sekmesine tıklayın ve ardından **+ Ekle**' ye tıklayın.
1. **Yapılandırma Içeri aktar** sayfasında bilgisayarınızdaki `TestConfig.ps1` dosyasına gidin.

   ![\* * Içeri aktarma yapılandırması * * dikey penceresinin ekran görüntüsü](./media/automation-dsc-getting-started/AddConfig.png)

1. **Tamam**'a tıklayın.

## <a name="viewing-a-configuration-in-azure-automation"></a>Azure Otomasyonu 'nda bir yapılandırmayı görüntüleme

Bir yapılandırmayı içeri aktardıktan sonra, Azure portal görüntüleyebilirsiniz.

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. **Otomasyon hesabı** sayfasında, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** öğesini seçin.
1. **Durum Yapılandırması (DSC)** sayfasında, **yapılandırmalar** sekmesine tıklayın ve ardından **testconfig** ' e tıklayın (Bu, önceki yordamda içeri aktardığınız yapılandırmanın adıdır).
1. **Testconfig yapılandırma** sayfasında **yapılandırma kaynağını görüntüle**' ye tıklayın.

   ![TestConfig yapılandırma dikey penceresinin ekran görüntüsü](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Yapılandırma için PowerShell kodunu görüntüleyen bir **Testconfig yapılandırma kaynağı** sayfası açılır.

## <a name="compiling-a-configuration-in-azure-automation"></a>Azure Otomasyonu 'nda yapılandırma derleme

İstenen bir durumu bir düğüme uygulayabilmeniz için önce bu durumu tanımlayan bir DSC yapılandırmasının bir veya daha fazla düğüm yapılandırmasında (MOF belgesi) derlenmesi ve Automation DSC çekme sunucusuna yerleştirilmesi gerekir. Azure Otomasyonu durum yapılandırmasında yapılandırmaların derlenmesi hakkında daha ayrıntılı bir açıklama için bkz. [Azure Automation durum yapılandırmasında yapılandırmaları derleme](automation-dsc-compile.md).
Yapılandırma derlemesi hakkında daha fazla bilgi için bkz. [DSC yapılandırması](/powershell/scripting/dsc/configurations/configurations).

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. **Otomasyon hesabı** sayfasında, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. **Durum Yapılandırması (DSC)** sayfasında, **yapılandırmalar** sekmesine tıklayın ve ardından **testconfig** (daha önce içeri aktarılan yapılandırmanın adı) öğesine tıklayın.
1. **Testconfig yapılandırma** sayfasında **Derle**' ye tıklayın ve ardından **Evet**' e tıklayın. Bu, bir derleme işi başlatır.

   ![TestConfig yapılandırma sayfası vurgulama derleme düğmesinin ekran görüntüsü](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Azure Otomasyonu 'nda bir yapılandırma derlerken, oluşturulan tüm düğüm yapılandırma MOF 'lar otomatik olarak çekme sunucusuna dağıtır.

## <a name="viewing-a-compilation-job"></a>Derleme işini görüntüleme

Bir derlemeyi başlattıktan sonra **yapılandırma** sayfasında **derleme işleri** kutucuğunda görüntüleyebilirsiniz. **Derleme işleri** kutucuğu Şu anda çalışan, tamamlanan ve başarısız işleri gösterir. Bir derleme işi sayfası açtığınızda, bu iş hakkında hata veya uyarı, yapılandırmada kullanılan giriş parametreleri ve derleme günlükleri dahil olmak üzere bu işle ilgili bilgiler gösterilir.

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. **Otomasyon hesabı** sayfasında, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. **Durum Yapılandırması (DSC)** sayfasında, **yapılandırmalar** sekmesine tıklayın ve ardından **testconfig** (daha önce içeri aktarılan yapılandırmanın adı) öğesine tıklayın.
1. **Derleme işleri**altında, görüntülemek istediğiniz derleme işini seçin. Derleme işinin başlatıldığı tarihle etiketlenmiş bir **derleme işi** sayfası açılır.

   ![Derleme Işi sayfasının ekran görüntüsü](./media/automation-dsc-getting-started/CompilationJob.png)

1. İşle ilgili diğer ayrıntıları görmek için **derleme işi** sayfasında herhangi bir kutucuğa tıklayın.

## <a name="viewing-node-configurations"></a>Düğüm yapılandırmasını görüntüleme

Bir derleme işinin başarılı bir şekilde tamamlanması bir veya daha fazla yeni düğüm yapılandırması oluşturur. Düğüm yapılandırması, çekme sunucusuna dağıtılan ve bir veya daha fazla düğüm tarafından çekilmeye ve uygulanan bir MOF belgesidir. Düğüm yapılandırmalarını, Otomasyon hesabınızda **Durum Yapılandırması (DSC)** sayfasında görüntüleyebilirsiniz. Düğüm yapılandırmasının, *ConfigurationName*biçimindeki bir adı vardır. *DüğümAdı*.

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. **Otomasyon hesabı** dikey penceresinde, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. **Durum Yapılandırması (DSC)** sayfasında, **derlenmiş yapılandırmalar** sekmesine tıklayın.

   ![Derlenen konfigürasyonlar sekmesinin ekran görüntüsü](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Azure Otomasyonu durum yapılandırması ile yönetim için bir Azure VM ekleme

Azure Otomasyonu durum yapılandırması 'nı kullanarak Azure VM 'lerini (hem klasik hem de Kaynak Yöneticisi), şirket içi VM 'Lere, Linux makinelere, AWS VM 'lerine ve şirket içi fiziksel makinelere yönetebilirsiniz. Bu makalede yalnızca Azure Resource Manager VM 'Leri eklemeyi öğreneceksiniz. Diğer makine türlerini ekleme hakkında daha fazla bilgi için bkz. [Azure Otomasyonu durum yapılandırmasına göre yönetim için makine ekleme](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Azure Otomasyonu durum yapılandırmasına göre yönetim için bir Azure Resource Manager VM 'si eklemek için

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. **Otomasyon hesabı** dikey penceresinde, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. **Durum Yapılandırması (DSC)** sayfasında, **düğümler** sekmesinde **+ Ekle**' ye tıklayın.

   ![Azure VM Ekle düğmesini vurgulayan DSC düğümleri sayfasının ekran görüntüsü](./media/automation-dsc-getting-started/OnboardVM.png)

1. **Sanal makineler** sayfasında VM 'nizi seçin.
1. **Sanal makine** ayrıntısı sayfasında **+ Bağlan**' a tıklayın.

   > [!IMPORTANT]
   > Bu, [desteklenen bir işletim sistemi](automation-dsc-overview.md#operating-system-requirements)çalıştıran BIR Azure Resource Manager VM olmalıdır.

2. **Kayıt** sayfasında, **düğüm YAPıLANDıRMASı adı** kutusunda VM 'ye uygulamak istediğiniz düğüm yapılandırmasının adını seçin. Bu noktada bir ad sağlamak isteğe bağlıdır. Düğüm eklendikten sonra atanan düğüm yapılandırmasını değiştirebilirsiniz.
   **Gerekirse yeniden başlatma düğümünü**işaretleyin ve ardından **Tamam**' a tıklayın.

   ![Kayıt dikey penceresinin ekran görüntüsü](./media/automation-dsc-getting-started/RegisterVM.png)

   Belirttiğiniz düğüm yapılandırması, VM 'ye **yapılandırma modu sıklığı**tarafından belirtilen aralıklarda UYGULANıR ve VM, **yenileme sıklığıyla**belirtilen aralıklarda düğüm yapılandırmasındaki güncelleştirmeleri denetler. Bu değerlerin nasıl kullanıldığı hakkında daha fazla bilgi için bkz. [yerel Configuration Manager yapılandırma](/powershell/scripting/dsc/managing-nodes/metaConfig).

Azure, VM 'yi ekleme işlemini başlatır. Bu tamamlandığında, VM, Otomasyon hesabındaki **Durum Yapılandırması (DSC)** sayfasının **düğümler** sekmesinde görüntülenir.

## <a name="viewing-the-list-of-managed-nodes"></a>Yönetilen düğümlerin listesini görüntüleme

Otomasyon hesabınızda yönetim için eklendi olan tüm makinelerin listesini **Durum Yapılandırması (DSC)** sayfasının **düğümler** sekmesinde görüntüleyebilirsiniz.

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. **Otomasyon hesabı** dikey penceresinde, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. **Durum Yapılandırması (DSC)** sayfasında **düğümler** sekmesine tıklayın.

## <a name="viewing-reports-for-managed-nodes"></a>Yönetilen düğümler için raporları görüntüleme

Azure Otomasyonu durum yapılandırması yönetilen bir düğümde tutarlılık denetimi gerçekleştirdiğinde, düğüm bir durum raporunu çekme sunucusuna geri gönderir. Bu raporları bu düğüm için sayfada görüntüleyebilirsiniz.

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. **Otomasyon hesabı** dikey penceresinde, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. **Durum Yapılandırması (DSC)** sayfasında **düğümler** sekmesine tıklayın. Burada, yapılandırma durumuna genel bakış ve her bir düğümün ayrıntılarına bakabilirsiniz.

   ![Düğüm sayfasının ekran görüntüsü](./media/automation-dsc-getting-started/NodesTab.png)

1. **Düğümler** sekmesinden, raporlamayı açmak için düğüm kaydına tıklayın. Ek raporlama ayrıntılarını görüntülemek istediğiniz raporu tıklatın.

   ![Rapor dikey penceresinin ekran görüntüsü](./media/automation-dsc-getting-started/NodeReport.png)

Tek bir raporun dikey penceresinde, ilgili tutarlılık denetimi için aşağıdaki durum bilgilerini görebilirsiniz:

- Raporun durumu — düğümün "uyumlu", "başarısız" veya düğüm "uyumsuz" olup olmadığı (düğüm **Applyandizleyici** modundayken ve makine istenen durumda olmadığında).
- Tutarlılık denetimi için başlangıç saati.
- Tutarlılık denetimi için toplam çalışma zamanı.
- Tutarlılık denetimi türü.
- Hata kodu ve hata iletisi dahil olmak üzere herhangi bir hata.
- Yapılandırmada kullanılan DSC kaynakları ve her bir kaynağın durumu (düğüm için istenen durumda olup olmadığı) — bu kaynakla ilgili daha ayrıntılı bilgi almak için her kaynağa tıklayabilirsiniz.
- Düğümün adı, IP adresi ve yapılandırma modu.

Ayrıca, düğümün sunucuya gönderdiği gerçek verileri görmek için **Ham raporu görüntüle** ' ye de tıklayabilirsiniz.
Bu verileri kullanma hakkında daha fazla bilgi için bkz. [DSC rapor sunucusu kullanma](/powershell/scripting/dsc/pull-server/reportserver).

İlk rapor kullanılabilir hale gelmeden önce düğüm eklendi olduktan sonra bu işlem biraz zaman alabilir. Düğüm ekledikten sonra ilk rapor için 30 dakikaya kadar beklemeniz gerekebilir.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Farklı düğüm yapılandırmasına bir düğümü yeniden atama

İlk atandığınızdan farklı bir düğüm yapılandırması kullanmak için bir düğüm atayabilirsiniz.

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. **Otomasyon hesabı** dikey penceresinde, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. **Durum Yapılandırması (DSC)** sayfasında **düğümler** sekmesine tıklayın.
1. **Düğümler** sekmesinde, yeniden atamak istediğiniz düğümün adına tıklayın.
1. Bu düğüm için sayfasında, **düğüm yapılandırması ata**' ya tıklayın.

    ![Düğüm ayrıntıları sayfası, düğüm yapılandırma ata düğmesinin vurgulandığı ekran görüntüsü](./media/automation-dsc-getting-started/AssignNode.png)

1. **Düğüm yapılandırması ata** sayfasında, düğümü atamak istediğiniz düğüm yapılandırmasını seçin ve ardından **Tamam**' a tıklayın.

    ![Düğüm yapılandırma ata sayfasının ekran görüntüsü](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Düğüm kaydını silme

Artık bir düğümün Azure Automation DSC tarafından yönetilmesini istemiyorsanız, bu hesabın kaydını kaldırabilirsiniz.

1. [Azure Portal](https://portal.azure.com)oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. **Otomasyon hesabı** dikey penceresinde, **yapılandırma yönetimi**altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. **Durum Yapılandırması (DSC)** sayfasında **düğümler** sekmesine tıklayın.
1. **Düğümler** sekmesinde, kaydını kaldırmak istediğiniz düğümün adına tıklayın.
1. Bu düğümün sayfasında, **kayıt Sil**' e tıklayın.

    ![Kayıt Sil düğmesini vurgulayan düğüm Ayrıntıları sayfasının ekran görüntüsü](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>İlgili Makaleler

- [Azure Otomasyonu durum yapılandırmasına genel bakış](automation-dsc-overview.md)
- [Azure Otomasyonu durum yapılandırmasına göre yönetim için makine ekleme](automation-dsc-onboarding.md)
- [Windows PowerShell Istenen durum yapılandırmasına genel bakış](/powershell/scripting/dsc/overview/overview)
- [Azure Otomasyonu durum yapılandırması cmdlet 'leri](/powershell/module/azurerm.automation/#automation)
- [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/)
