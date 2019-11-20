---
title: Azure Application test sürücüsü teklif | Azure Marketi
description: Azure Uygulama teklifi için test sürücüsünü Azure Market 'te yapılandırma.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 3855c600fe35c37ac15783995551a769e00532be
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826128"
---
# <a name="azure-applications-test-drive-tab"></a>Azure uygulamaları test sürücüsü sekmesi

Müşterilerinize yönelik bir deneme deneyimi sağlamak için test sürücüsü sekmesini kullanın.

## <a name="test-drive-benefits"></a>Sınama sürücüsü avantajları

Müşterileriniz için bir deneme sürümü oluşturma, güvenle satın aldıklarından emin olmak için en iyi uygulamadır. Kullanılabilir deneme seçenekleri, test sürücüsü yüksek kaliteli müşteri adayları oluşturma ve bu müşteri adaylarını daha fazla dönüştürme konusunda en etkili olanlardır.

Müşterilerin, gerçek hayatta bir uygulama senaryosunda, ürününüzün önemli özelliklerinin ve avantajlarına yönelik uygulamalı, kendinden destekli bir deneme sürümü sağlar.

## <a name="how-a-test-drive-works"></a>Test sürücüsü nasıl çalışacaktır?

Potansiyel bir müşteri, Market 'teki uygulamanızı arar ve keşfeder. Müşteri oturum açar ve kullanım koşullarını kabul eder. Bu noktada, müşteri, son derece uygun bir müşteri adayı elde etmek için, önceden yapılandırılmış ortamınızı sabit sayıda saat boyunca denemek üzere alır. Daha fazla bilgi için bkz. [test sürücüsü nedir?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

## <a name="setting-up-a-test-drive"></a>Test sürücüsü ayarlama

Bir sınama sürücüsünü etkinleştirmek ve yapılandırmak için aşağıdaki adımları kullanın.

### <a name="to-enable-a-test-drive"></a>Bir sınama sürücüsünü etkinleştirmek için:

1. **Yeni teklif**altında, **test sürücüsü** sekmesini seçin.
2. Test **sürücüsü altında,** **Test sürücüsünü etkinleştirmek**için **Evet** ' i seçin.

   ![Sınama sürücüsünü etkinleştir](./media/managed-app-enable-testdrive.png)

### <a name="to-configure-a-test-drive"></a>Bir sınama sürücüsü yapılandırmak için:

Bir test sürücüsünü etkinleştirdikten sonra, test sürücüsünü ayarlamak için aşağıdaki formları doldurursunuz:
  
 - Ayrıntılar
 - Teknik yapılandırma
 - Test sürücüsü dağıtım aboneliği ayrıntıları

Sonraki ekranda yakalama tüm test sürücüsü formlarını gösterir. Alan adının sonuna bir yıldız işareti (*), gerekli olduğunu gösterir. 

![Test sürücüsü yapılandırma](./media/managed-app-configure-testdrive.png)

Aşağıdaki tabloda, yönetilen uygulamanız için test sürücüsünü ayarlamak için gereken alanlar açıklanmaktadır.  Bir yıldız işaretiyle eklenen alanlar gereklidir.

|      Alan         |  Açıklama      |
|  ---------------   |  ---------------  |
| **Açıklama\***  |  Test sürücünüzde nelerin yapılabileceğini açıkla. Bu açıklamayı biçimlendirmek için temel HTML etiketlerini kullanabilirsiniz. Örneğin, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;ve başlıkları.                |
| **Kullanıcı El Ile\***  |  Müşterilerinizin sınama sürücüsü deneyimine yol göstermek için kullanabileceği bir kullanıcı el kitabı yükleyin. Bu belge bir. PDF dosyası olmalıdır.    |
| **Test sürücüsü tanıtım videosu** |  Test sürücünüze yönelik isteğe bağlı bir video kılavuzu. Bir müşteri, sınama sürücüsü almadan önce bu videoyu izleyebilir. YouTube veya Vimeo 'da videonun URL 'sini sağlayın. **+ Video ekle**' yi seçerseniz, aşağıdaki bilgileri sağlamanız istenir:<ul><li>Ad</li><li>URL'si</li><li>Küçük resim (PNG biçiminde, 533 x 324 piksel)</li></ul>  |
| **Örnekler\***      | Kaç tane örnek istediğinizi, hangi bölgelere ve müşterilerinizin sınama sürücüsünü ne kadar hızlı bir şekilde kullandığını yapılandırın. Daha fazla bilgi için bkz. [test sürücüsü yayımlama](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive#how-to-publish-a-test-drive).           |
| **Sınama sürücüsü süresi (saat)\*** | Saat sayısı için bir tamsayı girin. İzin verilen Aralık 1 ile 999 arasındadır. |
| **Sürücü ARM şablonunu test\***     | Uygulamanız için Azure Resource Manager şablonlarına sahip bir sıkıştırılmış (. zip) dosyayı karşıya yükleyin. Daha fazla bilgi için bkz. [Azure Resource Manager test sürücüsü](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive). |
| **Erişim bilgileri\***          | Müşteriniz test sürücüsünü aldıktan sonra erişim bilgilerini sağlayın. Örneğin, test sürücüsüne erişmek için bir URL ve bilgileri imzalayın. . Bu açıklamayı biçimlendirmek için temel HTML etiketlerini kullanabilirsiniz. Örneğin, &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt;ve başlıkları. |
| **Azure abonelik kimliği\***       | Bu, Azure hizmetlerine ve Azure portal erişim izni verir. Abonelik, kaynak kullanımının rapor edildiği ve hizmetlerin faturalandırılan yerdir. Yalnızca test sürücüleri için ayrı bir Azure aboneliğiniz yoksa, bir abonelik oluşturun.  |
| **Azure AD Kiracı kimliği\***          | Azure Active Directory için mevcut bir kiracı sağlayın veya bu test sürücüsü için bir kiracı oluşturun.  |
| **Azure AD Uygulaması Kimliği\***             | Yeni bir uygulama oluşturun ve kaydedin. Microsoft bu uygulamayı test sürücü örneğiniz üzerinde işlem gerçekleştirmek için kullanır.  |
| **Azure AD Uygulaması anahtar\***            | Uygulama için bir kimlik doğrulama anahtarı oluşturun ve bu alana yapıştırın.   |
|  |  |

Gerekli tüm bilgileri sağlamadıktan sonra, test sürücüsünü ayarlamayı sona **kazanmak Için kaydet** ' i seçin.


## <a name="next-steps"></a>Sonraki adımlar

[Market sekmesi](./cpp-marketplace-tab.md)
