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

Antes da criação de uma nova instância, o grupo de segurança criado para a atividade anterior foi utilizado novamente nesta atividade como segue na imagem abaixo, com a única adição da regra de entrada *inbound rules* dos dados referente ao banco de dados MySQL


  | Type         | Protocol | Port Range | Source Type | Source      | Description   |
  |--------------|----------|------------|-------------|-------------|---------------|
  | MYSQL/Aurora | TCP      | 3306       | Anywhere    | 0.0.0.0/0   | MySQL         |


![image](https://github.com/katiacardoso/Atividades_Compass_DevSecOps/assets/91233884/3c6a4aab-9f29-4cea-9023-1c881ddc9daa)

Caso ainda não tenha criado o seu grupo de segurança, recomendo seguir o repositório "AWS_LinuX" para a parte da documentação anterior que tratou com mais detalhes este aspecto de criação, ou então a leitura da documentação da própria AWS: https://docs.aws.amazon.com/pt_br/AWSEC2/latest/UserGuide/working-with-security-groups.html


# Criação da instância 


- criar em Launch Instance
- nomes e tags
- lembrar de add tipo valores e volume
-  a AMI escolhida foi Amazon Linux 2023
-  o Instace type :  t2.micro
-  foi criada uma key pair cno formado .pem
-  na sessão de Network setting foi marcada para seleção a vpc já criada para atividade passada e uma subnet pública. Adiciona o grupo de segurança que foi adicionado recentemente a nova regra para o acesso do banco de dados 

![image](https://github.com/katiacardoso/Atividades_Compass_DevSecOps/assets/91233884/c7227ab8-72f8-492c-9406-533766d58e9b)

- manteve o armazenamento sugerido

![image](https://github.com/katiacardoso/Atividades_Compass_DevSecOps/assets/91233884/9eda3186-50a6-407d-a39b-b703ee306b20)

- Adicionou o script abaixo na sessão de *Advanced details* (bem no final, pode rolar bastante) na parte de *user data)

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


- gerando um resumo da criação desta forma:

![image](https://github.com/katiacardoso/Atividades_Compass_DevSecOps/assets/91233884/818c8d2b-4a8d-4558-a7a7-463bd57dae60)

- Siga para criação da isntancia apertando em *Launch instance*

# RDS

https://aws.amazon.com/pt/tutorials/deploy-wordpress-with-amazon-rds/module-one/

- busca o serviço na barra
- chosse a database creation method: Standard create
- ![image](https://github.com/katiacardoso/Atividades_Compass_DevSecOps/assets/91233884/d88a1ce4-c797-4449-823933371552a944)
- Engine type : MySQL
- ![image](https://github.com/katiacardoso/Atividades_Compass_DevSecOps/assets/91233884/14691710-e99b-4de7-8a9d-6659f637fd2f)
- Template: Free tier
- ![image](https://github.com/katiacardoso/Atividades_Compass_DevSecOps/assets/91233884/166783a3-45c1-4711-90ba-6338d56f73c7)
- Em Setting: mantece o indentifier e master username e foi adicionada uma master password (é bom salvar em algum local pois será necessario para acessar depois )
- ![image](https://github.com/katiacardoso/Atividades_Compass_DevSecOps/assets/91233884/cbf9013b-ec4e-4d59-8781-dc1623a32e21)
- Em connectivity identificou a VPC automaticamente, mas é bom atentar a isto ; Selecione "Yes" para Public Access e selecione o seu security group criado e ajustado anteriormente em *Existing VPC security groups*
-![image](https://github.com/katiacardoso/Atividades_Compass_DevSecOps/assets/91233884/6dc10b06-3269-40ac-bc0b-13b90bd916ea)
- Desça até *Additional configuration* e especique um nome para o *Initial database name* pois ele será importante para fazer a conexão no futuro

- após a criação percebi que poderia ter colocado em subnets iguais, mas veremos cenas do proximo capitulo 

seguindo este tutorial: 
https://aws.amazon.com/pt/tutorials/deploy-wordpress-with-amazon-rds/module-two/

- o que difere até agora é a questão da liberação do IP para todos e nesta documentação esta especificada para apenas o My IP


https://aws.amazon.com/pt/tutorials/deploy-wordpress-with-amazon-rds/module-three/

- na parte de permissão de acesso foi necessário apagar a regra do banco e adicionar novamente, pois na hora de alterar a origem para o meu grupo de segurança, deu a mensagem de erro "You may not specify a referenced group id for an existing IPv4 CIDR rule."
- fiz acesso via SSH via putty, salvando uma chave privada via putty gen para gerar na extensão .ppk e loguei com user : ec2-user . dá de seguir este tutorial também https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html
- seguindo na parte de criar um usuário, ao colocar o comando sudo yum install -y mysql deu erro Error: Unable to find a match: mysql. Foi seguido este tutorial https://muleif.medium.com/how-to-install-mysql-on-amazon-linux-2023-5d39afa5bf11 até antes da parte dar start no mysql e então retornei ao comando sudo yum install -y mysql e deu certo e sendo assim, retorno ao tutorial da AWS
- export MYSQL_HOST=<your-endpoint>, neste comando ao colocar no terminal com seu endpoint, retire as chaves
- mysql --user=<user> --password=<password> wordpress e foi o nome wordpress mesmo (mas não verifica da onde veio ) e apos este comando veio esta mensagem
- ![image](https://github.com/katiacardoso/Atividades_Compass_DevSecOps/assets/91233884/435291b3-ac11-476d-a097-673ad295b773)
- seguindo com a parte de criação do usuário
- 

https://aws.amazon.com/pt/tutorials/deploy-wordpress-with-amazon-rds/module-four/

- ao inves do comando sudo service httpd start coloquei o sudo systemctl start httpd, pois o primeiro deu a seguinte mensagem Redirecting to /bin/systemctl start httpd.service
- verifiquei o status do apache com sudo systemctl status httpd e deu active
- nesta parte Você pode salvar e sair do nano digitando CTRL+O seguido de enter e depois de CTRL+X.
- deu um erro quando fui baixar a parte dos extras, possivlemtne por ser a versão 2023, contudo encontrei este tutorial e segui até o comando echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php e deu permission denied. Mas possivlemnte se mudasse para a imagem do amazon 2 seria mais tranquilo. Tão perto e tão longe.....



