---
title: Depolama hesabı yük devretmesini başlatma (Önizleme)-Azure depolama
description: Depolama hesabınızın birincil uç noktasının kullanılamaz duruma gelmesi durumunda, hesap yük devretmesini nasıl başlatacağınızı öğrenin. Yük devretme, ikincil bölgeyi depolama hesabınız için birincil bölge olacak şekilde güncelleştirir.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/11/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2bac51a86c8acdba0f6c2f03e5a24ab2b133aa8e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521001"
---
# <a name="initiate-a-storage-account-failover-preview"></a>Depolama hesabı yük devretmesini başlatma (Önizleme)

Coğrafi olarak yedekli depolama hesabınızın birincil uç noktası herhangi bir nedenle kullanılamaz duruma gelirse, hesap yük devretmesini (Önizleme) başlatabilirsiniz. Hesap yük devretmesi ikincil uç noktayı, depolama hesabınız için birincil uç nokta olacak şekilde güncelleştirir. Yük devretme işlemi tamamlandıktan sonra istemciler yeni birincil bölgeye yazmaya başlayabilir. Zorunlu yük devretme, uygulamalarınız için yüksek kullanılabilirlik sağlamanıza olanak sağlar.

Bu makalede, Azure portal, PowerShell veya Azure CLı kullanarak depolama hesabınız için hesap yük devretmesinin nasıl başlatılacağı gösterilmektedir. Hesap yük devretmesi hakkında daha fazla bilgi için bkz. [Azure Storage 'Da olağanüstü durum kurtarma ve hesap yük devretme (Önizleme)](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Hesap yük devretmesi genellikle bazı veri kaybıyla sonuçlanır. Hesap yük devretmesinin etkilerini anlamak ve veri kaybına hazırlanmak için, [Hesap yük devretme sürecini anlayın](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Depolama hesabınızda bir hesap yük devretmesi gerçekleştirebilmeniz için önce aşağıdaki adımları gerçekleştirdiğinizden emin olun:

- Hesap yük devretmesi önizlemesine kaydolun. Nasıl kaydedileceği hakkında bilgi için [Önizleme hakkında](storage-disaster-recovery-guidance.md#about-the-preview)bölümüne bakın.
- Depolama hesabınızın coğrafi olarak yedekli depolama (GRS) veya Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) kullanacak şekilde yapılandırıldığından emin olun. Coğrafi olarak yedekli depolama hakkında daha fazla bilgi için bkz. [coğrafi olarak yedekli depolama (GRS): Azure depolama Için çapraz bölgesel çoğaltma](storage-redundancy-grs.md). 

## <a name="important-implications-of-account-failover"></a>Hesap yük devretmesinin önemli etkileri

Depolama hesabınız için bir hesap yük devretmesi başlattığınızda, ikincil uç nokta için DNS kayıtları, ikincil uç nokta birincil uç nokta olacak şekilde güncelleştirilir. Yük devretme işlemine başlamadan önce depolama hesabınıza yönelik olası etkiyi anladığınızdan emin olun.

Yük devretme başlamadan önce büyük olasılıkla veri kaybı kapsamını tahmin etmek için `Get-AzStorageAccount` PowerShell cmdlet 'ini kullanarak **son eşitleme zamanı** özelliğini denetleyin ve `-IncludeGeoReplicationStats` parametresini ekleyin. Ardından, hesabınız için `GeoReplicationStats` özelliğini kontrol edin. 

Yük devretmenin ardından, depolama hesabı türü otomatik olarak yeni birincil bölgedeki yerel olarak yedekli depolama (LRS) olarak dönüştürülür. Hesap için coğrafi olarak yedekli depolamayı (GRS) veya Okuma Erişimli Coğrafi olarak yedekli depolamayı (RA-GRS) yeniden etkinleştirebilirsiniz. LRS 'den GRS 'ye veya RA-GRS ' ye dönüştürme ek bir maliyet doğurur. Daha fazla bilgi için bkz. [bant genişliği fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/bandwidth/). 

Depolama hesabınız için GRS 'yi yeniden etkinleştirdikten sonra Microsoft, hesabınızdaki verileri yeni ikincil bölgeye çoğaltmaya başlar. Çoğaltma süresi, çoğaltılan veri miktarına bağlıdır.  

## <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal hesap yük devretmesini başlatmak için aşağıdaki adımları izleyin:

1. Depolama hesabınıza gidin.
2. **Ayarlar**altında **coğrafi çoğaltma**' yı seçin. Aşağıdaki görüntüde, bir depolama hesabının coğrafi çoğaltma ve yük devretme durumu gösterilmektedir.

    ![Coğrafi çoğaltma ve yük devretme durumunu gösteren ekran görüntüsü](media/storage-initiate-account-failover/portal-failover-prepare.png)

3. Depolama hesabınızın coğrafi olarak yedekli depolama (GRS) veya Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) için yapılandırıldığını doğrulayın. Aksi takdirde, hesabınız coğrafi olarak yedekli olacak şekilde güncelleştirmek için **Ayarlar** ' ın altında **yapılandırma** ' yı seçin. 
4. **Son eşitleme zamanı** özelliği, ikinc'nin birincili hedeften gerisinde olduğunu gösterir. **Son eşitleme zamanı** , yük devretme tamamlandıktan sonra karşılaşabileceğiniz veri kaybı kapsamını tahmin eder.
5. **Yük devretme Için hazırla (Önizleme)** seçeneğini belirleyin. 
6. Onay iletişim kutusunu inceleyin. Hazırsanız, yük devretmeyi onaylamak ve başlatmak için **Evet** girin.

    ![Hesap yük devretmesi için onay iletişim kutusunu gösteren ekran görüntüsü](media/storage-initiate-account-failover/portal-failover-confirm.png)

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Bir hesap yük devretmesi başlatmak üzere PowerShell 'i kullanmak için, önce 6.0.1 Preview modülünü yüklemeniz gerekir. Modülünü yüklemek için şu adımları izleyin:

1. Azure PowerShell önceki tüm yüklemelerini kaldırın:

    - **Ayarlar**altındaki **uygulamalar & Özellikler** ayarını kullanarak Windows 'un önceki Azure PowerShell yüklemelerini kaldırın.
    - Tüm **Azure** modüllerini `%Program Files%\WindowsPowerShell\Modules`'dan kaldırın.

1. PowerShellGet 'in en son sürümüne sahip olduğunuzdan emin olun. Bir Windows PowerShell penceresi açın ve en son sürümü yüklemek için aşağıdaki komutu çalıştırın:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. PowerShellGet yükledikten sonra PowerShell penceresini kapatın ve yeniden açın. 

1. Azure PowerShell en son sürümünü yükler:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Hesap yük devretmesini destekleyen bir Azure depolama önizleme modülü yükler:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

1. PowerShell penceresini kapatıp yeniden açın.
 
PowerShell 'den bir hesap yük devretmesi başlatmak için aşağıdaki komutu yürütün:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name> 
```

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Hesap yük devretmesi başlatmak için Azure CLı 'yı kullanmak üzere aşağıdaki komutları yürütün:

```cli
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Storage 'da olağanüstü durum kurtarma ve hesap yük devretmesi (Önizleme)](storage-disaster-recovery-guidance.md)
- [RA-GRS’yi kullanarak yüksek kullanılabilirliğe sahip uygulamalar tasarlama](storage-designing-ha-apps-with-ragrs.md)
- [Öğretici: BLOB depolama ile yüksek oranda kullanılabilir bir uygulama oluşturma](../blobs/storage-create-geo-redundant-storage.md) 
