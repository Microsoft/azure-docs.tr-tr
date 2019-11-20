---
title: Kullanım Örneği - Müşteri Profili Oluşturma
description: Oyun müşterilerinin profilini oluşturmak için veri odaklı bir iş akışı (işlem hattı) oluşturmak üzere Azure Data Factory nasıl kullanıldığını öğrenin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 866a7fdabaf51738333d8583bea5d0fa9fabf6f2
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70139847"
---
# <a name="use-case---customer-profiling"></a>Kullanım Örneği - Müşteri Profili Oluşturma
Azure Data Factory, çözüm Hızlandırıcıların Cortana Intelligence Suite uygulamak için kullanılan birçok hizmetlerden biridir.  Cortana Intelligence hakkında daha fazla bilgi için [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics)ziyaret edin. Bu belgede, Azure Data Factory ortak analiz sorunlarını nasıl çözebileceğini anlamak için basit bir kullanım durumu açıklıyoruz.

## <a name="scenario"></a>Senaryo
Contoso, birden çok platform için oyunlar oluşturan bir oyun şirketidir: oyun konsolları, yandan tutulan cihazlar ve kişisel bilgisayarlar (bilgisayarlar). Oyuncular bu oyunları oynatacak için kullanım düzenlerini, oyun stilini ve Kullanıcı tercihlerini izleyen büyük miktarda günlük verisi üretilir.  Contoso, demografi, bölgesel ve ürün verileriyle birleştirildiğinde, oyuncuların deneyimlerini geliştirme ve yükseltmeler ve oyun içi satın alma işlemleri için nasıl hedefleceği hakkında rehberlik almak için analiz yapabilir. 

Contoso 'nun hedefi, oynatıcıların oyun geçmişine bağlı olarak, satış ve çapraz satış fırsatlarını belirlemektir ve iş büyümesi için etkileyici özellikler ekleyerek müşterilere daha iyi bir deneyim sağlar. Bu kullanım örneğinde, bir iş örneği olarak bir oyun şirketi kullanıyoruz. Şirket, oyuncuları temel alarak oyunlarını iyileştirmek istiyor. Bu ilkeler, müşterilerine kendi malları ve Hizmetleri etrafında sahip olmak ve müşterilerinin deneyimini geliştirmek isteyen tüm işletmeler için geçerlidir.

Bu çözümde, contoso yakın zamanda başlatıldığı bir pazarlama kampanyasının verimliliğini değerlendirmek istiyor. Ham oyun günlükleri ile başlıyoruz, coğrafi konum verileri ile onları işleyebilir ve zenginleştirin, reklam başvuru verileriyle birleştirin ve son olarak, kampanyanın etkisini analiz etmek için bunları bir Azure SQL veritabanına kopyalayın.

## <a name="deploy-solution"></a>Çözümü dağıt
Bu basit kullanım örneğini erişmeniz ve denemeniz, bir Azure [aboneliği](https://azure.microsoft.com/pricing/free-trial/), bir [Azure Blob depolama hesabı](../../storage/common/storage-quickstart-create-account.md)ve bir [Azure SQL veritabanı](../../sql-database/sql-database-get-started.md)olmalıdır. Müşteri profili oluşturma işlem hattını, veri fabrikanızın giriş sayfasındaki **örnek ardışık düzen** kutucuğundan dağıtırsınız.

1. Bir veri fabrikası oluşturun veya var olan bir veri fabrikasını açın. Veri Fabrikası oluşturma adımları için [Data Factory kullanarak blob DEPOLAMADAN SQL veritabanına veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) konusuna bakın.
2. Data Factory için **Data Factory** dikey penceresinde **örnek işlem hatları** kutucuğuna tıklayın.

    ![Örnek işlem hatları kutucuğu](./media/data-factory-samples/SamplePipelinesTile.png)
3. Örnek işlem **hatları** dikey penceresinde, dağıtmak istediğiniz **müşteri profili oluşturma** ' ya tıklayın.

    ![Örnek işlem hatları dikey penceresi](./media/data-factory-samples/SampleTile.png)
4. Örnek için yapılandırma ayarlarını belirtin. Örneğin, Azure depolama hesabınızın adı ve anahtarınız, Azure SQL Server adı, veritabanı, Kullanıcı KIMLIĞI ve parola.

    ![Örnek dikey pencere](./media/data-factory-samples/SampleBlade.png)
5. Yapılandırma ayarlarını belirterek tamamladıktan sonra, işlem hatları tarafından kullanılan örnek işlem hatlarını ve bağlı hizmetleri/tabloları oluşturmak/dağıtmak için **Oluştur** ' a tıklayın.
6. **Örnek işlem hatları** dikey penceresinde daha önce tıklattığınız örnek kutucukta dağıtım durumunu görürsünüz.

    ![Dağıtım durumu](./media/data-factory-samples/DeploymentStatus.png)
7. Örnek için kutucukta **dağıtım başarılı** iletisini gördüğünüzde, **örnek işlem hatları** dikey penceresini kapatın.  
8. **Data Factory** dikey penceresinde bağlı hizmetler, veri kümeleri ve işlem hatları 'nın veri fabrikanıza eklendiğini görürsünüz.  

    ![Data Factory dikey penceresi](./media/data-factory-samples/DataFactoryBladeAfter.png)

## <a name="solution-overview"></a>Çözüme Genel Bakış
Bu basit kullanım örneği, verileri almak, hazırlamak, dönüştürmek, analiz etmek ve yayımlamak için Azure Data Factory nasıl kullanabileceğinizi gösteren bir örnek olarak kullanılabilir.

![Uçtan uca iş akışı](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Bu şekilde, veri ardışık düzenleri dağıtıldıktan sonra Azure portal nasıl göründüğü gösterilmektedir.

1. **Partitiongamelogspipeline** blob depolamadan ham oyun olaylarını okur ve yıl, ay ve güne göre bölümler oluşturur.
2. **Enrichgamelogspipeline** , bölümlenmiş oyun olaylarını coğrafi kod başvuru verileriyle BIRLEŞTIRIR ve IP adreslerini karşılık gelen coğrafi konumlara eşleyerek verileri zenginleştirir.
3. **Çözümleyiciler** , en gelişmiş verileri kullanır ve pazarlama kampanyası verimliliğini içeren nihai çıktıyı oluşturmak için bunu reklam verileriyle işler.

Bu örnekte, giriş verilerini kopyalamak, verileri dönüştürmek ve işlemek ve nihai verileri bir Azure SQL veritabanına çıkarmak için Data Factory kullanılır.  Ayrıca veri işlem hatları ağını görselleştirin, bunları yönetebilir ve durumlarını kullanıcı arabiriminden izleyebilirsiniz.

## <a name="benefits"></a>Avantajlar
Kullanıcı profili analizlerini en iyi duruma getirerek ve iş hedefleri ile hizalayarak, oyun şirketi kullanım modellerini hızlıca toplayabilir ve pazarlama kampanyalarının verimliliğini analiz edebilir.

