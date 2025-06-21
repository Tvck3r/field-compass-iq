# PR Automation & Architecture Compliance

This document explains the automated PR checking system that ensures all changes align with our architectural guidelines.

## 🎯 Overview

Our PR automation system consists of **4 main GitHub Actions workflows** that run automatically on every pull request:

1. **Architecture Compliance Check** - Validates adherence to architectural patterns
2. **Code Quality & Formatting** - Ensures code standards and formatting
3. **Security Scan** - Checks for security vulnerabilities and sensitive data
4. **Documentation Check** - Validates documentation completeness and consistency

## 🏗️ Architecture Compliance Checks

### Backend Architecture Validation

**Domain Entity Exposure Check**
```bash
# Fails if controllers directly return domain entities
find . -name "*.cs" -path "*/Controllers/*" -exec grep -l "Domain\." {} \;
```
✅ **Fix**: Use DTOs with AutoMapper for all API responses

**CQRS Pattern Validation**
```bash
# Ensures handlers implement CQRS pattern
find . -name "*Handler.cs" -exec grep -L "IRequestHandler\|IRequest" {} \;
```
✅ **Fix**: Implement `IRequestHandler<TRequest, TResponse>` interface

**Command Validator Check**
```bash
# Ensures every command has a FluentValidation validator
Commands=$(find . -name "*Command.cs" | wc -l)
Validators=$(find . -name "*CommandValidator.cs" | wc -l)
```
✅ **Fix**: Create `{CommandName}Validator : AbstractValidator<{CommandName}>`

**Repository Pattern Enforcement**
```bash
# Prevents direct EF usage in Application layer
find . -name "*.cs" -path "*/Application/*" -exec grep -l "DbContext\|DbSet" {} \;
```
✅ **Fix**: Use repository interfaces instead of direct DbContext

### Frontend Architecture Validation

**Hardcoded Color Detection**
```bash
# Detects hardcoded Tailwind colors
find . -name "*.tsx" | xargs grep -l "bg-blue\|text-red\|border-green"
```
✅ **Fix**: Use CSS custom properties like `bg-primary-500`

**Type Safety Enforcement**
```bash
# Ensures use of auto-generated API types
find . -name "*.tsx" | xargs grep -L "import.*from.*@/types/api"
```
✅ **Fix**: Import and use auto-generated TypeScript types

**Inline Style Prevention**
```bash
# Prevents inline styles that break theming
find . -name "*.tsx" | xargs grep -l "style={{"
```
✅ **Fix**: Use Tailwind classes with CSS custom properties

### API & Database Validation

**API Versioning Check**
```bash
# Ensures controllers have API versioning
find . -name "*Controller.cs" -exec grep -L "ApiVersion\|Route.*v[0-9]" {} \;
```
✅ **Fix**: Add `[ApiVersion("1.0")]` and versioned routes

**External ID Field Warning**
```bash
# Warns about missing ExternalId fields in new tables
find . -name "*Migration*.cs" -exec grep -L "ExternalId" {} \;
```
✅ **Fix**: Add `ExternalId` property to customer-facing entities

## 🔒 Security Checks

### Secret Detection
```bash
# Scans for hardcoded secrets and passwords
grep -i "password\s*=\|secret\s*=\|key\s*=" files
```

### SQL Injection Prevention
```bash
# Detects potential SQL injection vulnerabilities
grep "\"SELECT \|\"INSERT \|\"UPDATE \|\"DELETE " *.cs
```

### Dependency Vulnerabilities
```bash
# Checks for vulnerable packages
dotnet list package --vulnerable --include-transitive
npm audit --audit-level=high
```

## 📋 Code Quality Standards

### Backend (.NET)
- **CSharpier formatting** enforced
- **Roslyn analyzers** for code quality
- **Unit test requirements**
- **Architecture tests** (when available)

### Frontend (React/TypeScript)
- **Prettier formatting** enforced
- **ESLint rules** for code quality
- **TypeScript strict mode** required
- **Build verification** required

## 📚 Documentation Compliance

### API Documentation
- **XML comments required** for all public APIs
- **Swagger documentation** auto-generated from XML comments

### Architecture Documentation
- **ADR updates** required for architectural changes
- **LLM context updates** for new patterns
- **README updates** for significant new features

### Link Validation
- **Markdown link checking** for all documentation
- **Broken link detection** and reporting

## 🔧 PR Template Integration

Our PR template includes architecture compliance checklists:

### Backend Checklist
- [ ] Used CQRS pattern with MediatR
- [ ] Added FluentValidation validators
- [ ] Only DTOs exposed in API responses
- [ ] Added external ID fields
- [ ] Proper error handling and logging

### Frontend Checklist
- [ ] Used CSS custom properties
- [ ] Used Shadcn components
- [ ] Auto-generated TypeScript types
- [ ] File-based routing conventions
- [ ] Theming compatibility tested

### Security Checklist
- [ ] No hardcoded secrets
- [ ] Proper input validation
- [ ] OWASP compliance
- [ ] SQL injection prevention

## 🚨 When Checks Fail

### Automatic Actions
1. **PR status check fails** - Prevents merging
2. **Comment added to PR** with specific guidance
3. **Architecture report generated** in GitHub summary
4. **Links to documentation** provided for fixes

### Common Failure Scenarios

**❌ Domain Entity Exposed**
```
Error: Domain entities exposed in controllers
Fix: Create DTOs and use AutoMapper
```

**❌ Missing Validator**
```
Error: Commands without validators detected  
Fix: Create FluentValidation validator for each command
```

**❌ Hardcoded Colors**
```
Error: Hardcoded colors detected
Fix: Use CSS custom properties for theming
```

**❌ Security Violation**
```
Error: Potential hardcoded secrets detected
Fix: Use configuration providers or secret managers
```

## 🔄 Workflow Triggers

### Architecture Compliance
**Runs on**: Changes to `src/**`, `tests/**`, `*.cs`, `*.tsx`, `*.ts`, `*.json`
**Checks**: CQRS patterns, DTO usage, theming compliance, API versioning

### Code Quality  
**Runs on**: All PRs to `main`/`develop`
**Checks**: Formatting (CSharpier/Prettier), linting, tests, builds

### Security Scan
**Runs on**: All PRs + weekly schedule
**Checks**: Vulnerabilities, secrets, dependency security

### Documentation
**Runs on**: Changes to `docs/**`, `**/*.md`, source files
**Checks**: API docs, ADR consistency, link validation

## 🎛️ Configuration Files

### Formatting Configuration
- **CSharpier**: Uses default configuration
- **Prettier**: Configured in `.prettierrc`
- **ESLint**: Configured in `.eslintrc.json`

### Security Configuration
- **Trivy**: Vulnerability scanning
- **DevSkim**: Security pattern detection
- **Custom scripts**: Secret and injection detection

### Documentation Configuration
- **Markdown link check**: `.github/markdown-link-check-config.json`
- **Issue templates**: Architecture violation reporting

## 🚀 Benefits

### Development Quality
- **Consistent architecture** across all features
- **Early detection** of violations
- **Automated enforcement** of best practices
- **Security vulnerabilities** caught before merge

### Team Productivity
- **Clear feedback** on what needs fixing
- **Reduced review time** with automated checks
- **Documentation links** for quick resolution
- **Standardized patterns** for new developers

### Customer Protection
- **Multi-tenant isolation** enforced
- **Security standards** maintained
- **Theming compatibility** guaranteed
- **API stability** through versioning

## 🔧 Customization

### Adding New Checks
1. **Edit workflow files** in `.github/workflows/`
2. **Add new validation scripts** 
3. **Update PR template** with new checklist items
4. **Document new requirements** in LLM context guide

### Adjusting Strictness
- **Modify grep patterns** for different sensitivity
- **Add exclusions** for special cases
- **Convert errors to warnings** for gradual adoption
- **Customize thresholds** for metrics

This automated system ensures that every code change aligns with our architectural vision while providing clear guidance for developers to fix any issues.
