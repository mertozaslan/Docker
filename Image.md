# Image
- Docker Image, bir uygulamanın ve onun içinde çalışması için gereken tüm ek kütüphane ve diğer ögelerin paketlenmiş haline ve  içerisinde kernel bulundurmayan yapıya docker image denir. Docker image’ dan container oluşturulur.

## Image Registry

![](https://miro.medium.com/max/951/1*G0sAFMN7--Tod-laJt-Z8w.png)

- Docker image'lerini depolayabildiğimiz sistemlere Image Registry denir.
- Docker ortamında varsayılan olan Docker Hub'dır. Benzer şekilde Microsoft Container Registry gibi Cloud servisler kullanılabilir.
### Image Isimlendirme

- Docker imajına verilen isim aynı zamanda o imajın nerede depolandığını da belirtir.

![Ekran Görüntüsü (15)](https://user-images.githubusercontent.com/98760765/188269951-7fb0fa2f-1b9b-4caf-90d7-4ce635e3ba42.png)

- Görselde de gözüktüğü gibi image'in isimlendirme yapısı `<RegistryURL>/<repository>:<tag>` şeklindedir.
- Burada ki Registry URL kısmını kullanacağımız image için özel olarak belirtmezsek varsayılan Image Registry olan Docker Hub(docker.io) kullanılacaktır.
- Benzer şekilde kullanacağımız image için özel olarak belirtmezsek burada ki varsayılan Tag olarak `:latest` kullanılacaktır. Buradaki `:latest` sürümü son sürüm değildir varsayılan sürümdür.
- Sonuç olarak `docker.io/ozgurozturknet/adanzyedocer:latest` ile `ozgurozturknet/adanzyedocker` kullanımı aynı sonucu verecektir.

### Docker'ın Katmanlı Dosya Sistemi Yapısı
- Docker depolama alt yapısında union file system adı verilen bir yapı kullanır.
- Docker imajları mevcut bir base imaj üzerine inşa edilir. Bu katmanlar sadece okunabilir katmanlardır. 
- Yeni Bir imaj oluşturulacağı zaman mevcut base imajın üzerine container yazılabilir bir katman eklenir. Container üzerinde yapılan tüm değişiklikler bu katman üzerinden yapılır.
- Tüm katmanlar ayrı ayrı saklanmasına rağmen hepsini bir arada görmemizi Union File System sağlar.
- Image’lar katmanlar şeklinde organize edildiği için Imaj'da meydana gelecek değişikliklerde Image’ın güncellenmesi sadece belirli katmanları etkileyeceğinden imaj’ların güncellenme maliyetleri minimum düzeydedir. 

### Image Komutları
- **`docker image --help`**  ->  image bileşeni ile kullanabileceğimiz komutları görebiliriz.
- Kullanımı ` docker image COMMAND ` formatındadır.
- **`docker image ls`**  -> Sistemdeki mevcut Image’leri listeler.
- **`docker image build `**  -> Dockerfile üzerinden yeni Image’ler oluşturur.
	- **`docker image build -t <image-name> .`**  -> Dockerfile yazılıktan sonra image build işlemini gerçekleştirir. Burada ki `-t` options’u oluşturulacak image’in tag’ini belirler ve bilindiği üzere image’lerde isimler oldukça önemlidir.
	- **`docker image build -t <image-name> -f <dockerfile-name> .`** -> Farklı bir Dockerfile'dan imaj build eder. Eğer ki oluşturduğunuz Dockerfile dosyasının adını `Dockerfile ` olarak kaydederseniz özel olarak -f ile <dockerfile-name> belirtilmesine gerek kalmaz.
- **`docker image rm <ImageID>`** -> ID'si verilen Image'i siler.
- **`docker image prune -a`** ->Çalışmayan tüm image’leri siler.
- **`docker image pull <ImageName>`** -> komutu sayesinde local sistemimize bir imajı çekeriz
  - Tag belirtmediğimiz müddetçe default olarak `latest` tag'ı kullanılır.
- **`docker image push <Image>`** -> komutu ile localdeki istediğimiz image'i hub'a pushlayabiliriz
  - Localde oluşturduğumuz bir Docker imajını hub'a göndermek için öncelikle CLI'da login olmamız gerekir
  - **`docker login`** -> komutu ile login olabiliriz.
- **`docker image history <ImageName>`** komutu ile image'in geçmişini görebiliriz. İçerisindeki tüm katmanlara bakabiliriz.
- **`docker save <LocalImage> -o <filename>.tar`** -> komutu ile sistemde bulunan bir image'i tar dosyası olarak kaydedebiliyoruz.
   - Buradaki `-o` (output) parametresidir.
   - **docker save** kullanımı genellikle internet erişimi bulunmayan ortamlar içerisinde kullanılır.
- **`docker load -i <TarFile>`** -> komutu ile sistemde tar dosyası olarak bulunan image'i sisteme yükleyebiliyoruz.

## DockerFile
- Bilindiği üzere  DockerHub gerek official (Ubuntu, Nginx, Redis, vb) gerekse de bu Image’lardan türetilen ve farklı özellikler barındıran birçok farklı ve çok faydalı Image içermektedir. Bu Image’lar ihtiyaçlarımızı çok büyük oranda karşılasa da kısa sürede gerek official gerekse de diğer repository’lerdeki Image’ları özelleştirme ihtiyacı ortaya çıkmaktadır. Yeni Image oluşturma işlemini ise Dockerfile dosyaları sayesinde yaparız.
- Dockerfile kendi özgü kuralları olan bir dille yazılan ve bizlerin Docker Image'leri oluşturmamızı sağlayan dosyalardır.

### DockerFile Dosya Yapısı
- Dockerfile dosyası oluşturulurken default "Dockerfile" şeklinde oluşturulur. Dockerfile.new, Dockerfile.env, Dockerfile.prod gibi örnekler ile aynı dizinde birden fazla Dockerfile oluşturulabilir.
- Docker, Dockerfile dosyasında bulunan komutları sırayla tek tek çalıştırır. 
- Her komut yeni bir katman oluşturur ve Docker build sonunda elimizde uygulamaya ait Docker Image oluşur . Docker Image'den container oluşturarak uygulamayı ayağa kaldırabiliriz.



### DockerFile Parametreleri

- **FROM** -> Oluşturulacak imajın hangi imajdan oluşturulacağını belirten talimat. Dockerfile içerisinde geçmesi mecburi tek talimat budur. Mutlaka olmalıdır. 
  - Ör: FROM ubuntu:18.04

- **LABEL** -> İmaj metadata’sına key=value şeklinde değer çiftleri eklemek için kullanılır. Örneğin team=development şeklinde bir etiket eklenerek bu imajın development ekibinin kullanması için yaratıldığı belirtilebilir.
  - Ör: LABEL version:1.0.8

- **RUN** -> İmaj oluşturulurken shell’de bir komut çalıştırmak istersek bu talimat kullanılır. Örneğin apt-get install xxx ile xxx isimli uygulamanın bu imaja yüklenmesi sağlanabilir. 
  - Ör: RUN apt-get update

- **WORKDIR** -> cd xxx komutuyla ile istediğimiz klasöre geçmek yerine bu talimat kullanılarak istediğimiz klasöre geçer ve oradan çalışmaya devam ederiz. 
  - Ör: WORKDIR /usr/src/app

- **USER** -> gireceğimiz komutları hangi kullanıcı ile çalıştırmasını istiyorsak bu talimat ile onu seçebiliriz. 
  - Ör: USER poweruser

- **COPY** -> İmaj içine dosya veya klasör kopyalamak için kullanırız
  - Ör: COPY /source /user/src/app

- **ADD** -> COPY ile aynı işi yapar yani dosya ya da klasör kopyalarsınız. Fakat ADD bunun yanında dosya kaynağının bir url olmasına da izin verir. Ayrıca ADD ile kaynak olarak bir .tar dosyası belirtilirse bu dosya imaja .tar olarak sıkıştırılmış haliyle değil de açılarak kopyalanır. 
  - Ör: ADD https://wordpress.org/latest.tar.gz /temp

- **ENV** -> Imaj içinde environment variable tanımlamak için kullanılır
  - Ör: ENV TEMP_FOLDER="/temp"

- **ARG** -> ARG ile de variable tanımlarsınız. Fakat bu variable sadece imaj oluşturulurken yani build aşamasında kullanılır. Imajın oluşturulmuş halinde bu variable bulunmaz. ENV ile imaj oluşturulduktan sonra da imaj içinde olmasını istediğiniz variable tanımlarsınız, ARG ile sadece oluştururken kullanmanız gereken variable tanımlarsınız.
  - Ör: ARG VERSION:1.0

- **VOLUME** -> Imaj içerisinde volume tanımlanamızı sağlayan talimat. Eğer bu volume host sistemde varsa container bunu kullanır. Yoksa yeni volume oluşturur. 
  - Ör: VOLUME /myvol

- **EXPOSE** -> Bu imajdan oluşturulacak containerların hangi portlar üstünden erişilebileceğini yani hangi portların yayınlanacağını bu talimatla belirtirsiniz. 
  - Ör: EXPOSE 80/tcp

- **ENTRYPOINT** -> Bu talimat ile bir containerın çalıştırılabilir bir uygulama gibi ayarlanabilmesini sağlarsınız.
  - Ör: ENTRYPOINT ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

- **CMD** -> Bu imajdan container yaratıldığı zaman varsayılan olarak çalıştırmasını istediğiniz komutu bu talimat ile belirlersiniz. 
  - Ör: CMD java merhaba

- **HEALTHCHECK** -> Bu talimat ile Docker'a bir konteynerin hala çalışıp çalışmadığını kontrol etmesini söylebiliriz. Docker varsayılan olarak container içerisinde çalışan ilk processi izler ve o çalıştığı sürece container çalışmaya devam eder. Fakat process çalışsa bile onun düzgün işlem yapıp yapmadığına bakmaz. HEALTHCHECK ile buna bakabilme imkanına kavuşuruz.
  - Ör: HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost/ || exit 1

- **SHELL** -> Dockerfile'ın komutları işleyeceği shell'in hangisi olduğunu belirtiriz. Linux için varsayılan shell ["/bin/sh", "-c"],Windows için ["cmd", "/S", "/C"]. Bunları SHELL talimatı ile değiştirebiliriz. 
  - Ör: SHELL ["powershell", "-command"]

### Multi-Stage Build

![](https://res.cloudinary.com/practicaldev/image/fetch/s--7-CASre---/c_imagga_scale,f_auto,fl_progressive,h_900,q_auto,w_1600/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ku5v8mzocm3wo2z0z8al.png)

	
- Multi-stage build özelliği bizim imaj yaratma aşamasını kademelere bölmemizi ve ilk kademede yarattığımız imaj içerisindeki dosyaları bir sonraki kademede oluşturacağımız imaja kopyalayabilmemize imkan sağlıyor. Bu sayede nihai imaj boyutumuzun küçülmesine imkan tanıyor.
- Dockerfile dosyasındaki her bir satır için image’de bir katman oluşturulmaktadır.Multistage Build mantığı Dockerfile dosyalarında birden fazla ‘FROM’ etiketi ile birden fazla base image tasarlanmasına dayanmaktadır
- Her bir ‘FROM’ talimatı farklı bir katmanda değerlendirilecektir ve önceki ‘FROM’ talimatlarını ezecektir. Böylece üretilecek image’de, tüm ‘FROM’ yükünü göz ardı ederek istediğiniz bir noktada belirli çıktıları kopyalayıp esas kabul edebilirsiniz. Böylece ilgili image’in; lüzumsuz katmanlardan, o katmanların barındırdığı imagelerden arındırılmış olmasından dolayı boyutu küçülecektir.

![dockerfile_image10](https://user-images.githubusercontent.com/98760765/182618440-2c2596c1-e217-4ef5-a37c-4f2d5871efc3.png)

- Yukarıda göründüğü üzere Dockerfile da birden fazla ‘FROM’ talimatı kullanarak uygulamanın derlenmesi için gerekli olan daha büyük boyutlu alpine image' ı yerine uygulamanın derlenmiş halinin bulunduğu ve içerisinde daha küçük boyutlu bizim ihtiyacımız karşılayabilecek bir alphine imajı içeren yeni bir image oluşturulmuştur. Bu sayede son image boyutumuz önemli ölçüde azalmıştır.
	
	
	
	
	
	
	
	
	
	
	
	

