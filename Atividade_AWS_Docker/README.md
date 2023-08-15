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
