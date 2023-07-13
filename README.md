# Atividade_Linux_AWS

### Estágio Compass.UOL - Trilha Cloud & DevSecOps 

Katia Lorena Cardoso Sena

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

Tabela de conteúdos
=================
<!--ts-->
   * [Criação da Instância EC2](#Criação-da-Instância-EC2)
   * [Tabela de Conteudo](#tabela-de-conteudo)
   * [Instalação](#instalacao)
   * [Como usar](#como-usar)
      * [Pre Requisitos](#pre-requisitos)
      * [Local files](#local-files)
      * [Remote files](#remote-files)
      * [Multiple files](#multiple-files)
      * [Combo](#combo)
   * [Tests](#testes)
   * [Tecnologias](#tecnologias)
<!--te-->

<div id='Criação-da-Instância-EC2'/> 
  
# Criação da Instância EC2

- Logue na sua conta da AWS e selecione o serviço de EC2 no console ( caso não esteja visível logo de cara, é possível pesquisar na barra de pesquisa logo acima e DICA: já favorita ele na estrelinha ao lado do nome ao pesquisar, pois ele será bastante utilizado nesta documentação).
- Na nova janela aberta, clique em **Executar Instâncias** e os jogos irão começar
- Na sessão de **Nomes e tags** é importante adicionar as Tags que foram instruídas para criação da instância ( porque caso contrário, dá erro de criação :)). Portanto é recomendado adicionar as chaves: Name, Project e CostCenter, e seus valores respectivos. Outro ponto importante que não deve ser esquecido é o acréscimo do **Tipo de Recurso**, que se encontra ao lado do direito do valor, o tipo: *Volume*, ficando mais ou menos assim:
  
  ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/cdbc1d55-1b8c-457e-abbc-9d83d4aedfd1)

- Na sessão **Imagens de aplicaçao e de sistema Operacional (Imagem de máquina da Amazon)** é o momento de escolher a imagem que foi determinada, neste caso: Amazon Linux 2. Ao encontra-lá na listagem de Imagem de máquina da Amazon (AMI), basta selecionar e continuar o processo

  ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/2405bb39-8c81-44a5-9ae7-943388246823)

 - Abaixo é encontrada a sessão **Tipo de instância** na qual é o momento de escolher o tipo de instância que foi previamente determinado, neste caso: t3.small. Basta encontrar na listagem de Tipo de instância, selecionar e seguir com o processo

 - Em seguida esta presente a sessão **Par de Chaves(login)**, na qual é o momento de gerar um par de chaves para conectar com segurança à esta instância que está sendo criada. Algumas questões para atenção:
    - <details>
      <summary>Comentários pessoais sobre problemas nesta parte</summary>
      
      -   Na atividade é solicitada uma *geração de chave pública para acesso ao ambiente*. Para fazer isto via Windows, é possível utilizar o PuttyGen e depois acessar via Putty. Porém para este acesso, utiliza-se a chave privada para autenticação, então em tese, ela só é gerada mas utilizada. Contudo, nesta documentação: https://repost.aws/pt/knowledge-center/ec2-ppk-pem-conversion comenta sobre o uso no Linux, então como em tese temos uma máquina Oracle Linux instalada, seria interessante tentar locar por ela e fazer esta geração. Mas por hora, a solução foi implementada com chave privada e acesso via Putty e quem sabe em posteriores atividades, a documentação aqui indicada auxilie.
      
      -   Sobre o tipo .pem e .ppk : é recomendado que você crie a chave no formato .pem, mesmo que você irá acessar pelo Putty (depois transforma ela em privada com o PuttyGen), pois futuramente é possível utilizar este mesmo par de chaves para o teste em máquinas Linux. - verificar nome do par de chaves no console AWS
      </details>
   
    - Foi criado um par de chaves novos, clicando em "Criar novo par de chaves" que se encontra dentro da sessão, adicionou-se um nome, deixou o tipo RSA e manteve o formato .pem . Clique em **Criar par de chaves** e salve o arquivo gerado ( apareça na sua pasta de Downloads da máquina local possivelmente) em um local seguro. 
 
 - Na próxima sessão: **Configurações de rede** deve ser realiza a conexão obrigatória com uma VPC, subnet e um grupo de segurança. Então por partes:
      <details>
      <summary> Caso não tenha uma VPC criada para realizar a associação, é necessário criar uma seguindo estes passos: </summary>
     
        
      - Na barra de pesquisa na parte superior, pesquise pelo serviço de VPC (recomedno também que favorite ele na estrelinha pois será de grande uso no processo :)) e clique para abrir o mesmo.
     
      - Clique em **Criar VPC**

      -  Na opção de **Recursos a serem criados**, mude de "Somente VPC" para "VPC e muito mais", pois assim já virá configurado automaticamente subnets, internet gateways, e assim por diante
  
      - No campo abaixo, pode acionarr um nome, no meu caso foi gerado um automaticamente então segui com ele
     
      - No campo de Bloco CIDR IPv4 eu deixei com /16, porém pode ser de boa prática deixar menos endereços IP's disponíveis pois nesta atividade não iremos utilizar 65.536 IP's :)
     
      - No campo Bloco CIDR IPv6, deixou-se marcado nenhum, pois vamos apenas de IPv4 mesmo
     
      - O restante deixei como padrão que veio, contudo se atente a criação de subnets públicas (mais detalhes no comentário abaixo) e apertei em "criar" no final da página
              - <details>
                 <summary>Comentários pessoais sobre problemas nesta parte</summary>
                Pois em uma das instâncias de testes criadas anteriormente, ocorreu um erro na porta 443 quando me conectei ao Putty e tentava executar qualquer comando yum. Pelas minhas pesquisas, seria necessário um NAT Gateway para funcionar, mas achei um pouco arriscado pois ele tem uma comunicação via única, ou seja, apenas acesso a internet e não permitir que a internet acesse ela. Portanto fiquei insegura e resolvi criar uma outra instância em uma subnet pública, visto que após criada não tem como mudar de subnet.
                </details>

      </details> 
      
   - Com a VPC já criada, ( se você teve que criar a VPC neste mesmo momento da criação da instância, basta apertar no ícone ao lado do para carregar as novas configurações), selecione a VPC e a subnet quue será referente a esta instância. Uma atenção especial para **coloca-lá em uma subnet pública, pois nesta documentação ela está alocada em uma pública**
   - No campo **Atribuir IP público automaticamente** foi mantido o padrão "desabilitado"
   - No campo **Firewall(grupos de segurança)** decidi selecionar a opção de **Selecionar um grupo de segurança existente** pois assim já realizei as configurações de portas de entrada solicitadas.
   - <details>
      <summary> Caso não tenha um grupo de segurança criado para realizar a associação, é necessário criar um seguindo estes passos: </summary>
     
        
      - Na barra de pesquisa na parte superior, pesquise pelo serviço de EC2, ou se você estiver favoritado ele desde o primeiro uso, ele estará na parte superior da tela, mais ou menos assim:
      - adicionar imagem serviços favoritados 
     
      - Na aba esquerda, encontre **Grupo de segurança** e clique nele 

      -  Clique em **Criar grupo de segurança** ? confirmar se é isso mesmo
  
      - No campo abaixo, pode acionar um nome e apertar para gerar ? Confirmar se é isso mesmo
     
      - Verificar exatamente a ordem, mas tem que adiconar as portas de entradas que foram solicitadas na tarefa, seguem abaixo o modo como ficou configurado:
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
     
      - RESERVA PARA ADICIONAR MAIS E NAO PERDER A FORMATAÇÃO
     
      - RESERVA PARA ADICIONAR MAIS E NAO PERDER A FORMATAÇÃO

      </details> 
   - Caso já tenha um grupo de segurança já existente anteriormente ao criado no passo anterior (lembra-se de adicionar as portas das **Regras de Entradas** , ou acabou de cria-lo anteriormente (lembrando de recarregar na setinha ao lado para subir as novas configurações) este é o momento de seleciona-lo para anexar o mesmo nesta instância que está sendo criada e seguir para o próximo passo.
  
   - Na próxima sessão **Configurar armazenamento** foi realizada basicamente a troca de "8gp2" para "16gp2", volume este que foi especificado na tarefa
   - E assim, chegou-se ao final da criação da Instância EC2, para fins de confirmação, segue um breve resumo das  configurações:
     
     ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/00c8ba32-54af-4118-bf7e-a9c6031d2c97)

   - Clique em Executar Instância e é após um pequeno momento de espera, é esperada uma mensagem de êxito na criação.
  
   - Para verificar se ela realmente está funcional, entre no serviço EC2, caso esteja fora dele, procure na barra lateral esquerda por "Instâncias" e confira se o Status Check (?) está 2/2 e ela está no estado "Executando"
     > 
      > **Note**: Quando a instância não estiver sendo utilizada, não esqueça de alterar o status dela para *Interrompida* para não gerar gastos extras 
          
----



