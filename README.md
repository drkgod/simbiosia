# Simbiosia — dois aplicativos separados e sincronizados

Pacote operacional privado em https://github.com/drkgod/simbiosia. Fase 1 revisada conforme escolha P9 de 14/10/2026.

- Ler [fase atual](04_fase-atual/fase.md) e [sete SPECs/TDD](04_fase-atual/specs/00-INDICE.md): 22 tarefas com IDs do portal preservados, nenhuma implementação concluída.
- Alvo ativo [Fábrica](07-sistemas/fabrica-listas): cadastro/base/listas/importação, produtos/réguas e pontuação.
- Alvo ativo [Funil](07-sistemas/funil-mkt): operação comercial/cadências/conversas, atribuição/aprovações, primeira abordagem e bloqueios globais.
- Maestro desenvolve nos dois repositórios durante o mesmo ciclo, por frentes elegíveis e contratos compatíveis. Fábrica não é apenas referência e não será incorporada ao Funil. Cada app mantém seu backend/login.
- SPEC-1-007 fecha IDs globais, autoridade por campo, projeções, revisões, mapeamento e reconciliação. Contrato/fixtures locais nas fases 1 e 2, sem HTTP entre apps; conexão real F3 e mercado após a fase 5.
- Pré-condições/provas/aceite liberam sucessoras; nenhuma configuração/aprovação é inventada. Este repo contém somente fase atual, entregas/notas e projetos de código, sem plano privado ou fases futuras.

## Clonar e trabalhar nos projetos

```sh
git clone --recurse-submodules https://github.com/drkgod/simbiosia.git
```

Em clone existente: git submodule update --init --recursive. Fazer alterações dentro de cada submódulo; commitar/publicar cada projeto no seu remoto autorizado e depois atualizar as referências no repo operacional. Registrar SHA dos dois projetos nos testes; commit do pai não publica automaticamente o código dos filhos.
