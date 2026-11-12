# shorts-pipeline

Pipeline que transforma um roteiro em vídeo curto pronto para publicar (YouTube Shorts e TikTok), sem edição manual. Ele narra, transcreve, sincroniza legenda palavra a palavra e renderiza o vídeo final.

Está em uso de verdade: alimenta um canal no YouTube que publica todo dia, com média de cerca de 1,2 mil views por Short. Cada vídeo leva uns 10 minutos para ficar pronto.

## Como funciona

```
roteiro (texto)
   │
   ▼
┌──────────────┐   provedor principal de TTS; se falhar,
│   Narração   │   cai automaticamente para o próximo
└──────┬───────┘
       ▼
┌──────────────┐   Whisper rodando local gera a transcrição
│ Transcrição  │   com timestamp de cada palavra
└──────┬───────┘
       ▼
┌──────────────┐   alinha o texto do roteiro com a transcrição
│   Legendas   │   (LCS) para a legenda bater com a fala
└──────┬───────┘
       ▼
┌──────────────┐   composição do vídeo em React com Remotion:
│    Render    │   narração, imagens, legenda animada
└──────┬───────┘
       ▼
   vídeo .mp4 (9:16)
```

### Legendas alinhadas por LCS

O Whisper acerta o tempo das palavras, mas às vezes erra a grafia, principalmente de nomes próprios e termos em inglês. O roteiro tem o texto certo, mas não tem tempo nenhum. O pipeline cruza os dois com Longest Common Subsequence: as palavras que batem herdam o timestamp do Whisper, e as que não batem são encaixadas entre os vizinhos. Resultado: legenda com o texto do roteiro e o tempo da fala.

### Fallback entre provedores

Narração e chamadas de LLM passam por uma camada que tenta o provedor principal e, em caso de erro, limite de uso ou timeout, tenta o próximo da lista. Um provedor fora do ar não derruba o pipeline.

### Melhores momentos de vídeos longos

Além dos vídeos a partir de roteiro, tem um algoritmo que lê um vídeo longo e pontua cada trecho para achar os melhores candidatos a corte. A nota combina:

- **energia do áudio**: picos de volume e intensidade;
- **presença de fala**: trechos com fala contínua valem mais que silêncio ou ruído;
- **mudança de cena**: cortes visuais indicam momentos com mais ação.

## Stack

- Node.js e TypeScript
- Remotion (renderização de vídeo com React)
- Whisper, rodando local
- APIs de LLM e TTS, com fallback

## Como rodar

> Seção a completar com os comandos do projeto.

```bash
npm install
# criar um .env com as chaves das APIs (LLM e TTS)
```

As chaves de API ficam só no `.env`, que está no `.gitignore`.

## Próximos passos

- Vídeos longos, além dos Shorts
- Narração com voz mais natural
- Escolha automática de pautas
