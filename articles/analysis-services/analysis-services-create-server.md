---
title: Hızlı başlangıç-Azure portal Analysis Services sunucu oluşturma | Microsoft Docs
description: Bu hızlı başlangıçta, Azure portal kullanılarak Azure Analysis Services sunucu örneğinin nasıl oluşturulacağı açıklanmaktadır.
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom:
- mode-portal
ms.openlocfilehash: 78e4e008a4c4dfcee1e4f0a11b519e0a2bbf5f56
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535079"
---
# <a name="quickstart-create-a-server---portal"></a>Hızlı başlangıç: Sunucu oluşturma - Portal

Bu hızlı başlangıçta, portalı kullanarak Azure aboneliğinizde bir Analysis Services sunucusu kaynağı oluşturma adımları açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar 

* **Azure aboneliği**: hesap oluşturmak Için [Azure Ücretsiz deneme sürümünü](https://azure.microsoft.com/offers/ms-azr-0044p/) ziyaret edin.
* **Azure Active Directory**: Aboneliğinizin bir Azure Active Directory Kiracısı ile ilişkilendirilmiş olması gerekir. Ayrıca bu Azure Active Directory içindeki bir hesapla Azure'da oturum açmış olmanız gerekir. Daha fazla bilgi için bkz. [Kimlik doğrulaması ve izinler](analysis-services-manage-users.md).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın 

[Portalda oturum açın](https://portal.azure.com)


## <a name="create-a-server"></a>Sunucu oluşturma

1. **+ Kaynak**  >  **Analizi**  >  **Analysis Services** oluştur ' a tıklayın.

    ![Portal](./media/analysis-services-create-server/aas-create-server-portal.png)

2. **Analysis Services** sayfasında gerekli alanları doldurduktan sonra **Oluştur**'a basın.
   
   * **Sunucu adı**: Sunucuya başvurmak için kullanılacak benzersiz bir ad yazın. Sunucu adı küçük harfli bir karakterle başlamalı ve 3 ila 128 küçük harf ve sayı içermelidir. Boşluk ve özel karakterlere izin verilmez.
   * **Abonelik**: Bu sunucuyla ilişkilendirilecek aboneliği seçin.
   * **Kaynak grubu**: Yeni bir kaynak grubu oluşturun veya zaten var olan bir kaynak grubunu seçin. Kaynak grupları, bir Azure kaynağı koleksiyonunu yönetmenize yardımcı olmak üzere tasarlanmıştır. Daha fazla bilgi edinmek için bkz. [kaynak grupları](../azure-resource-manager/management/overview.md).
   * **Konum**: Sunucuyu barındıran Azure veri merkezi konumudur. En büyük kullanıcı tabanınıza en yakın konumu seçin.
   * **Fiyatlandırma katmanı**: Bir fiyatlandırma katmanı seçin. Test yapıyorsanız ve örnek model veritabanını yükleyecekseniz ücretsiz **D1** katmanını seçin. Daha fazla bilgi için bkz. [Azure Analysis Services fiyatlandırması](https://azure.microsoft.com/pricing/details/analysis-services/). 
   * **Yönetici**: Varsayılan olarak oturum açmış olduğunuz hesap olacaktır. Azure Active Directory'den farklı bir hesap seçebilirsiniz.
   * **Yedekleme Depolama Alanı**: İsteğe bağlıdır. [Depolama hesabınız](../storage/common/storage-introduction.md) varsa model yedekleme veritabanı olarak varsayılan yapabilirsiniz. [Yedekleme ve geri yükleme](analysis-services-backup.md) ayarlarını daha sonra da yapabilirsiniz.
   * **Depo anahtarı süre sonu**: İsteğe bağlıdır. Depo anahtarı için süre sonu belirtin.

Sunucunun oluşturulması genellikle bir dakikadan kısa sürer. **Portala Ekle**'yi seçtiyseniz yeni sunucunuzu görmek için portalınıza gidin. Ya da,   >  sunucunuzun hazırlanın olup olmadığını görmek için tüm hizmetler **Analysis Services** gidin. Sunucular, 1200 ve daha yüksek uyumluluk düzeylerinde tablo modellerini destekler. Model uyumluluk düzeyi, Visual Studio veya SSMS 'de belirtilmiştir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

İhtiyacınız kalmadıysa sunucunuzu silebilirsiniz. Sunucunuzun **Genel Bakış** sayfasında **Sil**'e tıklayın. 

 ![Temizleme](./media/analysis-services-create-server/aas-create-server-cleanup.png)


## <a name="next-steps"></a>Sonraki adımlar
Bu hızlı başlangıçta Azure aboneliğinizde sunucu oluşturmayı öğrendiniz. Sunucuyu oluşturduğunuza göre bir sunucu güvenlik duvarı yapılandırarak (isteğe bağlı) güvenliğinin sağlanmasına yardımcı olacaksınız. Sunucunuza doğrudan portaldan örnek bir veri modeli de ekleyebilirsiniz. Örnek modelin olması, model veritabanı rollerini yapılandırma ve istemci bağlantılarını test etme işlemlerini öğrenme konusunda yardımcı olur. Daha fazla bilgi edinmek için, örnek model ekleme öğreticisiyle devam edin.

> [!div class="nextstepaction"]
> [Hızlı başlangıç: Sunucu güvenlik duvarını yapılandırma - Portal](analysis-services-qs-firewall.md)   
