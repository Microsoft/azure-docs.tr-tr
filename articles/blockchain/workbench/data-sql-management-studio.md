---
title: SQL Server Management Studio kullanarak Azure blok zinciri çalışma ekranı verilerini sorgulama
description: SQL Server Management Studio’nun içinden Azure Blockchain Workbench'in SQL Veritabanı’na bağlanmayı öğrenin.
ms.date: 11/20/2019
ms.topic: how-to
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: d34affa93af65acf5fd7d6558e2d8077a2efc360
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625169"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Azure Blockchain Workbench verilerini SQL Server Management Studio ile kullanma

Microsoft SQL Server Management Studio Azure blok zinciri çalışma ekranının SQL DB 'ye yönelik sorguları hızlı bir şekilde yazma ve test etme olanağı sağlar. Bu bölümde, Azure blok zinciri 'nin SQL veritabanına SQL Server Management Studio içinden nasıl bağlanabilmeniz için adım adım yönergeler yer almaktadır.

## <a name="prerequisites"></a>Önkoşullar

* [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)indirin.

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>SQL Server Management Studio’yu Azure Blockchain Workbench’teki verilere bağlama

1. SQL Server Management Studio’yu açıp **Bağlan**’ı seçin.
2. **Veritabanı Altyapısı**’nı seçin.

    ![Veritabanı altyapısı](./media/data-sql-management-studio/database-engine.png)

3. **Sunucuya Bağlan** iletişim kutusuna sunucu adını ve veritabanınızın kimlik bilgilerini girin.

    Azure Blockchain Workbench dağıtım işlemi tarafından oluşturulan kimlik bilgilerini kullanıyorsanız kullanıcı adı **dbadmin**, parola ise dağıtım sırasında belirttiğiniz paroladır.

    ![SQL kimlik bilgilerini girin](./media/data-sql-management-studio/sql-creds.png)

   1. SQL Server Management Studio, Azure Blockchain Workbench veritabanındaki veritabanlarının, veritabanı görünümlerinin ve saklı yordamların listesini görüntüler.

      ![Veritabanı listesi](./media/data-sql-management-studio/db-list.png)

5. Veritabanı görünümlerinin herhangi biri ile ilişkili verileri görüntülemek için aşağıdaki adımlarla otomatik olarak bir select deyimi oluşturabilirsiniz.
6. Nesne Gezgini veritabanı görünümlerinden birine sağ tıklayın.
7. **Betiği Farklı Görüntüle** seçeneğini belirleyin.
8. **Şuna kadar SEÇ:** seçeneğini belirleyin.
9. **Yeni Sorgu Düzenleyicisi Penceresi**‘ni seçin.
10. **Yeni Sorgu** seçilerek yeni bir sorgu oluşturulabilir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench’te veritabanı görünümleri](database-views.md)
