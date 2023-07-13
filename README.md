# Atividade_Linux_AWS

## Estágio Compass.UOL - Trilha Cloud & DevSecOps 

### Katia Lorena Cardoso Sena
---

  
## Requisitos AWS

Gerar uma chave pública para acesso ao ambiente; 

Criar 1 instância EC2 com o sistema operacional Amazon Linux 2 (Família t3.small, 16 GB SSD);

Gerar 1 elastic IP e anexar à instância EC2; 

Liberar as portas de comunicação para acesso público: (22/TCP, 111/TCP e UDP, 2049/TCP/UDP, 80/TCP, 443/TCP). 

## Requisitos Linux

Configurar o NFS entregue; 

Criar um diretorio dentro do filesystem do NFS com seu nome; 

Subir um apache no servidor - o apache deve estar online e rodando; 

Criar um script que valide se o serviço esta online e envie o resultado da validação para o seu diretorio no nfs; 

O script deve conter - Data HORA + nome do serviço + Status + mensagem personalizada de ONLINE ou offline; 

O script deve gerar 2 arquivos de saida: 1 para o serviço online e 1 para o serviço OFFLINE; 

Preparar a execução automatizada do script a cada 5 minutos. 

Fazer o versionamento da atividade; 

Fazer a documentação explicando o processo de instalação do Linux. 

---

  
# Configurações do Requisitos AWS

## Criação da instância EC2

- Logue na sua conta da AWS e selecione o serviço de EC2 no console ( caso não esteja visível logo de cara, é possível pesquisar na barra de pesquisa logo acima e DICA: já favorita ele na estrelinha ao lado do nome ao pesquisar, pois ele será bastante utilizado nesta documentação).

- Na nova janela aberta, clique em **Executar Instâncias** e os jogos irão começar
  
- Na sessão de **Nomes e tags** é importante adicionar as Tags que foram instruídas para criação da instância ( porque caso contrário, dá erro de criação :)). Portanto é recomendado adicionar as chaves: Name, Project e CostCenter, e seus valores respectivos. Outro ponto importante que não deve ser esquecido é o acréscimo do **Tipo de Recurso**, que se encontra ao lado do direito do valor, o tipo: *Volume*, ficando mais ou menos assim:
  
  ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/cdbc1d55-1b8c-457e-abbc-9d83d4aedfd1)

- Na sessão **Imagens de aplicaçao e de sistema Operacional (Imagem de máquina da Amazon)** é o momento de escolher a imagem que foi determinada, neste caso: Amazon Linux 2. Ao encontra-lá na listagem de Imagem de máquina da Amazon (AMI), basta selecionar e continuar o processo

  ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/2405bb39-8c81-44a5-9ae7-943388246823)

 - Abaixo é encontrada a sessão **Tipo de instância** na qual é o momento de escolher o tipo de instância que foi previamente determinado, neste caso: t3.small. Basta encontrar na listagem de Tipo de instância, selecionar e seguir com o processo
   
### Criação do par de chaves

Em seguida esta presente a sessão **Par de Chaves(login)**, na qual é o momento de gerar um par de chaves para conectar com segurança à esta instância que está sendo criada. 
  - <details>
      <summary>Comentários pessoais sobre problemas nesta parte</summary>
      
      -   Na atividade é solicitada uma *geração de chave pública para acesso ao ambiente*. Para fazer isto via Windows, é possível utilizar o PuttyGen e depois acessar via Putty. Contudo, nesta documentação: https://repost.aws/pt/knowledge-center/ec2-ppk-pem-conversion comenta sobre o uso no Linux, então como em tese temos uma máquina Oracle Linux instalada, seria interessante tentar logar por ela e fazer esta geração. Mas por hora, a solução foi implementada com acesso via Putty e quem sabe em posteriores atividades, a documentação aqui indicada auxilie.
      
      -   Sobre o tipo .pem e .ppk : é recomendado que você crie a chave no formato .pem, mesmo que você irá acessar pelo Putty (depois transforma ela em privada com o PuttyGen), pois futuramente é possível utilizar este mesmo par de chaves para o teste em máquinas Linux. - verificar nome do par de chaves no console AWS
      </details>
   
Foi criado um par de chaves novo, clicando em "Criar novo par de chaves" que se encontra dentro da sessão, adicionou-se um nome, deixou o tipo RSA e manteve o formato .pem . Clique em **Criar par de chaves** e salve o arquivo gerado ( apareça na sua pasta de Downloads da máquina local possivelmente) em um local seguro.
      
### Configuração de Rede

Na próxima sessão: **Configurações de rede** deve ser realiza a conexão obrigatória com uma VPC, subnet e um grupo de segurança. Então por partes:
  - <details>
      <summary> Caso não tenha uma VPC criada para realizar a associação, é necessário criar uma seguindo estes passos: </summary>
     
        
      - Na barra de pesquisa na parte superior, pesquise pelo serviço de VPC (recomendo também que favorite ele na estrelinha pois será de grande uso no processo :)) e clique para abrir o mesmo.
        
     #### Criação de VPC ( Virtual Private Cloud)
   
      - Clique em **Criar VPC**

      -  Na opção de **Recursos a serem criados**, mude de "Somente VPC" para "VPC e muito mais", pois assim já virá configurado automaticamente subnets, internet gateways, e assim por diante
  
      - No campo abaixo, pode adicionar um nome, no meu caso foi gerado um automaticamente então segui com ele
     
      - No campo de Bloco CIDR IPv4 eu deixei com /16, porém pode ser de boa prática deixar menos endereços IP's disponíveis pois nesta atividade não iremos utilizar 65.536 IP's :)
     
      - No campo Bloco CIDR IPv6, deixou-se marcado nenhum, pois vamos apenas de IPv4 mesmo
     
      - O restante deixei como padrão que veio, contudo se atente a criação de subnets públicas (mais detalhes no comentário abaixo) e apertei em "criar" no final da página
              - <details>
                 <summary>Comentários pessoais sobre problemas nesta parte</summary>
                Pois em uma das instâncias de testes criadas anteriormente, ocorreu um erro na porta 443 quando me conectei ao Putty e tentava executar qualquer comando yum. Pelas minhas pesquisas, seria necessário um NAT Gateway para funcionar, mas achei um pouco arriscado pois ele tem uma comunicação via única, ou seja, apenas acesso a internet e não permitir que a internet acesse ela. Portanto foi resolvido criar uma outra instância em uma subnet pública, visto que após criada não tem como mudar de subnet.
                </details>

      </details> 
      
   - Com a VPC já criada, ( se você teve que criar a VPC neste mesmo momento da criação da instância, basta apertar no ícone ao lado do para carregar as novas configurações), selecione a VPC e a subnet quue será referente a esta instância.
     
     >**Note** Uma atenção especial para **coloca-lá em uma subnet pública, pois nesta documentação ela está alocada em uma pública**

   - No campo **Atribuir IP público automaticamente** foi mantido o padrão "desabilitado"
     
     #### Configuração do grupo de segurança
     
   - No campo **Firewall(grupos de segurança)** decidi selecionar a opção de **Selecionar um grupo de segurança existente** pois assim já realizei as configurações de portas de entrada solicitadas.
     - <details>
        <summary> Caso não tenha um grupo de segurança criado para realizar a associação, é necessário criar um seguindo estes passos: </summary>
       
          
        - Na barra de pesquisa na parte superior, pesquise pelo serviço de EC2, ou se você estiver favoritado ele desde o primeiro uso, ele estará na parte superior da tela, mais ou menos assim:
        - adicionar imagem serviços favoritados 
       
        - Na aba esquerda, encontre **Grupo de segurança** e clique nele 
  
        -  Clique em **Criar grupo de segurança** ? confirmar se é isso mesmo
      
        -  No campo de VPC, encontrar e anexar a VPC criada anteiormente
    
        - No campo abaixo, pode acionar um nome e apertar para gerar ? Confirmar se é isso mesmo
       
        - Após criado, selecione este grupo de seurança e clique em **Ações**, no canto superior direito, e em seguida em **Editar regras de entrada**. Segue abaixo o modo como ficou configurado:
          ##
          | Type         | Protocol | Port Range | Source Type | Source      |
          |--------------|----------|------------|-------------|-------------|
          | SSH          | TCP      | 22         | Anywhere    | 0.0.0.0/0   |
          | Custom TCP   | TCP      | 111        | Anywhere    | 0.0.0.0/0   |
          | Custom UDP   | UDP      | 111        | Anywhere    | 0.0.0.0/0   |
          | Custom TCP   | TCP      | 2049       | Anywhere    | 0.0.0.0/0   |
          | Custom UDP   | UDP      | 2049       | Anywhere    | 0.0.0.0/0   |
          | Custom TCP   | TCP      | 80         | Anywhere    | 0.0.0.0/0   |
          | Custom TCP   | TCP      | 443        | Anywhere    | 0.0.0.0/0   |
          ##
       
  
        </details> 
   - Caso já tenha um grupo de segurança já existente anteriormente ao criado no passo anterior (lembra-se de adicionar as portas das **Regras de Entradas** , ou acabou de cria-lo anteriormente (lembrando de recarregar na setinha ao lado para subir as novas configurações) este é o momento de seleciona-lo para anexar o mesmo nesta instância que está sendo criada e seguir para o próximo passo.
  
   - Na próxima sessão **Configurar armazenamento** foi realizada basicamente a troca de "8gp2" para "16gp2", volume este que foi especificado na tarefa
   - E assim, chegou-se ao final da criação da Instância EC2, para fins de confirmação, segue um breve resumo das  configurações:
     
     ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/00c8ba32-54af-4118-bf7e-a9c6031d2c97)

   - Clique em **Executar Instância** e é após um pequeno momento de espera, é esperada uma mensagem de êxito na criação.
  
   - Para verificar se ela realmente está funcional, entre no serviço EC2, caso esteja fora dele, procure na barra lateral esquerda por "Instâncias" e confira se o Status Check (?) está 2/2 e ela está no estado "Executando"
     > 
      > **Warning**: Quando a instância não estiver sendo utilizada, não esqueça de alterar o status dela para *Interrompida* para não gerar gastos extras 

## Configuração do IP Elástico
  - Então, após o processo de criação, iremos configurar um IP elástico para permitir uma comunicação peristente com os recursos da AWS e facilitando a comunicação externa. Para a criação de um, deve-se estar no serviço EC2. Procure e clique em **IPs elásticos** na guia lateral esquerda. Em seguida, clique em **Alocar endereço IP Elástico** e em seguida em **Alocar**.
  - Após alocado ele deve ser associado a uma instância, caso contrário gerará custos. Portanto, clique no IP elástico alocado anteriormente, clique em **Ações** e em seguida em **Associar endereço IP Elástico**. Na nova janela, em **Resource Type** selecione **Instance**, em **Instance** selecione a instância criada anteriormente e clique no campo **Private IP Address** deve aparecer um endereço de IP lá para selecionar, ele será (ou deveria ser) o mesmo endereço privado que poderá ser encontrado nos detalhes da instância (na página da instância, não ali). Feito isso, basta clicar em **Associar** no final da página


# Acesso SSH à instância 

## Geração de chave para acesso ao ambiente

- Instale o programa PuttyGen e abra o mesmo na sua máquina local
  
- Em **Load** carregue o arquivo .pem do par de chaves que foi baixado na sua máquina no processo de vinculação à instância

- Clique em **Save private key** para gerar a chave privada em formato .ppk, para o futuro acesso à instância via Putty e salve no local desejado

>**Note** este passo é mais importante para a geração da chave em formato .ppk para acesso ao Putty, visto que, ao criar um par de chaves na AWS no tipo de chave RSA, o escolhido para esta atividade, na descrição está escrito assim: par de chaves privada e pública encriptografas com RSA. Portanto um dos requisitos da atividade de geração de chave pública já foi atendido

## Configurando acesso SSH via Putty

- O formato escolhido para o acesso foi o Putty (caso não tenha instalado na sua máquina, basta baixar na página oficial). Portanto, em sua máquina local, abra o programa do Putty

- No campo **Host Name (or IP address)** cole o IP público da sua instância. Este endereço é encontrado na página de detalhamento da instância. Mantenha a porta 22, pois a mesma já está liberada para acesso SSH no security group.
  
    ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/9240bb6a-aae9-4a5c-854b-a107147fb486)

- No menu lateral esquerdo do Putty, clique em **Connection** e em seguida em **SSH**, seguido de **Auth** e **Credentials**

- No campo "Private key file for authentication:", clique em **Browse...** para buscar o arquivo .ppk gerado anteriomente

- Em seguida, clique em **Open** e é esperado uma nova tela solicitando um login. Geralmente este login é : ec2-user. Mas para fins de checagem é possivel ir na sua instância no console da AWS, selecionar ela e clicar em **Conectar**. Nesta nova janela, siga para a parte de **Conexão de instância do EC2** e logo abaixo você encontrará o **Nome de usuário** e este é o utilizado para fazer o login

- Após estes passos, é esperado que a conexão tenha sido sucedida e encontre uma tela como essa:

    ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/39521efc-014e-433a-94a6-87f236c0f14b)




# Configurações do Requisitos Linux

## Configuração do NFS 

Para configurarmos um NFS (Network File System), iremos antes configurar um EFS ( Elastic File System). Pelo motivo de que o EFS está disponiveis para instâncias EC2 e utiliza o protocolo NFS, além de que com o EFS, é possível que várias instâncias conectem simultaneamente ao mesmo sistema de arquivos, sendo este um diferencial interessante pensando em escalabilidade do projeto.

### Configuração do EFS

- Busque o serviço **EFS** na barra de pesquisa do console AWS

- Clique em **Criar sistema de arquivos**.

- Na próxima tela, se preferir, coloque um nome, no caso da atividade foi colocado "EFS" para facilitar a identificação 

- No próximo campo, selecione a VPC que está anexada a sua instância e clique em **Criar**

- Com o EFS criado, clique nele e depois clique em **Visualizar detalhes** no canto superior direito

- Nesta nova página, encontre **Pontos de acesso**, clique nele. Se preferir, adicione um nome e clique **Criar ponto de acesso** no final da página

- Na página inicial do EFS, encontre **Rede** próximo do *Pontos de Acesso* acessado anteriomente e clique nele.

- Clique em **Gerenciar** (estará do lado esquerdo logo após clicar em Rede). E coloque o seu grupo de segurança criado para as Zonas de Disponibilidades apresentadas e clique em **Salvar** no final da página

- Retornado a página de detalhes do EFS, copie a informação que está abaixo do campo **Nome de DNS**, iremos utiliza-la posteriormente

- Neste momento iremos retornar ao Putty. Caso ao clicar e ele aparecer uma mensagem : *Network error: Software caused connection abort*, basta clicar com o botão direito na barra e clicar em **Restart Session**, como na foto abaixo e logar novamente com o usuário (isto evita ficar realizando a autenticação novamente, mas fica a seu critério):
  
  ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/a78cda39-0710-4b62-b734-743a9ef9877a)


- No terminal do Putty, execute os comandos abaixo para instalação do pacotes das ferramentas do EFS:

       sudo yum -y install amazon-efs-utils

---

- Retomando a configuração do NFS, ainda no terminal do Putty, foi realizada a criação de um diretório NFS com o comando:

      sudo mkdir /nfs
  
- Em seguida, montou-se um sistema de arquivos EFS com aquele **Nome de DNS** copiado, com o comando abaixo:

      sudo mount -t efs fs-0ef31a5a2cd4d79b2.efs.us-east-1.amazonaws.com /nfs

  Uma descrição geral dos comandos existentes nesta linha de comando acima comando é:

    - *mount* → comando para montar/conectar um sistema de arquivos
    - *-t efs* → é uma opção do comando mout para especificar o tipo de arquivo, no caso  um arquivo EFS
    - *efs fs-0ef31a5a2cd4d79b2.efs.us-east-1.amazonaws.com* → é um endereço de DNS copiado da página de detalhes do EFS.
         >**Warning** Importante lembrar que cada instância tem um endereço diferente, então copie o endereço referente a sua instância
    - */nfs* → é o diretório de destino onde o sistema EFS será montado
 
### Criaçao do arquivo com nome dentro do diretório NFS

Para esta criação, utilize o comando abaixo:

     sudo mkdir /nfs/katia

## Configuração do Apache

- Seguindo no terminal do Putty conectado à sua instância via SSH, digite os seguintes comandos:
  - Para instalação do Apache:

        sudo yum install httpd
  - Para inicializá-lo:

        sudo systemctl start httpd
  - Para habilitá-lo para execução:

        sudo systemctl enable httpd
        
  - Para verificar se ele está realmente ativo:

        sudo systemctl status httpd

- Se aparecer *running* como na imagem abaixo, significa que o serviço está ativo.
  
    ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/f6cf1e4f-64bb-4010-bc5e-4ce2c0aa8fbc)

- Sendo assim, é possivel ser acessado atráves do IP público da sua instância em um navegador
  
    ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/5a0bab35-5268-4a38-8658-3ed4e8a3ed12)

- Caso queira parar o serviço, digite o comando abaixo:

      sudo systemctl stop httpd

## Configuração do Script para validação dos serviços

Para criação do diretório para salvar o script:

    sudo mkdir /nfs/katia/status_apache

Para entrar neste diretório criado:

     cd /nfs/katia/status_apache

Para criação do arquivo de Script em formato .sh:

    sudo nano check_status.sh

Escreva o código abaixo dentro deste arquivo:

     #!/bin/bash
    
    DATE=$(date '+%d/%m/%Y %H:%M:%S')
    SERVICE="httpd"
    
    if systemctl is-active --quiet httpd; then
   		STATUS="Online"
    		MESSAGE="O Apache está rodando :)"
          echo "$DATE- $SERVICE - $STATUS - $MESSAGE" >> /nfs/katia/status_apache/log_online.txt
    else
    		STATUS="Offline"
   		MESSAGE="O Apache está offline :/."
          echo "$DATE- $SERVICE - $STATUS - $MESSAGE" >> /nfs/katia/status_apache/log_offine.txt
    fi

Para sair deste arquivo, basta apetar CTRL + X, em seguida Y ( de Yes) e após isto dê um Enter

É necessário permitir a execução deste arquivo de Script com o comando: 

    sudo chmod +x check_status.sh

Execute o arquivo com o comando:

      sudo ./check_status.sh

Execute os comandos abaixo para verificar os arquivos de saída respectivo que deseja consultar:
  - Para verificar o estado Offline:
    
        cat log_offline.txt

 - Para verificar o estado Online:
    
        cat log_online.txt

### Configuração da execução automatizada

Para a automatização da execução, foi utlizado o **cron**, um servidor do Linux que permite agendar execuções de tarefas de forma atuomática e repetitiva. Portanto com a criação da automatização, foi utilizado o comando abaixo para editar um arquivo *crontab* 

    crontab -e 
    
Dentro do arquivo, aperte a tecla "i" para poder escrever e digite a linha abaixo para execução a cada 5 minutos:

    */5 * * * * /nfs/katia/status_apache/check_status.sh
  
Para sair do arquivo aperte a tecla "Esc", em seguida digite **:wq** e aperte a tecla Enter 

É esperado que após estes passos seja possível realizar uma execução automatizada do script criado

Para verificação se a automação ocorreu bem, aguarde 5 minutos e realize novamente o comando de cat para os arquivos.

