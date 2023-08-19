<div align="center">
  <img src="https://github.com/katiacardoso/Atividades_Compass_DevSecOps/assets/91233884/b664fd8d-a5ef-4a50-aec4-0bfce4439f51)" width="215px">
</div>


# Atividade AWS e Docker

### *Atividade realizada por: Katia Cardoso, Maurício Souza e Thays Santos*


## Descrição da atividade

1. Instalação e configuração do DOCKER
ou CONTAINERD no host EC2;
Ponto adicional para o trabalho utilizar a
instalação via script de Start Instance
(user_data.sh)
2. Efetuar Deploy de uma aplicação
Wordpress com:
container de aplicação
RDS database Mysql
3. Configuração da utilização do serviço EFS
AWS para estáticos do container de
aplicação Wordpress
4. Configuração do serviço de Load
Balancer AWS para a aplicação
Wordpress

 Pontos de atenção:
 
- não utilizar ip público para saída do
serviços WP (Evitem publicar o serviço
WP via IP Público)
- sugestão para o tráfego de internet
sair pelo LB (Load Balancer Classic)
- pastas públicas e estáticos do
wordpress sugestão de utilizar o EFS
(Elastic File Sistem)
- Fica a critério de cada integrante (ou
dupla) usar Dockerfile ou
Dockercompose;
- Necessário demonstrar a aplicação
wordpress funcionando (tela de login)
- Aplicação Wordpress precisa estar
rodando na porta 80 ou 8080;
- Utilizar repositório git para
versionamento;
- Criar documentação


# Criação do grupo de segurança 

# Script no user data

```
#!/bin/bash

sudo yum update -y
sudo yum install docker -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user
#verificar o comando abaixo
sudo chkconfig docker on
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo mv /usr/local/bin/docker-compose /bin/docker-compose
sudo yum install nfs-utils -y
sudo mkdir /mnt/efs/
sudo chmod +rwx /mnt/efs/
```

<details>
    <summary>pequenos comentários de como funciona cada comando:</summary>

      
**sudo yum update -y** : atualiza os pacots do sistema operacional. O parâmetro '-y' faz uma atualização automática

**sudo yum install docker -y**: instala o Docker no sistema.

**sudo systemctl start docker**: inicia o serviço Docker

**sudo systemctl enable docker**: habilita o Docker para inicialização

**sudo usermod -aG docker ec2-user**: em geral este comando adiciona o usuário "ec2-user" ao grupo "docker". O *usermod* permite modificar configurções de usuários. A opção *-aG* do usermod significa *a(append)* e *G(groups)*. *docker* é o nome do grupo e *ec2-user* é o nome do usuário a ser adicionado 

**sudo chkconfig docker on** : similiar ao comando de systemctl enable 

**sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose** : baixa o utilitário Docker Compose do GitHub e o coloca na pasta "/usr/local/bin/". Indo por partes *curl* é utilizado para transferir dados via protocolos de rede. *-L* parâmetro de curl para seguir o direcionamento de URL. Em seguida encontra uma URL que aponta para o local do arquivo Docker Compose mais recente do GitHub, no qual *$(uname -s)* é substituido pelo nome do sistema operacional e *$(uname -m)* é substituido pela arquitetura. Já em *-o /usr/local/bin/docker-compose*  o parâmetro -o indica o caminho  onde o arquivo deve ser salvo, neste caso, esta pasta geralmente se armaazena executáveis 

**sudo chmod +x /usr/local/bin/docker-compose** :  dá permissão de execução ao arquivo 

**sudo mv /usr/local/bin/docker-compose /bin/docker-compose**: move o arquivo de pasta para permitir utilizar o Docker compose em qualquer ligar do sistema 

**sudo yum install nfs-utils -y**: instala o pacote 'nfs-util' para fornecer utilitários para trabalhar con NFS(Network File System)

**sudo mkdir /mnt/efs/**: criar um diretório para posterior criação de um sistema de arquivos NFS

**sudo chmod +rwx /mnt/efs/**: altera as permissões de leitura, gravação e execução do diretório criado anteriormente para todos os usuários

</details>

# RDS

