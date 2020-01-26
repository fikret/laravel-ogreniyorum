

# Laravel Öğreniyorum
Laravel öğrenirken kullandığım kaynaklar ve ipuçları
Bu notlar MAC cihazda yaptığım uygulamalarda alınmıştır. 

## Kurulum
Homestead ile bilgisayarına Laravelin gereksinimlerini içeren bir sanal makina kurmalısın. Böylece bilgisayarını kirletmeden bir sunucun olacak. Bunu yapabilmek için vagrant bize yardımcı olacak. Tüm bunlarla ilgili detaylı ingilizce döküman [şurada](https://laravel.com/docs/6.x/homestead) var. Homestead'in başka bir avantajı da Laravelin yeni sürümlerinde gereksinimleri otomatik olarak alması.
### Gereksinimler

 - [Virtualbox](https://www.virtualbox.org/wiki/Downloads) Ücretsiz ve açık kaynaklı bir sanal makina uygulaması
 - [vagrant](https://www.vagrantup.com/downloads.html) Virtualbox üzerinde sanal makina yönetimini sağlayan başka bir terminal uygulaması
 - **SSH Key** (Bu olmadan ilerleme)

#### Nasıl SSH Key Yaparım?
`ssh-keygen -t rsa -b 4096 -C "email@example.com"`
O ne lan diyenler için [şurda](https://docs.gitlab.com/ee/ssh/README.html#generating-a-new-ssh-key-pair) detaylar var

### Yapılacaklar

```sh
vagrant box add laravel/homestead
```
Terminalden bunu yazdığımızda laravel için gerekli olan sanal makina indirilecek bu epey sürüyor. 2gb falan sanırım
Terminal açılıp home dizindeyken şu kodu çalıştır:
```sh
git clone https://github.com/laravel/homestead.git ~/Homestead
```
```sh
cd ~/Homestead

git checkout release
```

```sh
bash init.sh
```

Şimdi sırada Config dosyasını ayarlamak var.  ~/Homestead/Homestead.yml dosyasını açıp aşağıdaki şekilde düzenle

```sh
---
ip: "192.168.10.10"
memory: 2048
cpus: 2
provider: virtualbox

authorize: ~/.ssh/id_rsa.pub

keys:
    - ~/.ssh/id_rsa

folders:
    - map: ~/code
      to: /home/vagrant/code

sites:
    - map: homestead.test
      to: /home/vagrant/code/project1/public

databases:
    - homestead

features:
    - mariadb: false
    - ohmyzsh: false
    - webdriver: false

```
Makinandan **~/code dizinine** gidip **project1/public** dizini oluştur içine index.html dosyası at test için

Bilgisayarında **host dosyasında 192.168.10.10 adresine homestead.test** ataması yap.

Bundan sonra şu komutlarla sanal makinayı tekrar başlat

    vagrant reload --provision

olmazsa

    vagrant halt
    vagrant provision

dene

Test etmek için homestead.test sayfasını tarayıcıda aç. Yüklediğin html dosya açılıyorsa başarılı şekilde dev ortamı hazır demektir.

Şimdi sırada Laravel'in yüklenmesi var.

 1. List ite
	Bilgisayarında terminalden `vagrant ssh` yazıp Homestead sanal makinamıza erişelim
 2. vagrantın home dizinindeyken composer ile laravel installer kuralım `composer global require laravel/installer`
 3. Daha sonra projelerimizin hepsinin olduğu ana dizin olan code dizinine geçelim ve şu kodu çalıştırıp laravel uygulamasını yükleyelim. `composer create-project --prefer-dist laravel/laravel projeninadi`
 4. Şimdi bu proje hangi sitemiz için oluşturuldu bunu sanal makinadan çıkıp homestead.yaml dosyamıza yazıp ayrıca kendi bilgisayarımızda host dosyasına yazalım.
 nano Homestead.yaml
     
        sites:
        - map: homestead.test
          to: /home/vagrant/code/project1/public
    
        - map: yeniprojem.test
          to: /home/vagrant/code/projeninadi/public
          
Dikkat edilirse ikinci sıradaki map kısmını yeni ekledik. Kaydedip çıkın sonrasında ise **yeniprojem.test sayfasını host** dosyasına da eklemeliyiz.
 6. Yukarıdaki işlemlerden sonra sanal makinamızı yeniden çalıştırmalıyız bunun için şu kodu çalıştırın
 
    vagrant reload --provision

Sanal makina açıldıktan sonra tarayıcıya yeniprojem.test yazdığınızda laravel karşılama ekranını görmelisiniz.

### Kurulum Hataları ve Çözümler
"No input file specified." hatası alıyorsan Homestead.yml dosyanda map kısmını yanlış yapmışsın demektir.

vagrant'ın desteklediği virtualbox'ın sistemde kurulu olduğundan emin ol son versiyonlar bazen çalışmıyor.

SSH key yoksa bu konuda hata verecektir. [Gereksinimlere](#Gereksinimler) bakmalısın.

## Laravel Dizinleri
### app
### artisan
php ile ilgili yardımcı işmeleri yapar
### bootstrap
uygulamanın ayağa kalkması için gereken ayarlar
### composer.json
composer ile ilgili paketler
### composer.lock
composer ile ilgili paket yüklemelerin son andaki her paketin bilgisini tutar
### config
her konunun kendine özel ayar dosyası var.
### database
veritabanı migrationlar burada
seederlar burada - uygulamanın çalışablmesi için veritabanında olması gereken veriler buraya eklenir.
### .editorconfig
editör ayarları (laravelle ilgili değil)
### .env
en önemli dosya. Laraveli sihirli hale getirir.
### .gitattributes
git tanımlamaları
### .gitignore
git'e gönderilmeyecek 
### package.json
önyüzle alakalı npm'in bağımlılıkları burada
### phpunit.xml
unit testlerle ilgili. Uygulama içi test yazmaya yarar. detayları tests dizininde oluyor. 
### public
projenin dışarıya açık tek klasörü
### resources
geliştirme ortamı verileri bulunur
### routes
#### web.php
Tarayıcından sitemize gelen kullanıcıları karşılayan dosyadır. Kullanıcılar nereye gidecekse bu dosya üzerinden ayarlanır. Sitemizdeki URL yapısını ayarlamamızı sağlar. Bunun içinde yaptığımız işler klasik tüm sayfanın yüklenmesi şeklinde çalışıyor. Buradaki yetkilendirme oturum(cookie) üzerinden yürür.

#### api.php
apiler için kullanılır. Tüm sayfanın yüklenmesi gerekmeyen durumlarda kullanılır. Ayrıca yetkilendirmeler yapılır. Bu yetkilendirme cookie'ye dayalı değildir.

#### channels.php
Kullanıcıların belli kanallara abone olup oradan anlık bildiriml almalarını sağlar.

#### console.php
http'yi aradan kaldırıp doğrudan console işlemleri yapmamızı sağlar

### server.php
hoca bilmiyor
### storage
cacheler, uygulama ile ilgili bazı dosyalar, loglar bulunur
### tests
testler burada yazılıyor.
### vendor
composer json dan çekilip içeriye kopyalanan paketlerin bulunduğu dizin
### webpack.mix.js
webpack ayarları

## Kullandığımız Çeşitli Komutlar
### Model oluşturmak


    php artisan make:model TodoItem -mc

 TodoItem
 -m migration oluştur
 -c ise controller oluştur demek
 komut çalışınca app dizini içinde
 TodoItem.php dosyası oluşur
 app/Http/controller içinde TodoItemController.php dosyası oluşur
 database/migraiton içinde ise
 2020_01_26_084500_create_todo_items_table.php dosyası oluşur.
migration dosyasına gidilip database için gerekli eklemeler yapılır
https://laravel.com/docs/6.x/migrations

Bu işlemlerden sonra aşağıdaki komut çalıştırılır.

    php artisan migrate
Bu komut çalıştığında veritabanı **migration** dosyası çalışır ve veritabanı tabloları oluşur.


------------


    php artisan tinker
Larvel içindeki kodların çalışıp çalışmadığını test etmek için kullanılır. Bunun güzelliği  bir php dosya oluşturmadan yapmış olduğumuz modelleri istediğimiz gibi test edebilmek
ör:
```shell
$todo = new App\TodoItem;
$todo->text = "Öğle yemeğine çıkılacak";
$todo->save();
$todo;
App\TodoItem::all();
```

 

## Diğer Kaynaklar
- [HTTP Statuses](https://httpstatuses.com/) HTTP ststus kodları
- [HTTPBIN](http://httpbin.org)
- [PHP Standards Recommendations](https://www.php-fig.org/psr/)
- [PHP Magic Methods](https://www.php.net/manual/tr/language.oop5.magic.php)
- [Test Driven Laravel](https://course.testdrivenlaravel.com/)
