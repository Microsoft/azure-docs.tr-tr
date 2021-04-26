---
title: Öğretici-Power BI Desktop ile Azure Analysis Services bağlama | Microsoft Docs
author: minewiskan
description: Bu öğreticide, Azure portal Analysis Services sunucu adını nasıl alabileceğinizi ve sonra Power BI Desktop kullanarak sunucuya nasıl bağlanacağınızı öğrenin.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 46805427e6d00e858cf869eef68bf68549f57fb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92018126"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Öğretici: Power BI Desktop ile bağlanma

Bu öğreticide, Power BI Desktop'u kullanarak sunucunuzdaki örnek adventureworks model veritabanına bağlanacaksınız. Tamamladığınız görevler, modele normal bir kullanıcı bağlantısının benzetimini yapar ve model verilerinden bir temel rapor oluşturulur.

> [!div class="checklist"]
> * Portaldan sunucu adınızı alma
> * Power BI Desktop kullanarak bağlanma
> * Temel bir rapor oluşturma

## <a name="prerequisites"></a>Önkoşullar

- Sunucunuza [örnek adventureworks model veritabanını ekleyin](../analysis-services-create-sample-model.md).
- Örnek adventureworks model veritabanı üzerinde [*okuma*](../analysis-services-server-admins.md) izinlerine sahip olun.
- [En yeni Power BI Desktop'u yükleyin](https://powerbi.microsoft.com/desktop).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın
Bu öğreticide, yalnızca sunucu adını almak için portalda oturum alacaksınız. Normalde, kullanıcılar sunucu adını sunucu yöneticisinden alabilir.

[Portalda](https://portal.azure.com/)oturum açın.

## <a name="get-server-name"></a>Sunucu adını alma
Power BI Desktop'tan sunucunuza bağlanmak için önce sunucu adını bilmelisiniz. Sunucu adını portaldan alabilirsiniz.

**Azure portalı** > sunucu > **Genel Bakış** > **Sunucu adı** menüsünde sunucu adını kopyalayın.
   
   ![Azure'da sunucu adını alma](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop'ta bağlanma

1. Power BI Desktop, **veri al**  >  **Azure**  >  **Azure Analysis Services veritabanı**' na tıklayın.

   ![Veri Al penceresinde Bağlan düğmesi](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. **Sunucu**'ya sunucu adını yapıştırın, sonra **Veritabanı**'na **adventureworks** girin ve **Tamam**'a tıklayın.

   ![Sunucu adını ve model veritabanını belirtme](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. İstendiğinde kimlik bilgilerinizi girin. Girdiğiniz hesabın örnek adventureworks model veritabanı üzerinde en azından okuma izinleri olmalıdır.

    Adventureworks modeli Power BI Desktop'ta Rapor görünümünde boş bir raporla açılır. **Alanlar** listesinde gizli olmayan tüm model nesneleri görüntülenir. Bağlantı durumu sağ alt köşede gösterilir.

4. **GÖRSEL ÖĞELER**'de **Kümelenmiş Çubuk Grafik**'i seçin, sonra **Biçim**'e (rulo fırça simgesi) tıklayın ve **Veri etiketleri**'ni açın. 

   ![Görsel öğeler](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. **Alanlar**  >  **Internet Sales** tablosunda, **Internet satışları toplamı** ve **kenar boşluğu** ölçüleri ' ni seçin. **Ürün Kategorisi** tablosunda **Ürün Kategorisi Adı**'nı seçin.

   ![Raporun tamamı](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Birkaç dakika ayırıp farklı görsel öğeler oluşturarak, verileri ve ölçümleri bölerek örnek adventureworks modelini keşfedin. Raporunuzdan memnun kaldığınızda kaydetmeyi unutmayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık ihtiyacınız yoksa raporunuzu kaydetmeyin veya kaydettiyseniz dosyayı silin.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Power BI Desktop kullanarak sunucudaki bir veri modeline bağlanmayı ve temel bir rapor oluşturmayı öğrenin. Veri modeli oluşturma konusunda bilgi sahibi değilseniz, SQL Server Analysis Services belgelerine [Adventure Works Internet satışları tablo verileri modelleme öğreticisine](/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) bakın.