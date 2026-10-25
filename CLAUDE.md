# Afinador Completo

Afinador de violão de alta precisão, para uso próprio do Sidney (educador musical, SESI-SP). Um único arquivo HTML (`index.html`), publicado pelo GitHub Pages e instalável como app ("Afinador+"). Responda e escreva comentários em **português do Brasil**.

- Site: `https://rapososidney.github.io/afinador-completo/`
- Publicar = `git push` na `main`. O Pages leva 1–2 min, e o navegador pode manter respostas em cache por até 10 min (para conferir antes disso, use `?v=N` na URL).
- Faça commit só quando o Sidney pedir. Mensagens de commit em português.
- Este arquivo veio de `afinador-precisao-violao.html` no repositório antigo `Quizes-Musicais-Teste` (em aposentadoria), usava `afinador.webmanifest` e os ícones `icon-plus-*` — aqui renomeados para o padrão `icon-192.png`/`icon-512.png`/`icon-512-maskable.png`. As regras comuns a todos os projetos estão no `CLAUDE.md` da pasta `Documentos\GitHub`.
- **Cuidado ao instalar**: o app instalado no celular do Sidney apontava para o endereço antigo (`Quizes-Musicais-Teste`). Ele precisa desinstalar o app antigo e instalar a partir deste endereço novo — o `id` do manifest mudou (era um escopo dentro do repositório antigo, agora é `./` deste repositório), então o celular trata como app diferente.

## Regras do repositório

- **Tudo no `index.html`, sem dependências externas**: sem CDN, sem build. Tem que abrir offline.
- App instalável: `manifest.webmanifest` (`id`, `start_url`, `scope` = `./`), `sw.js` (registrado sem escopo explícito) e os ícones `icon-192.png`, `icon-512.png`, `icon-512-maskable.png`.
- Ao adicionar arquivos ao cache, inclua-os em `ARQS` no `sw.js` e troque a versão de `CACHE`.
- **Não usa `localStorage`** — a calibração de inarmonicidade viaja só pela URL (`#cal=...`, ver abaixo), nunca é salva no navegador. Se isso mudar, use o prefixo `afinadorCompleto_`, nunca uma chave genérica — a origem `rapososidney.github.io` é compartilhada com os outros repositórios do Sidney.

## O app (index.html)

Mesma base de detecção do [`afinador`](https://rapososidney.github.io/afinador/) (YIN + refino por pico espectral, imune a inarmonicidade dos parciais), com dois recursos extras pensados para o próprio Sidney:

### Modo harmônico (12ª casa)
Alterna entre medir a **corda solta** (fundamental, f) ou o **harmônico da 12ª casa** (2º parcial, ≈2f). Medir o harmônico tem vantagem prática (nó mais fácil de tocar limpo em certas cordas) e serve de base para a calibração de inarmonicidade abaixo. Há aviso automático se o som captado parecer fundamental enquanto o modo harmônico está ativo (ou vice-versa, por distância em cents ao conjunto de fundamentais vs. harmônicos esperados).

### Calibração de inarmonicidade
Por rigidez da corda, o 2º parcial real não é exatamente 2f — fica um pouco acima (mais em cordas de aço que em nylon). Um assistente guiado (`wiz*`) mede, para cada corda, primeiro a fundamental (f₁) e depois o harmônico (f₂), e grava o desvio de f₂ em relação a 2×f₁ em cents (array `cal[0..5]`, `null` = não calibrado). O modo harmônico usa esse desvio para corrigir o alvo daquela corda específica.
- A calibração **não é salva no navegador**: o botão "Copiar link calibrado" codifica as 6 correções no hash da URL (`#cal=v0,v1,...,v5`, `x` para corda não calibrada) e `loadCalFromHash()` relê isso ao abrir a página. Pensado para o Sidney guardar/recarregar o link, não para persistência automática.
- É mais verificação/demonstração didática do que necessidade prática — a correção é de poucos décimos de cent em nylon, alguns décimos em aço.

### Resto
Igual ao `afinador`: A4 ajustável (400–470 Hz), mostrador em arco (±50 ¢) + medidor fino (±5 ¢), "afinado"/"cravado" pelos mesmos critérios, microfone sem AGC/NS/EC, FFT de 32768 amostras com decimação ×4 para o YIN grosseiro.
