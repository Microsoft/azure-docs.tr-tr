---
title: Azure Data Lake Analytics kullanarak avro verilerini sorgulama | Microsoft Docs
description: Cihaz telemetrisini blob depolamaya yönlendirmek ve BLOB depolamaya yazılan avro biçim verilerini sorgulamak için ileti gövdesi özelliklerini kullanın.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 92fc5bb88ff5efd8fe1a8cd61be833b3984b673a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605611"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Azure Data Lake Analytics kullanarak avro verilerini sorgulama

Bu makalede, Azure IoT Hub iletileri Azure hizmetlerine verimli bir şekilde yönlendirmek için avro verilerinin nasıl sorgulandığı açıklanır. [Ileti yönlendirme](iot-hub-devguide-messages-d2c.md) , ileti özelliklerine, ileti gövdesine, Device ikizi etiketlerine ve Device ikizi özelliklerine göre zengin sorgular kullanarak verileri filtrelemenizi sağlar. Ileti yönlendirmesinde sorgulama özellikleri hakkında daha fazla bilgi edinmek için [ileti yönlendirme sorgusu sözdizimi](iot-hub-devguide-routing-query-syntax.md)hakkında makalesine bakın.

Bu zorluk, Azure IoT Hub iletileri Azure Blob depolamaya yönlendirdiğini, varsayılan olarak IoT Hub bir ileti gövdesi özelliğine ve bir ileti özelliğine sahip olan içeriği avro biçiminde yazar. Diğer tüm uç noktalar için avro biçimi kullanılmaz. Veri ve ileti koruma için avro biçimi harika olsa da, verileri sorgulamak için kullanılması zor bir yoldur. Karşılaştırma bölümünde JSON veya CSV biçimi verileri sorgulamak için çok daha kolaydır. IoT Hub artık JSON 'daki blob depolamaya veri yazılmasını ve AVRO de desteklemektedir.

Daha fazla bilgi için bkz. [Azure Storage 'ı yönlendirme uç noktası olarak kullanma](iot-hub-devguide-messages-d2c.md#azure-storage).

İlişkisel olmayan büyük veri gereksinimlerini ve biçimlerini adresleyerek ve bu zorluğu aşmak için, verilerin dönüştürülmesi ve ölçeklendirilmesi için büyük veri desenlerinin birçoğunu kullanabilirsiniz. "Sorgu başına ödeme" desenlerinden biri, bu makalenin odaklanmasındaki Azure Data Lake Analytics. Sorguyu Hadoop veya diğer çözümlerde kolayca yürütebilseniz de, bu "sorgu başına ödeme" yaklaşımı için genellikle daha uygundur Data Lake Analytics.

U-SQL içindeki avro için bir "Extractor" vardır. Daha fazla bilgi için, bkz. [U-SQL avro örneği](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Avro verilerini bir CSV dosyasına sorgulama ve dışarı aktarma

Bu bölümde, verileri diğer depolara veya veri depolarına kolayca yerleştirebileceğiniz halde, avro verilerini sorgular ve Azure Blob depolama alanındaki bir CSV dosyasına dışarı aktarabilirsiniz.

1. İletileri seçmek için ileti gövdesinde bir özellik kullanarak Azure Blob depolama uç noktasına veri yönlendirmek üzere Azure IoT Hub ayarlayın.

   !["Özel uç noktalar" bölümü](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Yönlendirme kuralları](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Ayarlar yolları ve özel uç noktalar hakkında daha fazla bilgi için bkz. [IoT Hub Için Ileti yönlendirme](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Cihazınızın, ürün belgelerinde başvurulduğu şekilde, özelliklerde veya ileti gövdesinde kodlama, içerik türü ve gerekli verilere sahip olduğundan emin olun. Burada gösterildiği gibi Device Explorer bu öznitelikleri görüntülediğinizde, doğru ayarlandıklarından emin olabilirsiniz.

   ![Olay Hub 'ı veri bölmesi](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Bir Azure Data Lake Store örneği ve bir Data Lake Analytics örneği ayarlayın. Azure IoT Hub, bir Data Lake Store örneğine yönlendirmez, ancak bir Data Lake Analytics örneği için bir tane gerekir.

   ![Data Lake Store ve Data Lake Analytics örnekleri](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. Data Lake Analytics, Azure Blob depolamayı ek bir mağaza olarak yapılandırın. Azure IoT Hub verileri yönlendiren aynı BLOB depolama alanı.

   !["Veri kaynakları" bölmesi](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. [U-SQL avro örneğinde](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)anlatıldığı gıbı dört dll dosyasına ihtiyacınız vardır. Bu dosyaları Data Lake Store örneğindeki bir konuma yükleyin.

   ![Karşıya yüklenen dört DLL dosyası](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. Visual Studio 'da bir U-SQL projesi oluşturun.

   ! U-SQL projesi oluşturma] (./Media/iot-hub-Query-avro-Data/Query-avro-Data-6.png)

7. Aşağıdaki betiğin içeriğini yeni oluşturulan dosyaya yapıştırın. Vurgulanan üç bölümü değiştirin: Data Lake Analytics hesabınız, ilişkili DLL dosyası yolları ve depolama hesabınız için doğru yol.

   ![Değiştirilecek üç bölüm](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   Bir CSV dosyasına basit çıkış için gerçek U-SQL betiği:

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    10 analiz birimi ile sınırlı olan ve 177 dosya işlenen aşağıdaki betiği çalıştırmak için beş dakika Data Lake Analytics sürdü. Sonuç, aşağıdaki görüntüde görüntülenen CSV dosyası çıktısında gösterilir:

    ![CSV dosyasına çıktının sonuçları](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![Çıkış CSV dosyasına dönüştürüldü](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    JSON 'u ayrıştırmaya devam etmek için 8. adıma geçin.

8. Birçok IoT iletisi JSON dosya biçimindedir. Aşağıdaki satırları ekleyerek iletiyi bir JSON dosyasına ayrıştırarak WHERE yan tümcesini eklemenizi ve yalnızca gerekli verileri almanızı sağlayabilirsiniz.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    Çıktı, `SELECT` komutunda her öğe için bir sütun görüntüler.

    ![Her öğe için bir sütun gösteren çıktı](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure IoT Hub iletileri Azure hizmetlerine verimli bir şekilde yönlendirmek için avro verilerinin nasıl sorgulanalınacağını öğrendiniz.

IoT Hub kullanan uçtan uca çözümlerin tamamını örnekler için bkz. [Azure IoT Çözüm Hızlandırıcıları belgeleri](/azure/iot-accelerators).

IoT Hub çözümleri geliştirme hakkında daha fazla bilgi edinmek için [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)' na bakın.

IoT Hub ileti yönlendirme hakkında daha fazla bilgi için, bkz. [IoT Hub ile Ileti gönderme ve alma](iot-hub-devguide-messaging.md).
