---
title: Azure Analysis Services veritabanı yedekleme ve geri yükleme | Microsoft Docs
description: Bu makalede, model meta verilerinin ve verilerinin bir Azure Analysis Services veritabanından nasıl yedeklenme ve geri yükleneceği açıklanmaktadır.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3f63ecf560a14248fed6dea53c30a27acdf9a938
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573440"
---
# <a name="backup-and-restore"></a>Yedekleme ve geri yükleme

Tablo modeli veritabanlarının Azure Analysis Services yedeklenmesi, şirket içi Analysis Services kadar çok aynıdır. Birincil fark, yedekleme dosyalarınızı depoladığınız yerdir. Yedekleme dosyaları, bir [Azure depolama hesabındaki](../storage/common/storage-create-storage-account.md)bir kapsayıcıya kaydedilmelidir. Zaten sahip olduğunuz bir depolama hesabı ve kapsayıcısı kullanabilir ya da sunucunuz için depolama ayarları yapılandırılırken oluşturulabilir.

> [!NOTE]
> Depolama hesabı oluşturmak, yeni bir faturalanabilir hizmete neden olabilir. Daha fazla bilgi için bkz. [Azure Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Yedeklemeler bir. abf uzantısıyla kaydedilir. Bellek içi tablolu modeller için hem model verileri hem de meta veriler depolanır. DirectQuery tablolu modeller için yalnızca model meta verileri depolanır. Yedeklemeler, seçtiğiniz seçeneklere bağlı olarak sıkıştırılabilir ve şifrelenebilir.


## <a name="configure-storage-settings"></a>Depolama ayarlarını yapılandırma
Yedeklemeden önce, sunucunuz için depolama ayarlarını yapılandırmanız gerekir.


### <a name="to-configure-storage-settings"></a>Depolama ayarlarını yapılandırmak için
1.  Azure portal > **ayarları**' nda **Yedekle**' ye tıklayın.

    ![Ayarlarda yedeklemeler](./media/analysis-services-backup/aas-backup-backups.png)

2.  **Etkin**' e ve ardından **depolama ayarları**' na tıklayın.

    ![Etkinleştirme](./media/analysis-services-backup/aas-backup-enable.png)

3. Depolama hesabınızı seçin veya yeni bir tane oluşturun.

4. Bir kapsayıcı seçin veya yeni bir tane oluşturun.

    ![Kapsayıcı seçme](./media/analysis-services-backup/aas-backup-container.png)

5. Yedekleme ayarlarınızı kaydedin.

    ![Yedekleme ayarlarını kaydet](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>SSMS kullanarak yedeklemek için

1. SSMS 'de, **yedekleme**> bir veritabanına sağ tıklayın.

2. **Yedekleme veritabanı** > **Yedekleme dosyasında**, **Araştır**' a tıklayın.

3. **Dosyayı farklı kaydet** iletişim kutusunda, klasör yolunu doğrulayın ve ardından yedekleme dosyası için bir ad yazın. 

4. **Veritabanını yedekle** iletişim kutusunda Seçenekler ' i seçin.

    **Dosya üzerine yazmaya Izin ver** -aynı ada sahip yedekleme dosyalarının üzerine yazmak için bu seçeneği belirleyin. Bu seçenek seçilmezse, kaydettiğiniz dosya aynı konumda zaten bulunan bir dosyayla aynı ada sahip olamaz.

    **Sıkıştırmayı Uygula** -yedekleme dosyasını sıkıştırmak için bu seçeneği belirleyin. Sıkıştırılmış yedekleme dosyaları disk alanını kaydeder, ancak biraz daha yüksek CPU kullanımı gerektirir. 

    **Yedekleme dosyasını şifreleyin** -yedekleme dosyasını şifrelemek için bu seçeneği belirleyin. Bu seçenek, yedekleme dosyasının güvenliğini sağlamak için Kullanıcı tarafından sağlanan bir parola gerektirir. Parola, yedek verilerin okunmasını engeller ve geri yükleme işleminden farklı olur. Yedeklemeleri şifrelemeyi seçerseniz, parolayı güvenli bir yerde depolayın.

5. Yedekleme dosyasını oluşturmak ve kaydetmek için **Tamam** ' ı tıklatın.


### <a name="powershell"></a>PowerShell
[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase) cmdlet 'ini kullanın.

## <a name="restore"></a>Geri Yükleme
Geri yükleme sırasında, yedekleme dosyanız sunucunuz için yapılandırdığınız depolama hesabında olmalıdır. Bir yedekleme dosyasını şirket içi bir konumdan depolama hesabınıza taşımanız gerekiyorsa [Microsoft Azure Depolama Gezgini](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) veya [AzCopy](../storage/common/storage-use-azcopy.md) komut satırı yardımcı programını kullanın. 



> [!NOTE]
> Şirket içi bir sunucudan geri yükleme yapıyorsanız, tüm etki alanı kullanıcılarını modelin rolünden kaldırmalı ve onları Azure Active Directory Kullanıcı olarak rollere geri eklemeniz gerekir.
> 
> 

### <a name="to-restore-by-using-ssms"></a>SSMS kullanarak geri yüklemek için

1. SSMS 'de **Restore**> bir veritabanına sağ tıklayın.

2. **Yedekleme veritabanı** iletişim kutusunda, **yedekleme dosyası**' nda, **Araştır**' a tıklayın.

3. **Veritabanı dosyalarını bul** iletişim kutusunda, geri yüklemek istediğiniz dosyayı seçin.

4. **Veritabanını geri yükle**bölümünde veritabanını seçin.

5. Seçenekleri belirtin. Güvenlik seçeneklerinin yedeklenirken kullandığınız yedekleme seçenekleriyle eşleşmesi gerekir.


### <a name="powershell"></a>PowerShell

[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase) cmdlet 'ini kullanın.


## <a name="related-information"></a>İlgili bilgiler

[Azure depolama hesapları](../storage/common/storage-create-storage-account.md)  
[Yüksek düzey kullanılabilirlik](analysis-services-bcdr.md)     
[Azure Analysis Services Yönet](analysis-services-manage.md)
