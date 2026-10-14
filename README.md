# Simbiosia — construção

Pacote de construção preparado para a segunda consultoria de 14/10/2026.

- Ler [fase atual](04_fase-atual/fase.md) e [SPECs](04_fase-atual/specs/00-INDICE.md).
- Alvo de construção: 07-sistemas/funil-mkt/. Fábrica em 07-sistemas/fabrica-listas/ é referência de reuso; será incorporada ao Funil pelas tasks.
- Seis SPECs e 19 tasks da fase 1, abertas; UUIDs e visibilidade interna do portal preservados. Código ainda não alterado pela preparação.
- Escolher uma task elegível e sua SPEC; confirmar ambiente TESTE/acesso, produzir prova e receber teste humano antes da próxima.
- APIs externas a partir da fase 3; operação após validação da fase 5. Não habilitar envio durante esta fundação.
- Repositório operacional privado: https://github.com/drkgod/simbiosia. Contém somente a fase atual e os projetos de código, sem o plano privado ou fases futuras.

## Obter o projeto completo

```sh
git clone --recurse-submodules https://github.com/drkgod/simbiosia.git
```

Os dois projetos em `07-sistemas/` são submódulos dos repositórios de origem. Em um clone já existente, execute `git submodule update --init --recursive`. A construção altera o Funil; a Fábrica permanece referência de reuso.
