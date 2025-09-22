# ProxyCDN - Sistema de Proxy CDN com Analytics

![ProxyCDN](https://img.shields.io/badge/ProxyCDN-v2.2.0-blue.svg)
![Next.js](https://img.shields.io/badge/Next.js-14.0-black.svg)
![Supabase](https://img.shields.io/badge/Supabase-Database-green.svg)
![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue.svg)

Sistema completo de proxy CDN com analytics avançado, gerenciamento de domínios e dashboard administrativo.

## 📋 Índice

- [Visão Geral](#visão-geral)
- [Características](#características)
- [Tecnologias](#tecnologias)
- [Instalação](#instalação)
- [Configuração do Banco de Dados](#configuração-do-banco-de-dados)
- [Configuração](#configuração)
- [Uso](#uso)
- [API](#api)
- [Deploy](#deploy)
- [Troubleshooting](#troubleshooting)
- [Contribuição](#contribuição)
- [Changelog](#changelog)
- [Licença](#licença)

## 🎯 Visão Geral

O ProxyCDN é uma solução completa para gerenciamento de proxy CDN que oferece:

- **Proxy Inteligente**: Redirecionamento eficiente de tráfego
- **Analytics Avançado**: Métricas detalhadas de uso e performance
- **Gerenciamento de Domínios**: Interface para configuração de domínios
- **Dashboard Administrativo**: Painel completo para administração
- **Sistema de Pagamentos**: Integração com gateways de pagamento
- **Multi-tenant**: Suporte a múltiplos usuários e organizações

## ✨ Características

### 🚀 Performance
- Cache inteligente com Redis
- Otimização automática de recursos
- Compressão de dados
- Load balancing

### 📊 Analytics
- Métricas em tempo real
- Relatórios detalhados
- Geolocalização de visitantes
- Análise de performance

### 🔐 Segurança
- Autenticação JWT
- Rate limiting
- Proteção DDoS
- SSL/TLS automático

### 🎨 Interface
- Dashboard responsivo
- Tema escuro/claro
- Gráficos interativos
- Interface intuitiva

## 🛠 Tecnologias

### Frontend
- **Next.js 14** - Framework React
- **TypeScript** - Tipagem estática
- **Tailwind CSS** - Estilização
- **Shadcn/ui** - Componentes UI
- **Recharts** - Gráficos e visualizações

### Backend
- **Next.js API Routes** - API REST
- **Supabase** - Banco de dados PostgreSQL
- **Redis (Upstash)** - Cache e sessões
- **NextAuth.js** - Autenticação

### DevOps
- **Docker** - Containerização
- **Cloudflare** - CDN e DNS
- **Vercel/VPS** - Deploy e hospedagem

## 📦 Instalação

### Pré-requisitos

- Node.js 18+ 
- npm ou yarn
- Conta Supabase
- Conta Redis (Upstash)

### Clonagem e Instalação

```bash
# Clonar o repositório
git clone https://github.com/seu-usuario/ProxyCDN.git
cd ProxyCDN

# Instalar dependências
npm install

# Copiar arquivo de ambiente
cp .env.example .env.local

# Configurar variáveis de ambiente (ver seção Configuração)
nano .env.local
```

## 🗄️ Configuração do Banco de Dados

### 1. Configuração do Supabase

#### Criar Projeto no Supabase
1. Acesse [supabase.com](https://supabase.com)
2. Crie um novo projeto
3. Anote as credenciais:
   - **URL do Projeto**: `https://seu-projeto.supabase.co`
   - **Chave Anon**: `eyJhbGciOiJIUzI1NiIs...`
   - **Service Role**: `eyJhbGciOiJIUzI1NiIs...`

#### Configurar Variáveis de Ambiente

```bash
# Database - Supabase Configuration
DATABASE_URL="postgresql://postgres:sua-senha@db.seu-projeto.supabase.co:5432/postgres"
SUPABASE_URL="https://seu-projeto.supabase.co"
SUPABASE_ANON_KEY="sua-chave-anon"
SUPABASE_SERVICE_ROLE_KEY="sua-service-role-key"

# MCP Configuration for Supabase
MCP_SUPABASE_PROJECT_ID="seu-projeto-id"
MCP_SUPABASE_REGION="us-east-1"
MCP_DATABASE_HOST="db.seu-projeto.supabase.co"
MCP_DATABASE_PORT="5432"
MCP_DATABASE_NAME="postgres"
MCP_DATABASE_USER="postgres"
MCP_DATABASE_PASSWORD="sua-senha"
MCP_DATABASE_SSL="true"
```

### 2. Executar Migração do Banco

#### Método Automático (Recomendado)

```bash
# Testar conexão
node test-connection-simple.js

# Executar migração completa
node execute-migration.js

# Validar estrutura criada
node validate-database.js

# Testar operações CRUD
node test-final-operations.js
```

#### Método Manual (SQL Editor)

1. Acesse o Supabase Dashboard
2. Vá para **SQL Editor**
3. Execute o conteúdo do arquivo `create-tables-direct.sql`

### 3. Estrutura do Banco de Dados

#### Tabelas Principais

```sql
-- Usuários do sistema
CREATE TABLE "User" (
    "id" TEXT PRIMARY KEY,
    "email" TEXT UNIQUE NOT NULL,
    "name" TEXT,
    "password" TEXT NOT NULL,
    "role" "UserRole" DEFAULT 'USER',
    "createdAt" TIMESTAMP(3) DEFAULT CURRENT_TIMESTAMP,
    "updatedAt" TIMESTAMP(3) DEFAULT CURRENT_TIMESTAMP
);

-- Domínios configurados
CREATE TABLE "Domain" (
    "id" TEXT PRIMARY KEY,
    "domain" TEXT UNIQUE NOT NULL,
    "targetUrl" TEXT NOT NULL,
    "status" "DomainStatus" DEFAULT 'PENDING',
    "userId" TEXT NOT NULL,
    "createdAt" TIMESTAMP(3) DEFAULT CURRENT_TIMESTAMP,
    "updatedAt" TIMESTAMP(3) DEFAULT CURRENT_TIMESTAMP
);

-- Analytics principais
CREATE TABLE "Analytics" (
    "id" TEXT PRIMARY KEY,
    "domainId" TEXT NOT NULL,
    "visits" INTEGER DEFAULT 0,
    "bandwidth" BIGINT DEFAULT 0,
    "date" TIMESTAMP(3) DEFAULT CURRENT_TIMESTAMP
);

-- Pagamentos
CREATE TABLE "Payment" (
    "id" TEXT PRIMARY KEY,
    "userId" TEXT NOT NULL,
    "amount" DECIMAL(10,2) NOT NULL,
    "status" TEXT NOT NULL,
    "stripePaymentId" TEXT,
    "createdAt" TIMESTAMP(3) DEFAULT CURRENT_TIMESTAMP
);
```

#### Tabelas de Analytics Avançado

```sql
-- Logs detalhados de analytics
CREATE TABLE analytics_logs (
    id BIGSERIAL PRIMARY KEY,
    timestamp TIMESTAMPTZ DEFAULT NOW(),
    domain VARCHAR(255) NOT NULL,
    path VARCHAR(1000),
    user_agent TEXT,
    ip_address INET,
    referer TEXT,
    country VARCHAR(2),
    city VARCHAR(100),
    status_code INTEGER,
    response_time INTEGER,
    bytes_sent BIGINT,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Estatísticas por domínio
CREATE TABLE domain_stats (
    id BIGSERIAL PRIMARY KEY,
    domain VARCHAR(255) UNIQUE NOT NULL,
    total_requests BIGINT DEFAULT 0,
    unique_visitors BIGINT DEFAULT 0,
    total_bandwidth BIGINT DEFAULT 0,
    last_request TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Estatísticas de visitantes
CREATE TABLE visitor_stats (
    id BIGSERIAL PRIMARY KEY,
    visitor_hash VARCHAR(64) NOT NULL,
    domain VARCHAR(255) NOT NULL,
    first_visit TIMESTAMPTZ DEFAULT NOW(),
    last_visit TIMESTAMPTZ DEFAULT NOW(),
    visit_count INTEGER DEFAULT 1,
    country VARCHAR(2),
    city VARCHAR(100),
    user_agent_hash VARCHAR(64),
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Logs de acesso
CREATE TABLE access_logs (
    id BIGSERIAL PRIMARY KEY,
    timestamp TIMESTAMPTZ DEFAULT NOW(),
    domain VARCHAR(255) NOT NULL,
    method VARCHAR(10),
    path VARCHAR(1000),
    status_code INTEGER,
    response_size BIGINT,
    user_agent TEXT,
    ip_address INET,
    processing_time INTEGER,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

## ⚙️ Configuração

### Arquivo .env.local

```bash
# NextAuth
NEXTAUTH_SECRET="seu-nextauth-secret"
NEXTAUTH_URL="https://seu-dominio.com"

# Redis Upstash
UPSTASH_REDIS_REST_URL="https://sua-instancia.upstash.io"
UPSTASH_REDIS_REST_TOKEN="seu-token-redis"

# Payment Gateways
MERCADO_PAGO_ACCESS_TOKEN="seu-token-mercadopago"
MERCADO_PAGO_PUBLIC_KEY="sua-chave-publica-mercadopago"
PAGSEGURO_EMAIL="seu-email-pagseguro"
PAGSEGURO_TOKEN="seu-token-pagseguro"

# App Configuration
NEXT_PUBLIC_APP_URL="https://seu-dominio.com"
FRONTEND_URL="https://seu-dominio.com"
API_URL="https://api.seu-dominio.com"

# Security
JWT_SECRET="seu-jwt-secret-super-seguro"

# CDN Configuration
CDN_ENABLED=true
PROXY_TIMEOUT=30000
NODE_ENV="production"
```

### Gerar Secrets

```bash
# Gerar NextAuth Secret
openssl rand -base64 32

# Gerar JWT Secret
openssl rand -base64 64
```

## 🚀 Uso

### Desenvolvimento

```bash
# Iniciar servidor de desenvolvimento
npm run dev

# Executar em modo de produção
npm run build
npm start

# Executar testes
npm test

# Linting
npm run lint
```

### Scripts Úteis

```bash
# Testar conexão com banco
node test-connection-simple.js

# Validar estrutura do banco
node validate-database.js

# Executar migração
node execute-migration.js

# Debug de variáveis de ambiente
node debug-env.js
```

## 📡 API

### Endpoints Principais

#### Autenticação
- `POST /api/auth/login` - Login de usuário
- `POST /api/auth/register` - Registro de usuário
- `POST /api/auth/logout` - Logout

#### Domínios
- `GET /api/domains` - Listar domínios
- `POST /api/domains` - Criar domínio
- `PUT /api/domains/:id` - Atualizar domínio
- `DELETE /api/domains/:id` - Remover domínio

#### Analytics
- `GET /api/analytics/summary` - Resumo de analytics
- `GET /api/analytics/domain/:domain` - Analytics por domínio
- `POST /api/analytics/log` - Registrar evento

#### Administração
- `GET /api/admin/users` - Listar usuários
- `GET /api/admin/stats` - Estatísticas gerais
- `POST /api/admin/domains/approve` - Aprovar domínio

### Exemplo de Uso da API

```javascript
// Registrar evento de analytics
const response = await fetch('/api/analytics/log', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    domain: 'exemplo.com',
    path: '/pagina',
    userAgent: navigator.userAgent,
    referrer: document.referrer
  })
});
```

## 🐳 Deploy

### Docker

```bash
# Build da imagem
docker build -t proxycdn .

# Executar container
docker run -p 3000:3000 --env-file .env.local proxycdn
```

### Docker Compose

```yaml
version: '3.8'
services:
  proxycdn:
    build: .
    ports:
      - "3000:3000"
    env_file:
      - .env.local
    restart: unless-stopped
```

### VPS/Servidor

```bash
# Deploy para produção
chmod +x deploy-production.sh
./deploy-production.sh
```

### Cloudflare Tunnel

```bash
# Configurar tunnel
chmod +x cloudflare-cache-purge.sh
./cloudflare-cache-purge.sh
```

## 🤝 Contribuição

### Como Contribuir

1. Fork o projeto
2. Crie uma branch para sua feature (`git checkout -b feature/AmazingFeature`)
3. Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4. Push para a branch (`git push origin feature/AmazingFeature`)
5. Abra um Pull Request

### Padrões de Código

- Use TypeScript para novos arquivos
- Siga as convenções do ESLint
- Adicione testes para novas funcionalidades
- Documente APIs e funções complexas

### Estrutura de Commits

```
tipo(escopo): descrição

feat(auth): adicionar autenticação OAuth
fix(api): corrigir erro na validação de domínios
docs(readme): atualizar documentação de instalação
style(ui): melhorar responsividade do dashboard
refactor(db): otimizar queries de analytics
test(api): adicionar testes para endpoints de domínios
```

## 📝 Changelog

### [2.2.0] - 21-09-2029

#### 🎨 Design System Renovado
- **Menu Lateral Elegante**: Implementação de gradientes modernos (slate-900 → blue-600 → purple-600)
- **Efeitos Visuais Avançados**: Adição de sombras (shadow-lg, shadow-xl) e backdrop-blur
- **Animações Fluidas**: Transições suaves em todos os elementos interativos
- **Scrollbar Personalizada**: Design customizado para navegação do menu
- **Botão Mobile Aprimorado**: Design elegante com gradientes e efeitos hover

#### 📱 Responsividade Mobile
- **Menu Lateral Responsivo**: Implementação completa de menu mobile funcional
- **Overlay Inteligente**: Backdrop-blur e transições de opacidade suaves
- **Botão Toggle Elegante**: Design moderno com gradientes e sombras
- **Experiência Mobile Otimizada**: Interface adaptável para todos os dispositivos

#### 👥 Página de Usuários Aprimorada
- **Botões de Ação Redesenhados**: Implementação de ícones SVG e gradientes
- **Botão Editar**: Ícone de lápis com gradiente azul-roxo
- **Botão Ativar/Suspender**: Ícones dinâmicos (check/pause) com cores contextuais
- **Botão Excluir**: Ícone de lixeira com gradiente vermelho
- **Layout Otimizado**: Espaçamentos e alinhamentos aprimorados

#### 🔧 Modal de Edição Funcional
- **Design Elegante**: Modal com gradientes, sombras e backdrop-blur
- **Campos Completos**: Edição de Nome, Email, Função e Status
- **Informações Contextuais**: Exibição da data de criação do usuário
- **Funcionalidade Completa**: Salvamento real das alterações no banco de dados
- **Validação Visual**: Feedback imediato para ações do usuário
- **Botões Estilizados**: Design consistente com o sistema

#### 🎯 Melhorias de UX
- **Transições Consistentes**: Animações padronizadas em toda a interface
- **Estados Hover**: Efeitos visuais em todos os elementos interativos
- **Feedback Visual**: Indicadores claros de ações e estados
- **Acessibilidade**: Contrastes adequados e elementos focáveis
- **Performance**: Otimização de renderização e carregamento

#### 🛠 Melhorias Técnicas
- **Componente Sidebar**: Refatoração completa com design system moderno
- **Estados de Formulário**: Implementação robusta de gerenciamento de estado
- **Funções de Edição**: Lógica completa para CRUD de usuários
- **TypeScript**: Tipagem adequada para todos os novos componentes
- **CSS Moderno**: Uso de classes Tailwind CSS avançadas

### [2.1.0] - 21-09-2025

#### 🎉 Adicionado
- **Configurações Avançadas no Dashboard**: Controles para SSL, Cache e Redirecionamento 301
- **Sistema MCP (Migration Control Plane)**: Plataforma completa para gerenciamento de migrações
- **Dashboard MCP**: Interface administrativa para monitoramento de migrações
- **Integração Cloudflare Tunnel**: Suporte completo com headers otimizados
- **Scripts de Teste Avançados**: Testes CRUD completos para validação de operações
- **Sistema de Debug Logger**: Logging detalhado para identificação de problemas
- **Análise de Performance**: Ferramentas para monitoramento de site

#### 🔧 Melhorado
- **Headers de Segurança**: Implementação de X-Frame-Options, X-Content-Type-Options
- **Performance Next.js**: Otimizações com swcMinify e compress habilitados
- **Compatibilidade Cloudflare**: Headers específicos para tunnel e cache
- **Interface do Dashboard**: Configurações avançadas com toggles intuitivos
- **Sistema de Proxy**: Melhor detecção de IP real e headers de origem

#### 🐛 Corrigido
- **Problemas de Hidratação**: Resolução de erros com Cloudflare Tunnel
- **Detecção de IP**: Melhor captura de CF-Connecting-IP e X-Real-IP
- **Cache de Headers**: Otimização para evitar conflitos de cache
- **Roteamento de Requisições**: Correção de problemas de proxy

#### 🛠 Funcionalidades MCP
- **Planejamento de Migração**: Análise de dependências e avaliação de riscos
- **Sistema de Backup**: Backup completo e incremental com verificação
- **Execução Controlada**: Migração por etapas com monitoramento em tempo real
- **Validação de Dados**: Verificação de schema e integridade referencial
- **Monitoramento Contínuo**: Métricas de CPU, memória, disco e rede
- **Rollback Automático**: Desfaz alterações em caso de erro

#### 📊 Scripts Adicionados
- `test-final-operations.js` - Testes CRUD completos
- `auto-setup-database.js` - Configuração automática do banco
- `validate-database.js` - Validação de estrutura do banco
- `cloudflare-cache-purge.sh` - Script para limpeza de cache
- `deploy-production.sh` - Script de deploy para produção

#### 🔒 Melhorias de Segurança
- **Headers de Proteção**: X-Frame-Options DENY, X-Content-Type-Options nosniff
- **Referrer Policy**: Configuração origin-when-cross-origin
- **CORS Otimizado**: Headers específicos para API com controle de origem
- **Desabilitação de Headers**: Remoção do X-Powered-By para segurança

### [2.1.0] - 21-09-2025

#### 🎨 Interface e UX
- **Correção de Layout**: Resolução de problemas de sobreposição de cards no dashboard
- **Navegação Aprimorada**: Correção de links quebrados no menu lateral do SuperAdmin
- **Links Funcionais**: Implementação de navegação completa entre seções administrativas
- **Tradução Completa**: Interface 100% traduzida para português brasileiro

#### 🔧 Melhorias Técnicas
- **Dashboard SuperAdmin**: Otimização de layout e responsividade dos cards de métricas
- **Componentes UI**: Atualização do MetricsCard com traduções adequadas
- **Sidebar**: Correção de links de navegação e estrutura de menu
- **AnalyticsChart**: Manutenção de traduções consistentes

#### 🐛 Correções
- **Sobreposição de Cards**: Resolução de problemas de z-index e posicionamento
- **Links Quebrados**: Correção de rotas não funcionais no menu administrativo
- **Navegação**: Implementação de redirecionamentos corretos entre páginas
- **Responsividade**: Ajustes para melhor exibição em diferentes tamanhos de tela

#### 🌐 Localização
- **Português Brasileiro**: Tradução completa de todos os textos da interface
- **Componentes**: Atualização de MetricsCard, AnalyticsChart e Sidebar
- **Mensagens**: Tradução de tooltips, labels e textos de ajuda
- **Consistência**: Padronização de terminologia em toda a aplicação

#### 📊 Dashboard Administrativo
- **SuperAdmin**: Interface completamente funcional e traduzida
- **Métricas do Sistema**: Cards de CPU, Memória, Disco e Uptime
- **Ações Administrativas**: Botões funcionais para gerenciamento
- **Tabelas**: Exibição adequada de dados com headers traduzidos

### [2.0.0] - 21-09-2025

#### 🎉 Adicionado
- **Nova Configuração de Banco**: Migração completa para Supabase
- **Sistema de Analytics Avançado**: Tabelas especializadas para métricas detalhadas
- **Scripts de Migração**: Automação completa da configuração do banco
- **Validação de Estrutura**: Testes automatizados para verificar integridade
- **Documentação Completa**: README abrangente com guias de instalação

#### 🔧 Melhorado
- **Performance do Banco**: Otimização de queries e índices
- **Segurança**: Implementação de Row Level Security (RLS)
- **Monitoramento**: Logs detalhados de operações
- **Escalabilidade**: Estrutura preparada para alto volume

#### 🐛 Corrigido
- **Conexão com Supabase**: Resolução de problemas de autenticação
- **Schema Cache**: Tratamento adequado de cache vazio em bancos novos
- **Variáveis de Ambiente**: Validação e configuração automática

#### 📊 Tabelas Adicionadas
- `analytics_logs` - Logs detalhados de acesso
- `domain_stats` - Estatísticas agregadas por domínio
- `visitor_stats` - Métricas de visitantes únicos
- `access_logs` - Logs de acesso do sistema

#### 🛠 Scripts Adicionados
- `test-connection-simple.js` - Teste de conectividade
- `execute-migration.js` - Execução de migração
- `validate-database.js` - Validação de estrutura
- `test-final-operations.js` - Testes de operações CRUD

### [1.5.0] - 20-09-2025

#### 🎉 Adicionado
- Dashboard administrativo completo
- Sistema de pagamentos integrado
- Suporte a múltiplos gateways de pagamento
- Interface de gerenciamento de usuários

#### 🔧 Melhorado
- Performance do proxy CDN
- Interface do usuário
- Sistema de cache Redis

#### 🐛 Corrigido
- Problemas de autenticação
- Bugs na interface mobile
- Otimização de queries

### [1.0.0] - 20-09-2025

#### 🎉 Lançamento Inicial
- Sistema básico de proxy CDN
- Analytics simples
- Gerenciamento de domínios
- Autenticação de usuários

## 📄 Licença

Este projeto está licenciado sob a Licença MIT - veja o arquivo [LICENSE](LICENSE) para detalhes.

## 🔧 Troubleshooting

### Erro 502 Bad Gateway

Se você encontrar o erro **502 Bad Gateway** ao acessar a aplicação, siga estes passos:

#### 🔍 Diagnóstico

1. **Verificar se o servidor Next.js está rodando**:
```bash
ps aux | grep next
netstat -tlnp | grep :3000
```

2. **Verificar logs do nginx**:
```bash
tail -20 /www/wwwlogs/app.ricardtech.top.error.log
```

3. **Testar conectividade local**:
```bash
curl -I http://localhost:3000
curl -I http://localhost:3000/login
```

#### ✅ Solução

1. **Iniciar o servidor Next.js**:
```bash
cd /www/wwwroot/ProxyCDN
npm run start
```

2. **Para produção, usar PM2** (recomendado):
```bash
# Instalar PM2 globalmente
npm install -g pm2

# Iniciar aplicação com PM2
pm2 start npm --name "proxy-cdn" -- run start

# Salvar configuração do PM2
pm2 save

# Configurar inicialização automática
pm2 startup
```

3. **Verificar configuração do nginx**:
```bash
# Localizar arquivo de configuração
find /www -name "*.conf" -exec grep -l "app.ricardtech.top" {} \;

# Verificar se o proxy está apontando para a porta correta
cat /www/server/panel/vhost/nginx/app.ricardtech.top.conf
```

#### 📋 Configuração do Nginx

A configuração do nginx deve incluir:

```nginx
location ^~ / {
    proxy_pass http://127.0.0.1:3000;
    proxy_set_header Host 127.0.0.1;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_connect_timeout 60s;
    proxy_send_timeout 600s;
    proxy_read_timeout 600s;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

#### ⚠️ Warnings de Compatibilidade

Os seguintes warnings são **normais** e não afetam o funcionamento:

```
A Node.js API is used (setImmediate/process.nextTick) which is not supported in the Edge Runtime
```

Estes avisos aparecem porque:
- **Prisma Client** e **bcryptjs** usam APIs do Node.js
- Funcionam perfeitamente no runtime padrão do Next.js
- Só seriam problemáticos no Edge Runtime específico

#### 🚀 Verificação Final

Após seguir os passos acima:

1. **Servidor deve estar rodando**: `✓ Ready in X.Xs`
2. **Teste local deve retornar 200**: `HTTP/1.1 200 OK`
3. **Aplicação deve estar acessível**: `https://app.ricardtech.top`

### Outros Problemas Comuns

#### Problemas de Banco de Dados
```bash
# Testar conexão com Supabase
node test-connection-simple.js

# Validar estrutura do banco
node validate-database.js
```

#### Problemas de Build
```bash
# Limpar cache e rebuildar
rm -rf .next
npm run build
```

#### Problemas de Dependências
```bash
# Reinstalar dependências
rm -rf node_modules package-lock.json
npm install
```

## 📞 Suporte

- **Email**: suporte@proxycdn.com
- **Discord**: [Servidor da Comunidade](https://discord.gg/proxycdn)
- **Documentação**: [docs.proxycdn.com](https://docs.proxycdn.com)
- **Issues**: [GitHub Issues](https://github.com/seu-usuario/ProxyCDN/issues)

## 🙏 Agradecimentos

- [Next.js](https://nextjs.org/) - Framework React
- [Supabase](https://supabase.com/) - Backend as a Service
- [Tailwind CSS](https://tailwindcss.com/) - Framework CSS
- [Shadcn/ui](https://ui.shadcn.com/) - Componentes UI
- [Upstash](https://upstash.com/) - Redis serverless

---

**ProxyCDN** - Desenvolvido com ❤️ para a comunidade de desenvolvedores.

![Footer](https://img.shields.io/badge/Made%20with-❤️-red.svg)
![Next.js](https://img.shields.io/badge/Powered%20by-Next.js-black.svg)
![Supabase](https://img.shields.io/badge/Database-Supabase-green.svg)# ProxyCDN
