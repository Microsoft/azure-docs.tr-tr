---
title: Excel Online 'da verileri, çalışma sayfalarını ve tabloları yönetme
description: Excel Online 'daki çalışma sayfalarındaki ve tablolardaki verileri, Azure Logic Apps kullanarak OneDrive için Iş veya Excel Online 'da yönetin
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: 097db6683127b410e713be53e6de838cf7734ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400732"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Excel Online verilerini Azure Logic Apps yönetme

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [Iş için Excel Online](/connectors/excelonlinebusiness/) Bağlayıcısı veya OneDrive Bağlayıcısı [Için Excel Online](/connectors/excelonline/) Ile, iş veya OneDrive için Excel Online 'daki verilerinize göre otomatik görevler ve iş akışları oluşturabilirsiniz. Bu bağlayıcı, verilerle çalışmanıza ve elektronik tabloları yönetmenize yardımcı olan eylemler sağlar; örneğin:

* Yeni çalışma sayfaları ve tablolar oluşturun.
* Çalışma sayfaları, tablolar ve satırlar alın ve yönetin.
* Tek satır ve anahtar sütunları ekleyin.

Daha sonra bu eylemlerdeki çıktıları diğer hizmetlere yönelik eylemlerle kullanabilirsiniz. Örneğin, her hafta çalışma sayfası oluşturan bir eylem kullanırsanız, Office 365 Outlook bağlayıcısını kullanarak onay e-postası gönderen başka bir eylem kullanabilirsiniz.

Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps](../logic-apps/logic-apps-overview.md) olduğunu gözden geçirin.

> [!NOTE]
> [İş Için Excel Online](/connectors/excelonlinebusiness/) ve [OneDrive bağlayıcıları için excel online](/connectors/excelonline/) , Azure Logic Apps çalışır ve [PowerApps için Excel Bağlayıcısı](/connectors/excel/)'ndan farklıdır.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* İş hesabınız veya kişisel Microsoft hesabı için bir [iş veya okul hesabı](https://www.office.com/)

  Excel verileriniz bir depolama klasöründe (örneğin, OneDrive) bir virgülle ayrılmış değer (CSV) dosyası olarak bulunabilir. 
  Aynı CSV dosyasını [düz dosya Bağlayıcısı](../logic-apps/logic-apps-enterprise-integration-flatfile.md)ile de kullanabilirsiniz.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Excel Online verilerinize erişmek istediğiniz mantıksal uygulama. Bu bağlayıcı yalnızca eylemler sağlar, bu nedenle mantıksal uygulamanızı başlatmak için **yineleme** tetikleyicisi gibi ayrı bir tetikleyici seçin.

## <a name="add-excel-action"></a>Excel eylemi Ekle

1. [Azure Portal](https://portal.azure.com), zaten açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Tetikleyici altında **yeni adım**' ı seçin.

1. Arama kutusuna filtreniz olarak "Excel" yazın. Eylemler listesi altında istediğiniz eylemi seçin.

   > [!NOTE]
   > Logic App Designer, 100 veya daha fazla sütunu olan tabloları yükleyemez. Mümkünse, tasarımcı 'nın tabloyu yükleyebilmesi için seçili tablodaki sütun sayısını azaltın.

1. İstenirse, iş veya okul hesabınızda oturum açın.

   Kimlik bilgileriniz, mantıksal uygulamanızı Excel Online 'a yönelik bir bağlantı oluşturmak ve verilerinize erişmek için yetkilendirin.

1. Seçili eylem için gerekli ayrıntıları sağlamaya ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) dosyaları tarafından açıklandığı şekilde Tetikleyiciler, Eylemler ve sınırlar gibi teknik ayrıntılar için, şu bağlayıcı başvuru sayfalarına bakın:

* [Iş için Excel Online](/connectors/excelonlinebusiness/)
* [OneDrive için Excel Online](/connectors/excelonline/)

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
