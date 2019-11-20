---
title: Azure Marketi için Bulut İş Ortağı Portalı sanal makine test sürücüsü sekmesi
description: Azure Marketi VM teklifi oluşturma bölümünde kullanılan test sürücüsü sekmesini açıklar.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 94ebbcca50916f1675ab990a4b45f3b90e069104
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808871"
---
# <a name="virtual-machine-test-drive-tab"></a>Sanal makine sınama sürücüsü sekmesi

**Yeni teklif** sayfasının **test sürücüsü** sekmesi, olası müşterilerinizi, standart bir senaryoda gösterilen, ürününüzün temel özelliklerinin ve avantajlarının uygulamalı, kendinden kılavuzlu bir tanıtımı ile sağlamanıza olanak sağlar.  Test sürücüsü, test sürücüsünü destekleyen teklif türleri için isteğe bağlı bir özelliktir.  Sınama sürücüsü, destekleyici varlıkların düzgün bir şekilde uygulandığından emin olmanızı gerektirir.  Daha fazla bilgi için [Azure Marketi sınama sürücüsü](https://azure.microsoft.com/blog/azure-marketplace-test-drive/)makalesine bakın.  <!--TD: Replace with migrated version of Test Drive article! -->

Bu özelliği etkinleştirmek için, **test sürücüsü** sekmesinde, **Test sürücüsünü etkinleştir**' de **Evet** seçeneğine tıklayın.  **Test sürücüsü** sekmesi, düzenlenecek alanları görüntüler.  Alan adı üzerinde eklenen bir yıldız işareti (*) gerekli olduğunu gösterir.

![Sanal makineler için yeni teklif formunda sürücü sına sekmesi](./media/publishvm_007.png)


## <a name="field-values"></a>Alan değerleri

Aşağıdaki tabloda bu alanların amacı ve içeriği açıklanmaktadır.  Gerekli alanlar bir yıldız işareti (*) ile tanımlanır.


|    Alan                  |       Açıklama                                                            |
|  ---------                |     ---------------                                                          |
|  *Ayrıntılar*   |  |
| **Açıklama\***           | Test sürücünüzün senaryosuna genel bir bakış sağlar. Sınama sürücüsü sağlanırken bu metin kullanıcıya gösterilir. Bu alan, biçimlendirilen içerik sağlamak istiyorsanız temel HTML 'yi destekler.  |
| **Kullanıcı El Ile\***           | Test sürücüsü kullanıcılarının çözümünüzü nasıl kullanacağınızı anlamalarına yardımcı olan ayrıntılı bir kullanıcı el ile (. PDF) yükleyin.  |
| **Test sürücüsü tanıtım videosu** | Çözümünüze servis talebi gösteren bir video yükleyin.  Bu seçeneği tercih ederseniz, video için bir ad, video URL 'SI (YouTube veya Vimeo 'da barındırılır) ve (533x324 piksel) küçük resmini sağlamanız gerekir. |
| *Teknik yapılandırma* |  |
| **Örnekler\***             | Bölge kullanılabilirliğini ve sanal makine örneğinin görece kullanılabilirliğini belirtin (daha fazla ayrıntı için bilgi simgesine tıklayın).  <br/>Potansiyel eşzamanlı test sürücüsü oturumları, aboneliğinizin kota sınırını aşmamalıdır.  Bu değer, şu şekilde hesaplanır: [Seçili bölge sayısı] x [etkin örnekler] + [Seçili bölge sayısı] x [normal örnekler] + [Seçili bölge sayısı] x [soğuk örnekleri] |
| **Test sürücü süresi\***   | Saat cinsinden maksimum oturum süresi. Test sürücüsü oturumu bu süre dolduktan sonra otomatik olarak sonlandırılır.  |
|**Sürücü ARM şablonunu test\***| Bu test sürücüsüyle ilişkili Azure Resource Manager şablonunu karşıya yükleyin. Daha fazla bilgi için bkz. [test sürücüsü Için sanal makine Dağıtım şablonunu dönüştürme](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Erişim bilgileri\***    | Azure Resource Manager, salt metin veya basit HTML olarak yazılmış erişim ve deneme oturum açma bilgileri. |
| *Test sürücüsü dağıtım aboneliği ayrıntıları* |  |
| **Azure abonelik kimliği\*** | [Microsoft Azure Portal](https://ms.portal.azure.com) oturum açarak ve sol menü çubuğundan **abonelikler** ' e tıklayarak elde edilebilir. (Örnek: "a83645ac-1234-5AB6-345-1h234g764ghty")    Bu tanımlayıcı `a83645ac-1234-5ab6-6789-1h234g764ghty`form GUID olmalıdır.|
| **Azure AD Kiracı kimliği\***    | Azure Active Directory kiracı KIMLIĞI.  [Microsoft Azure Portal](https://ms.portal.azure.com) oturum açarak ve sol menü çubuğundan **Azure Active Directory** ' a tıklayıp ortadaki menü çubuğunda **Özellikler** ' e tıklayıp sonra da **dizin kimliğini** formdan kopyalayarak elde edilebilir.  Bu tanımlayıcı aynı zamanda bir GUID olmalıdır.  Boşsa, kuruluşunuz için bir kiracı KIMLIĞI oluşturmanız gerekir. |
| **Azure AD Uygulaması Kimliği\***       | Kayıtlı Azure VM çözümünüz için tanımlayıcı  |
| **Azure AD Uygulaması anahtar\***      | Kayıtlı çözümünüzün kimlik doğrulama anahtarı |
|   |   |


## <a name="next-steps"></a>Sonraki adımlar

Bir sonraki [Market](./cpp-marketplace-tab.md) sekmesinde, çözümünüz hakkında pazarlama ve yasal bilgiler sunacaktır.
