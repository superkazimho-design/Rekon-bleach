# REIKON — Portal privado de Bleach RPG (celular e PC)

## O que mudou
- Tela de entrada fechada para jogadores sem convite.
- Login exclusivo do mestre com e-mail/senha no Supabase Auth (você define sua senha; nenhuma senha mestre está embutida no código).
- Painel `/mestre` para gerar tokens aleatórios individuais, de uso único, e revogá-los.
- Tokens e permissões armazenados no banco de dados, protegidos por RLS e funções SQL.
- Estúdio de avatar 2D com raça, cabelo, cores livres, roupas, acessórios e marcas, integrado à ficha.
- Campos livres de aparência, história e habilidades permanecem disponíveis.
- Fichas também são enviadas ao Supabase quando configurado (a edição e sincronização entre aparelhos ainda precisam de refinamento).

## Configuração obrigatória antes de publicar
1. Crie um projeto no Supabase e abra **SQL Editor**. Execute `supabase/setup.sql`.
2. Em **Authentication > Providers**, habilite **Anonymous sign-ins** para jogadores. Em produção, configure CAPTCHA/rate limiting contra abuso de tokens.
3. Em **Authentication > Users**, crie sua conta mestre (e-mail e senha forte escolhida por você). Copie o UUID do usuário.
4. No SQL Editor, execute `insert into public.masters(user_id) values ('SEU-UUID-AQUI');`. Não coloque a senha em código, arquivos ou chat.
5. Na Vercel, configure `NEXT_PUBLIC_SUPABASE_URL` e `NEXT_PUBLIC_SUPABASE_ANON_KEY` em **Project Settings > Environment Variables**. Use a chave **publishable/anon**, NUNCA a `service_role`.
6. Envie o código a um repositório GitHub e importe na Vercel; cada atualização no GitHub fará um novo deploy. O projeto é Next.js.
7. Acesse `/acesso`, entre como mestre e gere um token para cada jogador. Cada token é resgatável uma vez e fica vinculado à sessão anônima daquele navegador. O mestre pode revogar convites, bloqueando sessões associadas.

## Desenvolvimento
`npm install` e `npm run dev` (em um ambiente Node.js, como editor cloud). Para trabalhar só pelo celular, use GitHub web e Vercel.

## Limitações de segurança e funcionalidade
O portal usa bloqueio visual no cliente e **RLS no banco** para proteger fichas e convites. Isso NÃO protege os arquivos públicos estáticos nem o HTML/JavaScript já distribuído pelo Next.js; para exigir bloqueio completo de todas as rotas no servidor, implemente middleware com sessões por cookie (@supabase/ssr). Não use este protótipo para armazenar dados confidenciais. As fichas antigas permanecem no localStorage; a sincronização de listagem/edição e a moderação de fichas ainda precisam ser completadas. Se um jogador limpar o navegador, poderá perder a sessão anônima e precisará de novo token. Evite distribuir tokens publicamente.

**Importante:** este projeto não foi implantado nem validado em build de produção. O banco de dados não existe até você executar as etapas acima. O avatar é um editor SVG 2D, não um criador 3D ou sistema de upload de cabelos desenhados.
