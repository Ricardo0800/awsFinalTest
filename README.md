# Aplicativo Web Escalável na AWS

## 📋 Informações do Projeto

**Disciplina:** Programação Web 2  
**Professor:** [IFB - Instituto Federal de Brasília]  
**Turma:** 3º Ano A  
**Alunos:**
- Guilherme Rodrigues Cunha
- Ricardo Felix Guedes Silva Nonato

---

## 🎯 Objetivo

Desenvolver uma aplicação web escalável utilizando infraestrutura na nuvem AWS, demonstrando conhecimento prático em arquitetura de microserviços, balanceamento de carga e alta disponibilidade.

---

## 🏗️ Arquitetura da Infraestrutura

### Componentes da Solução

A arquitetura foi projetada seguindo as melhores práticas de AWS para escalabilidade, alta disponibilidade e segurança:

```
┌──────────────────────────────────────��──────────────────────┐
│                        USUÁRIOS                              │
│                      (Internet)                              │
└────────────────────────┬────────────────────────────────────┘
                         │
                    ┌────▼────┐
                    │ LOAD     │
                    │BALANCER  │
                    │(ELB)     │
                    └────┬────┘
                         │
        ┌────────────────┴────────────────┐
        │                                  │
   ┌────▼─────┐                     ┌────▼─────┐
   │   EC2     │                     │   EC2    │
   │  (AZ A)   │                     │  (AZ B)  │
   │ Subnet    │                     │ Subnet   │
   │ Público   │                     │ Público  │
   └────┬─────┘                     └────┬─────┘
        │                                 │
   ┌────▼─────────────────────────────────▼────┐
   │  ELASTIC LOAD BALANCER (ELB/ALB)          │
   │  - Distribuição de tráfego                │
   │  - Health checks                          │
   └────┬──────────────────────────────────────┘
        │
   ┌────▼──────────────────────────────────────┐
   │  VPC (Virtual Private Cloud)              │
   │  CIDR: [Configurado]                      │
   │                                            │
   │  ┌─────────────────┐  ┌────────────────┐ │
   │  │   PUBLIC        │  │   PUBLIC       │ │
   │  │   SUBNET (AZ A) │  │  SUBNET (AZ B) │ │
   │  │                 │  │                │ │
   │  │  ┌───────────┐  │  │  ┌───────────┐ │ │
   │  │  │  NAT/IGW  │  │  │  │ NAT/IGW   │ │ │
   │  │  └───────────┘  │  │  └───────────┘ │ │
   │  └────────┬────────┘  └────────┬────────┘ │
   │           │                    │           │
   │  ┌────────▼────────┐  ┌───────▼────────┐ │
   │  │  PRIVATE        │  │ PRIVATE        │ │
   │  │  SUBNET (AZ A)  │  │ SUBNET (AZ B)  │ │
   │  │                 │  │                │ │
   │  │  ┌───────────┐  │  │  ┌───────────┐ │ │
   │  │  │ APP       │  │  │  │ APP       │ │ │
   │  │  │ SERVER    │  │  │  │ SERVER    │ │ │
   │  │  └───────────┘  │  │  └───────────┘ │ │
   │  │                 │  │                │ │
   │  │  ┌───────────┐  │  │  ┌───────────┐ │ │
   │  │  │   RDS     │  │  │  │   RDS     │ │ │
   │  │  │ (MySQL)   │  │  │  │ (MySQL)   │ │ │
   │  │  └───────────┘  │  │  └───────────┘ │ │
   │  └────────────────┘  └────────────────┘ │
   │                                          │
   └──────────────────────────────────────────┘
```

### Componentes AWS Utilizados

#### 1. **Rede e Conectividade**
- **VPC (Virtual Private Cloud)**: Rede isolada e customizável
- **Subnets Públicas**: Para ALB e NAT Gateway
- **Subnets Privadas**: Para aplicação e banco de dados
- **Availability Zones (AZ A e AZ B)**: Para alta disponibilidade
- **Elastic Load Balancer (ALB)**: Distribuição inteligente de tráfego
- **NAT Gateway**: Acesso à internet para instâncias privadas

#### 2. **Computação**
- **EC2 (Elastic Compute Cloud)**: Servidores web escaláveis
  - Instâncias em múltiplas zonas de disponibilidade
  - Auto Scaling Groups para escalabilidade automática
  - Security Groups para firewall de aplicação

#### 3. **Armazenamento de Dados**
- **RDS (Relational Database Service)**: Banco de dados MySQL gerenciado
  - Replicação Multi-AZ para alta disponibilidade
  - Backups automáticos
  - Failover automático

#### 4. **Segurança**
- **Security Groups**: Controle de tráfego de rede
- **Network ACLs**: Controle adicional de acesso
- **IAM Roles**: Gerenciamento de permissões

---

## 💰 Especificação de Preços (Estimativa)

### Análise de Custo (12 meses)

| Serviço | Custo Mensal | Custo em 12 Meses | Notas |
|---------|-------------|------------------|-------|
| **Amazon EC2** | R$ 5,21 | R$ 62,52 | 2 instâncias t2.micro (free tier elegível) |
| **Amazon RDS** | R$ 28,00 | R$ 336,00 | MySQL db.t2.micro Multi-AZ |
| **Elastic Load Balancer** | R$ 16,44 | R$ 197,28 | ALB com processamento de dados |
| **NAT Gateway** | R$ 45,00 | R$ 540,00 | 1 por zona de disponibilidade |
| **Data Transfer** | R$ 5,00 | R$ 60,00 | Saída de dados estimada |
| **VPC/Outros** | R$ 0,15 | R$ 1,80 | Recursos de rede adicionais |
| **TOTAL ESTIMADO** | **R$ 99,80** | **R$ 1.197,60** | Sem taxa inicial |

### Observações de Custo

✅ **AWS Free Tier**: Alguns serviços no primeiro ano têm cobertura gratuita  
✅ **On-Demand**: Sem compromisso de longo prazo  
✅ **Escalável**: Custos aumentam conforme crescimento da aplicação  
⚠️ **Data Transfer**: Principal custo variável  

---

## 📊 Diagrama de Arquitetura Detalhado

### Fluxo de Requisições

1. **Usuário** acessa a aplicação via internet
2. **ELB (Elastic Load Balancer)** recebe a requisição
3. **Load Balancer** distribui para EC2 em AZ A ou AZ B baseado em:
   - Health checks
   - Balanceamento por camada 7 (HTTP/HTTPS)
4. **Instância EC2** processa a requisição
5. **RDS** fornece acesso ao banco de dados
6. **Resposta** retorna ao usuário através do Load Balancer

### Alta Disponibilidade

- ✅ **Multi-AZ**: Aplicação rodando em 2 zonas de disponibilidade
- ✅ **Auto Scaling**: Adiciona/remove instâncias conforme demanda
- ✅ **RDS Replicação**: Banco de dados replicado para failover automático
- ✅ **Health Checks**: ELB monitora saúde de instâncias
- ✅ **SLA**: 99.99% de disponibilidade

---

## 🚀 Como a Aplicação Funciona

### Stack Tecnológico
- **Frontend**: HTML5, CSS3, JavaScript
- **Backend**: [Linguagem utilizada - PHP/Node.js/Python/etc]
- **Banco de Dados**: MySQL 8.0
- **Infraestrutura**: AWS EC2, RDS, ELB

### Processo de Deploy
1. Aplicação deployada em instâncias EC2
2. Load Balancer distribui requisições
3. Banco de dados centralizado no RDS
4. Logs e monitoramento via CloudWatch

---

## 📹 Apresentação do Projeto

Assista à apresentação completa do projeto para o professor:

🎥 **[Vídeo de Apresentação - YouTube](https://youtu.be/QenzIeEdaa0)**

Neste vídeo, demonstramos:
- Arquitetura da solução
- Componentes AWS utilizados
- Especificação de preços
- Deploy e testes da aplicação
- Métricas de desempenho

---

## 🎓 O que Aprendemos

✅ Arquitetura de aplicações em nuvem  
✅ Balanceamento de carga e alta disponibilidade  
✅ Segurança em redes AWS (Security Groups, subnets privadas)  
✅ Escalabilidade horizontal com Auto Scaling  
✅ Gerenciamento de bancos de dados em nuvem  
✅ Monitoramento e logging em produção  
✅ Estimativa e otimização de custos  

---

## 📚 Recursos Utilizados

- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [AWS RDS Documentation](https://docs.aws.amazon.com/rds/)
- [AWS VPC User Guide](https://docs.aws.amazon.com/vpc/)
- [AWS Elastic Load Balancing](https://docs.aws.amazon.com/elasticloadbalancing/)
- [AWS Pricing Calculator](https://calculator.aws/)

---

## 📝 Conclusão

Este projeto demonstra o uso prático e completo da plataforma AWS para construir uma aplicação web escalável, segura e altamente disponível. A infraestrutura foi projetada seguindo as melhores práticas da indústria, garantindo desempenho, confiabilidade e custo-benefício.

---

**Última atualização:** Junho de 2026  
**Versão:** 1.0

