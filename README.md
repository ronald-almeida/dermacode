# DERMACODE — Checkout Pix

Checkout responsivo em português, Open Sans, imagens fornecidas e preço fixo de **R$ 297,00**. Node.js 22 ou superior, sem dependências externas.

## Executar

Copie `.env.example` para `.env`, preencha `BLACKCAT_API_KEY` e `CHECKOUT_SECRET` (segredo aleatório de pelo menos 32 caracteres) e execute `npm start` ou `node --env-file-if-exists=.env server.mjs`. Acesse `http://localhost:3000`. Execute os testes com `node --test`.

Sem credenciais a interface funciona, mas a geração de Pix retorna indisponibilidade; não são criadas cobranças fictícias. Nunca coloque a chave em arquivos públicos ou no Git.

## Integração

Documentação: https://docs.blackcatoficial.com/

- `POST /api/pix`: valida o comprador, fixa 29700 centavos e cria a venda no servidor com `X-API-Key`.
- `GET /api/pix/status`: consulta autenticada por token assinado; confirma o valor e o ID antes de informar o status.
- QR Code e copia e cola vêm de `paymentData`. O navegador acompanha o status a cada 10 segundos e recupera o Pix da sessão após recarregar.
- `BLACKCAT_POSTBACK_URL` é opcional e deve apontar para seu serviço de entrega do curso. Esse serviço deve consultar a transação na Black Cat e entregar o acesso de forma idempotente, sem confiar somente no webhook recebido.

**Entrega de acesso/email não está implementada:** depende da plataforma de membros e do serviço de email escolhidos. A confirmação na tela só confirma o pagamento. O banner enviado cita um presente físico, mas este checkout trata a venda como acesso digital e não coleta endereço; confirme a logística dessa oferta antes de publicar.

## Hospedagem

### Vercel

Importe `ronald-almeida/dermacode`, selecione o preset **Other** e mantenha a pasta raiz. `vercel.json` serve `public/` e as funções em `api/`. Cadastre `BLACKCAT_API_KEY` e `CHECKOUT_SECRET` nas variáveis privadas de produção e preview antes de ativar os pagamentos. Depois de alterar variáveis, faça um novo deploy.

Na Vercel, as instâncias são efêmeras: o cache em memória não garante idempotência global. Para operação comercial, conecte armazenamento persistente compartilhado para as tentativas e configure o rate limiting por cliente na plataforma.

O GitHub guarda o código; a aplicação precisa de um servidor Node com HTTPS, comando `npm start` e as variáveis acima. GitHub Pages não executa a integração de pagamentos.

A proteção contra repetição e o limite de tentativas ficam na memória de uma única instância. Para várias instâncias ou garantias entre reinicializações, substitua os mapas por armazenamento persistente compartilhado e configure limites no proxy. Não foi presumido suporte a idempotência na API da Black Cat. Em resultado incerto, o checkout mantém a mesma tentativa e orienta atendimento, evitando novas tentativas automáticas de criação.

Antes de produção: configurar credenciais, hospedagem, entrega de acesso, contato de suporte e informações legais do vendedor; verificar uma transação real autorizada e a confirmação de pagamento. Os testes locais usam respostas simuladas, sem cobranças reais.
