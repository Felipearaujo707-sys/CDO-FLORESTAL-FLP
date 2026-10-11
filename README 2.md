# CDO Florestal — protótipo PWA

Protótipo responsivo para iPhone e computador. Inclui cadastro local de CDO, histórico, entradas de estoque, prévia de relatório, impressão/salvar como PDF via navegador, compartilhamento de resumo e exportação de backup JSON.

## Limites desta versão
- Dados ficam no `localStorage` do navegador/dispositivo. Não há sincronização entre usuários nem garantia de backup automático.
- Login e perfis são somente arquitetura proposta; não há autenticação/controle de acesso real.
- Estoque é demonstrativo: não faz baixa automática por consumo, não controla validade nem lotes com transações seguras.
- A impressão abre o diálogo do navegador. Escolha “Salvar como PDF” se essa opção estiver disponível.
- O compartilhamento utiliza as capacidades do navegador/aparelho; não envia mensagens automaticamente.
- Service worker/PWA exige hospedagem HTTPS (ou localhost para testes). Não é um app nativo publicado na App Store.

## Como testar no computador
1. Descompacte a pasta.
2. Sirva os arquivos por um servidor HTTP local, por exemplo `python -m http.server 8000` dentro da pasta.
3. Abra `http://localhost:8000`.
4. Teste registros, estoque, relatório e exportação JSON.

## Como testar no iPhone
Publique os arquivos em um host HTTPS. Abra o endereço no Safari e use Compartilhar → Adicionar à Tela de Início. O cache offline do app começa após a primeira visita online; teste em modo avião depois. Não use dados reais confidenciais antes da implantação segura.

## Arquitetura recomendada para produção
- Frontend: PWA TypeScript (idealmente migrar este protótipo para framework com testes e validação de formulários).
- Offline: IndexedDB + fila de sincronização e IDs UUID.
- Backend gerenciado: Supabase (PostgreSQL + Auth + Row Level Security + Storage) ou Firebase. Escolher um, não ambos inicialmente.
- Entidades: organizations, users, memberships, projects, farms, plots, teams, employees, activities, daily_reports, report_entries, products, stock_movements, audit_events, attachments.
- Segurança: cada registro ligado à organização; regras por função e escopo de fazenda/projeto; RLS no banco; logs de auditoria append-only; backups e política de retenção.
- Sincronização: cada alteração tem UUID, `updated_at`, `version`, `created_by`, `updated_by`, `sync_status`; fila local; idempotência no servidor; conflito explícito em vez de sobrescrever silenciosamente.
- PDF: gerar no navegador para impressão na primeira versão; depois gerar PDF padronizado no backend, armazenar com permissões e permitir compartilhamento.
- WhatsApp: botão de compartilhamento manual no dispositivo. Para mensagens automáticas corporativas, avaliar a WhatsApp Business Platform oficial, com consentimento e configuração de templates quando exigidos.
- Hospedagem: Cloudflare Pages/Vercel ou infraestrutura HTTPS aprovada pela TI; domínio corporativo, monitoramento e gestão de segredos.
- Segurança de aplicação de defensivos: dose nunca sugerida automaticamente sem produto/formulação/uso autorizado confirmado em bula vigente; armazenar referência de bula, data da validação e responsável técnico.

## Antes de produção
1. Definir a empresa e a estrutura de projetos/fazendas/talhões.
2. Aprovar campos obrigatórios e fluxo de revisão/aprovação.
3. Escolher backend e configurar credenciais no ambiente de implantação (nunca embutir chave administrativa no frontend).
4. Criar e testar políticas de acesso e migrações do banco.
5. Testar sincronização offline, conflitos, restauração de backup e permissões.
6. Validar relatórios, retenção de dados e processo de suporte.
