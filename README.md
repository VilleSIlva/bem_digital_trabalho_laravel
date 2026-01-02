# 🌐 Bem-Digital

**Bem-Digital** é uma plataforma de campanhas de doação onde instituições podem se cadastrar e criar campanhas para arrecadar doações.

As instituições terão acesso a diversas funcionalidades:
- ✅ Controle de estoque
- ✅ Perfil público
- ✅ Cadastro e gerenciamento de campanhas
- ✅ Registro de doações
- ✅ Cadastro de doadores e contribuintes
- ✅ Divulgação de campanhas
- ✅ E muitas outras!

---

## 💡 Origem do Projeto

O **Bem-Digital** surgiu como ideia em um trabalho acadêmico do curso **Sistemas para Internet** da **Faculdade UniAlfa**.

O projeto foi proposto pelo professor **Alex Morgado Pereira** na disciplina de **Desenvolvimento de Sites Avançado**, com o objetivo de criar um sistema **completo e funcional** para que instituições realizem campanhas de doação.

---

## 🛠️ Tecnologias Utilizadas

### Backend
- **PHP** 8.2+
- **Laravel** 12.0
- **Laravel Breeze** 2.3 (Autenticação)
- **Laravel Tinker** 2.10.1

### Frontend
- **Tailwind CSS** 3.1.0
- **Alpine.js** 3.15.0
- **DaisyUI** 5.1.25
- **Vite** 7.0.4
- **PenguinUI** 0.0.3

### Banco de Dados
- Suporta MySQL, PostgreSQL, SQLite

---

## 📊 Estrutura do Banco de Dados

### Diagrama de Relacionamento

```
┌─────────────┐         ┌──────────────┐         ┌─────────────┐
│   RULES     │         │   MODULES    │         │  CATEGORIES │
│             │         │              │         │             │
│ id (PK)     │         │ id (PK)      │         │ id (PK)     │
│ name        │         │ title        │         │ name        │
└─────────────┘         └──────────────┘         │ is_active   │
       │                       │                  └─────────────┘
       │                       │                        │
       │                       │                        │
       │                       │                        │
┌──────▼───────────────────────▼────────────────────────▼──────┐
│                         USERS                                 │
│  ┌────────────────────────────────────────────────────────┐  │
│  │ id (PK)                                                │  │
│  │ name, email (UK), password                             │  │
│  │ avatar, is_active, cpf, phone                          │  │
│  │ rule_id (FK) ──────────┐                              │  │
│  │ institution_id (FK) ────┼──┐                           │  │
│  │ address_id (FK) ────────┼──┼──┐                        │  │
│  └─────────────────────────┼──┼──┼────────────────────────┘  │
└────────────────────────────┼──┼──┼───────────────────────────┘
                             │  │  │
                             │  │  │
                    ┌────────┘  │  └────────┐
                    │           │           │
        ┌───────────▼───┐       │    ┌──────▼──────┐
        │ INSTITUTIONS  │       │    │  ADDRESSES  │
        │               │       │    │             │
        │ id (PK)       │       │    │ id (PK)     │
        │ fantasy_name  │       │    │ city, state │
        │ cnpj          │       │    │ zip, road  │
        │ phone, email  │       │    │ neighborhood│
        │ is_active     │       │    │ complement  │
        │ description   │       │    │ number      │
        │ photo_path    │       │    └─────────────┘
        │ address_id(FK)┼───────┘
        └───────┬───────┘
                │
                │
        ┌───────▼───────┐
        │   CAMPAIGNS   │
        │               │
        │ id (PK)       │
        │ name          │
        │ description   │
        │ phone         │
        │ is_active     │
        │ beginning     │
        │ termination   │
        │ mark, unit    │
        │ institution_id(FK)
        │ category_id(FK)
        └───────┬───────┘
                │
        ┌───────┼───────┐
        │       │       │
        │       │       │
┌───────▼───┐   │   ┌───▼────────┐
│ DONATIONS │   │   │ COLLECTION │
│           │   │   │   POINTS   │
│ id (PK)   │   │   │            │
│ user_id(FK)   │   │ id (PK)    │
│ campaign_id(FK)   │ title      │
│ type      │   │   │ address_id │
│ name      │   │   │ campaign_id│
│ quantify  │   │   └─────┬──────┘
│ amount    │   │         │
└───────────┘   │         │
                │         │
        ┌───────▼─────────▼──┐
        │      PHOTOS         │
        │                     │
        │ id (PK)             │
        │ filename            │
        │ imageable_id        │
        │ imageable_type      │
        │ (polimórfico)       │
        └─────────────────────┘
                │
                │
        ┌───────▼─────────┐
        │    SCHEDULES    │
        │                 │
        │ id (PK)         │
        │ collection_point_id(FK)
        │ dia            │
        │ abertura       │
        │ fechamento     │
        └─────────────────┘
```

### Tabelas e Relacionamentos

#### **Tabelas Principais**

| Tabela | Descrição | Relacionamentos |
|--------|-----------|-----------------|
| **users** | Usuários do sistema | → `institutions`, `rules`, `addresses`, `modules` (N:N), `donations` |
| **institutions** | Instituições cadastradas | → `users`, `addresses`, `campaigns` |
| **campaigns** | Campanhas de doação | → `institutions`, `categories`, `donations`, `collection_points`, `photos` |
| **donations** | Registro de doações | → `users` (opcional), `campaigns` |
| **categories** | Categorias de campanhas | ← `campaigns` |
| **addresses** | Endereços | ← `users`, `institutions`, `collection_points` |
| **collection_points** | Pontos de coleta | → `campaigns`, `addresses`, `schedules` |
| **schedules** | Horários de funcionamento | → `collection_points` |
| **photos** | Fotos (polimórfico) | → `institutions`, `campaigns` |
| **rules** | Perfis de acesso | ← `users` |
| **modules** | Módulos/Permissões | ↔ `users` (N:N via `module_user`) |
| **module_user** | Tabela pivot | → `users`, `modules` |

### Estrutura Detalhada das Tabelas

#### **users**
```sql
- id (PK)
- name
- email (UNIQUE)
- password
- avatar (nullable)
- is_active (boolean)
- cpf (nullable)
- phone (nullable)
- rule_id (FK → rules.id)
- institution_id (FK → institutions.id)
- address_id (FK → addresses.id, nullable)
- created_at, updated_at
```

#### **institutions**
```sql
- id (PK)
- fantasy_name
- cnpj
- phone
- email (UNIQUE)
- is_active (boolean)
- description (nullable)
- photo_path (nullable)
- address_id (FK → addresses.id, nullable)
- created_at, updated_at
```

#### **campaigns**
```sql
- id (PK)
- name
- description (text)
- phone
- legend_phone
- is_active (boolean)
- beginning (datetime)
- termination (datetime)
- mark (float, nullable)
- unit (string, nullable)
- institution_id (FK → institutions.id)
- category_id (FK → categories.id)
- created_at, updated_at
```

#### **donations**
```sql
- id (PK)
- user_id (FK → users.id, nullable) -- Doação pode ser anônima
- campaign_id (FK → campaigns.id)
- type (enum: 'entrada', 'saida')
- name
- description (nullable, text)
- quantify (integer)
- recipient_name (nullable, text)
- amount (decimal, nullable)
- created_at, updated_at
```

#### **collection_points**
```sql
- id (PK)
- title
- address_id (FK → addresses.id)
- campaign_id (FK → campaigns.id)
- created_at, updated_at
```

#### **schedules**
```sql
- id (PK)
- collection_point_id (FK → collection_points.id)
- dia (string)
- abertura (time, nullable)
- fechamento (time, nullable)
- created_at, updated_at
```

#### **photos** (Polimórfico)
```sql
- id (PK)
- filename
- imageable_id (bigint)
- imageable_type (string) -- 'App\Models\Institution' ou 'App\Models\Campaign'
- created_at, updated_at
```

#### **categories**
```sql
- id (PK)
- name
- is_active (boolean)
- created_at, updated_at
```

#### **rules**
```sql
- id (PK)
- name
- created_at, updated_at
```

#### **modules**
```sql
- id (PK)
- title
- created_at, updated_at
```

#### **module_user** (Tabela Pivot)
```sql
- id (PK)
- user_id (FK → users.id)
- module_id (FK → modules.id)
- created_at, updated_at
```

#### **addresses**
```sql
- id (PK)
- city (nullable)
- state (nullable)
- zip (nullable)
- road (nullable)
- neighborhood (nullable)
- complement (nullable)
- number (nullable)
- created_at, updated_at
```

---

## 📥 Instalação e Configuração

### Pré-requisitos
- PHP 8.2 ou superior
- Composer
- Node.js e NPM
- Banco de dados (MySQL, PostgreSQL ou SQLite)

### Passos para Instalação

1. **Clone o repositório**
```bash
git clone https://github.com/VilleNunes/bem_digital_trabalho_laravel.git
cd bem_digital_trabalho_laravel
```

2. **Instale as dependências do PHP**
```bash
composer install
```

3. **Instale as dependências do Node.js**
```bash
npm install
```

4. **Configure o ambiente**
```bash
cp .env.example .env
php artisan key:generate
```

5. **Configure o banco de dados no arquivo `.env`**
```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=bem_digital
DB_USERNAME=seu_usuario
DB_PASSWORD=sua_senha
```

6. **Execute as migrations e seeders**
```bash
php artisan migrate --seed
```

---

## ▶️ Executando o Projeto

### Desenvolvimento

**Terminal 1 - Frontend (Vite)**
```bash
npm run dev
```

**Terminal 2 - Backend (Laravel)**
```bash
php artisan serve
```

**Acesse no navegador:**
```
http://127.0.0.1:8000
```

### Comando Único (Desenvolvimento Completo)
```bash
composer dev
```
Este comando executa simultaneamente:
- Servidor Laravel
- Queue Worker
- Laravel Pail (logs)
- Vite (frontend)

---

## 🧪 Testes

Execute os testes com:
```bash
composer test
# ou
php artisan test
```

---

## 📁 Estrutura do Projeto

```
bem_digital_trabalho_laravel/
├── app/
│   ├── Http/
│   │   ├── Controllers/      # Controladores da aplicação
│   │   ├── Middleware/       # Middlewares customizados
│   │   └── Requests/         # Form Requests (validação)
│   └── Models/               # Modelos Eloquent
├── database/
│   ├── migrations/           # Migrations do banco de dados
│   ├── seeders/              # Seeders para dados iniciais
│   └── factories/            # Factories para testes
├── resources/
│   ├── views/                # Views Blade
│   │   ├── auth/             # Views de autenticação
│   │   ├── backend/          # Views do painel administrativo
│   │   └── frontend/         # Views do site público
│   ├── css/                  # Estilos CSS
│   └── js/                   # JavaScript
├── routes/
│   ├── web.php               # Rotas web
│   └── auth.php              # Rotas de autenticação
└── public/                    # Arquivos públicos
```

---

## 🔐 Sistema de Autenticação e Permissões

O sistema utiliza **Laravel Breeze** para autenticação e possui um sistema de permissões baseado em:
- **Rules (Regras)**: Perfis de usuário (Admin, User, etc.)
- **Modules (Módulos)**: Permissões específicas que podem ser atribuídas aos usuários
- **Middleware**: `CheckUserModule` verifica se o usuário possui acesso ao módulo

---

## 📝 Licença

Este projeto foi desenvolvido como trabalho acadêmico para a Faculdade UniAlfa.

---

## 👨‍💻 Desenvolvido por

Projeto desenvolvido para a disciplina de **Desenvolvimento de Sites Avançado** - **UniAlfa**

**Professor:** Alex Morgado Pereira
