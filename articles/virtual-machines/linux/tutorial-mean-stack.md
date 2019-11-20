---
title: Öğretici-Azure 'da Linux sanal makinesinde ortalama bir yığın oluşturma
description: Bu öğreticide, Azure’da bir Linux sanal makinesi üzerinde nasıl MongoDB, Express, AngularJS ve Node.js (MEAN) yığını oluşturulacağını öğreneceksiniz.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 50ccf0d6db5a50f7dd89732bc870ced6bcf1c2d7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034395"
---
# <a name="tutorial-create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-virtual-machine-in-azure"></a>Öğretici: Azure’da bir Linux sanal makinesi üzerinde MongoDB, Express, AngularJS ve Node.js (MEAN) yığını oluşturma

Bu öğreticide, Azure’da bir Linux sanal makinesi (VM) üzerinde MongoDB, Express, AngularJS ve Node.js (MEAN) yığınının nasıl uygulanacağı gösterilmektedir. Oluşturduğunuz MEAN yığını bir veritabanına kitap eklenmesine, veritabanındaki kitapların silinmesine ve listelenmesine olanak sağlar. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Linux VM oluşturma
> * Node.js yükleme
> * MongoDB yükleme ve sunucuyu ayarlama
> * Express’i yükleme ve sunucuya rotalar ayarlama
> * AngularJS ile rotalara erişme
> * Uygulamayı çalıştırma

Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Linux VM oluşturma

[az group create](https://docs.microsoft.com/cli/azure/group) komutuyla bir kaynak grubu oluşturun ve [az vm create](https://docs.microsoft.com/cli/azure/vm) komutuyla bir Linux sanal makinesi oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnekte Azure CLI kullanılarak *eastus* konumunda *myResourceGroupMEAN* adlı bir kaynak grubu oluşturulur. Varsayılan anahtar konumunda henüz yoksa SSH anahtarları ile *myVM* adlı bir sanal makine oluşturulur. Belirli bir anahtar kümesini kullanmak için --ssh-key-value seçeneğini kullanın.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

Sanal makine oluşturulduğunda Azure CLI, aşağıdaki örneğe benzer bilgiler gösterir: 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
`publicIpAddress` değerini not edin. Bu adres, VM’ye erişmek için kullanılır.

Sanal makine ile bir SSH oturumu oluşturmak için aşağıdaki komutu kullanın. Doğru genel IP adresini kullandığınızdan emin olun. Yukarıdaki örneğimizde IP adresi 13.72.77.9 şeklindedir.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Node.js yükleme

[Node.js](https://nodejs.org/en/), Chrome’un V8 JavaScript altyapısında derlenen bir JavaScript çalışma zamanıdır. Express rotalarını ve AngularJS denetleyicilerini ayarlamak için bu öğreticide Node.js kullanılmaktadır.

Sanal makinede, SSH ile açtığınız bash kabuğunu kullanarak Node.js yükleyin.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>MongoDB yükleme ve sunucuyu ayarlama
[MongoDB](https://www.mongodb.com), verileri JSON benzeri esnek belgelerde depolar. Bir veritabanındaki alanlar, belgeden belgeye değişiklik gösterebilir ve veri yapısı zaman içinde değiştirilebilir. Örnek uygulamamız için MongoDB anahtarına kitap adını, isbn numarasını, yazarı ve sayfa sayısını içeren kitap kayıtları ekliyoruz. 

1. Sanal makinede, SSH ile açtığınız bash kabuğunu kullanarak MongoDB anahtarını ayarlayın.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Paket yöneticisini anahtarla güncelleştirin.
  
    ```bash
    sudo apt-get update
    ```

3. MongoDB’yi yükleyin.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Sunucuyu başlatın.

    ```bash
    sudo service mongodb start
    ```

5. Ayrıca sunucuya yönelik isteklerde geçirilen JSON’u işlememize yardımcı olması için [body-parser](https://www.npmjs.com/package/body-parser-json) paketini de yüklememiz gerekir.

    Npm paket yöneticisini yükleyin.

    ```bash
    sudo apt-get install npm
    ```

    Gövde ayrıştırıcı paketini yükleyin.
    
    ```bash
    sudo npm install body-parser
    ```

6. *Kitaplar* adlı bir klasör oluşturun ve web sunucusu yapılandırmasını içeren *server.js* adlı bir dosyayı bu klasöre ekleyin.

    ```javascript
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Express’i yükleme ve sunucuya rotalar ayarlama

[Express](https://expressjs.com), web uygulamaları ve mobil uygulamalar için özellikler sağlayan minimal ve esnek bir Node.js web uygulaması çerçevesidir. Bu öğreticide Express, MongoDB veritabanına/veritabanından kitap bilgilerini geçirmek için kullanılmaktadır. [Mongoose](https://mongoosejs.com), uygulama verilerinizi modellemek için kolay ve şema temelli bir çözüm sağlar. Bu öğreticide Mongoose, veritabanına yönelik bir kitap şeması sağlamak için kullanılmaktadır.

1. Express’i ve Mongoose’u yükleyin.

    ```bash
    sudo npm install express mongoose
    ```

2. *Kitaplar* klasöründe *uygulamalar* adlı bir klasör oluşturun ve express rotaları tanımlanmış şekilde *routes.js* adlı bir dosya ekleyin.

    ```javascript
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. *uygulamalar* klasöründe *modeller* adlı bir klasör oluşturun ve kitap modeli yapılandırması tanımlanmış şekilde *book.js* adlı bir dosya ekleyin.  

    ```javascript
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>AngularJS ile rotalara erişme

[AngularJS](https://angularjs.org), web uygulamalarınızda dinamik görünümler oluşturmaya yönelik bir web çerçevesi sağlar. Bu öğreticide, Express ile web sayfamıza bağlanmak ve kitap veritabanımızda eylemler gerçekleştirmek için AngularJS kullanırız.

1. Dizini *Kitaplar* (`cd ../..`) olarak tekrar değiştirin, sonra *genel* adlı bir klasör oluşturun ve denetleyici yapılandırması tanımlanmış şekilde *script.js* adlı bir dosya ekleyin.

    ```javascript
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. *genel* klasöründe, web sayfası tanımlanmış şekilde *index.html* adlı bir dosya oluşturun.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>Uygulamayı çalıştırma

1. Dizini *Kitaplar* (`cd ..`) olarak tekrar değiştirin ve şu komutu çalıştırarak sunucuyu başlatın:

    ```bash
    nodejs server.js
    ```

2. Bir web tarayıcısında, sanal makine için kaydettiğiniz adresi açın. Örneğin, *http:\//13.72.77.9:3300*. Aşağıdaki sayfaya benzer bir şey görmeniz gerekir:

    ![Kitap kaydı](media/tutorial-mean/meanstack-init.png)

3. Metin kutularına veri girin ve **Ekle**’ye tıklayın. Örneğin:

    ![Kitap kaydı ekleme](media/tutorial-mean/meanstack-add.png)

4. Sayfayı yeniledikten sonra aşağıdaki sayfaya benzer bir şey görmeniz gerekir:

    ![Kitap kayıtlarını listeleme](media/tutorial-mean/meanstack-list.png)

5. **Sil**’e tıklayıp veritabanından kitap kaydını kaldırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Linux sanal makinesi üzerinde MEAN yığınını kullanarak kitap kayıtlarını takip eden bir web uygulaması oluşturdunuz. Şunları öğrendiniz:

> [!div class="checklist"]
> * Linux VM oluşturma
> * Node.js yükleme
> * MongoDB yükleme ve sunucuyu ayarlama
> * Express’i yükleme ve sunucuya rotalar ayarlama
> * AngularJS ile rotalara erişme
> * Uygulamayı çalıştırma

SSL sertifikalarını kullanarak güvenli web sunucularının güvenliğini nasıl sağlayabileceğinizi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [SSL ile web sunucusunun güvenliğini sağlama](tutorial-secure-web-server.md)
