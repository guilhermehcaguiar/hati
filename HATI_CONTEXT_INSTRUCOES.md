# HATI — LEIA ANTES DE CONTINUAR

Este arquivo acompanha o pacote mestre de contexto para ser **extraído na raiz** do projeto HATI. Ele não substitui arquivos `README.md`, `AGENTS.md` ou `CLAUDE.md` já existentes.

## Onde está a documentação

Toda a documentação recebida está em `docs/hati-context/`.

**Principal:** `docs/hati-context/scope/SCOPE_MATRIX.md` (187 decisões e seus refinamentos).
**Fonte:** `docs/hati-context/source/ALETHE_AUDIT_ORIGINAL.txt` (auditoria original integral da Athena).
**DT:** `docs/hati-context/governance/DIRECTIVES_DT.md` (10 decisões aprovadas).
**Exclusivas:** `docs/hati-context/scope/HX_REQUIREMENTS.md` (26 detalhes HX).
**Revisão:** `docs/hati-context/architecture/CROSS_REVIEW.md` (dependências e conflitos).
**Prompt para Athena:** entregue separadamente deste ZIP; não faz parte do pacote de contexto.

## Como usar

1. Extraia o ZIP na pasta raiz do HATI **mantendo os caminhos internos**.
2. Confira se a pasta `docs/hati-context/` está presente.
3. Abra OpenCode no diretório do projeto e escolha Athena.
4. Cole na Athena o **Prompt 02 sem shell**, fornecido separadamente; o ZIP contém somente fontes de contexto.
5. A Athena deve **ler a documentação com ferramentas nativas de leitura, conferir IDs/contagens e preparar apenas documentação**, sem utilizar shell ou terminal. Poderá gravar arquivos com ferramentas nativas de escrita/edição, se autorizadas; caso contrário, deverá fornecer o conteúdo na conversa para salvamento manual.
6. Revise o resultado e confirme o que foi de fato salvo. Somente depois, em outro prompt, discuta arquitetura.

## Atenção

- Não abra o ZIP em uma pasta aninhada adicional; os caminhos do pacote já são relativos à raiz do repositório.
- Evite sobrescrever qualquer arquivo pré-existente se já usou um pacote HATI com estes mesmos nomes.
- Este pacote NÃO contém dependências de software, build, Git history ou token de provedor.
- Não assuma que o projeto já tem implementação; a Athena deve inspecionar o estado real.
- Código, commits, pushes e execução Forge **não** foram autorizados.
