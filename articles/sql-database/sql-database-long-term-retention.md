---
title: Yedeklemeleri 10 yıla kadar depolayın
description: Azure SQL veritabanı 'nın tam veritabanı yedeklerini 10 yıla kadar depolamayı nasıl desteklediğini öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: 0cd4c45403d59819bf7ba729ea99de76ccf967ca
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819903"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Azure SQL veritabanı yedeklerini 10 yıla kadar depolayın

Birçok uygulamanın, veritabanı yedeklerini Azure SQL veritabanı [Otomatik yedeklemeleri](sql-database-automated-backups.md)tarafından belirtilen 7-35 günden daha fazla tutmanız gereken mevzuata, uyumluluk veya diğer iş amaçları vardır. Uzun süreli saklama (LTR) özelliğini kullanarak, belirtilen SQL veritabanı tam yedeklemelerini [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) BLOB depolama alanında 10 yıla kadar saklayabilirsiniz. Daha sonra herhangi bir yedeklemeyi yeni bir veritabanı olarak geri yükleyebilirsiniz.

> [!NOTE]
> LTR, tek ve havuza alınmış veritabanları için etkinleştirilebilir. Henüz yönetilen örneklerde örnek veritabanları için kullanılamaz. [Yalnızca kopya veritabanı yedeklerini](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) 35 gün daha fazla LTR 'e alternatif olarak ZAMANLAMAK Için SQL Agent işlerini kullanabilirsiniz.
> 

## <a name="how-sql-database-long-term-retention-works"></a>SQL veritabanı uzun süreli saklama nasıl kullanılır

Uzun süreli yedek saklama (LTR), nokta-saat geri yüklemeyi (ıNR) etkinleştirmek için [otomatik olarak oluşturulan](sql-database-automated-backups.md) tam veritabanı yedeklemelerini kullanır. Bir LTR ilkesi yapılandırılmışsa, bu yedeklemeler uzun vadeli depolama için farklı bloblara kopyalanır. Kopyalama işlemi, veritabanı iş yükü üzerinde performans etkisi olmayan bir arka plan işdir. LTR yedeklemeleri, LTR ilkesi tarafından belirlenen bir süre boyunca tutulur. Her SQL veritabanı için LTR ilkesi, LTR yedeklemelerin oluşturulma sıklığını da belirtebilir. Bu esnekliği etkinleştirmek için ilkeyi dört parametre birleşimi kullanarak tanımlayabilirsiniz: haftalık yedekleme bekletme (W), aylık yedekleme bekletme (e), yıllık yedekleme bekletme (Y) ve yılın haftası (WeekOfYear). W belirtirseniz, her hafta bir yedekleme uzun vadeli depolamaya kopyalanacaktır. D belirtirseniz, her ayın ilk haftası boyunca bir yedekleme uzun vadeli depolamaya kopyalanacaktır. Y belirtirseniz, WeekOfYear tarafından belirtilen hafta boyunca bir yedekleme uzun vadeli depolamaya kopyalanacaktır. Her yedekleme, bu parametreler tarafından belirtilen dönem için uzun vadeli depolama alanında tutulur. LTR ilkesinin herhangi bir değişikliği gelecekteki yedeklemeler için geçerlidir. Örneğin, belirtilen WeekOfYear, ilke yapılandırıldığında, ilk LTR yedeklemesi sonraki yıl oluşturulacaktır. 

LTR ilkesinin örnekleri:

-  W = 0, z = 0, Y = 5, WeekOfYear = 3

   Her yılın üçüncü tam yedeklemesi beş yıl boyunca saklanır.
   
- W = 0, Z = 3, Y = 0

   Her ayın ilk tam yedeklemesi üç ay boyunca saklanır.

- W = 12, Z = 0, Y = 0

   Her haftalık tam yedekleme, 12 hafta boyunca saklanacaktır.

- W = 6, z = 12, Y = 10, WeekOfYear = 16

   Her haftalık tam yedekleme altı hafta boyunca saklanır. 12 ay boyunca tutulacak olan her ayın ilk tam yedeklemesi hariç olur. Tam yedekleme, yılın 16. haftası üzerinde alınmış olması dışında, 10 yıl boyunca tutulacaktır. 

Aşağıdaki tabloda, şu ilke için uzun süreli yedeklemelerin temposunda ve süre sonu gösterilmektedir:

W = 12 hafta (84 gün), z = 12 ay (365 gün), Y = 10 yıl (3650 gün), WeekOfYear = 15 (15 Nisan 'dan sonra hafta)

   ![LTR örneği](./media/sql-database-long-term-retention/ltr-example.png)



Yukarıdaki ilkeyi değiştirir ve W = 0 (haftalık yedeklemeler yok) seçeneğini ayarlarsanız, yedekleme kopyalarının temposunda, yukarıdaki tabloda vurgulanan tarihlerle gösterildiği gibi değişecektir. Bu yedeklemelerin tutulması için gereken depolama miktarı uygun şekilde azaltıyordu. 

> [!IMPORTANT]
> Tek bir LTR yedeklemelerin zamanlaması Azure SQL veritabanı tarafından denetlenir. El ile bir LTR yedeklemesi oluşturamaz veya yedekleme oluşturma zamanlamasını kontrol edebilirsiniz. Bir LTR ilkesini yapılandırdıktan sonra, ilk LTR yedeklemenin kullanılabilir yedeklemeler listesinde gösterilmesi 7 güne kadar sürebilir.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Coğrafi çoğaltma ve uzun süreli yedekleme saklama

İş sürekliliği çözümünüz olarak etkin coğrafi çoğaltma veya yük devretme grupları kullanıyorsanız, son yük devretme işlemleri için hazırlık yapmanız ve coğrafi ikincil veritabanında aynı LTR ilkesini yapılandırmanız gerekir. Yedekleme, ikincgöre oluşturulmadığından, LTR depolama maliyetiniz artmaz. Yalnızca ikincili birincil hale geldiğinde yedeklemeler oluşturulacaktır. Yük devretme tetiklendiğinde ve birincil ikincil bölgeye geçirildiğinde, LTR yedeklemelerin kesintiye uğratılmamış olarak oluşturulmasını sağlar. 

> [!NOTE]
> Özgün birincil veritabanı, yük devretmeye neden olan bir kesintiden kurtarıldığında yeni bir ikincil olur. Bu nedenle, yedekleme oluşturma SÜRDÜRÜLMEYECEKTİR ve mevcut LTR ilkesi, birincil olana kadar etkili olmayacaktır. 

## <a name="configure-long-term-backup-retention"></a>Uzun süreli yedek saklama yapılandırma

Azure portal veya PowerShell 'i kullanarak uzun süreli saklama yapılandırmayı öğrenmek için bkz. [Azure SQL veritabanı uzun süreli yedekleme saklama](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Veritabanı, LTR yedeğinden geri yükle

Bir veritabanını LTR depolamadan geri yüklemek için belirli bir yedeklemeyi zaman damgasına göre seçebilirsiniz. Veritabanı, özgün veritabanıyla aynı abonelikte bulunan mevcut herhangi bir sunucuya geri yüklenebilir. Veritabanınızı bir LTR yedeklemesinden geri yüklemeyi öğrenmek için Azure portal veya PowerShell 'i kullanarak bkz. [Azure SQL veritabanı uzun süreli yedekleme saklama](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Sonraki adımlar

Veritabanı yedeklemeleri verileri yanlışlıkla bozulma veya silme işlemlerini koruduğundan, herhangi bir iş sürekliliği ve olağanüstü durum kurtarma stratejisinin önemli bir parçasıdır. Diğer SQL veritabanı iş sürekliliği çözümleri hakkında bilgi edinmek için bkz. [iş sürekliliği genel bakış](sql-database-business-continuity.md).
