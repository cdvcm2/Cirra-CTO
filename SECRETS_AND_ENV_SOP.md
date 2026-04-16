# SECRETS AND ENVIRONMENT VARIABLES SOP
**Version 1.0 | Cirra Edge | April 2026**

---

## Rules (no exceptions)

1. **Never commit .env files.** Not once. Not even empty ones. Git history is permanent.
2. **Use .env.local only.** Both .env and .env.local must be in .gitignore BEFORE the first commit.
3. **.gitignore is the first commit in every new repo.** Before any code, before any files.
4. **Edge Function secrets must NOT use the SUPABASE_ prefix.** Supabase reserves this prefix internally. Use SERVICE_ROLE_KEY, not SUPABASE_SERVICE_ROLE_KEY.
5. **Frontend env vars are public.** Anything prefixed VITE_ is baked into the client bundle. Never put secrets in VITE_ variables.
6. **Env vars in Vite are baked at build time.** Changing them requires a new deploy.

---

## If Secrets Were Exposed

If a .env file was committed to git at any point in history, ALL secrets in that file are compromised. Even if the file was later deleted, it remains in git history.

### Rotation Procedure

1. Generate new keys in Supabase dashboard (Settings > API > regenerate)
2. Update Cloudflare Pages environment variables with new keys
3. Update all Edge Function secrets with new keys
4. Redeploy all Edge Functions
5. Verify all portals still work (ops, driver, enterprise, partner)
6. Verify: git log --all -S "old_key_prefix" returns no results in new commits

### Verifying No Secrets in Git History

```bash
git log --all --name-only | grep -i "\.env"
git log --all -S "supabase" --oneline | head -20
git log --all -S "service_role" --oneline | head -20
git log --all -S "sk-" --oneline | head -20
```

If any of these return results, secrets have been in the history. Rotate immediately.

---

## Where Secrets Live (per platform)

| Platform | Where Secrets Are Stored | How to Access |
|----------|------------------------|---------------|
| Supabase | Dashboard > Settings > API | anon key + service role key |
| Supabase Edge Functions | Dashboard > Settings > Functions > Secrets | Key-value pairs |
| Cloudflare Pages | Dashboard > Settings > Environment Variables | Per-branch settings |
| Stripe | Dashboard > Developers > API Keys | Publishable + Secret key |
| Sentry | Dashboard > Settings > Client Keys (DSN) | DSN string |
| Resend | Dashboard > API Keys | API key |
| Anthropic | Console > API Keys | API key |

---

## Environment Variable Naming

| Context | Convention | Example |
|---------|-----------|---------|
| Vite frontend | VITE_ prefix (public, baked at build) | VITE_SUPABASE_URL |
| Edge Functions (Deno) | No prefix (use Deno.env.get) | SERVICE_ROLE_KEY |
| Cloudflare Pages | Match the VITE_ names for frontend vars | VITE_SUPABASE_ANON_KEY |

---

*SECRETS_AND_ENV_SOP.md | Version 1.0 | Cirra Edge | April 2026*
