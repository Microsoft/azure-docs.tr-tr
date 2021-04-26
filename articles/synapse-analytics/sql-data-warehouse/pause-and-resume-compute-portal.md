---
title: 'Hızlı başlangıç: Azure portal aracılığıyla adanmış SQL havuzunda işlemi duraklatma ve devam ettirme'
description: Maliyetleri kaydetmek için adanmış SQL havuzu için işlem duraklatmak üzere Azure portal kullanın. Veri ambarını kullanmaya hazırsanız işlem işlemine da izin verilmez.
services: synapse-analytics
author: julieMSFT
ms.author: jrasnick
manager: craigg
ms.reviewer: igorstan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: 27587f436013bcaccbacd668ce62f1d939f89e48
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566826"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>Hızlı başlangıç: Azure portal aracılığıyla adanmış SQL havuzunda işlemi duraklatma ve devam ettirme

Adanmış SQL havuzu işlem kaynaklarını duraklatmak ve devam etmek için Azure portal kullanabilirsiniz. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="before-you-begin"></a>Başlamadan önce

**Mysampledatawarehouse** adlı özel bir SQL havuzu oluşturmak için [oluşturma ve bağlanma-Portal](../quickstart-create-sql-pool-portal.md) ' i kullanın. 

## <a name="pause-compute"></a>İşlem Duraklat

Maliyetleri azaltmak için, talep üzerine işlem kaynaklarını duraklatabilir ve sürdürebilirsiniz. Örneğin, gece ve hafta sonları sırasında veritabanını kullanmıyorsanız, bu zamanlarda duraklatıp gün içinde devam edebilirsiniz.
 
>[!NOTE]
>Veritabanı duraklatıldığında işlem kaynakları için ücretlendirilmezsiniz. Ancak, depolama alanı için ücretlendirilmeye devam edersiniz. 

Adanmış bir SQL havuzunu duraklatmak için aşağıdaki adımları izleyin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. SQL havuzunu açmak için **ADANMıŞ SQL havuzu** sayfasına gidin. 
3. Uyarı **durumu** **çevrimiçi**.

    ![Çevrimiçi işlem](././media/pause-and-resume-compute-portal/compute-online.png)

4. Adanmış SQL havuzunu duraklatmak için **Duraklat** düğmesine tıklayın. 
5. Devam etmek isteyip istemediğinizi soran bir onay sorusu görüntülenir. **Evet**'e tıklayın.
6. Birkaç dakika bekleyin ve sonra **durumun** **duraklatıldığına** dikkat edin.

    ![Ekran görüntüsünde durum değeri duraklatma olan bir örnek veri ambarına yönelik Azure portal gösterilmektedir.](./media/pause-and-resume-compute-portal/pausing.png)

7. Duraklatma işlemi tamamlandığında durum **duraklatılır** ve seçenek düğmesi **devam** edilir.
8. Adanmış SQL havuzunun işlem kaynakları artık çevrimdışı. Hizmeti sürdürülene kadar işlem için ücretlendirilmezsiniz.

    ![Çevrimdışı işlem](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>İşlem işlemine geri dön

Adanmış bir SQL havuzunu sürdürmeyi sağlamak için aşağıdaki adımları izleyin.

1. SQL havuzunu açmak için **ADANMıŞ SQL havuzu** sayfasına gidin.
3. **Mysampledatawarehouse** sayfasında **durum** **duraklatıldı**' ya dikkat edin.

    ![Çevrimdışı işlem](././media/pause-and-resume-compute-portal/compute-offline.png)

1. SQL havuzunu yeniden başlatmak için, geri ' **ye tıklayın.** 
1. Başlatmak isteyip istemediğinizi soran bir onay sorusu görüntülenir. **Evet**'e tıklayın.
1. **Durumun** **sürdürüldiğine** dikkat edin.

    ![Ekran görüntüsünde, Başlangıç düğmesi seçili olan bir örnek veri ambarı için Azure portal ve devam eden durum değeri gösterilir.](./media/pause-and-resume-compute-portal/resuming.png)

1. SQL havuzu yeniden çevrimiçi olduğunda durum **çevrimiçi** olur ve seçenek düğmesi **duraklatılır**.
1. SQL havuzu için işlem kaynakları artık çevrimiçi ve hizmeti kullanabilirsiniz. İşlem ücretleri sürdürüldü.

    ![Çevrimiçi işlem](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Veri ambarı birimleri ve adanmış SQL havuzunuzdaki depolanan veriler için ücretlendiriliyorsunuz. Bu işlem ve depolama alanı kaynakları ayrı ayrı faturalandırılır. 

- Verileri depolama alanında tutmak istiyorsanız, işlem ' i duraklatın.
- Gelecekteki ücretleri kaldırmak istiyorsanız adanmış SQL havuzunu silebilirsiniz. 

Kaynakları istediğiniz gibi temizlemek için bu adımları izleyin.

1. [Azure Portal](https://portal.azure.com)oturum açın ve adanmış SQL havuzunuzu seçin.

    ![Kaynakları temizleme](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. İşlemi duraklatmak için, **Duraklat** düğmesine tıklayın. 

1. İşlem veya depolama için ücretlendirilmemek üzere adanmış SQL havuzunu kaldırmak için **Sil**' e tıklayın.



## <a name="next-steps"></a>Sonraki adımlar

Artık adanmış SQL havuzunuz için işlemi duraklatmış ve devam ettiriyorsunuz. [Özel BIR SQL havuzuna veri yükleme](./load-data-from-azure-blob-storage-using-copy.md)hakkında daha fazla bilgi edinmek için sonraki makaleye geçin. İşlem yeteneklerini yönetme hakkında daha fazla bilgi için bkz. [işlem yönetimine genel bakış](sql-data-warehouse-manage-compute-overview.md) makalesi.
