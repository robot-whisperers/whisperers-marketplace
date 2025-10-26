---
name: Cloudflare Deploy
description: Deploy backend applications to Cloudflare Workers, Pages, and Durable Objects using Wrangler CLI without MCP dependencies
version: 1.0.0
author: Claude
tags:
  - cloudflare
  - deployment
  - workers
  - edge
  - serverless
  - wrangler
  - backend
  - api
capabilities:
  - Deploy Cloudflare Workers
  - Deploy Cloudflare Pages with Functions
  - Configure Durable Objects
  - Manage KV, D1, and R2 storage
  - Set up environments and secrets
  - Local development and testing
  - CI/CD integration
---

# Cloudflare Deployment Skill

## Overview
This skill enables deployment of backend applications to Cloudflare's edge platform using Wrangler CLI. It covers Cloudflare Workers, Pages Functions, and Durable Objects without requiring MCP tools.

## Prerequisites Check

Before deploying, verify:
```bash
# Check if Wrangler is installed
wrangler --version

# If not installed:
npm install -g wrangler

# Login to Cloudflare (opens browser for auth)
wrangler login
```

## Project Structure Detection

Identify the project type by checking for:

### Cloudflare Workers
- `wrangler.toml` in root
- Entry point: typically `src/index.js`, `src/index.ts`, or `worker.js`
- No `functions/` directory

### Cloudflare Pages + Functions
- `functions/` directory (serverless functions)
- Static assets in root, `public/`, or `dist/`
- Optional `wrangler.toml` for configuration

### Durable Objects
- `wrangler.toml` with `[durable_objects]` section
- Class-based implementation with `fetch()` method

## Configuration Setup

### Worker Configuration (wrangler.toml)

```toml
name = "my-worker"
main = "src/index.js"
compatibility_date = "2024-01-01"

# Account details
account_id = "your-account-id"  # Get from: wrangler whoami

# Environment variables
[vars]
ENVIRONMENT = "production"

# Secrets (don't put in file)
# Set with: wrangler secret put SECRET_NAME

# KV Namespaces
[[kv_namespaces]]
binding = "MY_KV"
id = "kv_namespace_id"

# Durable Objects
[[durable_objects.bindings]]
name = "MY_DURABLE_OBJECT"
class_name = "MyDurableObject"
script_name = "my-worker"

# R2 Buckets
[[r2_buckets]]
binding = "MY_BUCKET"
bucket_name = "my-bucket"

# D1 Databases
[[d1_databases]]
binding = "DB"
database_name = "my-db"
database_id = "database-id"

# Service bindings (for calling other Workers)
[[services]]
binding = "OTHER_SERVICE"
service = "other-worker-name"
```

### Pages Configuration

For Pages, create `functions/_middleware.js` or `functions/_middleware.ts` for shared logic:

```typescript
// functions/_middleware.ts
export async function onRequest(context) {
  // Add CORS, auth, logging, etc.
  return await context.next();
}
```

## Deployment Workflows

### 1. First-Time Worker Deployment

```bash
# Initialize if no wrangler.toml exists
wrangler init my-worker

# Preview locally
wrangler dev

# Deploy to production
wrangler deploy

# Deploy to specific environment
wrangler deploy --env staging
```

### 2. Pages Deployment

```bash
# Deploy static site + functions
wrangler pages deploy <BUILD_OUTPUT_DIR>

# Example: Deploy dist/ folder
wrangler pages deploy dist

# With specific project name
wrangler pages deploy dist --project-name=my-site

# Preview deployment (doesn't go to production)
wrangler pages deploy dist --branch=preview
```

### 3. Update Existing Deployment

```bash
# Simply run deploy again
wrangler deploy

# View deployment status
wrangler deployments list
```

### 4. Rollback

```bash
# List recent deployments
wrangler deployments list

# View specific deployment
wrangler deployments view <DEPLOYMENT_ID>

# To rollback: redeploy a previous version
# (Cloudflare doesn't have direct rollback, but you can redeploy old code)
```

## Environment Management

### Setting Up Multiple Environments

```toml
# wrangler.toml
name = "my-worker"
main = "src/index.js"

[env.production]
name = "my-worker-prod"
vars = { ENVIRONMENT = "production" }

[env.staging]
name = "my-worker-staging"
vars = { ENVIRONMENT = "staging" }
```

Deploy to specific environment:
```bash
wrangler deploy --env staging
wrangler deploy --env production
```

### Managing Secrets

```bash
# Add secret (prompts for value)
wrangler secret put API_KEY

# Add secret to specific environment
wrangler secret put API_KEY --env production

# List secrets (doesn't show values)
wrangler secret list

# Delete secret
wrangler secret delete API_KEY
```

### Environment Variables vs Secrets

**Use vars for:** Non-sensitive configuration
**Use secrets for:** API keys, tokens, passwords

```toml
[vars]
API_URL = "https://api.example.com"  # OK - not sensitive

# Never put sensitive data in vars!
# API_KEY = "secret123"  # WRONG!
```

## Common Patterns

### 1. REST API Worker

```typescript
// src/index.ts
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const url = new URL(request.url);
    
    // CORS headers
    const corsHeaders = {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
      'Access-Control-Allow-Headers': 'Content-Type, Authorization',
    };
    
    if (request.method === 'OPTIONS') {
      return new Response(null, { headers: corsHeaders });
    }
    
    // Router
    if (url.pathname === '/api/health') {
      return new Response(JSON.stringify({ status: 'ok' }), {
        headers: { ...corsHeaders, 'Content-Type': 'application/json' },
      });
    }
    
    if (url.pathname.startsWith('/api/items')) {
      // Your API logic
      return new Response(JSON.stringify({ items: [] }), {
        headers: { ...corsHeaders, 'Content-Type': 'application/json' },
      });
    }
    
    return new Response('Not Found', { status: 404, headers: corsHeaders });
  },
};
```

### 2. Using KV Storage

```typescript
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    // Write to KV
    await env.MY_KV.put('key', 'value', {
      expirationTtl: 3600, // expires in 1 hour
    });
    
    // Read from KV
    const value = await env.MY_KV.get('key');
    
    // Delete from KV
    await env.MY_KV.delete('key');
    
    // List keys
    const keys = await env.MY_KV.list({ prefix: 'user:' });
    
    return new Response(value);
  },
};
```

### 3. Using D1 Database

```typescript
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    // Query
    const result = await env.DB.prepare(
      'SELECT * FROM users WHERE id = ?'
    ).bind(1).first();
    
    // Insert
    await env.DB.prepare(
      'INSERT INTO users (name, email) VALUES (?, ?)'
    ).bind('John', 'john@example.com').run();
    
    // Transaction
    await env.DB.batch([
      env.DB.prepare('INSERT INTO users (name) VALUES (?)').bind('Alice'),
      env.DB.prepare('INSERT INTO users (name) VALUES (?)').bind('Bob'),
    ]);
    
    return new Response(JSON.stringify(result), {
      headers: { 'Content-Type': 'application/json' },
    });
  },
};
```

### 4. Scheduled Jobs (Cron)

```toml
# wrangler.toml
[triggers]
crons = ["0 0 * * *"]  # Daily at midnight
```

```typescript
export default {
  async scheduled(event: ScheduledEvent, env: Env, ctx: ExecutionContext) {
    // Your scheduled task
    console.log('Cron job executed at', new Date(event.scheduledTime));
    await env.MY_KV.put('last_run', new Date().toISOString());
  },
};
```

## Testing Strategy

### Local Development

```bash
# Start local dev server with hot reload
wrangler dev

# Dev server with remote resources (KV, D1, etc)
wrangler dev --remote

# Specify port
wrangler dev --port 8787

# Test with local D1
wrangler d1 execute DB --local --command "SELECT * FROM users"
```

### Integration Testing

```bash
# Deploy to preview environment
wrangler deploy --env preview

# Run tests against preview
npm test -- --env=preview
```

## Database Management (D1)

```bash
# Create database
wrangler d1 create my-database

# Execute SQL
wrangler d1 execute DB --command "CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT)"

# Execute from file
wrangler d1 execute DB --file ./schema.sql

# Local D1 for development
wrangler d1 execute DB --local --command "INSERT INTO users (name) VALUES ('Test')"

# Backup
wrangler d1 export DB --output backup.sql
```

## KV Management

```bash
# Create KV namespace
wrangler kv:namespace create MY_KV

# Put value
wrangler kv:key put --namespace-id=<ID> "key" "value"

# Get value
wrangler kv:key get --namespace-id=<ID> "key"

# List keys
wrangler kv:key list --namespace-id=<ID>

# Delete key
wrangler kv:key delete --namespace-id=<ID> "key"
```

## Troubleshooting

### Common Issues

#### 1. "No account_id found"
```bash
# Get account ID
wrangler whoami

# Add to wrangler.toml
account_id = "your-account-id"
```

#### 2. "Module not found" errors
- Ensure `package.json` includes all dependencies
- Run `npm install` before deploying
- Check `main` field in `wrangler.toml` points to correct entry file

#### 3. "Exceeded CPU time limit"
- Workers have 10ms-50ms CPU time limit (depending on plan)
- Use `waitUntil()` for non-blocking operations
- Optimize hot code paths
```typescript
ctx.waitUntil(
  // Non-blocking analytics or cleanup
  logEvent(request)
);
```

#### 4. "Script size exceeded"
- Free plan: 1MB, Paid: 5MB
- Check bundle size: `wrangler deploy --dry-run`
- Use code splitting, dynamic imports
- Remove unused dependencies

#### 5. Binding not available
```bash
# Verify bindings are configured
wrangler tail  # Watch logs in real-time

# Check wrangler.toml matches your code
# Binding name in config must match env.BINDING_NAME in code
```

### Viewing Logs

```bash
# Tail logs in real-time
wrangler tail

# Filter by status
wrangler tail --status error

# Specific environment
wrangler tail --env production
```

## Deployment Checklist

Before deploying to production:

- [ ] All secrets configured: `wrangler secret list`
- [ ] Environment variables set in `wrangler.toml`
- [ ] KV/D1/R2 bindings created and configured
- [ ] Tested locally: `wrangler dev`
- [ ] Compatibility date updated to recent date
- [ ] Error handling implemented
- [ ] CORS configured (if needed)
- [ ] Rate limiting implemented (if needed)
- [ ] Logging added for debugging
- [ ] Routes configured correctly
- [ ] Custom domain set up (if needed)

## Custom Domains

```bash
# Add custom domain via dashboard or API
# Or use Cloudflare Pages for automatic HTTPS

# Workers: Use Routes in wrangler.toml
routes = [
  { pattern = "api.example.com/*", zone_name = "example.com" }
]

# Or add via CLI
wrangler route create api.example.com/* --zone-id <ZONE_ID>
```

## Performance Optimization

### 1. Caching
```typescript
// Cache API responses
const cache = caches.default;
const cacheKey = new Request(url.toString(), request);

let response = await cache.match(cacheKey);
if (!response) {
  response = await fetch(request);
  ctx.waitUntil(cache.put(cacheKey, response.clone()));
}
```

### 2. Smart Placement
```toml
# Enable Smart Placement for optimal routing
[placement]
mode = "smart"
```

### 3. Streaming Responses
```typescript
const { readable, writable } = new TransformStream();
ctx.waitUntil(
  streamData(writable)
);
return new Response(readable);
```

## CI/CD Integration

### GitHub Actions Example

```yaml
# .github/workflows/deploy.yml
name: Deploy to Cloudflare

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Deploy
        uses: cloudflare/wrangler-action@v3
        with:
          apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
          command: deploy
```

### Setting Up API Token

1. Go to Cloudflare Dashboard → My Profile → API Tokens
2. Create Token → Edit Cloudflare Workers template
3. Add token to GitHub Secrets as `CLOUDFLARE_API_TOKEN`

## Best Practices

1. **Use TypeScript** for type safety
2. **Implement error handling** with try-catch
3. **Add request validation** before processing
4. **Use waitUntil()** for non-blocking operations
5. **Configure CORS** properly for APIs
6. **Set compatibility_date** to recent date
7. **Monitor with Logpush** or `wrangler tail`
8. **Use environment-specific configs** for staging/prod
9. **Keep secrets out of code** (use `wrangler secret`)
10. **Test locally first** with `wrangler dev --remote`

## Additional Resources

- Cloudflare Workers Docs: https://developers.cloudflare.com/workers/
- Wrangler CLI Docs: https://developers.cloudflare.com/workers/wrangler/
- Examples: https://github.com/cloudflare/workers-sdk/tree/main/templates

## Quick Reference

```bash
# Essential commands
wrangler login                    # Authenticate
wrangler whoami                   # Check account
wrangler init                     # Initialize project
wrangler dev                      # Local development
wrangler deploy                   # Deploy to production
wrangler tail                     # View logs
wrangler secret put SECRET_NAME   # Add secret
wrangler d1 create DB_NAME        # Create D1 database
wrangler kv:namespace create KV   # Create KV namespace
wrangler deployments list         # View deployments
```
