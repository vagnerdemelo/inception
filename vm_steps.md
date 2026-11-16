### Iniciar configuração de VM pelo Vagrant
1. Rodar na máquina local dentro do diretório desejado o comando:
	```
	vagrant init bento/<image> #Ex: "ubuntu/jammy64". Esta imagem representa o Ubuntu 22.04LTS
	```

2. No arquivo `Vagrantfile` que foi gerado após o comando acima, incluir o seguinte bloco logo após `config.vm.box = "bento/ubuntu-26.04"`
	```
	config.vm.provider "virtualbox" do |vb|
		vb.name = "inception"
		vb.memory = 4096
		vb.cpus = 2
		vb.gui = false
	end
	```
	Nas instruções acima, estamos defininddo o provider, nome, tamanho da memória RAM, quantidade de CPU's e se queremos inicar a VM com uma interface gráfica.

3. Rodar `vagrant up` para iniciar a criação da VM.

4. Após a criação executada com sucesso, podemos acessar a VM rodando `vagrant ssh`.

___

### Intalando ferramentas
Agora já dentro da VM, podemos instalar as ferramentas necessárias para trabalharmos no projeto Inception.

1. Verifique se o docker e docker compose estão instalados:
	```
	docker --version
	docker compose version
	```
2. Caso não estejam instalados rode:
	```
	sudo apt update
	sudo apt install -y ca-certificates curl

	sudo install -m 0755 -d /etc/apt/keyrings

	sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
	-o /etc/apt/keyrings/docker.asc

	sudo chmod a+r /etc/apt/keyrings/docker.asc
	```
	Com os comandos acima, primeiro, instalaremos as dependências e adicionaremos a chave oficial.

	Agora, adicionaremos o repositório Docker ao APT:
	
	```
	sudo tee /etc/apt/sources.list.d/docker.sources > /dev/null <<EOF
	Types: deb
	URIs: https://download.docker.com/linux/ubuntu
	Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
	Components: stable
	Architectures: $(dpkg --print-architecture)
	Signed-By: /etc/apt/keyrings/docker.asc
	EOF

	sudo apt update
	```
3. Instalando o docker e docker compose:
	```
	sudo apt install -y \
	docker-ce \
	docker-ce-cli \
	containerd.io \
	docker-buildx-plugin \
	docker-compose-plugin
	```
	Confirme se foi instalado com sucesso rodando:
	```
	docker --version
	docker compose version
	sudo systemctl is-active docker
	```
4. Após a instalação do docker, é necessário alterar a permissão de acesso:
	```
	sudo usermod -aG docker $USER
	```
___

### Incluindo arquivos compartilhados
Para compartilhar um diretório da sua máquina local com a sua VM inclua a seguinte linha no seu `Vagrantfile`:
```
config.vm.synced_folder "<path_to_my_project>", "/home/vagrant/<path_destination>"
```
Rode `vagrant reload` para recarregar a configuração da VM e acesse novamente a sua VM com `vagrant ssh`.

Dessa forma vc consegue trabalhar de usa máquina local com as mudanças refletindo diretamente na VM
