---
title: Azure Otomasyonu durum yapılandırması 'nı kullanmaya başlama
description: Bu makalede, Azure Otomasyonu durum yapılandırmasında en sık kullanılan görevlerin nasıl yapılacağı açıklanır.
services: automation
ms.subservice: dsc
ms.date: 04/15/2019
ms.topic: conceptual
ms.openlocfilehash: 95072970004ae53663f130a78af36a84a6fab685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051507"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Azure Otomasyonu durum yapılandırması 'nı kullanmaya başlama

Bu makalede, yapılandırma oluşturma, içeri aktarma ve derleme, makinelerin yönetilmesi ve raporların görüntülenmesi gibi Azure Otomasyonu durum yapılandırması ile en yaygın görevleri gerçekleştirmek için adım adım kılavuz sağlanmıştır. Genel Bakış durumu yapılandırması için bkz. [durum yapılandırmasına genel bakış](automation-dsc-overview.md). Istenen durum yapılandırması (DSC) belgeleri için bkz. [Windows PowerShell Istenen durum yapılandırmasına genel bakış](/powershell/scripting/dsc/overview/overview).

Bu makalede açıklanan adımları izleyerek zaten ayarlanmış bir örnek ortam istiyorsanız, [Azure Otomasyonu yönetilen düğüm şablonunu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration)kullanabilirsiniz. Bu şablon, durum yapılandırması (DSC) tarafından yönetilen bir Azure VM de dahil olmak üzere, bir tamamen durum yapılandırması (DSC) ortamı oluşturur.

## <a name="prerequisites"></a>Önkoşullar

Bu makaledeki örnekleri tamamlayabilmeniz için aşağıdakiler gereklidir:

- Azure Otomasyonu hesabı. Otomasyon hesabı ve gereksinimleri hakkında daha fazla bilgi edinmek için bkz. [Otomasyon hesabı kimlik doğrulamasına genel bakış](./automation-security-overview.md).
- [Desteklenen bir işletim sistemi](automation-dsc-overview.md#operating-system-requirements)çalıştıran BIR Azure Resource Manager VM (klasik değil). VM oluşturma yönergeleri için bkz. [Azure portalında ilk Windows sanal makinenizi oluşturma](../virtual-machines/windows/quick-create-portal.md)

## <a name="create-a-dsc-configuration"></a>DSC yapılandırması oluşturma

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
1. Dosyayı **TestConfig.ps1** olarak kaydedin.

Bu yapılandırma, [WindowsFeature kaynağı](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource)olan her düğüm bloğunda bir kaynağı çağırır. Bu kaynak, **Web-Server** özelliğinin varlığını veya yokluğunu sağlar.

## <a name="import-a-configuration-into-azure-automation"></a>Azure Otomasyonu 'na bir yapılandırma içeri aktarma

Ardından, yapılandırmayı Otomasyon hesabına içeri aktarırsınız.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. Otomasyon hesabı sayfasında, **yapılandırma yönetimi** altında **Durum Yapılandırması (DSC)** öğesini seçin.
1. Durum Yapılandırması (DSC) sayfasında, **yapılandırmalar** sekmesine tıklayın ve ardından **Ekle**' ye tıklayın.
1. Yapılandırma al bölmesinde `TestConfig.ps1` bilgisayarınızdaki dosyaya gidin.

   ![* * Içeri aktarma yapılandırması * * dikey penceresinin ekran görüntüsü](./media/automation-dsc-getting-started/AddConfig.png)

1. **Tamam**'a tıklayın.

## <a name="view-a-configuration-in-azure-automation"></a>Azure Otomasyonu 'nda bir yapılandırmayı görüntüleme

Bir yapılandırmayı içeri aktardıktan sonra, Azure portal görüntüleyebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. Otomasyon hesabı sayfasında, **yapılandırma yönetimi** altında **Durum Yapılandırması (DSC)** öğesini seçin.
1. Durum Yapılandırması (DSC) sayfasında, **yapılandırmalar** sekmesine tıklayın ve ardından **testconfig**' e tıklayın. Bu, önceki yordamda içeri aktardığınız yapılandırmanın adıdır.
1. TestConfig Yapılandırma bölmesinde **yapılandırma kaynağını görüntüle**' ye tıklayın.

   ![TestConfig yapılandırma dikey penceresinin ekran görüntüsü](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Yapılandırma için PowerShell kodunu görüntüleyen bir TestConfig yapılandırma kaynağı bölmesi açılır.

## <a name="compile-a-configuration-in-azure-automation"></a>Azure Otomasyonu 'nda yapılandırma derleme

İstenen bir durumu bir düğüme uygulayabilmeniz için önce bu durumu tanımlayan bir DSC yapılandırmasının bir veya daha fazla düğüm yapılandırmasında (MOF belgesi) derlenmesi ve Automation DSC çekme sunucusuna yerleştirilmesi gerekir. Durum Yapılandırması 'nda (DSC) yapılandırmaların derlenmesi hakkında daha ayrıntılı bir açıklama için bkz. [Azure Automation durum yapılandırmasında yapılandırmaları derleme](automation-dsc-compile.md).
Yapılandırma derlemesi hakkında daha fazla bilgi için bkz. [DSC yapılandırması](/powershell/scripting/dsc/configurations/configurations).

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. Otomasyon hesabı sayfasında, **yapılandırma yönetimi** altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. Durum Yapılandırması (DSC) sayfasında, **yapılandırmalar** sekmesine tıklayın ve ardından **testconfig**' e tıklayın. Bu, daha önce içeri aktarılan yapılandırmanın adıdır.
1. TestConfig Yapılandırma bölmesinde **Derle**' ye tıklayın ve ardından **Evet**' e tıklayın. Bu, bir derleme işi başlatır.

   ![TestConfig yapılandırma sayfası vurgulama derleme düğmesinin ekran görüntüsü](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Azure Otomasyonu 'nda bir yapılandırma derlerken, oluşturulan düğüm yapılandırması MOF dosyalarını otomatik olarak çekme sunucusuna dağıtır.

## <a name="view-a-compilation-job"></a>Derleme işini görüntüleme

Bir derlemeyi başlattıktan sonra **yapılandırma** sayfasında **derleme işleri** kutucuğunda görüntüleyebilirsiniz. **Derleme işleri** kutucuğu Şu anda çalışan, tamamlanan ve başarısız işleri gösterir. Bir derleme işi bölmesi açtığınızda, bu iş hakkında hata veya uyarı, yapılandırmada kullanılan giriş parametreleri ve derleme günlükleri dahil olmak üzere bu işle ilgili bilgiler gösterilir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. Otomasyon hesabı sayfasında, **yapılandırma yönetimi** altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. Durum Yapılandırması (DSC) sayfasında, **yapılandırmalar** sekmesine tıklayın ve ardından **testconfig**' e tıklayın. Bu, daha önce içeri aktarılan yapılandırmanın adıdır.
1. **Derleme işleri** altında, görüntülenecek derleme işini seçin. Derleme işinin başlatıldığı tarihle etiketlenmiş bir derleme Işi bölmesi açılır.

   ![Derleme Işi sayfasının ekran görüntüsü](./media/automation-dsc-getting-started/CompilationJob.png)

1. İşle ilgili diğer ayrıntıları görmek için derleme Işi bölmesindeki herhangi bir kutucuğa tıklayın.

## <a name="view-node-configurations"></a>Düğüm yapılandırmasını görüntüle

Bir derleme işinin başarılı bir şekilde tamamlanması bir veya daha fazla yeni düğüm yapılandırması oluşturur. Düğüm yapılandırması, çekme sunucusuna dağıtılan ve bir veya daha fazla düğüm tarafından çekilmeye ve uygulanan bir MOF belgesidir. Düğüm yapılandırmalarını, Otomasyon hesabınızda durum yapılandırması (DSC) sayfasında görüntüleyebilirsiniz. Düğüm yapılandırması, form içeren bir ada sahiptir `ConfigurationName.NodeName` .

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. Otomasyon hesabı sayfasında, **yapılandırma yönetimi** altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. Durum Yapılandırması (DSC) sayfasında, **derlenmiş yapılandırmalar** sekmesine tıklayın.

   ![Derlenen konfigürasyonlar sekmesinin ekran görüntüsü](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>Durum Yapılandırması ile yönetim için Azure Resource Manager VM 'yi etkinleştirme

Azure VM 'lerini (hem klasik hem de Kaynak Yöneticisi), şirket içi VM 'Lere, Linux makinelere, AWS VM 'lerine ve şirket içi fiziksel makinelere yönetmek için durum yapılandırması ' nı kullanabilirsiniz. Bu makalede yalnızca Azure Resource Manager VM 'Leri etkinleştirmeyi öğreneceksiniz. Diğer makine türlerini etkinleştirme hakkında daha fazla bilgi için bkz. [Azure Otomasyonu durum yapılandırması tarafından yönetim için makineleri etkinleştirme](automation-dsc-onboarding.md).

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. Otomasyon hesabı sayfasında, **yapılandırma yönetimi** altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. Durum Yapılandırması (DSC) sayfasında **düğümler** sekmesini seçin ve **+ Ekle**' ye tıklayın.

   ![Azure VM Ekle düğmesini vurgulayan DSC düğümleri sayfasının ekran görüntüsü](./media/automation-dsc-getting-started/OnboardVM.png)

1. Sanal makineler bölmesinde VM 'nizi seçin.
1. Sanal makine ayrıntısı bölmesinde **+ Bağlan**' a tıklayın.

   > [!IMPORTANT]
   > VM, [desteklenen bir işletim sistemi](automation-dsc-overview.md#operating-system-requirements)çalıştıran BIR Azure Resource Manager VM olmalıdır.

2. Kayıt sayfasında, **düğüm yapılandırma adı** alanında VM 'ye uygulanacak düğüm yapılandırmasının adını seçin. Bu noktada bir ad sağlamak isteğe bağlıdır. Düğümü etkinleştirdikten sonra atanan düğüm yapılandırmasını değiştirebilirsiniz.

3. **Gerekirse yeniden başlatma düğümünü** işaretleyin ve ardından **Tamam**' a tıklayın.

   ![Kayıt dikey penceresinin ekran görüntüsü](./media/automation-dsc-getting-started/RegisterVM.png)

   Belirttiğiniz düğüm yapılandırması, VM 'ye **yapılandırma modu sıklığı** için belirtilen değerle belirtilen aralıklarla uygulanır. VM, **yenileme sıklığı** değeri tarafından belirtilen aralıklarda düğüm yapılandırmasındaki güncelleştirmeleri denetler. Bu değerlerin nasıl kullanıldığı hakkında daha fazla bilgi için bkz. [yerel Configuration Manager yapılandırma](/powershell/scripting/dsc/managing-nodes/metaConfig).

Azure, VM 'yi etkinleştirme işlemini başlatır. Bu tamamlandığında, VM, Otomasyon hesabındaki durum yapılandırması (DSC) sayfasının **düğümler** sekmesinde görüntülenir.

## <a name="view-the-list-of-managed-nodes"></a>Yönetilen düğümlerin listesini görüntüleyin

Otomasyon hesabınızda yönetim için etkinleştirilen tüm makinelerin listesini durum yapılandırması (DSC) sayfasının **düğümler** sekmesinde görüntüleyebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. Otomasyon hesabı sayfasında, **yapılandırma yönetimi** altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. Durum Yapılandırması (DSC) sayfasında **düğümler** sekmesine tıklayın.

## <a name="view-reports-for-managed-nodes"></a>Yönetilen düğümlerin raporlarını görüntüleme

Her zaman durum yapılandırması yönetilen bir düğümde tutarlılık denetimi gerçekleştirdiğinde, düğüm bir durum raporunu çekme sunucusuna geri gönderir. Bu raporları bu düğüm için sayfada görüntüleyebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. Otomasyon hesabı sayfasında, **yapılandırma yönetimi** altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. Durum Yapılandırması (DSC) sayfasında **düğümler** sekmesine tıklayın. Burada, yapılandırma durumuna genel bakış ve her bir düğümün ayrıntılarına bakabilirsiniz.

   ![Düğüm sayfasının ekran görüntüsü](./media/automation-dsc-getting-started/NodesTab.png)

1. **Düğümler** sekmesinden, raporlamayı açmak için düğüm kaydına tıklayın. Ek raporlama ayrıntılarını görüntülemek istediğiniz raporu tıklatın.

   ![Rapor dikey penceresinin ekran görüntüsü](./media/automation-dsc-getting-started/NodeReport.png)

Tek bir raporun dikey penceresinde, ilgili tutarlılık denetimi için aşağıdaki durum bilgilerini görebilirsiniz:

- Rapor durumu. Olası değerler şunlardır:
    * Uyumlu-düğüm, denetim ile uyumludur.
   * Başarısız-yapılandırma denetimi başarısız oldu.
   * Uyumlu değil-düğüm `ApplyandMonitor` modda ve makine istenen durumda değil.
- Tutarlılık denetimi için başlangıç saati.
- Tutarlılık denetimi için toplam çalışma zamanı.
- Tutarlılık denetimi türü.
- Hata kodu ve hata iletisi dahil olmak üzere herhangi bir hata.
- Yapılandırmada kullanılan DSC kaynakları ve her bir kaynağın durumu (düğümün istenen durumda olup olmadığı). Bu kaynakla ilgili daha ayrıntılı bilgi almak için her kaynağa tıklayabilirsiniz.
- Düğümün adı, IP adresi ve yapılandırma modu.

Ayrıca, düğümün sunucuya gönderdiği gerçek verileri görmek için **Ham raporu görüntüle** ' ye de tıklayabilirsiniz.
Bu verileri kullanma hakkında daha fazla bilgi için bkz. [DSC rapor sunucusu kullanma](/powershell/scripting/dsc/pull-server/reportserver).

İlk rapor kullanılabilir olmadan önce bir düğüm etkinleştirildikten sonra biraz zaman alabilir. Bir düğümü etkinleştirdikten sonra ilk rapor için 30 dakikaya kadar beklemeniz gerekebilir.

## <a name="reassign-a-node-to-a-different-node-configuration"></a>Farklı düğüm yapılandırmasına bir düğümü yeniden atama

İlk atandığınızdan farklı bir düğüm yapılandırması kullanmak için bir düğüm atayabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. Otomasyon hesabı sayfasında, **yapılandırma yönetimi** altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. Durum Yapılandırması (DSC) sayfasında **düğümler** sekmesine tıklayın.
1. **Düğümler** sekmesinde, yeniden atamak istediğiniz düğümün adına tıklayın.
1. Bu düğüm için sayfasında, **düğüm yapılandırması ata**' ya tıklayın.

    ![Düğüm ayrıntıları sayfası, düğüm yapılandırma ata düğmesinin vurgulandığı ekran görüntüsü](./media/automation-dsc-getting-started/AssignNode.png)

1. Düğüm yapılandırması ata sayfasında, düğümü atamak istediğiniz düğüm yapılandırmasını seçin ve ardından **Tamam**' a tıklayın.

    ![Düğüm yapılandırma ata sayfasının ekran görüntüsü](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>Bir düğümün kaydını silme

Bir düğümün durum yapılandırması tarafından yönetilmesini artık istemiyorsanız, bu hesabın kaydını kaldırabilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Sol tarafta **tüm kaynaklar** ' a ve ardından Otomasyon hesabınızın adına tıklayın.
1. Otomasyon hesabı sayfasında, **yapılandırma yönetimi** altında **Durum Yapılandırması (DSC)** öğesine tıklayın.
1. Durum Yapılandırması (DSC) sayfasında **düğümler** sekmesine tıklayın.
1. **Düğümler** sekmesinde, kaydını kaldırmak istediğiniz düğümün adına tıklayın.
1. Bu düğümün bölmesinde, **kayıt Sil**' e tıklayın.

    ![Kayıt Sil düğmesini vurgulayan düğüm Ayrıntıları sayfasının ekran görüntüsü](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="next-steps"></a>Sonraki adımlar

- Genel bakış için bkz. [Azure Otomasyonu durum yapılandırmasına genel bakış](automation-dsc-overview.md).
- Ortamınızdaki VM 'Ler için özelliği etkinleştirmek üzere bkz. [Azure Otomasyonu durum yapılandırmasını etkinleştirme](automation-dsc-onboarding.md).
- PowerShell DSC 'yi anlamak için bkz. [Windows PowerShell Istenen durum yapılandırmasına genel bakış](/powershell/scripting/dsc/overview/overview).
- Fiyatlandırma bilgileri için bkz. [Azure Otomasyonu durum yapılandırması fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/).
- PowerShell cmdlet başvurusu için bkz. [az. Automation](/powershell/module/az.automation).
