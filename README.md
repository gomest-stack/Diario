# Diário Escolar (Web System)

Sistema web leve e minimalista para gestão escolar pessoal de professores.

## Dados de Acesso para Teste
- **Login:** `2026`
- **Senha:** `2026`

## Armazenamento de Dados
Os dados são armazenados localmente no navegador via **`localStorage`**. As informações permanecem registradas mesmo se a página for recarregada ou o navegador fechado.

> **Nota de Segurança:** Esta versão local é destinada ao uso individual do professor em seu próprio dispositivo. Para uso em rede corporativa com múltiplos usuários, recomenda-se a migração para um backend dedicado.

## Como Cadastrar Novas Turmas
1. Na tela principal (**Minhas Turmas**), clique no botão **+ Nova Turma**.
2. Preencha os campos obrigatórios (Nome, Disciplina, Turno e Ano Letivo).
3. Clique em **Salvar Turma**.

## Como Alterar a Fórmula das Médias
No arquivo `script.js`, localize o objeto de configuração no topo do arquivo:

```javascript
const CONFIG = {
    PASSING_GRADE: 6.0,
    calculateAverage: function (av1, av2, trabalho, recuperacao) {
        // Altere a fórmula conforme a regra da sua instituição aqui
        return (av1 + av2 + trabalho) / 3;
    }
};
