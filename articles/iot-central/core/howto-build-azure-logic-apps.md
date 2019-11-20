---
title: Azure Logic Apps içinde IoT Central Bağlayıcısı ile iş akışları oluşturun | Microsoft Docs
description: İş akışlarını tetiklemek ve iş akışlarında cihaz oluşturmak, güncelleştirmek ve silmek için Azure Logic Apps IoT Central bağlayıcısını kullanın.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 2269ede83ad64172e316a208871ab65053b2d10e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951594"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Azure Logic Apps içinde IoT Central Bağlayıcısı ile iş akışları oluşturun

*Bu konu, oluşturucular ve yöneticiler için geçerlidir.*

Bulut hizmetleri ve şirket içi sistemler arasında uygulamaları ve verileri tümleştiren otomatik ölçeklenebilir iş akışları oluşturmak için Azure Logic Apps kullanın. Azure Logic Apps birçok Azure hizmeti, Microsoft hizmeti ve hizmet olarak yazılım (SaaS) ürününde çok sayıda bağlayıcı içerir. Azure Logic Apps IoT Central bağlayıcısını kullanarak IoT Central bir kural tetiklendiğinde iş akışlarını tetikleyebilirsiniz. Bir cihaz oluşturmak, bir cihazın özelliklerini ve ayarlarını güncelleştirmek veya bir cihazı silmek için IoT Central bağlayıcıdaki eylemleri de kullanabilirsiniz.

Microsoft Flow IoT Central bağlayıcısını kullanabilirsiniz. Hem Azure Logic Apps hem de Microsoft Flow tasarımcı-tümleştirme hizmetlerdir, ancak biraz farklı izleyicileri hedefleyebilir. Flow, gerek duydukları iş akışlarını oluşturmak için herhangi bir Office çalışanını güçler. Logic Apps, BT uzmanlarının veri bağlanması için ihtiyaç duydukları tümleştirmeleri oluşturmasını sağlar. Akışı [ve Logic Apps karşılaştırın](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). [Burada](howto-add-microsoft-flow.md)Microsoft Flow IoT Central Bağlayıcısı ile iş akışları oluşturma hakkında bilgi edinin.

## <a name="prerequisites"></a>Önkoşullar

- Kullandıkça Öde uygulaması
- Mantıksal uygulamalar oluşturmak ve yönetmek için bir Azure hesabı ve aboneliği

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Bir kural tetiklendiğinde iş akışını tetikleme

Bu bölümde, bir kural tetiklendiğinde Microsoft ekiplerine nasıl ileti nakledeceğiniz gösterilmektedir. İş akışınızı, Olay Hub 'ınıza bir olay göndermek, yeni bir Azure DevOps iş öğesi oluşturmak veya SQL Server 'da yeni bir satır eklemek gibi işlemleri yapmak için diğer bağlayıcıları kullanacak şekilde yapılandırabilirsiniz.

1. [IoT Central bir kural oluşturarak](howto-create-telemetry-rules.md)başlayın. Kural koşullarını kaydettikten sonra yeni bir eylem olarak **Azure Logic Apps** kutucuğunu seçin. **Azure Portal oluştur '** u seçin. Yeni bir mantıksal uygulama oluşturmak için Azure portal yönlendirilirsiniz. Azure hesabınızda oturum açmanız gerekebilir.

1. Yeni bir mantıksal uygulama oluşturmak için gerekli bilgileri girin. Yeni mantıksal uygulamanızı sağlamak için bir Azure aboneliği seçebilirsiniz. IoT Central uygulamanızın oluşturulduğu abonelikle aynı olması gerekmez. **Oluştur**'u seçin.

    ![Azure portal mantıksal uygulama oluşturma](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. Mantıksal uygulamanız başarıyla oluşturulduktan sonra, Logic Apps tasarımcısına otomatik olarak gezinilebilir. **Boş mantıksal uygulama**' yı seçin. 

    ![Boş mantıksal uygulama oluşturma](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. Tasarımcıda "IoT Central" araması yapın ve **bir kural** tetiklendiğinde tetikleyiciyi seçin. IoT Central uygulamanızda oturum açarken kullandığınız hesapla bağlayıcıda oturum açın.

    ![IoT Central Bağlayıcısı 'nda oturum açma](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Başarıyla oturum açtıktan sonra, alanları görmeniz gerekir. Açılır listeden **uygulamayı** ve **kuralı** seçin.

    ![Uygulama ve kural seçin](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Yeni bir eylem ekleyin. **İleti gönderme ekiplerini** arayın ve Microsoft ekipleri bağlayıcısından **ileti gönder** ' i seçin. Microsoft ekiplerinde kullandığınız hesapla bağlayıcıda oturum açın.

1. Eylemde, **ekibi** ve **kanalı**seçin. **İleti** alanını her iletinin söylediklerinize göre girin. IoT Central kuralınızdan *dinamik içerik* ekleyebilirsiniz ve bu, bildirime cihaz adı ve zaman damgası gibi önemli bilgileri sağlar.
    > [!NOTE]
    > Kuralı tetikleyen ölçüm ve özellik değerlerini almak için dinamik içerik penceresinde **daha fazla metni göster** ' i seçin.

    ![Dinamik bölme açıkken mantıksal uygulama düzenlemesi eylemi](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Eyleminizi düzenlemenizi bitirdiğinizde **Kaydet**' i seçin.

1. IoT Central uygulamanıza geri giderseniz, bu kuralın eylemler alanı altında bir Azure Logic Apps eylemi olduğunu görürsünüz.

Azure portalında Logic Apps IoT Central bağlayıcısını kullanarak her zaman bir iş akışı oluşturmaya başlayabilirsiniz. Daha sonra hangi IoT Central uygulamayı ve hangi kuralın bağlanılacağını seçebilirsiniz ve aynı şekilde çalışmaya devam edebilirsiniz. Her seferinde IoT Central kuralları sayfasından başlamak gerekmez.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Bir iş akışında cihaz oluşturma, güncelleştirme ve silme

Mantıksal uygulamanızda bir iş akışı oluştururken, IoT Central bağlayıcısını kullanarak bir eylem ekleyebilirsiniz. Kullanılabilir eylemler **cihaz oluşturma**, **cihazı güncelleştirme**ve **cihazı silme**işlemlerini oluşturur.

> [!NOTE]
> **Bir cihazı güncelleştirmek** ve **bir cihazı silmek**için, güncelleştirmek veya silmek ISTEDIĞINIZ mevcut bir cihazın kimliği gerekir. IoT Central cihazının KIMLIĞINI **Device Explorer** alabilir

![IoT Central cihaz Gezgini cihaz KIMLIĞI](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Sonraki adımlar

İş akışları oluşturmak için Microsoft Flow kullanmayı öğrendiğinize göre, önerilen sonraki adım [cihazları yönetmektedir](howto-manage-devices.md).
