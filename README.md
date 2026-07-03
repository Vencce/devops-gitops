# GitOps - Infraestrutura e Deploy Contínuo

## Descrição
Este é o repositório central da arquitetura GitOps do projeto acadêmico desenvolvido no IFC - Campus Araquari. Ele armazena os manifestos declarativos do Kubernetes (YAML), atuando como a única fonte da verdade (Single Source of Truth) para o ambiente de produção.

## Arquitetura
O ambiente de execução está hospedado na AWS utilizando instâncias EC2 gerenciadas via Terraform, com um cluster K3s em operação. A ferramenta ArgoCD monitora as modificações aprovadas neste repositório e assegura que a infraestrutura provisionada no cluster seja exatamente idêntica aos manifestos armazenados aqui.

## Estrutura de Manifestos
* `frontend.yaml`: Define o Deployment com escalabilidade (3 réplicas) e o Service da interface gráfica.
* `backend.yaml`: Define o Deployment e o Service da API, injetando as variáveis de ambiente necessárias.
* `postgres.yaml`: Provisiona o Deployment e o Service do banco de dados relacional.
* `ingress.yaml`: Contém as regras do controlador Traefik para capturar requisições HTTP na porta 80 e rotear o tráfego de rede entre o Frontend e a API de forma inteligente.
