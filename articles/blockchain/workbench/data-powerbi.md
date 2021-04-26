---
title: Azure Blockchain Workbench verilerini Microsoft Power BI’da kullanma
description: Azure Blockchain Workbench SQL DB verilerini Microsoft Power BI’da yüklemeyi ve görüntülemeyi öğrenin.
ms.date: 04/22/2020
ms.topic: how-to
ms.reviewer: sunri
ms.openlocfilehash: 7e0e585ce45616c2402972c725b502f4b704d1cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000156"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Azure Blockchain Workbench verilerini Microsoft Power BI ile kullanma

Microsoft Power BI, Power BI Desktop kullanarak SQL DB veritabanlarından kolayca güçlü raporlar oluşturma ve bunları ' de yayımlama olanağı sağlar [https://www.powerbi.com](https://www.powerbi.com) .

Bu makale, Azure Blockchain Workbench'in SQL Veritabanı’na PowerBI Desktop’ın içinden bağlanma, bir rapor oluşturma ve raporu powerbi.com’a dağıtma sürecine ilişkin adım adım bir kılavuz içerir.

## <a name="prerequisites"></a>Önkoşullar

* [Power BI Desktop](https://powerbi.microsoft.com/desktop/)indirin.

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Azure blok zinciri çalışma ekranı 'ndaki verilere Power BI bağlama

1.  Power BI Desktop'ı açın.
2.  **Veri Al**’ı seçin.

    ![Verileri alma](./media/data-powerbi/get-data.png)
3.  Veri kaynağı türleri listesinden **SQL Server**’ı seçin.

4.  İletişim kutusunda sunucu ve veritabanı adını belirtin. Veri içeri aktarmak istediğinizi mi yoksa bir **DirectQuery** mi gerçekleştirmek istediğinizi belirtin. **Tamam**’ı seçin.

    ![SQL Server'ı seçin](./media/data-powerbi/select-sql.png)

5.  Azure Blockchain Workbench’e erişmek için veritabanı kimlik bilgilerini sağlayın. **Veritabanı**’nı seçip kimlik bilgilerinizi girin.

    Azure Blockchain Workbench dağıtım işlemi tarafından oluşturulan kimlik bilgilerini kullanıyorsanız kullanıcı adı **dbadmin**, parola ise dağıtım sırasında belirttiğiniz paroladır.

    ![SQL DB ayarları](./media/data-powerbi/db-settings.png)

6.  Veritabanına bağlandığınızda **Gezgin** iletişim kutusunda veritabanındaki tablolar ve görünümler görüntülenir. Görünümler raporlama için tasarlanmıştır ve hepsinin önünde **vw** eki olur.

    ![Power BI Masaüstü 'nün, ' nin, bir Gezgin iletişim kutusuyla, ' nin, ' nin, ' nin seçili](./media/data-powerbi/navigator.png)

7.  Dahil etmek istediğiniz görünümleri seçin. Tanıtım amacıyla, bir sözleşmede gerçekleşen eylemler hakkında ayrıntılı bilgi sağlayan bir, **Cıcontractaction** ekledik.

    ![Görünümleri seçin](./media/data-powerbi/select-views.png)

Artık Power BI ile normalde yaptığınız gibi rapor oluşturup yayımlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench’te veritabanı görünümleri](database-views.md)