---
title: Verilerinizi dışarı aktarın ve silin
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (klasik) tarafından depolanan ürün içi veriler, Azure portal ve ayrıca kimliği doğrulanmış REST API 'Leri aracılığıyla dışarı ve silme için kullanılabilir. Telemetri verilerine Azure Gizlilik portalı üzerinden erişilebilir. Bu makalede nasıl yapılacağı gösterilir.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 9d17ff38ad521dcc282be4f06d33021faeb495b8
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837627"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio ürün içi kullanıcı verilerini dışa aktarma ve silme (klasik)

Azure portal, Studio (klasik) arabirimi, PowerShell ve kimliği doğrulanmış REST API 'Lerini kullanarak Azure Machine Learning Studio (klasik) tarafından depolanan ürün içi verileri silebilir veya dışarı aktarabilirsiniz. Bu makalede nasıl yapılacağı açıklanır. 

Telemetri verilerine Azure Gizlilik portalı üzerinden erişilebilir. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Studio (klasik) hangi tür Kullanıcı verileri toplar?

Bu hizmet için Kullanıcı verileri, hizmet ile kullanıcı etkileşimlerinin çalışma alanlarına ve telemetri kayıtlarına erişme yetkisi olan kullanıcılar hakkında bilgiler içerir.

Machine Learning Studio (klasik) iki tür Kullanıcı verisi vardır:
- **Kişisel hesap verileri:** Hesap kimlikleri ve hesap ile ilişkili e-posta adresleri.
- **Müşteri verileri:** Analiz etmek için karşıya yüklediğiniz veriler.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Studio (klasik) hesap türleri ve verilerin depolandığı yer

Machine Learning Studio (klasik) içinde üç tür hesap vardır. Sahip olduğunuz hesap türü, verilerinizin nasıl depolandığını ve nasıl silineceğini veya dışarı aktarabileceğini belirler.

- **Konuk çalışma alanı** , ücretsiz, anonim bir hesaptır. E-posta adresi veya parola gibi kimlik bilgileri sağlamadan kaydolabilirsiniz.
    -  Veriler, Konuk çalışma alanının süresi dolduktan sonra temizlenir.
    - Konuk kullanıcılar Kullanıcı arabirimi, REST API 'Leri veya PowerShell paketi aracılığıyla müşteri verilerini dışa aktarabilir.
- **Ücretsiz çalışma alanı** , Microsoft hesabı kimlik bilgileriyle oturum açmak için kullanabileceğiniz ücretsiz bir hesaptır. bir e-posta adresi ve parola.
    - Veri konu hakları (DSR) isteklerine tabi olan kişisel ve müşteri verilerini dışarı ve silebilirsiniz.
    - Kullanıcı arabirimi, REST API 'Leri veya PowerShell paketi aracılığıyla müşteri verilerini dışa aktarabilirsiniz.
    - Azure AD hesapları kullanmayan ücretsiz çalışma alanlarında telemetri, gizlilik Portalı kullanılarak aktarılabilir.
    - Çalışma alanını sildiğinizde, tüm kişisel müşteri verilerini silersiniz.
- **Standart çalışma alanı** , oturum açma kimlik bilgileriyle erişebileceğiniz ücretli bir hesaptır.
    - DSR isteklerine tabi olan kişisel ve müşteri verilerini dışarı ve silebilirsiniz.
    - Azure Gizlilik portalı aracılığıyla verilere erişebilirsiniz
    - Kullanıcı arabirimi, REST API 'Leri veya PowerShell paketi aracılığıyla kişisel ve müşteri verilerini dışarı aktarabilirsiniz
    - Azure portal verilerinizi silebilirsiniz.

## <a name="delete"></a>Studio 'da çalışma alanı verilerini silme (klasik) 

### <a name="delete-individual-assets"></a>Tek tek varlıkları Sil

Kullanıcılar bir çalışma alanındaki varlıkları seçip Sil düğmesini seçerek silebilir.

![Machine Learning Studio varlıkları silme (klasik)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Tüm çalışma alanını sil

Kullanıcılar, çalışma alanının tamamını da silebilir:
- Ücretli çalışma alanı: Azure portal ile silin.
- Ücretsiz çalışma alanı: **Ayarlar** bölmesindeki Sil düğmesini kullanın.

![Machine Learning Studio (klasik) içinde ücretsiz bir çalışma alanını silme](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>PowerShell ile Studio (klasik) verilerini dışa aktarma
Komutları kullanarak tüm bilgilerinizi klasik Azure Machine Learning Studio taşınabilir bir biçimde dışarı aktarmak için PowerShell kullanın. Bilgi için bkz. [Azure Machine Learning Studio Için PowerShell modülü (klasik)](powershell-module.md) makalesi.

## <a name="next-steps"></a>Sonraki adımlar

Web Hizmetleri ve taahhüt planı faturalandırmasını kapsayan belgeler için, bkz. [Azure Machine Learning Studio (klasik) REST API başvurusu](https://docs.microsoft.com/rest/api/machinelearning/). 
