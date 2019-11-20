---
title: Dropbox 'a bağlanma-Azure Logic Apps
description: Dropbox REST API 'Leri ve Azure Logic Apps dosya yükleme ve yönetme
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 03/01/2019
tags: connectors
ms.openlocfilehash: 4e0689454ec074348fcbc775373a48d6825cfac4
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050983"
---
# <a name="upload-and-manage-files-in-dropbox-by-using-azure-logic-apps"></a>Azure Logic Apps kullanarak Dropbox 'ta dosya yükleme ve yönetme

Dropbox Bağlayıcısı ve Azure Logic Apps ile Dropbox hesabınızdaki dosyaları karşıya yükleyen ve yöneten otomatik iş akışları oluşturabilirsiniz. 

Bu makalede, mantıksal uygulamanızdan Dropbox 'a nasıl bağlanacağınız ve sonra **bir dosya oluşturulduğunda** ve açılan kutu **yolu kullanarak dosya Içeriğini alma** işlemi olduğunda Dropbox nasıl ekleneceği gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/).

* Ücretsiz olarak kaydolabilir bir [Dropbox hesabı](https://www.dropbox.com/). Hesap kimlik bilgileriniz, mantıksal uygulamanız ve Dropbox hesabınız arasında bağlantı oluşturmak için gereklidir.

* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md)hakkında temel bilgi. Bu örnekte, boş bir mantıksal uygulama gerekir.

## <a name="add-trigger"></a>Tetikleyici ekle

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Arama kutusunda **Tümü**' ni seçin. Arama kutusuna filtreniz olarak "Dropbox" yazın.
Tetikleyiciler listesinde, bu tetikleyiciyi seçin: **Bir dosya oluşturulduğunda**

   ![Dropbox tetikleyicisi Seç](media/connectors-create-api-dropbox/select-dropbox-trigger.png)

1. Dropbox hesabınızın kimlik bilgilerinizle oturum açın ve Azure Logic Apps için Dropbox verilerinize erişim yetkisi verin.

1. Tetikleyiciniz için gerekli bilgileri sağlayın. 

   Bu örnekte, dosya oluşturmayı izlemek istediğiniz klasörü seçin. Klasörlerinize gözatıp **klasör** kutusunun yanındaki klasör simgesini seçin.

## <a name="add-action"></a>Eylem ekle

Şimdi herhangi bir yeni dosyadan içerik alan bir eylem ekleyin.

1. Tetikleyici altında, **İleri adım**' ı seçin. 

1. Arama kutusunda **Tümü**' ni seçin. Arama kutusuna filtreniz olarak "Dropbox" yazın.
Eylemler listesinden şu eylemi seçin: **Yolu kullanarak dosya içeriğini al**

1. Dropbox 'a erişme Azure Logic Apps henüz yetkiniz yoksa, erişimi Şimdi Yetkilendir.

1. Kullanmak istediğiniz dosya yoluna gitmek için **dosya yolu** kutusunun yanındaki üç nokta ( **...** ) düğmesini seçin. 

   Ayrıca, **dosya yolu** kutusunun içine tıklayabilir ve dinamik içerik listesinden, değeri önceki bölümde eklediğiniz tetikleyiciden çıktı olarak kullanılabilir olan **dosya yolu**' nu seçin.

1. İşiniz bittiğinde mantıksal uygulamanızı kaydedin.

1. Mantıksal uygulamanızı tetiklemek için Dropbox 'ta yeni bir dosya oluşturun.

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) dosyasında açıklandığı gibi Tetikleyiciler, Eylemler ve sınırlar gibi teknik ayrıntılar için [bağlayıcının başvuru sayfasına](/connectors/dropbox/)bakın.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
