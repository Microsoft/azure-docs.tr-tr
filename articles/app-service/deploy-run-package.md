---
title: Uygulamanızı bir ZIP paketinden çalıştırma
description: Uygulamanın ZIP paketini Atomicity ile dağıtın. ZIP dağıtım sürecinde uygulamanızın davranışının öngörülebilirlik ve güvenilirliğini artırabilirsiniz.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: d3315370342f54091598aa3f77f70f03bda4ad33
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772748"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>Uygulamanızı doğrudan bir ZIP paketinden Azure App Service çalıştırma

[Azure App Service](overview.md), uygulamalarınızı doğrudan BIR dağıtım ZIP paketi dosyasından çalıştırabilirsiniz. Bu makalede, uygulamanızda bu işlevselliğin nasıl etkinleştirileceği gösterilmektedir.

App Service diğer tüm dağıtım yöntemlerinin ortak bir şeyi vardır: dosyalarınız uygulamanızda *D:\home\site\wwwroot* dosyasına dağıtılır (veya Linux uygulamaları için */Home/site/Wwwroot* ). Aynı dizin, çalışma zamanında uygulamanız tarafından kullanıldığından, dosya kilidi çakışmaları nedeniyle dağıtımın başarısız olması ve dosyaların bazıları henüz güncelleştirilmemiş olduğundan uygulamanın tahmin edilemeyecek şekilde davranması mümkündür.

Buna karşılık, doğrudan bir paketten çalıştırdığınızda Paketteki dosyalar *Wwwroot* dizinine kopyalanmaz. Bunun yerine, ZIP paketinin kendisi doğrudan salt okuma *Wwwroot* dizini olarak bağlanır. Doğrudan bir paketten çalıştırmanın çeşitli avantajları vardır:

- Dağıtım ve çalışma zamanı arasındaki dosya kilidi çakışmalarını ortadan kaldırır.
- Dilediğiniz zaman yalnızca tam olarak dağıtılan uygulamaların çalıştırılmasını sağlar.
- , Bir üretim uygulamasına dağıtılabilir (yeniden başlatma ile).
- Azure Resource Manager dağıtımlarının performansını geliştirir.
- Özellikle büyük NPM paket ağaçları olan JavaScript işlevleri için soğuk başlangıç zamanlarını azaltabilir.

> [!NOTE]
> Şu anda yalnızca ZIP paketi dosyaları desteklenir.

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>Paketten çalışmayı etkinleştir

`WEBSITE_RUN_FROM_PACKAGE`Uygulama ayarı bir paketten çalıştırmaya izin vermez. Bunu ayarlamak için, Azure CLı ile aşağıdaki komutu çalıştırın.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` Uygulamanızı yerel bir paketten uygulamanıza çalıştırmanızı sağlar. Ayrıca, [uzak bir paketten da çalıştırabilirsiniz](#run-from-external-url-instead).

## <a name="run-the-package"></a>Paketi çalıştırın

App Service bir paketi çalıştırmanın en kolay yolu Azure CLı [az WebApp Deployment Source config-ZIP](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip) komutıdır. Örnek:

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

`WEBSITE_RUN_FROM_PACKAGE`Uygulama ayarı ayarlandığı için, bu komut paket içeriğini uygulamanızın *D:\home\site\wwwroot* dizinine ayıklamaz. Bunun yerine, ZIP dosyasını *D:\home\data\sitepackages* dizinine yükler ve aynı dizinde, çalışma ZAMANıNDA yüklenecek ZIP paketinin adını içeren bir *packagename.txt* oluşturur. ZIP paketinizi farklı bir şekilde (örneğin, [FTP](deploy-ftp.md)) yüklerseniz, *D:\home\data\sitepackages* dizinini ve *packagename.txt* dosyasını el ile oluşturmanız gerekir.

Komut ayrıca uygulamayı yeniden başlatır. `WEBSITE_RUN_FROM_PACKAGE`Ayarlandığı için, App Service karşıya yüklenen paketi salt okunurdur ve sonra bu bağlı dizinden  doğrudan çalıştırır.

## <a name="run-from-external-url-instead"></a>Bunun yerine dış URL 'den Çalıştır

Ayrıca, Azure Blob depolama gibi bir dış URL 'den paket de çalıştırabilirsiniz. Paket dosyalarını BLOB depolama hesabınıza yüklemek için [Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md) kullanabilirsiniz. App Service çalışma zamanının pakete güvenli bir şekilde erişmesini sağlamak için, [paylaşılan erişim imzası (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) ile özel bir depolama kapsayıcısı kullanmanız gerekir. 

Dosyanızı blob depolamaya yükledikten ve dosya için bir SAS URL 'sine sahip olduktan sonra `WEBSITE_RUN_FROM_PACKAGE` uygulama AYARıNı URL olarak ayarlayın. Aşağıdaki örnek, Azure CLı kullanarak bunu yapar:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

Aynı ada sahip güncelleştirilmiş bir paketi BLOB depolama alanına yayımlarsanız, güncelleştirilmiş paketin App Service ' ye yüklenmesi için uygulamanızı yeniden başlatmanız gerekir.

## <a name="troubleshooting"></a>Sorun giderme

- Doğrudan bir paketten çalıştırmak `wwwroot` salt okunurdur. Uygulamanız, bu dizine dosya yazmayı denediğinde bir hata alır.
- TAR ve GZIP biçimleri desteklenmez.
- ZIP dosyası en fazla 1 GB olabilir
- Bu özellik [Yerel önbellek](overview-local-cache.md)ile uyumlu değil.
- İyileştirilmiş soğuk başlangıç performansı için yerel ZIP seçeneğini ( `WEBSITE_RUN_FROM_PACKAGE` = 1) kullanın.

## <a name="more-resources"></a>Diğer kaynaklar

- [Azure App Service için sürekli dağıtım](deploy-continuous-deployment.md)
- [Kodu ZIP veya WAR dosyası ile dağıtma](deploy-zip.md)
