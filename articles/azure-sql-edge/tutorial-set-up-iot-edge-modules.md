---
title: Azure SQL Edge 'de IoT Edge modülleri ayarlama
description: Bu üç parçalı Azure SQL Edge öğreticisinin ikinci bölümünde, Iron ve yamallikleri tahmin etmek için IoT Edge modüller ve bağlantılar ayarlayacaksınız.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 5d768fdc2540496769883d839cfbb4f009a2000c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077644"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>IoT Edge modülleri ve bağlantıları ayarlama

Azure SQL Edge 'deki Iron ve yamallikleri tahmin etmek için üç bölümden oluşan Bu öğreticinin ikinci bölümünde aşağıdaki IoT Edge modüllerini ayarlayacaksınız:

- Azure SQL Edge
- Veri Oluşturucu IoT Edge modülü

## <a name="specify-container-registry-credentials"></a>Kapsayıcı kayıt defteri kimlik bilgilerini belirtin

Modül görüntülerinin barındırıldığı kapsayıcı kayıt defterlerinin kimlik bilgilerinin belirtilmesi gerekir. Bunlar, kaynak grubunuzda oluşturulan kapsayıcı kayıt defterinde bulunabilir. **Erişim tuşları** bölümüne gidin. Aşağıdaki alanları unutmayın:

- Kayıt defteri adı
- Oturum açma sunucusu
- Kullanıcı adı
- Parola

Şimdi IoT Edge modülünde kapsayıcı kimlik bilgilerini belirtin.

1. Kaynak grubunuzda oluşturulan IoT Hub 'ına gidin.

2. **Otomatik cihaz yönetimi** altındaki **IoT Edge** bölümünde **cihaz kimliği**' ne tıklayın. Bu öğretici için KIMLIK `IronOrePredictionDevice` .

3. **Modülleri ayarla** bölümünü seçin.

4. **Container Registry kimlik bilgileri** altında, aşağıdaki değerleri girin:

   | _Alan_   | _Değer_       |
   | -------   | -------       |
   | Name      | Kayıt defteri adı |
   | Adres   | Oturum açma sunucusu  | 
   | User Name | Kullanıcı adı      | 
   | Parola  | Parola      | 
  
## <a name="build-push-and-deploy-the-data-generator-module"></a>Veri Oluşturucu modülünü oluşturun, gönderin ve dağıtın

1. [Proje dosyalarını](https://github.com/microsoft/sqlsourabh/tree/main/SQLEdgeSamples/IoTEdgeSamples/IronOreSilica) makinenize kopyalayın.
2. Visual Studio 2019 kullanarak **IronOre_Silica_Predict. sln** dosyasını açın
3. **deployment.template.jsüzerindeki** kapsayıcı kayıt defteri ayrıntılarını güncelleştirin 
   ```json
   "registryCredentials":{
        "RegistryName":{
            "username":"",
            "password":""
            "address":""
        }
    }
   ```
4. Hedef kapsayıcı kayıt defterini (veya modül için depoyu) belirtmek için dosyadaki **modules.js** güncelleştirin
   ```json
   "image":{
        "repository":"samplerepo.azurecr.io/ironoresilicapercent",
        "tag":
    }
   ```
5. Projenin herhangi bir sorun olmadan çalıştığından emin olmak için projeyi hata ayıklamada veya yayın modunda yürütün 
6. Proje adına sağ tıklayıp **IoT Edge modülleri oluştur ve Gönder '** i seçerek projeyi kapsayıcı Kayıt defterinize gönderin.
7. Veri Oluşturucu modülünü, Edge cihazınıza bir IoT Edge modülü olarak dağıtın. 

## <a name="deploy-the-azure-sql-edge-module"></a>Azure SQL Edge modülünü dağıtma

1. Azure SQL Edge modülünü **+ Ekle** ' ye ve ardından **Market modülü**' ne tıklayarak dağıtın. 

2. **IoT Edge modülü marketi** dikey PENCERESINDE *Azure SQL Edge* ' i arayın ve *Azure SQL Edge geliştiricisi*' ni seçin. 

3. Azure SQL Edge modülünü yapılandırmak için **IoT Edge modüller** altında yeni eklenen *Azure SQL Edge* modülüne tıklayın. Yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. [Azure SQL Edge 'ı dağıtma](./deploy-portal.md).

4. `MSSQL_PACKAGE`Ortam değişkenini *Azure SQL Edge* modülü dağıtımına ekleyin ve bu öğreticiden [birinin](tutorial-deploy-azure-resources.md) 8. adımında oluşturulan VERITABANı dacpac dosyasının SAS URL 'sini belirtin.

5. **Güncelleştir** 'e tıklayın

6. **Cihazdaki modülleri ayarla** sayfasında, ileri ' ye tıklayın **: rotalar >**.

7. **Cihazdaki modülleri ayarla** sayfasındaki rotalar bölmesinde, modül için aşağıdaki açıklandığı gibi IoT Edge hub iletişimi yolları belirtin. Aşağıdaki yol tanımlarında modül adlarını güncelleştirdiğinizden emin olun.

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   Örnek:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. **Cihazdaki modülleri ayarla** sayfasında, ileri ' ye tıklayın **: gözden geçir + oluştur >**

8. **Cihazdaki modülleri ayarla** sayfasında **Oluştur** ' a tıklayın.

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>T-SQL akış Işini Azure SQL Edge 'de oluşturun ve başlatın.

1. Azure Data Studio'yu açın.

2. **Hoş geldiniz** sekmesinde, aşağıdaki ayrıntılarla yeni bir bağlantı başlatın:

   |_Alan_|_Değer_|
   |-------|-------|
   |Bağlantı türü| Microsoft SQL Server|
   |Sunucu|Bu tanıtım için oluşturulan VM 'de belirtilen genel IP adresi|
   |Kullanıcı adı|sa|
   |Parola|Azure SQL Edge örneği oluşturulurken kullanılan güçlü parola|
   |Veritabanı|Varsayılan|
   |Sunucu grubu|Varsayılan|
   |Ad (isteğe bağlı)|İsteğe bağlı bir ad sağlayın|

3. **Bağlan** 'a tıklayın

4. **Dosya** menüsü sekmesinde yeni bir not defteri açın veya CTRL + N klavye kısayolunu kullanın.

5. Yeni sorgu penceresinde, T-SQL akış işini oluşturmak için aşağıdaki betiği yürütün. Betiği yürütmeden önce, aşağıdaki değişkenleri değiştirdiğinizden emin olun. 
   - *SQL_SA_Password:* Azure SQL Edge modülünü dağıtırken belirtilen MSSQL_SA_PASSWORD değeri. 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. Veri oluşturma modülündeki verilerin veritabanına aktarılmakta olduğunu doğrulamak için aşağıdaki sorguyu kullanın. 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


Bu öğreticide, veri Oluşturucu modülünü ve SQL Edge modülünü dağıttık. Daha sonra veri oluşturma modülü tarafından oluşturulan verileri SQL 'e akışa almak için bir akış işi oluşturduk. 

## <a name="next-steps"></a>Sonraki Adımlar

- [ONNX kullanarak Azure SQL Edge 'de ML modeli dağıtma](tutorial-run-ml-model-on-sql-edge.md)
