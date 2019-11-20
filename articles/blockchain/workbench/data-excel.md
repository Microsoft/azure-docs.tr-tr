---
title: Azure Blockchain Workbench verilerini Microsoft Excel’de kullanma
description: Microsoft Excel 'de Azure blok zinciri çalışma ekranı önizleme SQL DB verilerini yükleme ve görüntüleme hakkında bilgi edinin.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f2a5170f95ebb1b25faea7eed74b08ae446b9499
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845091"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Azure Blockchain Workbench verilerini Microsoft Excel ile görüntüleme

Microsoft Excel’i kullanarak Azure Blockchain Workbench'in SQL veritabanındaki verileri görüntüleyebilirsiniz. Bu makalede aşağıdakiler için gerekli adımlar sağlanır:

* Microsoft Excel’den Blockchain Workbench veritabanına bağlanma
* Blockchain Workbench veritabanı tablolarına ve görünümlerine bakma
* Blockchain Workbench görünüm verilerini Excel’de yükleme

## <a name="connect-to-the-blockchain-workbench-database"></a>Blockchain Workbench veritabanına bağlanma

Bir Blockchain Workbench veritabanına bağlanmak için:

1. Microsoft Excel’i açın.
2. **Veri** sekmesinde **Veri Al**’ı seçin.
3. Sırayla **Azure’dan** ve **Azure SQL Veritabanı'ndan** seçeneğini belirleyin.

   ![Azure SQL Veritabanı'na bağlanma](./media/data-excel/connect-sql-db.png)

4. **SQL Server veritabanı** iletişim kutusunda:

    * **Sunucu** için Blockchain Workbench sunucusunun adını girin.
    * **Veritabanı (isteğe bağlı)** için veritabanının adını girin.

   ![Veritabanı sunucusunu ve veritabanını belirtin](./media/data-excel/provide-server-db.png)

5. **SQL Server veritabanı** iletişim kutusu gezinti çubuğunda **Veritabanı**’nı seçin. **Kullanıcı Adı** ve **Parola** bilgilerinizi girip **Bağlan**’ı seçin.

    > [!NOTE]
    > Azure Blockchain Workbench dağıtım işlemi sırasında oluşturulan kimlik bilgilerini kullanıyorsanız **Kullanıcı Adı** şudur: `dbadmin`. **Parola**, Blockchain Workbench’i dağıtırken oluşturduğunuz paroladır.
    
   ![Veritabanına erişmek için kimlik bilgilerini belirtin](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Veritabanı tablolarına ve görünümlerine bakın

Veritabanına bağlandığınızda Excel Gezgini iletişim kutusu açılır. Gezgin’i kullanarak veritabanındaki tablolara ve görünümlere bakabilirsiniz. Görünümler raporlama için tasarlanmıştır ve adlarının önünde **vw** eki yer alır.

   ![Bir görünümün Excel Gezgini önizlemesi](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Görünüm verilerini bir Excel çalışma kitabına yükleyin

Sonraki örnekte, bir görünümün verilerini bir Excel çalışma kitabına nasıl yükleyebileceğiniz gösterilir.

1. **Gezgin** kaydırma çubuğunda **vwContractAction** görünümünü seçin. **vwContractAction** önizlemesi, Blockchain Workbench veritabanındaki bir sözleşmeyle ilgili tüm eylemleri gösterir.
2. **Yükle**’yi seçerek görünümdeki tüm verileri alın ve Excel çalışma kitabınıza ekleyin.

   ![Bir görünümden yüklenen veriler](./media/data-excel/view-data.png)

Artık verileri yüklediğinize göre Excel özelliklerini kullanarak Azure Blockchain Workbench’ten alınan meta veriler ve işlem verileri ile kendi raporlarınızı oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench’te veritabanı görünümleri](database-views.md)