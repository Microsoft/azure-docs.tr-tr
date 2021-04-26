---
title: MongoDB için Azure Cosmos DB API 'SI ile angular uygulama kullanıcı arabirimi oluşturma (part3)
description: MongoDB için kullandığınız API'lerle Azure Cosmos DB üzerinde Angular ve Node ile bir MongoDB uygulaması oluşturma öğreticisi dizisinin 3. bölümü.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 181af5cf26d19a9f51e8d456e777badf7efa224d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097831"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---build-the-ui-with-angular"></a>MongoDB için Azure Cosmos DB API 'SI ile angular uygulaması oluşturma-kullanıcı arabirimini angular ile derleme
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Bu çok parçalı öğreticide, Express ve angular ile Node.js yazılmış yeni bir uygulama oluşturma ve ardından bunu [MongoDB için Cosmos DB API 'siyle yapılandırılmış Cosmos hesabınıza](mongodb-introduction.md)bağlama işlemlerinin nasıl yapılacağı gösterilmektedir.

Öğreticinin 3. bölümünde [2. bölümdeki](tutorial-develop-mongodb-nodejs-part2.md) konular genişletilir ve aşağıdaki görevler yer alır:

> [!div class="checklist"]
> * Angular Kullanıcı Arabirimini oluşturma
> * Görünümü ayarlamak için CSS kullanma
> * Uygulamayı yerel olarak test etme

## <a name="video-walkthrough"></a>Görüntülü kılavuz

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>Önkoşullar

Öğreticinin bu bölümüne başlamadan önce öğreticinin [2. bölümündeki](tutorial-develop-mongodb-nodejs-part2.md) adımları tamamladığınızdan emin olun.

> [!TIP]
> Bu öğretici, uygulamanızı adım adım oluşturmanızı sağlayacak adımlarla size yol gösterir. Tamamlanmış projeyi indirmek isterseniz, tamamlanmış uygulamayı github'daki [angular-cosmosdb deposundan](https://github.com/Azure-Samples/angular-cosmosdb) alabilirsiniz.

## <a name="build-the-ui"></a>Kullanıcı Arabirimini oluşturma

1. Visual Studio Code, :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png"::: düğüm uygulamasını durdurmak Için Durdur düğmesine tıklayın. 

2. Windows Komut İstemi veya Mac Terminal penceresinde bir heroes bileşeni oluşturmak için aşağıdaki komutu girin. Bu kodda g=oluştur, c=bileşen, heroes=bileşen adıdır ve düz dosya yapısı kullanılmıştır (--flat). Alt klasör oluşturulmamıştır.

    ```
    ng g c heroes --flat 
    ```

    Terminal penceresi yeni bileşenlerin onayını görüntüler.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/install-heros-component.png" alt-text="Hero bileşenini yükleme":::

    Oluşturulan ve güncelleştirilen dosyalara bir göz atalım. 

3. Visual Studio Code'daki **Explorer** bölmesinde, yeni **src\app** klasörüne gidin ve uygulama klasörünün içinde oluşturulan yeni **heroes.component.ts** dosyasını açın. Bu TypeScript bileşen dosyası önceki komut tarafından oluşturulmuştur.

    > [!TIP]
    > Visual Studio Code’da uygulama klasörü görünmüyorsa, Mac bilgisayarlarda CMD + SHIFT P tuşlarına, Windows çalıştıran bilgisayarlarda Ctrl + Shift + P tuşlarına basarak Komut Paletini açın ve ardından *Reload Window* yazarak sistem değişikliğini alın.

4. Aynı klasörde **app.module.ts** dosyasını açın. 5. ve 10. satırdaki bildirimlere `HeroesComponent` bileşeninin eklendiğine dikkat edin.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png" alt-text="app-module.ts dosyasını açın":::

5. **heroes.component.html** dosyasına dönün ve bu kodları kopyalayın. `<div>`, tüm sayfaya yönelik kapsayıcıdır. Kapsayıcı içinde, oluşturmamız gereken hero'ların bir listesi vardır. Kullanıcı arabiriminde bunları tıklayarak seçebilir, düzenleyebilir veya silebilirsiniz. HTML'de hangisinin seçili olduğunu anlayabilmeniz için bazı stil farklılıkları vardır. Ayrıca yeni hero’yu eklemenizi veya mevcut bir hero’yu düzenlemenizi sağlayan bir düzenleme alanı bulunur. 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

7. HTML’yi oluşturduğumuza göre artık şablonla etkileşim kurmasını sağlamak için **heroes.component.ts** dosyasına eklememiz gerekiyor. Aşağıdaki kod, şablonu bileşen dosyanıza ekler. Bazı hero’ları alan ve tüm verileri almak için hero hizmet bileşenini başlatan bir oluşturucu eklendi. Bu kod ayrıca, kullanıcı arabirimindeki olayları işleyebilmek için gerekli tüm yöntemleri ekler. **heroes.component.ts**’deki mevcut kodun üzerine aşağıdaki kodu kopyalayabilirsiniz. Hero ve HeroService alanlarında hataları görmeyi bekleyebilirsiniz çünkü bunlara karşılık gelen bileşenler henüz içeri aktarılmamıştır; bu hataları sonraki bölümde düzelteceksiniz. 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html',
      styleUrls: ['./heroes.component.scss']
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

8. **Explorer**’da **app/app.module.ts** dosyasını açın ve `FormsModule`’e yönelik içeri aktarma eklemek için imports bölümünü güncelleştirin. Import bölümü artık şöyle görünmelidir:

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

9. **app/app.module.ts** dosyasına 3. satırdaki yeni FormsModule modülüne yönelik içeri aktarma ekleyin. 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>Görünümü ayarlamak için CSS kullanma

1. Explorer sekmesinden, **src/styles.scss** dosyasını açın.

2. Aşağıdaki kodu **styles.scss** dosyasına kopyalayarak dosyanın mevcut içeriğini değiştirin.

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. Dosyayı kaydedin. 

## <a name="display-the-component"></a>Bileşeni görüntüleme

Şu an bileşenimiz var, peki ekranda görünmesini nasıl sağlarız? **app.component.ts** dosyasındaki varsayılan bileşenleri değiştirelim.

1. Explorer bölmesinde **/app/app.component.ts**'yi açın, başlığı Heroes olarak değiştirin, ardından **heroes.components.ts** (app-heroes) dosyasında oluşturduğumuz bileşenin adını, yeni bileşene başvurmak üzere ekleyin. Dosyanın içeriği şimdi aşağıdaki gibi görünmelidir: 

    ```ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
      template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `
    })
    export class AppComponent {
      title = 'app';
    }

    ```

2. **heroes.components.ts** dosyasında Hero bileşeni gibi başvurduğumuz diğer bileşenler de bulunduğu için bunu da oluşturmamız gerekir. Angular CLI komut isteminde g=oluştur, cl=sınıf ve hero=sınıf adı olmak üzere bir hero modeli ve **hero.ts** adlı bir dosya oluşturmak için aşağıdaki komutu kullanın.

    ```bash
    ng g cl hero
    ```

3. Explorer bölmesinden **src\app\hero.ts** dosyasını açın. **hero.ts** dosyasının içeriğini, bir kimlik, ad ve söze sahip bir Hero sınıfı ekleyen aşağıdaki kodla değiştirin.

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

4. **heroes.components.ts** dosyasına geri dönün ve `selectedHero: Hero;` satırındaki (10. satır) `Hero`’nun altında kırmızı bir çizgi olduğuna dikkat edin. 

5. `Hero` terimine sol tıkladığınızda Visual Studio, kod bloğunun sol tarafında bir ampul simgesi görüntüler. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png" alt-text="Visual Studio Code'da Ampul":::

6. Ampule ve ardından **Import Hero from "/app/hero".** ("/uygulama/hero"dan Hero İçeri Aktar) seçeneğine tıklayın. veya **Import Hero from "./hero".** ("./hero"dan Hero İçeri Aktar.) seçeneğine tıklayın. (İleti, kurulumunuza bağlı olarak değişebilir)

    2. satırda yeni bir kod satırı görünür. 2. satır /app/hero’ya başvuruyorsa, yerel klasördeki (./hero) hero dosyasına başvurması için bu satırı değiştirin. 2. satır şu şekilde görünmelidir:

   ```
   import { Hero } from "./hero";
   ``` 

    Bu kısım, modeli belirler ancak hizmeti oluşturmamız gerekir.

## <a name="create-the-service"></a>Hizmeti Oluşturma

1. Angular CLI komut isteminde g=oluştur, s=hizmet, hero=sınıf adı ve m=app.module’e koy olmak üzere **app.module.ts** dosyasında bir hero hizmeti oluşturmak için aşağıdaki komutu girin.

    ```bash
    ng g s hero -m app.module
    ```

2. Visual Studio Code’da **heroes.components.ts** dosyasına geri dönün. `constructor(private heroService: HeroService) {}` satırında (13. satır), `HeroService` altında kırmızı bir çizgi vardır. `HeroService`’e tıkladığınızda kod bloğunun sol tarafında bir ampul görünür. Ampule ve **Import HeroService from "./hero.service ".** ("./hero.service "den İçeri Aktar.) seçeneğine veya **Import HeroService from "/app/hero.service ".** ("/uygulama/hero.service"den HeroService İçeri Aktar) seçeneğine tıklayın.

    Ampule tıkladığınızda 2. satıra yeni bir kod satırı eklenir. 2. satır/App/heroman hizmet klasörüne başvuruyorsa, yerel klasördeki (./HEROM hizmeti) Hero dosyasına başvurması için bu dosyayı değiştirin. 2. satır şu şekilde görünmelidir:
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

3. Visual Studio Code'da **hero.service.ts** dosyasını açın ve dosyanın içeriğinin yerine aşağıdaki kodu kopyalayın.

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    Bu kod, sağlamanız gereken bir modül olan ve Angular’ın en yeni sürümünü sunduğu HttpClient'ı kullanır. Şimdi bunu yapacağız.

4. Visual Studio Code'da **app.module.ts** dosyasını açın ve içeri aktarma bölümünü HttpClientModule’ü içerecek şekilde güncelleştirerek HttpClientModule’ü içeri aktarın.

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

5. **app.module.ts** dosyasında içeri aktarma listesine HttpClientModule içeri aktarma deyimini ekleyin.

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

6. Visual Studio Code’daki tüm dosyaları kaydedin.

## <a name="build-the-app"></a>Uygulamayı oluşturma

1. Angular uygulamasını oluşturmak için komut isteminde aşağıdaki komutu girin. 

    ```bash
    ng b
    ``` 

    Bir sorun oluşursa, terminal penceresi düzeltilecek dosyalarla ilgili bilgileri görüntüler. Oluşturma işlemi tamamlandığında, yeni dosyalar **dist** klasörüne gider. Dilerseniz **dist** klasöründeki yeni dosyaları gözden geçirebilirsiniz.

    Şimdi uygulamayı çalıştıralım.

2. Visual Studio Code ' de, sol taraftaki **Hata Ayıkla** düğmesine tıklayın :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: ve ardından **hata ayıklamayı Başlat** düğmesine tıklayın :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png"::: .

3. Şimdi bir İnternet tarayıcısı açın ve **localhost:3000** adresine giderek yerel olarak çalışan uygulamayı görüntüleyin.

     :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png" alt-text="Yerel olarak çalışan Hero uygulaması":::

## <a name="next-steps"></a>Sonraki adımlar

Öğreticinin bu bölümünde aşağıdakileri yaptınız:

> [!div class="checklist"]
> * Angular kullanıcı arabirimi oluşturuldu
> * Uygulama yerel olarak test edildi

Azure Cosmos DB hesabı oluşturmak için öğreticinin sonraki bölümüne geçebilirsiniz.

> [!div class="nextstepaction"]
> [Azure CLI’yı kullanarak Azure Cosmos DB hesabı oluşturma](tutorial-develop-mongodb-nodejs-part4.md)
