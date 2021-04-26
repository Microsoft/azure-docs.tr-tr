---
title: SQL veritabanı DACPAC ve BACPAC paketlerini kullanma-Azure SQL Edge
description: Azure SQL Edge 'de DACPACs ve BacPacs kullanma hakkında bilgi edinin
keywords: SQL Edge, SqlPackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 40bd0eda16f9f96dd356eef900369ab25854e9f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392257"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL Edge 'de SQL veritabanı DACPAC ve BACPAC paketleri

Uç Cihazlar için Azure SQL, IoT ve uç dağıtımları için ayarlanan, iyileştirilmiş bir ilişkisel veritabanı altyapısıdır. Bu, Microsoft SQL veritabanı altyapısının, sektör lideri performans, güvenlik ve sorgu işleme özellikleri sağlayan en son sürümlerine kurulmuştur. Azure SQL Edge, SQL Server sektör lideri ilişkisel veritabanı yönetim özelliklerinin yanı sıra gerçek zamanlı analiz ve karmaşık olay işleme için yerleşik akış özelliği sağlar.

Azure SQL Edge, bir [SQL veritabanı DACPAC ve bacpac](/sql/relational-databases/data-tier-applications/data-tier-applications) paketini SQL Edge 'i dağıttıktan sonra dağıtmanıza imkan tanıyan yerel bir mekanizma sağlar.

SQL veritabanı dacpac ve bacpac paketleri, ortam değişkeni kullanılarak SQL Edge 'e dağıtılabilir `MSSQL_PACKAGE` . Ortam değişkeni aşağıdakilerden biriyle yapılandırılabilir.  
- Dacpac ve bacpac dosyalarını içeren SQL kapsayıcısı içindeki yerel bir klasör konumu. Bu klasör, bağlama noktaları veya veri birimi kapsayıcıları kullanılarak bir konak hacimleriyle eşleştirilebilir. 
- SQL kapsayıcısı içindeki bir yerel dosya yolu, dacpac veya bacpac dosyası eşlemesi. Bu dosya yolu, bağlama noktaları veya veri birimi kapsayıcıları kullanılarak bir konak hacimleriyle eşleştirilebilir. 
- SQL kapsayıcısı içindeki yerel bir dosya yolu, dacpac veya bacpac dosyalarını içeren bir zip dosyası ile eşleniyor. Bu dosya yolu, bağlama noktaları veya veri birimi kapsayıcıları kullanılarak bir konak hacimleriyle eşleştirilebilir. 
- Dacpac ve bacpac dosyalarını içeren bir ZIP dosyasının Azure Blob SAS URL 'SI.
- Bir dacpac veya bacpac dosyasına bir Azure Blob SAS URL 'SI. 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>SQL Edge ile SQL veritabanı DAC paketi kullanma

`(*.dacpac)` `(*.bacpac)` Azure Blob depolama ve bir zip dosyası kullanarak bir SQL veritabanı DAC PAKETINI veya bacpac dosyasını dağıtmak (veya içeri aktarmak) için aşağıdaki adımları izleyin. 

1. Bir DAC paketi oluşturun/ayıklayın veya aşağıda bahsedilen mekanizmayı kullanarak bacpac dosyasını dışarı aktarın. 
    - Bir SQL veritabanı DAC paketi oluşturun veya ayıklayın. Mevcut bir SQL Server veritabanı için DAC paketi oluşturma hakkında bilgi için bkz. [bir VERITABANıNDAN dac ayıklama](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) .
    - Dağıtılan bir DAC paketini veya veritabanını dışarı aktarma. Mevcut bir SQL Server veritabanı için bacpac dosyası oluşturma hakkında bilgi için bkz. [veri katmanı uygulamasını dışarı aktarma](/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) .

2. `*.dacpac`Veya `*.bacpac` dosyasını zip olarak bir Azure Blob depolama hesabına yükleyin. Azure Blob depolamaya dosya yükleme hakkında daha fazla bilgi için bkz. [Azure Portal blob 'Ları yükleme, indirme ve listeleme](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Azure portal kullanarak ZIP dosyası için paylaşılan erişim imzası oluşturun. Daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) ile erişim temsilcisi](../storage/common/storage-sas-overview.md).

4. SQL Edge modülü yapılandırmasını, DAC paketinin paylaşılan erişim URI 'sini içerecek şekilde güncelleştirin. SQL Edge modülünü güncelleştirmek için şu adımları uygulayın:

    1. Azure portal IoT Hub dağıtımınıza gidin.

    2. Sol bölmede **IoT Edge**' yi seçin.

    3. **IoT Edge** SAYFASıNDA, SQL Edge modülünün dağıtıldığı IoT Edge bulun ve seçin.

    4. Cihaz cihazı **IoT Edge** sayfasında, **modülü ayarla**' yı seçin.

    5. **Modülleri ayarla** sayfasında, Azure SQL Edge modülüne tıklayın.

    6. **IoT Edge modülü Güncelleştir** bölmesinde **ortam değişkenleri**' ni seçin. Ortam değişkenini ekleyin `MSSQL_PACKAGE` ve yukarıdaki 3. adımda oluşturulan SAS URL 'sini ortam değişkeninin değeri olarak belirtin. 

    7. **Güncelleştir**’i seçin.

    8. **Modülleri ayarla** sayfasında, **gözden geçir + oluştur**' u seçin.

    9. **Modülleri ayarla** sayfasında **Oluştur**' u seçin.

5. Modül güncelleştirildikten sonra, paket dosyaları indirilir, sıkıştırıldı ve SQL Edge örneğine göre dağıtılır.

Azure SQL Edge kapsayıcısının her yeniden başlatıldığında, SQL Edge daraltılmış dosya paketini indirmeye çalışır ve değişiklikleri değerlendirebilir. Dacpac dosyasının yeni bir sürümüne karşılaşılırsa, değişiklikler veritabanına SQL Edge 'de dağıtılır.

## <a name="known-issue"></a>Bilinen sorun

Bazı DACPAC veya BACPAC dağıtımları sırasında, kullanıcılar, dacpac dağıtım işleminin başarısız olmasıyla sonuçlanan bir komut zaman aşımları ile karşılaşabilir. Bu sorunla karşılaşırsanız, lütfen SQLPackage.exe (veya SQL Istemci araçları) kullanarak DACPAC veya BACPAC gerekirse uygulayın. 

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Portal ÜZERINDEN SQL Edge dağıtın](deploy-portal.md).
- [Akış verileri](stream-data.md)
- [SQL Edge 'de ONNX ile makine öğrenimi ve AI](onnx-overview.md)