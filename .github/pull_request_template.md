# Pull Request Template

## 📋 Description
Brief description of the changes and which issue this PR addresses.

Fixes # (issue number)

## 🏗️ Architecture Compliance Checklist

### Backend Changes (if applicable)
- [ ] Used CQRS pattern with MediatR (Commands/Queries)
- [ ] Added FluentValidation validators for all commands
- [ ] Only DTOs exposed in API responses (no domain entities)
- [ ] Added external ID fields for customer-facing entities
- [ ] Implemented proper error handling and logging
- [ ] Added unit tests for handlers

### Frontend Changes (if applicable)
- [ ] Used CSS custom properties (no hardcoded colors)
- [ ] Used Shadcn components with Tailwind classes
- [ ] Imported auto-generated TypeScript types from OpenAPI
- [ ] Followed file-based routing conventions
- [ ] Added proper error boundaries and loading states
- [ ] Tested theming compatibility

### Database Changes (if applicable)
- [ ] Added audit fields (CreatedAt, ModifiedAt, etc.)
- [ ] Added external ID fields where appropriate
- [ ] Created proper Entity Framework migrations
- [ ] Added database indexes for performance
- [ ] Considered data privacy and security implications

### API Changes (if applicable)
- [ ] Added API versioning attributes
- [ ] Updated Swagger/OpenAPI documentation
- [ ] Maintained backward compatibility
- [ ] Added proper authentication/authorization
- [ ] Considered rate limiting requirements

### Infrastructure Changes (if applicable)
- [ ] Updated Terraform modules if needed
- [ ] Considered multi-cloud compatibility
- [ ] Added monitoring and alerting
- [ ] Reviewed security implications
- [ ] Updated deployment documentation

## 🧪 Testing
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing completed
- [ ] Tested in multiple customer environments (if applicable)

## 📚 Documentation
- [ ] Updated relevant ADRs (if architectural changes)
- [ ] Updated LLM context guide (if new patterns)
- [ ] Added/updated API documentation
- [ ] Updated README (if new features)

## 🔒 Security Review
- [ ] No hardcoded secrets or credentials
- [ ] Proper input validation and sanitization
- [ ] OWASP compliance considerations
- [ ] SQL injection prevention
- [ ] Authentication/authorization properly implemented

## ⚡ Performance Impact
- [ ] Considered database query performance
- [ ] Reviewed bundle size impact (frontend)
- [ ] Added necessary caching
- [ ] Considered scalability implications

## 🎯 Customer Impact
- [ ] Backward compatible (no breaking changes)
- [ ] Considered multi-tenant implications
- [ ] Webhook events triggered appropriately
- [ ] Theming/branding compatibility maintained

## 📸 Screenshots (if applicable)
Add screenshots of UI changes, especially showing theming compatibility.

## 🔗 Related Issues/PRs
List any related issues or PRs that this change depends on or relates to.

## 📝 Additional Notes
Any additional information that reviewers should know about this change.

---

**For Reviewers:**
- [ ] Architecture compliance verified
- [ ] Code quality standards met
- [ ] Security review completed
- [ ] Documentation updated appropriately
- [ ] Test coverage adequate
