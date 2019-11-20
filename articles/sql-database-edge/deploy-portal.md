---
title: Azure portal kullanarak SQL veritabanı Edge önizlemesi dağıtma | Microsoft Docs
description: Azure portal kullanarak Azure SQL veritabanı Edge 'i dağıtmayı öğrenin
keywords: SQL veritabanı ucunu dağıtma
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da756b702c994d69aae42ecef0e2da4d44eed39
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514101"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Azure SQL veritabanı Edge önizlemeyi dağıtma

Azure SQL veritabanı Edge önizlemesi, IoT ve Azure IoT Edge dağıtımları için iyileştirilmiş bir ilişkisel veritabanı altyapısıdır. IoT uygulamaları ve çözümleri için yüksek performanslı veri depolama ve işleme katmanı oluşturma olanakları sağlar. Bu hızlı başlangıçta, Azure portal kullanarak Azure IoT Edge aracılığıyla Azure SQL veritabanı Edge modülü oluşturmaya nasıl başlacağınız gösterilmektedir.

## <a name="before-you-begin"></a>Başlamadan önce

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
* [Azure portalında](https://portal.azure.com/) oturum açın.
* Aboneliği SQL veritabanı kenarının dağıtımı için etkin olacak şekilde [buraya](https://azure.microsoft.com/services/sql-database-edge/#contact)bir istek gönderebilirsiniz.
* [Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md)oluşturun.
* Azure portal bir [IoT Edge cihazı](../iot-edge/how-to-register-device-portal.md)kaydedin.
* [Azure portal IoT Edge modülünü dağıtmak](../iot-edge/how-to-deploy-modules-portal.md)üzere IoT Edge cihazı hazırlayın.

> [!NOTE]
> Bir Azure Linux sanal makinesini IoT Edge bir cihaz olarak dağıtmak için bu [hızlı başlangıç kılavuzuna](../iot-edge/quickstart-linux.md)bakın.

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Azure Marketi 'nden SQL veritabanı Edge modülünü dağıtma

Azure Marketi, [IoT Edge modüller](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)dahil olmak üzere Azure 'da çalışmak üzere sertifikalı ve iyileştirilmiş çok sayıda kurumsal uygulama ve çözüm arasında dolaşabileceğiniz çevrimiçi bir uygulamalar ve hizmetler marketi. Azure SQL veritabanı Edge, Market aracılığıyla bir uç cihaza dağıtılabilir.

1. Azure Market 'te Azure SQL veritabanı Edge modülünü bulun.<br><br>

   ![Market 'te SQL veritabanı Edge](media/deploy-portal/find-offer-marketplace.png)

2. Gereksinimlerinize en iyi eşleşen yazılım planını seçin ve **Oluştur**' a tıklayın. <br><br>

   ![Doğru yazılım planını seçin](media/deploy-portal/pick-correct-plan.png)

3. IoT Edge modülü için hedef cihazlar sayfasında, aşağıdaki ayrıntıları belirtin ve ardından **Oluştur** ' a tıklayın.

   |**Alan**  |**Açıklama**  |
   |---------|---------|
   |Abonelik  |  IoT Hub oluşturulduğu Azure aboneliği |
   |IoT Hub   |  IoT Edge cihazının kaydedildiği IoT Hub adı ve sonra "bir cihaza dağıt" seçeneğini belirleyin|
   |IoT Edge cihaz adı  |  SQL veritabanı ucunun dağıtılacağı IoT Edge cihazının adı |

4. **Modülleri ayarla** sayfasında, dağıtım modülleri bölümüne gıdın ve SQL veritabanı Edge modülüne göre **Yapılandır** ' a tıklayın. 

5. **IoT Edge özel modüller** bölmesinde, ortam değişkenleri için istenen değerleri belirtin ve/veya modül için oluşturma seçeneklerini ve istenen özellikleri özelleştirin. Desteklenen ortam değişkenlerinin tüm listesi için [SQL Server kapsayıcı ortam değişkenlerine](/sql/linux/sql-server-linux-configure-environment-variables/)başvurabilirsiniz.

   |**Parametresinin**  |**Açıklama**|
   |---------|---------|
   | Ad | Modülün adı. |
   |SA_PASSWORD  | SQL veritabanı Edge yönetici hesabı için güçlü bir parola belirtin. |
   |MSSQL_LCID   | SQL Server için kullanılacak dil KIMLIĞINI ayarlar. Örneğin, 1036, Fransızca ' dır. |
   |MSSQL_COLLATION | SQL Server için Varsayılan harmanlamayı ayarlar. Bu ayar, dil KIMLIĞI 'nin (LCıD) varsayılan eşlemesini harmanlama olarak geçersiz kılar. |

   > [!NOTE]
   > Lütfen **görüntü URI** 'sini veya modüldeki **ACCEPT_EULA** ayarlarını değiştirmeyin veya güncelleştirmeyin.

6. **IoT Edge özel modüller** bölmesinde, kapsayıcı oluşturma seçeneklerini, **ana bilgisayar bağlantı noktası** Için istenen değeri ve bağlama noktalarının **hedefini** güncelleştirin. Bağlama noktası hedefi, SQL veritabanı dosyalarının ana bilgisayar IoT Edge cihazında depolanacağı yerdir.

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. **IoT Edge özel modüller** BÖLMESINDE, SQL paketinin konumunu ve Stream Analytics iş bilgisini dahil etmek için *set Module ikizi 'ın istenen özelliklerini* güncelleştirin. Bu iki alan isteğe bağlıdır ve SQL veritabanı Edge modülünü bir veritabanı ve akış işiyle dağıtmak istiyorsanız kullanılmalıdır.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. **IoT Edge özel modüller** bölmesinde, *Yeniden Başlat ilkesini* her zaman ve *istenen durum* ' a ayarlayın.
9. **IoT Edge özel modüller** bölmesinde **Kaydet**' e tıklayın.
10. **Modülleri ayarla** sayfasında **İleri**' ye tıklayın.
11. **Modülleri ayarla** sayfasının **route (Isteğe bağlı) yolunu belirtin** sayfasında modüle modül veya modülle yönelik yolları IoT Edge hub iletişimi için belirtin [ve IoT Edge ' de yolları oluşturma '](../iot-edge/module-composition.md)ya bakın.
12. **İleri**’ye tıklayın.
13. **Gönder**' e tıklayın.

Bu hızlı başlangıçta, bir IoT Edge cihazında SQL veritabanı Edge modülü dağıttınız.

## <a name="next-steps"></a>Sonraki Adımlar

- [SQL veritabanı Edge 'de ONNX ile Machine Learning ve yapay zeka](onnx-overview.md).
- IoT Edge kullanarak SQL veritabanı kenarıyla uçtan uca IoT çözümü oluşturma.