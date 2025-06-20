# Ansible Hardening Playbook

Este repositório contém um playbook Ansible que aplica boas práticas de hardening e segurança em servidores Ubuntu 24.04.

## 1. Pré-requisitos

1. **Máquina de controle (Linux)**
   * Ubuntu, Debian ou distribuições compatíveis.
   * Python ≥ 3.8.
2. **Ansible** (núcleo) e collections adicionais
   ```bash
   sudo apt update && sudo apt install -y ansible
   
   # Instalar as Coleções necessárias (apenas uma vez)
   ansible-galaxy collection install -r requirements.yml
   ```
   *O arquivo `requirements.yml` declara as collections `community.general` e `ansible.posix`, usadas pela role para módulos como `ufw`, `timezone`, `swapfile`, `sysctl`, etc.*
3. **Acesso SSH** aos servidores-alvo (porta 22, autenticação por senha).

## 2. Estrutura do projeto

```
ansibleserver/
├── inventory.ini                  # inventário de hosts
├── playbook.yml                   # playbook enxuto que chama a role
├── requirements.yml               # coleções Ansible necessárias
├── roles/
│   └── hardening_ubuntu/
│       ├── defaults/main.yml      # variáveis padrão
│       ├── files/                 # banner, regras auditd
│       ├── handlers/main.yml      # handlers de serviço
│       ├── tasks/main.yml         # tarefas do hardening
│       ├── templates/             # jail.local do fail2ban
│       └── meta/main.yml          # metadados da role
├── todo.md                        # melhorias futuras
└── README.md                      # este arquivo
```

## 3. Inventário de exemplo (`inventory.ini`)

```ini
[servers]
192.0.2.10 ansible_user=root
198.51.100.15 ansible_user=root
```

*Substitua pelos IPs dos seus servidores. O usuário inicial deve ter privilégios de root (diretamente ou via sudo sem senha).*  
Se você já usar outro usuário, ajuste `ansible_user` e acrescente `ansible_become=true`.

## 4. Executando o playbook

1. Instale as collections (se ainda não tiver feito):
   ```bash
   ansible-galaxy collection install -r requirements.yml
   ```
2. Posicione-se na pasta do projeto e execute:
   ```bash
   ansible-playbook -i inventory.ini playbook.yml \
                    --ask-pass           \
                    --ask-become-pass
   ```
   * `--ask-pass`        → solicita senha SSH para conectar.
   * `--ask-become-pass` → solicita senha de root/sudo no destino.
3. Você será solicitado a digitar **duas vezes** a senha que será atribuída ao usuário `deploy` (variável `deploy_password`).

> Dica: para um teste sem mudanças, adicione as flags `--check --diff`.

## 5. Conteúdo principal da role

1. Repositório de segurança `noble-security` habilitado.
2. Atualização de pacotes (dist-upgrade) + instalação dos pacotes essenciais (ufw, fail2ban, auditd, rkhunter, …) com `state: latest`.
3. Usuário `deploy` com sudo (senha fornecida em execução).
4. Swapfile via módulo `community.general.swapfile` (4 GB) e `vm.swappiness=10`.
5. Firewall UFW: políticas padrão e liberação das portas 22/80/443.
6. Hardening do SSH (root login off, limites, reload validado).
7. Políticas de kernel e sistema (core dumps off, login.defs, banner).
8. Regras do auditd (99-custom.rules) aplicadas e serviço reiniciado.
9. Fail2ban configurado para proteger SSH.
10. rkhunter atualizado, baseline criado e cron semanal.
11. Fuso horário `America/Sao_Paulo` e NTP (chrony) ativo; assert de sincronização.

## 6. Próximos passos

Consulte `todo.md` para acompanhar tarefas pendentes, como decidir sobre a execução do Lynis, integração com Prometheus/Grafana e transformar o playbook em uma *role* reutilizável.

---

Qualquer dúvida, abra uma *issue* ou contribuição com *pull request*. Boas automações! 