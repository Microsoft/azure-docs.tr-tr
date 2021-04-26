---
title: Azure Otomasyonu 'nu kullanarak StorSimple Veri Yöneticisi bir işi başlatın
description: StorSimple Veri Yöneticisi işleri tetiklemenin Azure Otomasyonu 'nu kullanmayı öğrenin
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 3d0103857b6f759560129dbe8e693ec6c0d7291e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94961218"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>İşi tetiklemek için Azure Otomasyonu'nu kullanma

Bu makalede, StorSimple cihaz verilerini dönüştürmek için StorSimple Veri Yöneticisi hizmeti içinde veri dönüştürme özelliğini nasıl kullanabileceğiniz açıklanır. Bir veri dönüştürme işini iki şekilde başlatabilirsiniz: 

 - .NET SDK’yı kullanma
 - Azure Otomasyonu runbook 'u kullanma
 
Bu makalede, bir Azure Otomasyonu runbook 'u oluşturma ve ardından bir veri dönüştürme işi başlatmak için kullanma hakkında bilgi yer alabilir. .NET SDK aracılığıyla veri dönüştürmeyi başlatma hakkında daha fazla bilgi edinmek için [.NET SDK kullanma bölümüne giderek veri dönüştürme işlerini tetikleyin](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce, şunları kullandığınızdan emin olun:

*   İstemci bilgisayarda yüklü Azure PowerShell. [Azure PowerShell indirin](/powershell/azure/azurerm/install-azurerm-ps).
*   Bir kaynak grubundaki StorSimple Veri Yöneticisi hizmetinde doğru şekilde yapılandırılmış bir iş tanımı.
*   [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip)GitHub deposundan dosyayı indirin. 
*   [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1)GitHub deposundan betiği indirin.

## <a name="step-by-step-procedure"></a>Adım adım yordam

### <a name="set-up-the-automation-account"></a>Otomasyon hesabını ayarlama

1. Azure portal Azure farklı çalıştır Otomasyon hesabı oluşturun. Bunu yapmak için **Azure Marketi 'Ne her şeyi >** gidin ve **Otomasyon**' ı arayın. **Otomasyon hesapları**' nı seçin.

    ![Farklı Çalıştır Otomasyon hesabı oluştur](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Yeni bir Otomasyon hesabı eklemek için **+ Ekle**' ye tıklayın.

    ![Farklı Çalıştır Otomasyon hesabı oluştur 2](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. **Otomasyon Ekle**:

   1. Otomasyon hesabınızın **adını** sağlayın.
   2. StorSimple Veri Yöneticisi hizmetinize bağlı **aboneliği** seçin.
   3. Yeni bir kaynak grubu oluşturun veya var olan bir kaynak grubundan seçin.
   4. Bir **konum** seçin.
   5. Varsayılan **Farklı Çalıştır hesabı oluştur** seçeneğini seçili bırakın.
   6. Panoda hızlı erişim için bir bağlantı almak üzere **panoya sabitle**' yi işaretleyin. **Oluştur**’a tıklayın.

      ![Farklı Çalıştır Otomasyon hesabı oluşturma 3](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Otomasyon hesabı başarıyla oluşturulduktan sonra bilgilendirirsiniz.
    
      ![Otomasyon hesabı dağıtımı için bildirim](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Daha fazla bilgi için [Farklı Çalıştır hesabı oluşturma](../automation/manage-runas-account.md)bölümüne gidin.

3. Yeni oluşturulan hesapta, **paylaşılan kaynaklar > modüller** ' e gidin ve **+ Modül Ekle**' ye tıklayın.

    ![Modül 1 içeri aktar](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. `DataTransformationApp.zip`Yerel bilgisayarınızdan dosyanın konumuna gidin ve modülünü seçin ve açın. Modülü içeri aktarmak için **Tamam** ' ı tıklatın.

    ![İçeri aktarma modülü 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Azure Otomasyonu hesabınıza bir modül aktardığında modülle ilgili meta verileri ayıklar. Bu işlem birkaç dakika sürebilir.

   ![İçeri aktarma modülü 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Modülün dağıtıldığını belirten bir bildirim ve işlem tamamlandığında başka bir bildirim alırsınız.  **Modüllerindeki** durum **kullanılabilir** olarak değişir.

    ![İçeri aktarma modülü 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Otomasyon Runbook 'unu içeri aktarma, yayımlama ve çalıştırma

İş tanımını tetiklemek için Runbook 'u içeri aktarmak, yayımlamak ve çalıştırmak için aşağıdaki adımları gerçekleştirin.

1. Azure portalında, Otomasyon hesabınızı açın. **Işlem otomasyonu > runbook** 'larına gidin ve **+ runbook Ekle**' ye tıklayın.

    ![Runbook Ekle 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. **Runbook Ekle**' de **mevcut bir runbook 'u içeri aktar**' a tıklayın.

3. `Trigger-DataTransformation-Job.ps1` **Runbook dosyası** için Azure PowerShell betik dosyasına işaret edin. Runbook türü otomatik olarak seçilir. Runbook için bir ad ve isteğe bağlı bir açıklama sağlayın. **Oluştur**’a tıklayın.

    ![Runbook Ekle 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Yeni runbook, Otomasyon hesabı için Runbook 'lar listesinde görüntülenir. Bu runbook 'u seçin ve tıklayın.

    ![Runbook Ekle 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Runbook 'u düzenleyin ve **Test** Bölmesi ' ne tıklayın.

    ![Runbook Ekle 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. StorSimple Veri Yöneticisi hizmetinizin adı, ilişkili kaynak grubu ve iş tanımı adı gibi parametreleri sağlayın. Testi **başlatın** . Çalışma tamamlandığında rapor oluşturulur. Daha fazla bilgi için, [runbook 'u test](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook)etme konusuna gidin.

    ![Runbook ekle 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Test bölmesindeki runbook 'tan çıktıyı inceleyin. Memnun olursa bölmeyi kapatın. **Yayımla** ' ya tıklayın ve onay sorulduğunda runbook 'u onaylayın ve yayımlayın.

    ![Runbook Ekle 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. **Runbook 'lara** geri dönün ve yeni oluşturulan runbook 'u seçin.

    ![Runbook ekle 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. Runbook 'u **başlatın** . **Runbook 'U Başlat**' da tüm parametreleri girin. Veri dönüştürme işini göndermek ve başlatmak için **Tamam** ' ı tıklatın.

10. Azure portal iş ilerlemesini izlemek için StorSimple Veri Yöneticisi hizmetinizdeki **işler** ' e gidin. İş ayrıntılarını görüntülemek için işi seçin ve tıklayın.

    ![Runbook ekleme 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Sonraki adımlar

[Verilerinizi dönüştürmek için StorSimple veri Yöneticisi Kullanıcı arabirimini kullanın](storsimple-data-manager-ui.md).