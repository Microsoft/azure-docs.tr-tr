---
title: Visual Studio kaynak grubu projelerini oluşturma & dağıtma
description: Azure Kaynak grubu projesi oluşturmak ve kaynakları Azure 'a dağıtmak için Visual Studio 'Yu kullanın.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 4c0e19042506e0cf1772398b0a02b81f0245b1c7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306985"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Visual Studio aracılığıyla Azure kaynak grupları oluşturma ve dağıtma

Visual Studio ile altyapınızı ve kodlarınızı Azure’a dağıtan bir proje oluşturabilirsiniz. Örneğin, Web sitesi için Web konağını, Web sitesini ve kodu dağıtabilirsiniz. Visual Studio genelde karşılaşılan senaryoların dağıtılması için birçok farklı başlangıç şablonu sağlar. Bu makalede bir Web uygulaması dağıtırsınız.

Bu makalede [, Visual Studio 2019 veya sonraki bir sürümünü kullanarak Azure geliştirme ve ASP.NET iş yükleri yüklü](/visualstudio/install/install-visual-studio)olarak açıklanmaktadır. Visual Studio 2017 kullanıyorsanız, deneyiminiz büyük ölçüde aynıdır.

## <a name="create-azure-resource-group-project"></a>Azure Kaynak Grubu projesi oluşturma

Bu bölümde, bir **Web uygulaması** şablonuyla bir Azure Kaynak grubu projesi oluşturacaksınız.

1. Visual Studio 'da **Dosya** > **Yeni** > **Proje**' yi seçin.
1. **Azure Kaynak grubu** proje şablonunu ve Ileri ' **yi** seçin.

    ![Ekran görüntüsünde, Azure Kaynak grubu ile yeni bir proje oluştur penceresi ve sonraki düğme vurgulandığını gösterir.](./media/create-visual-studio-deployment-project/create-project.png)

1. Projenize bir ad verin. Diğer varsayılan ayarlar muhtemelen iyidir, ancak ortamınız için çalışmak üzere bunları gözden geçirin. İşiniz bittiğinde **Oluştur**’u seçin.

    ![Proje oluşturma](./media/create-visual-studio-deployment-project/name-project.png)

1. Azure Resource Manager’da dağıtmak istediğiniz şablonu seçin. Dağıtmak istediğiniz proje türüne bağlı olarak çok sayıda farklı seçeneğiniz olduğunu unutmayın. Bu makale için **Web uygulaması** şablonunu ve **Tamam**' ı seçin.

    ![Şablon seçme](./media/create-visual-studio-deployment-project/select-project.png)

    Seçtiğiniz şablon sadece başlangıçtır; senaryonuzun gereksinimlerini karşılamak üzere kaynak ekleyebilir ve kaldırabilirsiniz.

1. Visual Studio, Web uygulaması için bir kaynak grubu dağıtım projesi oluşturur. Projenizin dosyalarını görmek için dağıtım projesindeki düğüme bakın.

    ![Düğümleri göster](./media/create-visual-studio-deployment-project/show-items.png)

    Web uygulaması şablonunu seçtiğinizden, aşağıdaki dosyaları görürsünüz:

   | Dosya adı | Açıklama |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Azure Resource Manager’da dağıtılacak PowerShell komutlarını çalıştıran PowerShell betiği. Visual Studio, şablonunuzu dağıtmak için bu PowerShell betiğini kullanır. |
   | Üzerinde WebSite.js |Azure’da dağıtmak istediğiniz altyapıyı tanımlayan Resource Manager şablonu ve dağıtım sırasında sağlayabileceğiniz parametreler. Resource Manager’ın kaynakları doğru sırayla dağıtmasını sağlamak için kaynaklarınız arasındaki bağımlılıkları da tanımlar. |
   | Üzerinde WebSite.parameters.js |Şablon tarafından gereken değerleri içeren bir parametre dosyası. Her bir dağıtımı özelleştirmek için parametre değerlerini geçirirsiniz. **Derleme eyleminin** **içerik** olarak ayarlandığını unutmayın. Daha fazla parametre dosyası eklerseniz, derleme eyleminin **içerik** olarak ayarlandığından emin olun. |

    Tüm kaynak grubu dağıtım projeleri bu temel dosyaları içerir. Diğer projelerde diğer işlevleri desteklemek için daha fazla dosya olabilir.

## <a name="customize-resource-manager-template"></a>Kaynak Yöneticisi şablonu özelleştirme

Dağıtım projesini, dağıtmak istediğiniz kaynakları açıklayan Kaynak Yöneticisi şablonunu değiştirerek özelleştirebilirsiniz. Resource Manager şablonu bileşenleri hakkında daha fazla bilgi edinmek için [Azure Resource Manager şablonları yazma](template-syntax.md).

1. Şablonunuzda çalışmak için **üzerindeWebSite.js** açın.

1. Visual Studio düzenleyicisi, Resource Manager şablonu düzenleme konusunda size yardımcı olan araçlar sağlar. **JSON Ana Hattı** penceresi, şablonunuzda tanımlanan bileşenleri görmenizi kolaylaştırır.

   ![JSON ana hattını göster](./media/create-visual-studio-deployment-project/show-json-outline.png)

1. Şablonun ilgili bölümüne gitmek için anahatta bir öğe seçin.

   ![JSON 'a git](./media/create-visual-studio-deployment-project/navigate-json.png)

1. JSON Ana Hattı penceresinin üst tarafında bulunan **Kaynak Ekle** düğmesini seçerek veya **kaynaklar**’a sağ tıklayıp **Yeni Kaynak Ekle**’yi seçerek yeni kaynak ekleyebilirsiniz.

   ![Ekran görüntüsü, yeni kaynak ekle seçeneğinin vurgulandığı JSON ana hat penceresini gösterir.](./media/create-visual-studio-deployment-project/add-resource.png)

1. **Depolama hesabı** ' nı seçin ve bir ad verin. 11 karakterden uzun olmayan ve yalnızca sayı ile küçük harf içeren bir ad belirtin.

   ![Depolama ekleme](./media/create-visual-studio-deployment-project/add-storage.png)

1. Yalnızca kaynak eklenmediğini, aynı zamanda depolama hesabı türü için bir parametre ve depolama hesabı adı bir değişken eklendiğini unutmayın.

   ![Ana hattı göster](./media/create-visual-studio-deployment-project/show-new-items.png)

1. Depolama hesabı türünün parametresi, izin verilen türler ve varsayılan bir tür ile önceden tanımlanmıştır. Bu değerleri bırakabilir veya senaryonuz için düzenleyebilirsiniz. Bu şablon aracılığıyla herkesin **Premium_LRS** depolama hesabı dağıtmasını istemiyorsanız izin verilen türlerden bunu kaldırın.

   ```json
   "demoaccountType": {
     "type": "string",
     "defaultValue": "Standard_LRS",
     "allowedValues": [
       "Standard_LRS",
       "Standard_ZRS",
       "Standard_GRS",
       "Standard_RAGRS"
     ]
   }
   ```

1. Visual Studio, şablonu düzenlenirken kullanılabilen özellikleri anlamanıza yardımcı olmak için IntelliSense de sağlar. Örneğin, App Service planınızın özelliklerini düzenlemek için **HostingPlan** kaynağına gidin ve **resources** için bir değer ekleyin. IntelliSense’in kullanılabilir değerleri gösterdiğini ve bu değerler için bir açıklama sunduğunu unutmayın.

   ![IntelliSense 'i göster](./media/create-visual-studio-deployment-project/show-intellisense.png)

   **Numberofçalışanları** 1 olarak ayarlayabilir ve dosyayı kaydedebilirsiniz.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. WebSite.parameters.jsdosya **üzerinde** açın. Dağıtım sırasında dağıtılan kaynağı özelleştiren değerleri geçirmek için parametreler dosyasını kullanın. Barındırma planına bir ad verin ve dosyayı kaydedin.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       }
     }
   }
   ```

## <a name="deploy-project-to-azure"></a>Projeyi Azure 'a dağıtma

Artık projenizi bir kaynak grubuna dağıtmaya hazırsınız.

Varsayılan olarak, projedeki PowerShell betiği (Deploy-AzureResourceGroup.ps1) Azurerd modülünü kullanır. AzureRM modülünü hala yüklediyseniz ve kullanmaya devam etmek istiyorsanız, bu varsayılan betiği kullanabilirsiniz. Bu komut dosyası ile çözümünüzü dağıtmak için Visual Studio arabirimini kullanabilirsiniz.

Ancak, yeni [az modüle](/powershell/azure/new-azureps-module-az)geçiş yaptıysanız projenize yeni bir komut dosyası eklemeniz gerekir. Az Module kullanan bir betik eklemek için [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) betiğini kopyalayın ve projenize ekleyin. Bu betiği dağıtım için kullanmak üzere, Visual Studio 'nun dağıtım arabirimini kullanmak yerine bir PowerShell konsolundan çalıştırmanız gerekir.

Her iki yaklaşım da bu makalede gösterilmektedir. Bu makale, Azurere modül betiği olarak varsayılan komut dosyası ve az Module betiği olarak yeni komut dosyası anlamına gelir.

### <a name="az-module-script"></a>Az Module betiği

Az Module betiği için bir PowerShell konsolu açın ve şunu çalıştırın:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>Azurerd modülü betiği

Azurerd modül betiği için Visual Studio 'Yu kullanın:

1. Dağıtım projesi düğümünün kısayol menüsünde Yeni **Dağıt**' ı seçin  >  .

    ![Yeni dağıtım menü öğesi](./media/create-visual-studio-deployment-project/deploy.png)

1. **Kaynak Grubuna Dağıt** iletişim kutusu görüntülenir. **Kaynak Grubu** açılır kutusunda, mevcut bir kaynak grubu seçin veya yeni bir tane oluşturun. **Dağıt**'ı seçin.

    ![Kaynak grubuna Dağıt iletişim kutusu](./media/create-visual-studio-deployment-project/show-deployment.png)

1. **Çıktı** pencerelerinde dağıtımın durumunu görürsünüz. Dağıtım tamamlandığında son ileti aşağıdakine benzer bir ifadeyle dağıtımın başarılı olduğunu belirtir:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Dağıtılan kaynakları görüntüle

Sonuçları kontrol edelim.

1. Bir tarayıcıda [Azure Portal](https://portal.azure.com/) açın ve hesabınızda oturum açın. Kaynak grubunu görmek için **Kaynak grupları**’nu ve dağıttığınız kaynak grubunu seçin.

1. Dağıtılan tüm kaynakları görürsünüz. Depolama hesabı adının, ilgili kaynağı eklerken belirttiğiniz adla tam olarak aynı olmadığına dikkat edin. Depolama hesabı benzersiz olmalıdır. Şablon, benzersiz bir ad oluşturmak için girdiğiniz ada otomatik olarak bir karakter dizesi ekler.

    ![Kaynakları göster](./media/create-visual-studio-deployment-project/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Projeye kod Ekle

Bu noktada, uygulamanız için altyapı dağıttınız, ancak proje ile dağıtılan gerçek bir kod yoktur.

1. Visual Studio çözümünüze bir proje ekleyin. Çözüme sağ tıklayın ve   >  **Yeni proje** Ekle ' yi seçin.

    ![Proje Ekle](./media/create-visual-studio-deployment-project/add-project.png)

1. ASP.NET Core bir **Web uygulaması** ekleyin.

    ![Web uygulaması Ekle](./media/create-visual-studio-deployment-project/add-app.png)

1. Web uygulamanıza bir ad verin ve **Oluştur**' u seçin.

    ![Web uygulamasını Adlandır](./media/create-visual-studio-deployment-project/name-web-app.png)

1. **Web uygulaması** ' nı seçin ve **oluşturun**.

    ![Web uygulaması Seç](./media/create-visual-studio-deployment-project/select-project-type.png)

1. Visual Studio web uygulamanızı oluşturduktan sonra her iki projeyi de çözümde görürsünüz.

    ![Projeleri göster](./media/create-visual-studio-deployment-project/show-projects.png)

1. Bundan böyle kaynak grubu projenizin yeni projeyi tanıdığından emin olmanız gerekir. Kaynak grubu projenize (ExampleAppDeploy) geri dönün. **Başvurular**’a sağ tıklayın ve **Başvuru Ekle**’yi seçin.

    ![Ekran görüntüsü, başvuru Ekle seçeneğinin vurgulandığı örnek AppDeploy menüsünü gösterir.](./media/create-visual-studio-deployment-project/add-new-reference.png)

1. Oluşturduğunuz web uygulaması projesini seçin.

   ![Başvuru ekleme](./media/create-visual-studio-deployment-project/add-reference.png)

   Bir başvuru ekleyerek, Web uygulaması projesini kaynak grubu projesine bağlarsınız ve bazı özellikleri otomatik olarak ayarlar. Bu özellikleri başvurunun **Özellikler** penceresinde görürsünüz. **Dosya Yolu Ekle** paketin oluşturulduğu yerin yolunu içerir. Klasörü (ExampleApp) ve dosyayı (package.zip) not edin. Uygulamayı dağıtırken parametre olarak ileteceğiniz için bu değerleri bilmeniz gerekir.

   ![Başvuruya bakın](./media/create-visual-studio-deployment-project/see-reference.png)

1. Şablonunuza geri dönün (WebSite.js) ve şablona bir kaynak ekleyin.

    ![Kaynak Ekle](./media/create-visual-studio-deployment-project/add-resource-2.png)

1. Bu kez **Web Apps için Web Dağıtımı**’nı seçin.

    ![Web dağıtımı Ekle](./media/create-visual-studio-deployment-project/add-web-deploy.png)

   Şablonunuzu kaydedin.

1. Şablonunuzda bazı yeni parametreler vardır. Önceki adımda eklenirler. **_ArtifactsLocation** veya **_artifactsLocationSasToken** için değer sağlamanız gerekmez, çünkü bu değerler otomatik olarak oluşturulur. Ancak, klasör ve dosya adını dağıtım paketini içeren yola ayarlamanız gerekir. Bu parametrelerin adları **PackageFolder** ve **packagefilename** ile biter. Adın ilk bölümü, eklediğiniz Web Dağıtımı kaynağının adıdır. Bu makalede, bunlar **Exampleapppackagefolder** ve **Exampleapppackagefilename** olarak adlandırılmaktadır.

   **Website.parameters.js** açın ve bu parametreleri başvuru özelliklerinde gördüğünüz değerlere ayarlayın. **Exampleapppackagefolder** değerini klasörün adına ayarlayın. **Exampleapppackagefilename** değerini ZIP dosyasının adına ayarlayın.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       },
       "ExampleAppPackageFolder": {
         "value": "ExampleApp"
       },
       "ExampleAppPackageFileName": {
         "value": "package.zip"
       }
     }
   }
   ```

## <a name="deploy-code-with-infrastructure"></a>Altyapı ile kod dağıtma

Projeye kod eklediyseniz, dağıtımınız bu kez biraz farklı olur. Dağıtım sırasında, projeniz için yapıtları Kaynak Yöneticisi erişebileceği bir yere aşamalandırın. Yapıtlar bir depolama hesabına hazırlanır.

### <a name="az-module-script"></a>Az Module betiği

Az Module betiğini kullanıyorsanız, şablonunuzda yapmanız gereken küçük bir değişiklik vardır. Bu betik, yapıt konumuna eğik çizgi ekler, ancak şablonunuz bu eğik çizgiyi beklemez. Üzerinde WebSite.jsaçın ve MSDeploy uzantısının özelliklerini bulun. **PackageURI** adlı bir özelliğe sahiptir. Yapıt konumuyla paket klasörü arasındaki eğik çizgiyi kaldırın.

Şu şekilde görünmelidir:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

Önceki örnekte `'/',` **Parametreler (' _artifactsLocation ')** ve **Parametreler (' exampleapppackagefolder ')** arasında bir uyarı yok.

Projeyi yeniden derleyin. Projeyi oluşturmak, dağıtmanız gereken dosyaların hazırlama klasörüne eklendiğinden emin olmanızı sağlar.

Şimdi bir PowerShell konsolu açın ve şunu çalıştırın:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>Azurerd modülü betiği

Azurerd modül betiği için Visual Studio 'Yu kullanın:

1. Yeniden dağıtmak için **Dağıt**' ı ve daha önce dağıttığınız kaynak grubunu seçin.

    ![Projeyi yeniden Dağıt](./media/create-visual-studio-deployment-project/redeploy.png)

1. **Yapıt depolama hesabı** için bu kaynak grubuyla dağıttığınız depolama hesabını seçin.

   ![Web dağıtımını yeniden Dağıt](./media/create-visual-studio-deployment-project/redeploy-web-app.png)

## <a name="view-web-app"></a>Web uygulamasını görüntüle

1. Dağıtım tamamlandıktan sonra portalda web uygulamanızı seçin. Yeni siteye göz atmak için URL’yi seçin.

   ![Siteye gözatamıyorum](./media/create-visual-studio-deployment-project/browse-site.png)

1. Varsayılan ASP.NET uygulamasını başarıyla dağıttığınızdan emin olun.

   ![Dağıtılan uygulamayı göster](./media/create-visual-studio-deployment-project/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>İşlem panosu ekleme

Yalnızca Visual Studio arabirimi aracılığıyla kullanılabilir olan kaynaklarla sınırlı olmazsınız. Şablonunuza özel bir kaynak ekleyerek dağıtımınızı özelleştirebilirsiniz. Kaynak eklemeyi göstermek için dağıttığınız kaynağı yönetmek üzere bir işlem panosu eklersiniz.

1. WebSite.jsdosya üzerinde açın ve depolama hesabı kaynağında, ancak kaynaklar bölümünün kapatılmadan önce aşağıdaki JSON 'u ekleyin `]` .

   ```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "type": "Microsoft.Portal/dashboards",
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Projenizi yeniden dağıtın.

1. Dağıtım tamamlandıktan sonra panonuzu portalda görüntüleyin. **Panoyu** seçin ve dağıttığınız bir tane seçin.

   ![Ekran görüntüsü, örnek özel bir panonun vurgulandığı Pano sayfasını gösterir.](./media/create-visual-studio-deployment-project/view-custom-dashboards.png)

1. Özelleştirilmiş panoyu görürsünüz.

   ![Özel Pano](./media/create-visual-studio-deployment-project/Ops-DemoSiteGroup-dashboard.png)

Panoya erişimi, Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak yönetebilirsiniz. Ayrıca dağıtıldıktan sonra panonun görünümünü özelleştirebilirsiniz. Ancak kaynak grubunuza yeniden dağıttığınızda pano şablonunuzdaki varsayılan durumuna sıfırlanır. Pano oluşturma hakkında daha fazla bilgi için bkz. [Program aracılığıyla Azure Panoları oluşturma](../../azure-portal/azure-portal-dashboards-create-programmatically.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık Azure kaynakları gerekli değilse, kaynak grubunu silerek dağıttığınız kaynakları temizleyin.

1. Azure portal, sol menüden **kaynak grupları** ' nı seçin.

1. Kaynak grubu adını seçin.

1. Üstteki menüden **kaynak grubunu sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, Visual Studio kullanarak şablon oluşturmayı ve dağıtmayı öğrendiniz. Şablon geliştirme hakkında daha fazla bilgi edinmek için bkz. Yeni Başlangıç Öğreticisi serisi:

> [!div class="nextstepaction"]
> [Başlangıç öğretileri](./template-tutorial-create-first-template.md)
