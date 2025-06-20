# TODO

- [ ] Decidir sobre a inclusão do **Lynis** no playbook (instalação e execução).
- [ ] Gerar o playbook Ansible definitivo contendo as etapas 1-9 já aprovadas.
- [ ] Testar o playbook em ambiente de staging antes de produção.
- [ ] Documentar todas as variáveis de entrada (ex.: senha do usuário *deploy*).
- [ ] Verificar desempenho da swap após implantação e ajustar tamanho se necessário.
- [ ] Avaliar a necessidade de coletar métricas com **sysstat**.
- [ ] Integrar logs/métricas com **Prometheus** e **Grafana**.
- [ ] Configurar alertas de e-mail para *Fail2ban*.
- [ ] Revisar e ajustar as regras do **auditd** conforme requisitos de compliance.
- [ ] Transformar o playbook em uma *role* Ansible reutilizável.
- [ ] Configurar integração contínua com **Molecule** e **Testinfra** para testar a role.
- [ ] Provisionar servidor SMTP (ou serviço SaaS) para notificações do *Fail2ban*. 