---
title: Azure Işlevleri 'ni kullanarak bir veritabanı temizleme görevi gerçekleştirme | Microsoft Docs
description: Düzenli aralıklarla satırları temizlemek için Azure SQL veritabanı 'na bağlanan bir görevi zamanlamak için Azure Işlevleri 'ni kullanın.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: glenga
ms.openlocfilehash: 469e0149a3b9dce22f0590240a053ee3b183c7b9
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815972"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Azure Işlevleri 'ni kullanarak bir Azure SQL veritabanına bağlanma

Bu makalede, Azure Işlevleri 'ni kullanarak bir Azure SQL veritabanına veya Azure SQL yönetilen örneğine bağlanan zamanlanmış bir iş oluşturma işlemi gösterilmektedir. İşlev kodu, veritabanındaki bir tablodaki satırları temizler. Yeni C# Işlev, Visual Studio 2019 ' de önceden tanımlanmış bir Zamanlayıcı tetikleyici şablonu temel alınarak oluşturulur. Bu senaryoyu desteklemek için, işlev uygulamasında bir uygulama ayarı olarak bir veritabanı bağlantı dizesi de ayarlamanız gerekir. Azure SQL yönetilen örneği için, Azure Işlevlerinden bağlanabilmek için [genel uç noktanın etkinleştirilmesi](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) gerekir. Bu senaryo, veritabanında bir toplu işlem kullanır. 

Bu C# işlevlerle çalışırken ilk deneyiminize sahipseniz, [Azure C# işlevleri geliştirici başvurusunu](functions-dotnet-class-library.md)okumanız gerekir.

## <a name="prerequisites"></a>Önkoşullar

+ Sürüm 2. x çalışma zamanını hedefleyen yerel bir işlev uygulaması oluşturmak için [Visual Studio kullanarak ilk işlevinizi oluşturma](functions-create-your-first-function-visual-studio.md) makalesindeki adımları izleyin. Ayrıca, projenizi Azure 'da bir işlev uygulamasına yayımlamalısınız.

+ Bu makalede, AdventureWorksLT örnek veritabanındaki **SalesOrderHeader** tablosunda toplu temizleme işlemini yürüten bir Transact-SQL komutu gösterilmektedir. AdventureWorksLT örnek veritabanını oluşturmak için [Azure Portal Azure SQL veritabanı oluşturma](../sql-database/sql-database-get-started-portal.md)makalesindeki adımları uygulayın.

+ Bu hızlı başlangıç için kullandığınız bilgisayarın genel IP adresi için [sunucu düzeyinde bir güvenlik duvarı kuralı](../sql-database/sql-database-get-started-portal-firewall.md) eklemeniz gerekir. Bu kural, SQL veritabanı örneğine yerel bilgisayarınızdan erişebiliyor olması gerekir.  

## <a name="get-connection-information"></a>Bağlantı bilgilerini alma

[Azure Portal Azure SQL veritabanı oluşturma](../sql-database/sql-database-get-started-portal.md)işlemi tamamlandığında oluşturduğunuz veritabanı için bağlantı dizesini almanız gerekir.

1. [Azure Portal](https://portal.azure.com/)’ında oturum açın.

1. Sol taraftaki menüden **SQL veritabanları** ' nı seçin ve **SQL veritabanları** sayfasında veritabanınızı seçin.

1. **Ayarlar** altında **bağlantı dizeleri** ' ni seçin ve tüm **ADO.net** bağlantı dizesini kopyalayın. Genel uç nokta için Azure SQL yönetilen örnek kopyası bağlantı dizesi.

    ![ADO.NET bağlantı dizesini kopyalayın.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Bağlantı dizesini ayarlama

İşlev uygulaması, Azure'daki işlev yürütme işlemlerinizi barındırır. En iyi güvenlik uygulaması olarak, işlev uygulaması ayarlarınızda bağlantı dizelerini ve diğer gizli dizileri depolayın. Uygulama ayarlarının kullanılması, bağlantı dizesinin kodunuzla yanlışlıkla açıklanmasını önler. İşlev uygulamanız için uygulama ayarlarına, doğrudan Visual Studio 'dan erişebilirsiniz.

Uygulamanızı daha önce Azure 'da yayımladınız. Daha önce yapmadıysanız, [işlev uygulamanızı Azure 'Da yayımlayın](functions-develop-vs.md#publish-to-azure).

1. Çözüm Gezgini, işlev uygulaması projesine sağ tıklayın ve **yayımla** > **uygulama ayarlarını yönet...** seçeneğini belirleyin. **Ayar ekle**' yi seçin, **Yeni uygulama ayarı adı**' nı `sqldb_connection` yazın ve **Tamam**' ı seçin.

    ![İşlev uygulaması için uygulama ayarları.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Yeni **sqldb_connection** ayarında, önceki bölümde kopyaladığınız bağlantı dizesini **Yerel** alana yapıştırın ve `{your_username}` ve `{your_password}` yer tutucuları gerçek değerlerle değiştirin. Güncelleştirilmiş değeri **uzak** alana kopyalamak için **yerelden değer Ekle** ' yi seçin ve ardından **Tamam**' ı seçin.

    ![SQL bağlantı dizesi ayarı ekleyin.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    Bağlantı dizeleri Azure 'da (**uzak**) şifrelenmiş olarak depolanır. Sızan parolaların sızmasını engellemek için, yerel. Settings. JSON proje dosyası (**Yerel**),. gitignore dosyası kullanarak kaynak denetiminden dışlanmalıdır.

## <a name="add-the-sqlclient-package-to-the-project"></a>Projeye SqlClient paketini ekleme

SqlClient kitaplığını içeren NuGet paketini eklemeniz gerekir. Bu veri erişim kitaplığı, bir SQL veritabanına bağlanmak için gereklidir.

1. Visual Studio 2019 ' de yerel işlev uygulama projenizi açın.

1. Çözüm Gezgini, uygulama projesine sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.

1. **Gözat** sekmesinde ```System.Data.SqlClient``` öğesini arayın ve bulduğunuzda seçin.

1. **System. Data. SqlClient** sayfasında sürüm `4.5.1` ' i seçin ve ardından **Install**' a tıklayın.

1. Yükleme tamamlandığında değişiklikleri gözden geçirin ve **Önizleme** penceresini kapamak için **Tamam**’a tıklayın.

1. **Lisans Kabulü** penceresi gösterilirse **Kabul Ediyorum**’a tıklayın.

Şimdi SQL veritabanınıza bağlanan C# işlev kodunu ekleyebilirsiniz.

## <a name="add-a-timer-triggered-function"></a>Zamanlayıcı ile tetiklenen işlev ekleme

1. Çözüm Gezgini, uygulama projesine sağ tıklayın ve  > **yeni Azure Işlevi** **Ekle**' yi seçin.

1. **Azure işlevleri** şablonu seçili olduğunda, yeni öğeyi `DatabaseCleanup.cs` gibi bir değere adlandırın ve **Ekle**' yi seçin.

1. **Yeni Azure işlevi** Iletişim kutusunda **Zamanlayıcı tetikleyicisi** ' ni ve ardından **Tamam**' ı seçin. Bu iletişim kutusu Zamanlayıcı tarafından tetiklenen işlev için bir kod dosyası oluşturur.

1. Yeni kod dosyasını açın ve dosyanın en üstüne aşağıdaki using deyimlerini ekleyin:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Var olan `Run` işlevini şu kodla değiştirin:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Bu işlev, sevk tarihini temel alarak `Status` sütununu güncelleştirmek için 15 saniyede bir çalışır. Süreölçer tetikleyicisi hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri Için süreölçer tetikleyicisi](functions-bindings-timer.md).

1. İşlev uygulamasını başlatmak için **F5** tuşuna basın. [Azure Functions Core Tools](functions-develop-local.md) yürütme penceresi Visual Studio 'nun arkasında açılır.

1. Başlangıçtan sonra 15 saniye sonra işlevi çalışır. Çıktıyı izleyin ve **SalesOrderHeader** tablosunda güncelleştirilmiş satır sayısını göz önünde edin.

    ![İşlev günlüklerini görüntüleyin.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    İlk yürütmede 32 satırlık verileri güncelleştirmeniz gerekir. @No__t-0 ifadesiyle daha fazla satır seçilmesi için, SalesOrderHeader tablo verilerinde değişiklik yapmadığınız takdirde, aşağıdaki çalıştırmalar bir veri satırı güncelleyerek güncelleştirme yapar.

[Bu işlevi yayımlamayı](functions-develop-vs.md#publish-to-azure)düşünüyorsanız, `TimerTrigger` özniteliğini her 15 saniyede bir daha makul bir [cron zamanlamaya](functions-bindings-timer.md#ncrontab-expressions) göre değiştirmeyi unutmayın.

## <a name="next-steps"></a>Sonraki adımlar

Daha sonra, nasıl kullanacağınızı öğrenin. Diğer hizmetlerle tümleştirilecek Logic Apps işlevleri.

> [!div class="nextstepaction"]
> [Logic Apps ile tümleşen bir işlev oluşturma](functions-twitter-email.md)

Işlevler hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

+ [Azure İşlevleri geliştirici başvurusu](functions-reference.md)  
  İşlevleri kodlamak ve tetikleyicileri ve bağlamaları tanımlamak için programcı başvurusu
+ [Azure İşlevlerini test etme](functions-test-a-function.md)  
  İşlevlerinizi test etmek için çeşitli araçları ve teknikleri açıklar.  
