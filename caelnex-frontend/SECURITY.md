# Caelnex Frontend — Security & Deployment Notes

## Two surfaces, two postures

| | `public/` | `dashboard/` |
|---|---|---|
| **Audience** | Anyone (investors, partners, public web) | Founding team + future subscribers only |
| **Network** | Public internet, static hosting | Intranet only — never exposed externally |
| **Auth** | None needed — no sensitive data | Keycloak (or equivalent), required |
| **Content** | Description, architecture overview, contact | Live graph state, supplier names, anomaly details, audit log |

The split matters: nothing in `public/` should ever reveal data that lives in
the graph (supplier names, specific anomalies, real campaign IDs). The example
data shown publicly (the three case studies) is historical and already public
via NASA/ESA investigation reports — safe to reference.

---

## Dashboard authentication — current state vs production

The `dashboard/index.html` file currently ships with a **demo auth screen**:
any non-empty username/password is accepted, and role is inferred from the
username string (`admin` → VP Engineering, `lead` → Organ Lead, else Viewer).

This is clearly marked in the code and in the UI ("Demo mode — any non-empty
credentials accepted"). **This must not go to any real deployment as-is.**

### Replacing demo auth with Keycloak

The architecture (see main README) already specifies Keycloak as the identity
provider — this is consistent with the offline-first, intranet deployment
model, since Keycloak can be self-hosted with zero external dependencies.

Replacement steps:

1. **Deploy Keycloak** on the intranet (Docker image: `quay.io/keycloak/keycloak`).
   Create a realm (`caelnex`), and a client for the dashboard
   (`caelnex-dashboard`, public client, PKCE enabled).

2. **Define roles** matching the authority table already in the UI:
   - `vp-engineering` — can authorise overrides
   - `organ-lead` — can disposition intercepts within their organ
   - `viewer` — read-only

3. **Install the JS adapter**:
   ```html
   <script src="https://your-keycloak-host/js/keycloak.js"></script>
   ```
   (Self-hosted — this is served by your own Keycloak instance, not a CDN.)

4. **Replace the demo auth block** (`<script>` at the bottom of
   `dashboard/index.html`) with:
   ```js
   const keycloak = new Keycloak({
     url: 'https://your-keycloak-host/',
     realm: 'caelnex',
     clientId: 'caelnex-dashboard'
   });

   keycloak.init({ onLoad: 'login-required', pkceMethod: 'S256' })
     .then(authenticated => {
       if (authenticated) {
         const username = keycloak.tokenParsed.preferred_username;
         const roles = keycloak.tokenParsed.realm_access.roles;
         const role = roles.includes('vp-engineering') ? 'VP Engineering'
                     : roles.includes('organ-lead') ? 'Organ Lead'
                     : 'Viewer';
         showDashboard(username, role);

         // Attach token to all GraphQL requests:
         // headers: { Authorization: `Bearer ${keycloak.token}` }

         // Refresh token before expiry:
         setInterval(() => keycloak.updateToken(70), 60000);
       }
     });

   document.getElementById('logout-btn').addEventListener('click', () => {
     keycloak.logout();
   });
   ```

5. **Remove** the `sessionStorage`-based demo session entirely — Keycloak's
   adapter manages session state via its own secure mechanism.

---

## HTTP security headers (both surfaces)

Apply via whatever serves the static files — nginx config shown, adapt for
your reverse proxy:

```nginx
# Public site — standard hardening
add_header X-Content-Type-Options "nosniff" always;
add_header X-Frame-Options "DENY" always;
add_header Referrer-Policy "no-referrer-when-downgrade" always;
add_header Content-Security-Policy "default-src 'self'; style-src 'self' https://fonts.googleapis.com; font-src https://fonts.gstatic.com; img-src 'self' data:; script-src 'self'" always;

# Dashboard — additionally:
add_header X-Robots-Tag "noindex, nofollow" always;          # also set via <meta> already
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains" always;  # if served over TLS even on intranet
add_header Cache-Control "no-store" always;                   # never cache pages with live graph data
```

### Why CSP matters here specifically

The dashboard will eventually make GraphQL requests to the API (Layer 4/5 of
the architecture). When that's wired up, the CSP `connect-src` directive must
explicitly allowlist the API origin:

```
connect-src 'self' https://api.internal.caelnex:4000;
```

Without this, a compromised dependency (e.g. a malicious npm package pulled
into a future build step) cannot exfiltrate graph data to an external host —
the browser will block the request at the network level regardless of what
the JS tries to do.

---

## Secrets — what must never appear in frontend code

Both files in this directory are static HTML/CSS/JS with **zero secrets** —
this is intentional and must be preserved:

- No API keys, tokens, or credentials of any kind in source
- No Neo4j/PostgreSQL connection strings
- No Keycloak client *secret* (the dashboard uses a **public** client with
  PKCE — by design, public clients have no secret to leak)

If a future iteration needs a build step (React, bundlers), add a
`.env.example` with placeholder names only, and ensure `.gitignore` excludes
`.env*` (already present in the main repo's `.gitignore`).

---

## Offline / intranet operation

Both pages currently load Google Fonts from `fonts.googleapis.com`. For true
offline operation on an air-gapped intranet:

1. Download the two font families (IBM Plex Mono, Inter) as `.woff2` files
2. Self-host under `/assets/fonts/`
3. Replace the `<link href="https://fonts.googleapis.com/...">` tag with a
   local `@font-face` declaration in each file's `<style>` block

This removes the only external network dependency in either file.

---

## Checklist before any real deployment

- [ ] Demo auth replaced with Keycloak (see above)
- [ ] Security headers applied at the reverse proxy
- [ ] Fonts self-hosted for offline operation
- [ ] CSP `connect-src` updated once GraphQL API origin is known
- [ ] Dashboard confirmed unreachable from outside the intranet
        (test: attempt access from a device outside the VPN/network)
- [ ] `public/` reviewed to confirm zero references to real supplier names,
        real anomaly IDs, or anything beyond the three historical case studies
