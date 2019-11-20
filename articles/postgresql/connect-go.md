---
title: Go dilini kullanarak PostgreSQL için Azure veritabanı 'na bağlanma-tek sunucu
description: Bu hızlı başlangıçta, PostgreSQL için Azure veritabanı 'na bağlanmak ve bu verileri sorgulamak için kullanabileceğiniz bir go programlama dili örneği sunulmaktadır-tek sunucu.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 645d34961fb735542729091719dd55c42436db95
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244487"
---
# <a name="azure-database-for-postgresql---single-server-use-go-language-to-connect-and-query-data"></a>PostgreSQL için Azure veritabanı-tek sunucu: bağlanmak ve veri sorgulamak için go dilini kullanın
Bu hızlı başlangıçta, [Go](https://golang.org/) dilinde (golang) yazılan kod kullanılarak PostgreSQL Için Azure veritabanı 'na nasıl bağlanabileceği gösterilmektedir. Veritabanındaki verileri sorgulamak, eklemek, güncelleştirmek ve silmek için SQL deyimlerinin nasıl kullanılacağını gösterir. Bu makalede, Go kullanarak geliştirmeyle ilgili bilgi sahibi olduğunuz ve PostgreSQL için Azure veritabanı ile çalışmaya yeni başladığınız varsayılır.

## <a name="prerequisites"></a>Prerequisites
Bu hızlı başlangıçta başlangıç noktası olarak bu kılavuzlardan birinde oluşturulan kaynaklar kullanılmaktadır:
- [DB oluşturma-Portal](quickstart-create-server-database-portal.md)
- [DB oluşturma-Azure CLı](quickstart-create-server-database-azure-cli.md)

## <a name="install-go-and-pq-connector"></a>Go ve PQ bağlayıcısını Install
Kendi makinenize [Go](https://golang.org/doc/install) ve [saf go Postgres sürücüsünü (PQ)](https://github.com/lib/pq) yükleme. Platformunuza bağlı olarak, uygun adımları izleyin:

### <a name="windows"></a>Windows
1. [Yükleme yönergelerine](https://golang.org/doc/install)göre Microsoft Windows için [Yükle](https://golang.org/dl/) ve Yükle ' ye gidin.
2. Başlat menüsünden komut istemi ' ni başlatın.
3. Projeniz için `mkdir  %USERPROFILE%\go\src\postgresqlgo` gibi bir klasör oluşturun.
4. Dizini, `cd %USERPROFILE%\go\src\postgresqlgo` gibi proje klasörüyle değiştirin.
5. GOPATH için ortam değişkenini, kaynak kodu dizinini işaret etmek üzere ayarlayın. `set GOPATH=%USERPROFILE%\go`.
6. @No__t-1 komutunu çalıştırarak [saf go Postgres sürücüsünü (PQ)](https://github.com/lib/pq) yükleme.

   Özet ' te, git ' i yükledikten sonra komut isteminde şu komutları çalıştırın:
   ```cmd
   mkdir  %USERPROFILE%\go\src\postgresqlgo
   cd %USERPROFILE%\go\src\postgresqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/lib/pq
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Bash kabuğunu başlatın. 
2. @No__t-0 çalıştırarak git ' i çalıştırın.
3. Ana dizininizdeki `mkdir -p ~/go/src/postgresqlgo/` gibi projeniz için bir klasör oluşturun.
4. Dizini, `cd ~/go/src/postgresqlgo/` gibi klasöre değiştirin.
5. GOPATH ortam değişkenini, geçerli giriş dizininizin go klasörü gibi geçerli bir kaynak dizine işaret etmek üzere ayarlayın. Bash kabuğunda, geçerli kabuk oturumunun GOPATH olarak go dizinini eklemek için `export GOPATH=~/go` ' ı çalıştırın.
6. @No__t-1 komutunu çalıştırarak [saf go Postgres sürücüsünü (PQ)](https://github.com/lib/pq) yükleme.

   Özet bölümünde şu Bash komutlarını çalıştırın:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

### <a name="apple-macos"></a>Apple macOS
1. Platformunuzla eşleşen [yükleme yönergelerine](https://golang.org/doc/install) göre go 'yu indirip yükleyin. 
2. Bash kabuğunu başlatın. 
3. Ana dizininizdeki `mkdir -p ~/go/src/postgresqlgo/` gibi projeniz için bir klasör oluşturun.
4. Dizini, `cd ~/go/src/postgresqlgo/` gibi klasöre değiştirin.
5. GOPATH ortam değişkenini, geçerli giriş dizininizin go klasörü gibi geçerli bir kaynak dizine işaret etmek üzere ayarlayın. Bash kabuğunda, geçerli kabuk oturumunun GOPATH olarak go dizinini eklemek için `export GOPATH=~/go` ' ı çalıştırın.
6. @No__t-1 komutunu çalıştırarak [saf go Postgres sürücüsünü (PQ)](https://github.com/lib/pq) yükleme.

   Özet ' te, git ' i yükledikten sonra şu Bash komutlarını çalıştırın:
   ```bash
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

## <a name="get-connection-information"></a>Bağlantı bilgilerini al
PostgreSQL için Azure veritabanı 'na bağlanmak üzere gereken bağlantı bilgilerini alın. Tam sunucu adı ve oturum açma kimlik bilgileri gerekir.

1. [Azure Portal](https://portal.azure.com/)oturum açın.
2. Azure Portal sol taraftaki menüden **tüm kaynaklar**' a tıklayın ve oluşturduğunuz sunucuyu (örneğin, **demosunucum**) arayın.
3. Sunucu adına tıklayın.
4. Sunucunun **genel bakış** panelinden **sunucu adı** ve **Sunucu Yöneticisi oturum açma adı**' nı bir yere göz atın. Parolanızı unutursanız, bu panelden parolayı da sıfırlayabilirsiniz.
 @no__t-PostgreSQL için Azure veritabanı sunucu adı @ no__t-1

## <a name="build-and-run-go-code"></a>Go kodu oluştur ve Çalıştır 
1. Golang kodunu yazmak için, Microsoft Windows, [vi](https://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) veya [nano](https://www.nano-editor.org/) in Ubuntu 'Da Not defteri veya MacOS 'ta TEXTEDIT gibi bir düz metin düzenleyici kullanabilirsiniz. Daha zengin bir etkileşimli geliştirme ortamını tercih ediyorsanız (IDE), JetBrains, Microsoft veya [atom](https://atom.io/)tarafından [Visual Studio Code](https://code.visualstudio.com/) için [golantıd](https://www.jetbrains.com/go/) 'yi deneyin.
2. Aşağıdaki bölümlerden Golang kodunu metin dosyalarına yapıştırın ve Windows yolu `%USERPROFILE%\go\src\postgresqlgo\createtable.go` veya Linux yolu `~/go/src/postgresqlgo/createtable.go` gibi @no__t -0. go dosya uzantısıyla proje klasörünüze kaydedin.
3. Koddaki `HOST`, `DATABASE`, `USER` ve `PASSWORD` sabitlerini bulun ve örnek değerleri kendi değerlerinizle değiştirin.  
4. Komut istemi veya bash kabuğunu başlatın. Dizini proje klasörünüze değiştirin. Örneğin, Windows `cd %USERPROFILE%\go\src\postgresqlgo\`. Linux `cd ~/go/src/postgresqlgo/`. Belirtilen IDE ortamlarından bazıları Kabuk komutlarına gerek duymadan hata ayıklama ve çalışma zamanı özellikleri sunar.
5. Uygulamayı derlemek ve çalıştırmak için `go run createtable.go` komutunu yazarak kodu çalıştırın. 
6. Alternatif olarak, kodu yerel bir uygulamada derlemek için `go build createtable.go`, ardından uygulamayı çalıştırmak için `createtable.exe` ' i başlatın.

## <a name="connect-and-create-a-table"></a>Bağlama ve tablo oluşturma
**Create Table** SQL deyimini kullanarak bağlamak ve tablo oluşturmak için aşağıdaki kodu kullanın ve ardından tabloya satır eklemek için SQL deyimlerine **ekleyin** .

Kod üç paketi içeri aktarır: [SQL paketi](https://golang.org/pkg/database/sql/), PostgreSQL sunucusuyla iletişim kuracak sürücü olarak [PQ paketi](https://godoc.org/github.com/lib/pq) ve komut satırında yazdırılan girdi ve çıktı için [FMT paketi](https://golang.org/pkg/fmt/) .

Kod, SQL yöntemini çağırır [. ](https://godoc.org/github.com/lib/pq#Open)PostgreSQL Için Azure veritabanı veritabanına bağlanmak üzere () öğesini açın ve yöntemi DB kullanarak bağlantıyı kontrol edin [. Ping ()](https://golang.org/pkg/database/sql/#DB.Ping). Bir [veritabanı tanıtıcısı](https://golang.org/pkg/database/sql/#DB) , veritabanı sunucusu için bağlantı havuzunu tutan şekilde kullanılır. Kod, birden çok SQL komutu çalıştırmak için [Exec ()](https://golang.org/pkg/database/sql/#DB.Exec) yöntemini birkaç kez çağırır. Özel checkError () yöntemi her seferinde bir hata oluşup olmadığını ve bir hata meydana gelir.

@No__t-0, `DATABASE`, `USER` ve `PASSWORD` parametrelerini kendi değerlerinizle değiştirin. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

## <a name="read-data"></a>Verileri oku
Bir **Select** SQL ifadesini kullanarak bağlanmak ve verileri okumak için aşağıdaki kodu kullanın. 

Kod üç paketi içeri aktarır: [SQL paketi](https://golang.org/pkg/database/sql/), PostgreSQL sunucusuyla iletişim kuracak sürücü olarak [PQ paketi](https://godoc.org/github.com/lib/pq) ve komut satırında yazdırılan girdi ve çıktı için [FMT paketi](https://golang.org/pkg/fmt/) .

Kod, SQL yöntemini çağırır [. ](https://godoc.org/github.com/lib/pq#Open)PostgreSQL Için Azure veritabanı veritabanına bağlanmak üzere () öğesini açın ve yöntemi DB kullanarak bağlantıyı kontrol edin [. Ping ()](https://golang.org/pkg/database/sql/#DB.Ping). Bir [veritabanı tanıtıcısı](https://golang.org/pkg/database/sql/#DB) , veritabanı sunucusu için bağlantı havuzunu tutan şekilde kullanılır. SELECT sorgusu, yöntem DB çağırarak çalıştırılır [. Sorgu ()](https://golang.org/pkg/database/sql/#DB.Query)ve sonuç satırları [satır](https://golang.org/pkg/database/sql/#Rows)türünde bir değişkende tutulur. Kod, geçerli satırdaki sütun verisi değerlerini Yöntem satırları kullanarak okur [. ](https://golang.org/pkg/database/sql/#Rows.Scan)Yineleyici satırlarını kullanarak satırlar üzerinde tarama () ve döngüler [. Next ()](https://golang.org/pkg/database/sql/#Rows.Next) daha fazla satır yok. Her bir satırın sütun değerleri konsola yazdırılır. Her seferinde hata oluşup olmadığını denetlemek için bir özel checkError () yöntemi kullanılır ve bir hata oluşursa, çıkış yapmak üzere panik.

@No__t-0, `DATABASE`, `USER` ve `PASSWORD` parametrelerini kendi değerlerinizle değiştirin. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)
    defer rows.Close()

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

## <a name="update-data"></a>Verileri güncelleştirme
Bir **Update** SQL ifadesini kullanarak bağlanmak ve verileri güncelleştirmek için aşağıdaki kodu kullanın.

Kod üç paketi içeri aktarır: [SQL paketi](https://golang.org/pkg/database/sql/), Postgres sunucusuyla iletişim kuracak sürücü olarak [PQ paketi](https://godoc.org/github.com/lib/pq) ve komut satırında yazdırılan girdi ve çıktı için [FMT paketi](https://golang.org/pkg/fmt/) .

Kod, SQL yöntemini çağırır [. ](https://godoc.org/github.com/lib/pq#Open)PostgreSQL Için Azure veritabanı veritabanına bağlanmak üzere () öğesini açın ve yöntemi DB kullanarak bağlantıyı kontrol edin [. Ping ()](https://golang.org/pkg/database/sql/#DB.Ping). Bir [veritabanı tanıtıcısı](https://golang.org/pkg/database/sql/#DB) , veritabanı sunucusu için bağlantı havuzunu tutan şekilde kullanılır. Kod, tabloyu güncelleştiren SQL ifadesini çalıştırmak için [Exec ()](https://golang.org/pkg/database/sql/#DB.Exec) yöntemini çağırır. Hata oluşup olmadığını denetlemek için özel checkError () yöntemi kullanılır.

@No__t-0, `DATABASE`, `USER` ve `PASSWORD` parametrelerini kendi değerlerinizle değiştirin. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

## <a name="delete-data"></a>Verileri Sil
Bir **Delete** SQL ifadesini kullanarak bağlanmak ve verileri silmek için aşağıdaki kodu kullanın. 

Kod üç paketi içeri aktarır: [SQL paketi](https://golang.org/pkg/database/sql/), Postgres sunucusuyla iletişim kuracak sürücü olarak [PQ paketi](https://godoc.org/github.com/lib/pq) ve komut satırında yazdırılan girdi ve çıktı için [FMT paketi](https://golang.org/pkg/fmt/) .

Kod, SQL yöntemini çağırır [. ](https://godoc.org/github.com/lib/pq#Open)PostgreSQL Için Azure veritabanı veritabanına bağlanmak üzere () öğesini açın ve yöntemi DB kullanarak bağlantıyı kontrol edin [. Ping ()](https://golang.org/pkg/database/sql/#DB.Ping). Bir [veritabanı tanıtıcısı](https://golang.org/pkg/database/sql/#DB) , veritabanı sunucusu için bağlantı havuzunu tutan şekilde kullanılır. Kod, tablodan bir satırı silen SQL ifadesini çalıştırmak için [Exec ()](https://golang.org/pkg/database/sql/#DB.Exec) yöntemini çağırır. Hata oluşup olmadığını denetlemek için özel checkError () yöntemi kullanılır.

@No__t-0, `DATABASE`, `USER` ve `PASSWORD` parametrelerini kendi değerlerinizle değiştirin. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mydemoserver.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mydemoserver"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

## <a name="next-steps"></a>Sonraki adımlar
> [!div class="nextstepaction"]
> [Dışarı aktarma ve Içeri aktarma kullanarak veritabanınızı geçirme](./howto-migrate-using-export-and-import.md)
