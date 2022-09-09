# Docker
* Docker, uygulama geliştirmek, dağıtmak ve çalıştırmak için oluşturulan açık bir platformdur. Docker açık kaynaklı (open source) container teknolojisidir. Docker aynı işletim sistemi üzerinde birbirinden bağımsız ve izole container oluşturmamızı ve uygulamamızı test etmenizi sağlayan teknolojidir. Docker tek işletim sistemi üzerinde çalıştığı için sunucu maliyetinden, disk alanından ve zamandan tasarruf etmemizi sağlar. Docker platformu altında birçok çözüm sunulur ve bunların en temelinde de docker engine ürünü bulunur.
## Docker Tarihçesi
* Docker, dünyada en çok kullanılan yazılım konteyner platformudur. Docker, Linux Kernel’e 2008 yılında eklenen Linux Containers (LXC) üzerine kurulu bir teknolojidir. LXC, işletim sistemi seviyesinde bir sanallaştırma altyapısı sunmaktadır.
## Docker Engine
![Docker Engine Resim](https://tecadmin.net/tutorial/wp-content/uploads/2017/09/docker-engine-architecture.png)
*  Docker sisteminin çekirdeğini temsil eder. Temeldeki istemci-sunucu teknolojisidir. Docker Engine üç bileşenden oluşur:
*  Docker Daemon: Hypervisor’ün dockerdaki karşılığıdır. Bütün CPU ve RAM vb gibi işletim sistemine ait işlerin yapıldığı bölümdür.
* Rest API: Programlar ve Docker arasında iletişim kurar
* Komut Satırı (CLI):Docker komutlarını çalıştırmak için kullanılır.
