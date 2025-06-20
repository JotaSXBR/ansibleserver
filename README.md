# Ansible Hardening Playbook

Este repositório contém um playbook Ansible que aplica boas práticas de hardening e segurança em servidores Ubuntu 24.04.

## 1. Pré-requisitos

1. **Máquina de controle (Linux)**
   * Ubuntu, Debian ou distribuições compatíveis.
   * Python ≥ 3.8.
2. **Ansible**
   ```bash
   sudo apt update && sudo apt install -y ansible
   # ou via pip
   # python3 -m pip install --user ansible
   ```
3. **Acesso SSH** aos servidores-alvo (porta 22, autenticação por senha).

## 2. Estrutura do projeto

```
ansibleserver/
├── playbook.yml          # (a ser criado) hardening completo
├── inventory.ini         # exemplo de inventário simples
├── todo.md               # próximos passos / melhorias
└── README.md             # este arquivo
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

1. Posicione-se na pasta do projeto.
2. Execute:
   ```bash
   ansible-playbook -i inventory.ini playbook.yml \
                    --ask-pass           \
                    --ask-become-pass
   ```
   Explicação das opções:
   * `--ask-pass`        → solicita senha SSH para conectar.
   * `--ask-become-pass` → solicita senha de root/sudo no destino.
3. O playbook pedirá **interativamente** a senha que será atribuída ao usuário `deploy` (variável `deploy_password`). Digite-a duas vezes para confirmação.

> Dica: para um "ensaio" sem mudanças reias, adicione a flag `--check` (modo *dry-run*) e `--diff` para ver as alterações.

## 5. Conteúdo do playbook (versão atual)

As tarefas aprovadas até o momento englobam:

1. Ativar repositório de segurança Ubuntu.
2. Atualizar pacotes, instalar ferramentas de segurança (ufw, fail2ban, auditd, debsums, rkhunter, etc.).
3. Criar usuário `deploy` com privilégios sudo (senha solicitada em tempo de execução).
4. Criar arquivo swap de 4 GB e ajustar `vm.swappiness`.
5. Configurar firewall UFW (deny incoming, permitir 22/80/443).
6. Fortalecer configuração do SSH com `PermitRootLogin no`, etc.
7. Políticas adicionais (core dumps off, login.defs, banner legal, módulos incomuns).
8. Configurar e habilitar auditd com regras customizadas.
9. Configurar Fail2ban.
10. Agendar verificação semanal do rkhunter.

*(Lynis e sysstat estão listados no `todo.md` para futura inclusão/ajuste.)*

## 6. Próximos passos

Consulte `todo.md` para acompanhar tarefas pendentes, como decidir sobre a execução do Lynis, integração com Prometheus/Grafana e transformar o playbook em uma *role* reutilizável.

---

Qualquer dúvida, abra uma *issue* ou contribuição com *pull request*. Boas automações! 