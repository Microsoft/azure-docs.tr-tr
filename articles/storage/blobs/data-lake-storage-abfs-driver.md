---
title: Azure Data Lake Storage 2. için Azure blob dosya sistemi sürücüsü
description: ABFS Hadoop dosya sistemi sürücüsü
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 370717e09e788faa56662c4c88e2e7c0de21eef7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933146"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Azure blob dosya sistemi sürücüsü (ABFS): Hadoop için adanmış bir Azure depolama sürücüsü

Azure Data Lake Storage 2. veri için birincil erişim yöntemlerinden biri [Hadoop FileSystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html)'ı aracılığıyla yapılır. Data Lake Storage 2., Azure Blob depolama erişimi kullanıcılarının yeni bir sürücüye, Azure blob dosya sistemi sürücüsüne veya `ABFS` ' a erişmesini sağlar. ABFS Apache Hadoop bir parçasıdır ve Hadoop 'un birçok ticari dağılımlarına dahil edilmiştir. Bu sürücüyü kullanarak, birçok uygulama ve çerçeve Data Lake Storage 2. açıkça başvuran hiçbir kod olmadan Azure Blob depolama alanındaki verilere erişebilir. 

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Önceki Özellik: Windows Azure Depolama Blobu sürücüsü

Windows Azure Depolama Blobu sürücüsü veya [Lıbb sürücüsü](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) , Azure Blob depolama için özgün desteği sağladı. Bu sürücü, Azure Blob depolama tarafından sunulan nesne deposu stil arabirimine dosya sistemi semantiğinin (Hadoop dosya arabirimi için gerekli olduğu gibi) eşlenmesinin karmaşık görevini gerçekleştirdi. Bu sürücü, bloblarda depolanan verilere yüksek performans erişimi sağlamak için bu modeli desteklemeye devam eder, ancak bu eşlemeyi gerçekleştiren önemli miktarda kod içerir ve devam etmek zordur. Ayrıca, dizinlere uygulandığında [FileSystem. Rename ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) ve [FileSystem. Delete ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) gibi bazı işlemler, sürücünün çok sayıda işlemi gerçekleştirmesini gerektirir (nesne, Dizin desteğinin olmamasından kaynaklanır) ve genellikle performansını düşürebilir. ABFS sürücüsü, IDB 'nin devralınan eksiklerini aşmak için tasarlandı.

## <a name="the-azure-blob-file-system-driver"></a>Azure blob dosya sistemi sürücüsü

[Azure Data Lake Storage Rest arabirimi](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) , Azure Blob depolama üzerinden dosya sistemi semantiğini destekleyecek şekilde tasarlanmıştır. Hadoop FileSystem aynı semantiğini desteklemek için de tasarlandığına göre, sürücüde karmaşık bir eşleme gereksinimi yoktur. Bu nedenle, Azure blob dosya sistemi sürücüsü (veya ABFS) REST API için bir boyutundaydı istemci Shim idir.

Ancak, sürücünün hala gerçekleştirmesi gereken bazı işlevler vardır:

### <a name="uri-scheme-to-reference-data"></a>Başvuru verilerine URI şeması

Hadoop içindeki diğer dosya sistemi uygulamalarıyla tutarlı olduğunda, ABFS sürücüsü kaynakların (dizinlerin ve dosyaların) tutarlı bir şekilde giderilmesi için kendi URI düzenini tanımlar. URI şeması [Azure Data Lake Storage 2. URI 'Sini kullanın](./data-lake-storage-introduction-abfs-uri.md). URI yapısı: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Yukarıdaki URI biçimini kullanarak, standart Hadoop araçları ve çerçeveleri bu kaynaklara başvurmak için kullanılabilir:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Dahili olarak, ABFS sürücüsü URI 'de belirtilen kaynakları dosya ve dizinlere çevirir ve bu başvurularla Azure Data Lake Storage REST API çağrıları yapar.

### <a name="authentication"></a>Kimlik Doğrulaması

ABFS sürücüsü, Hadoop uygulamasının Data Lake Storage 2. özellikli bir hesapta bulunan kaynaklara güvenli bir şekilde erişebilmesi için iki kimlik doğrulama biçimini destekler. Kullanılabilir kimlik doğrulama düzenlerinin tam ayrıntıları, [Azure Depolama Güvenliği Kılavuzu](../common/storage-security-guide.md)'nda verilmiştir. Bunlar:

- **Paylaşılan anahtar:** Bu, kullanıcıların hesaptaki tüm kaynaklara erişmesine izin verir. Anahtar şifrelenir ve Hadoop yapılandırmasında depolanır.

- **Azure Active Directory OAuth taşıyıcı belirteci:** Azure AD taşıyıcı belirteçleri, son kullanıcının kimliği veya yapılandırılmış bir hizmet sorumlusu kullanılarak sürücü tarafından alınır ve yenilenir. Bu kimlik doğrulama modelini kullanarak tüm erişim, sağlanan belirteçle ilişkilendirilen kimlik kullanılarak çağrı başına ve atanan POSIX Access Control listesi (ACL) ile değerlendirilir.

   > [!NOTE] 
   > Azure Data Lake Storage 2. yalnızca Azure AD v 1.0 uç noktalarını destekler.

### <a name="configuration"></a>Yapılandırma

ABFS sürücüsüne yönelik tüm yapılandırma <code>core-site.xml</code> yapılandırma dosyasında depolanır. [Ambarı](https://ambari.apache.org/)sağlayan Hadoop dağıtımları üzerinde, yapılandırma ayrıca Web portalı veya ambarı REST API kullanılarak yönetilebilir.

Desteklenen tüm yapılandırma girişlerinin ayrıntıları [resmi Hadoop belgelerinde](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)belirtilmiştir.

### <a name="hadoop-documentation"></a>Hadoop belgeleri

ABFS sürücüsü [resmi Hadoop belgelerinde](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) tam olarak belgelenmiştir

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Databricks kümesi oluşturma](./data-lake-storage-quickstart-create-databricks-account.md)
- [Azure Data Lake Storage 2. Nesil URI'sini kullanma](./data-lake-storage-introduction-abfs-uri.md)
