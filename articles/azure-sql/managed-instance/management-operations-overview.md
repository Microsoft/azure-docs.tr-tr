---
title: Yönetim işlemlerine genel bakış
titleSuffix: Azure SQL Managed Instance
description: Azure SQL yönetilen örnek yönetimi işlemleri süresi ve en iyi uygulamalar hakkında bilgi edinin.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: bd66c10bb1d6316bbe90e7ba4092d79c6a43a75d
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285303"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Azure SQL Yönetilen Örneği yönetim işlemlerine genel bakış
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL yönetilen örneği, yeni yönetilen örnekleri otomatik olarak dağıtmak, örnek özelliklerini güncelleştirmek ve artık gerekli olmadığında örnekleri silmek için kullanabileceğiniz yönetim işlemleri sağlar.

## <a name="what-are-management-operations"></a>Yönetim işlemleri nelerdir?

Tüm yönetim işlemleri aşağıdaki gibi kategorilere ayrılabilir:

- Örnek dağıtımı (yeni örnek oluşturma)
- Örnek Güncelleştirmesi (sanal çekirdekler veya ayrılmış depolama gibi örnek özelliklerini değiştirme)
- Örnek silme

[Azure sanal ağları içindeki dağıtımları](../../virtual-network/virtual-network-for-azure-services.md) desteklemek ve müşterilere yalıtım ve güvenlik sağlamak IÇIN, SQL yönetilen örneği [sanal kümelere](connectivity-architecture-overview.md#high-level-connectivity-architecture)bağımlıdır. Sanal küme, müşterinin sanal ağ alt ağı içinde dağıtılan ayrılmış bir yalıtılmış sanal makine kümesini temsil eder. Temelde, boş bir alt ağa dağıtılan her yönetilen örnek yeni bir sanal küme oluşturulabilir.

Yönetilen örneklerde sonraki yönetim işlemleri temeldeki sanal kümeyi etkileyebilir. Ek sanal makinelerin dağıtımı, Yeni dağıtımlar veya mevcut yönetilen örneklere güncelleştirmeler planlarken göz önünde bulundurmanız gereken bir ek yük ile birlikte, temel sanal kümeyi etkileyen değişiklikler yönetim işlemlerinin süresini etkileyebilir.


## <a name="duration"></a>Süre

Sanal kümedeki işlemlerin süresi değişebilir, ancak genellikle en uzun süreye sahip olabilir. 

Aşağıda, mevcut hizmet telemetri verilerine göre genellikle tahmin edebileceğiniz değerler verilmiştir:

- **Sanal küme oluşturma**: oluşturma, örnek yönetimi işlemlerinde zaman uyumlu bir adımdır. <br/> **işlemlerin %90 ' i 4 saat içinde tamamlanır**.
- **Sanal küme yeniden boyutlandırma (genişleme veya daraltma)**: genişletme zaman uyumsuz olarak (örnek yönetim işlemleri süresince etkilenmeden) zaman uyumsuz olarak gerçekleştirilir. <br/>**küme genişletmeleri %90, en az 2,5 saat içinde tamamlanır**.
- **Sanal küme silme**: silme işlemi zaman uyumsuz bir adımdır, ancak boş bir sanal kümede [el ile de başlatılabilir](virtual-cluster-delete.md) , bu durumda eşzamanlı olarak yürütülür. <br/>**%90, sanal küme silme işleminin 1,5 saat içinde sona ermesini**.

Ayrıca, örneklerin yönetimi barındırılan veritabanlarında gerçekleştirilen işlemlerden birini de içerebilir ve bu da uzun süreler oluşmasına neden olabilir:

- **Azure depolama 'dan veritabanı dosyaları iliştirme**: genel amaçlı hizmeti katmanında ölçeklendirme Işlemi (vçekirdekler) veya depolama alanı gibi zaman uyumlu bir adım. <br/>**Bu işlemlerin %90 ' i 5 dakika içinde tamamlanır**.
- **Always on kullanılabilirlik grubu dengeli dağıtım**: iş açısından kritik hizmet katmanındaki Işlem (vçekirdekler) veya depolama Ölçeklendirmesi gibi zaman uyumlu bir adım ve hizmet katmanını Genel Amaçlı iken iş açısından kritik (veya tam tersi) olarak değiştirme. Bu işlemin süresi toplam veritabanı boyutuyla ve geçerli veritabanı etkinliğiyle orantılıdır (etkin işlem sayısı). Bir örneği güncelleştirme sırasında veritabanı etkinliği toplam süreye önemli bir varyans getirebilir. <br/>**Bu işlemlerin %90 ' i 220 GB/saat veya daha yüksek bir süre içinde yürütülür**.

Aşağıdaki tablolar, işlem kategorisine göre işlemleri ve genel toplam süreleri özetler:

**Kategori: dağıtım**

|İşlem  |Uzun süre çalışan segment  |Tahmini süre  |
|---------|---------|---------|
|Boş bir alt ağdaki ilk örnek|Sanal küme oluşturma|işlemlerin %90 ' i 4 saat içinde tamamlanır.|
|Boş olmayan bir alt ağda (örneğin, gen 4 örneklerine sahip bir alt ağdaki ilk Gen 5 örneği) başka bir donanım oluşturma örneği|Sanal küme oluşturma<sup>1</sup>|işlemlerin %90 ' i 4 saat içinde tamamlanır.|
|Boş olmayan alt ağ içinde sonraki örnek oluşturma (2., üçüncü, vb. örnek)|Sanal küme yeniden boyutlandırma|işlemin %90 ' i 2,5 saat içinde tamamlanır.|
| | | 

<sup>1</sup> sanal küme, donanım oluşturma başına oluşturulmuştur.

**Kategori: Güncelleştir**

|İşlem  |Uzun süre çalışan segment  |Tahmini süre  |
|---------|---------|---------|
|Örnek özelliği değişikliği (yönetici parolası, Azure AD oturum açma, Azure Hibrit Avantajı bayrağı)|Yok|En fazla 1 dakika.|
|Örnek depolama ölçeği artırma/azaltma (Genel Amaçlı hizmet katmanı)|Uzun süre çalışan segment<sup>1</sup>|işlem %99, 5 dakika içinde tamamlanır.|
|Örnek depolama ölçeği artırma/azaltma (İş Açısından Kritik hizmet katmanı)|-Sanal küme yeniden boyutlandırma<br>-Always on kullanılabilirlik grubu dengeli dağıtımı|işlemlerin %90 ' i, tüm veritabanlarının (220 GB/saat) temel aldığı 2,5 saat ile sona ermesini sağlar.|
|Örnek işlem (Vçekirdekler) ölçeği artırma ve azaltma (Genel Amaçlı)|-Sanal küme yeniden boyutlandırma<br>-Veritabanı dosyaları iliştirme|işlemin %90 ' i 2,5 saat içinde tamamlanır.|
|Örnek işlem (Vçekirdekler) ölçeği artırma ve azaltma (İş Açısından Kritik)|-Sanal küme yeniden boyutlandırma<br>-Always on kullanılabilirlik grubu dengeli dağıtımı|işlemlerin %90 ' i, tüm veritabanlarının (220 GB/saat) temel aldığı 2,5 saat ile sona ermesini sağlar.|
|Örnek hizmeti katmanı değişikliği (İş Açısından Kritik Genel Amaçlı ve tam tersi)|-Sanal küme yeniden boyutlandırma<br>-Always on kullanılabilirlik grubu dengeli dağıtımı|işlemlerin %90 ' i, tüm veritabanlarının (220 GB/saat) temel aldığı 2,5 saat ile sona ermesini sağlar.|
| | | 

<sup>1</sup> ölçeklendirme genel amaçlı yönetilen örnek depolama, işlem sonunda yük devretmeye neden olmayacak. Bu durumda işlem, gönderilen istek için meta verileri güncelleştirme ve yanıtı yayma işleminden oluşur.

**Kategori: Sil**

|İşlem  |Uzun süre çalışan segment  |Tahmini süre  |
|---------|---------|---------|
|Örnek silme|Tüm veritabanları için günlük kuyruğu yedeklemesi|%90 işlem en fazla 1 dakika içinde tamamlanır.<br>Note: alt ağdaki son örnek silinirse, bu işlem 12 saat sonra sanal küme silmeyi zamanlar. <sup>1</sup>|
|Sanal küme silme (Kullanıcı tarafından başlatılan işlem olarak)|Sanal küme silme|İşlem %90, en fazla 1,5 saat içinde tamamlanır.|
| | | 

geçerli yapılandırma <sup>1</sup>12 saat, ancak gelecekte değiştirilebilir. Bir sanal kümeyi daha önce silmeniz gerekiyorsa (örneğin, alt ağı serbest bırakmak için), bkz. [yönetilen bir örneği sildikten sonra bir alt ağı silme](virtual-cluster-delete.md).

## <a name="instance-availability"></a>Örnek kullanılabilirliği

Güncelleştirme **işlemleri SıRASıNDA** SQL yönetilen örneği, güncelleştirmenin sonunda gerçekleşen yük devretmenin neden olduğu kısa bir kesinti haricinde kullanılabilir. Genellikle, uzun süreli işlemler kesintiye uğratıldığında, [hızlandırılmış veritabanı kurtarması](../accelerated-database-recovery.md)sayesinde 10 saniyeye kadar sürer.

> [!NOTE]
> Yönetilen örnek depolamanın ölçeklendirilmesi Genel Amaçlı güncelleştirme sonunda yük devretmeye neden olmayacaktır.

SQL yönetilen örneği, dağıtım ve silme işlemleri sırasında istemci uygulamaları için kullanılamaz.

> [!IMPORTANT]
> Azure SQL yönetilen örneği 'nin işlem veya depolama alanını ölçeklendirmeniz veya hizmet katmanını uzun süre çalışan işlemler (veri alma, veri işleme işleri, dizin yeniden oluşturma vb.) ile aynı anda değiştirmeniz önerilmez. İşlemin sonunda veritabanının yük devretmesi devam eden tüm işlemleri iptal eder. 

## <a name="management-operations-steps"></a>Yönetim işlemleri adımları

Yönetim işlemleri birden çok adımdan oluşur. [Operations API 'si](management-operations-monitor.md) ile bu adımlar, işlem alt kümesi (dağıtım ve güncelleştirme) için sunulmuştur. Dağıtım işlemi, altı adımda güncelleştirme işlemi gerçekleştirilirken üç adımdan oluşur. İşlem süresine ilişkin ayrıntılar için [yönetim işlemleri süresi](#duration) bölümüne bakın. Adımlar, yürütme sırasıyla listelenir.

### <a name="managed-instance-deployment-steps"></a>Yönetilen örnek dağıtım adımları

|Adım adı  |Adım açıklaması  |
|----|---------|
|İstek doğrulaması |Gönderilen parametreler onaylanır. Yanlış yapılandırma durumunda hata vererek başarısız olur. |
|Sanal küme yeniden boyutlandırma/oluşturma |Alt ağın durumuna bağlı olarak, sanal küme oluşturma veya yeniden boyutlandırma durumuna geçer. |
|Yeni SQL örneği başlatma |SQL işlemi, dağıtılan sanal kümede başlatılır. |

### <a name="managed-instance-update-steps"></a>Yönetilen örnek güncelleştirme adımları

|Adım adı  |Adım açıklaması  |
|----|---------|
|İstek doğrulaması | Gönderilen parametreler onaylanır. Yanlış yapılandırma durumunda hata vererek başarısız olur. |
|Sanal küme yeniden boyutlandırma/oluşturma |Alt ağın durumuna bağlı olarak, sanal küme oluşturma veya yeniden boyutlandırma durumuna geçer. |
|Yeni SQL örneği başlatma | SQL işlemi, dağıtılan sanal kümede başlatılır. |
|Dengeli veritabanı dosyaları/veritabanı dosyalarını iliştirme |Güncelleştirme işleminin türüne bağlı olarak, veritabanı dengeli veya ekleme veritabanı dosyaları gerçekleştirilir. |
|Yük devretme ve yük devretme hazırlanıyor |Verilerin hazırlanması veya veritabanı dosyaları yeniden iliştirilmesi sırasında, sistem yük devretme için hazırlanmaktadır. Her şey ayarlandığında, yük devretme **kısa bir kapalı kalma** süresiyle gerçekleştirilir. |
|Eski SQL örneği Temizleme |Eski SQL işlemi sanal kümeden kaldırılıyor |

> [!NOTE]
> Örneklerin ölçeklendirilmesi sonucunda, temel alınan sanal küme kullanılmayan kapasiteyi serbest bırakma ve olası kapasite birleştirme işlemleri aracılığıyla, oluşturma/ölçeklendirme işlemlerine katılmayan örnekleri etkileyebilecek şekilde çalışır. 


## <a name="management-operations-cross-impact"></a>Yönetim işlemleri çapraz etki

Yönetilen bir örnekteki yönetim işlemleri, aynı sanal kümenin içine yerleştirilmiş örneklerin diğer yönetim işlemlerini etkileyebilir:

- Bir sanal kümede **uzun süre çalışan geri yükleme işlemleri** , diğer örnek oluşturma veya ölçeklendirme işlemlerini aynı alt ağa beklemeye koyar.<br/>**Örnek:** Uzun süre çalışan bir geri yükleme işlemi varsa ve aynı alt ağda bir oluşturma veya ölçeklendirme isteği varsa, geri yükleme işleminin devam etmeden önce tamamlanmasını beklediği için bu isteğin tamamlanması daha uzun sürer.

- **Sonraki örnek oluşturma veya ölçeklendirme** işlemi, daha önce başlatılmış bir örnek oluşturma veya sanal kümenin yeniden boyutlandırılmasını başlatan örnek ölçeği tarafından beklemeye konur.<br/>**Örnek:** Aynı alt ağda aynı sanal küme altında birden fazla oluşturma ve/veya ölçek isteği varsa ve bunlardan biri bir sanal küme yeniden boyutlandırmayı başlatırsa, ilk işlem isteğinden 5 + dakika sonra gönderilen tüm istekler beklenenden daha uzun sürer. Bu istekler, devam etmeden önce yeniden boyutlandırmanın tamamlanmasını beklemek zorunda kalır.

- **5 dakikalık bir pencerede gönderilen oluşturma/ölçeklendirme işlemleri** , toplu olarak oluşturulur ve paralel olarak yürütülür.<br/>**Örnek:** 5 dakikalık bir pencerede gönderilen tüm işlemler için yalnızca bir sanal küme yeniden boyutlandırma gerçekleştirilecek (ilk işlem isteğini yürütme saatinden ölçme). İlk gönderildikten sonra başka bir istek 5 dakikadan uzun bir süre gönderilirse, yürütme başlamadan önce sanal küme yeniden boyutlandırmanın tamamlanmasını bekler.

> [!IMPORTANT]
> Devam eden başka bir işlem nedeniyle bekletilen yönetim işlemleri, devam eden koşullar karşılandıktan sonra otomatik olarak sürdürülecek. Geçici olarak duraklatılan yönetim işlemlerini sürdürmeniz için Kullanıcı eylemi gerekli değildir.

## <a name="monitoring-management-operations"></a>İzleme yönetim işlemleri

Yönetim işleminin ilerlemesini ve durumunu izlemeyi öğrenmek için bkz. [izleme yönetim işlemleri](management-operations-monitor.md).

## <a name="canceling-management-operations"></a>Yönetim işlemlerini iptal etme

Yönetim işlemini iptal etmeyi öğrenmek için bkz. [Yönetim Işlemlerini Iptal etme](management-operations-cancel.md).


## <a name="next-steps"></a>Sonraki adımlar

- İlk yönetilen örneğinizi oluşturmayı öğrenmek için bkz. [Hızlı Başlangıç Kılavuzu](instance-create-quickstart.md).
- Özellikler ve karşılaştırma listesi için bkz. [ortak SQL özellikleri](../database/features-comparison.md).
- VNet yapılandırması hakkında daha fazla bilgi için bkz. [SQL yönetilen örnek VNET yapılandırması](connectivity-architecture-overview.md).
- Yönetilen bir örnek oluşturan ve bir veritabanını bir yedekleme dosyasından geri yükleyen hızlı başlangıç için bkz. [yönetilen örnek oluşturma](instance-create-quickstart.md).
- Geçiş için Azure veritabanı geçiş hizmeti 'ni kullanma hakkında bir öğretici için bkz. [veritabanı geçiş hizmeti kullanılarak SQL yönetilen örnek geçişi](../../dms/tutorial-sql-server-to-managed-instance.md).
