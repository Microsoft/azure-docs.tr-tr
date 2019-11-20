---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 12c2f1bd2a3185d26eae02b5cd756392b5b87c16
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533191"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma

Bu bölümde, şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturur ve SQL Server veritabanını içeren bir şirket içi makine ile ilişkilendirirsiniz. Şirket içinde barındırılan tümleştirme çalışma zamanı, makinenizdeki SQL Server verileri Azure SQL veritabanı 'na kopyalayan bileşendir. 

1. Tümleştirme çalışma zamanının adı için bir değişken oluşturun. Benzersiz bir ad kullanın ve not edin. Daha sonra bu öğreticide kullanacaksınız. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Örnek çıktı aşağıdaki gibidir:

   ```json
    Name              : <Integration Runtime name>
    Type              : SelfHosted
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Description       : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DataFactory/factories/<DataFactoryName>/integrationruntimes/ADFTutorialIR
    ```
  
3. Oluşturulan tümleştirme çalışma zamanının durumunu almak için aşağıdaki komutu çalıştırın. **Durum** özelliği değerinin **NeedRegistration** olarak ayarlı olduğunu onaylayın. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Örnek çıktı aşağıdaki gibidir:

   ```json  
   State                     : NeedRegistration
   Version                   : 
   CreateTime                : 9/24/2019 6:00:00 AM
   AutoUpdate                : On
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   InternalChannelEncryption : 
   Capabilities              : {}
   ServiceUrls               : {eu.frontend.clouddatahub.net}
   Nodes                     : {}
   Links                     : {}
   Name                      : ADFTutorialIR
   Type                      : SelfHosted
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Description               : 
   Id                        : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroup name>/providers/Microsoft.DataFactory/factories/<DataFactory name>/integrationruntimes/<Integration Runtime name>
   ```

4. Şirket içinde barındırılan tümleştirme çalışma zamanını bulutta Data Factory hizmetine kaydetmek üzere kimlik doğrulaması anahtarlarını almak için aşağıdaki komutu çalıştırın: 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Örnek çıktı aşağıdaki gibidir:

   ```json
   {
    "AuthKey1": "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
    "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Sonraki adımda makinenize yüklediğiniz şirket içinde barındırılan tümleştirme çalışma zamanını kaydetmek için anahtarların birini (çift tırnak işaretlerini atın) kopyalayın.  

## <a name="install-the-integration-runtime"></a>Tümleştirme çalışma zamanını yükleme
1. Makinenizde zaten Integration Runtime varsa, **Program Ekle veya Kaldır** seçeneğini kullanarak programı kaldırın. 

2. Şirket içinde barındırılan tümleştirme çalışma zamanını yerel Windows makinesine [indirin](https://www.microsoft.com/download/details.aspx?id=39717). Yüklemeyi çalıştırın.

3. **Microsoft Integration Runtime Kurulumuna Hoş Geldiniz** sayfasında **İleri**'yi seçin.

4. **Son Kullanıcı Lisans Anlaşması** sayfasında koşulları ve lisans anlaşmasını kabul edin ve **İleri**'yi seçin.

5. **Hedef Klasör** sayfasında **İleri**'yi seçin.

6. **Microsoft Integration Runtime yüklenmeye hazır** sayfasında **Yükle**'yi seçin.

7. **Microsoft Integration Runtime Kurulumu Tamamlandı** sayfasında **Son**'u seçin.

8. **Integration Runtime’ı (Şirket içinde barındırılan) Kaydet** sayfasında, önceki bölümde kaydettiğiniz anahtarı yapıştırın ve **Kaydol**'u seçin. 

    ![Tümleştirme çalışma zamanını kaydetme](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

9. **Yeni Integration Runtime (Şirket içinde barındırılan) düğümü** sayfasında **son**' u seçin. 

10. Şirket içinde barındırılan tümleştirme çalışma zamanı başarıyla kaydedildiğinde aşağıdaki iletiyi görürsünüz:

    ![Başarıyla kaydedildi](media/data-factory-create-install-integration-runtime/registered-successfully.png)

14. **Integration Runtime’ı (Şirket içinde barındırılan) Kaydet** sayfasında **Configuration Manager'ı Başlat**'ı seçin.

15. Düğüm bulut hizmetine bağlandığında şu sayfayı görürsünüz:

    ![Düğüm bağlı sayfası](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Şimdi SQL Server veritabanınıza yönelik bağlantıyı test edin.

    ![Tanılama sekmesi](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. **Configuration Manager** sayfasında **Tanılama** sekmesine geçin.

    b. Veri kaynağı türü olarak **SqlServer**’ı seçin.

    c. Sunucu adını girin.

    d. Veritabanı adını girin.

    e. Kimlik doğrulama modunu seçin.

    f. Kullanıcı adını girin.

    g. Kullanıcı adı için ile ilişkili parolayı girin.

    h. Tümleştirme çalışma zamanının SQL Server’a bağlanabildiğini onaylamak için **Test**’i seçin. Bağlantı başarılı olursa yeşil bir onay işareti görürsünüz. Bağlantı başarılı olmazsa bir hata iletisi görürsünüz. Sorunları giderin ve tümleştirme çalışma zamanının SQL Server’a bağlanabildiğinden emin olun.    

    > [!NOTE]
    > Kimlik doğrulama türü, sunucu, veritabanı, kullanıcı ve parola değerlerini not edin. Daha sonra bu öğreticide kullanacaksınız. 
    
