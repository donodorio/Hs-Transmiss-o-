# HS Network — Web TV independente

Site estático em HTML, CSS e JavaScript, preparado para publicação na Vercel e para receber uma transmissão HLS pública do MediaMTX.

## Arquivos

- `index.html` — página completa, estilos responsivos e lógica do player.
- `README.md` — instruções de publicação e configuração.
- `vercel.json` — configuração simples para hospedagem estática na Vercel.

## 1. Antes de substituir o site atual

1. Abra o repositório do projeto no GitHub.
2. Baixe uma cópia ZIP do projeto atual ou duplique o repositório.
3. Guarde o `index.html` atual fora da pasta que será publicada.
4. Só depois substitua os arquivos.

## 2. Publicar na Vercel

1. Coloque `index.html`, `README.md` e `vercel.json` na raiz do repositório GitHub.
2. Faça commit e push para a branch conectada à Vercel.
3. Em Vercel, abra o projeto e confira **Settings → Build and Deployment**.
4. Para esta página estática, normalmente não é necessário comando de build. Se a configuração pedir uma pasta de saída, use a raiz do projeto (`.`).
5. Aguarde o deploy e abra o domínio de produção.
6. Confira o site no celular e no computador.

A publicação do site não publica automaticamente o servidor de vídeo. A página ficará no estado **FORA DO AR** até receber e confirmar um sinal HLS válido.

## 3. Configurar a URL HLS quando o servidor público estiver pronto

No final de `index.html`, procure:

```js
window.HS_STREAM_URL = "";
```

Troque a string vazia pela URL HLS pública HTTPS fornecida pela configuração do MediaMTX, por exemplo, apenas como formato:

```js
window.HS_STREAM_URL = "https://SEU-DOMINIO-PUBLICO/caminho/index.m3u8";
```

O exemplo acima é ilustrativo, não é um servidor real. Não use `localhost`, `127.0.0.1`, IP privado ou URL acessível apenas na rede local. Não coloque senhas, tokens secretos ou credenciais no frontend.

Salve o arquivo, faça commit/push e espere a Vercel publicar a nova versão.

## 4. Dependência HLS

- Safari e alguns navegadores podem reproduzir HLS diretamente pelo elemento HTML5 `<video>`.
- Nos navegadores sem suporte HLS nativo, o site carrega `hls.js` 1.5.17 a partir de jsDelivr.
- Se a biblioteca CDN estiver bloqueada ou indisponível, a reprodução nesses navegadores poderá falhar; o site mostrará uma mensagem em vez de fingir que está funcionando.
- O servidor HLS deve permitir HTTPS e CORS para o domínio do site, inclusive para o manifesto `.m3u8` e os segmentos de vídeo.

## 5. Como funciona o indicador ao vivo

O site não marca AO VIVO apenas porque a URL está preenchida. Ele tenta carregar o manifesto HLS e aguarda confirmação de mídia. Se não receber sinal, apresenta **FORA DO AR** e tenta novamente após um intervalo. A confirmação do manifesto não é garantia absoluta de que o vídeo continuará estável; o teste de ponta a ponta ainda é necessário.

## 6. Arquitetura de vídeo

Fluxo pretendido:

`OBS Studio no Mac → MediaMTX público → HLS HTTPS → player da HS Network na Vercel`

A Vercel hospeda o site; não deve ser considerada o servidor RTMP principal. O servidor público precisa estar acessível a partir da internet, com firewall, TLS/HTTPS e regras de acesso adequadas.

Antes de anunciar a transmissão:
1. Configure o MediaMTX em um servidor público ou serviço de streaming apropriado.
2. Configure o OBS para enviar a transmissão ao endpoint RTMP/RTMPS correto.
3. Configure o MediaMTX para publicar HLS por HTTPS.
4. Configure CORS para permitir o domínio da HS Network.
5. Inicie o OBS e teste o site em outro dispositivo usando outra conexão (por exemplo, dados móveis).
6. Encerre a transmissão e confirme que o player retorna a FORA DO AR.

## 7. Mais de 100 espectadores

Não há garantia de capacidade só por funcionar em um teste individual. HLS gera tráfego de saída proporcional ao bitrate e ao número de espectadores. Para 100 espectadores simultâneos, avalie banda de saída, limite de conexões, CPU, latência, custo e distribuição por CDN ou serviço de streaming. Faça um teste de carga controlado antes de divulgar um evento grande.

## 8. Limitações desta primeira versão

- Não inclui servidor MediaMTX público nem URL HLS real.
- Não inclui agendamento de programas, login, banco de dados ou painel administrativo.
- Os cartões de programas e a programação são propostas iniciais, não eventos confirmados.
- A integração real depende da infraestrutura de vídeo, HTTPS/CORS e teste externo.
