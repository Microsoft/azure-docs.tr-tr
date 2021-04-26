---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: b5450e4846c3c49c89830ae65c50a95ee0c8d6eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803221"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Derleme ve yayın işlem hatları ile IoT Edge CI/CD 'yi doğrulama

Bir yapı işini tetiklemek için, kaynak kodu deposuna bir kayıt gönderebilir veya el ile tetikleyebilirsiniz. Bu bölümde, onun çalıştığını test etmek için CI/CD işlem hattını el ile tetiklersiniz. Sonra dağıtımın başarılı olduğunu doğrulayın.

1. Sol bölme menüsünde, işlem **hatları** ' nı seçin ve bu makalenin başlangıcında oluşturduğunuz derleme işlem hattını açın.

2. Yapı işlem hattınızda derleme işini, sağ üst köşedeki işlem hattını **Çalıştır** düğmesini seçerek tetikleyebilirsiniz.

    ![Ardışık düzen Çalıştır düğmesini kullanarak derleme işlem hattınızı el ile tetikleme](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. Çalıştırma işlem **hattı** ayarlarını gözden geçirin. Sonra **Çalıştır**' ı seçin.

    ![Çalıştırma işlem hattı seçeneklerini belirtin ve Çalıştır 'ı seçin](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. Çalıştırmanın ilerlemesini izlemek için **Aracı işi 1** ' i seçin. İşi seçerek iş çıkışının günlüklerini gözden geçirebilirsiniz. 

    ![İşin günlük çıkışını gözden geçirin](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. Derleme işlem hattı başarıyla tamamlanırsa, **geliştirme** aşamasına bir yayın tetikler. Başarılı **geliştirme** sürümü, IoT Edge cihazlarını hedeflemek için IoT Edge dağıtımı oluşturur.

    ![Geliştirme sürümü](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. Yayın günlüklerini görmek için **geliştirme** aşaması ' na tıklayın.

    ![Yayın günlükleri](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)

7. İşlem hatlarınız başarısız olursa günlüklere bakarak başlayın. İşlem hattı çalıştırma özetine gidip işi ve görevi seçerek günlükleri görüntüleyebilirsiniz. Belirli bir görev başarısız olursa, bu görev için günlüklere bakın. Günlükleri yapılandırma ve kullanma hakkında ayrıntılı yönergeler için bkz. işlem [hattı sorunlarını tanılamak için günlükleri gözden geçirme](/azure/devops/pipelines/troubleshooting/review-logs).
