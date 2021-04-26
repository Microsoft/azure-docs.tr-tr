---
title: Azure Cosmos DB öykünücü derleme göreviyle CI/CD işlem hattı ayarlama
description: Azure DevOps'ta Cosmos DB öykünücüsü derleme görevini kullanarak derleme ve yayın iş yükü ayarlama öğreticisi
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/28/2020
ms.author: dech
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: c7246511a88e2d2756a8ef56c5adf51ddbfd3e58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560540"
---
# <a name="set-up-a-cicd-pipeline-with-the-azure-cosmos-db-emulator-build-task-in-azure-devops"></a>Azure DevOps 'da Azure Cosmos DB öykünücü derleme göreviyle bir CI/CD işlem hattı ayarlama
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB Öykünücüsü, geliştirme amaçlı olarak Azure Cosmos DB hizmetine öykünen yerel bir ortam sağlar. Öykünücü sayesinde Azure aboneliği oluşturmadan veya masraf yapmadan uygulamanızı yerel ortamda geliştirip test edebilirsiniz. 

Azure DevOps için Azure Cosmos DB öykünücü derleme görevi, bir CI ortamında aynı işlemi yapmanıza olanak sağlar. Derleme göreviyle derleme ve yayın iş yüklerinizin bir parçası olarak öykünücüyle test çalıştırabilirsiniz. Bu görev öykünücünün çalıştığı bir Docker kapsayıcı başlatır ve derleme tanımının kalanı tarafından kullanılabilecek bir uç nokta sunar. İstediğiniz sayıda öykünücü örneği oluşturup başlatabilirsiniz ve oluşturduğunuz her örnek ayrı bir kapsayıcıda çalışır. 

Bu makalede Azure DevOps'ta test çalıştırmak için Cosmos DB öykünücüsü derleme görevini kullanan bir ASP.NET uygulaması için CI işlem hattı ayarlama adımları gösterilmektedir. Bir Node.js veya Python uygulaması için CI işlem hattı ayarlamak için benzer bir yaklaşım kullanabilirsiniz. 

## <a name="install-the-emulator-build-task"></a>Öykünücü derleme görevini yükleme

Derleme görevini kullanmak için öncelikle Azure DevOps kuruluşunuza yüklemeniz gerekir. [Market](https://marketplace.visualstudio.com/items?itemName=azure-cosmosdb.emulator-public-preview) sayfasında **Azure Cosmos DB Emulator** uzantısını bulun ve **Ücretsiz edinin**'e tıklayın.

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_1.png" alt-text="Azure DevOps Market'te Azure Cosmos DB Emulator derleme görevini bulun ve yükleyin":::

Ardından uzantının yükleneceği kuruluşu seçin. 

> [!NOTE]
> Bir Azure DevOps kuruluşuna uzantı yüklemek için bir hesap sahibi veya proje koleksiyonu yöneticisi olmanız gerekir. Gerekli izinlere sahip değilseniz ancak hesap üyesiyseniz uzantı isteyebilirsiniz. [Daha fazla bilgi edinin.](/azure/devops/marketplace/faq-extensions)

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_2.png" alt-text="Uzantı yüklemek için bir Azure DevOps organizasyonu seçin":::

## <a name="create-a-build-definition"></a>Derleme tanımı oluşturma

Artık uzantı yüklendikten sonra, Azure DevOps kuruluşunuzda oturum açıp projeler panosundan projenizi bulabilirsiniz. Projenize bir [derleme işlem hattı](/azure/devops/pipelines/get-started-designer?preserve-view=true&tabs=new-nav) ekleyebilir veya var olan derleme işlem hattını değiştirebilirsiniz. Bir derleme işlem hattınız varsa [Derleme tanımına Öykünücü derlemesi ekleme](#addEmulatorBuildTaskToBuildDefinition) bölümüne geçebilirsiniz.

1. Yeni bir derleme tanımı oluşturmak için Azure DevOps uygulamasının **Derlemeler** sekmesine gidin. **+ Yeni** seçeneğini belirleyin. \> **Yeni derleme işlem hattı**

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_1.png" alt-text="Yeni derleme işlem hattı oluşturma":::

2. İstenen **kaynak**, **Takım projesi**, **Depo** ve **El ile ve zamanlanan derlemeler için varsayılan dal** değerlerini seçin. Gerekli seçenekleri belirttikten sonra **Devam**’ı seçin

   :::image type="content" source="./media/tutorial-setup-ci-cd/CreateNewBuildDef_2.png" alt-text="Derleme işlem hattı için takım projesini, depoyu ve dalı seçme":::

3. Son olarak derleme işlem hattı için kullanmak istediğiniz şablonu belirleyin. Bu öğreticide **ASP.NET** şablonunu seçeceğiz. Artık Azure Cosmos DB öykünücü derleme görevini kullanacak şekilde ayarlayabileceğiniz bir yapı ardışık düzeni vardır. 

> [!NOTE]
> Yükleme, CI 'nin bir parçası olarak önceki bir görevde el ile yapılmazsa, bu CI için seçilecek aracı havuzunun Docker for Windows yüklü olması gerekir. Aracı havuzlarının seçimi için bkz. [Microsoft barındırılan aracılar](/azure/devops/pipelines/agents/hosted?tabs=yaml) makalesi. ' i kullanmaya başlamanız önerilir `Hosted VS2017` .

Azure Cosmos DB öykünücü Şu anda barındırılan VS2019 Aracısı havuzunu desteklemiyor. Ancak, öykünücü zaten VS2019 yüklendi ve öykünücü, aşağıdaki PowerShell cmdlet 'leri ile başlatılarak bunu kullanıyorsunuz. VS2019 kullanırken herhangi bir sorunla karşılaşırsanız, yardım için [Azure DevOps](https://developercommunity.visualstudio.com/spaces/21/index.html) ekibine ulaşın:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
Start-CosmosDbEmulator
```

## <a name="add-the-task-to-a-build-pipeline"></a><a name="addEmulatorBuildTaskToBuildDefinition"></a>Görevi derleme işlem hattına ekleme

1. Derleme işlem hattına görev eklemeden önce aracı işi eklemelisiniz. Derleme işlem hattınıza gidin **...** öğesini ve **Aracı işi ekle**’yi seçin.

1. Ardından, **+** öykünücü derleme görevini eklemek için aracı işinin yanındaki simgeyi seçin. Arama kutusundan **cosmos** araması yapın, **Azure Cosmos DB Öykünücüsünü** seçin ve aracı işine ekleyin. Derleme görevi, üzerinde Cosmos DB öykünücüsünün bir örneğinin çalıştığı bir kapsayıcı başlatır. Azure Cosmos DB Öykünücüsü görevi, öykünücünün çalışır durumda olmasını gerektiren diğer görevlerden önce yerleştirilmelidir.

   :::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_3.png" alt-text="Öykünücü derleme görevini derleme tanımına ekleme":::

Bu öğreticide, testlerimiz çalıştırmadan önce öykünücünün kullanılabildiğinden emin olmak için görevi en başa ekleyeceksiniz.

### <a name="add-the-task-using-yaml"></a>YAML kullanarak görevi ekleyin

Bu adım isteğe bağlıdır ve yalnızca bir YAML görevi kullanarak CI/CD işlem hattını ayarlıyorsanız gereklidir. Böyle durumlarda, YAML görevini aşağıdaki kodda gösterildiği gibi tanımlayabilirsiniz:

```yml
- task: azure-cosmosdb.emulator-public-preview.run-cosmosdbemulatorcontainer.CosmosDbEmulator@2
  displayName: 'Run Azure Cosmos DB Emulator'

- script: yarn test
  displayName: 'Run API tests (Cosmos DB)'
  env:
    HOST: $(CosmosDbEmulator.Endpoint)
    # Hardcoded key for emulator, not a secret
    AUTH_KEY: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
    # The emulator uses a self-signed cert, disable TLS auth errors
    NODE_TLS_REJECT_UNAUTHORIZED: '0'
```

## <a name="configure-tests-to-use-the-emulator"></a>Testleri öykünücüyü kullanacak şekilde yapılandırma

Şimdi testlerimizi öykünücüyü kullanacak şekilde yapılandıracağız. Öykünücü derleme görevi, derleme işlem hattındaki diğer görevlerin istek düzenleyebileceği "CosmosDbEmulator.Endpoint" ortam değişkenini dışarı aktarır. 

Bu öğreticide [Visual Studio Test görevini](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/VsTestV2/README.md) kullanarak **.runsettings** dosyasıyla yapılandırılmış birim testlerini çalıştıracağız. Birim testi kurulumu hakkında daha fazla bilgi edinmek için [belgeleri](/visualstudio/test/configure-unit-tests-by-using-a-dot-runsettings-file?preserve-view=true&view=vs-2017) inceleyin. Bu belgede kullandığınız tüm Todo uygulama kodu örneği [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app) 'da kullanılabilir

Uygulamanın birim testlerine iletilecek parametreleri tanımlayan örnek **.runsettings** dosyasını aşağıda görebilirsiniz. Kullanılan `authKey` değişkeninin öykünücü için [iyi bilinen anahtar](./local-emulator.md#authenticate-requests) olduğuna dikkat edin. Bu `authKey`, öykünücü derleme görevi tarafından beklenen anahtardır ve **.runsettings** dosyanızda tanımlanmalıdır.

```csharp
<RunSettings>
    <TestRunParameters>
    <Parameter name="endpoint" value="https://localhost:8081" />
    <Parameter name="authKey" value="C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==" />
    <Parameter name="database" value="ToDoListTest" />
    <Parameter name="collection" value="ItemsTest" />
  </TestRunParameters>
</RunSettings>
```

MongoDB için Azure Cosmos DB API 'sini kullanan bir uygulama için bir CI/CD işlem hattı ayarlıyorsanız, bağlantı dizesi varsayılan olarak 10255 numaralı bağlantı noktası numarasını içerir. Ancak, bu bağlantı noktası şu anda açık değildir, alternatif olarak bağlantıyı kurmak için 10250 numaralı bağlantı noktasını kullanmanız gerekir. Azure Cosmos DB MongoDB bağlantı dizesi için API, desteklenen bağlantı noktası numarası 10255 yerine 10250 olur.

`TestRunParameters` parametrelerine uygulamanın test projesindeki bir `TestContext` özelliği aracılığıyla başvurulur. Burada Cosmos DB ile çalışan örnek bir testi görebilirsiniz.

```csharp
namespace todo.Tests
{
    [TestClass]
    public class TodoUnitTests
    {
        public TestContext TestContext { get; set; }

        [TestInitialize()]
        public void Initialize()
        {
            string endpoint = TestContext.Properties["endpoint"].ToString();
            string authKey = TestContext.Properties["authKey"].ToString();
            Console.WriteLine("Using endpoint: ", endpoint);
            DocumentDBRepository<Item>.Initialize(endpoint, authKey);
        }
        [TestMethod]
        public async Task TestCreateItemsAsync()
        {
            var item = new Item
            {
                Id = "1",
                Name = "testName",
                Description = "testDescription",
                Completed = false,
                Category = "testCategory"
            };

            // Create the item
            await DocumentDBRepository<Item>.CreateItemAsync(item);
            // Query for the item
            var returnedItem = await DocumentDBRepository<Item>.GetItemAsync(item.Id, item.Category);
            // Verify the item returned is correct.
            Assert.AreEqual(item.Id, returnedItem.Id);
            Assert.AreEqual(item.Category, returnedItem.Category);
        }

        [TestCleanup()]
        public void Cleanup()
        {
            DocumentDBRepository<Item>.Teardown();
        }
    }
}
```

Visual Studio Test görevindeki Execution Options (Yürütme Seçenekleri) bölümüne gidin. **Settings file** (Ayarlar dosyası) seçeneğinde testlerin **.runsettings** dosyasıyla yapılandırıldığını belirtin. **Override test run parameters** (Test çalıştırması parametrelerini geçersiz kıl) seçeneğine `-endpoint $(CosmosDbEmulator.Endpoint)` girişini ekleyin. Bunu yaptığınızda Test görevi **.runsettings** dosyasında tanımlanan uç noktanın yerine öykünücü derleme görevinin uç noktasına başvurur.  

:::image type="content" source="./media/tutorial-setup-ci-cd/addExtension_5.png" alt-text="Uç noktası değişkenini öykünücü derleme görevi uç noktasıyla geçersiz kılma":::

## <a name="run-the-build"></a>Derlemeyi çalıştırma

Şimdi, derlemeyi **kaydedin ve kuyruğa** alın. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_1.png" alt-text="Ekran görüntüsü, Kaydet & kuyruğu seçiliyken bir derlemeyi gösterir.":::

Derleme başlatıldıktan sonra Cosmos DB öykünücüsü görevinin öykünücünün yüklü olduğu Docker görüntüsünü çekmeye başladığından emin olun. 

:::image type="content" source="./media/tutorial-setup-ci-cd/runBuild_4.png" alt-text="Ekran görüntüsü Cosmos D B öykünücü görevinin çekilmekte olduğunu gösterir.":::

Derleme tamamlandıktan sonra testlerinizin iletildiğinden ve tümünün derleme görevindeki Cosmos DB öykünücüsüyle çalıştığından emin olun!

:::image type="content" source="./media/tutorial-setup-ci-cd/buildComplete_1.png" alt-text="Ekran görüntüsü Özet sekmesindeki Ilerleme değerini gösterir.":::

## <a name="next-steps"></a>Sonraki adımlar

Yerel geliştirme ve test için öykünücü kullanımı hakkında daha fazla bilgi edinmek için bkz. [Yerel geliştirme ve test için Azure Cosmos DB Öykünücüsünü kullanma](./local-emulator.md).

Öykünücü TLS/SSL sertifikalarını dışarı aktarmak için bkz [. Java, Python ve Node.jsbirlikte kullanmak üzere Azure Cosmos DB öykünücü sertifikalarını dışarı aktarma ](./local-emulator-export-ssl-certificates.md)
