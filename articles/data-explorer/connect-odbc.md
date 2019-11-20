---
title: ODBC ile Azure Veri Gezgini bağlanma
description: Bu makalede, Azure Veri Gezgini 'e yönelik açık bir veritabanı bağlantısı (ODBC) bağlantısı ayarlamayı öğreneceksiniz.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 1b2e7a79eb932f5b971dda1d5d51b650789394db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034025"
---
# <a name="connect-to-azure-data-explorer-with-odbc"></a>ODBC ile Azure Veri Gezgini bağlanma

Açık veritabanı bağlantısı ([ODBC](/sql/odbc/reference/odbc-overview)), veritabanı erişimi için yaygın olarak kabul edilen bir uygulama programlama ARABIRIMIDIR (API). Adanmış Bağlayıcısı olmayan uygulamalardan Azure Veri Gezgini bağlanmak için ODBC kullanın.

Arka planda uygulamalar, *sürücüler*adlı veritabanına özel MODÜLLERDE uygulanan ODBC arabirimindeki işlevleri çağırır. Azure Veri Gezgini, SQL Server iletişim protokolünün ([MS-tds](/azure/kusto/api/tds/)) bir alt kümesini destekler, bu nedenle SQL Server için ODBC sürücüsünü kullanabilir.

Aşağıdaki videoyu kullanarak bir ODBC bağlantısı oluşturmayı öğrenebilirsiniz. 

> [!VIDEO https://www.youtube.com/embed/qA5wxhrOwog]

Alternatif olarak, [ODBC veri kaynağını](#configure-the-odbc-data-source) aşağıda ayrıntılı şekilde yapılandırabilirsiniz. 

Makalede, ODBC 'yi destekleyen herhangi bir uygulamadan Azure Veri Gezgini bağlanabilmeniz için SQL Server ODBC sürücüsünü nasıl kullanacağınızı öğreneceksiniz. 

## <a name="prerequisites"></a>Önkoşullar

Şunlar gerekir:

* İşletim sisteminiz için [Sürüm 17.2.0.1 veya üstünü SQL Server için Microsoft ODBC sürücüsü](/sql/connect/odbc/download-odbc-driver-for-sql-server) .

## <a name="configure-the-odbc-data-source"></a>ODBC veri kaynağını yapılandırma

SQL Server ODBC sürücüsünü kullanarak bir ODBC veri kaynağı yapılandırmak için bu adımları izleyin.

1. Windows 'ta *ODBC veri kaynakları*' nı ARAYıN ve ODBC veri kaynakları masaüstü uygulamasını açın.

1. **Add (Ekle)** seçeneğini belirleyin.

    ![Veri kaynağı ekleme](media/connect-odbc/add-data-source.png)

1. **SQL Server Için ODBC sürücüsü 17** ' yi seçin ve ardından **sona**.

    ![Sürücü seçin](media/connect-odbc/select-driver.png)

1. Bağlantı ve bağlanmak istediğiniz küme için bir ad ve açıklama girin, ardından **İleri**' yi seçin. Küme URL 'SI *\<ClusterName\>.\<Region\>. kusto.Windows.net*biçiminde olmalıdır.

    ![Sunucu Seç](media/connect-odbc/select-server.png)

1. Daha sonra **Active Directory tümleştirilmiş** **' ı seçin**.

    ![Active Directory tümleşik](media/connect-odbc/active-directory-integrated.png)

1. Örnek verilerle veritabanını seçin ve ardından **İleri**' ye tıklayın.

    ![Varsayılan veritabanını Değiştir](media/connect-odbc/change-default-database.png)

1. Sonraki ekranda, tüm seçenekleri varsayılan olarak bırakın ve **son**' u seçin.

1. **Test veri kaynağını**seçin.

    ![Test veri kaynağı](media/connect-odbc/test-data-source.png)

1. Testin başarılı olduğunu doğrulayıp **Tamam**' ı seçin. Test başarılı olmadıysa, önceki adımlarda belirttiğiniz değerleri kontrol edin ve kümeye bağlanmak için yeterli izinlere sahip olduğunuzdan emin olun.

    ![Test başarılı](media/connect-odbc/test-succeeded.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Tableau adresinden Azure Veri Gezgini bağlanma](tableau.md)