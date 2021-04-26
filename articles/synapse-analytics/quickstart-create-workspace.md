---
title: 'Hızlı başlangıç: SYNAPSE çalışma alanı oluşturma'
description: Bu kılavuzdaki adımları izleyerek bir Synapse çalışma alanı oluşturun.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: d38f1f294f60b73e8f1e69169a75333eb175c9f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600165"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Hızlı başlangıç: SYNAPSE çalışma alanı oluşturma
Bu hızlı başlangıçta Azure portal kullanarak bir Azure SYNAPSE çalışma alanı oluşturma adımları açıklanmaktadır.

## <a name="create-a-synapse-workspace"></a>Synapse çalışma alanı oluşturma

1. [Azure Portal](https://portal.azure.com)açın ve en üstteki **SYNAPSE** için arama yapın.
1. Arama sonuçlarında **Hizmetler**' ın altında **Azure SYNAPSE Analytics**' i seçin.
1. Çalışma alanı oluşturmak için **Ekle** ' yi seçin.
1. **Temel bilgiler** sekmesinde, çalışma alanına benzersiz bir ad verin. Bu belgede **Kapsamım çalışma alanını** kullanacağız
1. Bir çalışma alanı oluşturmak için bir ADLSGEN2 hesabınızın olması gerekir. En basit seçenek yeni bir tane oluşturmaktır. Mevcut bir işlemi yeniden kullanmak istiyorsanız, bazı ek yapılandırmalar gerçekleştirmeniz gerekir. 
1. 1. seçenek yeni bir ADLSGEN2 hesabı oluşturma 
    1. **Data Lake Storage Gen 2**' yi seçin altında **Yeni oluştur** ' a tıklayın ve **contosolake** olarak adlandırın.
    1. **Data Lake Storage Gen 2**' yi seçin altında **dosya sistemi** ' ne tıklayın ve ardından **kullanıcıları** adlandırın.
1. 2. seçenek bu belgenin en altındaki **depolama hesabı hazırlama** yönergelerine bakın.
1. Azure SYNAPSE çalışma alanınız, bu depolama hesabını "birincil" depolama hesabı ve çalışma alanı verilerini depolamak için kapsayıcı olarak kullanacaktır. Çalışma alanı, verileri Apache Spark tablolarında depolar. Spark uygulama günlüklerini **/SYNAPSE/WorkspaceName** adlı bir klasörde depolar.
1. **Gözden geçir ve oluştur** > **Oluştur**'u seçin. Çalışma alanınız birkaç dakika içinde hazırlanıyor.

> [!NOTE]
> Azure SYNAPSE çalışma alanınızı oluşturduktan sonra, çalışma alanını başka bir Azure Active Directory kiracıya taşıyameyeceksiniz. Abonelik geçişiyle veya başka eylemlerle bunu yaparsanız, çalışma alanının içindeki yapıtlara erişimi kaybedebilirsiniz.
> Ayrıca, şu anda bir [bulut çözümü sağlayıcısı (CSP)](/partner-center/csp-overview) aboneliğinde bir Synapse Analytics çalışma alanı oluşturamazsınız.

## <a name="open-synapse-studio"></a>SYNAPSE Studio 'Yu açın

Azure SYNAPSE çalışma alanınız oluşturulduktan sonra, SYNAPSE Studio 'Yu açmak için iki yol vardır:

* [Azure Portal](https://portal.azure.com)SYNAPSE çalışma alanınızı açın. **Genel bakış** bölümünün üst kısmında, **SYNAPSE Studio 'yu Başlat**' ı seçin.
* Adresine gidin `https://web.azuresynapse.net` ve çalışma alanınızda oturum açın.

## <a name="prepare-an-existing-storage-account-for-use-with-azure-synapse-analytics"></a>Mevcut bir depolama hesabını Azure SYNAPSE Analytics ile kullanmak üzere hazırlama

1. [Azure portalını](https://portal.azure.com) açın.
1. Mevcut bir ADLSGEN2 Storage hesabına git
1. Sol bölmede **erişim denetimi (IAM)** seçeneğini belirleyin. Ardından, aşağıdaki rolleri atayın veya zaten atanmış olduklarından emin olun:
    * Kendinizi **sahip** rolüne atayın.
    * Kendinizi **Depolama Blobu veri sahibi** rolüne atayın.
1. Sol bölmede **kapsayıcılar** ' ı seçin ve bir kapsayıcı oluşturun.
1. Kapsayıcıya bir ad verebilirsiniz. Bu belgede kapsayıcı **kullanıcılarını** adı vereceğiz.
1. Varsayılan ayar olan **genel erişim düzeyini** kabul edin ve **Oluştur**' u seçin.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>Çalışma alanınızdan depolama hesabına erişimi yapılandırma

Azure SYNAPSE çalışma alanınız için Yönetilen kimlikler zaten depolama hesabına erişebilmiş olabilir. Aşağıdakileri sağlamak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com) ve çalışma alanınız için seçilen birincil depolama hesabını açın.
1. Sol bölmeden **erişim denetimi (IAM)** seçeneğini belirleyin.
1. Aşağıdaki rolleri atayın veya zaten atandıklarından emin olun. Çalışma alanı kimliği ve çalışma alanı adı için aynı adı kullanıyoruz.
    * Depolama hesabındaki **Depolama Blobu veri katılımcısı** rolü için çalışma alanı kimliği olarak **MyWorkspace** ' i atayın.
    * Çalışma alanı adı olarak **MyWorkspace** atayın.

1. **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* [Adanmış bir SQL havuzu oluşturma](quickstart-create-sql-pool-studio.md) 
* [Sunucusuz Apache Spark havuzu oluşturma](quickstart-create-apache-spark-pool-portal.md)
* [Sunucusuz SQL havuzu kullanma](quickstart-sql-on-demand.md)