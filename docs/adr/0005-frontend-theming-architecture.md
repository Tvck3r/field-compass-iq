# ADR-0005: Dynamic Frontend Theming Architecture

**Status:** Accepted
**Date:** 2025-06-21
**Deciders:** Development Team

## Context

Field-Compass-IQ needs to support enterprise customers who want to embed our interface within their existing applications or maintain brand consistency. Customers should be able to provide their own color palettes, fonts, and styling to create a seamless integration with their existing systems. The solution must support iframe embedding and white-label scenarios.

## Decision

We will implement a **dynamic theming system** using CSS custom properties (CSS variables) with Tailwind CSS, allowing customers to provide a single theming configuration file that completely transforms the application's appearance without code changes.

## Consequences

### Positive
- **Brand consistency**: Customers can match our interface to their existing brand
- **Iframe-friendly**: Works seamlessly when embedded in customer portals
- **White-label support**: Can completely rebrand the application per customer
- **No code changes**: Theming changes don't require application deployment
- **Real-time updates**: Theme changes can be applied without page refresh
- **Maintainable**: Single source of truth for all styling decisions
- **Developer friendly**: Uses familiar CSS custom properties

### Negative
- **Initial complexity**: More complex setup than hardcoded styles
- **Testing overhead**: Need to test with multiple themes
- **Performance considerations**: CSS variables may have slight performance impact
- **Design constraints**: Need to design components to work with various color schemes

### Neutral
- **Theme validation**: Need to validate customer theme configurations
- **Documentation**: Comprehensive theming guide required for customers
- **Fallback handling**: Robust fallbacks for invalid theme values

## Alternatives Considered

1. **Hardcoded Tailwind Classes**
   - Rejected: No customization possible, doesn't meet customer requirements

2. **Runtime CSS Generation**
   - Rejected: Complex, performance issues, SEO problems

3. **Multiple Build Targets**
   - Rejected: Requires separate builds per customer, deployment complexity

4. **SCSS Variables**
   - Rejected: Requires build-time compilation, not dynamic

5. **Styled Components with Themes**
   - Rejected: Runtime performance overhead, conflicts with Tailwind approach

## Implementation Notes

### Theme Configuration Structure
```json
{
  "name": "Customer Brand Theme",
  "version": "1.0.0",
  "colors": {
    "primary": {
      "50": "#eff6ff",
      "100": "#dbeafe",
      "500": "#3b82f6",
      "900": "#1e3a8a"
    },
    "secondary": {
      "50": "#f0fdf4",
      "500": "#22c55e",
      "900": "#14532d"
    },
    "background": "#ffffff",
    "surface": "#f8fafc",
    "text": {
      "primary": "#1f2937",
      "secondary": "#6b7280"
    }
  },
  "typography": {
    "fontFamily": {
      "sans": ["Inter", "sans-serif"],
      "mono": ["JetBrains Mono", "monospace"]
    },
    "fontSize": {
      "xs": "0.75rem",
      "sm": "0.875rem",
      "base": "1rem",
      "lg": "1.125rem"
    }
  },
  "spacing": {
    "borderRadius": {
      "sm": "0.25rem",
      "md": "0.375rem",
      "lg": "0.5rem"
    }
  },
  "branding": {
    "logo": "https://customer.com/logo.svg",
    "favicon": "https://customer.com/favicon.ico"
  }
}
```

### CSS Custom Properties Implementation
```css
:root {
  /* Colors */
  --color-primary-50: 239 246 255;
  --color-primary-500: 59 130 246;
  --color-primary-900: 30 58 138;
  
  /* Typography */
  --font-family-sans: 'Inter', sans-serif;
  --font-size-base: 1rem;
  
  /* Spacing */
  --border-radius-md: 0.375rem;
}

/* Dark mode support */
[data-theme="dark"] {
  --color-background: 15 23 42;
  --color-text-primary: 248 250 252;
}
```

### Tailwind Configuration
```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: {
          50: 'rgb(var(--color-primary-50) / <alpha-value>)',
          500: 'rgb(var(--color-primary-500) / <alpha-value>)',
          900: 'rgb(var(--color-primary-900) / <alpha-value>)',
        }
      },
      fontFamily: {
        sans: ['var(--font-family-sans)', ...defaultTheme.fontFamily.sans],
      }
    }
  }
}
```

### Component Implementation
```tsx
// Button component example
const Button = ({ variant = "primary", children, ...props }) => {
  return (
    <button
      className={cn(
        "px-4 py-2 rounded-md font-medium transition-colors",
        {
          "bg-primary-500 text-white hover:bg-primary-600": variant === "primary",
          "bg-secondary-500 text-white hover:bg-secondary-600": variant === "secondary",
        }
      )}
      {...props}
    >
      {children}
    </button>
  );
};
```

### Theme Loading System
- **Runtime Loading**: Themes loaded via API call on application startup
- **Caching**: Theme configurations cached in browser storage
- **Validation**: Schema validation for theme configurations
- **Fallbacks**: Graceful degradation for missing or invalid theme values
- **Hot Reloading**: Development mode supports real-time theme updates

### Customer Integration
1. **Theme Configuration**: Customer provides theme JSON configuration
2. **Asset Hosting**: Customer hosts logo, favicon, and custom fonts
3. **API Integration**: Theme configuration stored per customer tenant
4. **Preview Mode**: Customers can preview themes before applying
5. **Version Control**: Theme configurations versioned for rollback capability

### Technical Requirements
- **CSS Custom Properties**: All styling uses CSS variables
- **Tailwind Integration**: Custom Tailwind plugin for dynamic values
- **TypeScript Support**: Type definitions for theme configuration
- **Accessibility**: Ensure color contrast ratios meet WCAG guidelines
- **Dark Mode**: Built-in support for dark/light theme variants
- **Performance**: Minimize CSS bundle size and runtime overhead

### File Structure
```
src/
├── styles/
│   ├── globals.css          # CSS custom properties
│   ├── themes/
│   │   ├── default.json     # Default theme
│   │   └── dark.json        # Default dark theme
├── hooks/
│   └── useTheme.ts          # Theme management hook
├── providers/
│   └── ThemeProvider.tsx    # Theme context provider
└── utils/
    ├── themeValidator.ts    # Theme configuration validation
    └── cssVariables.ts      # CSS variable utilities
```
