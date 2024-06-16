## Projeto Final Programa de Estágios DevSecOps - CompassUOL


Equipe:

[![Adenilson konzelmann](https://img.shields.io/badge/Adenilson%20Konzelmann-gray?style=flat-square&logo=github&logoColor=white&logoWidth=20&link=https://github.com/Adenilson365)](https://github.com/Adenilson365)
[![Gustavo Pedoni](https://img.shields.io/badge/Gustavo%20Pedoni-gray?style=flat-square&logo=github&logoColor=white&logoWidth=20&link=https://github.com/Gustavopedoni1)](https://github.com/Gustavopedoni1)


### Objetivos 
- Desenvolver a arquitetura para um eCommerce usando as melhores práticas de Devops
  - Ambiente Kubernetes
  - Banco de dados PaaS MySQL
  - Balanceamento de carga 
  - Segurança

### Diagrama técnico - Infraestrutura AWS

![Diagrama Tecnico Ambiente AWS](./assets/diagrama_arquitetura.png)


### Diagrama da Migração do Banco de Dados On-Premisse para AWS RDS
![Diagrama da Migração do Banco de Dados](./assets/migracao_bd.png)

## Recursos usados no projeto:

### Pipeline CI/CD

- <p> AWS CodePipeline</p>  Para gerenciar a pipeline de deploy
- <p> AWS CodeCommit</p>  Como repositório e versionamento de código, nessa arquitetura é onde recebemos o entregável da equipe dev
- <p>AWS CodeBuild</p>  Para Converter o código em imagem docker, e depois realizar o deploy no cluster EKS via kubectl
- <p> AWS ECR</p>  Como registry privado para as imagens Docker.

### Migração do Banco de Dados On-Premisse para AWS RDS

- <p>VPN Connection</p>  Prover segurança na conexão MySQL local para o AWS DMS
- <p>DMS</p>  Serviço de migração de banco de dados da AWS, para replicar em tempo real o MySQL On-Premisse para RDS MySQL na nuvem AWS, objetivando menor janela de manutenção.
- <p>AWS RDS MySQL</p> Serviço de banco de dados SQL na nuvem AWS, usamos como multi-az para failover (Quando um banco cai outro assume em segundos).
- Após migração completa a applicação para a responder na nuvem AWS

### Arquitetura da Aplicação 

> Optamos por um cluster de EKS em multi-az, com subnets totalmente privadas, dessa forma provendo alta disponíbilidade e segurança a aplicação. A aplicação recebe tráfego a partir de um load balancer de camada 7 aberto para internet, os servidores são gerenciados usando kubernetes,  acessam internet via NAT gateway.

- <p>Route 53</p>  Hospeda nossas zonas de DNS
- <p>Cloudfront</p>  Fornece CDN ( Cache de borda para acelerar a entrega de conteúdo ao usuário da aplicação)
- <p>AWS S3</p>  Para armazenar os estáticos da aplicação
- <p>Load Balancer de aplicação</p>  Para fazer o balanceamento de carga e verificar a integridade dos servidores
- <p>WAF - Web Application Firewall</p>  Firewall de aplicação para proteger o Load Balancer de ataques, como SQL injection, DDoS entre outros...
- <p>ACM</p>  Para fornecer certificado SSL, provendo conexão segura do usuário para a aplicação.
- NAT Gateway</p>  Para permitir que os servidores saiam para a internet mas não sejam accessíveis da internet
- <p>EC2</p>  Servidores Linux
- <p>Auto Scaling</p>  Para fornecer elasticidade a infraestrutura, permitindo que ela se adapte rápidamente a demanda da aplicação, adicionando ou removendo servidores conforme demanda, Escalabilidade Horizontal.
- <p>EKS</p>  Serviço de Kubernetes gerenciado da AWS, fornecendo node Master totalmente gerenciado.
- <p>EFS</p>  Sistema de NFS para fornecer persistência de volume para nossos pods de kubernetes.

### Serviços de Segurança e gestão 

- <p>IAM </p>Configurar e gerenciar regras de acesso para o Ambiente AWS, tanto de Usuários quanto de um recurso para o outro.
- <p>CloudWatch</p>  Fornecer observabilidade do cluster, e também do ambiente AWS e seus recursos, coletando logs, métricas e gerando alertas em cima dessas métricas.
- <p>SNS</p>  Serviço de notificação, a partir dos alertas do cloudwatch envia notificação para o responsável. Pode trabalhar junto com o cloudwatch e com cost Explorer
- <p>AWS Config</p> Permite acompanhar as alterações realizadas nos recursos aws, permitindo controles de conformidade e rastreabilidade
- <p>Secrets Manager</p>  Responsável por gerenciar as credenciais do RDS, permitindo rotacionar essas credencias conforme configuração, e não sendo necessário codificar na aplicação.
- <p>Cost Explorer</p>  Usado para acompanhar o financeiro da infra AWS, permitindo acompanhar e otimizar a arquitetura sob a ótica de custos. 

> Além disso foi proposto um plano de suporte visando acompanhar a aplicação e ajustar seus recursos de acordo com o crescimento de demanda da aplicação e análise do seu comportamento, visando propor estratégias de otimização de custos e infra, seguindo o well-architected framework


