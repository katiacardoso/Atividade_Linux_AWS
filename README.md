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

# Criação da Instância EC2

- Logue na sua conta da AWS e selecione o serviço de EC2 no console ( caso não esteja visível logo de cara, é possível pesquisar na barra de pesquisa logo acima e DICA: já favorita ele na estrelinha ao lado do nome ao pesquisar, pois ele será bastante utilizado nesta documentação).
- Na nova janela aberta, clique em **Executar Instâncias** e os jogos irão começar
- Na sessão de **Nomes e tags** é importante adicionar as Tags que foram instruídas para criação da instância ( porque caso contrário, dá erro de criação :)). Portanto é recomendado adicionar as chaves: Name, Project e CostCenter, e seus valores respectivos. Outro ponto importante que não deve ser esquecido é o acréscimo do **Tipo de Recurso**, que se encontra ao lado do direito do valor, o tipo: *Volume*, ficando mais ou menos assim:
  
  ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/cdbc1d55-1b8c-457e-abbc-9d83d4aedfd1)

- Na sessão **Imagens de aplicaçao e de sistema Operacional (Imagem de máquina da Amazon)** é o momento de escolher a imagem que foi determinada, neste caso: Amazon Linux 2. Ao encontra-lá na listagem de Imagem de máquina da Amazon (AMI), basta selecionar e continuar o processo

  ![image](https://github.com/katiacardoso/Atividade_Linux_AWS/assets/91233884/2405bb39-8c81-44a5-9ae7-943388246823)

 - Abaixo é encontrada a sessão ** Tipo de instância** na qual é o momento de escolher o tipo de instância que foi previamente determinado, neste caso: t3.small. Basta encontrar na listagem de Tipo de instância, selecionar e seguir com o processo

 - Em seguida esta presente a sessão **Par de Chaves(login)**, na qual é o momento de gerar um par de chaves para conectar com segurança à esta instância que está sendo criada. Algumas questões para atenção:
    - na atividade é solicitada uma *geração de chave pública para acesso ao ambiente*. Para fazer isto via Windows, é possível utilizar o PuttyGen e depois acessar via Putty. Porém para este acesso, utiliza-se a chave privada para autenticação, então em tese, ela só é gerada mas utilizada. Contudo, nesta documentação: https://repost.aws/pt/knowledge-center/ec2-ppk-pem-conversion comenta sobre o uso no Linux, então como em tese temos uma máquina Oracle Linux instalada, seria interessante tentar locar por ela e fazer esta geração. Mas por hora, a solução foi implementada com chave privada e acesso via Putty e quem sabe em posteriores atividades, a documentação aqui indicada auxilie.
    - sobre o tipo .pem e .ppk : é recomendado que você crie a chave no formato .pem, mesmo que você irá acessar pelo Putty (depois transforma ela em privada com o PuttyGen), pois futuramente é possível utilizar este mesmo par de chaves para o teste em máquinas Linux. - verificar nome do par de chaves no console AWS
    - Sendo assim, foi criado um par de chaves novos, clicando em "Criar novo par de chaves" que se encontra dentro da sessão, adicionou-se um nome, deixou o tipo RSA e manteve o formato .pem . Clique em **Criar par de chaves** e salve o arquivo gerado ( apareça na sua pasta de Downloads da máquina local possivelmente) em um local seguro. 
    - 
    




