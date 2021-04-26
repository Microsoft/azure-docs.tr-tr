---
title: 'Hızlı başlangıç: Başlarken-SYNAPSE çalışma alanı oluşturma'
description: Bu öğreticide, bir Synapse çalışma alanı, adanmış bir SQL havuzu ve sunucusuz Apache Spark havuzu oluşturmayı öğreneceksiniz.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 4b7251be220c012ca51970863ac2eed55d46d711
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751157"
---
# <a name="creating-a-synapse-workspace"></a>SYNAPSE çalışma alanı oluşturma

Bu öğreticide, bir Synapse çalışma alanı, adanmış bir SQL havuzu ve sunucusuz Apache Spark havuzu oluşturmayı öğreneceksiniz. 

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticinin adımlarını tamamlayabilmeniz için, **sahip** rolü atadığınız bir kaynak grubuna erişiminizin olması gerekir. Bu kaynak grubunda SYNAPSE çalışma alanını oluşturun.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Azure portal bir Synapse çalışma alanı oluşturun

### <a name="start-the-process"></a>İşlemi Başlat
1. [Azure Portal](https://portal.azure.com)açın, arama çubuğunda, ENTER tuşuna basarak **SYNAPSE** girin.
1. Arama sonuçlarında **Hizmetler**' ın altında **Azure SYNAPSE Analytics**' i seçin.
1. Çalışma alanı oluşturmak için **Ekle** ' yi seçin.

## <a name="basics-tab--project-details"></a>Proje ayrıntıları > temel kavramlar sekmesi
Şu alanları doldurun:

1. **Abonelik** -herhangi bir abonelik seçin.
1. **Kaynak grubu** -herhangi bir kaynak grubunu kullanın.
1. **Yönetilen kaynak grubu** -bu alanı boş bırakın.

## <a name="basics-tab--workspace-details"></a>> çalışma alanı ayrıntılarının temelleri sekmesi
Şu alanları doldurun:

1. **Çalışma alanı adı** -genel olarak benzersiz bir ad seçin. Bu öğreticide, **MyWorkspace** kullanacağız.
1. **Bölge** -istemci uygulamalarınızı/hizmetlerinizi yerleştirdiğiniz bölgeyi (örneğin, Azure VM, Power BI, Azure Analysis Service) ve veri içeren depolarınızı (Örneğin Azure Data Lake depolama, Azure Cosmos DB analitik depolama) seçin.

> [!NOTE]
> İstemci uygulamaları veya depolaması ile birlikte bulunan bir çalışma alanı, birçok performans sorununa ilişkin kök neden olabilir. Veriler veya istemciler birden çok bölgeye yerleştirilirse, veri ve istemcileriniz ile farklı bölgelerde ayrı çalışma alanları oluşturabilirsiniz.

**Data Lake Storage Gen 2**' yi seçin:

1. **Hesap adına** göre, **Yeni oluştur** ' u seçin ve yeni depolama hesabının adı ' nı **contosolake** veya benzerine benzer olmalıdır.
1. **Dosya sistemi adına** göre, **Yeni oluştur** ' u seçin ve BT **kullanıcılarını** adlandırın. Bu, **Kullanıcılar** adlı bir depolama kapsayıcısı oluşturur. Çalışma alanı, bu depolama hesabını Spark tabloları ve Spark uygulama günlükleri için "birincil" depolama hesabı olarak kullanacaktır.
1. "Data Lake Storage 2. hesabında Depolama Blobu veri katılımcısı rolünü ata" kutusunu işaretleyin. 

## <a name="completing-the-process"></a>İşlem Tamamlanıyor
**Gözden geçir ve oluştur** > **Oluştur**'u seçin. Çalışma alanınız birkaç dakika içinde hazırlanıyor.

> [!NOTE]
> Mevcut ayrılmış bir SQL havuzundan (eski adıyla SQL DW) çalışma alanı özelliklerini etkinleştirmek için, [ADANMıŞ SQL havuzunuz (eski ADıYLA SQL DW) için bir çalışma alanı etkinleştirme](./sql-data-warehouse/workspace-connected-create.md)bölümüne bakın.


## <a name="open-synapse-studio"></a>SYNAPSE Studio 'Yu açın

Azure SYNAPSE çalışma alanınız oluşturulduktan sonra, SYNAPSE Studio 'Yu açmak için iki yol vardır:

* SYNAPSE çalışma alanınızı [Azure Portal](https://portal.azure.com)açın, SYNAPSE çalışma alanının **genel bakış** bölümünde SYNAPSE Studio 'yu Aç kutusunda **Aç** ' ı seçin.
* Adresine gidin `https://web.azuresynapse.net` ve çalışma alanınızda oturum açın.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Örnek verileri birincil depolama hesabına yerleştirme
Bu başlangıç kılavuzunda birçok örnek için NYX TAXI cab verilerinin küçük bir 100K satırı örnek veri kümesini kullanacağız. Çalışma alanı için oluşturduğunuz birincil depolama hesabına yerleştirilerek başlayacağız.

* Bu dosyayı bilgisayarınıza indirin: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* SYNAPSE Studio 'da veri merkezine gidin. 
* **Bağlı**' yı seçin.
* Kategori **Azure Data Lake Storage 2.** , **MyWorkspace (Primary-contosolake)** gibi bir ada sahip bir öğe görürsünüz.
* **Kullanıcılar (birincil)** adlı kapsayıcıyı seçin.
* **Karşıya yükle** ' yi seçin ve `NYCTripSmall.parquet` indirdiğiniz dosyayı seçin.

Bir Parquet dosyası karşıya yüklendi, iki eşdeğer URI üzerinden kullanılabilir:
* `https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet` 
* `abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet`

Bu öğreticide izleyen örneklerde, Kullanıcı arabirimindeki **contosolake** ' i, çalışma alanınız için seçtiğiniz birincil depolama hesabının adıyla değiştirdiğinizden emin olun.



## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Sunucusuz SQL havuzu kullanarak çözümleme](get-started-analyze-sql-on-demand.md)
