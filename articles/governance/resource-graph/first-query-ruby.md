---
title: 'Hızlı başlangıç: ilk Ruby sorgunuz'
description: Bu hızlı başlangıçta, Ruby için kaynak grafiği GEFA 'yı etkinleştirmek ve ilk sorgunuzu çalıştırmak için adımları izleyin.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 41769359eaa1f930d34b0a182f35da38a6f5109d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920049"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>Hızlı başlangıç: Ruby kullanarak ilk kaynak grafik sorgunuzu çalıştırma

Azure Kaynak Grafiği 'ni kullanmanın ilk adımı, Ruby için gereken Gems 'lerin yüklenip yüklenmediğini denetmektir. Bu hızlı başlangıçta, Ruby yüklemenize Gems ekleme işlemi adım adım gösterilmektedir.

Bu işlemin sonunda, Ruby yüklemenize Gems 'yi eklemiş ve ilk kaynak Graf sorgunuzu çalıştıracaksınız.

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.
- _ClientID_ ve _ClientSecret_ dahil olmak üzere bir Azure hizmet sorumlusu.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Kaynak Grafiği projesi oluşturma

Ruby 'nin Azure Kaynak Grafiği 'ni sorgulamasını sağlamak için, geI 'nin öğesine eklenmesi gerekir `Gemfile` . Bu ged, [Azure Cloud Shell](https://shell.azure.com), [Windows 10 ' da Bash](/windows/wsl/install-win10)veya yerel olarak yüklenmiş olarak da dahil olmak üzere Ruby 'nin kullanılabileceği her yerde çalışmaktadır.

1. En son Ruby 'nin yüklü olup olmadığını denetleyin (en azından **2.7.1**). Henüz yüklenmemişse, [Ruby-lang.org](https://www.ruby-lang.org/en/downloads/)adresinden indirin.

1. Seçtiğiniz Ruby ortamınızda yeni bir proje klasöründe bir paket başlatın:

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. `Gemfile`Azure Kaynak Grafiği için gereken Gems ile hesabınızı güncelleştirin. Güncelleştirilmiş dosya şu örneğe benzemelidir:

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. Proje klasöründen öğesini çalıştırın `bundle install` . İle yüklenen Gems 'leri onaylayın `bundle list` .

1. Aynı proje klasöründe `argQuery.rb` aşağıdaki kodla oluşturun ve güncelleştirilmiş dosyayı kaydedin:

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>İlk Kaynak Grafiği sorgunuzu çalıştırma

Ruby betiği kaydedildiğinden ve kullanıma hazır olan bir basit kaynak grafik sorgusunu denemenize zaman atalım. Sorgu, ilk beş Azure kaynağını her bir kaynağın **adı** ve **kaynak türü** ile döndürür.

Her çağrıda `argQuery` , kendi değerlerinizle değiştirmeniz gereken kullanılan değişkenler vardır:

- `{tenantId}` -Kiracı KIMLIĞINIZLE değiştirin
- `{clientId}` -Hizmet sorumlunun istemci KIMLIĞIYLE değiştirin
- `{clientSecret}` -Hizmet sorumlunun istemci gizli anahtarı ile değiştirin
- `{subscriptionId}` - Abonelik kimliğinizle değiştirin

1. Dizinleri ve dosyalarını oluşturduğunuz proje klasörüyle değiştirin `Gemfile` `argClient.rb` .

1. Gems ve yöntemini kullanarak ilk Azure Kaynak Grafiği sorgunuzu çalıştırın `resources` :

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Bu sorgu örneği, `order by` gibi bir sıralama değiştirici sağlamadığı için, bu sorgunun birden çok kez çalıştırılması muhtemelen istek başına farklı bir kaynak kümesi sunacaktır.

1. Son parametreyi olarak değiştirin `argQuery.rb` ve sorguyu Name özelliği olarak değiştirin `order by` : 

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > İlk sorguda olduğu gibi, bu sorguyu birden çok kez çalıştırmak, muhtemelen istek başına farklı bir kaynak kümesi sunacaktır. Sorgu komutlarının düzeni önemlidir. Bu örnekte `order by`, `limit`’den sonra gelmektedir. Bu komut sırası ilk olarak sorgu sonuçlarını kısıtlar ve sonra sıralar.

1. Son parametreyi olarak değiştirin `argQuery.rb` ve sorguyu ilk olarak `order by` **ad** özelliğine, sonra da `limit` en üstteki beş sonuca değiştirin:

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Son sorgu birkaç kez çalıştırıldığında, ortamınızdaki hiçbir şeyin değişmediği kabul edildiğinde döndürülen sonuçlar tutarlı ve **ad** özelliğine göre sıralanır, ancak yine de en üstteki beş sonuçla sınırlıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yüklü Gems 'leri Ruby ortamınızdan kaldırmak isterseniz, aşağıdaki komutu kullanarak bunu yapabilirsiniz:

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> GEB `azure_mgmt_resourcegraph` gibi bağımlılıklar `ms_rest` ve `ms_rest_azure` ortamınıza bağlı olarak da yüklenmiş olabilir. Artık gerekli değilse, bu Gems 'leri de kaldırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, kaynak grafiği Gems 'yi Ruby ortamınıza eklediniz ve ilk sorgunuzu çalıştırdık. Kaynak grafik dili hakkında daha fazla bilgi edinmek için sorgu dili ayrıntıları sayfasına ilerleyin.

> [!div class="nextstepaction"]
> [Sorgu dili hakkında daha fazla bilgi alın](./concepts/query-language.md)
