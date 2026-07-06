Trabalho Final - Fundamentos de DevOps

Aluno: Vitor Ferreira
Curso: Bacharelado em Sistemas de Informação (5º Período) - Instituto Federal Catarinense (Campus Araquari)
Disciplina: Fundamentos de DevOps

1. Introdução
Este projeto consiste no desenvolvimento de uma infraestrutura automatizada e na implementação de uma esteira de deploy contínuo utilizando práticas GitOps. O objetivo é subir uma aplicação (Backend em Python, Frontend em HTML/CSS/JS e Banco de Dados PostgreSQL) garantindo alta disponibilidade, automação e roteamento reverso.

2. Escolha do Ambiente
Ambiente: Nuvem (AWS Learner Lab).

Justificativa: A nuvem proporciona flexibilidade, escalabilidade sob demanda e integração perfeita com ferramentas de Infraestrutura como Código (IaC).

Instâncias Criadas: Foram provisionadas 4 instâncias EC2 rodando Ubuntu:

Control Plane: Responsável pelo gerenciamento do cluster (Master).

Workers (3 instâncias): Nós de trabalho para execução dos contêineres.

3. Provisionamento
Ferramentas: Terraform (para criação da infraestrutura, provisionamento das instâncias EC2 e configuração dos Security Groups na AWS).

Desafios e Soluções: O gerenciamento do ambiente no AWS Learner Lab exige lidar com a renovação de credenciais e IPs dinâmicos a cada nova sessão, o que foi mitigado pelo uso de variáveis de ambiente no terminal e aplicação direta das saídas (outputs) do Terraform para conexão SSH.

4. Cluster Kubernetes
Ferramenta: Utilizou-se o K3s por ser uma distribuição leve do Kubernetes, ideal para ambientes de estudo e produção com recursos otimizados.

Configuração: O cluster possui 1 Control Plane e 3 Workers integrados. O roteamento de entrada é feito nativamente pelo Ingress Controller Traefik.

5. GitOps com ArgoCD
Instalação: O ArgoCD foi implantado diretamente no cluster K3s.

Estratégia GitOps: A ferramenta monitora um repositório exclusivo para infraestrutura (devops-gitops). Qualquer alteração nos manifestos YAML de Deployment e Service reflete automaticamente no cluster através das políticas de Auto-Sync, Prune e Self-Heal, garantindo que o estado do cluster seja sempre idêntico ao versionado no Git.

6. Integração Contínua e Entrega Contínua (CI/CD)
Foi implementada uma esteira completa utilizando GitHub Actions para elevar o nível de automação:

Automação (CI): Sempre que um novo código é enviado para a branch principal (main) dos repositórios da aplicação, o workflow dispara automaticamente o build da nova imagem Docker e realiza o push versionado para o Docker Hub.

Automação (CD): No mesmo fluxo, a Action acessa o repositório GitOps e atualiza automaticamente a tag da imagem no arquivo YAML correspondente.

Segurança: Utilizou-se GitHub Secrets para armazenar as credenciais (DOCKER_USERNAME, DOCKER_PASSWORD) e o token de acesso pessoal (PAT_TOKEN), garantindo a comunicação segura entre os repositórios sem expor dados sensíveis.

7. Aplicação e Acesso
A aplicação é dividida em microsserviços interligados:

Backend: API em Python, operando na porta 8000 internamente.

Frontend: Interface em HTML/CSS/JS, operando na porta 80 internamente.

Banco de Dados: PostgreSQL 15-alpine (porta 5432).

Como Acessar (Roteamento via Ingress):

Frontend: http://<IP-DO-CONTROL-PLANE>/

Backend (API): http://<IP-DO-CONTROL-PLANE>/api

ArgoCD (Painel de Gerenciamento): https://<IP-DO-CONTROL-PLANE>:30080 (Exposto via NodePort).

8. Conclusão
O desenvolvimento do projeto consolidou a importância de tratar a infraestrutura como código (IaC) e a eficácia do paradigma GitOps na modernização do ciclo de vida do software. Um dos principais desafios técnicos superados envolveu a transição do ambiente de desenvolvimento local para a nuvem, exigindo o ajuste das requisições do frontend (que inicialmente apontavam para o localhost da máquina) para que passassem a utilizar o roteamento dinâmico correto do Traefik apontando para a API no cluster. A implementação de uma esteira completa de CI/CD (GitHub Actions + ArgoCD) permitiu um fluxo de trabalho profissional, onde cada alteração no código é construída, versionada e implantada de forma totalmente autônoma, resultando em um processo de entrega contínua sem interrupção de serviço (Zero Downtime).
