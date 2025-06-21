# Development Setup Guide

This guide will help you set up your development environment for Field-Compass-IQ.

## Prerequisites

### Required Software
- **Node.js 18+** - [Download](https://nodejs.org/)
- **.NET 9 SDK** - [Download](https://dotnet.microsoft.com/download)
- **Docker Desktop** - [Download](https://www.docker.com/products/docker-desktop)
- **Terraform 1.5+** - [Download](https://terraform.io/downloads)
- **Git** - [Download](https://git-scm.com/)

### Required VS Code Extensions

#### Backend Development (.NET)
- **C# Dev Kit** (`ms-dotnettools.csdevkit`)
- **C#** (`ms-dotnettools.csharp`)
- **CSharpier - Code formatter** (`csharpier.csharpier-vscode`)
- **.NET Install Tool** (`ms-dotnettools.vscode-dotnet-runtime`)

#### Frontend Development (React/TypeScript)
- **Prettier - Code formatter** (`esbenp.prettier-vscode`)
- **ESLint** (`dbaeumer.vscode-eslint`)
- **TypeScript Importer** (`pmneo.tsimporter`)
- **Tailwind CSS IntelliSense** (`bradlc.vscode-tailwindcss`)
- **Auto Rename Tag** (`formulahendry.auto-rename-tag`)

#### Infrastructure & DevOps
- **HashiCorp Terraform** (`hashicorp.terraform`)
- **Docker** (`ms-azuretools.vscode-docker`)
- **YAML** (`redhat.vscode-yaml`)

#### General Development
- **GitLens** (`eamodio.gitlens`)
- **Thunder Client** (`rangav.vscode-thunder-client`)
- **Error Lens** (`usernamehw.errorlens`)
- **Path Intellisense** (`christian-kohler.path-intellisense`)

## Repository Setup

### Clone Repositories
```bash
# Main project repository
git clone https://github.com/Tvck3r/field-compass-iq.git
cd field-compass-iq

# Clone all related repositories (when available)
git clone https://github.com/Tvck3r/field-compass-iq-api.git
git clone https://github.com/Tvck3r/field-compass-iq-web.git
git clone https://github.com/Tvck3r/field-compass-iq-mobile.git
git clone https://github.com/Tvck3r/field-compass-iq-infra.git
```

### Development Environment Configuration

#### VS Code Settings
Create `.vscode/settings.json` in each repository:

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "[csharp]": {
    "editor.defaultFormatter": "csharpier.csharpier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "typescript.preferences.importModuleSpecifier": "relative",
  "tailwindCSS.experimental.classRegex": [
    ["cn\\(([^)]*)\\)", "(?:'|\"|`)([^']*)(?:'|\"|`)"]
  ]
}
```

#### VS Code Extensions Configuration
Create `.vscode/extensions.json` in each repository:

```json
{
  "recommendations": [
    "ms-dotnettools.csdevkit",
    "ms-dotnettools.csharp",
    "csharpier.csharpier-vscode",
    "esbenp.prettier-vscode",
    "dbaeumer.vscode-eslint",
    "pmneo.tsimporter",
    "bradlc.vscode-tailwindcss",
    "hashicorp.terraform",
    "ms-azuretools.vscode-docker",
    "eamodio.gitlens",
    "rangav.vscode-thunder-client"
  ]
}
```

## Backend Setup (.NET API)

### Project Structure
```
field-compass-iq-api/
├── src/
│   ├── FieldCompass.API/           # Web API project
│   ├── FieldCompass.Application/   # CQRS/MediatR handlers
│   ├── FieldCompass.Domain/        # Domain entities and logic
│   ├── FieldCompass.Infrastructure/ # Data access and external services
│   └── FieldCompass.Shared/        # Shared DTOs and utilities
├── tests/
│   ├── FieldCompass.UnitTests/
│   ├── FieldCompass.IntegrationTests/
│   └── FieldCompass.ArchitectureTests/
└── docs/
    └── api/
```

### Required NuGet Packages
- **MediatR** - CQRS implementation
- **FluentValidation** - Command validation
- **AutoMapper** - Entity-DTO mapping
- **Entity Framework Core** - Data access
- **Swashbuckle** - Swagger documentation
- **Serilog** - Logging
- **xUnit** - Testing framework

### Local Development Database
```bash
# Start local PostgreSQL with Docker
docker run --name fieldcompass-db \
  -e POSTGRES_DB=fieldcompass_dev \
  -e POSTGRES_USER=dev \
  -e POSTGRES_PASSWORD=devpassword \
  -p 5432:5432 \
  -d postgres:15
```

### Environment Configuration
Create `appsettings.Development.json`:
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Database=fieldcompass_dev;Username=dev;Password=devpassword"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```

## Frontend Setup (React/TypeScript)

### Project Structure
```
field-compass-iq-web/
├── src/
│   ├── app/                    # Next.js app directory
│   ├── components/
│   │   ├── ui/                 # Shadcn components
│   │   └── features/           # Feature-specific components
│   ├── hooks/                  # Custom React hooks
│   ├── lib/                    # Utilities and configurations
│   ├── providers/              # Context providers
│   ├── styles/                 # Global styles and themes
│   └── types/                  # TypeScript type definitions
├── public/                     # Static assets
└── docs/
```

### Package Installation
```bash
cd field-compass-iq-web
npm install

# Install required dependencies
npm install @tanstack/react-query
npm install @hookform/resolvers
npm install zod
npm install lucide-react
npm install class-variance-authority
npm install clsx tailwind-merge
```

### Environment Configuration
Create `.env.local`:
```env
NEXT_PUBLIC_API_URL=http://localhost:5000/api
NEXT_PUBLIC_APP_NAME=Field Compass IQ
```

### TypeScript Configuration
Ensure `tsconfig.json` includes:
```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "es6"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

## Infrastructure Setup (Terraform)

### Project Structure
```
field-compass-iq-infra/
├── modules/
│   ├── gcp/
│   │   ├── project/
│   │   ├── database/
│   │   ├── compute/
│   │   └── monitoring/
│   ├── aws/                    # Future implementation
│   └── azure/                  # Future implementation
├── environments/
│   ├── dev/
│   ├── staging/
│   └── production/
└── customers/
    └── [customer-id]/
        ├── dev/
        ├── staging/
        └── production/
```

### GCP Setup
1. Install Google Cloud SDK
2. Authenticate: `gcloud auth login`
3. Set project: `gcloud config set project [PROJECT_ID]`
4. Enable required APIs:
   ```bash
   gcloud services enable compute.googleapis.com
   gcloud services enable sqladmin.googleapis.com
   gcloud services enable container.googleapis.com
   ```

## Development Workflow

### Daily Development
1. **Pull latest changes**: `git pull origin main`
2. **Start backend**: 
   ```bash
   cd field-compass-iq-api
   dotnet run --project src/FieldCompass.API
   ```
3. **Start frontend**:
   ```bash
   cd field-compass-iq-web
   npm run dev
   ```
4. **Access applications**:
   - API: http://localhost:5000
   - Swagger: http://localhost:5000/swagger
   - Frontend: http://localhost:3000

### Code Quality Checks
```bash
# Backend formatting (CSharpier)
dotnet csharpier .

# Frontend formatting (Prettier)
npm run format

# Frontend linting (ESLint)
npm run lint

# Run tests
dotnet test  # Backend
npm test     # Frontend
```

### Git Workflow
1. Create feature branch: `git checkout -b feature/description`
2. Make changes and commit: `git commit -m "feat: description"`
3. Push changes: `git push origin feature/description`
4. Create pull request
5. After review and approval, merge to main

## Troubleshooting

### Common Issues

#### Backend Issues
- **Database connection**: Ensure PostgreSQL container is running
- **Package restore**: Run `dotnet restore`
- **Migration issues**: Run `dotnet ef database update`

#### Frontend Issues
- **Dependencies**: Delete `node_modules` and run `npm install`
- **Port conflicts**: Change port in `package.json` scripts
- **Type errors**: Run `npm run type-check`

#### Infrastructure Issues
- **Terraform state**: Ensure backend state configuration is correct
- **GCP authentication**: Run `gcloud auth application-default login`
- **Permissions**: Verify IAM roles and permissions

### Getting Help
- Check the [troubleshooting guide](./troubleshooting.md)
- Review relevant ADRs in [docs/adr/](./adr/)
- Ask questions in team chat or create GitHub issues
